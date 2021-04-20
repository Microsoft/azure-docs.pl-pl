---
title: Samouczek — tworzenie hierarchii urządzeń IoT Edge — Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć hierarchiczną strukturę IoT Edge przy użyciu bram.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 44fe6bb3787e1fe0df7ccf83200497b46c473568
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728504"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Samouczek: tworzenie hierarchii IoT Edge urządzeń

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Wdrażanie Azure IoT Edge w sieciach zorganizowanych w warstwach hierarchicznych. Każda warstwa w hierarchii jest urządzeniem bramy, które obsługuje komunikaty i żądania z urządzeń w warstwie poniżej.

Hierarchię urządzeń można utworzyć w taki sposób, aby tylko warstwa górna była w stanie komunikować się z chmurą, a niższe warstwy mogą komunikować się tylko z sąsiednimi warstwami północ-południe. Ta warstwa sieci jest podstawą większości sieci przemysłowych, które są zgodne ze standardem [ISA-95.](https://en.wikipedia.org/wiki/ANSI/ISA-95)

Celem tego samouczka jest utworzenie hierarchii urządzeń IoT Edge, które symulują uproszczone środowisko produkcyjne. Na koniec wdrożysz moduł [Simulated Temperature Sensor](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) na urządzeniu niższej warstwy bez dostępu do Internetu, pobierając obrazy kontenerów za pośrednictwem hierarchii.

Aby osiągnąć ten cel, w tym samouczku otworzymy hierarchię urządzeń IoT Edge, wdrożymy kontenery środowiska uruchomieniowego programu IoT Edge na urządzeniach i skonfigurujemy urządzenia lokalnie. W tym samouczku użyjemy zautomatyzowanego narzędzia do konfiguracji, aby:

> [!div class="checklist"]
>
> * Tworzenie i definiowanie relacji w hierarchii IoT Edge urządzeń.
> * Skonfiguruj środowisko IoT Edge uruchomieniowe na urządzeniach w hierarchii.
> * Zainstaluj spójne certyfikaty w całej hierarchii urządzeń.
> * Dodaj obciążenia do urządzeń w hierarchii.
> * Użyj modułu [serwera proxy IoT Edge API,](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) aby bezpiecznie przekierowyć ruch HTTP przez jeden port z urządzeń w niższej warstwie.

>[!TIP]
>Ten samouczek zawiera połączenie kroków ręcznych i automatycznych w celu zapewnienia pokazu zagnieżdżonych IoT Edge funkcji.
>
>Jeśli chcesz uzyskać całkowicie zautomatyzowane spojrzenie na konfigurowanie hierarchii urządzeń IoT Edge, możesz wykonać kroki skryptu Azure IoT Edge dla przemysłowego [IoT.](https://aka.ms/iotedge-nested-sample) W tym scenariuszu skryptowym maszyny wirtualne platformy Azure są wdrażane jako wstępnie skonfigurowane urządzenia w celu symulowania środowiska fabryki.
>
>Jeśli chcesz szczegółowo przyjrzeć się ręcznym krokom tworzenia hierarchii urządzeń IoT Edge i zarządzania nimi, zobacz przewodnik z instrukcjami na temat hierarchii bramy IoT Edge [urządzeń.](how-to-connect-downstream-iot-edge-device.md)

W tym samouczku definiowane są następujące warstwy sieci:

* **Górna warstwa:** IoT Edge w tej warstwie mogą łączyć się bezpośrednio z chmurą.

* **Niższe warstwy:** IoT Edge w warstwach poniżej górnej warstwy nie mogą łączyć się bezpośrednio z chmurą. Aby wysyłać i odbierać dane, IoT Edge muszą przejść przez co najmniej jeden pośrednik.

W tym samouczku dla uproszczenia użyto dwóch hierarchii urządzeń, jak po ilustracji poniżej. Jedno urządzenie, **urządzenie najwyższej warstwy,** reprezentuje urządzenie w górnej warstwie hierarchii, które może łączyć się bezpośrednio z chmurą. To urządzenie będzie również nazywane urządzeniem **nadrzędnym**. Drugie urządzenie, **urządzenie** warstwy niższej, reprezentuje urządzenie w niższej warstwie hierarchii, które nie może łączyć się bezpośrednio z chmurą. W razie potrzeby można dodać więcej urządzeń w niższej warstwie, aby reprezentować środowisko produkcyjne. Urządzenia w niższych warstwach będą również określane jako **urządzenia podrzędne.**

![Struktura hierarchii samouczka zawierająca dwa urządzenia: urządzenie warstwy najwyższej i urządzenie warstwy niższej](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć hierarchię IoT Edge urządzeń, potrzebne są:

* Komputer (z systemem Windows lub Linux) z łącznością z Internetem.
* Konto platformy Azure z ważną subskrypcją. Jeśli nie masz subskrypcji platformy [Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
* Warstwa Bezpłatna lub Standardowa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) platformie Azure.
* Powłoka Bash w Azure Cloud Shell interfejsie wiersza polecenia platformy Azure w wersji 2.3.1 z zainstalowanym rozszerzeniem usługi Azure IoT w wersji 0.10.6 lub wyższej. W tym samouczku jest używany [Azure Cloud Shell](../cloud-shell/overview.md). Jeśli nie masz informacji na temat Azure Cloud Shell, zapoznaj się z przewodnikiem Szybki start, [aby uzyskać szczegółowe informacje.](./quickstart-linux.md#prerequisites)
  * Aby wyświetlić bieżące wersje modułów i rozszerzeń interfejsu wiersza polecenia platformy Azure, uruchom [narzędzie az version](/cli/azure/reference-index?#az_version).
* Urządzenie z systemem Linux do skonfigurowania jako IoT Edge dla każdego urządzenia w hierarchii. W tym samouczku używane są dwa urządzenia. Jeśli nie masz dostępnych urządzeń, możesz utworzyć maszyny wirtualne platformy Azure dla każdego urządzenia w hierarchii przy użyciu poniższego polecenia.

   Zastąp tekst symbolu zastępczego w poniższym poleceniu i uruchom go dwa razy dla każdej maszyny wirtualnej. Każda maszyna wirtualna musi mieć unikatowy prefiks DNS, który będzie również służyć jako nazwa. Prefiks DNS musi być zgodny z następującym wyrażeniem regularnym: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   Maszyna wirtualna używa kluczy SSH do uwierzytelniania użytkowników. Jeśli nie wiesz, jak tworzyć klucze SSH i ich używać, możesz wykonać instrukcje dotyczące par [klucz publiczny-prywatny SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)dla maszyn wirtualnych z systemem Linux na platformie Azure.

   IoT Edge wersji 1.2 jest wstępnie zainstalowana przy użyciu tego szablonu usługi ARM, co pozwala zaoszczędzić na konieczności ręcznego instalowania zasobów na maszynach wirtualnych. Jeśli instalujesz program IoT Edge na własnych urządzeniach, zobacz Temat [Install Azure IoT Edge for Linux (version 1.2) (Instalowanie](how-to-install-iot-edge.md) programu Azure IoT Edge dla systemu Linux w wersji 1.2) lub Update IoT Edge to version 1.2 (Instalowanie programu Azure IoT Edge dla systemu Linux w wersji [1.2) lub Update IoT Edge to version 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)(Instalowanie programu IoT Edge do wersji 1.2).

   Pomyślne utworzenie maszyny wirtualnej przy użyciu tego szablonu usługi ARM spowoduje wyprowadzanie dojścia maszyny wirtualnej i w pełni `SSH` kwalifikowanej nazwy domeny ( `FQDN` ). W kolejnych krokach użyjesz dojścia SSH i albo FQDN, albo adresu IP każdej maszyny wirtualnej do konfiguracji, więc śledź te informacje. Przykładowe dane wyjściowe przedstawiono poniżej.

   >[!TIP]
   >Adres IP i WQDN można również znaleźć w Azure Portal. W polu Adres IP przejdź do listy maszyn wirtualnych i zanotuj **pole Publiczny adres IP.** W przypadku nazwy FQDN przejdź do strony przeglądu każdej maszyny wirtualnej i poszukaj pola **nazwa DNS.**

   ![Maszyna wirtualna wyprowadzi dane JSON po utworzeniu, który zawiera jego dojście SSH](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Upewnij się, że następujące porty są otwarte dla wszystkich urządzeń z wyjątkiem urządzeń o najniższej warstwie: 8000, 443, 5671, 8883:
  * 8000: Służy do ściągania obrazów kontenerów platformy Docker za pośrednictwem serwera proxy interfejsu API.
  * 443: Używany między nadrzędnymi i podrzędnym koncentratorami brzegowym dla wywołań interfejsu API REST.
  * 5671, 8883: używany w przypadku amqp i MQTT.

  Aby uzyskać więcej informacji, zobacz [jak otworzyć porty dla maszyny wirtualnej za pomocą Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Konfigurowanie hierarchii IoT Edge urządzeń

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Tworzenie hierarchii IoT Edge urządzeń

IoT Edge urządzenia to warstwy hierarchii. Ten samouczek utworzy hierarchię dwóch urządzeń IoT Edge: urządzenia warstwy najwyższej i jego urządzenia podrzędnego, urządzenia warstwy **niższej**.  W razie potrzeby można utworzyć dodatkowe urządzenia podrzędne.

Aby utworzyć i skonfigurować hierarchię IoT Edge, użyjesz `iotedge-config` narzędzia . To narzędzie upraszcza konfigurację hierarchii przez zautomatyzowanie i skondensowanie kilku kroków do dwóch:

1. Konfigurowanie konfiguracji chmury i przygotowywanie każdej konfiguracji urządzenia, w tym:

   * Tworzenie urządzeń w IoT Hub
   * Ustawianie relacji nadrzędny-podrzędny w celu autoryzowania komunikacji między urządzeniami
   * Generowanie łańcucha certyfikatów dla każdego urządzenia w celu ustanowienia bezpiecznej komunikacji między nimi
   * Generowanie plików konfiguracji dla każdego urządzenia

1. Instalowanie poszczególnych konfiguracji urządzeń, w tym:

   * Instalowanie certyfikatów na każdym urządzeniu
   * Stosowanie plików konfiguracji dla każdego urządzenia

Narzędzie spowoduje również automatyczne wdrożenie modułów na `iotedge-config` IoT Edge urządzeniu.

Aby użyć narzędzia `iotedge-config` do tworzenia i konfigurowania hierarchii, wykonaj poniższe kroki w interfejsie wiersza polecenia platformy Azure:

1. W [Azure Cloud Shell](https://shell.azure.com/)katalog dla zasobów samouczka:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Pobierz wersję narzędzia do konfiguracji i szablonów konfiguracji:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Spowoduje to utworzenie `iotedge_config_cli_release` folderu w katalogu samouczka.

   Plik szablonu używany do tworzenia hierarchii urządzeń to `iotedge_config.yaml` plik znaleziony w pliku `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . W tym samym katalogu znajduje się plik wdrożenia JSON zawierający instrukcje dotyczące modułów do wdrożenia na urządzeniu warstwy `deploymentLowerLayer.json` **niższej**. Plik jest taki sam, ale dla urządzenia najwyższej warstwy , ponieważ moduły wdrożone na poszczególnych urządzeniach `deploymentTopLayer.json` nie są takie same.  Plik jest szablonem konfiguracji IoT Edge urządzenia i będzie używany do automatycznego generowania pakietów konfiguracji dla urządzeń `device_config.toml` w hierarchii.

   Jeśli chcesz przyjrzeć się kodowi źródłowego i skryptom narzędzia, zapoznaj się z repozytorium Azure-Samples `iotedge-config` [w witrynie GitHub.](https://github.com/Azure-Samples/iotedge_config_cli)

1. Otwórz szablon konfiguracji samouczka i edytuj go przy użyciu swoich informacji:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   W sekcji **iothub** wypełnij pola `iothub_hostname` i `iothub_name` swoimi informacjami. Te informacje można znaleźć na stronie przeglądu IoT Hub na Azure Portal.

   W sekcji **certyfikaty opcjonalne** można wypełnić pola ścieżkami bezwzględnymi do certyfikatu i klucza. Jeśli pozostawisz te pola puste, skrypt automatycznie wygeneruje certyfikaty testowe z podpisem własnym do użycia. Jeśli nie masz informacji na temat sposobu, w jaki certyfikaty są używane w scenariuszu bramy, zapoznaj się z sekcją przewodników po [certyfikatach.](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)

   W **sekcji konfiguracji** jest `template_config_path` to ścieżka do szablonu `device_config.toml` używanego do tworzenia konfiguracji urządzeń. Pole określa, jakie urządzenia niższej warstwy obrazu agenta edge będą ściągać `default_edge_agent` i skąd.

   W sekcji **edgedevices (urządzenia** brzegowe) dla scenariusza produkcyjnego można edytować drzewo hierarchii, aby odzwierciedlić żądaną strukturę. Na potrzeby tego samouczka zaakceptuj drzewo domyślne. Dla każdego urządzenia znajduje się `device_id` pole, w którym można nazwać urządzenia. Istnieje również pole `deployment` , które określa ścieżkę do pliku JSON wdrożenia dla tego urządzenia.

   Możesz również ręcznie rejestrować urządzenia IoT Edge na urządzeniu w IoT Hub za pośrednictwem Azure Portal lub Azure Cloud Shell. Aby dowiedzieć się, jak to zrobić, zapoznaj się z przewodnikiem na temat [rejestrowania IoT Edge urządzenia.](how-to-register-device.md)

   Relacje nadrzędny-podrzędny można również zdefiniować ręcznie. Aby dowiedzieć [się](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) więcej, zobacz sekcję tworzenie hierarchii bramy w przewodniku.

   ![Sekcja edgedevices pliku konfiguracji umożliwia zdefiniowanie hierarchii](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Zapisz i zamknij plik:

   `CTRL + S`, `CTRL + Q`

1. Utwórz katalog outputs dla pakietów konfiguracji w katalogu zasobów samouczka:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Przejdź do katalogu `iotedge_config_cli_release` i uruchom narzędzie , aby utworzyć hierarchię IoT Edge urządzeń:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Za pomocą flagi narzędzie tworzy certyfikaty urządzeń, pakiety certyfikatów i plik dziennika w `--output` wybranego katalogu. Po skonfigurowaniu flag narzędzie automatycznie poszuka istniejących urządzeń IoT Edge w twoim centrum IoT Hub je i usunie je, aby uniknąć błędów i zachować centrum w stanie `-f` czystym.

   Narzędzie do konfiguracji tworzy IoT Edge i konfiguruje między nimi relacje nadrzędny-podrzędny. Opcjonalnie tworzy certyfikaty dla urządzeń do użycia. Jeśli zostaną podane ścieżki do wdrożenia sieci JSN, narzędzie automatycznie utworzy te wdrożenia na urządzeniach, ale nie jest to wymagane. Na koniec narzędzie wygeneruje pakiety konfiguracji dla urządzeń i umieszcza je w katalogu wyjściowym. Aby dokładnie przyjrzeć się krokom podjętym przez narzędzie konfiguracji, zobacz plik dziennika w katalogu wyjściowym.

   ![Skrypt wyświetli topologię hierarchii po wykonaniu](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Sprawdź, czy dane wyjściowe topologii ze skryptu wyglądają poprawnie. Gdy hierarchia jest prawidłowo ustrukturyzowana, możesz kontynuować.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi IoT Edge

Oprócz aprowizowania urządzeń kroki konfiguracji ustanawiają zaufaną komunikację między urządzeniami w hierarchii przy użyciu utworzonych wcześniej certyfikatów. Kroki te rozpoczynają się również w celu ustanowienia struktury sieci hierarchii. Urządzenie najwyższej warstwy będzie utrzymywać łączność z Internetem, umożliwiając ściąganie obrazów środowiska uruchomieniowego z chmury, natomiast urządzenia warstwy niższej będą kierowane przez urządzenie warstwy najwyższej, aby uzyskać dostęp do tych obrazów.

Aby skonfigurować IoT Edge uruchomieniowego, należy zastosować do urządzeń pakiety konfiguracyjne utworzone przez skrypt konfiguracji. Konfiguracje między urządzeniem warstwy najwyższej **a** urządzeniem warstwy niższej nieco się różnią, dlatego należy pamiętać o tym, który plik konfiguracji urządzenia jest stosowanie do każdego urządzenia. 

1. Każde urządzenie wymaga odpowiedniego pakietu konfiguracji. Do przenoszenia pakietów [](https://www.ssh.com/ssh/scp/) konfiguracyjnych do poszczególnych urządzeń można użyć dysku USB lub bezpiecznego kopiowania plików.

   Pamiętaj, aby wysłać prawidłowy pakiet konfiguracji do każdego urządzenia.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Oznacza `:~` to, że folder konfiguracji zostanie umieszczony w katalogu macierzystym na maszynie wirtualnej.

1. Zaloguj się do maszyny wirtualnej, aby zastosować pakiet konfiguracji do urządzenia:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Na każdym urządzeniu rozpakować pakiet konfiguracji. Najpierw należy zainstalować plik zip:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Na każdym urządzeniu zastosuj pakiet konfiguracji do urządzenia:

   ```bash
   sudo ./install.sh
   ```

   Na urządzeniu **najwyższej warstwy** zostanie wyświetlony monit o wprowadzenie nazwy hosta. Na urządzeniu **w warstwie** dolnej zostanie zapytana nazwa hosta i nazwa hosta nadrzędnego. Dla każdego monitu należy podać odpowiedni adres IP lub WFQDN. Możesz użyć jednej z tych opcji, ale być spójnym wyborem dla różnych urządzeń. Dane wyjściowe skryptu instalacji przedstawiono poniżej.

   Jeśli chcesz bliżej przyjrzeć się tym, jakie modyfikacje są dokonywane w pliku konfiguracji urządzenia, zobacz sekcję konfigurowanie IoT Edge na urządzeniach w [przewodniku](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![Zainstalowanie pakietów konfiguracji spowoduje zaktualizowanie plików config.toml na urządzeniu i automatyczne IoT Edge wszystkich usług](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Jeśli powyższe kroki zostały wykonane poprawnie, możesz sprawdzić, czy urządzenia są poprawnie skonfigurowane.

Uruchamianie kontroli konfiguracji i łączności na urządzeniach. W przypadku **urządzenia najwyższej warstwy:**

   ```bash
   sudo iotedge check
   ```

W przypadku **urządzenia z niższą** warstwą obraz diagnostyczny musi zostać ręcznie przekazany w poleceniu :

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

Na urządzeniu **najwyższej warstwy należy** spodziewać się danych wyjściowych z kilkoma przekazywaniem ocen. Mogą pojawić się ostrzeżenia dotyczące zasad dzienników i, w zależności od sieci, zasad DNS.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Gdy konfiguracje na poszczególnych urządzeniach są poprawne, możesz kontynuować.

## <a name="deploy-modules-to-your-devices"></a>Wdrażanie modułów na urządzeniach

Wdrożenia modułów na urządzeniach zostały automatycznie wygenerowane podczas ich tworzenia. Narzędzie `iotedge-config-cli` po utworzeniu urządzenia JSON do wdrożenia na urządzeniach najwyższej i niższej  warstwy. Wdrożenie modułu było oczekujące podczas konfigurowania środowiska uruchomieniowego IoT Edge na każdym urządzeniu. Po skonfigurowaniu środowiska uruchomieniowego rozpoczęto wdrożenia na urządzeniu **najwyższej** warstwy. Po zakończeniu tych  wdrożeń urządzenie w niższej warstwie może używać modułu serwera proxy interfejsu **API** IoT Edge do ściągania niezbędnych obrazów.

W [Azure Cloud Shell](https://shell.azure.com/)można przyjrzeć się danych JSON wdrożenia urządzenia najwyższej warstwy, aby zrozumieć, które moduły zostały wdrożone na urządzeniu: 

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Ponadto moduły środowiska uruchomieniowego **IoT Edge Agent** i **IoT Edge** Hub  , urządzenie najwyższej warstwy odbiera moduł rejestru platformy **Docker** i moduł IoT Edge api **proxy.**

Moduł **rejestru platformy Docker** wskazuje istniejącą Azure Container Registry. W tym przypadku `REGISTRY_PROXY_REMOTEURL` wskazuje na Microsoft Container Registry. W `createOptions` programie widać, że komunikuje się on na porcie 5000.

Moduł **serwera proxy IoT Edge API** kieruje żądania HTTP do innych modułów, dzięki czemu urządzenia w niższej warstwie mogą ściągać obrazy kontenerów lub wypychać obiekty blob do magazynu. W tym samouczku komunikuje się on na porcie 8000 i jest skonfigurowany do wysyłania tras żądań ściągnnięcia obrazu kontenera platformy Docker do modułu rejestru **platformy Docker** na porcie 5000. Ponadto wszystkie żądania przekazania magazynu obiektów blob są kierowane do modułu AzureBlobStorageonIoTEdge na porcie 11002. Aby uzyskać więcej informacji na **temat IoT Edge serwera proxy** interfejsu API i sposobu jego konfigurowania, zobacz przewodnik po [module](how-to-configure-api-proxy-module.md).

Jeśli chcesz dowiedzieć się, jak utworzyć wdrożenie w ten sposób za pośrednictwem usługi Azure Portal lub Azure Cloud Shell, zobacz sekcję urządzenia najwyższej warstwy przewodnika [.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)

W [Azure Cloud Shell](https://shell.azure.com/)można przyjrzeć się JSON  wdrożenia urządzenia niższej warstwy, aby zrozumieć, które moduły zostały wdrożone na urządzeniu:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Można zobaczyć, że moduły środowiska uruchomieniowego urządzenia warstwy niższej są ustawione do ściągania z systemu , a nie , tak jak urządzenie warstwy `systemModules`  `$upstream:8000` `mcr.microsoft.com` **najwyższej.** Urządzenie **warstwy niższej** wysyła żądanie obrazu platformy Docker **do modułu serwera** proxy interfejsu API IoT Edge na porcie 8000, ponieważ nie może bezpośrednio ściągać obrazów z chmury. Drugi moduł wdrożony na urządzeniu w **niższej warstwie**, moduł **Simulated Temperature Sensor,** również wykonuje żądanie obrazu do modułu `$upstream:8000` .

Jeśli chcesz dowiedzieć się, jak utworzyć wdrożenie w ten sposób za pośrednictwem usługi Azure Portal lub Azure Cloud Shell, zobacz sekcję urządzenia w niższej warstwie przewodnika [.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)

Stan modułów można wyświetlić za pomocą polecenia :

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   To polecenie spowoduje wyprowadznie wszystkich zgłoszonych właściwości edgeAgent. Oto kilka przydatnych funkcji do monitorowania stanu *urządzenia:* stan środowiska uruchomieniowego, czas rozpoczęcia środowiska uruchomieniowego, czas *ostatniego* zakończenia środowiska uruchomieniowego, liczba *ponownego uruchomienia środowiska uruchomieniowego.*

Stan modułów można również sprawdzić na stronie [Azure Portal.](https://ms.portal.azure.com/) Przejdź do **IoT Edge** sekcji aplikacji, IoT Hub wyświetlić urządzenia i moduły.

Gdy wdrożenia modułów będą zadowalać, możesz kontynuować.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Wypchnięty moduł **Simulated Temperature Sensor** generuje przykładowe dane środowiska. Wysyła on komunikaty, które obejmują temperaturę otoczenia i wilgotność, temperaturę i ciśnienie maszyny oraz znacznik czasu.

Te komunikaty można również wyświetlić za pośrednictwem [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Uruchom polecenie `iotedge check` , aby zweryfikować konfigurację i rozwiązać problemy z błędami.

Można uruchamiać `iotedge check` w hierarchii zagnieżdżonych, nawet jeśli maszyny podrzędne nie mają bezpośredniego dostępu do Internetu.

Po uruchomieniu z niższej warstwy program próbuje ściągnąć obraz z elementu nadrzędnego za `iotedge check` pośrednictwem portu 443.

W tym samouczku używamy portu 8000, więc musimy go określić:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2
```

Wartość `azureiotedge-diagnostics` jest ciągnięta z rejestru kontenerów połączonego z modułem rejestru. W tym samouczku domyślnie ustawiono wartość https://mcr.microsoft.com:

| Nazwa | Wartość |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Jeśli używasz prywatnego rejestru kontenerów, upewnij się, że wszystkie obrazy (IoTEdgeAPIProxy, edgeAgent, edgeHub, Simulated Temperature Sensor i diagnostics) są obecne w rejestrze kontenerów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby uniknąć nalicznych opłat, możesz usunąć konfiguracje lokalne i zasoby platformy Azure utworzone w tym artykule.

Aby usunąć zasoby:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. Wybierz nazwę grupy zasobów, która zawiera zasoby testowe usługi IoT Edge. 

3. Przejrzyj listę zasobów znajdujących się w grupie zasobów. Jeśli chcesz usunąć je wszystkie, możesz wybrać pozycję **Usuń grupę zasobów**. Jeśli chcesz usunąć tylko niektóre z nich, możesz kliknąć poszczególne zasoby, aby usunąć je pojedynczo. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano dwa IoT Edge jako bramy i ustawiono jedno jako urządzenie nadrzędne drugiego. Następnie pokazaliśmy ściąganie obrazu kontenera na urządzenie podrzędne za pośrednictwem bramy przy użyciu modułu serwera proxy IoT Edge API. Jeśli [chcesz](how-to-configure-api-proxy-module.md) dowiedzieć się więcej, zapoznaj się z przewodnikiem po użyciu modułu serwera proxy.

Aby dowiedzieć się więcej na temat tworzenia hierarchicznych warstw IoT Edge przy użyciu bram, zobacz przewodnik z przewodnikami po łączeniu urządzeń IoT Edge [podrzędnej.](how-to-connect-downstream-iot-edge-device.md)

Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"]
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
