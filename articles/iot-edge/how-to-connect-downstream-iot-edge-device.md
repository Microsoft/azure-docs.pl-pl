---
title: Połącz IoT Edge podrzędne urządzenia — Azure IoT Edge | Microsoft Docs
description: Jak skonfigurować urządzenie IoT Edge do nawiązywania połączenia z urządzeniami bramy Azure IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1258fd4b5c69b399b70d1f2db1be63765771e631
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629407"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Łączenie urządzenia podrzędnego IoT Edge z bramą Azure IoT Edge (wersja zapoznawcza)

Ten artykuł zawiera instrukcje dotyczące ustanawiania zaufanego połączenia między bramą IoT Edge i IoT Edge urządzeniem podrzędnym.

>[!NOTE]
>Ta funkcja wymaga IoT Edge w wersji 1,2, która jest w publicznej wersji zapoznawczej z uruchomionymi kontenerami systemu Linux.

W scenariuszu bramy urządzenie IoT Edge może być zarówno bramą, jak i urządzeniem podrzędnym. Wiele bram IoT Edge można przydzielić warstwami, aby utworzyć hierarchię urządzeń. Urządzenia podrzędne (lub potomne) mogą uwierzytelniać i wysyłać lub odbierać wiadomości za pomocą urządzenia bramy (lub nadrzędnego).

Istnieją dwie różne konfiguracje IoT Edge urządzeń w hierarchii bramy i ten artykuł dotyczy obu tych konfiguracji. Pierwszy jest **najwyższego poziomu** IoT Edge urządzeniu. Gdy wiele urządzeń IoT Edge nawiązuje połączenie przez siebie, każde urządzenie, które nie ma urządzenia nadrzędnego, ale łączy się bezpośrednio z IoT Hub jest uznawane za znajdujące się w górnej warstwie. To urządzenie jest odpowiedzialne za obsługę żądań ze wszystkich urządzeń znajdujących się poniżej. Druga konfiguracja ma zastosowanie do każdego urządzenia IoT Edge w **niższej warstwie** hierarchii. Te urządzenia mogą być bramami dla innych podrzędnych urządzeń IoT i IoT Edge, ale muszą również kierować każdą komunikację za pośrednictwem własnych urządzeń nadrzędnych.

Niektóre architektury sieciowe wymagają, aby tylko górne urządzenie IoT Edge w hierarchii mogły łączyć się z chmurą. W tej konfiguracji wszystkie urządzenia IoT Edge w niższych warstwach hierarchii mogą komunikować się tylko z urządzeniem bramy (lub nadrzędnym) i wszystkimi urządzeniami podrzędnymi (lub podrzędnymi).

Wszystkie kroki opisane w tym artykule umożliwiają kompilację na temat [konfigurowania urządzenia IoT Edge jako niejawnej bramy](how-to-create-transparent-gateway.md), która konfiguruje urządzenie IoT Edge jako bramę dla urządzeń z systemem IoT. Te same podstawowe kroki dotyczą wszystkich scenariuszy bramy:

* **Uwierzytelnianie**: utwórz tożsamości IoT Hub dla wszystkich urządzeń w hierarchii bramy.
* **Autoryzacja**: Skonfiguruj relację nadrzędną/podrzędną w IoT Hub, aby autoryzować urządzenia podrzędne do łączenia się z urządzeniem nadrzędnym, tak jak w przypadku łączenia się z IoT Hub.
* **Odnajdywanie bramy**: Upewnij się, że urządzenie podrzędne może znaleźć swoje urządzenie nadrzędne w sieci lokalnej.
* **Bezpieczne połączenie**: Ustanów bezpieczne połączenie z zaufanymi certyfikatami, które są częścią tego samego łańcucha.

## <a name="prerequisites"></a>Wymagania wstępne

* Bezpłatna lub standardowa wersja Centrum IoT Hub.
* Co najmniej dwa **urządzenia IoT Edge**, jeden to jako urządzenie warstwy najwyższego poziomu i jedno lub więcej urządzeń niższej warstwy. Jeśli nie masz dostępnych urządzeń IoT Edge, możesz [uruchomić Azure IoT Edge na maszynach wirtualnych Ubuntu](how-to-install-iot-edge-ubuntuvm.md).
* Jeśli używasz interfejsu wiersza polecenia platformy Azure do tworzenia urządzeń i zarządzania nimi, użyj interfejsu wiersza polecenia platformy Azure w wersji 2.3.1 z zainstalowaną usługą Azure IoT Extension v 0.10.6 lub nowszą.

Ten artykuł zawiera szczegółowe instrukcje i opcje ułatwiające utworzenie odpowiedniej hierarchii bramy dla danego scenariusza. Samouczek z przewodnikiem zawiera temat [Tworzenie hierarchii IoT Edge urządzeń przy użyciu bram](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Tworzenie hierarchii bramy

Hierarchię bramy IoT Edge można utworzyć, definiując relacje nadrzędny/podrzędny dla urządzeń IoT Edge w tym scenariuszu. Można ustawić urządzenie nadrzędne podczas tworzenia nowej tożsamości urządzenia lub można zarządzać nadrzędnym i podrzędnym identyfikatorem istniejącej tożsamości urządzenia.

Krok konfigurowania relacji nadrzędny/podrzędny zezwala urządzeniom podrzędnym na łączenie się z urządzeniem nadrzędnym, tak jak w przypadku łączenia się z IoT Hub.

Tylko urządzenia IoT Edge mogą być urządzeniami nadrzędnymi, ale zarówno urządzenia IoT Edge, jak i urządzenia IoT mogą być elementami podrzędnymi. Element nadrzędny może mieć wiele elementów podrzędnych, ale element podrzędny może mieć tylko jeden element nadrzędny. Hierarchia bramy jest tworzona przez łańcuch zestawów nadrzędnych/podrzędnych, dzięki czemu element podrzędny jednego urządzenia jest elementem nadrzędnym innego.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal można zarządzać relacją nadrzędny/podrzędny podczas tworzenia nowych tożsamości urządzeń lub przez edycję istniejących urządzeń.

Podczas tworzenia nowego urządzenia IoT Edge istnieje możliwość wyboru urządzeń nadrzędnych i podrzędnych z listy istniejących urządzeń IoT Edge w tym centrum.

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz pozycję **Dodaj urządzenie IoT Edge**.
1. Wraz z ustawianiem identyfikatora urządzenia i ustawień uwierzytelniania można **ustawić urządzenie nadrzędne** lub **wybrać opcję urządzenia podrzędne**.
1. Wybierz urządzenie lub urządzenia, które mają być nadrzędne lub podrzędne.

Można również utworzyć relacje nadrzędny/podrzędny dla istniejących urządzeń lub zarządzać nimi.

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz urządzenie, którym chcesz zarządzać, z listy **urządzeń IoT Edge**.
1. Wybierz pozycję **Ustaw urządzenie nadrzędne** lub **Zarządzaj urządzeniami podrzędnymi**.
1. Dodawanie lub usuwanie wszystkich urządzeń nadrzędnych lub podrzędnych.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozszerzenie [Azure-IoT](/cli/azure/ext/azure-iot) dla interfejsu wiersza polecenia platformy Azure udostępnia polecenia służące do zarządzania zasobami IoT. Można zarządzać relacją nadrzędną/podrzędną urządzeń IoT i IoT Edge przy tworzeniu nowych tożsamości urządzeń lub przez edycję istniejących urządzeń.

[AZ IoT Hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) Set of Commands umożliwia zarządzanie relacjami nadrzędny/podrzędny dla danego urządzenia.

`create`Polecenie obejmuje parametry dodawania urządzeń podrzędnych i ustawiania urządzenia nadrzędnego w momencie tworzenia urządzenia.

Dodatkowe polecenia dotyczące tożsamości urządzeń, w tym `add-children` , `list-children` , `remove-children` i `get-parent` i `set-parent` , umożliwiają zarządzanie relacjami nadrzędny/podrzędny dla istniejących urządzeń.

---

## <a name="prepare-certificates"></a>Przygotuj certyfikaty

Aby zapewnić bezpieczną komunikację między sobą, należy zainstalować spójny łańcuch certyfikatów między urządzeniami w tej samej hierarchii bramy. Każde urządzenie w hierarchii, niezależnie od tego, czy urządzenie IoT Edge lub urządzenie liścia IoT, wymaga kopii tego samego certyfikatu głównego urzędu certyfikacji. Każde urządzenie IoT Edge w hierarchii używa tego certyfikatu głównego urzędu certyfikacji jako katalogu głównego dla certyfikatu urzędu certyfikacji urządzenia.

W przypadku tej konfiguracji każde urządzenie podrzędne IoT Edge lub urządzenie typu liść IoT może zweryfikować tożsamość swojego elementu nadrzędnego, sprawdzając, czy edgeHub, z którym się łączą, ma certyfikat serwera podpisany przez udostępniony certyfikat głównego urzędu certyfikacji.

<!-- TODO: certificate graphic -->

Utwórz następujące certyfikaty:

* **Certyfikat głównego urzędu certyfikacji**, który jest najwyżej udostępnionym certyfikatem dla wszystkich urządzeń w danej hierarchii bramy. Ten certyfikat jest instalowany na wszystkich urządzeniach.
* Wszystkie **Certyfikaty pośrednie** , które mają zostać uwzględnione w łańcuchu certyfikatów głównych.
* **Certyfikat urzędu certyfikacji urządzenia** i jego **klucz prywatny** generowane przez certyfikaty główne i pośrednie. Wymagany jest jeden unikatowy certyfikat urzędu certyfikacji dla każdego urządzenia IoT Edge w hierarchii bramy.

>[!NOTE]
>Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038.

Możesz użyć urzędu certyfikacji z podpisem własnym lub kupić jeden z zaufanych komercyjnych urzędów certyfikacji, takich jak Baltimore, VeriSign, DigiCert lub GlobalSign.

Jeśli nie masz własnych certyfikatów do użycia, możesz [utworzyć certyfikaty demonstracyjne w celu przetestowania funkcji urządzenia IoT Edge](how-to-create-test-certificates.md). Wykonaj kroki opisane w tym artykule, aby utworzyć jeden zestaw certyfikatów głównych i pośrednich, a następnie utworzyć IoT Edge certyfikaty urzędu certyfikacji dla każdego z urządzeń.

## <a name="configure-iot-edge-on-devices"></a>Konfigurowanie IoT Edge na urządzeniach

Kroki konfigurowania IoT Edge jako bramy są bardzo podobne do kroków konfigurowania IoT Edge jako urządzenia podrzędnego.

Aby włączyć odnajdywanie bramy, każde urządzenie bramy IoT Edge musi być skonfigurowane przy użyciu **nazwy hosta** , która będzie używana przez urządzenia podrzędne do znajdowania w sieci lokalnej. Każde urządzenie podrzędne IoT Edge musi zostać skonfigurowane za pomocą **parent_hostname** do nawiązania połączenia. Jeśli pojedyncze urządzenie IoT Edge jest urządzeniem nadrzędnym i podrzędnym, wymagane są oba parametry.

Aby włączyć bezpieczne połączenia, każde urządzenie IoT Edge w scenariuszu bramy musi być skonfigurowane przy użyciu unikatowego certyfikatu urzędu certyfikacji urządzenia i kopii certyfikatu głównego urzędu certyfikacji, który jest współużytkowany przez wszystkie urządzenia w hierarchii bramy.

Na urządzeniu powinno być już zainstalowane IoT Edge. W przeciwnym razie wykonaj kroki w celu [zarejestrowania urządzenia IoT Edge w IoT Hub](how-to-register-device.md) a następnie [Zainstaluj środowisko uruchomieniowe Azure IoT Edge](how-to-install-iot-edge.md).

Kroki opisane w tej sekcji odwołują się do **certyfikatu głównego urzędu certyfikacji** i **certyfikatu urzędu certyfikacji urządzenia oraz klucza prywatnego** , który został omówiony wcześniej w tym artykule. Jeśli te certyfikaty zostały utworzone na innym urządzeniu, należy je udostępnić na tym urządzeniu. Pliki można przenieść fizycznie, podobnie jak w przypadku dysku USB, za pomocą usługi, takiej jak [Azure Key Vault](../key-vault/general/overview.md), lub za pomocą funkcji, takiej jak [zabezpieczony plik kopii](https://www.ssh.com/ssh/scp/).

Wykonaj poniższe kroki, aby skonfigurować IoT Edge na urządzeniu.

Upewnij się, że użytkownik **iotedge** ma uprawnienia do odczytu w katalogu zawierającym certyfikaty i klucze w systemie Linux.

1. Zainstaluj **certyfikat głównego urzędu certyfikacji** na tym urządzeniu IoT Edge.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Aktualizowanie magazynu certyfikatów.

   ```bash
   sudo update-ca-certificates
   ```

   To polecenie powinno spowodować, że jeden certyfikat został dodany do/etc/SSL/certs.

1. Otwórz plik konfiguracji demona Security IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję **Certyfikaty** w pliku config. YAML. Zaktualizuj trzy pola certyfikatów, aby wskazywały swoje certyfikaty. Podaj ścieżki URI pliku, które mają format `file:///<path>/<filename>` .

   * **device_ca_cert**: ścieżka identyfikatora URI pliku do certyfikatu urzędu certyfikacji urządzenia, który jest unikatowy dla tego urządzenia.
   * **device_ca_pk**: ścieżka identyfikatora URI pliku do klucza prywatnego urzędu certyfikacji urządzenia unikatowy dla tego urządzenia.
   * **trusted_ca_certs**: ścieżka identyfikatora URI pliku do certyfikatu głównego urzędu certyfikacji, który jest współużytkowany przez wszystkie urządzenia w hierarchii bramy.

1. Znajdź parametr **hostname** w pliku config. YAML. Zaktualizuj nazwę hosta tak, aby była to w pełni kwalifikowana nazwa domeny (FQDN) lub adres IP urządzenia IoT Edge.

   Wartość tego parametru jest używany przez urządzenia podrzędne do łączenia się z tą bramą. Nazwa hosta domyślnie przyjmuje nazwę komputera, ale w celu połączenia z urządzeniami podrzędnymi wymagane są nazwy FQDN lub adresu IP.

   Użyj nazwy hosta krótszej niż 64 znaków, czyli limitu znaków dla nazwy pospolitej certyfikatu serwera.

   Być spójne ze wzorcem nazwy hosta w hierarchii bramy. Użyj nazw FQDN lub adresów IP, ale nie obu.

1. **Jeśli to urządzenie jest urządzeniem podrzędnym**, znajdź parametr **parent_hostname** . Zaktualizuj pole **parent_hostname** tak, aby było to nazwa FQDN lub adres IP urządzenia nadrzędnego, dopasowanie dowolnego elementu jako nazwy hosta w pliku config. YAML.

1. Chociaż ta funkcja jest dostępna w publicznej wersji zapoznawczej, należy skonfigurować urządzenie IoT Edge w taki sposób, aby korzystało z publicznej wersji zapoznawczej agenta IoT Edge podczas jego uruchamiania.

   Znajdź sekcję YAML **agenta** i zaktualizuj wartość obrazu do obrazu publicznej wersji zapoznawczej:

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Save ( `Ctrl+O` ) i Close ( `Ctrl+X` ) plik config. YAML.

1. Jeśli wcześniej korzystano z innych certyfikatów dla IoT Edge, Usuń pliki z następujących dwóch katalogów, aby upewnić się, że zostały zastosowane nowe certyfikaty:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Uruchom ponownie usługę IoT Edge, aby zastosować zmiany.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Sprawdź, czy w konfiguracji nie występują błędy.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Narzędzie sprawdzania IoT Edge używa kontenera do przeprowadzenia niektórych testów diagnostycznych. Jeśli chcesz używać tego narzędzia na urządzeniach podrzędnych IoT Edge, upewnij się, że są one dostępne `mcr.microsoft.com/azureiotedge-diagnostics:latest` , lub Zablokuj obraz kontenera w rejestrze kontenera prywatnego.

## <a name="configure-runtime-modules-for-public-preview"></a>Konfigurowanie modułów czasu wykonywania dla publicznej wersji zapoznawczej

Chociaż ta funkcja jest dostępna w publicznej wersji zapoznawczej, należy skonfigurować urządzenie IoT Edge, aby korzystało z publicznej wersji zapoznawczej modułów IoT Edge środowiska uruchomieniowego. W poprzedniej sekcji przedstawiono procedurę konfigurowania edgeAgent podczas uruchamiania. Należy również skonfigurować moduły środowiska uruchomieniowego w ramach wdrożeń dla urządzenia.

1. Skonfiguruj moduł edgeHub do korzystania z obrazu publicznej wersji zapoznawczej: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

1. Skonfiguruj następujące zmienne środowiskowe dla modułu edgeHub:

   | Nazwa | Wartość |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Skonfiguruj moduł edgeAgent do korzystania z obrazu publicznej wersji zapoznawczej: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

## <a name="network-isolate-downstream-devices"></a>Izolowanie sieci na urządzeniach podrzędnych

Kroki opisane w tym artykule dotyczą konfigurowania IoT Edge urządzeń jako bramy lub urządzenia podrzędnego oraz tworzenia zaufanego połączenia między nimi. Urządzenie bramy obsługuje interakcje między urządzeniem podrzędnym a IoT Hub, w tym uwierzytelnianie i Routing komunikatów. Moduły wdrożone na urządzeniach podrzędnych IoT Edge mogą nadal tworzyć własne połączenia z usługami w chmurze.

Niektóre architektury sieci, takie jak te, które są zgodne ze standardem ISA-95, umożliwiają zminimalizowanie liczby połączeń internetowych. W tych scenariuszach można skonfigurować podrzędne urządzenia IoT Edge bez bezpośredniej łączności z Internetem. Poza routingiem IoT Hub komunikacji za pośrednictwem urządzenia bramy urządzenia podrzędne IoT Edge mogą polegać na urządzeniu bramy dla wszystkich połączeń w chmurze.

Ta konfiguracja sieci wymaga, aby tylko urządzenie IoT Edge w najwyższej warstwie hierarchii bramy miało bezpośrednie połączenia z chmurą. Urządzenia IoT Edge w niższych warstwach mogą komunikować się tylko z urządzeniami nadrzędnymi lub urządzeniami podrzędnymi. Moduły specjalne na urządzeniach bramy umożliwiają ten scenariusz, w tym:

* **Moduł proxy interfejsu API** jest wymagany w przypadku bramy IoT Edge, która ma inne urządzenie IoT Edge poniżej. Oznacza to, że musi znajdować się na *każdej warstwie* hierarchii bramy, z wyjątkiem warstwy dolnej. Ten moduł używa zwrotnego serwera proxy [Nginx](https://nginx.org) do kierowania danych http za pomocą warstwy sieci przez pojedynczy port. Jest wysoce konfigurowalny za poorednictwem sznurka modułu i zmiennych środowiskowych, dlatego można je dopasować do wymagań scenariusza bramy.

* **Moduł rejestru platformy Docker** można wdrożyć na bramie IoT Edge w *górnej warstwie* hierarchii bramy. Ten moduł jest odpowiedzialny za pobieranie i buforowanie obrazów kontenerów w imieniu wszystkich IoT Edge urządzeń w niższych warstwach. Alternatywą dla wdrożenia tego modułu na najwyższej warstwie jest użycie rejestru lokalnego lub ręczne załadowanie obrazów kontenera na urządzenia i ustawienie zasad ściągania modułu na wartość **nigdy**.

* **BLOB Storage platformy Azure na IoT Edge** można wdrożyć na bramie IoT Edge w *górnej warstwie* hierarchii bramy. Ten moduł jest odpowiedzialny za przekazywanie obiektów BLOB w imieniu wszystkich IoT Edge urządzeń w niższych warstwach. Możliwość przekazywania obiektów BLOB umożliwia również korzystanie z użytecznych funkcji rozwiązywania problemów w przypadku urządzeń IoT Edge w niższych warstwach, takich jak przekazywanie dzienników modułów i przekazywanie pakietów obsługi.

### <a name="network-configuration"></a>Konfiguracja sieci

Dla każdego urządzenia bramy w górnej warstwie operatory sieci muszą:

* Podaj statyczny adres IP lub w pełni kwalifikowaną nazwę domeny (FQDN).
* Autoryzuj komunikację wychodzącą z tego adresu IP do nazwy hosta IoT Hub platformy Azure za pośrednictwem portów 443 (HTTPS) i 5671 (AMQP).
* Autoryzuj komunikację wychodzącą z tego adresu IP do nazwy hosta Azure Container Registry za pośrednictwem portu 443 (HTTPS).

  Moduł proxy interfejsu API może obsługiwać tylko połączenia z jednym rejestrem kontenerów naraz. Zaleca się, aby wszystkie obrazy kontenerów, łącznie z obrazami publicznymi udostępnionymi przez firmę Microsoft Container Registry (mcr.microsoft.com), przechowywane w prywatnym rejestrze kontenerów.

Dla każdego urządzenia bramy w niższej warstwie operatory sieci muszą:

* Podaj statyczny adres IP.
* Autoryzuj komunikację wychodzącą z tego adresu IP na adres IP bramy nadrzędnej za pośrednictwem portów 443 (HTTPS) i 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Wdrażanie modułów na urządzeniach najwyższej warstwy

Urządzenie IoT Edge w najwyższej warstwie hierarchii bramy ma zestaw wymaganych modułów, które muszą zostać wdrożone, oprócz modułów obciążenia, które mogą być uruchamiane na urządzeniu.

Moduł proxy interfejsu API został zaprojektowany tak, aby był dostosowany do obsługi większości typowych scenariuszy bramy. W tym artykule przedstawiono i przykład konfigurowania modułów w konfiguracji podstawowej. Zapoznaj się z tematem [Konfigurowanie modułu proxy interfejsu API dla scenariusza hierarchii bramy](how-to-configure-api-proxy-module.md) , aby uzyskać bardziej szczegółowe informacje i przykłady.

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz urządzenie warstwy najwyższej konfiguracji, które konfigurujesz z listy **urządzeń IoT Edge**.
1. Wybierz pozycję **Ustaw moduły**.
1. W sekcji **IoT Edge modułów** wybierz pozycję **Dodaj** , a następnie wybierz pozycję **moduł witryny Marketplace**.
1. Wyszukaj i wybierz moduł **proxy interfejsu API IoT Edge** .
1. Wybierz nazwę modułu proxy interfejsu API z listy wdrożonych modułów i zaktualizuj następujące ustawienia modułu:
   1. Na karcie **zmienne środowiskowe** zaktualizuj wartość **NGINX_DEFAULT_PORT** do `443` .
   1. Na karcie **Opcje tworzenia kontenera** zaktualizuj powiązania portów do portu referencyjnego 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Te zmiany umożliwiają skonfigurowanie modułu proxy interfejsu API do nasłuchiwania na porcie 443. Aby zapobiec kolizjom powiązań portów, należy skonfigurować moduł edgeHub w taki sposób, aby nie nasłuchiwać na porcie 443. Zamiast tego moduł proxy interfejsu API będzie kierować dowolny ruch edgeHub na porcie 443.

1. Wybierz pozycję **Ustawienia środowiska uruchomieniowego** i Znajdź opcje tworzenia modułu edgeHub. Usuń powiązanie portów dla portu 443, pozostawiając powiązania dla portów 5671 i 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany w ustawieniach środowiska uruchomieniowego.
1. Wybierz pozycję **Dodaj** ponownie, a następnie wybierz **moduł IoT Edge**.
1. Podaj następujące wartości, aby dodać moduł Docker Registry do wdrożenia:
   1. **Nazwa modułu IoT Edge**: `registry`
   1. Na karcie **Ustawienia modułu** **Identyfikator URI obrazu**: `registry:latest`
   1. Na karcie **zmienne środowiskowe** Dodaj następujące zmienne środowiskowe:

      * **Nazwa**: `REGISTRY_PROXY_REMOTEURL` **wartość**: adres URL rejestru kontenerów, na który ma być mapowany ten moduł rejestru. Na przykład `https://myregistry.azurecr`.

        Moduł rejestru może mapować tylko jeden rejestr kontenerów, dlatego zaleca się, aby wszystkie obrazy kontenerów były w jednym rejestrze prywatnym kontenerów.

      * **Name**: `REGISTRY_PROXY_USERNAME` **Value**: username do uwierzytelniania w rejestrze kontenerów.

      * **Name**: `REGISTRY_PROXY_PASSWORD` **Value**: password do uwierzytelniania w rejestrze kontenerów.

   1. Na karcie **Opcje tworzenia kontenera** wklej:

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

1. Wybierz pozycję **Dodaj** , aby dodać moduł do wdrożenia.
1. Wybierz pozycję **Dalej: trasy** , aby przejść do następnego kroku.
1. Aby włączyć obsługę komunikatów z urządzenia do chmury z urządzeń podrzędnych w celu osiągnięcia IoT Hub, należy uwzględnić trasę przekazującą wszystkie komunikaty do IoT Hub. Przykład:
    1. **Nazwa**: `Route`
    1. **Wartość**: `FROM /messages/* INTO $upstream`
1. Wybierz pozycję **Recenzja + Utwórz** , aby przejść do ostatniego kroku.
1. Wybierz pozycję **Utwórz** , aby wdrożyć na urządzeniu.

### <a name="deploy-modules-to-lower-layer-devices"></a>Wdrażanie modułów na urządzeniach warstwy niższej

Urządzenia IoT Edge w niższych warstwach hierarchii bramy mają jeden wymagany moduł, który musi zostać wdrożony, oprócz modułów obciążenia, które mogą być uruchamiane na urządzeniu.

#### <a name="route-container-image-pulls"></a>Ściąganie obrazów kontenera tras

Przed przeprowadzeniem omówienia wymaganego modułu proxy dla urządzeń IoT Edge w hierarchiach bram należy zrozumieć, jak IoT Edge urządzenia w niższych warstwach pobierają obrazy modułu.

Jeśli urządzenia warstwy niższej nie mogą połączyć się z chmurą, ale chcesz, aby wypychali obrazy modułów w zwykły sposób, a następnie należy skonfigurować urządzenie najwyższej warstwy w hierarchii bramy, aby obsługiwało te żądania. Na urządzeniu warstwy najwyższej wymagane jest uruchomienie modułu **rejestru** platformy Docker, który jest mapowany na rejestr kontenerów. Następnie skonfiguruj moduł proxy interfejsu API, aby kierować do niego żądania kontenera. Te szczegóły zostały omówione w poprzednich sekcjach tego artykułu. W tej konfiguracji urządzenia warstwy niższej nie powinny wskazywać rejestrów kontenerów w chmurze, ale do rejestru działającego w górnej warstwie.

Na przykład zamiast wywoływania `mcr.microsoft.com/azureiotedge-api-proxy:latest` urządzenia niższej warstwy powinny być wywoływane `$upstream:443/azureiotedge-api-proxy:latest` .

Parametr **$upstream** wskazuje element nadrzędny urządzenia warstwy niższej, więc żądanie będzie kierowane przez wszystkie warstwy, dopóki nie osiągnie warstwy najwyższej, która ma żądania kontenera routingu środowiska proxy do modułu rejestru. `:443`Port w tym przykładzie powinien zostać zastąpiony przez port, na którym nasłuchuje moduł proxy interfejsu API na urządzeniu nadrzędnym.

Moduł proxy interfejsu API może być kierowany tylko do jednego modułu rejestru, a każdy moduł rejestru może mapować tylko jeden rejestr kontenerów. W związku z tym wszystkie obrazy, które muszą zostać wyściągane, muszą być przechowywane w jednym rejestrze kontenera.

Jeśli nie chcesz, aby urządzenia warstwy niższe nie przepełniły żądań ściągnięcia modułu za pomocą hierarchii bramy, inna opcja polega na zarządzaniu lokalnym rozwiązaniem rejestru. Możesz też wypchnąć obrazy modułów na urządzenia przed utworzeniem wdrożeń, a następnie ustawić **imagePullPolicy** na **nigdy**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Inicjowanie agenta IoT Edge

Agent IoT Edge to pierwszy składnik środowiska uruchomieniowego do uruchomienia na dowolnym IoT Edge urządzeniu. Należy upewnić się, że wszystkie urządzenia podrzędne IoT Edge mogą uzyskać dostęp do obrazu modułu edgeAgent podczas uruchamiania, a następnie mogą uzyskać dostęp do wdrożeń i rozpocząć pozostałe obrazy modułu.

Po przejściu do pliku config. YAML na urządzeniu IoT Edge, aby podać informacje o uwierzytelnianiu, certyfikatach i nadrzędnej nazwie hosta, należy również zaktualizować obraz kontenera edgeAgent.

Jeśli urządzenie bramy najwyższego poziomu jest skonfigurowane do obsługi żądań obrazu kontenera, Zastąp `mcr.microsoft.com` wartość nadrzędną nazwą hosta i portem nasłuchu interfejsu API serwera proxy. W manifeście wdrożenia można użyć `$upstream` jako skrótu, ale wymaga modułu edgeHub do obsługi routingu i tego modułu nie uruchomiono w tym momencie. Przykład:

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc2"
    auth: {}
```

Jeśli używasz lokalnego rejestru kontenerów lub ręcznie udostępniasz obrazy kontenerów na urządzeniu, zaktualizuj plik config. YAML odpowiednio.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Skonfiguruj środowisko uruchomieniowe i Wdróż moduł proxy

**Moduł proxy interfejsu API** jest wymagany do routingu całej komunikacji między chmurą i wszystkimi podrzędnymi IoT Edge urządzeniami. Ten moduł nie wymaga, aby urządzenie IoT Edge w dolnej warstwie hierarchii bez podrzędnych urządzeń IoT Edge.

Moduł proxy interfejsu API został zaprojektowany tak, aby był dostosowany do obsługi większości typowych scenariuszy bramy. W tym artykule krótko opisano procedurę konfigurowania modułów w konfiguracji podstawowej. Zapoznaj się z tematem [Konfigurowanie modułu proxy interfejsu API dla scenariusza hierarchii bramy](how-to-configure-api-proxy-module.md) , aby uzyskać bardziej szczegółowe informacje i przykłady.

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz urządzenie warstwy niższej konfiguracji, które konfigurujesz z listy **urządzeń IoT Edge**.
1. Wybierz pozycję **Ustaw moduły**.
1. W sekcji **IoT Edge modułów** wybierz pozycję **Dodaj** , a następnie wybierz pozycję **moduł witryny Marketplace**.
1. Wyszukaj i wybierz moduł **proxy interfejsu API IoT Edge** .
1. Wybierz nazwę modułu proxy interfejsu API z listy wdrożonych modułów i zaktualizuj następujące ustawienia modułu:
   1. Na karcie **Ustawienia modułu** zaktualizuj wartość **identyfikatora URI obrazu**. Zastąp element `mcr.microsoft.com` pytaniem `$upstream:443`.
   1. Na karcie **zmienne środowiskowe** zaktualizuj wartość **NGINX_DEFAULT_PORT** do `443` .
   1. Na karcie **Opcje tworzenia kontenera** zaktualizuj powiązania portów do portu referencyjnego 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Te zmiany umożliwiają skonfigurowanie modułu proxy interfejsu API do nasłuchiwania na porcie 443. Aby zapobiec kolizjom powiązań portów, należy skonfigurować moduł edgeHub w taki sposób, aby nie nasłuchiwać na porcie 443. Zamiast tego moduł proxy interfejsu API będzie kierować dowolny ruch edgeHub na porcie 443.

1. Wybierz pozycję **Ustawienia środowiska uruchomieniowego**.
1. Zaktualizuj ustawienia modułu edgeHub:

   1. W polu **obraz** Zastąp ciąg `mcr.microsoft.com` opcją `$upstream:443` .
   1. W polu **Utwórz opcje** Usuń powiązanie portu dla portu 443, pozostawiając powiązania dla portów 5671 i 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Zaktualizuj ustawienia modułu edgeAgent:
   1. W polu **obraz** Zastąp ciąg `mcr.microsoft.com` opcją `$upstream:443` .

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany w ustawieniach środowiska uruchomieniowego.
1. Wybierz pozycję **Dalej: trasy** , aby przejść do następnego kroku.
1. Aby włączyć obsługę komunikatów z urządzenia do chmury z urządzeń podrzędnych w celu uzyskania dostępu IoT Hub, Uwzględnij trasę przekazującą wszystkie komunikaty do programu `$upstream` . Parametr nadrzędny wskazuje urządzenie nadrzędne w przypadku niższych warstw urządzeń. Przykład:
    1. **Nazwa**: `Route`
    1. **Wartość**: `FROM /messages/* INTO $upstream`
1. Wybierz pozycję **Recenzja + Utwórz** , aby przejść do ostatniego kroku.
1. Wybierz pozycję **Utwórz** , aby wdrożyć na urządzeniu.

## <a name="next-steps"></a>Następne kroki

[Jak używać urządzenia usługi IoT Edge jako bramy](iot-edge-as-gateway.md)

[Konfigurowanie modułu proxy interfejsu API dla scenariusza hierarchii bramy](how-to-configure-api-proxy-module.md)