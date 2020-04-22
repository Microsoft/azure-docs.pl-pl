---
title: Przygotowanie do wdrożenia rozwiązania w produkcji — usługa Azure IoT Edge
description: Dowiedz się, jak zabrać swoje rozwiązanie usługi Azure IoT Edge z rozwoju do produkcji, w tym konfigurowanie urządzeń z odpowiednimi certyfikatami i tworzenie planu wdrożenia dla przyszłych aktualizacji kodu.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f1de8330b950ffa09ce3e8ae168f05021b2ad80c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729456"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Przygotowanie do wdrożenia rozwiązania IoT Edge w produkcji

Gdy wszystko będzie gotowe do wprowadzenia rozwiązania IoT Edge z programu dewelopera do produkcji, upewnij się, że jest ono skonfigurowane pod kątem ciągłej wydajności.

Informacje zawarte w tym artykule nie są równe. Aby ułatwić ustalanie priorytetów, każda sekcja zaczyna się od list, które dzielą pracę na dwie sekcje: **ważne** do ukończenia przed przejściem do produkcji lub **pomocne,** aby wiedzieć.

## <a name="device-configuration"></a>Konfiguracja urządzenia

Urządzenia IoT Edge mogą być czymś, od Raspberry Pi do laptopa do maszyny wirtualnej uruchomionej na serwerze. Możesz mieć dostęp do urządzenia fizycznie lub za pośrednictwem połączenia wirtualnego lub może być izolowane przez dłuższy czas. Tak czy inaczej, chcesz upewnić się, że jest skonfigurowany do pracy odpowiednio.

* **Ważne**
  * Instalowanie certyfikatów produkcyjnych
  * Mieć plan zarządzania urządzeniami
  * Użyj Moby jako silnika kontenera

* **Przydatne**
  * Wybieranie protokołu nadrzędnego

### <a name="install-production-certificates"></a>Instalowanie certyfikatów produkcyjnych

Każde urządzenie IoT Edge w produkcji potrzebuje certyfikatu urzędu certyfikacji urządzenia (CA). Ten certyfikat urzędu certyfikacji jest następnie deklarowany do środowiska wykonawczego usługi IoT Edge w pliku config.yaml. W scenariuszach programowania i testowania środowisko uruchomieniowe usługi IoT Edge tworzy tymczasowe certyfikaty, jeśli w pliku config.yaml nie są deklarowane żadne certyfikaty. Jednak te tymczasowe certyfikaty wygasają po trzech miesiącach i nie są bezpieczne dla scenariuszy produkcyjnych.

Aby zrozumieć rolę certyfikatu urzędu certyfikacji urządzenia, zobacz [Jak usługa Azure IoT Edge używa certyfikatów](iot-edge-certs.md).

Aby uzyskać więcej informacji na temat instalowania certyfikatów na urządzeniu usługi IoT Edge i odwoływania się do nich z pliku config.yaml, zobacz [Instalowanie certyfikatów produkcyjnych na urządzeniu usługi IoT Edge](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Mieć plan zarządzania urządzeniami

Przed umieszczeniem dowolnego urządzenia w produkcji powinieneś wiedzieć, jak zamierzasz zarządzać przyszłymi aktualizacjami. W przypadku urządzenia Usługi IoT Edge lista składników do aktualizacji może zawierać:

* Oprogramowanie układowe urządzenia
* Biblioteki systemów operacyjnych
* Silnik kontenerowy, jak Moby
* Demon IoT Edge
* Certyfikaty urzędów certyfikacji

Aby uzyskać więcej informacji, zobacz [Aktualizowanie środowiska wykonawczego usługi IoT Edge](how-to-update-iot-edge.md). Bieżące metody aktualizowania demona IoT Edge wymagają fizycznego lub SSH dostępu do urządzenia IoT Edge. Jeśli masz wiele urządzeń do aktualizacji, należy rozważyć dodanie kroków aktualizacji do skryptu lub użyć narzędzia automatyzacji, takich jak Ansible.

### <a name="use-moby-as-the-container-engine"></a>Użyj Moby jako silnika kontenera

Aparat kontenera jest warunkiem wstępnym dla każdego urządzenia usługi IoT Edge. Tylko moby-engine jest obsługiwany w produkcji. Inne aparaty kontenerów, takie jak docker, współpracują z aplikacją IoT Edge i można używać tych silników do tworzenia. Aparat moby może być ponownie rozdzielony, gdy jest używany z usługą Azure IoT Edge, a firma Microsoft zapewnia obsługę dla tego aparatu.

### <a name="choose-upstream-protocol"></a>Wybieranie protokołu nadrzędnego

Można skonfigurować protokół (który określa używany port) do komunikacji nadrzędnej do usługi IoT Hub dla agenta usługi IoT Edge i centrum usługi IoT Edge. Domyślnym protokołem jest protokół AMQP, ale można to zmienić w zależności od konfiguracji sieci.

Dwa moduły środowiska wykonawczego oba mają zmienną środowiskową **UpstreamProtocol.** Prawidłowe wartości zmiennej to:

* MQTT
* AMQP
* MQTTWS ( MQTTWS )
* Z OKS

Skonfiguruj zmienną UpstreamProtocol dla agenta usługi IoT Edge w pliku config.yaml na samym urządzeniu. Jeśli na przykład urządzenie usługi IoT Edge znajduje się za serwerem proxy, który blokuje porty USŁUGI AMQP, może być konieczne skonfigurowanie agenta usługi IoT Edge do używania usługi AMQP przez usługę WebSocket (AMQPWS) w celu nawiązania początkowego połączenia z centrum IoT Hub.

Po nawiązaniu połączenia urządzenia Usługi IoT Edge należy kontynuować konfigurowanie zmiennej UpstreamProtocol dla obu modułów środowiska uruchomieniowego w przyszłych wdrożeniach. Przykład tego procesu znajduje się w [temacie Konfigurowanie urządzenia usługi IoT Edge do komunikacji za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Wdrożenie

* **Przydatne**
  * Bądź zgodny z protokołem nadrzędnym
  * Konfigurowanie magazynu hosta dla modułów systemowych
  * Zmniejsz ilość miejsca w pamięci używanej przez koncentrator IoT Edge
  * Nie używaj wersji debugowania obrazów modułów

### <a name="be-consistent-with-upstream-protocol"></a>Bądź zgodny z protokołem nadrzędnym

Jeśli agent usługi IoT Edge został skonfigurowany na urządzeniu usługi IoT Edge do używania innego protokołu niż domyślny protokół AMQP, należy zadeklarować ten sam protokół we wszystkich przyszłych wdrożeniach. Jeśli na przykład urządzenie usługi IoT Edge znajduje się za serwerem proxy, który blokuje porty AMQP, prawdopodobnie skonfigurowano urządzenie do łączenia się za pośrednictwem usługi AMQP za pośrednictwem usługi WebSocket (AMQPWS). Podczas wdrażania modułów na urządzeniu należy skonfigurować ten sam protokół AMQPWS dla agenta usługi IoT Edge i centrum Usługi IoT Edge, w przeciwnym razie domyślny protokół AMQP zastąpi ustawienia i uniemożliwi ponowne nawiązanie połączenia.

Należy skonfigurować tylko zmienną środowiskową UpstreamProtocol dla agenta usługi IoT Edge i modułów koncentratora usługi IoT Edge. Wszelkie dodatkowe moduły przyjmują dowolny protokół ustawiony w modułach środowiska wykonawczego.

Przykład tego procesu znajduje się w [temacie Konfigurowanie urządzenia usługi IoT Edge do komunikacji za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Konfigurowanie magazynu hosta dla modułów systemowych

Moduły usługi IoT Edge i agent używają magazynu lokalnego do obsługi stanu i włączania obsługi wiadomości między modułami, urządzeniami i chmurą. Aby uzyskać większą niezawodność i wydajność, skonfiguruj moduły systemowe do używania pamięci masowej w systemie plików hosta.

Aby uzyskać więcej informacji, zobacz [Magazyn hosta dla modułów systemowych](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Zmniejsz ilość miejsca w pamięci używanej przez centrum Usługi IoT Edge

Jeśli wdrażasz ograniczone urządzenia z ograniczoną ilością dostępnej pamięci, możesz skonfigurować centrum Usługi IoT Edge tak, aby działało w bardziej usprawnionych pojemnościach i zużywało mniej miejsca na dysku. Te konfiguracje ograniczają jednak wydajność centrum usługi IoT Edge, więc znajdź właściwą równowagę, która działa dla twojego rozwiązania.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Nie optymalizuj pod kątem wydajności na urządzeniach o ograniczonym zasięgu

Centrum usługi IoT Edge jest domyślnie zoptymalizowane pod kątem wydajności, więc próbuje przydzielić duże fragmenty pamięci. Ta konfiguracja może powodować problemy ze stabilnością na mniejszych urządzeniach, takich jak Raspberry Pi. Jeśli wdrażasz urządzenia z ograniczonymi zasobami, możesz ustawić zmienną środowiskową **OptimizeForPerformance** na **false** w centrum usługi IoT Edge.

Gdy **OptimizeForPerformance** jest ustawiona na **true,** głowica protokołu MQTT używa PooledByteBufferAllocator, który ma lepszą wydajność, ale przydziela więcej pamięci. Alokator nie działa dobrze na 32-bitowych systemach operacyjnych lub na urządzeniach o małej ilości pamięci. Ponadto po zoptymalizowaniu pod kątem wydajności, RocksDb przydziela więcej pamięci do swojej roli jako dostawcy magazynu lokalnego.

Aby uzyskać więcej informacji, zobacz [Problemy ze stabilnością urządzeń z ograniczeniami zasobów](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Wyłączanie nieużywanych protokołów

Innym sposobem optymalizacji wydajności centrum Usługi IoT Edge i zmniejszenia jego użycia pamięci jest wyłączenie głowic protokołów dla protokołów, których nie używasz w rozwiązaniu.

Głowice protokołów są konfigurowane przez ustawienie zmiennych środowiskowych logicznych dla modułu centrum usługi IoT Edge w manifestach wdrażania. Trzy zmienne to:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Wszystkie trzy zmienne mają *dwa podkreślenia* i można ustawić na true lub false.

#### <a name="reduce-storage-time-for-messages"></a>Skrócenie czasu przechowywania wiadomości

Moduł centrum usługi IoT Edge przechowuje wiadomości tymczasowo, jeśli nie mogą być dostarczone do Usługi IoT Hub z jakiegokolwiek powodu. Można skonfigurować, jak długo centrum Usługi IoT Edge przechowuje na niedostarczone wiadomości przed pozwoleniem ich wygaśnięcia. Jeśli masz problemy z pamięcią na urządzeniu, można obniżyć **timeToLiveSecs** wartość w bliźniaczej reprezentacji modułu ioT Edge koncentratora.

Domyślna wartość parametru timeToLiveSecs wynosi 7200 sekund, czyli dwie godziny.

### <a name="do-not-use-debug-versions-of-module-images"></a>Nie używaj wersji debugowania obrazów modułów

Podczas przechodzenia ze scenariuszy testowych do scenariuszy produkcyjnych należy pamiętać o usunięciu konfiguracji debugowania z manifestów wdrażania. Sprawdź, czy żaden z obrazów modułu w manifestach wdrażania nie ma sufiksu ** \.debugowania.** Jeśli dodano opcje tworzenia, aby udostępnić porty w modułach do debugowania, usuń te opcje tworzenia, jak również.

## <a name="container-management"></a>Zarządzanie kontenerami

* **Ważne**
  * Zarządzanie dostępem do rejestru kontenerów
  * Zarządzanie wersjami za pomocą tagów

### <a name="manage-access-to-your-container-registry"></a>Zarządzanie dostępem do rejestru kontenerów

Przed wdrożeniem modułów na urządzeniach produkcyjnych usługi IoT Edge należy kontrolować dostęp do rejestru kontenerów, aby osoby postronne nie mogły uzyskać dostępu do obrazów kontenerów ani ich wprowadzać. Do zarządzania obrazami kontenerów należy używać prywatnego, nie publicznego rejestru kontenerów.

W samouczkach i innej dokumentacji firma Westruujemy użytkownika, aby używać tych samych poświadczeń rejestru kontenerów na urządzeniu usługi IoT Edge, które są używane na komputerze deweloperskim. Te instrukcje są przeznaczone tylko do konfigurowania środowisk testowania i programowania łatwiej i nie powinny być przestrzegane w scenariuszu produkcyjnym.

Aby uzyskać bardziej bezpieczny dostęp do rejestru, masz do wyboru [opcje uwierzytelniania.](../container-registry/container-registry-authentication.md) Popularnym i zalecanym uwierzytelnianiem jest użycie jednostki usługi Active Directory, która jest dobrze odpowiednia dla aplikacji lub usług do ściągania obrazów kontenerów w sposób zautomatyzowany lub w inny sposób nienadzorowany (bezołowy), tak jak robią to urządzenia usługi IoT Edge.

Aby utworzyć jednostkę usługi, uruchom dwa skrypty zgodnie z [opisem](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal)w create a service principal . Te skrypty wykonują następujące zadania:

* Pierwszy skrypt tworzy jednostkę usługi. Jest to identyfikator jednostki usługi i hasło jednostki usługi. Przechowuj te wartości bezpiecznie w rekordach.

* Drugi skrypt tworzy przypisania ról do przyznania podmiotowi usługi, które można uruchomić następnie w razie potrzeby. Zaleca się zastosowanie roli użytkownika **acrPull** dla parametru. `role` Aby uzyskać listę ról, zobacz [Role i uprawnienia rejestru kontenerów platformy Azure](../container-registry/container-registry-roles.md).

Aby uwierzytelnić się przy użyciu jednostki usługi, podaj identyfikator jednostki usługi i hasło uzyskane z pierwszego skryptu. Określ te poświadczenia w manifeście wdrażania.

* W przypadku nazwy użytkownika lub identyfikatora klienta określ identyfikator jednostki usługi.

* W przypadku hasła lub klucza tajnego klienta określ hasło jednostki usługi.

> [!NOTE]
> Po zaimplementowanie rozszerzonego uwierzytelniania zabezpieczeń należy wyłączyć ustawienie **Administrator,** aby domyślny dostęp do nazwy użytkownika/hasła nie był już dostępny. W rejestrze kontenerów w witrynie Azure portal z menu lewego okienka w obszarze **Ustawienia**wybierz pozycję **Klawisze dostępu**.

### <a name="use-tags-to-manage-versions"></a>Zarządzanie wersjami za pomocą tagów

Tag to koncepcja platformy docker, której można użyć do rozróżniania wersji kontenerów docker. Tagi są sufiksy jak **1.0,** które go na końcu repozytorium kontenera. Na przykład **mcr.microsoft.com/azureiotedge-agent:1.0**. Tagi są modyfikowalne i można je zmienić, aby wskazać inny kontener w dowolnym momencie, więc zespół powinien uzgodnić konwencję do naśladowania podczas aktualizowania obrazów modułu do przodu.

Tagi ułatwiają również wymuszanie aktualizacji na urządzeniach IoT Edge. Po wypchnięciu zaktualizowanej wersji modułu do rejestru kontenerów należy zwiększyć tag. Następnie wypchnij nowe wdrożenie do swoich urządzeń z przyrostem tagu. Aparat kontenera rozpozna przyrostowy tag jako nową wersję i pociągnie najnowszą wersję modułu w dół do urządzenia.

Na przykład konwencji tagów zobacz [Aktualizowanie środowiska wykonawczego usługi IoT Edge,](how-to-update-iot-edge.md#understand-iot-edge-tags) aby dowiedzieć się, jak usługa IoT Edge używa tagów rolowanych i określonych tagów do śledzenia wersji.

## <a name="networking"></a>Networking

* **Przydatne**
  * Przeglądanie konfiguracji ruchu wychodzącego/przychodzącego
  * Zezwalaj na połączenia z urządzeń Usługi IoT Edge
  * Konfigurowanie komunikacji za pośrednictwem serwera proxy

### <a name="review-outboundinbound-configuration"></a>Przeglądanie konfiguracji ruchu wychodzącego/przychodzącego

Kanały komunikacji między usługą Azure IoT Hub i usługą IoT Edge są zawsze skonfigurowane do ruchu wychodzącego. W większości scenariuszy usługi IoT Edge konieczne są tylko trzy połączenia. Aparat kontenerów musi połączyć się z rejestru kontenerów (lub rejestrów), który przechowuje obrazy modułu. Środowisko wykonawcze usługi IoT Edge musi połączyć się z Centrum IoT Hub, aby pobrać informacje o konfiguracji urządzenia oraz wysłać wiadomości i dane telemetryczne. A jeśli korzystasz z automatycznego inicjowania obsługi administracyjnej, demon usługi IoT Edge musi połączyć się z usługą inicjowania obsługi administracyjnej urządzeń. Aby uzyskać więcej informacji, zobacz [Reguły konfiguracji zapory i portu](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Zezwalaj na połączenia z urządzeń Usługi IoT Edge

Jeśli konfiguracja sieci wymaga jawnego zezwolenia na połączenia wykonane z urządzeń Usługi IoT Edge, przejrzyj następującą listę składników usługi IoT Edge:

* **Agent usługi IoT Edge** otwiera trwałe połączenie AMQP/MQTT z centrum IoT Hub, prawdopodobnie za pośrednictwem websockets.
* **Centrum usługi IoT Edge** otwiera pojedyncze trwałe połączenie AMQP lub wiele połączeń MQTT z Centrum IoT Hub, prawdopodobnie za pośrednictwem websockets.
* **Demon IoT Edge** wykonuje sporadyczne wywołania HTTPS do Centrum IoT Hub.

We wszystkich trzech przypadkach nazwa DNS \*będzie zgodna ze wzorcem .azure-devices.net.

Ponadto aparat **kontenera** wykonuje wywołania rejestrów kontenerów za pośrednictwem protokołu HTTPS. Aby pobrać obrazy kontenerów środowiska uruchomieniowego usługi IoT Edge, nazwa DNS jest mcr.microsoft.com. Aparat kontenera łączy się z innymi rejestrami skonfigurowany w wdrożeniu.

Ta lista kontrolna jest punktem wyjścia dla reguł zapory:

   | ADRES\* URL ( = symbol wieloznaczny) | Wychodzące porty TCP | Sposób użycia |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Rejestr kontenerów firmy Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Dostęp DPS (opcjonalnie) |
   | \*azurecr.io .azurecr.io | 443 | Rejestry kontenerów osobistych i rejestrów kontenerów innych firm |
   | \*.blob.core.windows.net | 443 | Pobieranie różnic obrazu rejestru kontenerów platformy Azure z magazynu obiektów blob |
   | \*azure-devices.net .azure-devices.net | 5671, 8883, 443 | Dostęp do centrum IoT |
   | \*docker.io .  | 443 | Dostęp do centrum platformy Docker (opcjonalnie) |

Niektóre z tych reguł zapory są dziedziczone z rejestru kontenerów platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguł dostępu do rejestru kontenerów platformy Azure za zaporą](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Konfigurowanie komunikacji za pośrednictwem serwera proxy

Jeśli urządzenia mają być wdrożone w sieci, która używa serwera proxy, muszą być w stanie komunikować się za pośrednictwem serwera proxy, aby uzyskać dostęp do usługi IoT Hub i rejestrów kontenerów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia usługi IoT Edge do komunikowania się za pośrednictwem serwera proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Zarządzanie rozwiązaniami

* **Przydatne**
  * Konfigurowanie dzienników i diagnostyki
  * Rozważ testy i potoki ciągłej integracji/ciągłej integracji/dysków CD

### <a name="set-up-logs-and-diagnostics"></a>Konfigurowanie dzienników i diagnostyki

W systemie Linux demon IoT Edge używa dzienników jako domyślnego sterownika rejestrowania. Za pomocą narzędzia `journalctl` wiersza polecenia można wysyłać zapytania do dzienników demonów. W systemie Windows demon IoT Edge używa diagnostyki programu PowerShell. Służy `Get-IoTEdgeLog` do wykonywania zapytań o dzienniki z demona. Moduły usługi IoT Edge używają sterownika JSON do rejestrowania, co jest wartością domyślną.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Podczas testowania wdrożenia usługi IoT Edge, zazwyczaj można uzyskać dostęp do urządzeń, aby pobrać dzienniki i rozwiązywać problemy. W scenariuszu wdrażania może nie mieć tej opcji. Zastanów się, jak zamierzasz zbierać informacje o swoich urządzeniach w produkcji. Jedną z opcji jest użycie modułu rejestrowania, który zbiera informacje z innych modułów i wysyła je do chmury. Jednym z przykładów modułu rejestrowania jest [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), lub można zaprojektować własne.

### <a name="place-limits-on-log-size"></a>Umieść limity rozmiaru dziennika

Domyślnie aparat kontenera Moby nie ustawia limitów rozmiaru dziennika kontenera. Z biegiem czasu może to prowadzić do urządzenia napełniania dzienników i wyczerpania miejsca na dysku. Należy wziąć pod uwagę następujące opcje, aby temu zapobiec:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Opcja: Ustawianie limitów globalnych, które mają zastosowanie do wszystkich modułów kontenerów

Można ograniczyć rozmiar wszystkich plików dziennika kontenera w opcjach dziennika aparatu kontenera. W poniższym przykładzie `json-file` ustawia sterownik dziennika na (zalecane) z ograniczeniami rozmiaru i liczby plików:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Dodaj (lub dołącz) te informacje `daemon.json` do pliku o nazwie i umieść je w odpowiedniej lokalizacji dla platformy urządzenia.

| Platforma | Lokalizacja |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Aby zmiany zostały wprowadzone, należy ponownie uruchomić aparat kontenera.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Opcja: Dostosuj ustawienia dziennika dla każdego modułu kontenera

Można to zrobić w **createOptions** każdego modułu. Przykład:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Dodatkowe opcje w systemach Linux

* Skonfiguruj aparat kontenerów `systemd` do `journald` wysyłania dzienników do [dziennika,](https://docs.docker.com/config/containers/logging/journald/) ustawiając jako domyślny sterownik rejestrowania.

* Okresowo usuwaj stare dzienniki z urządzenia, instalując narzędzie logrotate. Użyj następującej specyfikacji pliku:

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Rozważ testy i potoki ciągłej integracji/ciągłej integracji/dysków CD

Aby uzyskać najbardziej efektywny scenariusz wdrażania usługi IoT Edge, należy rozważyć zintegrowanie wdrożenia produkcyjnego z potokami testowania i ciągłej integracji/ciągłego wdrażania. Usługa Azure IoT Edge obsługuje wiele platform ciągłej integracji/ciągłego wdrażania, w tym usługi Azure DevOps. Aby uzyskać więcej informacji, zobacz [Ciągła integracja i ciągłe wdrażanie usługi Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [automatycznym wdrażaniu usługi IoT Edge](module-deployment-monitoring.md).
* Zobacz, jak IoT Edge obsługuje [ciągłą integrację i ciągłe wdrażanie.](how-to-ci-cd.md)
