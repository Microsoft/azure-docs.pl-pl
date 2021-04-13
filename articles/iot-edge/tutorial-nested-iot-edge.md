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
ms.openlocfilehash: bfecc88dc0c504cee615f1a3d35f9208aeb724f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309195"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Samouczek: Tworzenie hierarchii urządzeń IoT Edge

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Wdróż węzły Azure IoT Edge w różnych sieciach zorganizowanych w warstwach hierarchicznych. Każda warstwa w hierarchii jest urządzeniem bramy, które obsługuje komunikaty i żądania z urządzeń znajdujących się w warstwie poniżej.

Hierarchię urządzeń można struktury w taki sposób, aby tylko warstwa najwyższej warstwy łączyła się z chmurą, a niższe warstwy mogą komunikować się tylko z sąsiednimi warstwami Północne i Południowe. Ta warstwa sieciowa jest podstawą większości sieci przemysłowych, które są zgodne ze [standardem ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

Celem tego samouczka jest utworzenie hierarchii urządzeń IoT Edge, które symulują uproszczone środowisko produkcyjne. Na koniec zostanie wdrożony [moduł symulowanej czujnika temperatury](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) na niższym urządzeniu warstwy bez dostępu do Internetu przez pobranie obrazów kontenerów za pomocą hierarchii.

Aby osiągnąć ten cel, ten samouczek przeprowadzi Cię przez proces tworzenia hierarchii IoT Edge urządzeń, wdrażania kontenerów środowiska uruchomieniowego IoT Edge na urządzeniach oraz lokalnego konfigurowania urządzeń. W tym samouczku użyjesz zautomatyzowanego narzędzia konfiguracji, aby:

> [!div class="checklist"]
>
> * Utwórz i zdefiniuj relacje w hierarchii IoT Edge urządzeń.
> * Skonfiguruj środowisko uruchomieniowe IoT Edge na urządzeniach w hierarchii.
> * Instalowanie spójnych certyfikatów w hierarchii urządzeń.
> * Dodawanie obciążeń do urządzeń w hierarchii.
> * [Moduł Proxy IoT Edge API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) umożliwia bezpieczne kierowanie ruchu HTTP przez jeden port z niższych warstw.

>[!TIP]
>Ten samouczek zawiera kombinację ręcznych i zautomatyzowanych kroków, które zapewniają pokaz zagnieżdżonych funkcji IoT Edge.
>
>Jeśli chcesz całkowicie zautomatyzowanym sprawdzeniem, jak skonfigurować hierarchię IoT Edge urządzeń, możesz skorzystać z Azure IoT Edge skryptowych [dla przykładu przemysłowego IoT](https://aka.ms/iotedge-nested-sample). Ten scenariusz skryptowy służy do wdrażania maszyn wirtualnych platformy Azure jako wstępnie skonfigurowanych urządzeń w celu symulowania środowiska fabryki.
>
>Jeśli chcesz zapoznać się z szczegółowymi krokami tworzenia hierarchii IoT Edge urządzeń i zarządzania nią, zapoznaj [się z tematem Przewodnik po tym IoT Edge hierarchie bramy urządzeń](how-to-connect-downstream-iot-edge-device.md).

W tym samouczku zdefiniowano następujące warstwy sieci:

* **Warstwa najwyższej warstwy**: urządzenia IoT Edge w tej warstwie mogą łączyć się bezpośrednio z chmurą.

* **Niższe warstwy**: urządzenia IoT Edge na warstwach poniżej górnej warstwy nie mogą łączyć się bezpośrednio z chmurą. Aby wysyłać i odbierać dane, należy przejść przez co najmniej jedno IoT Edge urządzeń.

W tym samouczku użyto następującej hierarchii urządzeń dla uproszczenia poniżej. Jedno urządzenie **warstwy najwyższego** poziomu reprezentuje urządzenie w najwyższej warstwie hierarchii, które może połączyć się bezpośrednio z chmurą. To urządzenie będzie również określane jako **urządzenie nadrzędne**. Inne urządzenie, **urządzenie warstwy niższej**, reprezentuje urządzenie w niższej warstwie hierarchii, które nie może połączyć się bezpośrednio z chmurą. W razie potrzeby można dodać więcej mniejszych urządzeń warstwy do reprezentowania środowiska produkcyjnego. Urządzenia w niższych warstwach są również nazywane **urządzeniami podrzędnymi**.

![Struktura hierarchii samouczka zawierającego dwa urządzenia: urządzenie warstwy najwyższego poziomu i urządzenie warstwy niższej](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby utworzyć hierarchię urządzeń IoT Edge, potrzebne są:

* Komputer (system Windows lub Linux) z łącznością z Internetem.
* Konto platformy Azure z prawidłową subskrypcją. Jeśli nie masz [subskrypcji platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .
* Warstwa Bezpłatna lub standardowa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.
* Powłoka Bash w Azure Cloud Shell przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.3.1 z zainstalowaną usługą Azure IoT Extension v 0.10.6 lub nowszą. Ten samouczek używa [Azure Cloud Shell](../cloud-shell/overview.md). Jeśli nie znasz Azure Cloud Shell, [zapoznaj się z przewodnikiem Szybki Start, aby uzyskać szczegółowe informacje](./quickstart-linux.md#prerequisites).
  * Aby wyświetlić bieżące wersje modułów i rozszerzeń interfejsu wiersza polecenia platformy Azure, uruchom polecenie [AZ Version](/cli/azure/reference-index?#az_version).
* Urządzenie z systemem Linux do konfigurowania jako urządzenie IoT Edge dla każdego urządzenia w hierarchii. Ten samouczek używa dwóch urządzeń. Jeśli nie masz dostępnych urządzeń, możesz utworzyć maszyny wirtualne platformy Azure dla każdego urządzenia w hierarchii przy użyciu poniższego polecenia.

   Zastąp tekst zastępczy w poniższym poleceniu i uruchom go dwa razy dla każdej maszyny wirtualnej. Każda maszyna wirtualna wymaga unikatowego prefiksu DNS, który również będzie traktować jako nazwę. Prefiks DNS musi być zgodny z następującym wyrażeniem regularnym: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

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

   Maszyna wirtualna używa kluczy SSH do uwierzytelniania użytkowników. Jeśli nie masz doświadczenia w tworzeniu kluczy SSH i korzystaniu z nich, możesz postępować zgodnie [z instrukcjami dotyczącymi par kluczy publiczny-prywatny SSH dla maszyn wirtualnych z systemem Linux na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

   IoT Edge wersja 1,2 jest wstępnie zainstalowana z tym szablonem ARM, co pozwala zaoszczędzić konieczność ręcznej instalacji zasobów na maszynach wirtualnych. Jeśli instalujesz IoT Edge na własnych urządzeniach, zobacz [Install Azure IoT Edge for Linux (wersja 1,2)](how-to-install-iot-edge.md) lub [Update IoT Edge do wersji 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Pomyślne utworzenie maszyny wirtualnej przy użyciu tego szablonu usługi ARM spowoduje wyjście z uchwytu maszyny wirtualnej `SSH` i w pełni kwalifikowanej nazwy domeny ( `FQDN` ). Aby skonfigurować te informacje, użyj dojścia SSH i nazwy FQDN lub adresu IP każdej maszyny wirtualnej. Poniżej przedstawiono przykładowe dane wyjściowe.

   >[!TIP]
   >Możesz również znaleźć adres IP i nazwę FQDN na Azure Portal. W polu adres IP przejdź do listy maszyn wirtualnych i zanotuj **pole publiczny adres IP**. W przypadku nazwy FQDN przejdź do strony Przegląd każdej maszyny wirtualnej i Wyszukaj pole **nazwa DNS** .

   ![Maszyna wirtualna będzie wyprowadzać plik JSON po utworzeniu, który zawiera jego uchwyt SSH](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Upewnij się, że następujące porty są otwarte dla ruchu przychodzącego dla wszystkich urządzeń z wyjątkiem urządzenia najniższej warstwy: 8000, 443, 5671, 8883:
  * 8000: służy do ściągania obrazów kontenerów platformy Docker za pomocą serwera proxy interfejsu API.
  * 443: używane między nadrzędnym a podrzędnym koncentratorem krawędzi dla wywołań interfejsu API REST.
  * 5671, 8883: używany dla AMQP i MQTT.

  Aby uzyskać więcej informacji, zobacz [Jak otworzyć porty na maszynie wirtualnej przy użyciu Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

## <a name="configure-your-iot-edge-device-hierarchy"></a>Skonfiguruj hierarchię urządzeń IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Tworzenie hierarchii urządzeń IoT Edge

Urządzenia IoT Edge tworzą warstwy hierarchii. W tym samouczku utworzysz hierarchię dwóch IoT Edge urządzeń: **urządzenie warstwy najwyższego** poziomu i jego element podrzędny, **urządzenie warstwy niższej**. W razie konieczności można utworzyć dodatkowe urządzenia podrzędne.

Aby utworzyć i skonfigurować hierarchię IoT Edge urządzeń, użyjesz tego `iotedge-config` Narzędzia. To narzędzie upraszcza konfigurację hierarchii przez Automatyzowanie i kondensację kilku kroków do dwóch:

1. Skonfigurowanie konfiguracji chmury i przygotowanie każdej konfiguracji urządzenia, która obejmuje:

   * Tworzenie urządzeń w IoT Hub
   * Ustawianie relacji nadrzędny-podrzędny do autoryzacji komunikacji między urządzeniami
   * Generowanie łańcucha certyfikatów dla każdego urządzenia w celu ustanowienia bezpiecznej komunikacji między nimi
   * Generowanie plików konfiguracji dla każdego urządzenia

1. Instalowanie poszczególnych konfiguracji urządzeń, w tym:

   * Instalowanie certyfikatów na poszczególnych urządzeniach
   * Stosowanie plików konfiguracji dla każdego urządzenia

`iotedge-config`Narzędzie spowoduje również automatyczne wdrożenie modułu na urządzeniu IoT Edge.

Aby użyć `iotedge-config` Narzędzia do tworzenia i konfigurowania hierarchii programu, wykonaj następujące kroki w interfejsie wiersza polecenia platformy Azure:

1. W [Azure Cloud Shell](https://shell.azure.com/)Utwórz katalog dla zasobów samouczka:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Pobierz wersję narzędzia konfiguracji i szablony konfiguracji:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Spowoduje to utworzenie `iotedge_config_cli_release` folderu w katalogu samouczka.

   Plik szablonu używany do tworzenia hierarchii urządzeń jest `iotedge_config.yaml` plikiem znalezionym w `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . W tym samym katalogu `deploymentLowerLayer.json` to plik wdrożenia JSON zawierający instrukcje dotyczące modułów, które mają zostać wdrożone na **urządzeniu warstwy niższej**. `deploymentTopLayer.json`Plik jest taki sam, ale w przypadku **urządzenia warstwy najwyższej**, ponieważ moduły wdrożone na poszczególnych urządzeniach nie są takie same. Ten `device_config.toml` plik jest szablonem IoT Edge konfiguracjami urządzeń i zostanie użyty do automatycznego wygenerowania pakietów konfiguracyjnych dla urządzeń w hierarchii.

   Jeśli chcesz zajrzeć do kodu źródłowego i skryptów `iotedge-config` Narzędzia, zapoznaj [się z repozytorium Azure-Samples w witrynie GitHub](https://github.com/Azure-Samples/iotedge_config_cli).

1. Otwórz szablon konfiguracji samouczka i edytuj go wraz z informacjami:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   W sekcji **iothub** Wypełnij `iothub_hostname` `iothub_name` pola i. Te informacje można znaleźć na stronie Przegląd IoT Hub na Azure Portal.

   W sekcji opcjonalne **Certyfikaty** można wypełnić pola ścieżkami bezwzględnymi do certyfikatu i klucza. Pozostawienie tych pól pustego spowoduje automatyczne wygenerowanie przez skrypt certyfikatów testów z podpisem własnym do użycia. Jeśli nie znasz sposobu używania certyfikatów w scenariuszu bramy, zapoznaj [się z sekcją "certyfikat przewodnika"](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   W sekcji **Konfiguracja** `template_config_path` jest ścieżką do `device_config.toml` szablonu używanego do tworzenia konfiguracji urządzenia. To `default_edge_agent` pole określa, w jaki sposób urządzenia warstwy niższego obrazu agenta będą ściągane i od których mają być pobierane.

   W sekcji **edgedevices** w scenariuszu produkcyjnym można edytować drzewo hierarchii, aby odzwierciedlić żądaną strukturę. Na potrzeby tego samouczka zaakceptuj domyślne drzewo. Dla każdego urządzenia istnieje `device_id` pole, w którym można nazwać urządzenia. Istnieje również `deployment` pole, które określa ścieżkę do pliku JSON wdrożenia dla tego urządzenia.

   Możesz również ręcznie rejestrować IoT Edge urządzeń w IoT Hub za pomocą Azure Portal lub Azure Cloud Shell. Aby dowiedzieć się, jak [zarejestrować urządzenie IoT Edge](how-to-register-device.md), zobacz przewodnik.

   Relacje nadrzędny-podrzędny można definiować również ręcznie. Aby dowiedzieć się więcej, zobacz sekcję [Tworzenie hierarchii bramy](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) w przewodniku krok po kroku.

   ![Sekcja edgedevices pliku konfiguracji umożliwia zdefiniowanie hierarchii](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Zapisz i zamknij plik:

   `CTRL + S`, `CTRL + Q`

1. Utwórz katalog danych wyjściowych dla pakietów konfiguracyjnych w katalogu zasobów samouczka:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Przejdź do `iotedge_config_cli_release` katalogu i uruchom narzędzie, aby utworzyć hierarchię IoT Edge urządzeń:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Przy użyciu `--output` flagi narzędzie tworzy certyfikaty urządzeń, pakiety certyfikatów i plik dziennika w wybranym katalogu. Po `-f` ustawieniu flagi narzędzie automatycznie wyszuka istniejące IoT Edge urządzenia w IoT Hub i usunie je, aby uniknąć błędów i utrzymać czyszczenie centrum.

   Narzędzie konfiguracji tworzy IoT Edge urządzenia i konfiguruje relacje nadrzędny-podrzędny między nimi. Opcjonalnie tworzy certyfikaty dla urządzeń do użycia. Jeśli są podane ścieżki do JSON wdrożenia, narzędzie automatycznie utworzy te wdrożenia na urządzeniach, ale nie jest to wymagane. Na koniec narzędzie wygeneruje pakiety konfiguracyjne dla urządzeń i umieści je w katalogu wyjściowym. Aby zapoznać się z dokładniejszą procedurą wykonywaną przez narzędzie konfiguracji, zapoznaj się z plikiem dziennika w katalogu wyjściowym.

   ![Po wykonaniu skryptu zostanie wyświetlona topologia hierarchii.](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Sprawdź dokładnie, czy dane wyjściowe topologii skryptu są poprawne. Gdy Twoja hierarchia jest prawidłowo strukturalna, możesz rozpocząć pracę.

### <a name="configure-the-iot-edge-runtime"></a>Konfigurowanie środowiska uruchomieniowego usługi IoT Edge

Oprócz aprowizacji urządzeń, kroki konfiguracji ustanawiają zaufaną komunikację między urządzeniami w hierarchii przy użyciu utworzonych wcześniej certyfikatów. Kroki te zaczynają również ustalić strukturę sieci hierarchii. Warstwa najwyższego poziomu zapewnia łączność z Internetem, umożliwiając jej ściąganie obrazów dla środowiska uruchomieniowego z chmury, podczas gdy niższe urządzenia warstwowe będą kierować do tych obrazów za pośrednictwem warstwy najwyższej warstwy.

Aby skonfigurować środowisko uruchomieniowe IoT Edge, należy zastosować pakiety konfiguracyjne utworzone przez skrypt instalacyjny na urządzeniach. Konfiguracje różnią się nieco między **urządzeniem warstwy najwyższej** i **niższą warstwą**, dlatego należy zastanowić się, w jaki sposób plik konfiguracji urządzenia stosowany do poszczególnych urządzeń.

1. Każde urządzenie wymaga odpowiedniego pakietu konfiguracyjnego. Aby przenieść pakiety konfiguracyjne na poszczególne urządzenia, można użyć dysku USB lub [bezpiecznego kopiowania plików](https://www.ssh.com/ssh/scp/) .

   Upewnij się, że do każdego urządzenia jest wysyłany poprawny pakiet konfiguracji.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   `:~`Oznacza to, że folder konfiguracji zostanie umieszczony w katalogu macierzystym na maszynie wirtualnej.

1. Zaloguj się do maszyny wirtualnej, aby zastosować pakiet konfiguracyjny do urządzenia:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Na każdym urządzeniu Rozpakuj pakiet konfiguracji. Musisz najpierw zainstalować plik zip:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Na każdym urządzeniu Zastosuj pakiet konfiguracyjny do urządzenia:

   ```bash
   sudo ./install.sh
   ```

   Na **urządzeniu warstwy najwyższego** poziomu zostanie wyświetlony monit o wprowadzenie nazwy hosta. Na **urządzeniu z niższą warstwą** zostanie poproszony o podanie nazwy hosta i nazwy hosta elementu nadrzędnego. Podaj odpowiedni adres IP lub nazwę FQDN dla każdego monitu. Możesz użyć dowolnego z nich, ale w wybranym przez siebie urządzeniach. Na poniższej ilustracji przedstawiono dane wyjściowe skryptu instalacji.

   Jeśli chcesz bliżej dowiedzieć się, jakie zmiany są wprowadzane do pliku konfiguracyjnego urządzenia, zobacz [sekcję konfigurowanie IoT Edge na urządzeniach w przewodniku krok po kroku](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![Zainstalowanie pakietów konfiguracyjnych zaktualizuje pliki config. toml na urządzeniu i automatycznie uruchomi ponownie wszystkie usługi IoT Edge](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Jeśli powyższe kroki zostały wykonane prawidłowo, możesz sprawdzić, czy urządzenia są prawidłowo skonfigurowane.

Uruchom testy konfiguracji i łączności na urządzeniach. Dla **urządzenia warstwy najwyższej**:

   ```bash
   sudo iotedge check
   ```

W przypadku **urządzenia z niższą warstwą** obraz diagnostyczny musi zostać ręcznie przekazywać w poleceniu:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

Na **urządzeniu warstwy najwyższego poziomu** oczekuje się, że dane wyjściowe mają kilka ocen przekazujących. Mogą pojawić się ostrzeżenia dotyczące zasad dzienników i, w zależności od sieci, zasad DNS.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Po upewnieniu się, że konfiguracje są poprawne na każdym urządzeniu, możesz kontynuować pracę.

## <a name="deploy-modules-to-your-devices"></a>Wdrażanie modułów na urządzeniach

Wdrożenia modułów na urządzeniach zostały automatycznie wygenerowane podczas tworzenia urządzeń. W `iotedge-config-cli` przypadku **urządzeń z warstwą górną i niższą** po utworzeniu narzędzia tworzone są dane JSON dotyczące wdrażania. Wdrożenie modułu było w toku podczas konfigurowania środowiska uruchomieniowego IoT Edge na każdym urządzeniu. Po skonfigurowaniu środowiska uruchomieniowego na **urządzeniu warstwy najwyższej** zostanie rozpoczęte wdrażanie. Po zakończeniu tych wdrożeń **urządzenie warstwy niższej** może użyć modułu **proxy IoT Edge API** do ściągania niezbędnych obrazów.

W [Azure Cloud Shell](https://shell.azure.com/)można zapoznać się z elementem JSON wdrożenia **urządzenia warstwy najwyższego** poziomu, aby zrozumieć, jakie moduły zostały wdrożone na urządzeniu:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Oprócz modułów środowiska uruchomieniowego **IoT Edge agenta** i **Centrum IoT Edge**, **urządzenie warstwy najwyższej** odbiera moduł **rejestru platformy docker** i moduł **proxy interfejsu API IoT Edge** .

Moduł **rejestru platformy Docker** wskazuje istniejący Azure Container Registry. W tym przypadku `REGISTRY_PROXY_REMOTEURL` wskazuje na Container Registry firmy Microsoft. W programie `createOptions` można zobaczyć, czy komunikuje się on z portem 5000.

Moduł **serwera proxy interfejsu API IoT Edge** kieruje żądania HTTP do innych modułów, umożliwiając niższym urządzeniom warstwy ściąganie obrazów kontenera lub wypychanie obiektów BLOB do magazynu. W tym samouczku komunikuje się on z portem 8000 i jest skonfigurowany do wysyłania żądań ściągnięcia obrazów kontenera Docker do modułu **rejestru platformy Docker** na porcie 5000. Ponadto wszystkie żądania przekazywania magazynu obiektów BLOB są kierowane do modułu AzureBlobStorageonIoTEdge na porcie 11002. Aby uzyskać więcej informacji na temat modułu **serwera proxy interfejsu API IoT Edge** i sposobu jego konfiguracji, zobacz [Przewodnik po podręczniku](how-to-configure-api-proxy-module.md)modułu.

Jeśli chcesz dowiedzieć się, jak utworzyć wdrożenie podobne do tego za pomocą Azure Portal lub Azure Cloud Shell, zobacz [sekcję najważniejsze urządzenie warstwowe przewodnika](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

W [Azure Cloud Shell](https://shell.azure.com/)możesz zapoznać się z kodem JSON wdrożenia **urządzenia warstwy niższej** , aby zrozumieć, jakie moduły zostały wdrożone na urządzeniu:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

Można zobaczyć, `systemModules` że moduły środowiska uruchomieniowego **urządzenia warstwy niższej** są skonfigurowane do ściągania z `$upstream:8000` , a nie `mcr.microsoft.com` jako **urządzenia warstwy najwyższej** . **Urządzenie warstwy niższej** wysyła żądanie obrazu platformy Docker do modułu **serwera Proxy interfejsu API IoT Edge** na porcie 8000, ponieważ nie może bezpośrednio pobrać obrazów z chmury. Drugi moduł wdrożony na **niższym urządzeniu warstwy**, moduł **symulowane czujnika temperatury** , również wysyła żądanie obrazu do programu `$upstream:8000` .

Jeśli chcesz dowiedzieć się, jak utworzyć wdrożenie podobne do tego za pomocą Azure Portal lub Azure Cloud Shell, zobacz [sekcję dotyczącą urządzenia warstwy niższej w przewodniku](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

Stan modułów można wyświetlić za pomocą polecenia:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   To polecenie spowoduje wyjście wszystkich edgeAgent raportowanych właściwości. Poniżej przedstawiono kilka przydatnych do monitorowania stanu urządzenia: *stan środowiska uruchomieniowego*, *czas rozpoczęcia środowiska uruchomieniowego*, *czas ostatniego zakończenia* wykonywania, *liczba ponownych uruchomień środowiska uruchomieniowego*.

Stan modułów można także sprawdzić na [Azure Portal](https://ms.portal.azure.com/). Przejdź do sekcji **IoT Edge** IoT Hub, aby zobaczyć swoje urządzenia i moduły.

Gdy Twoje wdrożenia modułów są zadowalające, możesz kontynuować pracę.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

Moduł **symulowane czujnika temperatury** , który został wypychany, generuje przykładowe dane środowiska. Wysyła komunikaty, które obejmują temperaturę otoczenia i wilgotność, temperaturę i ciśnienie maszyny oraz sygnaturę czasową.

Te komunikaty można także wyświetlić za pomocą [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Uruchom `iotedge check` polecenie, aby zweryfikować konfigurację i rozwiązać problemy z błędami.

Można uruchamiać `iotedge check` w hierarchii zagnieżdżonej, nawet jeśli komputery podrzędne nie mają bezpośredniego dostępu do Internetu.

Po uruchomieniu `iotedge check` z warstwy niższej program próbuje ściągnąć obraz z elementu nadrzędnego za pośrednictwem portu 443.

W tym samouczku używamy portu 8000, dlatego musimy go określić:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics`Wartość jest pobierana z rejestru kontenerów połączonej z modułem rejestru. Ten samouczek jest domyślnie ustawiony na https://mcr.microsoft.com:

| Nazwa | Wartość |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Jeśli używasz prywatnego rejestru kontenerów, upewnij się, że wszystkie obrazy (IoTEdgeAPIProxy, edgeAgent, edgeHub, symulowany czujnik temperatury i Diagnostyka) znajdują się w rejestrze kontenerów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć konfiguracje lokalne i zasoby platformy Azure, które zostały utworzone w tym artykule, aby uniknąć naliczania opłat.

Aby usunąć zasoby:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. Wybierz nazwę grupy zasobów, która zawiera zasoby testowe usługi IoT Edge. 

3. Przejrzyj listę zasobów znajdujących się w grupie zasobów. Jeśli chcesz usunąć je wszystkie, możesz wybrać pozycję **Usuń grupę zasobów**. Jeśli chcesz usunąć tylko niektóre z nich, możesz kliknąć poszczególne zasoby, aby usunąć je pojedynczo. 

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano dwa IoT Edge urządzeń jako bramy i ustawili je jako urządzenie nadrzędne. Następnie pokazano, jak utworzyć obraz kontenera na urządzeniu podrzędnym za pośrednictwem bramy przy użyciu modułu proxy interfejsu API IoT Edge. Jeśli chcesz dowiedzieć się więcej, zobacz [Przewodnik po użyciu modułu proxy](how-to-configure-api-proxy-module.md) .

Aby dowiedzieć się więcej o używaniu bram do tworzenia warstw hierarchicznych IoT Edge urządzeń, zobacz [Przewodnik po tym, jak podłączyć urządzenia podrzędne IoT Edge](how-to-connect-downstream-iot-edge-device.md).

Aby zobaczyć, jak w usłudze Azure IoT Edge utworzyć więcej rozwiązań dla swojej firmy, przejdź do kolejnych samouczków.

> [!div class="nextstepaction"]
> [Wdrażanie modelu usługi Azure Machine Learning jako modułu](tutorial-deploy-machine-learning.md)
