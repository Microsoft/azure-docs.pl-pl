---
title: Samouczek — Tworzenie hierarchii IoT Edge urządzeń — Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć hierarchiczną strukturę IoT Edge urządzeń przy użyciu bram.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: cd0fd7ac004d07b71a69a3e59c9cfd4727d98eb6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184675"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Samouczek: Tworzenie hierarchii urządzeń IoT Edge (wersja zapoznawcza)

Wdróż węzły Azure IoT Edge w różnych sieciach zorganizowanych w warstwach hierarchicznych. Każda warstwa w hierarchii jest urządzeniem bramy, które obsługuje komunikaty i żądania z urządzeń znajdujących się w warstwie poniżej.

>[!NOTE]
>Ta funkcja wymaga IoT Edge w wersji 1,2, która jest w publicznej wersji zapoznawczej z uruchomionymi kontenerami systemu Linux.

Hierarchię urządzeń można struktury w taki sposób, aby tylko warstwa najwyższej warstwy łączyła się z chmurą, a niższe warstwy mogą komunikować się tylko z sąsiednimi warstwami Północne i Południowe. Ta warstwa sieciowa jest podstawą większości sieci przemysłowych, które są zgodne ze [standardem ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Celem tego samouczka jest utworzenie hierarchii urządzeń IoT Edge, które symulują środowisko produkcyjne. Na koniec zostanie wdrożony [moduł symulowanej czujnika temperatury](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) na niższym urządzeniu warstwy bez dostępu do Internetu przez pobranie obrazów kontenerów za pomocą hierarchii.

Aby osiągnąć ten cel, ten samouczek przeprowadzi Cię przez proces tworzenia hierarchii IoT Edge urządzeń, wdrażania kontenerów środowiska uruchomieniowego IoT Edge na urządzeniach oraz lokalnego konfigurowania urządzeń. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Utwórz i zdefiniuj relacje w hierarchii IoT Edge urządzeń.
> * Skonfiguruj środowisko uruchomieniowe IoT Edge na urządzeniach w hierarchii.
> * Instalowanie spójnych certyfikatów w hierarchii urządzeń.
> * Dodawanie obciążeń do urządzeń w hierarchii.
> * Moduł proxy interfejsu API umożliwia bezpieczne kierowanie ruchu HTTP przez jeden port z urządzeń z niższymi warstwami.

W tym samouczku zdefiniowano następujące warstwy sieci:

* **Warstwa najwyższej warstwy**: urządzenia IoT Edge w tej warstwie mogą łączyć się bezpośrednio z chmurą.

* **Warstwa niższa**: urządzenia IoT Edge z tej warstwy nie mogą łączyć się bezpośrednio z chmurą. Aby wysyłać i odbierać dane, należy przejść przez co najmniej jedno IoT Edge urządzeń.

W tym samouczku dla uproszczenia użyto dwóch hierarchii urządzeń. Jedno urządzenie, **topLayerDevice**, reprezentuje urządzenie w najwyższej warstwie hierarchii, które może połączyć się bezpośrednio z chmurą. To urządzenie będzie również określane jako **urządzenie nadrzędne**. Inne urządzenie, **lowerLayerDevice**, reprezentuje urządzenie w niższej warstwie hierarchii, które nie może połączyć się bezpośrednio z chmurą. To urządzenie będzie również określane jako **urządzenie podrzędne**. Do reprezentowania środowiska produkcyjnego można dodać dodatkowe mniejsze urządzenia warstwy. Konfiguracja wszelkich dodatkowych urządzeń warstwy niższej będzie zgodna z konfiguracją **lowerLayerDevice**.

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć hierarchię urządzeń IoT Edge, potrzebne są:

* Komputer (system Windows lub Linux) z łącznością z Internetem.
* Dwa urządzenia z systemem Linux do konfigurowania jako urządzenia IoT Edge. Jeśli nie masz dostępnych urządzeń, możesz korzystać z [usługi Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/).
* Konto platformy Azure z prawidłową subskrypcją. Jeśli nie masz [subskrypcji platformy Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Warstwa Bezpłatna lub standardowa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.
* Interfejs wiersza polecenia platformy Azure w wersji 2.3.1 z zainstalowaną usługą Azure IoT Extension v 0.10.6 lub nowszą. Ten samouczek używa [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Jeśli nie znasz Azure Cloud Shell, [zapoznaj się z przewodnikiem Szybki Start, aby uzyskać szczegółowe informacje](https://docs.microsoft.com/azure/iot-edge/quickstart-linux#use-azure-cloud-shell).

Możesz również wypróbować ten scenariusz, postępując zgodnie z Azure IoT Edge skryptowymi [dla przykładu przemysł IoT](https://aka.ms/iotedge-nested-sample), który wdraża maszyny wirtualne platformy Azure jako wstępnie skonfigurowane urządzenia, aby symulować środowisko fabryki.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Skonfiguruj hierarchię urządzeń IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Tworzenie hierarchii urządzeń IoT Edge

Pierwszym krokiem tworzenia IoT Edge urządzeń można dokonać za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure. W tym samouczku utworzysz hierarchię dwóch IoT Edge urządzeń: **topLayerDevice** i jej podrzędną **lowerLayerDevice**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Wybierz pozycję **+ Dodaj urządzenie IoT Edge**. To urządzenie będzie urządzeniem warstwy najwyższej, więc wprowadź odpowiedni unikatowy identyfikator urządzenia. Wybierz pozycję **Zapisz**.

1. Ponownie wybierz pozycję **+ Dodaj urządzenie IoT Edge** . To urządzenie będzie urządzeniem warstwy niższej krawędzi, więc wprowadź odpowiedni unikatowy identyfikator urządzenia.

1. Wybierz pozycję **Ustaw urządzenie nadrzędne**, wybierz z listy urządzeń urządzenie z najwyższą warstwą, a następnie wybierz **przycisk OK**. Wybierz pozycję **Zapisz**.

   ![Ustawianie elementu nadrzędnego dla urządzenia warstwy niższej](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. W [Azure Cloud Shell](https://shell.azure.com/)wprowadź następujące polecenie, aby utworzyć urządzenie IoT Edge w centrum. To urządzenie będzie urządzeniem warstwy najwyższej, więc wprowadź odpowiedni unikatowy identyfikator urządzenia:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Wprowadź następujące polecenie, aby utworzyć podrzędne urządzenie IoT Edge i utworzyć relację nadrzędny-podrzędny między urządzeniami:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Zanotuj parametry połączenia poszczególnych urządzeń IoT Edge. Zostaną one użyte później.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do sekcji **IoT Edge** w IoT Hub.

1. Kliknij identyfikator urządzenia jednego z urządzeń na liście urządzeń.

1. Wybierz opcję **Kopiuj** w polu **podstawowe parametry połączenia** i Zapisz ją w wybranym miejscu.

1. Powtórz te czynności dla wszystkich innych urządzeń.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. W [Azure Cloud Shell](https://shell.azure.com/)dla każdego urządzenia wprowadź następujące polecenie, aby pobrać parametry połączenia urządzenia i zapisać je w wybranym miejscu:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>Tworzenie certyfikatów

Wszystkie urządzenia w [scenariuszu bramy](iot-edge-as-gateway.md) muszą mieć współużytkowany certyfikat w celu skonfigurowania bezpiecznych połączeń między nimi. Wykonaj następujące kroki, aby utworzyć certyfikaty demonstracyjne dla obu urządzeń w tym scenariuszu.

Aby utworzyć certyfikaty demonstracyjne na urządzeniu z systemem Linux, należy sklonować skrypty generacji i skonfigurować je tak, aby uruchamiały się lokalnie w bash.

1. Sklonuj repozytorium IoT Edge git, które zawiera skrypty do generowania certyfikatów demonstracyjnych.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Przejdź do katalogu, w którym chcesz korzystać. Wszystkie pliki certyfikatów i kluczy zostaną utworzone w tym katalogu.

1. Skopiuj pliki konfiguracji i skryptów ze sklonowanego repozytorium IoT Edge do katalogu roboczego.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Utwórz certyfikat głównego urzędu certyfikacji i jeden certyfikat pośredni.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy, ale używamy następującego pliku jako **certyfikatu głównego urzędu certyfikacji** dla hierarchii bramy:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Utwórz dwa zestawy certyfikatów i kluczy prywatnych urzędu certyfikacji urządzenia IoT Edge za pomocą następującego polecenia: jeden zestaw dla urządzenia warstwy najwyższej i jeden zestaw dla urządzenia warstwy niższej. Podaj do zapamiętania nazwy certyfikatów urzędu certyfikacji, aby odróżnić je od siebie.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy, ale używamy poniższego certyfikatu i pary kluczy na każdym urządzeniu IoT Edge i przywoływany w pliku config. YAML:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Każde urządzenie musi mieć kopię certyfikatu głównego urzędu certyfikacji i kopię własnego certyfikatu urzędu certyfikacji urządzenia oraz klucza prywatnego. Aby przenieść certyfikaty na poszczególne urządzenia, można użyć dysku USB lub [bezpiecznego kopiowania plików](https://www.ssh.com/ssh/scp/) .

1. Po przeniesieniu certyfikatów Zainstaluj główny urząd certyfikacji dla każdego urządzenia.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   To polecenie powinno wyprowadzić, że jeden certyfikat został dodany w/etc/SSL/certs.

### <a name="install-iot-edge-on-the-devices"></a>Instalowanie IoT Edge na urządzeniach

Zainstaluj IoT Edge, wykonując następujące kroki na obu urządzeniach.

1. Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

1. Zainstaluj aparat Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Instalowanie demona hsmlib i IoT Edge <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/libiothsm-std_1.2.0.rc2-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/iotedge_1.2.0_rc2-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi IoT Edge

Skonfiguruj środowisko uruchomieniowe IoT Edge, wykonując następujące czynności na urządzeniach. Skonfigurowanie środowiska uruchomieniowego IoT Edge dla urządzeń obejmuje cztery kroki, które można wykonać, edytując plik konfiguracji IoT Edge:

1. Ręcznie Zainicjuj obsługę każdego urządzenia, dodając parametry połączenia tego urządzenia do pliku konfiguracji.

1. Zakończ Konfigurowanie certyfikatów urządzenia, wskazując plik konfiguracji na certyfikat urzędu certyfikacji urządzenia, klucz prywatny urzędu certyfikacji urządzenia i certyfikat głównego urzędu certyfikacji.

1. Załadowania systemu przy użyciu agenta IoT Edge.

1. Zaktualizuj parametr **hostname** dla urządzenia **warstwy najwyższego poziomu** i zaktualizuj parametr **hostname** oraz parametr **parent_hostname** dla urządzeń z **niższą warstwą** .

Wykonaj te kroki i uruchom ponownie usługę IoT Edge, aby skonfigurować urządzenia.

1. Na każdym urządzeniu Otwórz plik konfiguracji IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź konfiguracje aprowizacji pliku i usuń znaczniki **ręcznej konfiguracji aprowizacji za pomocą sekcji parametrów połączenia** .

1. Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia IoT Edge. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

1. Znajdź sekcję **Certyfikaty** . Usuń komentarz i zaktualizuj trzy pola certyfikatów, aby wskazywały certyfikaty utworzone w poprzedniej sekcji i przeniesione na urządzenie IoT Edge. Podaj ścieżki URI pliku, które mają format `file:///<path>/<filename>` .

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Znajdź parametr **hostname** dla urządzenia **warstwy najwyższego poziomu** . Zaktualizuj wartość tak, aby była to w pełni kwalifikowana nazwa domeny (FQDN) lub adres IP urządzenia IoT Edge. Należy używać wybranej wartości, która jest spójna na urządzeniach w hierarchii.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. W przypadku urządzeń IoT Edge w **niższych warstwach** Zaktualizuj plik konfiguracji, aby wskazywał nazwę FQDN lub adres IP urządzenia nadrzędnego, dopasowując się do pola **Nazwa hosta** urządzenia nadrzędnego. W przypadku urządzeń IoT Edge w **górnej warstwie** Pozostaw ten parametr komentarz.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > W przypadku hierarchii z więcej niż jedną niższą warstwą należy zaktualizować pole *parent_hostname* za pomocą nazwy FQDN urządzenia w warstwie bezpośrednio powyżej.

1. W przypadku urządzenia **warstwy najwyższego poziomu** Znajdź sekcję YAML **agenta** i zaktualizuj wartość obrazu do poprawnej wersji agenta IoT Edge. W takim przypadku będziemy wskazywać IoT Edge agenta na Azure Container Registry z publiczną wersją zapoznawczą dostępnego obrazu agenta IoT Edge.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. W przypadku urządzeń IoT Edge w **niższych warstwach** zaktualizuj nazwę domeny wartości obrazu, tak aby wskazywała na to, że nazwa FQDN lub adres IP urządzenia nadrzędnego następuje przez port serwera proxy interfejsu API, 8000. Moduł proxy interfejsu API zostanie dodany do następnej sekcji.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

1. Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Wdrażanie modułów na urządzeniu warstwy najwyższej

Pozostałe kroki służące do ukończenia konfiguracji IoT Edge środowiska uruchomieniowego i wdrażania obciążeń są wykonywane w chmurze za pośrednictwem Azure Portal lub interfejsu wiersza polecenia platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [Azure Portal](https://ms.portal.azure.com/):

1. Przejdź do IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Kliknij identyfikator urządzenia na urządzeniu brzegowym **najwyższej warstwy** na liście urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

1. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** obok ikony koła zębatego.

1. W obszarze **centrum brzegowe** w polu obraz wprowadź `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

   ![Edytuj obraz centrum brzegowego](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Dodaj następujące zmienne środowiskowe do modułu centrum brzegowego:

    | Nazwa | Wartość |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Edytuj zmienne środowiskowe centrum brzegowego](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. W obszarze **Agent Edge** w polu obraz wprowadź wartość `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2` . Wybierz pozycję **Zapisz**.

1. Dodaj moduł Docker Registry do urządzenia warstwy najwyższego poziomu. Wybierz pozycję **+ Dodaj** i wybierz **moduł IoT Edge** z listy rozwijanej. Podaj nazwę `registry` modułu Docker Registry i wprowadź `registry:latest` dla identyfikatora URI obrazu. Następnie Dodaj zmienne środowiskowe i Utwórz opcje, aby wskazać lokalny moduł rejestru w usłudze Microsoft Container Registry w celu pobrania obrazów kontenerów z i do obsłużenia tych obrazów w rejestrze: 5000.

1. Na karcie zmienne środowiskowe Wprowadź następującą parę nazwa zmiennej środowiskowej:

    | Nazwa | Wartość |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Na karcie Opcje tworzenia kontenera wprowadź następujący kod JSON:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Następnie Dodaj moduł proxy interfejsu API do urządzenia warstwy najwyższego poziomu. Wybierz pozycję **+ Dodaj** i wybierz **moduł Marketplace** z listy rozwijanej. Wyszukaj `IoT Edge API Proxy` i wybierz moduł. Serwer proxy interfejsu API IoT Edge korzysta z portu 8000 i jest skonfigurowany do używania modułu rejestru o nazwie `registry` Domyślnie na porcie 5000.

1. Wybierz pozycję **Przegląd + Utwórz**, a następnie pozycję **Utwórz** , aby zakończyć wdrażanie. Środowisko uruchomieniowe IoT Edge urządzenia warstwy najwyższego poziomu, które ma dostęp do Internetu, spowoduje pobranie i uruchomienie **publicznej konfiguracji wersji zapoznawczej** dla IoT Edge hub i IoT Edge agenta.

   ![Kompletne wdrożenie zawierające moduł usługi Edge Hub, agenta brzegowego, modułu rejestru i serwera proxy interfejsu API](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. W [Azure Cloud Shell](https://shell.azure.com/)wprowadź następujące polecenie, aby utworzyć deployment.jsna pliku:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Skopiuj zawartość JSON poniżej do deployment.jsna, Zapisz ją i Zamknij.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Wprowadź następujące polecenie, aby utworzyć wdrożenie na urządzeniu brzegowym najwyższej warstwy:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>Wdrażanie modułów na urządzeniu warstwy niższej

Możesz użyć zarówno Azure Portal, jak i interfejsu wiersza polecenia platformy Azure do wdrożenia obciążeń z chmury na urządzeniach z **niższą warstwą** .

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [Azure Portal](https://ms.portal.azure.com/):

1. Przejdź do IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Na liście urządzeń IoT Edge kliknij identyfikator urządzenia warstwy niższej.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

1. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** obok ikony koła zębatego.

1. W obszarze **centrum brzegowe** w polu obraz wprowadź `$upstream:8000/azureiotedge-hub:1.2.0-rc2` .

1. Dodaj następujące zmienne środowiskowe do modułu centrum brzegowego:

    | Nazwa | Wartość |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. W obszarze **Agent Edge** w polu obraz wprowadź wartość `$upstream:8000/azureiotedge-agent:1.2.0-rc2` . Wybierz pozycję **Zapisz**.

1. Dodaj moduł czujnika temperatury. Wybierz pozycję **+ Dodaj** i wybierz **moduł Marketplace** z listy rozwijanej. Wyszukaj `Simulated Temperature Sensor` i wybierz moduł.

1. W obszarze **IoT Edge modułów** wybierz `Simulated Temperature Sensor` właśnie dodany moduł i zaktualizuj jego identyfikator URI obrazu, aby wskazywał `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` .

1. Wybierz pozycję **Zapisz**, **Przejrzyj + Utwórz** i **Utwórz** , aby zakończyć wdrażanie.

   ![Kompletne wdrożenie zawierające centrum brzegowe, agenta brzegowego i symulowany czujnik temperatury](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. W [Azure Cloud Shell](https://shell.azure.com/)wprowadź następujące polecenie, aby utworzyć deployment.jsna pliku:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Skopiuj zawartość JSON poniżej do deployment.jsna, Zapisz ją i Zamknij.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Wprowadź następujące polecenie, aby utworzyć wdrożenie modułów z niższą warstwą:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## View generated data

The **Simulated Temperature Sensor** module that you pushed generates sample environment data. It sends messages that include ambient temperature and humidity, machine temperature and pressure, and a timestamp.

You can watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

You can also view these messages through the [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały utworzone w tym artykule, aby uniknąć naliczania opłat.

Aby usunąć zasoby:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. Wybierz nazwę grupy zasobów, która zawiera zasoby testowe usługi IoT Edge. 

3. Przejrzyj listę zasobów znajdujących się w grupie zasobów. Jeśli chcesz usunąć je wszystkie, możesz wybrać pozycję **Usuń grupę zasobów**. Jeśli chcesz usunąć tylko niektóre z nich, możesz kliknąć poszczególne zasoby, aby usunąć je pojedynczo. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano dwa IoT Edge urządzeń jako bramy i ustawili je jako urządzenie nadrzędne. Następnie pokazano, jak utworzyć obraz kontenera na urządzeniu podrzędnym za pomocą bramy. Możesz również wypróbować ten scenariusz, postępując zgodnie z Azure IoT Edge skryptowymi [dla przykładu przemysł IoT](https://aka.ms/iotedge-nested-sample), który wdraża maszyny wirtualne platformy Azure jako wstępnie skonfigurowane urządzenia, aby symulować środowisko fabryki.

Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"]
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
