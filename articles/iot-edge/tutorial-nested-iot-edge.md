---
title: Samouczek — Tworzenie hierarchii IoT Edge urządzeń — Azure IoT Edge
description: W tym samouczku pokazano, jak utworzyć hierarchiczną strukturę IoT Edge urządzeń przy użyciu bram.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 3f997b577fb473e30fbafec08c4e68547a641fa3
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200093"
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
> * [Moduł Proxy IoT Edge API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) umożliwia bezpieczne kierowanie ruchu HTTP przez jeden port z niższych warstw.

W tym samouczku zdefiniowano następujące warstwy sieci:

* **Warstwa najwyższej warstwy**: urządzenia IoT Edge w tej warstwie mogą łączyć się bezpośrednio z chmurą.

* **Niższe warstwy**: urządzenia IoT Edge na warstwach poniżej górnej warstwy nie mogą łączyć się bezpośrednio z chmurą. Aby wysyłać i odbierać dane, należy przejść przez co najmniej jedno IoT Edge urządzeń.

W tym samouczku użyto następującej hierarchii urządzeń dla uproszczenia poniżej. Jedno urządzenie **warstwy najwyższego** poziomu reprezentuje urządzenie w najwyższej warstwie hierarchii, które może połączyć się bezpośrednio z chmurą. To urządzenie będzie również określane jako **urządzenie nadrzędne**. Inne urządzenie, **urządzenie warstwy niższej**, reprezentuje urządzenie w niższej warstwie hierarchii, które nie może połączyć się bezpośrednio z chmurą. W razie potrzeby można dodać kolejne urządzenia warstwy niższej do reprezentowania środowiska produkcyjnego. Urządzenia w niższych warstwach są również nazywane **urządzeniami podrzędnymi**. Konfiguracja wszelkich dodatkowych urządzeń warstwy niższej będzie zgodna z konfiguracją **urządzenia warstwy niższej**.

![Struktura hierarchii samouczka zawierającego dwa urządzenia: urządzenie warstwy najwyższego poziomu i urządzenie warstwy niższej](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć hierarchię urządzeń IoT Edge, potrzebne są:

* Komputer (system Windows lub Linux) z łącznością z Internetem.
* Konto platformy Azure z prawidłową subskrypcją. Jeśli nie masz [subskrypcji platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Warstwa Bezpłatna lub standardowa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.
* Interfejs wiersza polecenia platformy Azure w wersji 2.3.1 z zainstalowaną usługą Azure IoT Extension v 0.10.6 lub nowszą. Ten samouczek używa [Azure Cloud Shell](../cloud-shell/overview.md). Jeśli nie znasz Azure Cloud Shell, [zapoznaj się z przewodnikiem Szybki Start, aby uzyskać szczegółowe informacje](./quickstart-linux.md#prerequisites).
  * Aby wyświetlić bieżące wersje modułów i rozszerzeń interfejsu wiersza polecenia platformy Azure, uruchom polecenie [AZ Version](/cli/azure/reference-index?#az_version).
* Urządzenie z systemem Linux do konfigurowania jako urządzenie IoT Edge dla każdego urządzenia w hierarchii. Ten samouczek używa dwóch urządzeń. Jeśli nie masz dostępnych urządzeń, możesz utworzyć maszyny wirtualne platformy Azure dla każdego urządzenia w hierarchii, zastępując tekst symbolu zastępczego w następującym poleceniu i uruchamiając go:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Upewnij się, że następujące porty są otwarte dla ruchu przychodzącego: 8000, 443, 5671, 8883:
  * 8000: służy do ściągania obrazów kontenerów platformy Docker za pomocą serwera proxy interfejsu API.
  * 443: używane między nadrzędnym a podrzędnym koncentratorem krawędzi dla wywołań interfejsu API REST.
  * 5671, 8883: używany dla AMQP i MQTT.

  Aby uzyskać więcej informacji, zobacz [Jak otwierać porty do maszyny wirtualnej z portalu Azure](../virtual-machines/windows/nsg-quickstart-portal.md).

>[!TIP]
>Jeśli chcesz automatycznie sprawdzić konfigurację hierarchii IoT Edge urządzeń, możesz użyć Azure IoT Edge skryptowych [dla przykładu usługi IoT](https://aka.ms/iotedge-nested-sample). Ten scenariusz skryptowy służy do wdrażania maszyn wirtualnych platformy Azure jako wstępnie skonfigurowanych urządzeń w celu symulowania środowiska fabryki.
>
>Jeśli chcesz kontynuować tworzenie przykładowej hierarchii krok po kroku, wykonaj kroki opisane poniżej.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Skonfiguruj hierarchię urządzeń IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Tworzenie hierarchii urządzeń IoT Edge

Urządzenia IoT Edge tworzą warstwy hierarchii. W tym samouczku utworzysz hierarchię dwóch IoT Edge urządzeń: **urządzenie warstwy najwyższego** poziomu i jego element podrzędny, **urządzenie warstwy niższej**. W razie konieczności można utworzyć dodatkowe urządzenia podrzędne.

Aby utworzyć urządzenia IoT Edge, można użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Wybierz pozycję **+ Dodaj urządzenie IoT Edge**. To urządzenie będzie urządzeniem warstwy najwyższej, więc wprowadź odpowiedni unikatowy identyfikator urządzenia. Wybierz pozycję **Zapisz**.

1. Ponownie wybierz pozycję **+ Dodaj urządzenie IoT Edge** . To urządzenie będzie urządzeniem warstwy niższej, więc wprowadź odpowiedni unikatowy identyfikator urządzenia.

1. Wybierz pozycję **Ustaw urządzenie nadrzędne**, wybierz z listy urządzeń urządzenie z najwyższą warstwą, a następnie wybierz **przycisk OK**. Wybierz pozycję **Zapisz**.

   ![Ustawianie elementu nadrzędnego dla urządzenia warstwy niższej](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. W [Azure Cloud Shell](https://shell.azure.com/)wprowadź następujące polecenie, aby utworzyć urządzenie IoT Edge w centrum. To urządzenie będzie urządzeniem warstwy najwyższej, więc wprowadź odpowiedni unikatowy identyfikator urządzenia:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   Pomyślne utworzenie urządzenia spowoduje wyjście z konfiguracji JSON urządzenia.

   ![Dane wyjściowe JSON dotyczące pomyślnego utworzenia urządzenia](./media/tutorial-nested-iot-edge/json-success-output.png)

1. Wprowadź następujące polecenie, aby utworzyć niższą warstwę urządzenie IoT Edge. Jeśli potrzebujesz więcej warstwy w hierarchii, możesz utworzyć więcej niż jedno urządzenie warstwy niższej. Upewnij się, że dla każdego z nich są podano unikatowe tożsamości urządzeń.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Wprowadź następujące polecenie, aby zdefiniować każdą relację nadrzędny-podrzędny między **urządzeniem warstwy najwyższej** i każdym **urządzeniem warstwy niższej**. Upewnij się, że polecenie jest uruchamiane dla każdego urządzenia warstwy niższej w hierarchii.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   To polecenie nie ma jawnych danych wyjściowych.

---

Następnie zanotuj parametry połączenia poszczególnych urządzeń IoT Edge. Zostaną one użyte później.

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

Jeśli powyższe kroki zostały wykonane prawidłowo, można wykonać następujące czynności, aby sprawdzić, czy relacje nadrzędny-podrzędny są poprawne w Azure Portal lub interfejsie wiersza polecenia platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do sekcji **IoT Edge** w IoT Hub.

1. Na liście urządzeń kliknij identyfikator urządzenia **niższej warstwy** .

1. Na stronie szczegółów urządzenia powinna zostać wyświetlona tożsamość **urządzenia warstwy najwyższego poziomu** na liście obok pola **urządzenie nadrzędne** .

   [Urządzenie nadrzędne potwierdzone przez urządzenie podrzędne](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

Możesz również obsłużyć podrzędną relację hierarchii za poorednictwem **warstwy najwyższego poziomu**.

1. Na liście urządzeń kliknij identyfikator urządzenia **warstwy najwyższego** poziomu.

1. Wybierz kartę **Zarządzanie urządzeniami podrzędnymi** w górnej części ekranu.

1. Na liście urządzeń powinna zostać wyświetlona **urządzenie warstwy niższej**.

   [Urządzenie podrzędne potwierdzone przez urządzenie nadrzędne](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. W [Azure Cloud Shell](https://shell.azure.com/) można sprawdzić, czy wszystkie urządzenia podrzędne pomyślnie ustanowiły swoje relacje z urządzeniem nadrzędnym, pobierając tożsamość potwierdzonego urządzenia nadrzędnego urządzenia podrzędnego. Spowoduje to wyjście z konfiguracji JSON urządzenia nadrzędnego, na zdjęciu powyżej:

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

Możesz również uzyskać podrzędną relację hierarchii za poorednictwem zapytania dotyczącego **urządzenia warstwy najwyższej**.

1. Wyświetl listę urządzeń podrzędnych znanego na urządzeniu nadrzędnym:

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

Gdy Twoja hierarchia jest prawidłowo strukturalna, możesz rozpocząć pracę.

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
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy, ale używamy następującego certyfikatu i pary kluczy na każdym urządzeniu IoT Edge i przywoływany w pliku konfiguracji:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Każde urządzenie musi mieć kopię certyfikatu głównego urzędu certyfikacji i kopię własnego certyfikatu urzędu certyfikacji urządzenia oraz klucza prywatnego. Aby przenieść certyfikaty na poszczególne urządzenia, można użyć dysku USB lub [bezpiecznego kopiowania plików](https://www.ssh.com/ssh/scp/) .

1. Po przeniesieniu certyfikatów Zainstaluj główny urząd certyfikacji dla każdego urządzenia.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   To polecenie powinno spowodować, że jeden certyfikat został dodany w programie `/etc/ssl/certs` .

   [Komunikat instalacji pomyślnego certyfikatu](./media/tutorial-nested-iot-edge/certificates-success-output.png)

Jeśli powyższe kroki zostały wykonane prawidłowo, możesz sprawdzić, czy certyfikaty są zainstalowane w programie, `/etc/ssl/certs` przechodząc do tego katalogu i wyszukując zainstalowane certyfikaty.

1. Przejdź do `/etc/ssl/certs` :

   ```bash
   cd /etc/ssl/certs
   ```

1. Wyświetl listę zainstalowanych certyfikatów i `grep` dla programu `azure` :

   ```bash
   ll | grep azure
   ```

   Powinien zostać wyświetlony skrót certyfikatu połączony z certyfikatem głównego urzędu certyfikacji oraz certyfikat głównego urzędu certyfikacji połączony z kopią w `usr/local/share` katalogu.

   [Wyniki wyszukiwania certyfikatów platformy Azure](./media/tutorial-nested-iot-edge/certificate-list-results.png)

Gdy certyfikaty zostaną zainstalowane na każdym urządzeniu, możesz rozpocząć pracę.

### <a name="install-iot-edge-on-the-devices"></a>Instalowanie IoT Edge na urządzeniach

Zainstalowanie obrazów środowiska uruchomieniowego IoT Edge w wersji 1,2 zapewnia urządzeniom dostęp do funkcji niezbędnych do działania jako hierarchia urządzeń.

Aby zainstalować IoT Edge, należy zainstalować odpowiednią konfigurację repozytorium, zainstalować wymagania wstępne i zainstalować wymagane zasoby wydania.

1. Zainstaluj konfigurację repozytorium zgodną z systemem operacyjnym urządzenia.

   * **Ubuntu Server 18,04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Rozciąganie systemu operacyjnego Raspberry Pi**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. Skopiuj wytworzoną listę do katalogu sources. list. d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Zainstaluj klucz publiczny programu Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

1. Zainstaluj aparat Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Zainstaluj IoT Edge i usługę tożsamość IoT.

   ```bash
   sudo apt-get install aziot-edge
   ```

Jeśli powyższe kroki zostały wykonane prawidłowo, zobaczysz komunikat transparentu Azure IoT Edge żądanie zaktualizowania Azure IoT Edge pliku konfiguracji, `/etc/aziot/config.toml` na każdym urządzeniu w hierarchii. Jeśli tak, wszystko jest gotowe do dalszej pracy.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi IoT Edge

Oprócz aprowizacji urządzeń, kroki konfiguracji ustanawiają zaufaną komunikację między urządzeniami w hierarchii przy użyciu utworzonych wcześniej certyfikatów. Kroki te zaczynają również ustalić strukturę sieci hierarchii. Warstwa najwyższego poziomu zapewnia łączność z Internetem, umożliwiając jej ściąganie obrazów dla środowiska uruchomieniowego z chmury, podczas gdy niższe urządzenia warstwowe będą kierować do tych obrazów za pośrednictwem warstwy najwyższej warstwy.

Aby skonfigurować środowisko uruchomieniowe IoT Edge, należy zmodyfikować kilka składników pliku konfiguracyjnego. Konfiguracje różnią się nieco między **urządzeniem warstwy najwyższej** i **niższą warstwą**, dlatego należy zastanowić się, jak plik konfiguracyjny urządzenia, który edytujesz dla każdego kroku. Skonfigurowanie środowiska uruchomieniowego IoT Edge dla urządzeń obejmuje cztery kroki, które można wykonać, edytując plik konfiguracji IoT Edge:

* Ręcznie Zainicjuj obsługę każdego urządzenia, dodając parametry połączenia tego urządzenia do pliku konfiguracji.

* Zakończ Konfigurowanie certyfikatów urządzenia, wskazując plik konfiguracji na certyfikat urzędu certyfikacji urządzenia, klucz prywatny urzędu certyfikacji urządzenia i certyfikat głównego urzędu certyfikacji.

* Załadowania systemu przy użyciu agenta IoT Edge.

* Zaktualizuj parametr **hostname** dla urządzenia **warstwy najwyższego poziomu** i zaktualizuj parametr **hostname** oraz parametr **parent_hostname** dla urządzeń z **niższą warstwą** .

Wykonaj te kroki i uruchom ponownie usługę IoT Edge, aby skonfigurować urządzenia.

>[!TIP]
>Podczas nawigowania w pliku konfiguracji programu nano można użyć **kombinacji klawiszy Ctrl + w** , aby wyszukać słowa kluczowe w pliku.

1. Na każdym urządzeniu Utwórz plik konfiguracji na podstawie dołączonego szablonu.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. W przypadku urządzenia **warstwy najwyższego poziomu** Znajdź sekcję **Nazwa hosta** . Usuń komentarz z wiersza z `hostname` parametrem i zaktualizuj wartość jako w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP urządzenia IoT Edge. Należy używać wybranej wartości, która jest spójna na urządzeniach w hierarchii.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

1. W przypadku dowolnego IoT Edge urządzenia w **niższych warstwach** Znajdź sekcję **nadrzędna nazwa hosta** . Usuń komentarz z wiersza z `parent_hostname` parametrem i zaktualizuj wartość tak, aby wskazywała nazwę FQDN lub adres IP urządzenia nadrzędnego. Użyj dokładnej wartości wprowadzonej w polu **Nazwa hosta** urządzenia nadrzędnego. Dla urządzenia IoT Edge w **górnej warstwie** Pozostaw ten parametr komentarz.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > W przypadku hierarchii z więcej niż jedną niższą warstwą należy zaktualizować pole *parent_hostname* za pomocą nazwy FQDN urządzenia w warstwie bezpośrednio powyżej.

1. Znajdź sekcję **certyfikat pakietu zaufania** pliku. Usuń komentarz z wiersza z `trust_bundle_cert` parametrem i zaktualizuj wartość przy użyciu ścieżki URI pliku do certyfikatu głównego urzędu certyfikacji, który jest współużytkowany przez wszystkie urządzenia w hierarchii bramy.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. Znajdź sekcję **aprowizacji** pliku. Usuń znaczniki komentarza z wierszy do **ręcznego inicjowania obsługi przy użyciu parametrów połączenia**. Dla każdego urządzenia w hierarchii należy zaktualizować wartość **connection_string** przy użyciu parametrów połączenia z urządzenia IoT Edge.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. Znajdź **domyślną sekcję Agent graniczny** .

   * W przypadku urządzenia **warstwy najwyższego poziomu** należy zaktualizować wartość obrazu edgeAgent do publicznej wersji zapoznawczej modułu w Azure Container Registry.
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * Dla każdego urządzenia IoT Edge w **niższych warstwach** zaktualizuj obraz edgeAgent w taki sposób, aby wskazywał urządzenie nadrzędne, a następnie port, na którym nasłuchuje serwer proxy interfejsu API. Moduł proxy interfejsu API zostanie wdrożony na urządzeniu nadrzędnym w następnej sekcji.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. Znajdź sekcję **certyfikatu brzegowego urzędu certyfikacji** . Usuń komentarz z pierwszych trzech wierszy tej sekcji. Następnie należy zaktualizować dwa parametry, aby wskazywały na certyfikat urzędu certyfikacji urządzenia i pliki kluczy prywatnych urzędu certyfikacji, które zostały utworzone w poprzedniej sekcji i przeniesione na urządzenie IoT Edge. Podaj ścieżki URI pliku, które mają format, na przykład `file:///<path>/<filename>` `file:///certs/iot-edge-device-ca-top-layer-device.key.pem` .

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >Upewnij się, że używasz ścieżki certyfikatu **pełnego łańcucha** i nazwy pliku, aby wypełnić `device_ca_cert` pole.

1. Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

1. Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji Zastosuj zmiany:

   ```bash
   sudo iotedge config apply
   ```

Przed kontynuowaniem upewnij się, że zaktualizowano plik konfiguracyjny każdego urządzenia w hierarchii. W zależności od struktury hierarchii należy skonfigurować jedno **urządzenie warstwy najwyższego poziomu** i co najmniej jedno **urządzenie niższej warstwy**.

Jeśli powyższe kroki zostały wykonane prawidłowo, możesz sprawdzić, czy urządzenia są prawidłowo skonfigurowane.

1. Uruchom testy konfiguracji i łączności na urządzeniach:

   ```bash
   sudo iotedge check
   ```

Na **urządzeniu warstwy najwyższego poziomu** należy zobaczyć dane wyjściowe z kilkoma testami przekazywania i co najmniej jednym ostrzeżeniem. W `latest security daemon` wyniku sprawdzenia zostanie wyświetlone ostrzeżenie, że inna wersja IoT Edge to najnowsza stabilna wersja, ponieważ IoT Edge wersja 1,2 jest w publicznej wersji zapoznawczej. Mogą pojawić się dodatkowe ostrzeżenia dotyczące zasad dzienników i, w zależności od sieci, zasad DNS.

Na **niższym urządzeniu warstwy** należy zobaczyć dane wyjściowe podobne do najwyższego urządzenia warstwy, ale z dodatkowym ostrzeżeniem wskazującą, że nie można ściągnąć modułu EdgeAgent z nadrzędnego. Jest to dopuszczalne, ponieważ moduł proxy interfejsu API IoT Edge i moduł Docker Container Registry, które warstwy niższego poziomu będą ściągać obrazy, nie są jeszcze wdrożone na **urządzeniach warstwy najwyższej**.

Poniżej pokazano przykładowe dane wyjściowe `iotedge check` :

[Przykładowa konfiguracja i wyniki łączności](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

Po upewnieniu się, że konfiguracje są poprawne na każdym urządzeniu, możesz kontynuować pracę.

## <a name="deploy-modules-to-the-top-layer-device"></a>Wdrażanie modułów na urządzeniu warstwy najwyższej

Moduły umożliwiają ukończenie wdrożenia i środowisko uruchomieniowe IoT Edge na urządzeniach oraz dokładniejsze zdefiniowanie struktury hierarchii. Moduł proxy usługi IoT Edge API bezpiecznie routs ruch HTTP przez jeden port z urządzeń warstwy niższej. Moduł Docker Registry umożliwia korzystanie z repozytorium obrazów platformy Docker, do których urządzenia warstwy niższej mogą uzyskać dostęp za pomocą ściągania obrazu do warstwy najwyższego poziomu.

Aby wdrożyć moduły na urządzeniu warstwy najwyższej, możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure.

>[!NOTE]
>Pozostałe kroki służące do ukończenia konfiguracji środowiska uruchomieniowego IoT Edge i wdrożenia obciążeń nie są wykonywane na urządzeniach IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [Azure Portal](https://ms.portal.azure.com/):

1. Przejdź do IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Kliknij identyfikator urządzenia na urządzeniu brzegowym **najwyższej warstwy** na liście urządzeń.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

1. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** obok ikony koła zębatego.

1. W obszarze **centrum brzegowe** w polu obraz wprowadź `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

   ![Edytuj obraz centrum brzegowego](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Dodaj następujące zmienne środowiskowe do modułu centrum brzegowego:

    | Nazwa | Wartość |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Edytuj zmienne środowiskowe centrum brzegowego](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. W obszarze **Agent Edge** w polu obraz wprowadź wartość `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4` . Wybierz pozycję **Zapisz**.

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
                       "registry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
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

Jeśli powyższe kroki zostały wykonane prawidłowo, **urządzenie warstwy najwyższego poziomu** powinno zgłosić cztery moduły: moduł proxy interfejsu API IoT Edge, moduł docker Container Registry i moduły systemowe, jak **określono we wdrożeniu**. Odebranie nowego wdrożenia przez urządzenie i uruchomienie modułów może potrwać kilka minut. Odśwież stronę do momentu wyświetlenia modułu czujnika temperatury wymienionego na liście **zgłoszone przez urządzenie**. Po zgłoszeniu modułów przez urządzenie możesz kontynuować pracę.

## <a name="deploy-modules-to-the-lower-layer-device"></a>Wdrażanie modułów na urządzeniu warstwy niższej

Moduły są również używane jako obciążenia urządzeń niższej warstwy. Moduł symulowane czujnika temperatury tworzy przykładowe dane telemetryczne w celu zapewnienia funkcjonalnego przepływu danych w hierarchii urządzeń.

Aby wdrożyć moduły na urządzeniach warstwy niższej, możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W [Azure Portal](https://ms.portal.azure.com/):

1. Przejdź do IoT Hub.

1. W menu po lewej stronie w obszarze **Automatyczne zarządzanie urządzeniami** wybierz pozycję **IoT Edge**.

1. Na liście urządzeń IoT Edge kliknij identyfikator urządzenia warstwy niższej.

1. Na górnym pasku wybierz pozycję **Ustaw moduły**.

1. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** obok ikony koła zębatego.

1. W obszarze **centrum brzegowe** w polu obraz wprowadź `$upstream:8000/azureiotedge-hub:1.2.0-rc4` .

1. Dodaj następujące zmienne środowiskowe do modułu centrum brzegowego:

    | Nazwa | Wartość |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. W obszarze **Agent Edge** w polu obraz wprowadź wartość `$upstream:8000/azureiotedge-agent:1.2.0-rc4` . Wybierz pozycję **Zapisz**.

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
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
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

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics`Wartość jest pobierana z rejestru kontenerów połączonej z modułem rejestru. Ten samouczek jest domyślnie ustawiony na https://mcr.microsoft.com:

| Nazwa | Wartość |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Jeśli używasz prywatnego rejestru kontenerów, upewnij się, że wszystkie obrazy (na przykład IoTEdgeAPIProxy, edgeAgent, edgeHub i Diagnostics) znajdują się w rejestrze kontenerów.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Moduł **symulowane czujnika temperatury** , który został wypychany, generuje przykładowe dane środowiska. Wysyła komunikaty, które obejmują temperaturę otoczenia i wilgotność, temperaturę i ciśnienie maszyny oraz sygnaturę czasową.

Możesz obejrzeć komunikaty docierające do centrum IoT Hub przy użyciu [rozszerzenia IoT Hub platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Te komunikaty można także wyświetlić za pomocą [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały utworzone w tym artykule, aby uniknąć naliczania opłat.

Aby usunąć zasoby:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. Wybierz nazwę grupy zasobów, która zawiera zasoby testowe usługi IoT Edge. 

3. Przejrzyj listę zasobów znajdujących się w grupie zasobów. Jeśli chcesz usunąć je wszystkie, możesz wybrać pozycję **Usuń grupę zasobów**. Jeśli chcesz usunąć tylko niektóre z nich, możesz kliknąć poszczególne zasoby, aby usunąć je pojedynczo. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano dwa IoT Edge urządzeń jako bramy i ustawili je jako urządzenie nadrzędne. Następnie pokazano, jak utworzyć obraz kontenera na urządzeniu podrzędnym za pomocą bramy.

Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"]
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
