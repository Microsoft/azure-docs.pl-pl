---
title: Łączenie urządzeń IoT Edge — Azure IoT Edge | Microsoft Docs
description: Jak skonfigurować urządzenie IoT Edge nawiązać połączenie z Azure IoT Edge bramą.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 500833d1bb4fc492942c08239bd488c2d2c16d30
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484323"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>Łączenie urządzenia podrzędnego IoT Edge z bramą Azure IoT Edge wirtualnej

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Ten artykuł zawiera instrukcje dotyczące nawiązywania zaufanego połączenia między bramą IoT Edge a urządzeniem IoT Edge podrzędnego.

W scenariuszu bramy urządzeniem IoT Edge może być zarówno brama, jak i urządzenie podrzędne. Wiele IoT Edge można nakładać na warstwy, aby utworzyć hierarchię urządzeń. Urządzenia podrzędne mogą uwierzytelniać i wysyłać lub odbierać komunikaty za pośrednictwem urządzenia bramy (lub nadrzędnego).

Istnieją dwie różne konfiguracje dla IoT Edge w hierarchii bramy i w tym artykule o adresach obu. Pierwsza z nich to **warstwa górna IoT Edge** urządzenia. Jeśli wiele IoT Edge łączy się za pośrednictwem siebie, każde urządzenie, które nie ma urządzenia nadrzędnego, ale łączy się bezpośrednio z IoT Hub, jest uznawane za w górnej warstwie. To urządzenie jest odpowiedzialne za obsługę żądań ze wszystkich urządzeń poniżej. Druga konfiguracja ma zastosowanie do IoT Edge w niższej **warstwie** hierarchii. Te urządzenia mogą być bramą dla innych urządzeń podrzędnych IoT i IoT Edge, ale muszą również rozsyłać dowolną komunikację za pośrednictwem własnych urządzeń nadrzędnych.

Niektóre architektury sieci wymagają, aby tylko najważniejsze IoT Edge w hierarchii łączyły się z chmurą. W tej konfiguracji wszystkie IoT Edge w niższych warstwach hierarchii mogą komunikować się tylko z urządzeniami bramy (lub nadrzędnymi) i dowolnymi urządzeniami podrzędnym (lub podrzędnym).

Wszystkie kroki opisane w tym artykule są opisane w artykule Configure [an IoT Edge device to act](how-to-create-transparent-gateway.md)as a transparent gateway (Konfigurowanie urządzenia usługi IoT Edge do działania jako przezroczysta brama), która konfiguruje urządzenie usługi IoT Edge jako bramę dla urządzeń podrzędnego IoT. Te same podstawowe kroki mają zastosowanie do wszystkich scenariuszy bramy:

* **Uwierzytelnianie:** IoT Hub tożsamości dla wszystkich urządzeń w hierarchii bramy.
* **Autoryzacja:** skonfiguruj relację nadrzędny/podrzędny w IoT Hub, aby autoryzować urządzenia podrzędne do nawiązywania połączenia z urządzeniem nadrzędnym w taki sposób, w IoT Hub.
* **Odnajdywanie bramy:** upewnij się, że urządzenie podrzędne może znaleźć swoje urządzenie nadrzędne w sieci lokalnej.
* **Bezpieczne połączenie:** ustanów bezpieczne połączenie z zaufanymi certyfikatami, które są częścią tego samego łańcucha.

## <a name="prerequisites"></a>Wymagania wstępne

* Bezpłatne lub standardowe centrum IoT.
* Co najmniej **dwa IoT Edge ,** jedno urządzenie warstwy najwyższej i jedno lub więcej urządzeń warstwy niższej. Jeśli nie masz dostępnych IoT Edge, możesz uruchomić Azure IoT Edge [na maszynach wirtualnych z systemem Ubuntu.](how-to-install-iot-edge-ubuntuvm.md)
* Jeśli używasz interfejsu wiersza polecenia platformy Azure do tworzenia urządzeń i zarządzania nimi, masz zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.3.1 z rozszerzeniem usługi Azure IoT w wersji 0.10.6 lub wyższej.

Ten artykuł zawiera szczegółowe instrukcje i opcje, które ułatwiają tworzenie hierarchii bramy właściwej dla scenariusza. Aby uzyskać samouczek z przewodnikiem, zobacz Create a hierarchy of IoT Edge devices using gateways (Tworzenie [hierarchii urządzeń IoT Edge przy użyciu bram).](tutorial-nested-iot-edge.md)

## <a name="create-a-gateway-hierarchy"></a>Tworzenie hierarchii bramy

Hierarchię bramy IoT Edge przez zdefiniowanie relacji nadrzędny/podrzędny dla IoT Edge w scenariuszu. Urządzenie nadrzędne można ustawić podczas tworzenia nowej tożsamości urządzenia lub zarządzać elementami nadrzędnymi i podrzędnym istniejącej tożsamości urządzenia.

Krok konfigurowania relacji nadrzędny/podrzędny autoryzuje urządzenia podrzędne do nawiązywania połączenia z urządzeniem nadrzędnym w taki sposób, w IoT Hub.

Tylko IoT Edge mogą być urządzeniami nadrzędnymi, ale zarówno urządzenia IoT Edge, jak i urządzenia IoT mogą być urządzeniami podrzędnym. Element nadrzędny może mieć wiele dzieci, ale element podrzędny może mieć tylko jeden element nadrzędny. Hierarchia bramy jest tworzona przez łańcuch zestawów nadrzędny/podrzędny tak, aby element podrzędny jednego urządzenia był elementem nadrzędnym innego.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Portal można zarządzać relacją nadrzędny/podrzędny podczas tworzenia nowych tożsamości urządzeń lub edytowania istniejących urządzeń.

Podczas tworzenia nowego urządzenia IoT Edge masz możliwość wybrania urządzeń nadrzędnych i podrzędnych z listy istniejących urządzeń IoT Edge w tym centrum.

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz **pozycję Dodaj IoT Edge urządzenie.**
1. Oprócz ustawienia identyfikatora urządzenia i ustawień uwierzytelniania można ustawić urządzenie **nadrzędne** lub wybrać **urządzenia podrzędne.**
1. Wybierz urządzenie lub urządzenia, które mają być nadrzędne lub podrzędne.

Możesz również tworzyć relacje nadrzędny/podrzędny dla istniejących urządzeń lub zarządzać nimi.

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz urządzenie, którym chcesz zarządzać, z listy IoT Edge **urządzeń.**
1. Wybierz **pozycję Ustaw urządzenie nadrzędne lub** Zarządzaj urządzeniami **podrzędnym.**
1. Dodaj lub usuń wszystkie urządzenia nadrzędne lub podrzędne.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Rozszerzenie [azure-iot](/cli/azure/iot) dla interfejsu wiersza polecenia platformy Azure udostępnia polecenia do zarządzania zasobami IoT. Relacją nadrzędny/podrzędny urządzeń IoT i urządzeń IoT Edge można zarządzać podczas tworzenia nowych tożsamości urządzeń lub edytowania istniejących urządzeń.

Zestaw [poleceń az iot hub device-identity](/cli/azure/iot/hub/device-identity) umożliwia zarządzanie relacjami nadrzędny/podrzędny dla danego urządzenia.

Polecenie zawiera parametry służące do dodawania urządzeń children i `create` ustawiania urządzenia nadrzędnego w czasie tworzenia urządzenia.

Dodatkowe polecenia tożsamości urządzeń, w tym , i i , umożliwiają zarządzanie relacjami `add-children` `list-children` `remove-children` `get-parent` `set-parent` nadrzędny/podrzędny dla istniejących urządzeń.

---

## <a name="prepare-certificates"></a>Przygotowywanie certyfikatów

Aby ustanowić bezpieczną komunikację między sobą, należy zainstalować spójny łańcuch certyfikatów na urządzeniach w tej samej hierarchii bramy. Każde urządzenie w hierarchii, niezależnie od IoT Edge lub urządzenia liściA IoT, wymaga kopii tego samego certyfikatu głównego urzędu certyfikacji. Każdy IoT Edge w hierarchii używa tego certyfikatu głównego urzędu certyfikacji jako głównego certyfikatu urzędu certyfikacji urządzenia.

Dzięki tej konfiguracji każde urządzenie podrzędne usługi IoT Edge lub urządzenie liścia IoT może zweryfikować tożsamość swojego elementu nadrzędnego, weryfikując, czy element edgeHub, z którym nawiązano połączenie, ma certyfikat serwera podpisany przez certyfikat udostępnionego głównego urzędu certyfikacji.

<!-- TODO: certificate graphic -->

Utwórz następujące certyfikaty:

* Certyfikat **głównego urzędu certyfikacji**, który jest najwyższym certyfikatem udostępnionym dla wszystkich urządzeń w danej hierarchii bramy. Ten certyfikat jest instalowany na wszystkich urządzeniach.
* Wszystkie **certyfikaty pośrednie,** które mają zostać dołączyć do łańcucha certyfikatów głównych.
* Certyfikat **urzędu certyfikacji urządzenia i** jego klucz **prywatny** wygenerowany przez certyfikaty główne i pośrednie. Potrzebujesz jednego unikatowego certyfikatu urzędu certyfikacji dla każdego IoT Edge urządzenia w hierarchii bramy.

Możesz użyć urzędu certyfikacji z podpisem własnym lub kupić go od zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, Verisign, Digicert lub GlobalSign.

Jeśli nie masz własnych certyfikatów do użycia, możesz utworzyć certyfikaty demonstracyjne, aby przetestować IoT Edge [urządzenia.](how-to-create-test-certificates.md) Wykonaj kroki opisane w tym artykule, aby utworzyć jeden zestaw certyfikatów głównych i pośrednich, a następnie utworzyć IoT Edge urzędu certyfikacji urządzenia dla każdego z urządzeń.

## <a name="configure-iot-edge-on-devices"></a>Konfigurowanie IoT Edge na urządzeniach

Kroki konfigurowania usługi IoT Edge jako bramy są bardzo podobne do kroków konfigurowania IoT Edge jako urządzenia podrzędnego.

Aby włączyć odnajdywanie bramy, IoT Edge urządzenie bramy musi być skonfigurowane przy użyciu nazwy **hosta,** która będzie przez nie używania do znalezienia go w sieci lokalnej. Każde urządzenie podrzędne IoT Edge musi być skonfigurowane przy użyciu **parent_hostname** nawiązać połączenie. Jeśli pojedyncze urządzenie IoT Edge jest urządzeniem nadrzędnym i podrzędnym, wymagane są oba parametry.

Aby włączyć bezpieczne połączenia, każde urządzenie IoT Edge w scenariuszu bramy musi być skonfigurowane z unikatowym certyfikatem urzędu certyfikacji urządzenia i kopią certyfikatu głównego urzędu certyfikacji współużytkowana przez wszystkie urządzenia w hierarchii bramy.

Na urządzeniu IoT Edge już zainstalowane aplikacje. Jeśli nie, postępuj zgodnie z instrukcjami, aby [zarejestrować urządzenie IoT Edge w programie IoT Hub](how-to-register-device.md) a następnie zainstalować Azure IoT Edge [uruchomieniowe.](how-to-install-iot-edge.md)

Kroki opisane w tej sekcji odwołują  się do certyfikatu głównego **urzędu certyfikacji** i certyfikatu urzędu certyfikacji urządzenia oraz klucza prywatnego, które zostały omówione wcześniej w tym artykule. Jeśli te certyfikaty zostały utworzone na innym urządzeniu, muszą być dostępne na tym urządzeniu. Pliki można przesyłać fizycznie, na przykład za pomocą dysku USB, za pomocą usługi, Azure Key Vault [,](../key-vault/general/overview.md)lub funkcji, np. bezpiecznego [kopiowania plików.](https://www.ssh.com/ssh/scp/)

Aby skonfigurować IoT Edge na urządzeniu, należy wykonać następujące czynności.

Upewnij się, że użytkownik **iotedge ma** uprawnienia do odczytu dla katalogu, w którym są w posiadaniu certyfikaty i klucze.

1. Zainstaluj certyfikat **głównego urzędu certyfikacji na** tym IoT Edge urządzeniu.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. Zaktualizuj magazyn certyfikatów.

   ```bash
   sudo update-ca-certificates
   ```

   To polecenie powinno zwrócić, że jeden certyfikat został dodany do /etc/ssl/certs.

1. Otwórz plik konfiguracji usługi IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Jeśli plik konfiguracji nie istnieje jeszcze na urządzeniu, użyj go jako szablonu, `/etc/aziot/config.toml.edge.template` aby go utworzyć.

1. Znajdź **sekcję Nazwa hosta** w pliku konfiguracji. Odkomentuj wiersz zawierający parametr i zaktualizuj wartość na w pełni kwalifikowaną nazwę domeny (FQDN) lub adres IP IoT Edge `hostname` urządzenia.

   Wartość tego parametru jest tym, czego urządzenia podrzędne będą używać do łączenia się z tą bramą. Nazwa hosta domyślnie przyjmuje nazwę maszyny, ale nazwa FQDN lub adres IP jest wymagana do połączenia urządzeń dalszych.

   Użyj nazwy hosta krótszej niż 64 znaki, która jest limitem znaków dla nazwy pospolitej certyfikatu serwera.

   Należy zapewnić spójność ze wzorcem nazwy hosta w hierarchii bramy. Użyj zarówno sieci FQDN, jak i adresów IP, ale nie obu tych adresów.

1. *Jeśli to urządzenie jest urządzeniem podrzędnym,* znajdź **sekcję Nadrzędna nazwa hosta.** Odłącz i zaktualizuj parametr tak, aby był nazwą FQDN lub adresem IP urządzenia nadrzędnego, pasujący do nazwy hosta podanej w pliku konfiguracji `parent_hostname` urządzenia nadrzędnego.

1. Znajdź **sekcję Trust bundle cert (Certyfikat pakietu** zaufania). Odłącz i zaktualizuj parametr przy użyciu pliku URI do certyfikatu głównego urzędu `trust_bundle_cert` certyfikacji na urządzeniu.

1. Sprawdź, IoT Edge urządzenie będzie używać poprawnej wersji agenta IoT Edge podczas uruchamiania.

   Znajdź **sekcję Default Edge Agent (Domyślny agent przeglądarki Edge)** i sprawdź, czy wartość obrazu jest IoT Edge 1.2. Jeśli nie, zaktualizuj go:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. Znajdź **sekcję Certyfikat urzędu certyfikacji usługi Edge** w pliku konfiguracji. Odkomentuj wiersze w tej sekcji i podaj ścieżki URI pliku dla plików certyfikatów i kluczy na IoT Edge urządzeniu.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Zapisz ( `Ctrl+O` ) i zamknij plik konfiguracji ( `Ctrl+X` ).

1. Jeśli wcześniej używaliśmy innych certyfikatów dla programu IoT Edge, usuń pliki z następujących dwóch katalogów, aby upewnić się, że nowe certyfikaty zostaną zastosowane:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Zastosuj zmiany.

   ```bash
   sudo iotedge config apply
   ```

1. Sprawdź, czy nie występują błędy w konfiguracji.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Narzędzie IoT Edge sprawdzania używa kontenera do wykonania niektórych czynności diagnostycznych. Jeśli chcesz użyć tego narzędzia na urządzeniach IoT Edge, upewnij się, że mają one dostęp do lub mają obraz kontenera `mcr.microsoft.com/azureiotedge-diagnostics:latest` w prywatnym rejestrze kontenerów.

## <a name="network-isolate-downstream-devices"></a>Izolowanie sieci przez urządzenia podrzędne

Kroki opisane do tej pory w tym artykule IoT Edge jako bramę lub urządzenie podrzędne i utworzenie zaufanego połączenia między nimi. Urządzenie bramy obsługuje interakcje między urządzeniem nadrzędnym a IoT Hub, w tym uwierzytelnianie i routing komunikatów. Moduły wdrożone na urządzeniach IoT Edge mogą nadal tworzyć własne połączenia z usługami w chmurze.

Niektóre architektury sieci, takie jak te, które są zgodne ze standardem ISA-95, mają na celu zminimalizowanie liczby połączeń internetowych. W tych scenariuszach można skonfigurować urządzenia podrzędne IoT Edge bez bezpośredniej łączności z Internetem. Poza routingiem IoT Hub za pośrednictwem urządzenia bramy urządzenia podrzędne IoT Edge mogą polegać na urządzeniu bramy dla wszystkich połączeń w chmurze.

Ta konfiguracja sieci wymaga, IoT Edge urządzenie sieciowe w górnej warstwie hierarchii bramy ma bezpośrednie połączenia z chmurą. IoT Edge w niższych warstwach mogą komunikować się tylko z urządzeniami nadrzędnymi lub urządzeniami podrzędnym. Moduły specjalne na urządzeniach bramy umożliwiają ten scenariusz, w tym:

* Moduł **serwera proxy interfejsu API** jest wymagany na dowolnej bramie IoT Edge, która ma IoT Edge pod nim inne urządzenie. Oznacza to, że musi ona znajdować się w *każdej warstwie* hierarchii bramy z wyjątkiem warstwy dolnej. W tym module jest używany zwrotny serwer proxy [nginx](https://nginx.org) do przekierowywania danych HTTP przez warstwy sieciowe za pośrednictwem jednego portu. Jest wysoce konfigurowalne za pomocą bliźniaczej reprezentacji modułu i zmiennych środowiskowych, więc można ją dostosować do wymagań scenariusza bramy.

* Moduł **rejestru platformy Docker można** wdrożyć w bramie IoT Edge w *górnej warstwie* hierarchii bramy. Ten moduł jest odpowiedzialny za pobieranie i buforowanie obrazów kontenerów w imieniu wszystkich IoT Edge w niższych warstwach. Alternatywą dla wdrożenia tego modułu w górnej warstwie jest użycie rejestru lokalnego lub ręczne załadowanie obrazów kontenerów na urządzeniach i ustawienie zasad ściągania modułu na **nigdy.**

* Usługę **Azure Blob Storage na IoT Edge** można wdrożyć na bramie IoT Edge w górnej *warstwie* hierarchii bramy. Ten moduł jest odpowiedzialny za przekazywanie obiektów blob w imieniu wszystkich urządzeń IoT Edge w niższych warstwach. Możliwość przekazywania obiektów blob umożliwia również korzystanie z przydatnych funkcji rozwiązywania problemów IoT Edge w niższych warstwach, takich jak przekazywanie dzienników modułów i obsługa przekazywania pakietów.

### <a name="network-configuration"></a>Konfiguracja sieci

Dla każdego urządzenia bramy w górnej warstwie operatorzy sieci muszą:

* Podaj statyczny adres IP lub w pełni kwalifikowaną nazwę domeny (FQDN).
* Autoryzuj komunikację wychodzącą z tego adresu IP do Azure IoT Hub hosta za pośrednictwem portów 443 (HTTPS) i 5671 (AMQP).
* Autoryzuj komunikację wychodzącą z tego adresu IP do Azure Container Registry hosta za pośrednictwem portu 443 (HTTPS).

  Moduł serwera proxy interfejsu API może obsługiwać tylko połączenia z jednym rejestrem kontenerów jednocześnie. Zalecamy, aby wszystkie obrazy kontenerów, w tym obrazy publiczne udostępniane przez usługę Microsoft Container Registry (mcr.microsoft.com), przechowywane w prywatnym rejestrze kontenerów.

Dla każdego urządzenia bramy w niższej warstwie operatorzy sieci muszą:

* Podaj statyczny adres IP.
* Autoryzowanie komunikacji wychodzącej z tego adresu IP do adresu IP bramy nadrzędnej za pośrednictwem portów 443 (HTTPS) i 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Wdrażanie modułów na urządzeniach najwyższej warstwy

Urządzenie IoT Edge w górnej warstwie hierarchii bramy ma zestaw wymaganych modułów, które należy wdrożyć na nim, oprócz wszystkich modułów obciążenia, które można uruchomić na urządzeniu.

Moduł serwera proxy interfejsu API został zaprojektowany tak, aby był dostosowany do obsługi najbardziej typowych scenariuszy bramy. Ten artykuł zawiera przykład konfigurowania modułów w konfiguracji podstawowej. Aby uzyskać bardziej szczegółowe informacje i [przykłady,](how-to-configure-api-proxy-module.md) zobacz Konfigurowanie modułu serwera proxy interfejsu API dla scenariusza hierarchii bramy.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz konfigurowane urządzenie najwyższej warstwy z listy urządzeń **IoT Edge .**
1. Wybierz **pozycję Ustaw moduły.**
1. W sekcji **IoT Edge modułów wybierz** pozycję **Dodaj,** a następnie wybierz moduł **Marketplace.**
1. Wyszukaj i wybierz moduł **serwera proxy IoT Edge API.**
1. Wybierz nazwę modułu serwera proxy interfejsu API z listy wdrożonych modułów i zaktualizuj następujące ustawienia modułu:
   1. Na **karcie Zmienne środowiskowe** zaktualizuj wartość właściwości **NGINX_DEFAULT_PORT** na `443` .
   1. Na karcie **Opcje tworzenia kontenera** zaktualizuj powiązania portów do portu 443.

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

   Te zmiany konfigurują moduł serwera proxy interfejsu API do nasłuchiwać na porcie 443. Aby zapobiec kolizjom powiązań portów, należy skonfigurować moduł edgeHub tak, aby nie nasłuchiwać na porcie 443. Zamiast tego moduł serwera proxy interfejsu API będzie rozsyłać dowolny ruch edgeHub na porcie 443.

1. Wybierz **pozycję Ustawienia środowiska** uruchomieniowego i znajdź opcje tworzenia modułu edgeHub. Usuń powiązanie portu 443, pozostawiając powiązania dla portów 5671 i 8883.

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

1. Wybierz **pozycję Zapisz,** aby zapisać zmiany w ustawieniach środowiska uruchomieniowego.
1. Wybierz **ponownie pozycję** Dodaj, a następnie IoT Edge **moduł**.
1. Podaj następujące wartości, aby dodać moduł rejestru platformy Docker do wdrożenia:
   1. **IoT Edge modułu:**`registry`
   1. Na karcie **Ustawienia modułu wybierz** pozycję Image **URI ( Image URI):**`registry:latest`
   1. Na **karcie Zmienne środowiskowe** dodaj następujące zmienne środowiskowe:

      * **Nazwa:** `REGISTRY_PROXY_REMOTEURL` **Wartość:** adres URL rejestru kontenerów, na który ma być mapowy ten moduł rejestru. Na przykład `https://myregistry.azurecr`.

        Moduł rejestru można mapować tylko na jeden rejestr kontenerów, dlatego zalecamy używanie wszystkich obrazów kontenerów w jednym prywatnym rejestrze kontenerów.

      * **Nazwa:** `REGISTRY_PROXY_USERNAME` **Wartość:** nazwa użytkownika do uwierzytelnienia w rejestrze kontenerów.

      * **Nazwa:** `REGISTRY_PROXY_PASSWORD` **Wartość:** Hasło do uwierzytelniania w rejestrze kontenerów.

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

1. Wybierz **pozycję Dodaj,** aby dodać moduł do wdrożenia.
1. Wybierz **pozycję Dalej: Trasy,** aby przejść do następnego kroku.
1. Aby umożliwić komunikatom z urządzenia do chmury z urządzeń IoT Hub, należy dołączyć trasę, która przekazuje wszystkie komunikaty do IoT Hub. Na przykład:
    1. **Nazwa:**`Route`
    1. **Wartość**: `FROM /messages/* INTO $upstream`
1. Wybierz **pozycję Przejrzyj i utwórz,** aby przejść do ostatniego kroku.
1. Wybierz **pozycję Utwórz,** aby wdrożyć na urządzeniu.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. W [Azure Cloud Shell](https://shell.azure.com/)utwórz plik JSON wdrożenia. Na przykład:

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
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
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
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
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

   Ten plik wdrożenia konfiguruje moduł serwera proxy interfejsu API do nasłuchiwać na porcie 443. Aby zapobiec kolizjom powiązań portów, plik konfiguruje moduł edgeHub tak, aby nie nasłuchiwać na porcie 443. Zamiast tego moduł serwera proxy interfejsu API będzie rozsyłać dowolny ruch edgeHub na porcie 443.

1. Wprowadź następujące polecenie, aby utworzyć wdrożenie na IoT Edge urządzenia:

   ```bash
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>Wdrażanie modułów na urządzeniach w niższej warstwie

IoT Edge w niższych warstwach hierarchii bramy mają jeden wymagany moduł, który musi zostać wdrożony na nich, oprócz wszystkich modułów obciążenia, które można uruchomić na urządzeniu.

#### <a name="route-container-image-pulls"></a>Ściąganie obrazu kontenera tras

Przed omówieniem wymaganego modułu serwera proxy dla urządzeń IoT Edge w hierarchiach bramy należy zrozumieć, jak urządzenia IoT Edge w niższych warstwach uzyskają obrazy modułów.

Jeśli urządzenia w niższej warstwie nie mogą łączyć się z chmurą, ale chcesz, aby ściągały obrazy modułów w zwykły sposób, urządzenie najwyższej warstwy w hierarchii bramy musi być skonfigurowane do obsługi tych żądań. Urządzenie najwyższej warstwy musi uruchomić moduł **rejestru** platformy Docker, który jest mapowany na rejestr kontenerów. Następnie skonfiguruj moduł serwera proxy interfejsu API, aby przekierowyć do niego żądania kontenera. Te szczegóły zostały omówione we wcześniejszych sekcjach tego artykułu. W tej konfiguracji urządzenia w niższej warstwie nie powinny wskazać rejestrów kontenerów w chmurze, ale rejestr uruchomiony w najwyższej warstwie.

Na przykład zamiast wywoływania `mcr.microsoft.com/azureiotedge-api-proxy:1.0` , urządzenia w warstwie niższej powinny wywołać . `$upstream:443/azureiotedge-api-proxy:1.0`

Parametr **$upstream** wskazuje element nadrzędny urządzenia warstwy niższej, więc żądanie będzie kierowane przez wszystkie warstwy do momentu osiągnięcia najwyższej warstwy, która ma środowisko proxy, przekierowywuje żądania kontenera do modułu rejestru. Port w tym przykładzie należy zastąpić portem, na którym nasłuchuje moduł serwera proxy interfejsu API na urządzeniu `:443` nadrzędnym.

Moduł serwera proxy interfejsu API może być przekierowynyny tylko do jednego modułu rejestru, a każdy moduł rejestru może być mapowy tylko na jeden rejestr kontenerów. W związku z tym wszystkie obrazy, które urządzenia niższej warstwy muszą ściągać, muszą być przechowywane w jednym rejestrze kontenerów.

Jeśli nie chcesz, aby urządzenia w niższej warstwie do żądań ściągnęć modułów za pośrednictwem hierarchii bramy, kolejną opcją jest zarządzanie rozwiązaniem rejestru lokalnego. Możesz też wypchnąć obrazy modułów na urządzenia przed utworzeniem wdrożeń, a następnie ustawić **wartość imagePullPolicy** na **nigdy.**

#### <a name="bootstrap-the-iot-edge-agent"></a>Bootstrap IoT Edge agenta

Agent IoT Edge jest pierwszym składnikiem środowiska uruchomieniowego, który uruchamia się na dowolnym IoT Edge uruchomieniowym. Musisz upewnić się, że wszystkie urządzenia podrzędne IoT Edge mają dostęp do obrazu modułu edgeAgent podczas uruchamiania, a następnie mogą uzyskać dostęp do wdrożeń i uruchomić pozostałe obrazy modułów.

Po dojściu do pliku konfiguracji na urządzeniu IoT Edge w celu podania informacji o uwierzytelnianiu, certyfikatów i nadrzędnej nazwy hosta zaktualizuj również obraz kontenera edgeAgent.

Jeśli urządzenie bramy najwyższego poziomu jest skonfigurowane do obsługi żądań obrazu kontenera, zastąp element nadrzędną nazwą hosta i portem nasłuchiwania `mcr.microsoft.com` serwera proxy interfejsu API. W manifeście wdrożenia możesz użyć go jako skrótu, ale wymaga to modułu edgeHub do obsługi routingu i ten moduł nie został uruchomiony `$upstream` w tym momencie. Na przykład:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

Jeśli używasz lokalnego rejestru kontenerów lub ręcznie dostarczasz obrazy kontenerów na urządzeniu, odpowiednio zaktualizuj plik konfiguracji.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Konfigurowanie środowiska uruchomieniowego i wdrażanie modułu serwera proxy

Moduł **serwera proxy interfejsu API** jest wymagany do kierowania całej komunikacji między chmurą a dowolnymi urządzeniami IoT Edge podrzędne. Urządzenie IoT Edge w dolnej warstwie hierarchii, bez urządzeń IoT Edge, nie potrzebuje tego modułu.

Moduł serwera proxy interfejsu API został zaprojektowany tak, aby był dostosowany do obsługi najbardziej typowych scenariuszy bramy. W tym artykule krótko opisano kroki konfigurowania modułów w konfiguracji podstawowej. Aby uzyskać bardziej szczegółowe informacje i [przykłady, zobacz](how-to-configure-api-proxy-module.md) Konfigurowanie modułu serwera proxy interfejsu API dla scenariusza hierarchii bramy.

1. W [Azure Portal](https://portal.azure.com)przejdź do centrum IoT Hub.
1. Wybierz **IoT Edge** z menu nawigacji.
1. Wybierz konfigurowane urządzenie w warstwie dolnej z listy urządzeń **IoT Edge warstwy**.
1. Wybierz **pozycję Ustaw moduły.**
1. W sekcji **IoT Edge modułów wybierz** pozycję **Dodaj,** a następnie wybierz moduł **Marketplace.**
1. Wyszukaj i wybierz moduł **serwera proxy IoT Edge API.**
1. Wybierz nazwę modułu serwera proxy interfejsu API z listy wdrożonych modułów i zaktualizuj następujące ustawienia modułu:
   1. Na karcie **Ustawienia modułu** zaktualizuj wartość Image **URI (Obraz URI).** Zastąp element `mcr.microsoft.com` pytaniem `$upstream:443`.
   1. Na **karcie Zmienne środowiskowe** zaktualizuj wartość właściwości **NGINX_DEFAULT_PORT** na `443` .
   1. Na karcie **Opcje tworzenia kontenera** zaktualizuj powiązania portów do portu 443.

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

   Te zmiany konfigurują moduł serwera proxy interfejsu API do nasłuchiwać na porcie 443. Aby zapobiec kolizjom powiązań portów, należy skonfigurować moduł edgeHub tak, aby nie nasłuchiwać na porcie 443. Zamiast tego moduł serwera proxy interfejsu API będzie rozsyłać dowolny ruch edgeHub na porcie 443.

1. Wybierz **pozycję Ustawienia środowiska uruchomieniowego.**
1. Zaktualizuj ustawienia modułu edgeHub:

   1. W polu **Obraz** zastąp `mcr.microsoft.com` wartością `$upstream:443` .
   1. W polu **Utwórz opcje** usuń powiązanie portu 443, pozostawiając powiązania dla portów 5671 i 8883.

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
   1. W polu **Obraz** zastąp `mcr.microsoft.com` wartością `$upstream:443` .

1. Wybierz **pozycję Zapisz,** aby zapisać zmiany w ustawieniach środowiska uruchomieniowego.
1. Wybierz **pozycję Dalej: Trasy,** aby przejść do następnego kroku.
1. Aby umożliwić komunikatom z urządzenia do chmury z urządzeń nadrzędnych dotarcie do IoT Hub, dołącz trasę, która przekazuje wszystkie komunikaty do usługi `$upstream` . Parametr nadrzędny wskazuje urządzenie nadrzędne w przypadku urządzeń w niższej warstwie. Na przykład:
    1. **Nazwa:**`Route`
    1. **Wartość**: `FROM /messages/* INTO $upstream`
1. Wybierz **pozycję Przejrzyj i utwórz,** aby przejść do ostatniego kroku.
1. Wybierz **pozycję Utwórz,** aby wdrożyć na urządzeniu.

## <a name="next-steps"></a>Następne kroki

[Jak używać urządzenia usługi IoT Edge jako bramy](iot-edge-as-gateway.md)

[Konfigurowanie modułu serwera proxy interfejsu API dla scenariusza hierarchii bramy](how-to-configure-api-proxy-module.md)