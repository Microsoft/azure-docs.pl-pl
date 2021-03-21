---
title: Przygotowanie do wdrożenia rozwiązania w środowisku produkcyjnym — Azure IoT Edge
description: Dowiedz się, jak wdrożyć rozwiązanie Azure IoT Edge od projektowania w środowisku produkcyjnym, w tym konfigurowania urządzeń przy użyciu odpowiednich certyfikatów i sporządzania planu wdrożenia w przyszłości.
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
ms.openlocfilehash: fda69d582f26b0c9189898bb5c8b0004a1e47360
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722773"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Przygotowanie do wdrożenia rozwiązania IoT Edge w środowisku produkcyjnym

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Gdy wszystko jest gotowe do przygotowania rozwiązania IoT Edge od projektowania do produkcji, należy upewnić się, że jest ono skonfigurowane pod kątem ciągłej wydajności.

Informacje zawarte w tym artykule nie są równe. Aby ułatwić określanie priorytetów, Każda sekcja rozpoczyna się od listy dzielącej pracę na dwie sekcje: **Ważne** do ukończenia przed przejściem do środowiska produkcyjnego lub **pomocne** dla użytkownika.

## <a name="device-configuration"></a>Konfiguracja urządzenia

Urządzenia IoT Edge mogą być dowolne od Raspberry Pi do laptopu do maszyny wirtualnej działającej na serwerze. Dostęp do urządzenia może być fizycznie lub przez połączenie wirtualne lub może być odizolowany przez dłuższy czas. W obu przypadkach trzeba upewnić się, że jest odpowiednio skonfigurowany do pracy.

* **Ważne**
  * Instalowanie certyfikatów produkcyjnych
  * Zaplanuj zarządzanie urządzeniami
  * Używanie Moby jako aparatu kontenera

* **Pomaga**
  * Wybierz protokół nadrzędny

### <a name="install-production-certificates"></a>Instalowanie certyfikatów produkcyjnych

Każde urządzenie IoT Edge w środowisku produkcyjnym wymaga zainstalowanego certyfikatu urzędu certyfikacji urządzenia (CA). Ten certyfikat urzędu certyfikacji jest następnie deklarowany do środowiska uruchomieniowego IoT Edge w pliku konfiguracyjnym. W przypadku scenariuszy projektowania i testowania środowisko uruchomieniowe IoT Edge tworzy certyfikaty tymczasowe, jeśli w pliku konfiguracyjnym nie zadeklarowano żadnych certyfikatów. Jednak te certyfikaty tymczasowe wygasną po trzech miesiącach i nie są bezpieczne w scenariuszach produkcyjnych. W przypadku scenariuszy produkcyjnych należy podać własny certyfikat urzędu certyfikacji z podpisem własnym lub zakupionego od komercyjnego urzędu certyfikacji.

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038.

:::moniker-end

Aby zrozumieć rolę certyfikatu urzędu certyfikacji, zobacz [jak Azure IoT Edge używa certyfikatów](iot-edge-certs.md).

Aby uzyskać więcej informacji na temat sposobu instalowania certyfikatów na urządzeniu IoT Edge i odwoływania się do nich z pliku konfiguracji, zobacz [Zarządzanie certyfikatem na urządzeniu IoT Edge](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Zaplanuj zarządzanie urządzeniami

Przed umieszczeniem dowolnego urządzenia w środowisku produkcyjnym należy wiedzieć, jak chcesz zarządzać przyszłymi aktualizacjami. W przypadku urządzenia IoT Edge lista składników do zaktualizowania może obejmować:

* Oprogramowanie układowe urządzenia
* Biblioteki systemu operacyjnego
* Aparat kontenerów, taki jak Moby
* IoT Edge
* Certyfikaty urzędów certyfikacji

Aby uzyskać więcej informacji, zobacz [aktualizacja środowiska uruchomieniowego IoT Edge](how-to-update-iot-edge.md). Bieżące metody aktualizowania IoT Edge wymagają fizycznego lub SSH dostępu do urządzenia IoT Edge. Jeśli masz wiele urządzeń do zaktualizowania, rozważ dodanie kroków aktualizacji do skryptu lub użyj narzędzia automatyzacji, takiego jak rozwiązania ansible.

### <a name="use-moby-as-the-container-engine"></a>Używanie Moby jako aparatu kontenera

Aparat kontenerów jest wymaganiem wstępnym dla dowolnego IoT Edge urządzenia. Tylko aparat Moby jest obsługiwany w środowisku produkcyjnym. Inne aparaty kontenerów, takie jak Docker, pracują z IoT Edge i są tak, aby używać tych aparatów do programowania. Moby-Engine można rozpowszechniać w przypadku użycia z Azure IoT Edge, a firma Microsoft zapewnia obsługę tego aparatu.

### <a name="choose-upstream-protocol"></a>Wybierz protokół nadrzędny

Można skonfigurować protokół (który określa używany port) do komunikacji nadrzędnej IoT Hub zarówno dla agenta IoT Edge, jak i do centrum IoT Edge. Domyślny protokół to AMQP, ale można go zmienić w zależności od konfiguracji sieci.

Dwa moduły środowiska uruchomieniowego mają zmienną środowiskową **UpstreamProtocol** . Prawidłowe wartości dla zmiennej to:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Skonfiguruj zmienną UpstreamProtocol dla agenta IoT Edge w pliku konfiguracyjnym na urządzeniu. Na przykład jeśli urządzenie IoT Edge znajduje się za serwerem proxy, który blokuje porty AMQP, może być konieczne skonfigurowanie agenta IoT Edge do używania AMQP przez protokół WebSocket (AMQPWS) w celu nawiązania początkowego połączenia z IoT Hub.

Po nawiązaniu połączenia z urządzeniem IoT Edge należy kontynuować konfigurowanie zmiennej UpstreamProtocol dla obu modułów czasu wykonywania w przyszłych wdrożeniach. Przykład tego procesu znajduje się w temacie [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Wdrożenie

* **Pomaga**
  * Być zgodne z protokołem nadrzędnym
  * Konfigurowanie magazynu hosta dla modułów systemowych
  * Zmniejszanie ilości miejsca w pamięci używanej przez Centrum IoT Edge
  * Nie używaj debugowania wersji obrazów modułów

### <a name="be-consistent-with-upstream-protocol"></a>Być zgodne z protokołem nadrzędnym

W przypadku skonfigurowania agenta IoT Edge na urządzeniu IoT Edge w celu użycia innego protokołu niż domyślny AMQP należy zadeklarować ten sam protokół we wszystkich przyszłych wdrożeniach. Na przykład jeśli urządzenie IoT Edge znajduje się za serwerem proxy, który blokuje porty AMQP, prawdopodobnie urządzenie zostało skonfigurowane w celu połączenia za pośrednictwem AMQP przez protokół WebSocket (AMQPWS). Podczas wdrażania modułów na urządzeniu Skonfiguruj ten sam protokół AMQPWS dla agenta IoT Edge i IoT Edge Hub, a w przeciwnym razie domyślne AMQP przesłonią ustawienia i uniemożliwią ponowne nawiązanie połączenia.

Należy tylko skonfigurować zmienną środowiskową UpstreamProtocol dla agentów IoT Edge i IoT Edge modułów centrum. Wszelkie dodatkowe moduły przyjmują dowolny protokół jest ustawiany w modułach środowiska uruchomieniowego.

Przykład tego procesu znajduje się w temacie [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Konfigurowanie magazynu hosta dla modułów systemowych

Moduły IoT Edge Hub i Agent używają magazynu lokalnego do utrzymania stanu i włączania obsługi komunikatów między modułami, urządzeniami i chmurą. Aby zapewnić lepszą niezawodność i wydajność, należy skonfigurować moduły systemowe do korzystania z magazynu w systemie plików hosta.

Aby uzyskać więcej informacji, zobacz [host Storage for System modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Zmniejszanie ilości miejsca w pamięci używanej przez Centrum IoT Edge

W przypadku wdrażania urządzeń z ograniczeniami z ograniczoną ilością dostępnej pamięci można skonfigurować IoT Edge Hub do uruchamiania w bardziej usprawnionej pojemności i zmniejszyć ilość miejsca na dysku. Te konfiguracje ograniczają wydajność centrum IoT Edge, jednak można znaleźć odpowiednie saldo, które działa dla danego rozwiązania.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Nie Optymalizuj pod kątem wydajności na ograniczonych urządzeniach

Centrum IoT Edge jest zoptymalizowane pod kątem wydajności domyślnie, dlatego próbuje przydzielić duże fragmenty pamięci. Ta konfiguracja może spowodować problemy ze stabilnością na mniejszych urządzeniach, takich jak Raspberry Pi. Jeśli wdrażasz urządzenia z ograniczonymi zasobami, możesz chcieć ustawić dla zmiennej środowiskowej **OptimizeForPerformance** **wartość false** w centrum IoT Edge.

Gdy **OptimizeForPerformance** ma **wartość true**, kierownik protokołu MQTT używa PooledByteBufferAllocator, co zapewnia lepszą wydajność, ale przydziela więcej pamięci. Program przydzielający nie działa poprawnie z 32-bitowymi systemami operacyjnymi ani na urządzeniach z małą ilością pamięci. Ponadto w przypadku optymalizacji pod kątem wydajności program RocksDb przydziela więcej pamięci dla roli jako dostawcy magazynu lokalnego.

Aby uzyskać więcej informacji, zobacz [problemy ze stabilnością na mniejszych urządzeniach](troubleshoot-common-errors.md#stability-issues-on-smaller-devices).

#### <a name="disable-unused-protocols"></a>Wyłącz nieużywane protokoły

Innym sposobem na zoptymalizowanie wydajności Centrum IoT Edge i zmniejszenie użycia pamięci jest wyłączenie głowic protokołu dla protokołów, które nie są używane w rozwiązaniu.

Głowice protokołów są konfigurowane przez ustawienie logicznych zmiennych środowiskowych dla modułu IoT Edge Hub w manifestach wdrożenia. Trzy zmienne:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Wszystkie trzy zmienne mają *dwie podkreślenia* i mogą być ustawione na wartość true lub false.

#### <a name="reduce-storage-time-for-messages"></a>Skrócenie czasu magazynowania dla komunikatów

Moduł IoT Edge Hub przechowuje komunikaty tymczasowo, jeśli nie można ich dostarczyć do IoT Hub z dowolnego powodu. Można skonfigurować, jak długo Centrum IoT Edge będzie przechowywane do niedostarczonych komunikatów przed ich wygaśnięciem. Jeśli masz problemy z pamięcią na urządzeniu, możesz obniżyć wartość **timeToLiveSecs** w postaci sznurka modułu IoT Edge Hub.

Wartość domyślna parametru timeToLiveSecs to 7200 sekund, czyli dwie godziny.

### <a name="do-not-use-debug-versions-of-module-images"></a>Nie używaj debugowania wersji obrazów modułów

Podczas przechodzenia z scenariuszy testowych do scenariuszy produkcyjnych należy pamiętać o usunięciu konfiguracji debugowania z manifestów wdrożenia. Sprawdź, czy żaden z obrazów modułu w manifestach wdrożenia ma sufiks **\. debugowania** . Jeśli dodano opcje tworzenia w celu udostępnienia portów w modułach na potrzeby debugowania, Usuń również te opcje.

## <a name="container-management"></a>Zarządzanie kontenerami

* **Ważne**
  * Zarządzanie dostępem do rejestru kontenerów
  * Używanie tagów do zarządzania wersjami
* **Pomaga**
  * Przechowuj kontenery środowiska uruchomieniowego w rejestrze prywatnym

### <a name="manage-access-to-your-container-registry"></a>Zarządzanie dostępem do rejestru kontenerów

Przed wdrożeniem modułów na urządzeniach produkcyjnych IoT Edge upewnij się, że masz kontrolę dostępu do rejestru kontenerów, dzięki czemu nie będą mogli uzyskać dostępu do obrazów kontenera ani wprowadzać do nich zmiany. Do zarządzania obrazami kontenerów służy prywatny, niepubliczny rejestr kontenerów.

W samouczkach i innych dokumentach poinformujemy o użyciu tych samych poświadczeń rejestru kontenerów na urządzeniu z systemem IoT Edge, które są używane na komputerze deweloperskim. Te instrukcje są przeznaczone tylko do ułatwienia konfigurowania środowisk testowych i programistycznych i nie należy ich stosować w scenariuszu produkcyjnym.

Aby uzyskać bardziej bezpieczny dostęp do rejestru, można wybrać [Opcje uwierzytelniania](../container-registry/container-registry-authentication.md). Popularnym i zalecanym uwierzytelnianiem jest użycie jednostki usługi Active Directory, która jest odpowiednia dla aplikacji lub usług w celu ściągania obrazów kontenera w sposób zautomatyzowany lub w inny sposób nienadzorowany (bezobsługowy), jak urządzenia IoT Edge.

Aby utworzyć jednostkę usługi, Uruchom dwa skrypty zgodnie z opisem w temacie [Tworzenie nazwy głównej usługi](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal). Skrypty te wykonują następujące zadania:

* Pierwszy skrypt tworzy jednostkę usługi. Powoduje to wyjście z identyfikatora jednostki usługi i hasła nazwy głównej usługi. Bezpiecznie przechowuj te wartości w rekordach.

* Drugi skrypt tworzy przypisania ról do udzielenia jednostce usługi, która może być uruchamiana w razie potrzeby. Zalecamy zastosowanie **acrPull** roli użytkownika dla `role` parametru. Aby uzyskać listę ról, zobacz [Azure Container Registry ról i uprawnień](../container-registry/container-registry-roles.md).

Aby uwierzytelnić się przy użyciu nazwy głównej usługi, podaj identyfikator jednostki usługi i hasło uzyskane z pierwszego skryptu. Określ te poświadczenia w manifeście wdrożenia.

* Określ identyfikator jednostki usługi w polu Nazwa użytkownika lub identyfikator klienta.

* W przypadku hasła lub klucza tajnego klienta określ hasło nazwy głównej usługi.

> [!NOTE]
> Po wdrożeniu rozszerzonego uwierzytelniania zabezpieczeń należy wyłączyć ustawienie **użytkownika Administrator** , aby domyślny dostęp do nazwy użytkownika/hasła nie był już dostępny. W rejestrze kontenerów w Azure Portal z menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **klucze dostępu**.

### <a name="use-tags-to-manage-versions"></a>Używanie tagów do zarządzania wersjami

Tag to koncepcja platformy Docker, której można użyć do rozróżnienia między wersjami kontenerów platformy Docker. Tagi to sufiksy podobne do **1,0** , które znajdują się na końcu repozytorium kontenera. Na przykład **MCR.Microsoft.com/azureiotedge-Agent:1.0**. Tagi są modyfikowalne i można je zmienić tak, aby wskazywały na inny kontener w dowolnym momencie, więc zespół powinien wyrazić zgodę na Konwencję, która będzie zgodna z aktualizacją obrazów modułów przenoszonych do przodu.

Tagi umożliwiają również Wymuszanie aktualizacji na urządzeniach IoT Edge. W przypadku wypychania zaktualizowanej wersji modułu do rejestru kontenerów, należy zwiększyć tag. Następnie należy wypchnąć nowe wdrożenie na urządzenia przy użyciu znacznika o zwiększonym obroście. Aparat kontenerów rozpozna zwiększony tag jako nową wersję i pobierze najnowszą wersję modułu do urządzenia.

#### <a name="tags-for-the-iot-edge-runtime"></a>Tagi środowiska uruchomieniowego IoT Edge

Obraz IoT Edge i centrum IoT Edge są oznaczone za pomocą IoT Edge wersji, z którą są skojarzone. Istnieją dwa różne sposoby używania tagów z obrazami środowiska uruchomieniowego:

* **Tagi walcowe** — Użyj tylko dwóch pierwszych wartości numeru wersji, aby uzyskać najnowszy obraz, który jest zgodny z tymi cyframi. Na przykład aktualizacja 1,1 jest aktualizowana za każdym razem, gdy istnieje nowe wydanie wskazujące najnowszą wersję 1.1. x. Jeśli środowisko uruchomieniowe kontenera na urządzeniu IoT Edge ponownie pobierze obraz, moduły środowiska uruchomieniowego zostaną zaktualizowane do najnowszej wersji. Wdrożenia z Azure Portal domyślne do stopniowego znacznika. *To podejście jest sugerowane w celach deweloperskich.*

* **Określone Tagi** — Użyj wszystkich trzech wartości numeru wersji, aby jawnie ustawić wersję obrazu. Na przykład 1.1.0 nie zmieni się po początkowej wersji. W manifeście wdrożenia można zadeklarować nowy numer wersji, gdy wszystko jest gotowe do aktualizacji. *To podejście jest sugerowane w celach produkcyjnych.*

### <a name="store-runtime-containers-in-your-private-registry"></a>Przechowuj kontenery środowiska uruchomieniowego w rejestrze prywatnym

Wiesz już, jak przechowywać obrazy kontenerów dla niestandardowych modułów kodu w prywatnym rejestrze platformy Azure, ale można go również używać do przechowywania publicznych obrazów kontenerów, takich jak dla modułów środowiska uruchomieniowego edgeAgent i edgHub. Może to być konieczne, jeśli istnieją bardzo ścisłe ograniczenia zapory, ponieważ te kontenery środowiska uruchomieniowego są przechowywane w Container Registry firmy Microsoft (MCR).

Uzyskaj obrazy z poleceniem "Docker pull", które ma zostać umieszczone w rejestrze prywatnym. Należy pamiętać, że konieczne będzie zaktualizowanie obrazów przy użyciu każdej nowej wersji środowiska uruchomieniowego IoT Edge.

| Kontener środowiska uruchomieniowego IoT Edge | Polecenie Docker pull |
| --- | --- |
| [Agent Azure IoT Edge](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge Hub](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

Następnie należy zaktualizować odwołania do obrazu w deployment.template.jspliku dla modułów systemowych edgeAgent i edgeHub. Zastąp ciąg `mcr.microsoft.com` nazwą rejestru i serwerem dla obu modułów.

* edgeAgent:

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* edgeHub:

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>Sieć

* **Pomaga**
  * Przejrzyj konfigurację wychodzącą/przychodzącą
  * Zezwalaj na połączenia z urządzeń IoT Edge
  * Konfigurowanie komunikacji za pomocą serwera proxy

### <a name="review-outboundinbound-configuration"></a>Przejrzyj konfigurację wychodzącą/przychodzącą

Kanały komunikacyjne między usługą Azure IoT Hub i IoT Edge są zawsze skonfigurowane jako wychodzące. W przypadku większości scenariuszy IoT Edge niezbędne są tylko trzy połączenia. Aparat kontenerów musi nawiązać połączenie z rejestrem kontenerów (rejestrem) zawierającym obrazy modułu. Środowisko uruchomieniowe IoT Edge musi nawiązać połączenie z usługą IoT Hub w celu pobrania informacji o konfiguracji urządzenia oraz wysyłania komunikatów i danych telemetrycznych. Jeśli używasz automatycznej aprowizacji, IoT Edge musi nawiązać połączenie z usługą Device Provisioning. Aby uzyskać więcej informacji, zobacz [reguły konfiguracji zapory i portów](troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="allow-connections-from-iot-edge-devices"></a>Zezwalaj na połączenia z urządzeń IoT Edge

Jeśli konfiguracja sieci wymaga jawnie zezwolenia na połączenia wykonane z urządzeń IoT Edge, Przejrzyj następującą listę składników IoT Edge:

* **Agent IoT Edge** otwiera trwałe połączenie AMQP/MQTT z IoT Hub, prawdopodobnie za pośrednictwem usługi WebSockets.
* **IoT Edge Hub** otwiera pojedyncze trwałe połączenie AMQP lub wiele połączeń MQTT do IoT Hub, które prawdopodobnie przekraczają usługi WebSockets.
* **Usługa IoT Edge** powoduje sporadyczne wywołania HTTPS do IoT Hub.

We wszystkich trzech przypadkach nazwa DNS byłaby zgodna ze wzorcem \* . Azure-Devices.NET.

Dodatkowo **aparat kontenerów** wykonuje wywołania rejestrów kontenerów za pośrednictwem protokołu HTTPS. Aby można było pobrać obrazy kontenerów środowiska uruchomieniowego IoT Edge, nazwa DNS to mcr.microsoft.com. Aparat kontenerów nawiązuje połączenie z innymi rejestrami zgodnie z konfiguracją we wdrożeniu.

Ta lista kontrolna jest punktem początkowym dla reguł zapory:

   | URL ( \* = symbol wieloznaczny) | Wychodzące porty TCP | Użycie |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Container Registry firmy Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Dostęp do punktu dystrybucji (opcjonalnie) |
   | \*. azurecr.io | 443 | Rejestry kontenerów osobistych i innych firm |
   | \*.blob.core.windows.net | 443 | Pobieranie Azure Container Registry różnic obrazu z magazynu obiektów BLOB |
   | \*. azure-devices.net | 5671, 8883, 443 | Dostęp IoT Hub |
   | \*. docker.io  | 443 | Dostęp do centrum platformy Docker (opcjonalnie) |

Niektóre z tych reguł zapory są dziedziczone z Azure Container Registry. Aby uzyskać więcej informacji, zobacz [Konfigurowanie reguł dostępu do usługi Azure Container Registry za zaporą](../container-registry/container-registry-firewall-access-rules.md).

> [!NOTE]
> Aby zapewnić spójną nazwę FQDN między punktami końcowymi REST i Data, począwszy od **15 czerwca 2020** , punkt końcowy danych Microsoft Container Registry zmieni się z `*.cdn.mscr.io` na `*.data.mcr.microsoft.com`  
> Aby uzyskać więcej informacji, zobacz [Konfiguracja reguł zapory klienta firmy Microsoft Container Registry](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)

Jeśli nie chcesz konfigurować zapory w taki sposób, aby zezwalała na dostęp do rejestrów publicznych kontenerów, możesz przechowywać obrazy w rejestrze prywatnych kontenerów, jak opisano w [kontenerach środowiska uruchomieniowego magazynu w rejestrze prywatnym](#store-runtime-containers-in-your-private-registry).

### <a name="configure-communication-through-a-proxy"></a>Konfigurowanie komunikacji za pomocą serwera proxy

Jeśli urządzenia zostaną wdrożone w sieci, w której jest używany serwer proxy, muszą być w stanie komunikować się za pośrednictwem serwera proxy w celu uzyskania dostępu do IoT Hub i rejestrów kontenerów. Aby uzyskać więcej informacji, zobacz [Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Zarządzanie rozwiązaniami

* **Pomaga**
  * Konfigurowanie dzienników i diagnostyki
  * Rozważ użycie testów i potoków ciągłej integracji/ciągłego wdrażania

### <a name="set-up-logs-and-diagnostics"></a>Konfigurowanie dzienników i diagnostyki

W systemie Linux demon IoT Edge używa dzienników jako domyślnego sterownika rejestrowania. Za pomocą narzędzia wiersza polecenia można `journalctl` wykonywać zapytania dotyczące dzienników demona.

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

Począwszy od wersji 1,2, IoT Edge opiera się na wielu demonach. Podczas gdy dzienniki poszczególnych demonów mogą być indywidualnie zapytania `journalctl` , `iotedge system` polecenia zapewniają wygodny sposób wykonywania zapytań dotyczących połączonych dzienników.

* Skonsolidowane `iotedge` polecenie:

  ```bash
  sudo iotedge system logs
  ```

* Równoważne `journalctl` polecenie:

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

W systemie Windows demon IoT Edge używa diagnostyki programu PowerShell. Służy `Get-IoTEdgeLog` do wykonywania zapytań dotyczących dzienników z demona. Moduły IoT Edge używają sterownika JSON do rejestrowania, co jest ustawieniem domyślnym.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

W przypadku testowania wdrożenia IoT Edge można zazwyczaj uzyskać dostęp do urządzeń w celu pobierania dzienników i rozwiązywania problemów. W scenariuszu wdrażania nie można korzystać z tej opcji. Rozważ, jak chcesz zbierać informacje o urządzeniach w środowisku produkcyjnym. Jedną z opcji jest użycie modułu rejestrowania, który zbiera informacje z innych modułów i wysyła je do chmury. Jednym z przykładów modułu rejestrowania jest [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), lub możesz zaprojektować własny.

### <a name="place-limits-on-log-size"></a>Limity umieszczenia rozmiaru dziennika

Domyślnie aparat kontenera Moby nie ustawia limitów rozmiaru dziennika kontenera. W miarę upływu czasu może to prowadzić do wypełniania urządzenia przy użyciu dzienników i uruchamiania miejsca na dysku. Aby tego uniknąć, należy wziąć pod uwagę następujące opcje:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Opcja: Ustaw limity globalne, które mają zastosowanie do wszystkich modułów kontenera

Można ograniczyć rozmiar wszystkich plików dziennika kontenerów w opcjach dziennika aparatu kontenera. W poniższym przykładzie ustawiono sterownik dziennika na `json-file` (zalecane) z limitami rozmiaru i liczby plików:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Dodaj (lub Dołącz) te informacje do pliku o nazwie `daemon.json` i umieść go w odpowiedniej lokalizacji dla platformy urządzeń.

| Platforma | Lokalizacja |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić silnik kontenera.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Opcja: Dopasuj ustawienia dziennika dla każdego modułu kontenera

Można to zrobić w **opcjach** dla każdego modułu. Na przykład:

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

* Skonfiguruj aparat kontenera do wysyłania dzienników do `systemd` [dziennika](https://docs.docker.com/config/containers/logging/journald/) przez ustawienie `journald` domyślnego sterownika rejestrowania.

* Okresowo Usuwaj stare dzienniki z urządzenia, instalując narzędzie logrotate. Użyj następującej specyfikacji pliku:

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

### <a name="consider-tests-and-cicd-pipelines"></a>Rozważ użycie testów i potoków ciągłej integracji/ciągłego wdrażania

Aby zapoznać się z najbardziej wydajnym scenariuszem wdrażania IoT Edge, rozważ integrację wdrożenia produkcyjnego z potokami testowania i ciągłej integracji/ciągłego dostarczania. Azure IoT Edge obsługuje wiele platform ciągłej integracji/ciągłego wdrażania, w tym Azure DevOps. Aby uzyskać więcej informacji, zobacz [ciągła integracja i ciągłe wdrażanie do Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [IoT Edge wdrożeniu automatycznym](module-deployment-monitoring.md).
* Zobacz, jak IoT Edge obsługuje [ciągłą integrację i ciągłe wdrażanie](how-to-continuous-integration-continuous-deployment.md).
