---
title: Azure Monitor metryki według typu zasobu
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/15/2021
ms.author: robb
ms.openlocfilehash: 1091d103428315a065dd1ff9800ce2ad16632df0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600016"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki z Azure Monitor

> [!NOTE]
> Ta lista jest w dużej mierze generowana automatycznie. Wszelkie modyfikacje wprowadzone do tej listy za pośrednictwem usługi GitHub mogą zostać zapisane bez ostrzeżenia. Aby uzyskać szczegółowe informacje na temat tworzenia trwałych aktualizacji, skontaktuj się z autorem tego artykułu.

Azure Monitor udostępnia kilka sposobów interakcji z metrykami, w tym wykresy w portalu, uzyskiwanie do nich dostępu za pośrednictwem interfejsu API REST lub wykonywanie zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. 

Ten artykuł zawiera pełną listę wszystkich metryk platformy (czyli automatycznie zbieranych) dostępnych obecnie w Azure Monitor potoku metryk skonsolidowanych. Metryki zmienione lub dodane po dacie w górnej części tego artykułu mogą jeszcze nie być wyświetlane poniżej. Aby programowo odpytować listę metryk i uzyskać do nich dostęp, użyj wersji [interfejsu API 2018-01-01.](/rest/api/monitor/metricdefinitions) Inne metryki, których nie ma na tej liście, mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

Metryki są zorganizowane według dostawców zasobów i typu zasobu. Aby uzyskać listę usług oraz dostawców zasobów i typów, które do nich należą, zobacz [Dostawcy zasobów dla usług platformy Azure.](../../azure-resource-manager/management/azure-services-resource-providers.md)  

## <a name="exporting-platform-metrics-to-other-locations"></a>Eksportowanie metryk platformy do innych lokalizacji

Metryki platformy można wyeksportować z potoku usługi Azure Monitor do innych lokalizacji na jeden z dwóch sposobów.
1. Korzystanie z [interfejsu API REST metryk](/rest/api/monitor/metrics/list)
2. Używanie [ustawień diagnostyki do](../essentials/diagnostic-settings.md) przekierowywu metryk platformy do 
    - Azure Storage
    - Azure Monitor dzienników (a tym samym usługi Log Analytics)
    - Usługa Event Hubs— w ten sposób można je pobrać do systemów innych niż firmy Microsoft 

Użycie ustawień diagnostycznych jest najprostszym sposobem na trasę metryk, ale istnieją pewne ograniczenia: 

- **Niektóre metryki** nie można eksportować — wszystkie metryki można eksportować przy użyciu interfejsu API REST, ale niektórych nie można eksportować przy użyciu ustawień diagnostycznych z powodu zawiłości Azure Monitor zaplecza. Kolumna *Eksportowalne za pośrednictwem ustawień diagnostycznych* w poniższych tabelach zawiera listę metryk, które można eksportować w ten sposób.  

- **Metryki wielowymiarowe —** wysyłanie metryk wielowymiarowych do innych lokalizacji za pośrednictwem ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach. *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.

## <a name="guest-os-and-host-os-metrics"></a>Metryki systemu operacyjnego gościa i systemu operacyjnego hosta

> [!WARNING]
> Metryki dla systemu operacyjnego gościa (systemu operacyjnego gościa), który działa w usługach Azure Virtual Machines, Service Fabric i Cloud Services **NIE** są wymienione w tym miejscu. Metryki systemu operacyjnego gościa muszą być zbierane za pośrednictwem jednego lub większej liczby agentów, którzy są uruchamiani w systemie operacyjnym gościa lub w jego ramach.  Metryki systemu operacyjnego gościa obejmują liczniki wydajności, które śledzą procent użycia procesora CPU gościa lub pamięci, z których oba są często używane do automatycznego skalowania lub alertów. 
>
> **Metryki systemu operacyjnego hosta SĄ dostępne i wymienione poniżej.** Nie są takie same. Metryki systemu operacyjnego hosta odnoszą się do sesji funkcji Hyper-V hostowania sesji systemu operacyjnego gościa. 

> [!TIP]
> Najlepszym rozwiązaniem jest użycie [](../agents/diagnostics-extension-overview.md) i skonfigurowanie rozszerzenia Diagnostyka Azure w celu wysyłania metryk wydajności systemu operacyjnego gościa do tej samej bazy danych metryk Azure Monitor, w której są przechowywane metryki platformy. Rozszerzenie kieruje metryki systemu operacyjnego gościa za pośrednictwem [interfejsu](../essentials/metrics-custom-overview.md) API metryk niestandardowych. Następnie możesz utworzyć wykres, alerty i w inny sposób użyć metryk systemu operacyjnego gościa, takich jak metryki platformy. Alternatywnie lub dodatkowo możesz użyć agenta usługi Log Analytics do wysyłania metryk systemu operacyjnego gościa do usługi Azure Monitor Logs/Log Analytics. Istnieje możliwość wykonywania zapytań dotyczących tych metryk w połączeniu z danymi innymi niż metryki. 

Aby uzyskać ważne informacje dodatkowe, zobacz [Omówienie agentów monitorowania](../agents/agents-overview.md).

## <a name="table-formatting"></a>Formatowanie tabeli

> [!IMPORTANT] 
> Ta najnowsza aktualizacja dodaje nową kolumnę i zmienia kolejność metryk na alfabetyczną. Dodatkowe informacje oznaczają, że poniższe tabele mogą mieć poziomy pasek przewijania u dołu, w zależności od szerokości okna przeglądarki. Jeśli uważasz, że brakuje informacji, użyj paska przewijania, aby wyświetlić całą tabelę.
## <a name="microsoftaadiamazureadmetrics"></a>microsoft.a miliam/azureADMetrics

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ThrottledRequests|Nie|ThrottledRequests|Liczba|Średnia|metryka typu azureADMetrics|Brak wymiarów|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Tak|Pamięć: Czyszcząc bieżącą cenę|Liczba|Średnia|Bieżąca cena pamięci, $/byte/time, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Tak|Pamięć: pamięć czyszcząca nieoczyszczalna|Bajty|Średnia|Ilość pamięci w bajtach, która nie może być czyszcząca przez czyszczenie w tle.|ServerResourceType|
|CleanerMemoryShrinkable|Tak|Pamięć: pamięć czyszcząca jest zmniejszana|Bajty|Średnia|Ilość pamięci w bajtach może być przeczyszczana przez czyszczenie w tle.|ServerResourceType|
|CommandPoolBusyThreads|Tak|Wątki: zajęte wątki puli poleceń|Liczba|Średnia|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Tak|Wątki: bezczynne wątki puli poleceń|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolJobQueueLength|Tak|Długość kolejki zadania puli poleceń|Liczba|Średnia|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|CurrentConnections (Bieżące połączenia)|Tak|Połączenie: Bieżące połączenia|Liczba|Średnia|Bieżąca liczba ustanowionych połączeń klienckich.|ServerResourceType|
|CurrentUserSessions|Tak|Bieżące sesje użytkowników|Liczba|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|LongParsingBusyThreads|Tak|Wątki: długie analizowanie zajętych wątków|Liczba|Średnia|Liczba zajętych wątków w długiej puli wątków analizy.|ServerResourceType|
|LongParsingIdleThreads|Tak|Wątki: długie analizowanie bezczynnych wątków|Liczba|Średnia|Liczba bezczynnych wątków w długiej puli wątków analizy.|ServerResourceType|
|LongParsingJobQueueLength|Tak|Wątki: długie analizowanie długości kolejki zadań|Liczba|Średnia|Liczba zadań w kolejce długiej puli wątków analizy.|ServerResourceType|
|mashup_engine_memory_metric|Tak|Pamięć aparatu M|Bajty|Średnia|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_private_bytes_metric|Tak|Bajty prywatne aparatu M|Bajty|Średnia|Użycie bajtów prywatnych przez procesy aparatu mashupów.|ServerResourceType|
|mashup_engine_qpu_metric|Tak|M Engine QPU|Liczba|Średnia|Użycie QPU przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Tak|Bajty wirtualne aparatu M|Bajty|Średnia|Użycie bajtów wirtualnych przez procesy aparatu mashupów.|ServerResourceType|
|memory_metric|Tak|Pamięć|Bajty|Średnia|Memory (pamięć). Zakres 0–25 GB dla S1, 0–50 GB dla S2 i 0–100 GB dla S4|ServerResourceType|
|memory_thrashing_metric|Tak|Przeładowywanie pamięci|Procent|Średnia|Średnie przerzucanie pamięci.|ServerResourceType|
|MemoryLimitHard|Tak|Pamięć: limit pamięci twardy|Bajty|Średnia|Limit pamięci twardej z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Tak|Pamięć: wysoki limit pamięci|Bajty|Średnia|Wysoki limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Tak|Pamięć: niski limit pamięci|Bajty|Średnia|Limit małej ilości pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Tak|Pamięć: Limit pamięci VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|Użycie pamięci|Tak|Pamięć: użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera używane do obliczania ceny pamięci czyszczenia. Równa licznikowi Process\PrivateBytes plus rozmiar danych mapowanych w pamięci, ignorując dowolną pamięć, która została zamapowana lub przydzielona przez aparat analizy w pamięci xVelocity (VertiPaq) w porównaniu z limitem pamięci aparatu xVelocity.|ServerResourceType|
|private_bytes_metric|Tak|Bajty prywatne|Bajty|Średnia|Bajty prywatne.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Tak|Wątki: przetwarzanie wątków zadań we/wy zajętej puli|Liczba|Średnia|Liczba wątków z uruchomionymi zadaniami We/Wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Tak|Wątki: pula przetwarzana jest zajęta wątkami nie we/wy|Liczba|Średnia|Liczba wątków, w których są uruchomione zadania inne niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Tak|Wątki: przetwarzanie wątków zadań we/wy bezczynnych puli|Liczba|Średnia|Liczba bezczynnych wątków dla zadań We/Wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Tak|Wątki: przetwarzanie wątków bezczynnych puli innych niż we/wy|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania przeznaczonych dla zadań innych niż we/wy.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Tak|Wątki: długość kolejki zadań we/wy puli|Liczba|Średnia|Liczba zadań We/Wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolJobQueueLength|Tak|Długość kolejki zadania puli przetwarzania|Liczba|Średnia|Liczba zadań innych niż We/Wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|qpu_metric|Tak|Funkcja QPU|Liczba|Średnia|Funkcja QPU. Zakres od 0 do 100 dla S1, 0–200 dla S2 i 0–400 dla S4|ServerResourceType|
|QueryPoolBusyThreads|Tak|Zajęte wątki puli zapytań|Liczba|Średnia|Liczba zajętych wątków w puli wątków zapytania.|ServerResourceType|
|QueryPoolIdleThreads|Tak|Wątki: bezczynne wątki puli zapytań|Liczba|Średnia|Liczba bezczynnych wątków dla zadań We/Wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Tak|Wątki: lengt kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytania.|ServerResourceType|
|limit przydziału|Tak|Pamięć: limit przydziału|Bajty|Średnia|Bieżący limit przydziału pamięci w bajtach. Limit przydziału pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|ServerResourceType|
|QuotaBlocked|Tak|Pamięć: zablokowano limit przydziału|Liczba|Średnia|Bieżąca liczba żądań przydziałów, które są blokowane do momentu, gdy zostaną wolne inne limity przydziału pamięci.|ServerResourceType|
|RowsConvertedPerSec|Tak|Przetwarzanie: wiersze przekonwertowane na sekundę|CountPerSecond|Średnia|Szybkość wierszy konwertowanych podczas przetwarzania.|ServerResourceType|
|RowsReadPerSec|Tak|Przetwarzanie: liczba wierszy do odczytu na sekundę|CountPerSecond|Średnia|Szybkość odczytywania wierszy ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsWrittenPerSec|Tak|Przetwarzanie: wiersze zapisywane na sekundę|CountPerSecond|Średnia|Szybkość wierszy zapisywanych podczas przetwarzania.|ServerResourceType|
|ShortParsingBusyThreads|Tak|Wątki: krótkie analizowanie zajętych wątków|Liczba|Średnia|Liczba zajętych wątków w krótkiej puli wątków analizy.|ServerResourceType|
|ShortParsingIdleThreads|Tak|Wątki: krótkie analizowanie bezczynnych wątków|Liczba|Średnia|Liczba bezczynnych wątków w krótkiej puli wątków analizy.|ServerResourceType|
|ShortParsingJobQueueLength|Tak|Wątki: krótka długość kolejki zadań analizy|Liczba|Średnia|Liczba zadań w kolejce krótkiej puli wątków analizy.|ServerResourceType|
|SuccessfullConnectionsPerSec|Tak|Pomyślne połączenia na sekundę|CountPerSecond|Średnia|Szybkość pomyślnego ukończenia połączenia.|ServerResourceType|
|TotalConnectionFailures|Tak|Łączna liczba niepowodzeń połączeń|Liczba|Średnia|Łączna liczba nieudanych prób połączenia.|ServerResourceType|
|TotalConnectionRequests|Tak|Łączna liczba żądań połączeń|Liczba|Średnia|Łączna liczba żądań połączeń. Są to przyloty.|ServerResourceType|
|VertiPaqNonpaged|Tak|Pamięć: VertiPaq niestronicowane|Bajty|Średnia|Bajty pamięci zablokowanej w zestawie roboczym do użytku przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Tak|Pamięć: VertiPaq Stronicowane|Bajty|Średnia|Bajty stronicowane pamięci w użyciu dla danych w pamięci.|ServerResourceType|
|virtual_bytes_metric|Tak|Bajty wirtualne|Bajty|Średnia|Bajty wirtualne.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BackendDuration|Tak|Czas trwania żądań zaplecza|Milisekund|Średnia|Czas trwania żądań zaplecza w milisekundach|Lokalizacja, nazwa hosta|
|Pojemność|Tak|Pojemność|Procent|Średnia|Metryka wykorzystania dla usługi ApiManagement|Lokalizacja|
|Czas trwania|Tak|Ogólny czas trwania żądań bramy|Milisekund|Średnia|Ogólny czas trwania żądań bramy w milisekundach|Lokalizacja, nazwa hosta|
|EventHubDroppedEvents|Tak|Porzucone zdarzenia EventHub|Liczba|Łącznie|Liczba zdarzeń pominiętych z powodu osiągniętego limitu rozmiaru kolejki|Lokalizacja|
|EventHubRejectedEvents|Tak|Odrzucone zdarzenia EventHub|Liczba|Łącznie|Liczba odrzuconych zdarzeń usługi EventHub (nieprawidłowa konfiguracja lub brak autoryzacji)|Lokalizacja|
|EventHubSuccessfulEvents|Tak|Pomyślne zdarzenia EventHub|Liczba|Łącznie|Liczba pomyślnych zdarzeń usługi EventHub|Lokalizacja|
|EventHubThrottledEvents|Tak|Zdarzenia EventHub z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń EventHub z ograniczeniami|Lokalizacja|
|EventHubTimedoutEvents|Tak|Zdarzenia EventHub z przebyciem czasu|Liczba|Łącznie|Liczba zdarzeń EventHub z przekierowywem czasu|Lokalizacja|
|EventHubTotalBytesSent|Tak|Rozmiar zdarzeń EventHub|Bajty|Łącznie|Łączny rozmiar zdarzeń eventHub w bajtach|Lokalizacja|
|EventHubTotalEvents|Tak|Łączna liczba zdarzeń eventHub|Liczba|Łącznie|Liczba zdarzeń wysłanych do usługi EventHub|Lokalizacja|
|EventHubTotalFailedEvents|Tak|Zdarzenia EventHub, które zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba zdarzeń EventHub, które zakończyły się niepowodzeniem|Lokalizacja|
|FailedRequests|Tak|Żądania bramy, które zakończyły się niepowodzeniem (przestarzałe)|Liczba|Łącznie|Liczba błędów w żądaniach bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|Łączność sieciowa|Tak|Stan łączności sieciowej zasobów (wersja zapoznawcza)|Liczba|Średnia|Stan łączności sieciowej zależnych typów zasobów z API Management service|Location, ResourceType|
|OtherRequests|Tak|Inne żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba innych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|Żądania|Tak|Żądania|Liczba|Łącznie|Metryki żądań bramy o wielu wymiarach|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Tak|Żądania bramy pomyślne (przestarzałe)|Liczba|Łącznie|Liczba pomyślnych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|TotalRequests|Tak|Łączna liczba żądań bramy (przestarzałe)|Liczba|Łącznie|Liczba żądań bramy — zamiast tego użyj metryki żądania o wielu wymiarach z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|UnauthorizedRequests|Tak|Nieautoryzowane żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba nieautoryzowanych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Tak|HttpIncomingRequestCount|Liczba|Liczba|Łączna liczba przychodzących żądań HTTP.|StatusCode, Authentication|
|HttpIncomingRequestDuration|Tak|HttpIncomingRequestDuration|Liczba|Średnia|Opóźnienie w żądaniu HTTP.|StatusCode, Authentication|
|ThrottledHttpRequestCount|Tak|ThrottledHttpRequestCount|Liczba|Liczba|Żądania HTTP z ograniczeniami.|Brak wymiarów|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|liczba aktywnych czasomierzy|Tak|liczba aktywnych czasomierzy|Liczba|Średnia|Liczba czasomierzy, które są obecnie aktywne|Wdrożenie, AppName, Zasobnik|
|alloc-rate|Tak|alloc-rate|Bajty|Średnia|Liczba bajtów przydzielonych w zarządzanym stosie|Wdrożenie, AppName, Zasobnik|
|AppCpuUsage|Tak|Użycie procesora CPU przez aplikację |Procent|Średnia|Ostatnie użycie procesora CPU dla aplikacji|Wdrożenie, AppName, Zasobnik|
|liczba zestawu|Tak|liczba zestawu|Liczba|Średnia|Liczba załadowanych zestawów|Wdrożenie, AppName, Zasobnik|
|użycie procesora CPU|Tak|użycie procesora CPU|Procent|Średnia|czas wykorzystania procesora CPU przez proces|Wdrożenie, AppName, Zasobnik|
|bieżące żądania|Tak|bieżące żądania|Liczba|Średnia|Łączna liczba żądań przetwarzanych w okresie istnienia procesu|Wdrożenie, AppName, Zasobnik|
|liczba wyjątków|Tak|liczba wyjątków|Liczba|Łącznie|Liczba wyjątków|Wdrożenie, AppName, Zasobnik|
|żądania nieudane|Tak|żądania nieudane|Liczba|Średnia|Łączna liczba żądań, które zakończyły się niepowodzeniem w okresie istnienia procesu|Wdrożenie, AppName, Zasobnik|
|rozmiar sterty gc|Tak|rozmiar sterty gc|Liczba|Średnia|Łączny rozmiar sterty zgłoszony przez wykaz globalny (MB)|Wdrożenie, AppName, Zasobnik|
|gen-0-gc-count|Tak|gen-0-gc-count|Liczba|Średnia|Liczba gcs gen 0|Wdrożenie, AppName, Zasobnik|
|rozmiar gen-0|Tak|rozmiar gen-0|Bajty|Średnia|Rozmiar sterty gen 0|Wdrożenie, AppName, Zasobnik|
|gen-1-gc-count|Tak|gen-1-gc-count|Liczba|Średnia|Liczba gen 1 GCs|Wdrożenie, AppName, Zasobnik|
|rozmiar gen-1|Tak|rozmiar gen-1|Bajty|Średnia|Rozmiar sterty gen 1|Wdrożenie, AppName, Zasobnik|
|gen-2-gc-count|Tak|gen-2-gc-count|Liczba|Średnia|Liczba 2. generacji GC|Wdrożenie, AppName, Zasobnik|
|rozmiar gen-2|Tak|rozmiar gen-2|Bajty|Średnia|Rozmiar sterty gen 2|Wdrożenie, AppName, Zasobnik|
|jvm.gc.live.data.size|Tak|jvm.gc.live.data.size|Bajty|Średnia|Rozmiar puli pamięci starej generacji po pełnym wykazie pamięci|Wdrożenie, AppName, Zasobnik|
|jvm.gc.max.data.size|Tak|jvm.gc.max.data.size|Bajty|Średnia|Maksymalny rozmiar puli pamięci starej generacji|Wdrożenie, AppName, Zasobnik|
|jvm.gc.memory.allocated|Tak|jvm.gc.memory.allocated|Bajty|Maksimum|Zwiększany w celu zwiększenia rozmiaru puli pamięci nowej generacji po jednym gc do przed następnym|Wdrożenie, AppName, Zasobnik|
|jvm.gc.memory.promoted|Tak|jvm.gc.memory.promoted|Bajty|Maksimum|Liczba dodatnich wzrostów rozmiaru starej puli pamięci generacji przed gc do po GC|Wdrożenie, AppName, Zasobnik|
|jvm.gc.pause.total.count|Tak|jvm.gc.pause.total.count|Liczba|Łącznie|Liczba wstrzymywania gc|Wdrożenie, AppName, Zasobnik|
|jvm.gc.pause.total.time|Tak|jvm.gc.pause.total.time|Milisekund|Łącznie|Gc wstrzymać całkowity czas|Wdrożenie, AppName, Zasobnik|
|jvm.memory.committed|Tak|jvm.memory.committed|Bajty|Średnia|Pamięć przypisana do JVM w bajtach|Wdrożenie, AppName, Zasobnik|
|jvm.memory.max|Tak|jvm.memory.max|Bajty|Maksimum|Maksymalna ilość pamięci w bajtach, która może być używana do zarządzania pamięcią|Wdrożenie, AppName, Zasobnik|
|jvm.memory.used|Tak|jvm.memory.used|Bajty|Średnia|Pamięć aplikacji używana w bajtach|Wdrożenie, AppName, Zasobnik|
|rozmiar loh|Tak|rozmiar loh|Bajty|Średnia|Rozmiar sterty LOH|Wdrożenie, AppName, Zasobnik|
|monitor-lock-contention-count|Tak|monitor-lock-contention-count|Liczba|Średnia|Liczba prób podjęcia blokady monitora w przypadku podjęcia kłódki|Wdrożenie, AppName, Zasobnik|
|process.cpu.usage|Tak|process.cpu.usage|Procent|Średnia|Ostatnie użycie procesora CPU dla procesu JVM|Wdrożenie, AppName, Zasobnik|
|żądania na sekundę|Tak|szybkość żądań|Liczba|Średnia|Szybkość żądania|Wdrożenie, AppName, Zasobnik|
|system.cpu.usage|Tak|system.cpu.usage|Procent|Średnia|Ostatnie użycie procesora CPU dla całego systemu|Wdrożenie, AppName, Zasobnik|
|liczba ukończonych elementów puli wątków|Tak|liczba ukończonych elementów puli wątków|Liczba|Średnia|Liczba ukończonych elementów roboczych w puli ThreadPool|Wdrożenie, AppName, Zasobnik|
|threadpool-queue-length|Tak|threadpool-queue-length|Liczba|Średnia|Długość kolejki elementów roboczych ThreadPool|Wdrożenie, AppName, Zasobnik|
|threadpool-thread-count|Tak|threadpool-thread-count|Liczba|Średnia|Liczba wątków ThreadPool|Wdrożenie, AppName, Zasobnik|
|time-in-gc|Tak|time-in-gc|Procent|Średnia|% czasu w wykazie globalny od ostatniego gc|Wdrożenie, AppName, Zasobnik|
|tomcat.global.error|Tak|tomcat.global.error|Liczba|Łącznie|Globalny błąd serwera Tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.global.received|Tak|tomcat.global.received|Bajty|Łącznie|Całkowita liczba odebranych bajtów w programie Tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.global.request.avg.time|Tak|tomcat.global.request.avg.time|Milisekund|Średnia|Średni czas żądania tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.global.request.max|Tak|tomcat.global.request.max|Milisekund|Maksimum|Maksymalny czas żądania tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.global.request.total.count|Tak|tomcat.global.request.total.count|Liczba|Łącznie|Łączna liczba żądań tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.global.request.total.time|Tak|tomcat.global.request.total.time|Milisekund|Łącznie|Tomcat Request Total Time|Wdrożenie, AppName, Zasobnik|
|tomcat.global.sent|Tak|tomcat.global.sent|Bajty|Łącznie|Całkowita liczba wysłanych bajtów w programie Tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.sessions.active.current|Tak|tomcat.sessions.active.current|Liczba|Łącznie|Liczba aktywnych sesji tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.sessions.active.max|Tak|tomcat.sessions.active.max|Liczba|Łącznie|Maksymalna liczba aktywnych sesji tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.sessions.alive.max|Tak|tomcat.sessions.alive.max|Milisekund|Maksimum|Maksymalny czas aktywności sesji tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.sessions.created|Tak|tomcat.sessions.created|Liczba|Łącznie|Liczba utworzonych sesji tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.sessions.expired|Tak|tomcat.sessions.expired|Liczba|Łącznie|Liczba wygasłych sesji tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.sessions.rejected|Tak|tomcat.sessions.rejected|Liczba|Łącznie|Liczba odrzuconych sesji tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.threads.config.max|Tak|tomcat.threads.config.max|Liczba|Łącznie|Maksymalna liczba wątków konfiguracji programu Tomcat|Wdrożenie, AppName, Zasobnik|
|tomcat.threads.current|Tak|tomcat.threads.current|Liczba|Łącznie|Bieżąca liczba wątków w programie Tomcat|Wdrożenie, AppName, Zasobnik|
|łączna liczba żądań|Tak|łączna liczba żądań|Liczba|Średnia|Łączna liczba żądań w okresie istnienia procesu|Wdrożenie, AppName, Zasobnik|
|zestaw roboczy|Tak|zestaw roboczy|Liczba|Średnia|Ilość zestawu roboczego używanego przez proces (MB)|Wdrożenie, AppName, Zasobnik|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|TotalJob|Tak|Łączna liczba zadań|Liczba|Łącznie|Łączna liczba zadań|Runbook, stan|
|TotalUpdateDeploymentMachineRuns|Tak|Łączna liczba przebiegów wdrażania aktualizacji maszyny|Liczba|Łącznie|Łączna liczba przebiegów maszyny wdrażania aktualizacji oprogramowania w uruchomieniu wdrożenia aktualizacji oprogramowania|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Tak|Łączna liczba przebiegów wdrażania aktualizacji|Liczba|Łącznie|Łączna liczba przebiegów wdrażania aktualizacji oprogramowania|SoftwareUpdateConfigurationName, Status|


## <a name="microsoftavsprivateclouds"></a>Microsoft.AVS/privateClouds

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CapacityLatest|Tak|Całkowita pojemność dysku magazynu danych|Bajty|Średnia|Całkowita pojemność dysku w magazynze danych|nazwa dsname|
|DiskUsedPercentage|Tak| Procent użytego dysku magazynu danych|Procent|Średnia|Procent dostępnego dysku używanego w magazynze danych|nazwa dsname|
|EffectiveCpuAverage|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent używanych zasobów procesora CPU w klastrze|nazwa klastra|
|EffectiveMemAverage|Tak|Średnia efektywna pamięć|Bajty|Średnia|Łączna dostępna ilość pamięci komputera w klastrze|nazwa klastra|
|OverheadAverage|Tak|Średni narzut na pamięć|Bajty|Średnia|Pamięć fizyczna hosta zużywana przez infrastrukturę wirtualizacji|nazwa klastra|
|TotalMbAverage|Tak|Średnia łączna pamięć|Bajty|Średnia|Całkowita ilość pamięci w klastrze|nazwa klastra|
|UsageAverage|Tak|Średnie użycie pamięci|Procent|Średnia|Użycie pamięci jako procent całkowitej skonfigurowanej lub dostępnej pamięci|nazwa klastra|
|UsedLatest|Tak|Używany dysk magazynu danych|Bajty|Średnia|Całkowita ilość dysku używanego w magazynze danych|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CoreCount|Nie|Liczba rdzeni dedykowanych|Liczba|Łącznie|Łączna liczba rdzeni dedykowanych na koncie usługi Batch|Brak wymiarów|
|CreatingNodeCount|Nie|Tworzenie liczby węzłów|Liczba|Łącznie|Liczba węzłów, które są tworzone|Brak wymiarów|
|IdleNodeCount|Nie|Liczba bezczynnych węzłów|Liczba|Łącznie|Liczba bezczynnych węzłów|Brak wymiarów|
|JobDeleteCompleteEvent|Tak|Zdarzenia zakończenia usuwania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie usunięte.|Jobid|
|JobDeleteStartEvent|Tak|Zdarzenia rozpoczęcia usuwania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały usunięte.|Jobid|
|JobDisableCompleteEvent|Tak|Zdarzenia zakończenia wyłączania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie wyłączone.|Jobid|
|JobDisableStartEvent|Tak|Zdarzenia uruchamiania wyłączania zadania|Liczba|Łącznie|Łączna liczba zadań, dla których zażądano wyłączenia.|Jobid|
|JobStartEvent|Tak|Zdarzenia uruchamiania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie uruchomione.|Jobid|
|JobTerminateCompleteEvent|Tak|Zdarzenia zakończenia zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie zakończone.|Jobid|
|JobTerminateStartEvent|Tak|Zdarzenia rozpoczęcia zakończenia zadania|Liczba|Łącznie|Łączna liczba zadań, dla których zażądano zakończenia.|Jobid|
|LeavingPoolNodeCount|Nie|Opuszczanie liczby węzłów puli|Liczba|Łącznie|Liczba węzłów opuszczających pulę|Brak wymiarów|
|LowPriorityCoreCount|Nie|Liczba rdzeni o niskiej wartościpriority|Liczba|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie usługi Batch|Brak wymiarów|
|OfflineNodeCount|Nie|Liczba węzłów w trybie offline|Liczba|Łącznie|Liczba węzłów trybu offline|Brak wymiarów|
|PoolCreateEvent|Tak|Zdarzenia tworzenia puli|Liczba|Łącznie|Łączna liczba utworzonych pul|poolId|
|PoolDeleteCompleteEvent|Tak|Zdarzenia ukończenia usuwania puli|Liczba|Łącznie|Łączna liczba zakończonych operacji usuwania puli|poolId|
|PoolDeleteStartEvent|Tak|Zdarzenia rozpoczęcia usuwania puli|Liczba|Łącznie|Łączna liczba rozpoczętych usuwania puli|poolId|
|PoolResizeCompleteEvent|Tak|Zdarzenia zakończenia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba zakończonych operacji zmiany rozmiaru puli|poolId|
|PoolResizeStartEvent|Tak|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba rozpoczętych rozmiarów puli|poolId|
|PreemptedNodeCount|Nie|Liczba wywłaszowanych węzłów|Liczba|Łącznie|Liczba wywłaszowanych węzłów|Brak wymiarów|
|RebootingNodeCount|Nie|Ponowne uruchamianie liczby węzłów|Liczba|Łącznie|Liczba węzłów ponownego uruchamiania|Brak wymiarów|
|ReimagingNodeCount|Nie|Ponowne odtwarzanie liczby węzłów|Liczba|Łącznie|Liczba węzłów ponownego obrazu|Brak wymiarów|
|RunningNodeCount|Nie|Liczba uruchomionych węzłów|Liczba|Łącznie|Liczba uruchomionych węzłów|Brak wymiarów|
|StartingNodeCount|Nie|Początkowa liczba węzłów|Liczba|Łącznie|Liczba węzłów, od których rozpoczyna się|Brak wymiarów|
|StartTaskFailedNodeCount|Nie|Liczba węzłów: niepowodzenie zadania uruchamiania|Liczba|Łącznie|Liczba węzłów, w których zadanie uruchamiania nie powiodło się|Brak wymiarów|
|TaskCompleteEvent|Tak|Zdarzenia ukończenia zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały ukończone|poolId, jobId|
|TaskFailEvent|Tak|Zdarzenia niepowodzenie zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały wykonane w stanie niepowodzeniem|poolId, jobId|
|TaskStartEvent|Tak|Zdarzenia rozpoczęcia zadania|Liczba|Łącznie|Łączna liczba uruchomionych zadań|poolId, jobId|
|TotalLowPriorityNodeCount|Nie|Low-Priority liczby węzłów|Liczba|Łącznie|Łączna liczba węzłów o niskim priorytecie na koncie usługi Batch|Brak wymiarów|
|TotalNodeCount|Nie|Liczba dedykowanych węzłów|Liczba|Łącznie|Łączna liczba dedykowanych węzłów na koncie usługi Batch|Brak wymiarów|
|UnusableNodeCount|Nie|Liczba węzłów bezużytecznych|Liczba|Łącznie|Liczba węzłów bezużytecznych|Brak wymiarów|
|WaitingForStartTaskNodeCount|Nie|Oczekiwanie na liczbę węzłów zadania uruchamiania|Liczba|Łącznie|Liczba węzłów oczekujących na ukończenie zadania podrzędnego uruchamiania|Brak wymiarów|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Rdzenie aktywne|Tak|Rdzenie aktywne|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Aktywne węzły|Tak|Aktywne węzły|Liczba|Średnia|Liczba uruchomionych węzłów|Scenariusz, ClusterName|
|Bezczynne rdzenie|Tak|Bezczynne rdzenie|Liczba|Średnia|Liczba bezczynnych rdzeni|Scenariusz, ClusterName|
|Węzły bezczynne|Tak|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów|Scenariusz, ClusterName|
|Zadanie ukończone|Tak|Zadanie ukończone|Liczba|Łącznie|Liczba ukończonych zadań|Scenario, ClusterName, ResultType|
|Przesłane zadanie|Tak|Przesłane zadanie|Liczba|Łącznie|Liczba przesłanych zadań|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Tak|Opuszczanie rdzeni|Liczba|Średnia|Liczba opuszczających rdzeni|Scenariusz, ClusterName|
|Opuszczanie węzłów|Tak|Opuszczanie węzłów|Liczba|Średnia|Liczba opuszczających węzłów|Scenariusz, ClusterName|
|Wywłaszcze rdzenie|Tak|Wywłaszcze rdzenie|Liczba|Średnia|Liczba wywłaszowanych rdzeni|Scenariusz, ClusterName|
|Wywłaszcze węzły|Tak|Wywłaszcze węzły|Liczba|Średnia|Liczba wywłaszowanych węzłów|Scenariusz, ClusterName|
|Procent wykorzystania limitu przydziału|Tak|Procent wykorzystania limitu przydziału|Liczba|Średnia|Procent wykorzystanego limitu przydziału|Scenario, ClusterName, VmFamilyName, VmPriority|
|Łączna liczba rdzeni|Tak|Łączna liczba rdzeni|Liczba|Średnia|Łączna liczba rdzeni|Scenariusz, ClusterName|
|Łączna liczba węzłów|Tak|Łączna liczba węzłów|Liczba|Średnia|Łączna liczba węzłów|Scenariusz, ClusterName|
|Rdzenie bezużytelne|Tak|Rdzenie bezużytelne|Liczba|Średnia|Liczba niezdatnych do użytku rdzeni|Scenariusz, ClusterName|
|Węzły bezużytelne|Tak|Węzły bezużytelne|Liczba|Średnia|Liczba węzłów, które nie mogą być używane|Scenariusz, ClusterName|

## <a name="microsoftbingaccounts"></a>microsoft.bing/accounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BlockedCalls|Tak|Zablokowane wywołania|Liczba|Łącznie|Liczba wywołań, które przekroczyły limit przydziału lub szybkości|ApiName, ServingRegion, StatusCode|
|ClientErrors|Tak|Błędy klienta|Liczba|Łącznie|Liczba wywołań z dowolnym błędem klienta (kod stanu HTTP 4xx)|ApiName, ServingRegion, StatusCode|
|DataIn|Tak|Dane w|Bajty|Łącznie|Długość zawartości żądania przychodzącego w bajtach|ApiName, ServingRegion, StatusCode|
|DataOut|Tak|Dane wychodzące|Bajty|Łącznie|Długość zawartości odpowiedzi wychodzącej w bajtach|ApiName, ServingRegion, StatusCode|
|Opóźnienie|Tak|Opóźnienie|Milisekund|Średnia|Opóźnienie w milisekundach|ApiName, ServingRegion, StatusCode|
|ServerErrors (ServerErrors)|Tak|Błędy serwera|Liczba|Łącznie|Liczba wywołań z dowolnym błędem serwera (kod stanu HTTP 5xx)|ApiName, ServingRegion, StatusCode|
|SuccessfulCalls|Tak|Pomyślne wywołania|Liczba|Łącznie|Liczba pomyślnych wywołań (kod stanu HTTP 2xx)|ApiName, ServingRegion, StatusCode|
|TotalCalls|Tak|Łączna liczba wywołań|Liczba|Łącznie|Łączna liczba wywołań|ApiName, ServingRegion, StatusCode|
|TotalErrors|Tak|Całkowita liczba błędów|Liczba|Łącznie|Liczba wywołań z dowolnym błędem (kod stanu HTTP 4xx lub 5xx)|ApiName, ServingRegion, StatusCode|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Tak|BroadcastProcessedCountDisplayName|Liczba|Średnia|Liczba przetworzonych transakcji.|Węzeł, kanał, typ, stan|
|Kod łańcuchaExecuteTimeouts|Tak|ChaincodeExecuteTimeoutsDisplayName|Liczba|Średnia|Liczba wykonań kodu łańcuchowego (Init lub Invoke), których uchybnił czas.|Węzeł, kod łańcucha|
|ChaincodeLaunchFailures|Tak|ChaincodeLaunchFailuresDisplayName|Liczba|Średnia|Liczba uruchomień kodu łańcucha, które zakończyły się niepowodzeniem.|Węzeł, kod łańcucha|
|ChaincodeLaunchTimeouts|Tak|ChaincodeLaunchTimeoutsDisplayName|Liczba|Średnia|Liczba uruchomień kodów łańcuchowych, dla których uchybnił czas.|Węzeł, kod łańcucha|
|ChaincodeShimRequestsCompleted|Tak|ChaincodeShimRequestsCompletedDisplayName|Liczba|Średnia|Liczba ukończonych żądań podkładki kodu łańcuchowego.|Węzeł, typ, kanał, kod łańcucha, powodzenie|
|ChaincodeShimRequestsReceived|Tak|ChaincodeShimRequestsReceivedDisplayName|Liczba|Średnia|Liczba odebranych żądań podkładki kodu łańcucha.|Węzeł, typ, kanał, kod łańcucha|
|ClusterCommEgressQueueCapacity|Tak|ClusterCommEgressQueueCapacityDisplayName|Liczba|Średnia|Pojemność kolejki wychodzącej.|Węzeł, host, msg_type, kanał|
|ClusterCommEgressQueueLength|Tak|ClusterCommEgressQueueLengthDisplayName|Liczba|Średnia|Długość kolejki wychodzącej.|Węzeł, host, msg_type, kanał|
|ClusterCommEgressQueueWorkers|Tak|ClusterCommEgressQueueWorkersDisplayName|Liczba|Średnia|Liczba pracowników kolejki wychodzącej.|Węzeł, kanał|
|ClusterCommEgressStreamCount|Tak|ClusterCommEgressStreamCountDisplayName|Liczba|Średnia|Liczba strumieni do innych węzłów.|Węzeł, kanał|
|ClusterCommEgressTlsConnectionCount|Tak|ClusterCommEgressTlsConnectionCountDisplayName|Liczba|Średnia|Liczba połączeń TLS z innymi węzłami.|Węzeł|
|ClusterCommIngressStreamCount|Tak|ClusterCommIngressStreamCountDisplayName|Liczba|Średnia|Liczba strumieni z innych węzłów.|Węzeł|
|ClusterCommMsgDroppedCount|Tak|ClusterCommMsgDroppedCountDisplayName|Liczba|Średnia|Liczba porzuconych komunikatów.|Węzeł, host, kanał|
|ConnectionAccepted|Tak|Zaakceptowane połączenia|Liczba|Łącznie|Zaakceptowane połączenia|Węzeł|
|ConnectionActive|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Węzeł|
|ConnectionHandled|Tak|Obsługiwane połączenia|Liczba|Łącznie|Obsługiwane połączenia|Węzeł|
|ConsensusEtcdraftActiveNodes|Tak|ConsensusEtcdraftActiveNodesDisplayName|Liczba|Średnia|Liczba aktywnych węzłów w tym kanale.|Węzeł, kanał|
|ConsensusEtcdraftClusterSize|Tak|ConsensusEtcdraftClusterSizeDisplayName|Liczba|Średnia|Liczba węzłów w tym kanale.|Węzeł, kanał|
|ConsensusEtcdraftCommittedBlockNumber|Tak|ConsensusEtcdraftCommittedBlockNumberDisplayName|Liczba|Średnia|Numer bloku najnowszego zatwierdzonego bloku.|Węzeł, kanał|
|ConsensusEtcdraftConfigProposalsReceived|Tak|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Liczba|Średnia|Łączna liczba propozycji odebranych dla transakcji typu konfiguracji.|Węzeł, kanał|
|ConsensusEtcdraftIsLeader|Tak|ConsensusEtcdraftIsLeaderDisplayName|Liczba|Średnia|Stan kierownictwa bieżącego węzła: 1, jeśli jest liderem.|Węzeł, kanał|
|ConsensusEtcdraftLeaderChanges|Tak|ConsensusEtcdraftLeaderChangesDisplayName|Liczba|Średnia|Liczba liderów zmienia się od początku procesu.|Węzeł, kanał|
|ConsensusEtcdraftNormalProposalsReceived|Tak|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Liczba|Średnia|Łączna liczba propozycji otrzymanych dla transakcji typu normalnego.|Węzeł, kanał|
|ConsensusEtcdraftProposalFailures|Tak|ConsensusEtcdraftProposalFailuresDisplayName|Liczba|Średnia|Liczba błędów propozycji.|Węzeł, kanał|
|ConsensusEtcdraftSnapshotBlockNumber|Tak|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Liczba|Średnia|Numer bloku najnowszej migawki.|Węzeł, kanał|
|ConsensusKafkaBatchSize|Tak|ConsensusKafkaBatchSizeDisplayName|Liczba|Średnia|Średni rozmiar partii w bajtach wysyłanych do tematów.|Węzeł, temat|
|ConsensusKafkaCompressionRatio|Tak|ConsensusKafkaCompressionRatioDisplayName|Liczba|Średnia|Średni współczynnik kompresji (jako wartość procentowa) dla tematów.|Węzeł, temat|
|ConsensusKafkaIncomingByteRate|Tak|ConsensusKafkaIncomingByteRateDisplayName|Liczba|Średnia|Bajty/sekundę odczytają brokerów.|Node, broker_id|
|ConsensusKafkaLastOffsetPersisted|Tak|ConsensusKafkaLastOffsetPersistedDisplayName|Liczba|Średnia|Przesunięcie określone w metadanych bloku ostatnio zatwierdzonego bloku.|Węzeł, kanał|
|ConsensusKafkaOutgoingByteRate|Tak|ConsensusKafkaOutgoingByteRateDisplayName|Liczba|Średnia|Bajty/sekundę zapisywane w brokerach.|Node, broker_id|
|ConsensusKafkaRecordSendRate|Tak|ConsensusKafkaRecordSendRateDisplayName|Liczba|Średnia|Liczba rekordów na sekundę wysyłanych do tematów.|Węzeł, temat|
|ConsensusKafkaRecordsPerRequest|Tak|ConsensusKafkaRecordsPerRequestDisplayName|Liczba|Średnia|Średnia liczba rekordów wysłanych na żądanie do tematów.|Węzeł, temat|
|ConsensusKafkaRequestLatency|Tak|ConsensusKafkaRequestLatencyDisplayName|Liczba|Średnia|Średnie opóźnienie żądania w ms do brokerów.|Node, broker_id|
|ConsensusKafkaRequestRate|Tak|ConsensusKafkaRequestRateDisplayName|Liczba|Średnia|Żądania/sekundy wysyłane do brokerów.|Node, broker_id|
|ConsensusKafkaRequestSize|Tak|ConsensusKafkaRequestSizeDisplayName|Liczba|Średnia|Średni rozmiar żądania w bajtach do brokerów.|Node, broker_id|
|ConsensusKafkaResponseRate|Tak|ConsensusKafkaResponseRateDisplayName|Liczba|Średnia|Żądania/sekundy wysyłane do brokerów.|Node, broker_id|
|ConsensusKafkaResponseSize|Tak|ConsensusKafkaResponseSizeDisplayName|Liczba|Średnia|Średni rozmiar odpowiedzi w bajtach od brokerów.|Node, broker_id|
|CpuUsagePercentageInDouble|Tak|Procent użycia procesora CPU|Procent|Maksimum|Procent użycia procesora CPU|Węzeł|
|DeliverBlocksSent|Tak|DeliverBlocksSentDisplayName|Liczba|Średnia|Liczba bloków wysłanych przez usługę dostarczania.|Węzeł, kanał, filtrowane, data_type|
|DeliverRequestsCompleted|Tak|DeliverRequestsCompletedDisplayName|Liczba|Średnia|Liczba ukończonych żądań dostarczenia.|Węzeł, kanał, filtrowane, data_type, powodzenie|
|DeliverRequestsReceived|Tak|DeliverRequestsReceivedDisplayName|Liczba|Średnia|Liczba odebranych żądań dostarczenia.|Węzeł, kanał, filtrowane, data_type|
|DeliverStreamsClosed|Tak|DeliverStreamsClosedDisplayName|Liczba|Średnia|Liczba strumieni GRPC, które zostały zamknięte dla usługi dostarczania.|Węzeł|
|DeliverStreamsOpened|Tak|DeliverStreamsOpenedDisplayName|Liczba|Średnia|Liczba strumieni GRPC, które zostały otwarte dla usługi dostarczania.|Węzeł|
|EndorserChaincodeInstantiationFailures|Tak|EndorserChaincodeInstantiationFailuresDisplayName|Liczba|Średnia|Liczba wystąpienia kodów łańcuchowych lub uaktualnień, które zakończyły się niepowodzeniem.|Węzeł, kanał, kod łańcucha|
|EndorserDuplicateTransactionFailures|Tak|EndorserDuplicateTransactionFailuresDisplayName|Liczba|Średnia|Liczba propozycji, które zakończyły się niepowodzeniem z powodu zduplikowanego identyfikatora transakcji.|Węzeł, kanał, kod łańcucha|
|EndorserEndorsementFailures|Tak|EndorserEndorsementFailuresDisplayName|Liczba|Średnia|Liczba nieudanych poręczenia.|Node, channel, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Tak|EndorserProposalAclFailuresDisplayName|Liczba|Średnia|Liczba propozycji, które zakończyły się niepowodzeniem kontroli listy ACL.|Węzeł, kanał, kod łańcucha|
|EndorserProposalSimulationFailures|Tak|EndorserProposalSimulationFailuresDisplayName|Liczba|Średnia|Liczba nieudanych symulacji propozycji.|Węzeł, kanał, kod łańcucha|
|EndorserProposalsReceived|Tak|EndorserProposalsReceivedDisplayName|Liczba|Średnia|Liczba odebranych propozycji.|Węzeł|
|EndorserProposalValidationFailures|Tak|EndorserProposalValidationFailuresDisplayName|Liczba|Średnia|Liczba propozycji, które nie powiodły się podczas wstępnej weryfikacji.|Węzeł|
|EndorserSuccessfulProposals|Tak|EndorserSuccessfulProposalsDisplayName|Liczba|Średnia|Liczba pomyślnych propozycji.|Węzeł|
|Wersja sieci szkieletowej|Tak|FabricVersionDisplayName|Liczba|Średnia|Aktywna wersja sieci szkieletowej.|Węzeł, wersja|
|GossipCommMessagesReceived|Tak|GossipCommMessagesReceivedDisplayName|Liczba|Średnia|Liczba odebranych komunikatów.|Węzeł|
|GossipCommMessagesSent|Tak|GossipCommMessagesSentDisplayName|Liczba|Średnia|Liczba wysłanych komunikatów.|Węzeł|
|GossipCommOverflowCount|Tak|GossipCommOverflowCountDisplayName|Liczba|Średnia|Liczba przepełnień buforu kolejki wychodzącej.|Węzeł|
|GossipLeaderElectionLeader|Tak|GossipLeaderElectionLeaderDisplayName|Liczba|Średnia|Równorzędny jest liderem (1) lub obserwowany (0).|Węzeł, kanał|
|GossipMembershipTotalPeersKnown|Tak|GossipMembershipTotalPeersKnownDisplayName|Liczba|Średnia|Łączna liczba znanych elementów równorzędnych.|Węzeł, kanał|
|GossipPayloadBufferSize|Tak|GossipPayloadBufferSizeDisplayName|Liczba|Średnia|Rozmiar buforu ładunku.|Węzeł, kanał|
|GossipStateHeight|Tak|GossipStateHeightDisplayName|Liczba|Średnia|Bieżąca wysokość rejestru.|Węzeł, kanał|
|GrpcCommConnClosed|Tak|GrpcCommConnClosedDisplayName|Liczba|Średnia|Zamknięte połączenia gRPC. Otwarty minus zamknięty to aktywna liczba połączeń.|Węzeł|
|GrpcCommConnOpened|Tak|GrpcCommConnOpenedDisplayName|Liczba|Średnia|Otwarte połączenia gRPC. Otwarty minus zamknięty to aktywna liczba połączeń.|Węzeł|
|GrpcServerStreamMessagesReceived|Tak|GrpcServerStreamMessagesReceivedDisplayName|Liczba|Średnia|Liczba odebranych komunikatów strumieniowych.|Węzeł, usługa, metoda|
|GrpcServerStreamMessagesSent|Tak|GrpcServerStreamMessagesSentDisplayName|Liczba|Średnia|Liczba wysłanych komunikatów strumieniowych.|Węzeł, usługa, metoda|
|GrpcServerStreamRequestsCompleted|Tak|GrpcServerStreamRequestsCompletedDisplayName|Liczba|Średnia|Liczba ukończonych żądań strumienia.|Węzeł, usługa, metoda, kod|
|GrpcServerUnaryRequestsReceived|Tak|GrpcServerUnaryRequestsReceivedDisplayName|Liczba|Średnia|Liczba odebranych żądań w trybie jednym.|Węzeł, usługa, metoda|
|IOReadBytes|Tak|Bajty odczytu we/wy|Bajty|Łącznie|Bajty odczytu we/wy|Węzeł|
|IOWriteBytes|Tak|Bajty zapisu we/wy|Bajty|Łącznie|Bajty zapisu we/wy|Węzeł|
|LedgerBlockchainHeight|Tak|LedgerBlockchainHeightDisplayName|Liczba|Średnia|Wysokość łańcucha w blokach.|Węzeł, kanał|
|LedgerTransactionCount|Tak|LedgerTransactionCountDisplayName|Liczba|Średnia|Liczba przetworzonych transakcji.|Node, channel, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Tak|LoggingEntriesCheckedDisplayName|Liczba|Średnia|Liczba wpisów dziennika sprawdzonych względem aktywnego poziomu rejestrowania.|Węzeł, poziom|
|LoggingEntriesWritten|Tak|LoggingEntriesWrittenDisplayName|Liczba|Średnia|Liczba zapisywanych wpisów dziennika.|Węzeł, poziom|
|Memorylimit|Tak|Limit pamięci|Bajty|Średnia|Limit pamięci|Węzeł|
|MemoryUsage (Użycie pamięci)|Tak|Użycie pamięci|Bajty|Średnia|Użycie pamięci|Węzeł|
|MemoryUsagePercentageInDouble|Tak|Procent użycia pamięci|Procent|Średnia|Procent użycia pamięci|Węzeł|
|PendingTransactions|Tak|Oczekujące transakcje|Liczba|Średnia|Oczekujące transakcje|Węzeł|
|ProcessedBlocks|Tak|Przetworzone bloki|Liczba|Łącznie|Przetworzone bloki|Węzeł|
|ProcessedTransactions|Tak|Przetworzone transakcje|Liczba|Łącznie|Przetworzone transakcje|Węzeł|
|QueuedTransactions|Tak|Transakcje w kolejce|Liczba|Średnia|Transakcje w kolejce|Węzeł|
|RequestHandled|Tak|Obsługiwane żądania|Liczba|Łącznie|Obsługiwane żądania|Węzeł|
|StorageUsage|Tak|Użycie magazynu|Bajty|Średnia|Użycie magazynu|Węzeł|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|RequestLatency|Tak|Opóźnienie żądania|Milisekund|Łącznie|Czas przetwarzania żądania przez serwer|Operacja, uwierzytelnianie, protokół, centrum danych|
|RequestsTraffic|Tak|Żądania ruchu|Procent|Liczba|Liczba wykonanych żądań|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|allcachehits|Tak|Trafienia pamięci podręcznej (na podstawie wystąpienia)|Liczba|Łącznie||ShardId, Port, Primary|
|allcachemisses|Tak|Chybienia pamięci podręcznej (na podstawie wystąpienia)|Liczba|Łącznie||ShardId, Port, Primary|
|allcacheRead|Tak|Odczyt pamięci podręcznej (oparty na wystąpieniu)|BytesPerSecond|Maksimum||ShardId, Port, Primary|
|allcacheWrite|Tak|Zapis w pamięci podręcznej (oparty na wystąpieniu)|BytesPerSecond|Maksimum||ShardId, Port, Primary|
|allconnectedclients|Tak|Połączeni klienci (oparte na wystąpieniach)|Liczba|Maksimum||ShardId, Port, Primary|
|allevictedkeys|Tak|Eksmitowane klucze (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, Port, Primary|
|allexpiredkeys|Tak|Wygasłe klucze (oparte na wystąpieniu)|Liczba|Łącznie||ShardId, Port, Primary|
|allgetcommands|Tak|Pobiera (na podstawie wystąpienia)|Liczba|Łącznie||ShardId, Port, Primary|
|alloperationsPerSecond|Tak|Operacje na sekundę (oparte na wystąpieniu)|Liczba|Maksimum||ShardId, Port, Primary|
|allserverLoad|Tak|Obciążenie serwera (oparte na wystąpieniu)|Procent|Maksimum||ShardId, Port, Primary|
|allsetcommands|Tak|Zestawy (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, Port, Primary|
|alltotalcommandsprocessed|Tak|Łączna liczba operacji (na podstawie wystąpienia)|Liczba|Łącznie||ShardId, Port, Primary|
|alltotalkeys|Tak|Łączna liczba kluczy (oparta na wystąpieniu)|Liczba|Maksimum||ShardId, Port, Primary|
|allusedmemory|Tak|Używana pamięć (oparta na wystąpieniu)|Bajty|Maksimum||ShardId, Port, Primary|
|allusedmemorypercentage|Tak|Procent użytej pamięci (na podstawie wystąpienia)|Procent|Maksimum||ShardId, Port, Primary|
|allusedmemoryRss|Tak|Funkcja RSS używanej pamięci (oparta na wystąpieniu)|Bajty|Maksimum||ShardId, Port, Primary|
|cachehits|Tak|Trafienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachehits0|Tak|Trafienia pamięci podręcznej (fragment 0)|Liczba|Łącznie||Brak wymiarów|
|cachehits1|Tak|Trafienia pamięci podręcznej (fragment 1)|Liczba|Łącznie||Brak wymiarów|
|cachehits2|Tak|Trafienia pamięci podręcznej (fragment 2)|Liczba|Łącznie||Brak wymiarów|
|cachehits3|Tak|Trafienia pamięci podręcznej (fragment 3)|Liczba|Łącznie||Brak wymiarów|
|cachehits4|Tak|Trafienia pamięci podręcznej (fragment 4)|Liczba|Łącznie||Brak wymiarów|
|cachehits5|Tak|Trafienia pamięci podręcznej (fragment 5)|Liczba|Łącznie||Brak wymiarów|
|cachehits6|Tak|Trafienia pamięci podręcznej (fragment 6)|Liczba|Łącznie||Brak wymiarów|
|cachehits7|Tak|Trafienia pamięci podręcznej (fragment 7)|Liczba|Łącznie||Brak wymiarów|
|cachehits8|Tak|Trafienia pamięci podręcznej (fragment 8)|Liczba|Łącznie||Brak wymiarów|
|cachehits9|Tak|Trafienia pamięci podręcznej (fragment 9)|Liczba|Łącznie||Brak wymiarów|
|cacheLatency|Tak|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Liczba|Średnia||ShardId|
|cachemisses (pamięć podręczna)|Tak|Chybienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachemisses0|Tak|Chybienia pamięci podręcznej (fragment 0)|Liczba|Łącznie||Brak wymiarów|
|cachemisses1|Tak|Chybienia pamięci podręcznej (fragment 1)|Liczba|Łącznie||Brak wymiarów|
|cachemisses2|Tak|Chybienia pamięci podręcznej (fragment 2)|Liczba|Łącznie||Brak wymiarów|
|cachemisses3|Tak|Chybienia pamięci podręcznej (fragment 3)|Liczba|Łącznie||Brak wymiarów|
|cachemisses4|Tak|Chybienia pamięci podręcznej (fragment 4)|Liczba|Łącznie||Brak wymiarów|
|cachemisses5|Tak|Chybienia pamięci podręcznej (fragment 5)|Liczba|Łącznie||Brak wymiarów|
|cachemisses6|Tak|Chybienia pamięci podręcznej (fragment 6)|Liczba|Łącznie||Brak wymiarów|
|cachemisses7|Tak|Chybienia pamięci podręcznej (fragment 7)|Liczba|Łącznie||Brak wymiarów|
|cachemisses8|Tak|Chybienia pamięci podręcznej (fragment 8)|Liczba|Łącznie||Brak wymiarów|
|cachemisses9|Tak|Chybienia pamięci podręcznej (fragment 9)|Liczba|Łącznie||Brak wymiarów|
|cachemissrate|Tak|Wskaźnik chybień pamięci podręcznej|Procent|cachemissrate||ShardId|
|cacheRead|Tak|Odczyt pamięci podręcznej|BajtyPerSecond|Maksimum||ShardId|
|cacheRead0|Tak|Odczyt pamięci podręcznej (fragment 0)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead1|Tak|Odczyt pamięci podręcznej (fragment 1)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead2|Tak|Odczyt pamięci podręcznej (fragment 2)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead3|Tak|Odczyt pamięci podręcznej (fragment 3)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead4|Tak|Odczyt pamięci podręcznej (fragment 4)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead5|Tak|Odczyt pamięci podręcznej (fragment 5)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead6|Tak|Odczyt pamięci podręcznej (fragment 6)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead7|Tak|Odczyt pamięci podręcznej (fragment 7)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead8|Tak|Odczyt pamięci podręcznej (fragment 8)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead9|Tak|Odczyt pamięci podręcznej (fragment 9)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite|Tak|Zapis w pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheWrite0|Tak|Zapis w pamięci podręcznej (fragment 0)|BajtyPerSecond|Maksimum||Brak wymiarów|
|cacheWrite1|Tak|Zapis w pamięci podręcznej (fragment 1)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite2|Tak|Zapis w pamięci podręcznej (fragment 2)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite3|Tak|Zapis w pamięci podręcznej (fragment 3)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite4|Tak|Zapis w pamięci podręcznej (fragment 4)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite5|Tak|Zapis w pamięci podręcznej (fragment 5)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite6|Tak|Zapis w pamięci podręcznej (fragment 6)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite7|Tak|Zapis w pamięci podręcznej (fragment 7)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite8|Tak|Zapis w pamięci podręcznej (fragment 8)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite9|Tak|Zapis w pamięci podręcznej (fragment 9)|BajtyPerSecond|Maksimum||Brak wymiarów|
|connectedclients|Tak|Połączeni klienci|Liczba|Maksimum||ShardId|
|connectedclients0|Tak|Połączeni klienci (fragment 0)|Liczba|Maksimum||Brak wymiarów|
|connectedclients1|Tak|Połączeni klienci (fragment 1)|Liczba|Maksimum||Brak wymiarów|
|connectedclients2|Tak|Połączeni klienci (fragment 2)|Liczba|Maksimum||Brak wymiarów|
|connectedclients3|Tak|Połączeni klienci (fragment 3)|Liczba|Maksimum||Brak wymiarów|
|connectedclients4|Tak|Połączeni klienci (fragment 4)|Liczba|Maksimum||Brak wymiarów|
|connectedclients5|Tak|Połączeni klienci (fragment 5)|Liczba|Maksimum||Brak wymiarów|
|connectedclients6|Tak|Połączeni klienci (fragment 6)|Liczba|Maksimum||Brak wymiarów|
|connectedclients7|Tak|Połączeni klienci (fragment 7)|Liczba|Maksimum||Brak wymiarów|
|connectedclients8|Tak|Połączeni klienci (fragment 8)|Liczba|Maksimum||Brak wymiarów|
|connectedclients9|Tak|Połączeni klienci (fragment 9)|Liczba|Maksimum||Brak wymiarów|
|błędy|Tak|błędy|Liczba|Maksimum||ShardId, ErrorType|
|klucze eksmisji|Tak|Wykluczone klucze|Liczba|Łącznie||ShardId|
|evictedkeys0|Tak|Klucze eksmisji (fragment 0)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys1|Tak|Klucze eksmisji (fragment 1)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys2|Tak|Klucze eksmisji (fragment 2)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys3|Tak|Klucze eksmisji (fragment 3)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys4|Tak|Eksmitowane klucze (fragment 4)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys5|Tak|Eksmitowane klucze (fragment 5)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys6|Tak|Eksmitowane klucze (fragment 6)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys7|Tak|Eksmitowane klucze (fragment 7)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys8|Tak|Eksmitowane klucze (fragment 8)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys9|Tak|Eksmitowane klucze (fragment 9)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys|Tak|Wygasłe klucze|Liczba|Łącznie||ShardId|
|expiredkeys0|Tak|Wygasłe klucze (fragment 0)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys1|Tak|Wygasłe klucze (fragment 1)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys2|Tak|Wygasłe klucze (fragment 2)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys3|Tak|Wygasłe klucze (fragment 3)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys4|Tak|Wygasłe klucze (fragment 4)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys5|Tak|Wygasłe klucze (fragment 5)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys6|Tak|Wygasłe klucze (fragment 6)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys7|Tak|Wygasłe klucze (fragment 7)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys8|Tak|Wygasłe klucze (fragment 8)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys9|Tak|Wygasłe klucze (fragment 9)|Liczba|Łącznie||Brak wymiarów|
|getcommands|Tak|Pobrania|Liczba|Łącznie||ShardId|
|getcommands0|Tak|Pobiera (fragment 0)|Liczba|Łącznie||Brak wymiarów|
|getcommands1|Tak|Pobiera (fragment 1)|Liczba|Łącznie||Brak wymiarów|
|getcommands2|Tak|Pobiera (fragment 2)|Liczba|Łącznie||Brak wymiarów|
|getcommands3|Tak|Pobiera (fragment 3)|Liczba|Łącznie||Brak wymiarów|
|getcommands4|Tak|Pobiera (fragment 4)|Liczba|Łącznie||Brak wymiarów|
|getcommands5|Tak|Pobiera (fragment 5)|Liczba|Łącznie||Brak wymiarów|
|getcommands6|Tak|Pobiera (fragment 6)|Liczba|Łącznie||Brak wymiarów|
|getcommands7|Tak|Pobiera (fragment 7)|Liczba|Łącznie||Brak wymiarów|
|getcommands8|Tak|Pobiera (fragment 8)|Liczba|Łącznie||Brak wymiarów|
|getcommands9|Tak|Pobiera (fragment 9)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond|Tak|Liczba operacji na sekundę|Liczba|Maksimum||ShardId|
|operationsPerSecond0|Tak|Operacje na sekundę (fragment 0)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond1|Tak|Operacje na sekundę (fragment 1)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond2|Tak|Operacje na sekundę (fragment 2)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond3|Tak|Operacje na sekundę (fragment 3)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond4|Tak|Operacje na sekundę (fragment 4)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond5|Tak|Operacje na sekundę (fragment 5)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond6|Tak|Operacje na sekundę (fragment 6)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond7|Tak|Operacje na sekundę (fragment 7)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond8|Tak|Operacje na sekundę (fragment 8)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond9|Tak|Operacje na sekundę (fragment 9)|Liczba|Maksimum||Brak wymiarów|
|percentProcessorTime|Tak|Procesor CPU|Procent|Maksimum||ShardId|
|percentProcessorTime0|Tak|Procesor CPU (fragment 0)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime1|Tak|Procesor CPU (fragment 1)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime2|Tak|Procesor CPU (fragment 2)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime3|Tak|Procesor CPU (fragment 3)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime4|Tak|Procesor CPU (fragment 4)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime5|Tak|Procesor CPU (fragment 5)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime6|Tak|Procesor CPU (fragment 6)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime7|Tak|Procesor CPU (fragment 7)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime8|Tak|Procesor CPU (fragment 8)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime9|Tak|Procesor CPU (fragment 9)|Procent|Maksimum||Brak wymiarów|
|serverLoad|Tak|Obciążenie serwera|Procent|Maksimum||ShardId|
|serverLoad0|Tak|Obciążenie serwera (fragment 0)|Procent|Maksimum||Brak wymiarów|
|serverLoad1|Tak|Obciążenie serwera (fragment 1)|Procent|Maksimum||Brak wymiarów|
|serverLoad2|Tak|Obciążenie serwera (fragment 2)|Procent|Maksimum||Brak wymiarów|
|serverLoad3|Tak|Obciążenie serwera (fragment 3)|Procent|Maksimum||Brak wymiarów|
|serverLoad4|Tak|Obciążenie serwera (fragment 4)|Procent|Maksimum||Brak wymiarów|
|serverLoad5|Tak|Obciążenie serwera (fragment 5)|Procent|Maksimum||Brak wymiarów|
|serverLoad6|Tak|Obciążenie serwera (fragment 6)|Procent|Maksimum||Brak wymiarów|
|serverLoad7|Tak|Obciążenie serwera (fragment 7)|Procent|Maksimum||Brak wymiarów|
|serverLoad8|Tak|Obciążenie serwera (fragment 8)|Procent|Maksimum||Brak wymiarów|
|serverLoad9|Tak|Obciążenie serwera (fragment 9)|Procent|Maksimum||Brak wymiarów|
|setcommands|Tak|Zestawy|Liczba|Łącznie||ShardId|
|setcommands0|Tak|Zestawy (fragment 0)|Liczba|Łącznie||Brak wymiarów|
|setcommands1|Tak|Zestawy (fragment 1)|Liczba|Łącznie||Brak wymiarów|
|setcommands2|Tak|Zestawy (fragment 2)|Liczba|Łącznie||Brak wymiarów|
|setcommands3|Tak|Zestawy (fragment 3)|Liczba|Łącznie||Brak wymiarów|
|setcommands4|Tak|Zestawy (fragment 4)|Liczba|Łącznie||Brak wymiarów|
|setcommands5|Tak|Zestawy (fragment 5)|Liczba|Łącznie||Brak wymiarów|
|setcommands6|Tak|Zestawy (fragment 6)|Liczba|Łącznie||Brak wymiarów|
|setcommands7|Tak|Zestawy (fragment 7)|Liczba|Łącznie||Brak wymiarów|
|setcommands8|Tak|Zestawy (fragment 8)|Liczba|Łącznie||Brak wymiarów|
|setcommands9|Tak|Zestawy (fragment 9)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed|Tak|Łączna liczba operacji|Liczba|Łącznie||ShardId|
|totalcommandsprocessed0|Tak|Łączna liczba operacji (fragment 0)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed1|Tak|Łączna liczba operacji (fragment 1)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed2|Tak|Łączna liczba operacji (fragment 2)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed3|Tak|Łączna liczba operacji (fragment 3)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed4|Tak|Łączna liczba operacji (fragment 4)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed5|Tak|Łączna liczba operacji (fragment 5)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed6|Tak|Łączna liczba operacji (fragment 6)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed7|Tak|Łączna liczba operacji (fragment 7)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed8|Tak|Łączna liczba operacji (fragment 8)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed9|Tak|Łączna liczba operacji (fragment 9)|Liczba|Łącznie||Brak wymiarów|
|totalkeys|Tak|Łączna liczba kluczy|Liczba|Maksimum||ShardId|
|totalkeys0|Tak|Łączna liczba kluczy (fragment 0)|Liczba|Maksimum||Brak wymiarów|
|totalkeys1|Tak|Łączna liczba kluczy (fragment 1)|Liczba|Maksimum||Brak wymiarów|
|totalkeys2|Tak|Łączna liczba kluczy (fragment 2)|Liczba|Maksimum||Brak wymiarów|
|totalkeys3|Tak|Łączna liczba kluczy (fragment 3)|Liczba|Maksimum||Brak wymiarów|
|totalkeys4|Tak|Łączna liczba kluczy (fragment 4)|Liczba|Maksimum||Brak wymiarów|
|totalkeys5|Tak|Łączna liczba kluczy (fragment 5)|Liczba|Maksimum||Brak wymiarów|
|totalkeys6|Tak|Łączna liczba kluczy (fragment 6)|Liczba|Maksimum||Brak wymiarów|
|totalkeys7|Tak|Łączna liczba kluczy (fragment 7)|Liczba|Maksimum||Brak wymiarów|
|totalkeys8|Tak|Łączna liczba kluczy (fragment 8)|Liczba|Maksimum||Brak wymiarów|
|totalkeys9|Tak|Łączna liczba kluczy (fragment 9)|Liczba|Maksimum||Brak wymiarów|
|usedmemory|Tak|Użyta pamięć|Bajty|Maksimum||ShardId|
|usedmemory0|Tak|Używana pamięć (fragment 0)|Bajty|Maksimum||Brak wymiarów|
|usedmemory1|Tak|Używana pamięć (fragment 1)|Bajty|Maksimum||Brak wymiarów|
|usedmemory2|Tak|Używana pamięć (fragment 2)|Bajty|Maksimum||Brak wymiarów|
|usedmemory3|Tak|Używana pamięć (fragment 3)|Bajty|Maksimum||Brak wymiarów|
|usedmemory4|Tak|Używana pamięć (fragment 4)|Bajty|Maksimum||Brak wymiarów|
|usedmemory5|Tak|Używana pamięć (fragment 5)|Bajty|Maksimum||Brak wymiarów|
|usedmemory6|Tak|Używana pamięć (fragment 6)|Bajty|Maksimum||Brak wymiarów|
|usedmemory7|Tak|Używana pamięć (fragment 7)|Bajty|Maksimum||Brak wymiarów|
|usedmemory8|Tak|Używana pamięć (fragment 8)|Bajty|Maksimum||Brak wymiarów|
|usedmemory9|Tak|Używana pamięć (fragment 9)|Bajty|Maksimum||Brak wymiarów|
|usedmemorypercentage|Tak|Procentowe użycie pamięci|Procent|Maksimum||ShardId|
|usedmemoryRss|Tak|Funkcja RSS używanej pamięci|Bajty|Maksimum||ShardId|
|usedmemoryRss0|Tak|Funkcja RSS używanej pamięci (fragment 0)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss1|Tak|Funkcja RSS używanej pamięci (fragment 1)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss2|Tak|Źródło danych RSS używanej pamięci (fragment 2)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss3|Tak|Źródło danych RSS używanej pamięci (fragment 3)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss4|Tak|Źródło danych RSS używanej pamięci (fragment 4)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss5|Tak|Źródło danych RSS używanej pamięci (fragment 5)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss6|Tak|Źródło danych RSS używanej pamięci (fragment 6)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss7|Tak|Źródło danych RSS używanej pamięci (fragment 7)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss8|Tak|Źródło danych RSS używanej pamięci (fragment 8)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss9|Tak|Funkcja RSS używanej pamięci (fragment 9)|Bajty|Maksimum||Brak wymiarów|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|cachehits|Tak|Trafienia w pamięci podręcznej|Liczba|Łącznie||Brak wymiarów|
|cacheLatency|Tak|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Liczba|Średnia||InstanceId|
|cachemisses (pamięć podręczna)|Tak|Chybienia w pamięci podręcznej|Liczba|Łącznie||InstanceId|
|cacheRead|Tak|Odczyt pamięci podręcznej|BytesPerSecond|Maksimum||InstanceId|
|cacheWrite|Tak|Zapis w pamięci podręcznej|BytesPerSecond|Maksimum||InstanceId|
|connectedclients|Tak|Połączeni klienci|Liczba|Maksimum||InstanceId|
|błędy|Tak|błędy|Liczba|Maksimum||InstanceId, ErrorType|
|klucze eksmisji|Tak|Wykluczone klucze|Liczba|Łącznie||Brak wymiarów|
|expiredkeys|Tak|Wygasłe klucze|Liczba|Łącznie||Brak wymiarów|
|getcommands|Tak|Pobrania|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond|Tak|Liczba operacji na sekundę|Liczba|Maksimum||Brak wymiarów|
|percentProcessorTime|Tak|Procesor CPU|Procent|Maksimum||InstanceId|
|serverLoad|Tak|Obciążenie serwera|Procent|Maksimum||Brak wymiarów|
|setcommands|Tak|Zestawy|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed|Tak|Łączna liczba operacji|Liczba|Łącznie||Brak wymiarów|
|totalkeys|Tak|Łączna liczba kluczy|Liczba|Maksimum||Brak wymiarów|
|usedmemory|Tak|Użyta pamięć|Bajty|Maksimum||Brak wymiarów|
|usedmemorypercentage|Tak|Procentowe użycie pamięci|Procent|Maksimum||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Tak|Web Application Firewall liczby żądań|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez Web Application Firewall|PolicyName, RuleName, Akcja|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ByteHitRatio|Tak|Współczynnik trafień bajtów|Procent|Średnia|Jest to stosunek całkowitej liczby bajtów obsługiwanych z pamięci podręcznej do całkowitej liczby bajtów odpowiedzi|Punkt końcowy|
|OriginHealthPercentage|Tak|Procent kondycji źródła|Procent|Średnia|Procent pomyślnych sond kondycji z systemu AFDX do zaplecza.|Origin, OriginGroup|
|OriginLatency|Tak|Opóźnienie źródła|Milisekund|Średnia|Czas obliczony od momentu wysłania żądania przez urządzenie brzegowe AFDX do zaplecza do momentu, gdy afdx odebrał ostatni bajt odpowiedzi z zaplecza.|Źródło, punkt końcowy|
|OriginRequestCount|Tak|Liczba żądań źródła|Liczba|Łącznie|Liczba żądań wysłanych z afdx do źródła.|HttpStatus, HttpStatusGroup, Origin, Endpoint|
|Percentage4XX|Tak|Wartość procentowa 4XX|Procent|Średnia|Procent wszystkich żądań klientów, dla których kod stanu odpowiedzi to 4XX|Endpoint, ClientRegion, ClientCountry|
|Percentage5XX|Tak|Wartość procentowa 5XX|Procent|Średnia|Procent wszystkich żądań klientów, dla których kod stanu odpowiedzi to 5XX|Endpoint, ClientRegion, ClientCountry|
|RequestCount|Tak|Liczba żądań|Liczba|Łącznie|Liczba żądań klientów obsługiwanych przez serwer proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|RequestSize|Tak|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysłanych jako żądania od klientów do afdx.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|ResponseSize|Tak|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|TotalLatency|Tak|Całkowite opóźnienie|Milisekund|Średnia|Czas obliczony od momentu otrzymania żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|WebApplicationFirewallRequestCount|Tak|Web Application Firewall liczby żądań|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez Web Application Firewall|PolicyName, RuleName, Akcja|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku/s|Nie|Odczyt dysku|BajtyPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Disk Read IOPS (We/Wy odczytu z dysku).|RoleInstanceId|
|Bajty zapisu dysku/s|Nie|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywanych na dysku w okresie monitorowania.|RoleInstanceId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|We/Wy zapisu na dysku.|RoleInstanceId|
|Sieć — wejście|Tak|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Tak|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wychodzących we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie w użyciu przez maszyny wirtualne.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku/s|Nie|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Disk Read IOPS (We/Wy odczytu z dysku).|Brak wymiarów|
|Bajty zapisu na dysku/s|Nie|Zapis na dysku|BajtyPerSecond|Średnia|Średnia liczba bajtów zapisywanych na dysku w okresie monitorowania.|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|We/Wy zapisu na dysku.|Brak wymiarów|
|Sieć — wejście|Tak|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|Brak wymiarów|
|Sieć — wyjście|Tak|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wychodzących we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|Brak wymiarów|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie w użyciu przez maszyny wirtualne.|Brak wymiarów|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API, w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania, w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Nie|Używana pojemność|Bajty|Średnia|Używana pojemność konta|Brak wymiarów|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|BlobCapacity|Nie|Pojemność obiektu blob|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez konto magazynu w Blob service bajtach.|BlobType, Warstwa|
|BlobCount|Nie|Liczba obiektów blob|Liczba|Średnia|Liczba obiektów blob na koncie magazynu w Blob service.|BlobType, Warstwa|
|ContainerCount|Tak|Liczba kontenerów obiektów blob|Liczba|Średnia|Liczba kontenerów na koncie magazynu w Blob service.|Brak wymiarów|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|IndexCapacity|Nie|Pojemność indeksu|Bajty|Średnia|Ilość miejsca używanego przez indeks ADLS Gen2 (hierarchiczny) w bajtach.|Brak wymiarów|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API, w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania, w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication, FileShare|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|Nie|Pojemność pliku|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Udział plików|
|FileCount|Nie|Liczba plików|Liczba|Średnia|Liczba plików w usłudze plików konta magazynu.|Udział plików|
|FileShareCount|Nie|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|Brak wymiarów|
|FileShareQuota|Nie|Rozmiar limitu przydziału udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udział plików|
|FileShareSnapshotCount|Nie|Liczba migawek udziałów plików|Liczba|Średnia|Liczba migawek w udziałach w usłudze Plików konta magazynu.|Udział plików|
|FileShareSnapshotSize|Nie|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udział plików|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|End-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds (Opóźnienie końcowe żądań pomyślnych do usługi magazynu lub określonej operacji interfejsu API, w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania, w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication, FileShare|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Tak|Pojemność kolejki|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez konto magazynu w usługa kolejki w bajtach.|Brak wymiarów|
|QueueCount|Tak|Liczba kolejek|Liczba|Średnia|Liczba kolejek na koncie magazynu w usługa kolejki.|Brak wymiarów|
|QueueMessageCount|Tak|Liczba komunikatów w kolejce|Liczba|Średnia|Przybliżona liczba komunikatów w kolejce w usługa kolejki.|Brak wymiarów|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|End-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds (Opóźnienie końcowe żądań pomyślnych do usługi magazynu lub określonej operacji interfejsu API, w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania, w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API, w milisekundach. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Opóźnienie używane przez usługę Azure Storage do przetwarzania pomyślnego żądania, w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|TableCapacity|Tak|Pojemność tabeli|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę tabel konta magazynu w bajtach.|Brak wymiarów|
|TableCount|Tak|Liczba tabel|Liczba|Średnia|Liczba tabel w usłudze Tabel konta magazynu.|Brak wymiarów|
|TableEntityCount|Tak|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli w usłudze Table Service konta magazynu.|Brak wymiarów|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BlockedCalls|Tak|Zablokowane wywołania|Liczba|Łącznie|Liczba wywołań, które przekroczyły limit szybkości lub limitu przydziału.|ApiName, OperationName, Region|
|Wytrenowane znaki|Tak|Wytrenowane znaki|Liczba|Łącznie|Całkowita liczba przeszkolonych znaków.|ApiName, OperationName, Region|
|ZnakiPrzetłumaczone|Tak|Przetłumaczone znaki|Liczba|Łącznie|Łączna liczba znaków w przychodzącym żądaniu tekstowym.|ApiName, OperationName, Region|
|ClientErrors|Tak|Błędy klienta|Liczba|Łącznie|Liczba wywołań z błędem po stronie klienta (kod odpowiedzi HTTP 4xx).|ApiName, OperationName, Region|
|DataIn|Tak|Dane w|Bajty|Łącznie|Rozmiar danych przychodzących w bajtach.|ApiName, OperationName, Region|
|DataOut (Daneout)|Tak|Dane wychodzące|Bajty|Łącznie|Rozmiar danych wychodzących w bajtach.|ApiName, OperationName, Region|
|Opóźnienie|Tak|Opóźnienie|Milisekund|Średnia|Opóźnienie w milisekundach.|ApiName, OperationName, Region|
|LearnedEvents|Tak|Zdarzenia poznane|Liczba|Łącznie|Liczba poznanych zdarzeń.|IsMatchBaseline, Mode, RunId|
|MatchedRewards|Tak|Dopasowane nagrody|Liczba|Łącznie| Liczba dopasowanych nagrody.|IsMatchBaseline, Mode, RunId|
|ObservedRewards (Zaobserwowane odejechy)|Tak|Zaobserwowane nagrody|Liczba|Łącznie|Liczba zaobserwowanych nagrody.|IsMatchBaseline, Mode, RunId|
|ProcessedCharacters|Tak|Przetworzone znaki|Liczba|Łącznie|Liczba znaków.|ApiName, FeatureName, UsageChannel, Region|
|ProcessedTextRecords|Tak|Przetworzone rekordy tekstowe|Liczba|Łącznie|Liczba rekordów tekstowych.|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors (ServerErrors)|Tak|Błędy serwera|Liczba|Łącznie|Liczba wywołań z błędem wewnętrznym usługi (kod odpowiedzi HTTP 5xx).|ApiName, OperationName, Region|
|SpeechSessionDuration|Tak|Czas trwania sesji mowy|Sekundy|Łącznie|Łączny czas trwania sesji mowy w sekundach.|ApiName, OperationName, Region|
|SuccessfulCalls|Tak|Pomyślne wywołania|Liczba|Łącznie|Liczba pomyślnych wywołań.|ApiName, OperationName, Region|
|TotalCalls|Tak|Łączna liczba wywołań|Liczba|Łącznie|Łączna liczba wywołań.|ApiName, OperationName, Region|
|TotalErrors|Tak|Całkowita liczba błędów|Liczba|Łącznie|Łączna liczba wywołań z odpowiedzią na błąd (kod odpowiedzi HTTP 4xx lub 5xx).|ApiName, OperationName, Region|
|TotalTokenCalls|Tak|Łączna liczba wywołań tokenów|Liczba|Łącznie|Łączna liczba wywołań tokenu.|ApiName, OperationName, Region|
|TotalTransactions|Tak|Łączna liczba transakcji|Liczba|Łącznie|Łączna liczba transakcji.|Brak wymiarów|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|Nie|Żądania interfejsu API uwierzytelniania|Liczba|Liczba|Liczba wszystkich żądań względem punktu końcowego Communication Services uwierzytelniania.|Operation, StatusCode, StatusCodeClass|
|APIRequestChat|Tak|Żądania interfejsu API czatu|Liczba|Liczba|Liczba wszystkich żądań względem punktu końcowego Communication Services Czat.|Operation, StatusCode, StatusCodeClass|
|APIRequestSMS|Tak|interfejs API Wiadomości SMS żądania|Liczba|Liczba|Liczba wszystkich żądań względem punktu końcowego Communication Services SMS.|Operation, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|RoleInstanceId, RoleId|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Disk Read IOPS|RoleInstanceId, RoleId|
|Bajty zapisu na dysku|Tak|Bajty zapisu na dysku|Bajty|Łącznie|Bajty zapisane na dysku w okresie monitorowania|RoleInstanceId, RoleId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Disk Write IOPS|RoleInstanceId, RoleId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie w użyciu przez maszyny wirtualne|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|RoleInstanceId, RoleId|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Disk Read IOPS|RoleInstanceId, RoleId|
|Bajty zapisu na dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisane na dysku w okresie monitorowania|RoleInstanceId, RoleId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Disk Write IOPS|RoleInstanceId, RoleId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie w użyciu przez maszyny wirtualne|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku złożonego/s|Nie|Bajty odczytu dysku/s (wersja zapoznawcza)|Bajty|Średnia|Bajty/s odczytane z dysku w okresie monitorowania. Należy pamiętać, że ta metryka jest w wersji zapoznawczej i może ulec zmianie, zanim stanie się ogólnie dostępna||
|Operacje odczytu z dysku złożonego na sekundę|Nie|Operacje odczytu z dysku/s (wersja zapoznawcza)|Bajty|Średnia|Liczba operacji we/wy odczytu wykonanych na dysku w okresie monitorowania. Należy pamiętać, że ta metryka jest w wersji zapoznawczej i może ulec zmianie, zanim stanie się ogólnie dostępna||
|Bajty zapisu dysku złożonego/s|Nie|Bajty zapisu dysku/s (wersja zapoznawcza)|Bajty|Średnia|Bajty/s zapisywane na dysku w okresie monitorowania. Należy pamiętać, że ta metryka jest w wersji zapoznawczej i może ulec zmianie, zanim stanie się ogólnie dostępna||
|Operacje zapisu na dysku złożonym na sekundę|Nie|Operacje zapisu na dysku/s (wersja zapoznawcza)|Bajty|Średnia|Liczba operacji we/wy zapisu wykonanych na dysku w okresie monitorowania. Należy pamiętać, że ta metryka jest w wersji zapoznawczej i może ulec zmianie, zanim stanie się ogólnie dostępna||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Zużyte środki na użycie procesora CPU|Tak|Zużyte środki na użycie procesora CPU|Liczba|Średnia|Łączna liczba środków zużytych przez maszynę wirtualną. Dostępne tylko na maszyny wirtualne serii B z serii B z serii Burstable|Brak wymiarów|
|Pozostałe środki na procesor CPU|Tak|Pozostałe środki na procesor CPU|Liczba|Średnia|Łączna liczba środków dostępnych na serię. Dostępne tylko na maszyny wirtualne serii B z serii B z serii Burstable|Brak wymiarów|
|Procent użycia przepustowości dysku danych|Tak|Procent użycia przepustowości dysku danych|Procent|Średnia|Procent zużytej przepustowości dysku danych na minutę|Jednostki lun|
|Procent zużytych wartości we/wy na dysku danych|Tak|Procent zużytej wartości we/wy dysku danych|Procent|Średnia|Procent zużytych we/wy dysku danych na minutę|Jednostki lun|
|Maksymalna przepustowość dysku danych z serii|Tak|Maksymalna przepustowość serii dysków danych|Liczba|Średnia|Maksymalna liczba bajtów na sekundę przepływności dysk danych może osiągnąć dzięki zwiększaniu|Jednostki lun|
|Data Disk Max Burst IOPS|Tak|Data Disk Max Burst IOPS|Liczba|Średnia|Maksymalna liczba we/wy na dysku danych może osiągnąć dzięki serii|Jednostki lun|
|Głębokość kolejki dysku danych|Tak|Głębokość kolejki dysku danych|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostki lun|
|Bajty odczytu dysku danych/s|Tak|Bajty odczytu dysku danych/s|BajtyPerSecond|Średnia|Bajty/s odczytane z jednego dysku w okresie monitorowania|Jednostki lun|
|Operacje odczytu dysku danych/s|Tak|Operacje odczytu dysku danych/s|CountPerSecond|Średnia|Odczytywanie IOPS z pojedynczego dysku w okresie monitorowania|Jednostki lun|
|Przepustowość docelowa dysku danych|Tak|Przepustowość docelowa dysku danych|Liczba|Średnia|Bajty bazowe na sekundę przepływności dysk danych może osiągnąć bez zwiększenia|Jednostki lun|
|Docelowa wartość we/wy dysku danych|Tak|Docelowa wartość we/wy dysku danych|Liczba|Średnia|Bazowy dysk danych IOPS może osiągnąć bez serii|Jednostki lun|
|Procent użytych środków na wzrost b/s na dysku danych|Tak|Procent użytych środków na wzrost b/s na dysku danych|Procent|Średnia|Procent używanych do tej pory środków na wzrost przepustowości dysku danych|Jednostki lun|
|Procent środków na we/wy na dysku danych z serii|Tak|Procent środków na we/wy na dysku danych z serii|Procent|Średnia|Procent używanych do tej pory środków na we/wy dla dysku danych|Jednostki lun|
|Bajty zapisu dysku danych/s|Tak|Bajty zapisu dysku danych/s|BytesPerSecond|Średnia|Bajty/s zapisywane na jednym dysku w okresie monitorowania|Jednostki lun|
|Operacje zapisu na dysku danych/s|Tak|Operacje zapisu na dysku danych/s|CountPerSecond|Średnia|Zapis we/wy na dysku na jednym dysku w okresie monitorowania|Jednostki lun|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Disk Read IOPS|Brak wymiarów|
|Bajty zapisu na dysku|Tak|Bajty zapisu na dysku|Bajty|Łącznie|Bajty zapisane na dysku w okresie monitorowania|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Disk Write IOPS|Brak wymiarów|
|Przepływy przychodzące|Tak|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Maksymalna szybkość tworzenia przepływów przychodzących|Tak|Maksymalna szybkość tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Sieć — wejście|Tak|Sieć rozliczana (przestarzałe)|Bajty|Łącznie|Liczba rozliczanych bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|Brak wymiarów|
|Łączna liczba sieci|Tak|Łączna liczba sieci|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak wymiarów|
|Sieć — wyjście|Tak|Rozliczane w sieci (przestarzałe)|Bajty|Łącznie|Liczba rozliczanych bajtów wychodzących we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|Brak wymiarów|
|Suma ruchu wychodzącego sieci|Tak|Suma ruchu wychodzącego sieci|Bajty|Łącznie|Liczba bajtów wychodzących we wszystkich interfejsach sieciowych według maszyn wirtualnych (ruch wychodzący)|Brak wymiarów|
|Procent zużytej przepustowości dysku systemu operacyjnego|Tak|Procent zużytej przepustowości dysku systemu operacyjnego|Procent|Średnia|Procent zużytej przepustowości dysku systemu operacyjnego na minutę|Jednostki lun|
|Procent zużytych wartości we/wy dysku systemu operacyjnego|Tak|Procent zużytych wartości we/wy dysku systemu operacyjnego|Procent|Średnia|Procent zużytych we/wy dysku systemu operacyjnego na minutę|Jednostki lun|
|Maksymalna przepustowość dysku systemu operacyjnego|Tak|Maksymalna przepustowość dysku systemu operacyjnego|Liczba|Średnia|Maksymalna liczba bajtów na sekundę, które może osiągnąć dysk systemu operacyjnego przepływności dzięki zwiększaniu|Jednostki lun|
|Maksymalna liczba we/wy na dysku systemu operacyjnego z serii|Tak|Maksymalna liczba we/wy na dysku systemu operacyjnego z serii|Liczba|Średnia|Maksymalny rozmiar dysku systemu operacyjnego IOPS można osiągnąć przy użyciu serii|Jednostki lun|
|Głębokość kolejki dysku systemu operacyjnego|Tak|Głębokość kolejki dysku systemu operacyjnego|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu dysku systemu operacyjnego/s|BytesPerSecond|Średnia|Bajty/s odczytane z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu dysku systemu operacyjnego/s|Tak|Operacje odczytu dysku systemu operacyjnego/s|CountPerSecond|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Przepustowość docelowa dysku systemu operacyjnego|Tak|Przepustowość docelowa dysku systemu operacyjnego|Liczba|Średnia|Bajty bazowe na sekundę przepływności Dysk systemu operacyjnego może osiągnąć bez zwiększenia|Jednostki lun|
|Docelowa wartość we/wy na dysku systemu operacyjnego|Tak|Docelowa wartość we/wy na dysku systemu operacyjnego|Liczba|Średnia|Dysk systemu operacyjnego z bazową wydajnością we/wy na woluminie można osiągnąć bez serii|Jednostki lun|
|Procent użytego dysku systemu operacyjnego na wzrost środków na bity na bity na bity|Tak|Procent użytego dysku systemu operacyjnego na wzrost środków na bity na bity na bity|Procent|Średnia|Procent wykorzystanej do tej pory przepustowości dysku systemu operacyjnego|Jednostki lun|
|Procent środków na we/wy na dysku systemu operacyjnego|Tak|Procent środków na we/wy na dysku systemu operacyjnego|Procent|Średnia|Procent używanych do tej pory środków na we/wy dysków systemu operacyjnego|Jednostki lun|
|Bajty zapisu dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s|BajtyPerSecond|Średnia|Bajty/s zapisywane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego na sekundę|CountPerSecond|Średnia|Zapis IOPS z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Przepływy wychodzące|Tak|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku wychodzącym (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Maksymalna szybkość tworzenia przepływów wychodzących|Tak|Maksymalna szybkość tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie w użyciu przez maszyny wirtualne|Brak wymiarów|
|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Tak|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Procent|Średnia|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Jednostki lun|
|Chybienie odczytu pamięci podręcznej dysku danych w awarii Premium|Tak|Chybienie odczytu pamięci podręcznej dysku danych Premium|Procent|Średnia|Chybienie odczytu pamięci podręcznej dysku danych Premium|Jednostki lun|
|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Tak|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Procent|Średnia|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Brak wymiarów|
|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Tak|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Procent|Średnia|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Brak wymiarów|
|Procent użycia przepustowości pamięci podręcznej maszyny wirtualnej|Tak|Procent użycia przepustowości pamięci podręcznej maszyny wirtualnej|Procent|Średnia|Procent przepustowości dysku w pamięci podręcznej używanej przez maszynę wirtualną|Brak wymiarów|
|Procent zużytych wartości we/wy na dysku w pamięci podręcznej maszyny wirtualnej|Tak|Procent zużytych wartości we/wy na dysku w pamięci podręcznej maszyny wirtualnej|Procent|Średnia|Procent buforowanej ilości danych we/wy na dysku zużytej przez maszynę wirtualną|Brak wymiarów|
|Procent użycia przepustowości bez pamięci maszyny wirtualnej|Tak|Procent zużytej przepustowości bez pamięci maszyny wirtualnej|Procent|Średnia|Procent przepustowości dysku bez pamięci używanej przez maszynę wirtualną|Brak wymiarów|
|Procent zużytych bezcachowanych IOPS maszyny wirtualnej|Tak|Procent zużytych bezcachowanych IOPS maszyny wirtualnej|Procent|Średnia|Procent niecachowanych wartości we/wy na dysku zużytych przez maszynę wirtualną|Brak wymiarów|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Zużyte środki na użycie procesora CPU|Tak|Zużyte środki na użycie procesora CPU|Liczba|Średnia|Łączna liczba środków zużytych przez maszynę wirtualną. Dostępne tylko na maszyny wirtualne serii B z serii B z serii Burstable|Brak wymiarów|
|Pozostałe środki na procesor CPU|Tak|Pozostałe środki na procesor CPU|Liczba|Średnia|Łączna liczba środków dostępnych na serię. Dostępne tylko na maszyny wirtualne serii B z serii B z serii Burstable|Brak wymiarów|
|Procent użycia przepustowości dysku danych|Tak|Procent zużytej przepustowości dysku danych|Procent|Średnia|Procent zużytej przepustowości dysku danych na minutę|LUN, VMName|
|Procent zużytej wartości we/wy dysku danych|Tak|Procent zużytych wartości we/wy na dysku danych|Procent|Średnia|Procent zużytych we/wy dysku danych na minutę|LUN, VMName|
|Maksymalna przepustowość serii dysków danych|Tak|Maksymalna przepustowość serii dysków danych|Liczba|Średnia|Maksymalna liczba bajtów na sekundę przepływności dysk danych może osiągnąć dzięki zwiększaniu|LUN, VMName|
|Data Disk Max Burst IOPS|Tak|Data Disk Max Burst IOPS|Liczba|Średnia|Maksymalna liczba we/wy na dysku danych może osiągnąć dzięki serii|LUN, VMName|
|Głębokość kolejki dysku danych|Tak|Głębokość kolejki dysku danych|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|LUN, VMName|
|Bajty odczytu dysku danych/s|Tak|Bajty odczytu dysku danych/s|BajtyPerSecond|Średnia|Bajty/s odczytu z jednego dysku w okresie monitorowania|LUN, VMName|
|Operacje odczytu dysku danych/s|Tak|Operacje odczytu dysku danych/s|CountPerSecond|Średnia|Odczytywanie IOPS z pojedynczego dysku w okresie monitorowania|LUN, VMName|
|Przepustowość docelowa dysku danych|Tak|Przepustowość docelowa dysku danych|Liczba|Średnia|Bajty bazowe na sekundę przepływności dysk danych może osiągnąć bez zwiększenia|LUN, VMName|
|Docelowa wartość we/wy dysku danych|Tak|Docelowa wartość we/wy dysku danych|Liczba|Średnia|Bazowy dysk danych IOPS może osiągnąć bez serii|LUN, VMName|
|Procent użytych środków na wzrost b/s na dysku danych|Tak|Procent użytych środków na wzrost b/s na dysku danych|Procent|Średnia|Procent używanych do tej pory środków na wzrost przepustowości dysku danych|LUN, VMName|
|Procent środków na we/wy na dysku danych z serii|Tak|Procent środków na we/wy na dysku danych|Procent|Średnia|Procent używanych do tej pory środków na we/wy dysków Data Disk|LUN, VMName|
|Bajty zapisu dysku danych/s|Tak|Bajty zapisu na dysku danych/s|BajtyPerSecond|Średnia|Bajty/s zapisywane na jednym dysku w okresie monitorowania|LUN, VMName|
|Operacje zapisu na dysku danych/s|Tak|Operacje zapisu na dysku danych/s|CountPerSecond|Średnia|Zapis we/wy na dysku na jednym dysku w okresie monitorowania|LUN, VMName|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|VMName|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Disk Read IOPS|VMName|
|Bajty zapisu na dysku|Tak|Bajty zapisu na dysku|Bajty|Łącznie|Bajty zapisane na dysku w okresie monitorowania|VMName|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Disk Write IOPS|VMName|
|Przepływy przychodzące|Tak|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|VMName|
|Maksymalna szybkość tworzenia przepływów przychodzących|Tak|Maksymalna szybkość tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|VMName|
|Sieć — wejście|Tak|Sieć rozliczana (przestarzałe)|Bajty|Łącznie|Liczba rozliczanych bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|VMName|
|Łączna liczba sieci|Tak|Łączna liczba sieci|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Sieć — wyjście|Tak|Rozliczane z sieci (przestarzałe)|Bajty|Łącznie|Liczba rozliczanych bajtów na wszystkich interfejsach sieciowych według maszyn wirtualnych (ruch wychodzący) (przestarzałe)|VMName|
|Suma ruchu wychodzącego sieci|Tak|Suma ruchu wychodzącego sieci|Bajty|Łącznie|Liczba bajtów wychodzących we wszystkich interfejsach sieciowych według maszyn wirtualnych (ruch wychodzący)|VMName|
|Procent zużytej przepustowości dysku systemu operacyjnego|Tak|Procent zużytej przepustowości dysku systemu operacyjnego|Procent|Średnia|Procent zużytej przepustowości dysku systemu operacyjnego na minutę|LUN, VMName|
|Procent zużytych wartości we/wy dysku systemu operacyjnego|Tak|Procent zużytych wartości we/wy dysku systemu operacyjnego|Procent|Średnia|Procent zużytych we/wy dysku systemu operacyjnego na minutę|LUN, VMName|
|Maksymalna przepustowość dysku systemu operacyjnego|Tak|Maksymalna przepustowość dysku systemu operacyjnego|Liczba|Średnia|Maksymalna liczba bajtów na sekundę, które dysk systemu operacyjnego przepływności może osiągnąć dzięki zwiększaniu|LUN, VMName|
|Maksymalna liczba we/wy na dysku systemu operacyjnego|Tak|Maksymalna liczba we/wy na dysku systemu operacyjnego|Liczba|Średnia|Maksymalna liczba we/wy na dysku systemu operacyjnego w trybie "bursting"|LUN, VMName|
|Głębokość kolejki dysku systemu operacyjnego|Tak|Głębokość kolejki dysku systemu operacyjnego|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|VMName|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu dysku systemu operacyjnego/s|BytesPerSecond|Średnia|Bajty/s odczytane z jednego dysku w okresie monitorowania dysku systemu operacyjnego|VMName|
|Operacje odczytu dysku systemu operacyjnego/s|Tak|Operacje odczytu dysku systemu operacyjnego/s|CountPerSecond|Średnia|Odczyt IOPS z jednego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Przepustowość docelowa dysku systemu operacyjnego|Tak|Przepustowość docelowa dysku systemu operacyjnego|Liczba|Średnia|Bajty bazowe na sekundę przepływności Dysk systemu operacyjnego może osiągnąć bez zwiększenia|LUN, VMName|
|Docelowa wartość we/wy dysku systemu operacyjnego|Tak|Docelowa wartość we/wy dysku systemu operacyjnego|Liczba|Średnia|Podstawowy dysk systemu operacyjnego we/wy na dane można osiągnąć bez serii|LUN, VMName|
|Procent środków na sypkich bitów na bity w bitach na dysku systemu operacyjnego|Tak|Procent środków na sypkich bitów na bity w bitach na dysku systemu operacyjnego|Procent|Średnia|Procent używanych do tej pory środków na przepustowość dysku systemu operacyjnego|LUN, VMName|
|Procent użytych środków na we/wy na dysku systemu operacyjnego|Tak|Procent środków na we/wy na dysku systemu operacyjnego|Procent|Średnia|Procent używanych do tej pory środków na we/wy dysków systemu operacyjnego|LUN, VMName|
|Bajty zapisu dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s|BytesPerSecond|Średnia|Bajty/s zapisywane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|VMName|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s|CountPerSecond|Średnia|Zapis we/wy na dysku z jednego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Przepływy wychodzące|Tak|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku wychodzącym (ruch wychodzący z maszyny wirtualnej)|VMName|
|Maksymalna szybkość tworzenia przepływów wychodzących|Tak|Maksymalna szybkość tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|VMName|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie w użyciu przez maszyny wirtualne|VMName|
|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Tak|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Procent|Średnia|Trafienie w pamięci podręcznej dysku danych w awarii Premium|LUN, VMName|
|Chybienie odczytu pamięci podręcznej dysku danych w awarii Premium|Tak|Chybienie odczytu pamięci podręcznej dysku danych w awarii Premium|Procent|Średnia|Chybienie odczytu pamięci podręcznej dysku danych w awarii Premium|LUN, VMName|
|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Tak|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Procent|Średnia|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|VMName|
|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Tak|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Procent|Średnia|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|VMName|
|Procent użycia przepustowości w pamięci podręcznej maszyny wirtualnej|Tak|Procent użycia przepustowości w pamięci podręcznej maszyny wirtualnej|Procent|Średnia|Procent przepustowości dysku w pamięci podręcznej używanej przez maszynę wirtualną|VMName|
|Procent zużytych we/wy na pamięć podręczną maszyn wirtualnych|Tak|Procent zużytych we/wy na pamięć podręczną maszyn wirtualnych|Procent|Średnia|Procent buforowanych danych we/wy na dysku zużytych przez maszynę wirtualną|VMName|
|Procent zużytej przepustowości bez pamięci maszyny wirtualnej|Tak|Procent użycia przepustowości bez pamięci maszyny wirtualnej|Procent|Średnia|Procent nieukończanej przepustowości dysku używanej przez maszynę wirtualną|VMName|
|Procent zużytych niecachowanych wartości we/wy na dysku maszyny wirtualnej|Tak|Procent zużytych niecachowanych wartości we/wy na dysku maszyny wirtualnej|Procent|Średnia|Procent niecachowanych danych we/wy na dysku zużytych przez maszynę wirtualną|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Zużyte środki na procesor CPU|Tak|Zużyte środki na procesor CPU|Liczba|Średnia|Łączna liczba środków zużywanych przez maszynę wirtualną. Dostępne tylko na maszynach wirtualnych serii B z serii B z serii Burst|Brak wymiarów|
|Pozostałe środki na procesor CPU|Tak|Pozostałe środki na procesor CPU|Liczba|Średnia|Łączna liczba środków dostępnych dla serii. Dostępne tylko na maszynach wirtualnych serii B z serii B z serii Burst|Brak wymiarów|
|Procent użycia przepustowości dysku danych|Tak|Procent użycia przepustowości dysku danych|Procent|Średnia|Procent zużytej przepustowości dysku danych na minutę|Jednostki lun|
|Procent zużytych wartości we/wy na dysku danych|Tak|Procent zużytych wartości we/wy na dysku danych|Procent|Średnia|Procent zużytych we/wy dysku danych na minutę|Jednostki lun|
|Maksymalna przepustowość dysku danych z serii|Tak|Maksymalna przepustowość dysku danych z serii|Liczba|Średnia|Maksymalna liczba bajtów na sekundę, które dysk danych może osiągnąć dzięki zwiększaniu przepustowości|Jednostki lun|
|Maksymalna liczba we/wy na dysku danych z serii|Tak|Maksymalna liczba we/wy na dysku danych z serii|Liczba|Średnia|Maksymalny rozmiar dysku danych IOPS można osiągnąć przy użyciu serii|Jednostki lun|
|Głębokość kolejki dysku danych|Tak|Głębokość kolejki dysku danych|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostki lun|
|Bajty odczytu dysku danych/s|Tak|Bajty odczytu dysku danych/s|BytesPerSecond|Średnia|Bajty/s odczytu z jednego dysku w okresie monitorowania|Jednostki lun|
|Operacje odczytu z dysku danych/s|Tak|Operacje odczytu z dysku danych/s|CountPerSecond|Średnia|Odczyt IOPS z pojedynczego dysku w okresie monitorowania|Jednostki lun|
|Przepustowość docelowa dysku danych|Tak|Przepustowość docelowa dysku danych|Liczba|Średnia|Bajty bazowe na sekundę przepływności dysk danych może osiągnąć bez zwiększenia|Jednostki lun|
|Docelowa wartość we/wy dysku danych|Tak|Docelowa wartość we/wy dysku danych|Liczba|Średnia|Dysk danych podstawowej IOPS może osiągnąć bez serii|Jednostki lun|
|Procent użytych środków na wzrost w bps na dysku danych|Tak|Procent użytych środków na wzrost w bps na dysku danych|Procent|Średnia|Procent używanych do tej pory środków na przepustowość dysków Data Disk|Jednostki lun|
|Procent użytych dysków danych w przypadku serii środków we/wy|Tak|Procent użytych dysków danych w przypadku serii środków we/wy|Procent|Średnia|Procent używanych do tej pory środków na we/wy dla dysku danych|Jednostki lun|
|Bajty zapisu dysku danych/s|Tak|Bajty zapisu dysku danych/s|BytesPerSecond|Średnia|Bajty/s zapisywane na jednym dysku w okresie monitorowania|Jednostki lun|
|Operacje zapisu na dysku danych/s|Tak|Operacje zapisu na dysku danych/s|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period (Zapis we/wy zapisu na dysku z jednego dysku w okresie monitorowania)|Jednostki lun|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Disk Read IOPS|Brak wymiarów|
|Bajty zapisu na dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisane na dysku w okresie monitorowania|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Disk Write IOPS|Brak wymiarów|
|Przepływy przychodzące|Tak|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Maksymalna szybkość tworzenia przepływów przychodzących|Tak|Maksymalna szybkość tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Sieć — wejście|Tak|Sieć rozliczana (przestarzałe)|Bajty|Łącznie|Liczba rozliczanych bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|Brak wymiarów|
|Łączna liczba sieci|Tak|Łączna liczba sieci|Bajty|Łącznie|Liczba bajtów odebranych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak wymiarów|
|Sieć — wyjście|Tak|Rozliczane z sieci (przestarzałe)|Bajty|Łącznie|Liczba rozliczanych bajtów na wszystkich interfejsach sieciowych według maszyn wirtualnych (ruch wychodzący) (przestarzałe)|Brak wymiarów|
|Suma ruchu wychodzącego sieci|Tak|Suma ruchu wychodzącego sieci|Bajty|Łącznie|Liczba bajtów wychodzących we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Brak wymiarów|
|Procent użycia przepustowości dysku systemu operacyjnego|Tak|Procent użycia przepustowości dysku systemu operacyjnego|Procent|Średnia|Procent zużytej przepustowości dysku systemu operacyjnego na minutę|Jednostki lun|
|Procent zużytych wartości we/wy na dysku systemu operacyjnego|Tak|Procent zużytych wartości we/wy na dysku systemu operacyjnego|Procent|Średnia|Procent zużytych we/wy dysku systemu operacyjnego na minutę|Jednostki lun|
|Maksymalna przepustowość dysku systemu operacyjnego|Tak|Maksymalna przepustowość dysku systemu operacyjnego|Liczba|Średnia|Maksymalna liczba bajtów na sekundę, które dysk systemu operacyjnego przepływności może osiągnąć dzięki zwiększaniu|Jednostki lun|
|Maksymalna liczba we/wy na dysku systemu operacyjnego|Tak|Maksymalna liczba we/wy na dysku systemu operacyjnego|Liczba|Średnia|Maksymalna liczba we/wy na dysku systemu operacyjnego w trybie "bursting"|Jednostki lun|
|Głębokość kolejki dysku systemu operacyjnego|Tak|Głębokość kolejki dysku systemu operacyjnego|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu dysku systemu operacyjnego/s|BytesPerSecond|Średnia|Bajty/s odczytane z jednego dysku w okresie monitorowania dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Tak|Operacje odczytu z dysku systemu operacyjnego/s|CountPerSecond|Średnia|Odczyt IOPS z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Przepustowość miejsca docelowego dysku systemu operacyjnego|Tak|Przepustowość docelowa dysku systemu operacyjnego|Liczba|Średnia|Bajty bazowe na sekundę przepływności Dysk systemu operacyjnego może osiągnąć bez zwiększenia|Jednostki lun|
|Docelowa wartość we/wy dysku systemu operacyjnego|Tak|Docelowa wartość we/wy dysku systemu operacyjnego|Liczba|Średnia|Podstawowy dysk systemu operacyjnego we/wy na dane można osiągnąć bez serii|Jednostki lun|
|Procent środków na sypkich bitów na bity w bitach na dysku systemu operacyjnego|Tak|Procent środków na sypkich bitów na bity w bitach na dysku systemu operacyjnego|Procent|Średnia|Procent używanych do tej pory środków na przepustowość dysku systemu operacyjnego|Jednostki lun|
|Procent użytych środków na we/wy na dysku systemu operacyjnego|Tak|Procent użytych środków na we/wy na dysku systemu operacyjnego|Procent|Średnia|Procent używanych do tej pory środków na we/wy na dysku systemu operacyjnego|Jednostki lun|
|Bajty zapisu dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s|BytesPerSecond|Średnia|Bajty/s zapisywane na jednym dysku w okresie monitorowania dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s|CountPerSecond|Średnia|Zapis we/wy na dysku z jednego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Przepływy wychodzące|Tak|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku wychodzącym (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Maksymalna szybkość tworzenia przepływów wychodzących|Tak|Maksymalna szybkość tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przydzielonych jednostek obliczeniowych, które są obecnie w użyciu przez maszyny wirtualne|Brak wymiarów|
|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Tak|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Procent|Średnia|Trafienie w pamięci podręcznej dysku danych w awarii Premium|Jednostki lun|
|Chybienie odczytu pamięci podręcznej dysku danych w awarii Premium|Tak|Chybienie odczytu pamięci podręcznej dysku danych w awarii Premium|Procent|Średnia|Chybienie odczytu pamięci podręcznej dysku danych w awarii Premium|Jednostki lun|
|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Tak|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Procent|Średnia|Trafienie w pamięci podręcznej dysku systemu operacyjnego Premium|Brak wymiarów|
|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Tak|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Procent|Średnia|Chybienie odczytu pamięci podręcznej dysku systemu operacyjnego Premium|Brak wymiarów|
|Procent użycia przepustowości w pamięci podręcznej maszyny wirtualnej|Tak|Procent użycia przepustowości w pamięci podręcznej maszyny wirtualnej|Procent|Średnia|Procent przepustowości dysku w pamięci podręcznej używanej przez maszynę wirtualną|Brak wymiarów|
|Procent zużytych we/wy na pamięć podręczną maszyn wirtualnych|Tak|Procent zużytych we/wy na pamięć podręczną maszyn wirtualnych|Procent|Średnia|Procent buforowanych danych we/wy na dysku zużytych przez maszynę wirtualną|Brak wymiarów|
|Procent użycia przepustowości bez pamięci maszyny wirtualnej|Tak|Procent zużytej przepustowości bez pamięci maszyny wirtualnej|Procent|Średnia|Procent przepustowości dysku bez pamięci używanej przez maszynę wirtualną|Brak wymiarów|
|Procent zużytych niecachowanych wartości we/wy na dysku maszyny wirtualnej|Tak|Procent zużytych niecachowanych wartości we/wy na dysku maszyny wirtualnej|Procent|Średnia|Procent niecachowanych danych we/wy na dysku zużytych przez maszynę wirtualną|Brak wymiarów|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Użycie procesora CPU|Tak|Użycie procesora|Liczba|Średnia|Użycie procesora CPU na wszystkich rdzeniach w milirdzeniach.|containerName (nazwa kontenera)|
|MemoryUsage (Użycie pamięci)|Tak|Użycie pamięci|Bajty|Średnia|Łączne użycie pamięci w bajtach.|containerName (nazwa kontenera)|
|NetworkBytesReceivedPerSecond|Tak|Liczba odebranych bajtów sieciowych na sekundę|Bajty|Średnia|Liczba odebranych bajtów sieciowych na sekundę.|Brak wymiarów|
|NetworkBytesTransmittedPerSecond|Tak|Bajty sieciowe przesyłane na sekundę|Bajty|Średnia|Bajty sieciowe przesyłane na sekundę.|Brak wymiarów|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Tak|Czas procesora CPU puli agentów|Sekundy|Łącznie|Czas procesora CPU puli agentów w sekundach|Brak wymiarów|
|RunDuration|Tak|Czas trwania uruchomienia|Milisekund|Łącznie|Czas trwania uruchomienia w milisekundach|Brak wymiarów|
|SuccessfulPullCount|Tak|Liczba pomyślnych ściągnięć|Liczba|Średnia|Liczba pomyślnych ściągnięć obrazów|Brak wymiarów|
|SuccessfulPushCount|Tak|Liczba pomyślnych wypchnięć|Liczba|Średnia|Liczba pomyślnych wypchnięć obrazów|Brak wymiarów|
|TotalPullCount|Tak|Łączna liczba ściągnięć|Liczba|Średnia|Łączna liczba ściąganych obrazów|Brak wymiarów|
|TotalPushCount|Tak|Łączna liczba wypchnięć|Liczba|Średnia|Łączna liczba wypchnięć obrazów|Brak wymiarów|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|Nie|Żądania inflight|Liczba|Średnia|Maksymalna liczba aktualnie używanych żądań inflight na serwerze apiserver na rodzaj żądania w ciągu ostatniej sekundy|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|Nie|Kondycja klastra|Liczba|Średnia|Określa, czy usługa autoskalowania klastra będzie akcję w klastrze||
|cluster_autoscaler_scale_down_in_cooldown|Nie|Czas ochłodzenia skalowania w dół|Liczba|Średnia|Określa, czy skalowanie w dół jest w czasie ochłodzenia — w tym czasie nie zostaną usunięte żadne węzły||
|cluster_autoscaler_unneeded_nodes_count|Nie|Niepotrzebnych węzłów|Liczba|Średnia|Cluster auotscaler oznacza te węzły jako kandydatów do usunięcia i są ostatecznie usuwane||
|cluster_autoscaler_unschedulable_pods_count|Nie|Nieplanowane zasobniki|Liczba|Średnia|Liczba zasobników, które są obecnie nieplanowane w klastrze||
|kube_node_status_allocatable_cpu_cores|Nie|Łączna liczba dostępnych rdzeni procesora CPU w klastrze zarządzanym|Liczba|Średnia|Łączna liczba dostępnych rdzeni procesora CPU w klastrze zarządzanym||
|kube_node_status_allocatable_memory_bytes|Nie|Łączna ilość dostępnej pamięci w klastrze zarządzanym|Bajty|Średnia|Łączna ilość dostępnej pamięci w klastrze zarządzanym||
|kube_node_status_condition|Nie|Stany dla różnych warunków węzła|Liczba|Średnia|Stany dla różnych warunków węzła|warunek, stan, stan2, węzeł|
|kube_pod_status_phase|Nie|Liczba zasobników według fazy|Liczba|Średnia|Liczba zasobników według fazy|phase, namespace, pod|
|kube_pod_status_ready|Nie|Liczba zasobników w stanie Gotowe|Liczba|Średnia|Liczba zasobników w stanie Gotowe|przestrzeń nazw, zasobnik, warunek|
|node_cpu_usage_millicores|Tak|Milirdzeni użycia procesora CPU|MilliCores|Średnia|Zagregowana miara użycia procesora CPU w milirdzeniach w klastrze|node, nodepool|
|node_cpu_usage_percentage|Tak|Procent użycia procesora CPU|Procent|Średnia|Zagregowane średnie wykorzystanie procesora CPU mierzone w procentach w klastrze|node, nodepool|
|node_disk_usage_bytes|Tak|Dysk używany w bajtach|Bajty|Średnia|Miejsce na dysku używane w bajtach przez urządzenie|node, nodepool, device|
|node_disk_usage_percentage|Tak|Procent użytego dysku|Procent|Średnia|Miejsce na dysku używane w procentach przez urządzenie|node, nodepool, device|
|node_memory_rss_bytes|Tak|Bajty FUNKCJI RSS pamięci|Bajty|Średnia|Pamięć rss kontenera używana w bajtach|node, nodepool|
|node_memory_rss_percentage|Tak|Procent rss pamięci|Procent|Średnia|Pamięć rss kontenera używana w procentach|node, nodepool|
|node_memory_working_set_bytes|Tak|Bajty zestawu roboczego pamięci|Bajty|Średnia|Pamięć zestawu roboczego kontenera używana w bajtach|node, nodepool|
|node_memory_working_set_percentage|Tak|Procent zestawu roboczego pamięci|Procent|Średnia|Użycie pamięci zestawu roboczego kontenera w procentach|node, nodepool|
|node_network_in_bytes|Tak|Sieć w bajtach|Bajty|Średnia|Bajty odebrane przez sieć|node, nodepool|
|node_network_out_bytes|Tak|Bajty wychodzące sieci|Bajty|Średnia|Bajty przesyłane przez sieć|node, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|FailedRequests|Tak|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Pobiera dostępne dzienniki dla niestandardowych dostawców zasobów|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Tak|Żądania pomyślne|Liczba|Łącznie|Żądania wykonane pomyślnie przez dostawcę niestandardowego|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AvailableCapacity|Tak|Dostępna pojemność|Bajty|Średnia|Dostępna pojemność w bajtach w okresie raportowania.|Brak wymiarów|
|BytesUploadedToCloud|Tak|Przekazane bajty w chmurze (urządzenie)|Bajty|Średnia|Łączna liczba bajtów przekazywanych na platformę Azure z urządzenia w okresie raportowania.|Brak wymiarów|
|BytesUploadedToCloudPerShare|Tak|Przekazane bajty w chmurze (udział)|Bajty|Średnia|Łączna liczba bajtów przekazywanych na platformę Azure z udziału w okresie raportowania.|Udostępnij|
|CloudReadThroughput|Tak|Przepływność pobierania w chmurze|BytesPerSecond|Średnia|Przepływność pobierania w chmurze na platformę Azure w okresie raportowania.|Brak wymiarów|
|CloudReadThroughputPerShare|Tak|Przepływność pobierania w chmurze (udział)|BajtyPerSecond|Średnia|Przepływność pobierania na platformę Azure z udziału w okresie raportowania.|Udostępnij|
|CloudUploadThroughput|Tak|Przepływność przekazywania do chmury|BajtyPerSecond|Średnia|Przepływność przekazywania w chmurze na platformę Azure w okresie raportowania.|Brak wymiarów|
|CloudUploadThroughputPerShare|Tak|Przepływność przekazywania do chmury (udział)|BajtyPerSecond|Średnia|Przepływność przekazywania na platformę Azure z udziału w okresie raportowania.|Udostępnij|
|Funkcja HyperVMemoryUtilization|Tak|Obliczenia brzegowe — użycie pamięci|Procent|Średnia|Ilość pamięci RAM w użyciu|InstanceName|
|HyperVVirtualProcessorUtilization|Tak|Obliczenia brzegowe — procentowe użycie procesora CPU|Procent|Średnia|Procent użycia procesora CPU|InstanceName|
|NICReadThroughput|Tak|Przepływność odczytu (sieć)|BajtyPerSecond|Średnia|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie raportowania dla wszystkich woluminów w bramie.|InstanceName|
|NICWriteThroughput|Tak|Przepływność zapisu (sieć)|BytesPerSecond|Średnia|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie raportowania dla wszystkich woluminów w bramie.|InstanceName|
|TotalCapacity|Tak|Całkowita pojemność|Bajty|Średnia|Całkowita pojemność|Brak wymiarów|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DataAssetCount|Tak|Utworzone zasoby danych|Liczba|Maksimum|Liczba utworzonych zasobów danych|DataAssetName|
|PipelineCount|Tak|Utworzone potoki|Liczba|Maksimum|Liczba utworzonych potoków|Nazwa potoku|
|ProposalCount|Tak|Utworzone propozycje|Liczba|Maksimum|Liczba utworzonych propozycji|ProposalName (Nazwa propozycji)|
|ScriptCount|Tak|Utworzone skrypty|Liczba|Maksimum|Liczba utworzonych skryptów|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|FailedRuns|Tak|Nieudane przebiegi|Liczba|Łącznie||pipelineName, activityName|
|SuccessfulRuns|Tak|Pomyślne przebiegi|Liczba|Łącznie||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fabryki

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Tak|Anulowane działanie uruchamia metryki|Liczba|Łącznie||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Tak|Nieudane działanie uruchamia metryki|Liczba|Łącznie||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Tak|Metryki powodzenia działania|Liczba|Łącznie||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Tak|Całkowity rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak wymiarów|
|IntegrationRuntimeAvailableMemory|Tak|Dostępna pamięć środowiska Integration Runtime|Bajty|Średnia||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Tak|Liczba dostępnych węzłów środowiska Integration Runtime|Liczba|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Tak|Czas trwania kolejki środowiska Integration Runtime|Sekundy|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Tak|Wykorzystanie procesora CPU środowiska Integration Runtime|Procent|Średnia||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Tak|Długość kolejki środowiska Integration Runtime|Liczba|Średnia||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Tak|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak wymiarów|
|MaxAllowedResourceCount|Tak|Maksymalna liczba dozwolonych jednostek|Liczba|Maksimum||Brak wymiarów|
|PipelineCancelledRuns|Tak|Metryki anulowanych uruchomień potoku|Liczba|Łącznie||FailureType, Name|
|PipelineElapsedTimeRuns|Tak|Metryki przebiegów potoku czasu, który upłynął|Liczba|Łącznie||RunId, Name|
|PipelineFailedRuns|Tak|Metryki nieudanych uruchomień potoku|Liczba|Łącznie||FailureType, Name|
|PipelineSucceededRuns|Tak|Metryki powodzenia przebiegów potoku|Liczba|Łącznie||FailureType, Name|
|ResourceCount|Tak|Łączna liczba jednostek|Liczba|Maksimum||Brak wymiarów|
|SSISIntegrationRuntimeStartCancel|Tak|Anulowane metryki uruchamiania środowiska SSIS Integration Runtime|Liczba|Łącznie||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Tak|Metryki uruchamiania środowiska SSIS Integration Runtime zakończyły się niepowodzeniem|Liczba|Łącznie||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Tak|Metryki uruchamiania środowiska SSIS Integration Runtime — powodzenie|Liczba|Łącznie||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|Tak|Zablokowane metryki zatrzymania środowiska SSIS Integration Runtime|Liczba|Łącznie||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Tak|Metryki zatrzymania środowiska SSIS Integration Runtime — powodzenie|Liczba|Łącznie||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Tak|Anulowane metryki wykonywania pakietu SSIS|Liczba|Łącznie||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Tak|Metryki wykonywania pakietu SSIS zakończyły się niepowodzeniem|Liczba|Łącznie||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Tak|Metryki wykonania pakietu SSIS zakończyły się pomyślnie|Liczba|Łącznie||IntegrationRuntimeName|
|TriggerCancelledRuns|Tak|Metryki przebiegów anulowanych wyzwalaczy|Liczba|Łącznie||Name, FailureType|
|TriggerFailedRuns|Tak|Metryki przebiegów wyzwalacza, które zakończyły się niepowodzeniem|Liczba|Łącznie||Name, FailureType|
|TriggerSucceededRuns|Tak|Metryki przebiegów pomyślnie uruchomionych wyzwalaczy|Liczba|Łącznie||Name, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Tak|Anulowany czas AU|Sekundy|Łącznie|Łączny czas AU dla anulowanych zadań.|Brak wymiarów|
|JobAUEndedFailure|Tak|Czas au niepowodzenie|Sekundy|Łącznie|Łączny czas AU dla zadań, które zakończyły się niepowodzeniem.|Brak wymiarów|
|JobAUEndedSuccess|Tak|Pomyślny czas AU|Sekundy|Łącznie|Łączny czas AU dla pomyślnych zadań.|Brak wymiarów|
|JobEndedCancelled|Tak|Anulowane zadania|Liczba|Łącznie|Liczba anulowanych zadań.|Brak wymiarów|
|JobEndedFailure|Tak|Zadania, które zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba zadań, które zakończyły się niepowodzeniem.|Brak wymiarów|
|JobEndedSuccess|Tak|Pomyślne zadania|Liczba|Łącznie|Liczba pomyślnych zadań.|Brak wymiarów|
|JobStage|Tak|Zadania na etapie|Liczba|Łącznie|Liczba zadań w każdym etapie.|Brak wymiarów|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dataread|Tak|Odczytywanie danych|Bajty|Łącznie|Łączna ilość danych odczytanych z konta.|Brak wymiarów|
|DataWritten|Tak|Zapisane dane|Bajty|Łącznie|Łączna ilość danych zapisywanych na koncie.|Brak wymiarów|
|ReadRequests|Tak|Żądania odczytu|Liczba|Łącznie|Liczba żądań odczytu danych do konta.|Brak wymiarów|
|TotalStorage|Tak|Łączny rozmiar magazynu|Bajty|Maksimum|Łączna ilość danych przechowywanych na koncie.|Brak wymiarów|
|WriteRequests|Tak|Żądania zapisu|Liczba|Łącznie|Liczba żądań zapisu danych na koncie.|Brak wymiarów|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Tak|Odebrane migawki udziału zakończyły się niepowodzeniem|Liczba|Liczba|Liczba odebranych migawek udziału, które zakończyły się niepowodzeniem na koncie|Brak wymiarów|
|FailedShareSynchronizations|Tak|Nieudane migawki wysłanego udziału|Liczba|Liczba|Liczba nieudanych migawek wysłanych udziałów na koncie|Brak wymiarów|
|ShareCount|Tak|Wysłane udziały|Liczba|Maksimum|Liczba wysłanych udziałów na koncie|Nazwaudziału|
|ShareSubscriptionCount|Tak|Odebrane udziały|Liczba|Maksimum|Liczba odebranych udziałów na koncie|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Tak|Odebrane migawki udziału zakończyły się pomyślnie|Liczba|Liczba|Liczba odebranych migawek udziału zakończyło się pomyślnie na koncie|Brak wymiarów|
|SucceededShareSynchronizations|Tak|Migawki z pomyślnie wysłanym udziałem|Liczba|Liczba|Liczba pomyślnie wysłanych migawek udziału na koncie|Brak wymiarów|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|io_consumption_percent|Tak|Procent we/wy|Procent|Średnia|Procent we/wy|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Wychodzące sieci między aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w obrębie aktywnych połączeń|Brak wymiarów|
|seconds_behind_master|Tak|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak wymiarów|
|serverlog_storage_limit|Tak|Limit magazynu dzienników serwera|Bajty|Maksimum|Limit magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_percent|Tak|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Tak|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|storage_limit|Tak|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|aborted_connections|Tak|Przerwane połączenia|Liczba|Łącznie|Przerwane połączenia|Brak wymiarów|
|active_connections|Tak|Aktywne połączenia|Liczba|Maksimum|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Maksimum|Używany magazyn kopii zapasowych|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU hosta|Procent|Maksimum|Procent użycia procesora CPU hosta|Brak wymiarów|
|io_consumption_percent|Tak|Procent we/wy|Procent|Maksimum|Procent we/wy|Brak wymiarów|
|memory_percent|Tak|Procent pamięci hosta|Procent|Maksimum|Procent pamięci hosta|Brak wymiarów|
|network_bytes_egress|Tak|Sieć hosta wychodzącego|Bajty|Łącznie|Ruch wychodzący sieci hosta w bajtach|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć hosta w programie|Bajty|Łącznie|Ruch przychodzący sieci hosta w bajtach|Brak wymiarów|
|Zapytania|Tak|Zapytania|Liczba|Łącznie|Zapytania|Brak wymiarów|
|replication_lag|Tak|Opóźnienie replikacji w sekundach|Sekundy|Maksimum|Opóźnienie replikacji w sekundach|Brak wymiarów|
|storage_limit|Tak|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Maksimum|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Maksimum|Używany magazyn|Brak wymiarów|
|total_connections|Tak|Łączna liczba połączeń|Liczba|Łącznie|Łączna liczba połączeń|Brak wymiarów|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|io_consumption_percent|Tak|Procent we/wy|Procent|Średnia|Procent we/wy|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Wychodzące sieci między aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|seconds_behind_master|Tak|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak wymiarów|
|serverlog_storage_limit|Tak|Limit magazynu dzienników serwera|Bajty|Maksimum|Limit magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_percent|Tak|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Tak|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|storage_limit|Tak|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|connections_succeeded|Tak|Pomyślnie nasyłane połączenia|Liczba|Łącznie|Pomyślnie nasyłane połączenia|Brak wymiarów|
|cpu_credits_consumed|Tak|Zużyte środki na procesor CPU|Liczba|Średnia|Łączna liczba środków zużywanych przez serwer bazy danych|Brak wymiarów|
|cpu_credits_remaining|Tak|Pozostałe środki na procesor CPU|Liczba|Średnia|Łączna liczba środków dostępnych dla serii|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|disk_queue_depth|Tak|Głębokość kolejki dysku|Liczba|Średnia|Liczba zaległych operacji we/wy na dysku danych|Brak wymiarów|
|Iops|Tak|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak wymiarów|
|maximum_used_transactionIDs|Tak|Maksymalne używane identyfikatory transakcji|Liczba|Średnia|Maksymalne używane identyfikatory transakcji|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Wychodzące sieci między aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|read_iops|Tak|Odczyt IOPS|Liczba|Średnia|Liczba operacji we/wy odczytu dysku danych na sekundę|Brak wymiarów|
|read_throughput|Tak|Liczba bajtów odczytu/s przepływności|Liczba|Średnia|Bajty odczytane z dysku danych w okresie monitorowania|Brak wymiarów|
|storage_free|Tak|Ilość wolnego miejsca do magazynowania|Bajty|Średnia|Ilość wolnego miejsca do magazynowania|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|
|txlogs_storage_used|Tak|Użyty magazyn dzienników transakcji|Bajty|Średnia|Użyty magazyn dzienników transakcji|Brak wymiarów|
|write_iops|Tak|Write IOPS|Liczba|Średnia|Liczba operacji zapisu we/wy dysku danych na sekundę|Brak wymiarów|
|write_throughput|Tak|Liczba bajtów zapisu przepływności na sekundę|Liczba|Średnia|Bajty zapisywane na sekundę na dysku danych w okresie monitorowania|Brak wymiarów|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|ServerName|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|ServerName|
|Iops|Tak|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|ServerName|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|ServerName|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Wychodzące sieci między aktywnymi połączeniami|ServerName|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w obrębie aktywnych połączeń|ServerName|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|ServerName|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|io_consumption_percent|Tak|Procent we/wy|Procent|Średnia|Procent we/wy|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Wychodzące sieci między aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|pg_replica_log_delay_in_bytes|Tak|Maksymalne opóźnienie między replikami|Bajty|Maksimum|Opóźnienie w bajtach repliki o najczęściej opóźnianych opóźnieniach|Brak wymiarów|
|pg_replica_log_delay_in_seconds|Tak|Opóźnienie repliki|Sekundy|Maksimum|Opóźnienie repliki w sekundach|Brak wymiarów|
|serverlog_storage_limit|Tak|Limit magazynu dzienników serwera|Bajty|Maksimum|Limit magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_percent|Tak|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Tak|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|storage_limit|Tak|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|Iops|Tak|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Wychodzące sieci między aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w obrębie aktywnych połączeń|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Tak|żądana stawka użycia|Procent|Średnia|żądana stawka użycia|Brak wymiarów|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Tak|Porzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak wymiarów|
|c2d.commands.egress.complete.success|Tak|Ukończono dostarczanie komunikatów C2D|Liczba|Łącznie|Liczba pomyślnych dostaw komunikatów z chmury do urządzenia przez urządzenie|Brak wymiarów|
|c2d.commands.egress.reject.success|Tak|Odrzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak wymiarów|
|c2d.methods.failure|Tak|Nieudane wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań metody bezpośredniej.|Brak wymiarów|
|c2d.methods.requestSize|Tak|Rozmiar żądania wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych żądań metody bezpośredniej.|Brak wymiarów|
|c2d.methods.responseSize|Tak|Rozmiar odpowiedzi wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych odpowiedzi metody bezpośredniej.|Brak wymiarów|
|c2d.methods.success|Tak|Pomyślne wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak wymiarów|
|c2d.twin.read.failure|Tak|Nieudane odczyty bliźniaczej reprezentacji z back end|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów bliźniaczej reprezentacji zainicjowanych przez zatyczek.|Brak wymiarów|
|c2d.twin.read.size|Tak|Rozmiar odpowiedzi bliźniaczych odczytów z zadomowień|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych odczytów bliźniaczych reprezentacji inicjowanych przez zaczekiw.|Brak wymiarów|
|c2d.twin.read.success|Tak|Pomyślne odczyty bliźniaczej reprezentacji z zadomowień|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów bliźniaczej reprezentacji inicjowanych przez zaczeki.|Brak wymiarów|
|c2d.twin.update.failure|Tak|Nieudane aktualizacje bliźniaczych reprezentacji z zadomowień|Liczba|Łącznie|Liczba wszystkich nieudanych aktualizacji bliźniaczych reprezentacji inicjowanych przez zaczekiw.|Brak wymiarów|
|c2d.twin.update.size|Tak|Rozmiar aktualizacji bliźniaczych reprezentacji z zadomowień|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji inicjowanych przez zaczekiw.|Brak wymiarów|
|c2d.twin.update.success|Tak|Pomyślne aktualizacje bliźniaczych reprezentacji z zadomowień|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji inicjowanych przez zaczekię.|Brak wymiarów|
|C2DMessagesExpired|Tak|Wygasłe komunikaty C2D|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak wymiarów|
|Konfiguracji|Tak|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracji|Brak wymiarów|
|connectedDeviceCount|Tak|Połączone urządzenia|Liczba|Średnia|Liczba urządzeń połączonych z centrum IoT|Brak wymiarów|
|d2c.endpoints.egress.builtIn.events|Tak|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Liczba|Łącznie|Ile razy usługa IoT Hub pomyślnie dostarczała komunikaty do wbudowanego punktu końcowego (komunikatów/zdarzeń).|Brak wymiarów|
|d2c.endpoints.egress.eventHubs|Tak|Routing: komunikaty dostarczane do centrum zdarzeń|Liczba|Łącznie|Ile razy usługa routingu IoT Hub pomyślnie dostarczała komunikaty do punktów końcowych centrum zdarzeń.|Brak wymiarów|
|d2c.endpoints.egress.serviceBusQueues|Tak|Routing: komunikaty dostarczane do Service Bus Queue|Liczba|Łącznie|Liczba pomyślnych IoT Hub pomyślnie dostarczonych komunikatów do Service Bus końcowych kolejki.|Brak wymiarów|
|d2c.endpoints.egress.serviceBusTopics|Tak|Routing: komunikaty dostarczane do Service Bus tematu|Liczba|Łącznie|Liczba pomyślnych IoT Hub pomyślnie dostarczonych komunikatów do punktów końcowych Service Bus tematu.|Brak wymiarów|
|d2c.endpoints.egress.storage|Tak|Routing: komunikaty dostarczane do magazynu|Liczba|Łącznie|Ile razy routing IoT Hub pomyślnie dostarczał komunikaty do punktów końcowych magazynu.|Brak wymiarów|
|d2c.endpoints.egress.storage.blobs|Tak|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Ile razy usługa IoT Hub dostarczała obiekty blob do punktów końcowych magazynu.|Brak wymiarów|
|d2c.endpoints.egress.storage.bytes|Tak|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub dostarczanych do punktów końcowych magazynu.|Brak wymiarów|
|d2c.endpoints.latency.builtIn.events|Tak|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem IoT Hub i przychodzącym komunikatem telemetrii do wbudowanego punktu końcowego (komunikatów/zdarzeń).|Brak wymiarów|
|d2c.endpoints.latency.eventHubs|Tak|Routing: opóźnienie komunikatów dla centrum zdarzeń|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem a IoT Hub do punktu końcowego centrum zdarzeń.|Brak wymiarów|
|d2c.endpoints.latency.serviceBusQueues|Tak|Routing: opóźnienie komunikatów dla Service Bus kolejki|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem a IoT Hub komunikatów telemetrycznych do punktu końcowego Service Bus kolejki.|Brak wymiarów|
|d2c.endpoints.latency.serviceBusTopics|Tak|Routing: opóźnienie komunikatów dla Service Bus tematu|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym i IoT Hub komunikatów telemetrycznych do punktu końcowego Service Bus tematu.|Brak wymiarów|
|d2c.endpoints.latency.storage|Tak|Routing: opóźnienie komunikatów dla magazynu|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem a IoT Hub i komunikatem telemetrii przychodzącym do punktu końcowego magazynu.|Brak wymiarów|
|d2c.telemetry.egress.dropped|Tak|Routing: porzucone komunikaty telemetryczne |Liczba|Łącznie|Liczba porzucanych komunikatów przez routing IoT Hub z powodu nieumarłych punktów końcowych. Ta wartość nie zlicza komunikatów dostarczonych do trasy powrotu, ponieważ porzucone komunikaty nie są tam dostarczane.|Brak wymiarów|
|d2c.telemetry.egress.fallback|Tak|Routing: komunikaty dostarczane do rezerwy|Liczba|Łącznie|Ile razy routing IoT Hub do punktu końcowego skojarzonego z trasą rezerwową.|Brak wymiarów|
|d2c.telemetry.egress.invalid|Tak|Routing: niezgodne komunikaty telemetryczne|Liczba|Łącznie|Liczba razy, IoT Hub routing nie dostarczał komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak wymiarów|
|d2c.telemetry.egress.orphaned|Tak|Routing: oddzielone komunikaty telemetrii |Liczba|Łącznie|Liczba oddzielonych komunikatów przez routing IoT Hub, ponieważ nie są one zgodne z żadnymi regułami rozsyłania (w tym regułą rezerwy). |Brak wymiarów|
|d2c.telemetry.egress.success|Tak|Routing: dostarczane komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnych komunikatów dostarczonych do wszystkich punktów końcowych przy użyciu IoT Hub routingu. Jeśli komunikat jest przekierowyny do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli komunikat jest dostarczany do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak wymiarów|
|d2c.telemetry.ingress.allProtocol|Tak|Próby wysyłania komunikatów telemetrii|Liczba|Łącznie|Liczba komunikatów telemetrii urządzenie-chmura, które próbowano wysłać do centrum IoT|Brak wymiarów|
|d2c.telemetry.ingress.sendThrottle|Tak|Liczba błędów ograniczania przepustowości|Liczba|Łącznie|Liczba błędów ograniczania przepustowości spowodowanych ograniczeniami przepływności urządzenia|Brak wymiarów|
|d2c.telemetry.ingress.success|Tak|Wysłane komunikaty telemetryczne|Liczba|Łącznie|Liczba komunikatów telemetrii urządzenie-chmura wysłanych pomyślnie do centrum IoT|Brak wymiarów|
|d2c.twin.read.failure|Tak|Nieudane odczyty bliźniaczej reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów bliźniaczej reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.read.size|Tak|Rozmiar odpowiedzi bliźniaczych odczytów z urządzeń|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych odczytów bliźniaczej reprezentacji zainicjowanej przez urządzenie.|Brak wymiarów|
|d2c.twin.read.success|Tak|Pomyślne odczyty bliźniaczej reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów bliźniaczej reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.update.failure|Tak|Nieudane aktualizacje bliźniaczych reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych aktualizacji bliźniaczych reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.update.size|Tak|Rozmiar aktualizacji bliźniaczych reprezentacji z urządzeń|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji inicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.update.success|Tak|Pomyślne aktualizacje bliźniaczych reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|dailyMessageQuotaUsed|Tak|Łączna liczba użytych komunikatów|Liczba|Maksimum|Liczba użytych obecnie komunikatów łącznie|Brak wymiarów|
|deviceDataUsage|Tak|Łączne użycie danych urządzenia|Bajty|Łącznie|Bajty przesyłane do i z dowolnych urządzeń połączonych z serwisem IotHub|Brak wymiarów|
|deviceDataUsageV2|Tak|Łączne użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesyłane do i z dowolnych urządzeń połączonych z serwisem IotHub|Brak wymiarów|
|devices.connectedDevices.allProtocol|Tak|Połączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń połączonych z centrum IoT|Brak wymiarów|
|devices.totalDevices|Tak|Łączna liczba urządzeń (przestarzałe)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w centrum IoT|Brak wymiarów|
|EventGridDeliveries|Tak|Event Grid dostaw|Liczba|Łącznie|Liczba zdarzeń IoT Hub publikowanych w Event Grid. Użyj wymiaru Wynik dla liczby żądań pomyślnych i nieudanych. Wymiar EventType pokazuje typ zdarzenia ( https://aka.ms/ioteventgrid) .|Result, EventType|
|EventGridLatency|Tak|Event Grid opóźnienie|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) od czasu wygenerowania zdarzenia usługi IoT Hub do czasu opublikowania zdarzenia w Event Grid. Ta liczba jest średnią między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Typ zdarzenia|
|jobs.cancelJob.failure|Tak|Anulowanie zadania, które zakończyło się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań w celu anulowania zadania.|Brak wymiarów|
|jobs.cancelJob.success|Tak|Pomyślne anulowanie zadania|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań w celu anulowania zadania.|Brak wymiarów|
|jobs.completed|Tak|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak wymiarów|
|jobs.createDirectMethodJob.failure|Tak|Nieudane tworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich nieudanych zadań wywołania metody bezpośredniej.|Brak wymiarów|
|jobs.createDirectMethodJob.success|Tak|Pomyślne tworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich pomyślnych zadań wywołania metody bezpośredniej.|Brak wymiarów|
|jobs.createTwinUpdateJob.failure|Tak|Nieudane tworzenie zadań aktualizacji bliźniaczych reprezentacji|Liczba|Łącznie|Liczba wszystkich nieudanych zadań aktualizacji bliźniaczych.|Brak wymiarów|
|jobs.createTwinUpdateJob.success|Tak|Pomyślne tworzenie zadań aktualizacji bliźniaczych reprezentacji|Liczba|Łącznie|Liczba wszystkich pomyślnych zadań aktualizacji bliźniaczych reprezentacji.|Brak wymiarów|
|jobs.failed|Tak|Zadania, które zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich zadań, które zakończyły się niepowodzeniem.|Brak wymiarów|
|jobs.listJobs.failure|Tak|Nieudane wywołania zadań listy|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań zadań listy.|Brak wymiarów|
|jobs.listJobs.success|Tak|Pomyślne wywołania zadań listy|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań zadań listy.|Brak wymiarów|
|jobs.queryJobs.failure|Tak|Zapytania dotyczące zadań, które zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań zadań zapytań.|Brak wymiarów|
|jobs.queryJobs.success|Tak|Zapytania dotyczące pomyślnego zadania|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań zadań zapytań.|Brak wymiarów|
|RoutingDataSizeInBytesDelivered|Tak|Rozmiar komunikatu dostarczania routingu w bajtach (wersja zapoznawcza)|Bajty|Łącznie|Całkowity rozmiar w bajtach komunikatów dostarczonych przez centrum IoT do punktu końcowego. Wymiary EndpointName i EndpointType mogą być używane do wyświetlania rozmiaru komunikatów w bajtach dostarczonych do różnych punktów końcowych. Wartość metryki zwiększa się dla każdego dostarczonego komunikatu, w tym jeśli komunikat zostanie dostarczony do wielu punktów końcowych lub jeśli komunikat zostanie dostarczony do tego samego punktu końcowego wiele razy.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Tak|Dostarczanie routingu (wersja zapoznawcza)|Liczba|Łącznie|Liczba prób dostarczenia IoT Hub do wszystkich punktów końcowych przy użyciu routingu. Aby wyświetlić liczbę pomyślnych lub nieudanych prób, użyj wymiaru Wynik. Aby wyświetlić przyczynę awarii, na przykład nieprawidłową, porzuconą lub oddzieloną, użyj wymiaru FailureReasonCategory. Możesz również użyć wymiarów EndpointName i EndpointType, aby zrozumieć, ile komunikatów zostało dostarczonych do różnych punktów końcowych. Wartość metryki zwiększa się o jeden dla każdej próby dostarczenia, w tym jeśli komunikat jest dostarczany do wielu punktów końcowych lub jeśli komunikat jest dostarczany do tego samego punktu końcowego wiele razy.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Tak|Opóźnienie dostarczania routingu (wersja zapoznawcza)|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem a IoT Hub i komunikatem telemetrii przychodzącym do punktu końcowego. Możesz użyć wymiarów EndpointName i EndpointType, aby zrozumieć opóźnienie dla różnych punktów końcowych.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Tak|żądana stawka użycia|Procent|Średnia|żądana stawka użycia|Brak wymiarów|
|totalDeviceCount|Tak|Łączna liczba urządzeń|Liczba|Średnia|Liczba urządzeń zarejestrowanych w centrum IoT|Brak wymiarów|
|twinQueries.failure|Tak|Nieudane zapytania bliźniaczych reprezentacji|Liczba|Łącznie|Liczba wszystkich nieudanych zapytań bliźniaczych reprezentacji.|Brak wymiarów|
|twinQueries.resultSize|Tak|Rozmiar wyników zapytań bliźniaczych|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wyniku wszystkich pomyślnych zapytań bliźniaczych reprezentacji.|Brak wymiarów|
|twinQueries.success|Tak|Pomyślne zapytania bliźniaczych reprezentacji|Liczba|Łącznie|Liczba wszystkich pomyślnych zapytań bliźniaczych.|Brak wymiarów|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Tak|Porzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak wymiarów|
|c2d.commands.egress.complete.success|Tak|Zakończono dostarczanie komunikatów C2D|Liczba|Łącznie|Liczba pomyślnie ukończonych dostaw komunikatów z chmury do urządzenia przez urządzenie|Brak wymiarów|
|c2d.commands.egress.reject.success|Tak|Odrzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak wymiarów|
|c2d.methods.failure|Tak|Nieudane wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań metody bezpośredniej.|Brak wymiarów|
|c2d.methods.requestSize|Tak|Rozmiar żądania wywołania metody bezpośredniej|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych żądań metody bezpośredniej.|Brak wymiarów|
|c2d.methods.responseSize|Tak|Rozmiar odpowiedzi w wywołaniach metody bezpośredniej|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych odpowiedzi metody bezpośredniej.|Brak wymiarów|
|c2d.methods.success|Tak|Pomyślne wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak wymiarów|
|c2d.twin.read.failure|Tak|Nieudane odczyty bliźniaczej reprezentacji z zadomowień|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów bliźniaczych reprezentacji inicjowanych przez zaczeki.|Brak wymiarów|
|c2d.twin.read.size|Tak|Rozmiar odpowiedzi bliźniaczych odczytów z serwera końcowego|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych odczytów bliźniaczej reprezentacji zainicjowanej przez zaczekiw.|Brak wymiarów|
|c2d.twin.read.success|Tak|Pomyślne odczyty bliźniaczej reprezentacji z back end|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów bliźniaczej reprezentacji inicjowanych przez zatyczek.|Brak wymiarów|
|c2d.twin.update.failure|Tak|Aktualizacje bliźniaczych reprezentacji z zadomowień zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich nieudanych aktualizacji bliźniaczych reprezentacji zainicjowanych przez back end.|Brak wymiarów|
|c2d.twin.update.size|Tak|Rozmiar aktualizacji bliźniaczych reprezentacji z zadomowień|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji inicjowanych przez zaczekię.|Brak wymiarów|
|c2d.twin.update.success|Tak|Pomyślne aktualizacje bliźniaczych reprezentacji z zadomowień|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji zainicjowanych przez zaczekię.|Brak wymiarów|
|C2DMessagesExpired|Tak|Wygasły komunikaty C2D|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak wymiarów|
|Konfiguracji|Tak|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracji|Brak wymiarów|
|connectedDeviceCount|Nie|Połączone urządzenia|Liczba|Średnia|Liczba urządzeń połączonych z centrum IoT|Brak wymiarów|
|d2c.endpoints.egress.builtIn.events|Tak|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Liczba|Łącznie|Ile razy usługa IoT Hub pomyślnie dostarczała komunikaty do wbudowanego punktu końcowego (komunikatów/zdarzeń).|Brak wymiarów|
|d2c.endpoints.egress.eventHubs|Tak|Routing: komunikaty dostarczane do centrum zdarzeń|Liczba|Łącznie|Ile razy usługa IoT Hub pomyślnie dostarczała komunikaty do punktów końcowych centrum zdarzeń.|Brak wymiarów|
|d2c.endpoints.egress.serviceBusQueues|Tak|Routing: komunikaty dostarczane do Service Bus kolejki|Liczba|Łącznie|Liczba pomyślnych IoT Hub pomyślnie dostarczonych komunikatów do Service Bus końcowych kolejki.|Brak wymiarów|
|d2c.endpoints.egress.serviceBusTopics|Tak|Routing: komunikaty dostarczane do Service Bus tematu|Liczba|Łącznie|Liczba pomyślnych IoT Hub dostarczonych komunikatów do punktów końcowych Service Bus tematu.|Brak wymiarów|
|d2c.endpoints.egress.storage|Tak|Routing: komunikaty dostarczane do magazynu|Liczba|Łącznie|Liczba pomyślnych IoT Hub pomyślnie dostarczonych komunikatów do punktów końcowych magazynu.|Brak wymiarów|
|d2c.endpoints.egress.storage.blobs|Tak|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Ile razy usługa IoT Hub dostarczała obiekty blob do punktów końcowych magazynu.|Brak wymiarów|
|d2c.endpoints.egress.storage.bytes|Tak|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub dostarczanych do punktów końcowych magazynu.|Brak wymiarów|
|d2c.endpoints.latency.builtIn.events|Tak|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącymi komunikatami IoT Hub i przychodzącymi komunikatami telemetrii do wbudowanego punktu końcowego (komunikatów/zdarzeń).|Brak wymiarów|
|d2c.endpoints.latency.eventHubs|Tak|Routing: opóźnienie komunikatów dla centrum zdarzeń|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącymi komunikatami do IoT Hub i komunikatem przychodzącym do punktu końcowego centrum zdarzeń.|Brak wymiarów|
|d2c.endpoints.latency.serviceBusQueues|Tak|Routing: opóźnienie komunikatów dla Service Bus kolejki|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem a IoT Hub i komunikatem telemetrii przychodzącym do punktu końcowego Service Bus kolejki.|Brak wymiarów|
|d2c.endpoints.latency.serviceBusTopics|Tak|Routing: opóźnienie komunikatów dla Service Bus tematu|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącymi komunikatami do IoT Hub i komunikatami telemetrii przychodzącymi do punktu końcowego Service Bus tematu.|Brak wymiarów|
|d2c.endpoints.latency.storage|Tak|Routing: opóźnienie komunikatów dla magazynu|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem a IoT Hub komunikatów telemetrycznych do punktu końcowego magazynu.|Brak wymiarów|
|d2c.telemetry.egress.dropped|Tak|Routing: porzucone komunikaty telemetryczne |Liczba|Łącznie|Liczba porzucanych komunikatów przez routing IoT Hub z powodu nieumarłych punktów końcowych. Ta wartość nie zlicza komunikatów dostarczonych do trasy powrotu, ponieważ porzucone komunikaty nie są tam dostarczane.|Brak wymiarów|
|d2c.telemetry.egress.fallback|Tak|Routing: komunikaty dostarczane do rezerwy|Liczba|Łącznie|Ile razy routing IoT Hub do punktu końcowego skojarzonego z trasą rezerwową.|Brak wymiarów|
|d2c.telemetry.egress.invalid|Tak|Routing: niezgodne komunikaty telemetryczne|Liczba|Łącznie|Ile razy routing IoT Hub nie dostarczał komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak wymiarów|
|d2c.telemetry.egress.orphaned|Tak|Routing: oddzielone komunikaty telemetrii |Liczba|Łącznie|Liczba oddzielonych komunikatów za pomocą IoT Hub routingu, ponieważ nie są one zgodne z żadnymi regułami routingu (w tym regułą rezerwy). |Brak wymiarów|
|d2c.telemetry.egress.success|Tak|Routing: dostarczane komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnych komunikatów do wszystkich punktów końcowych przy użyciu IoT Hub routingu. Jeśli komunikat jest przekierowyny do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli komunikat jest dostarczany do tego samego punktu końcowego wielokrotnie, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak wymiarów|
|d2c.telemetry.ingress.allProtocol|Tak|Próby wysyłania komunikatów telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które zostały wysłane do centrum IoT|Brak wymiarów|
|d2c.telemetry.ingress.sendThrottle|Tak|Liczba błędów ograniczania przepustowości|Liczba|Łącznie|Liczba błędów ograniczania przepustowości spowodowanych ograniczeniami przepływności urządzenia|Brak wymiarów|
|d2c.telemetry.ingress.success|Tak|Wysłane komunikaty telemetryczne|Liczba|Łącznie|Liczba komunikatów telemetrycznych wysyłanych z urządzenia do chmury pomyślnie wysłanych do centrum IoT|Brak wymiarów|
|d2c.twin.read.failure|Tak|Nieudane odczyty bliźniaczej reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów bliźniaczych reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.read.size|Tak|Rozmiar odpowiedzi bliźniaczych odczytów z urządzeń|Bajty|Średnia|Średnia, minimalna i maksymalna wszystkich pomyślnych odczytów bliźniaczej reprezentacji zainicjowanej przez urządzenie.|Brak wymiarów|
|d2c.twin.read.success|Tak|Pomyślne odczyty bliźniaczej reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów bliźniaczych reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.update.failure|Tak|Nieudane aktualizacje bliźniaczych reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych aktualizacji bliźniaczych reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.update.size|Tak|Rozmiar aktualizacji bliźniaczych reprezentacji z urządzeń|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|d2c.twin.update.success|Tak|Pomyślne aktualizacje bliźniaczych reprezentacji z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji bliźniaczych reprezentacji zainicjowanych przez urządzenie.|Brak wymiarów|
|dailyMessageQuotaUsed|Tak|Łączna liczba użytych komunikatów|Liczba|Maksimum|Liczba użytych obecnie komunikatów łącznie|Brak wymiarów|
|deviceDataUsage|Tak|Łączne użycie danych urządzenia|Bajty|Łącznie|Bajty przesyłane do i z dowolnych urządzeń połączonych z serwisem IotHub|Brak wymiarów|
|deviceDataUsageV2|Tak|Łączne użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesyłane do i z dowolnych urządzeń połączonych z serwisem IotHub|Brak wymiarów|
|devices.connectedDevices.allProtocol|Tak|Połączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń połączonych z centrum IoT|Brak wymiarów|
|devices.totalDevices|Tak|Łączna liczba urządzeń (przestarzałe)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w centrum IoT|Brak wymiarów|
|EventGridDeliveries|Tak|Event Grid dostaw|Liczba|Łącznie|Liczba zdarzeń IoT Hub publikowanych w Event Grid. Użyj wymiaru Wynik, aby uzyskać liczbę żądań pomyślnych i nieudanych. Wymiar EventType pokazuje typ zdarzenia ( https://aka.ms/ioteventgrid) .|Result, EventType|
|EventGridLatency|Tak|Event Grid opóźnienie|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) od wygenerowania zdarzenia IoT Hub do czasu opublikowania zdarzenia w Event Grid. Ta liczba jest średnią między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Typ zdarzenia|
|jobs.cancelJob.failure|Tak|Anulowanie zadania, które zakończyło się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań w celu anulowania zadania.|Brak wymiarów|
|jobs.cancelJob.success|Tak|Pomyślne anulowanie zadania|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań w celu anulowania zadania.|Brak wymiarów|
|jobs.completed|Tak|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak wymiarów|
|jobs.createDirectMethodJob.failure|Tak|Nieudane tworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich nieudanych zadań wywołania metody bezpośredniej.|Brak wymiarów|
|jobs.createDirectMethodJob.success|Tak|Pomyślne tworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich pomyślnych zadań wywołania metody bezpośredniej.|Brak wymiarów|
|jobs.createTwinUpdateJob.failure|Tak|Nieudane tworzenie zadań aktualizacji bliźniaczych reprezentacji|Liczba|Łącznie|Liczba wszystkich nieudanych zadań aktualizacji bliźniaczych reprezentacji.|Brak wymiarów|
|jobs.createTwinUpdateJob.success|Tak|Pomyślne tworzenie zadań aktualizacji bliźniaczych reprezentacji|Liczba|Łącznie|Liczba wszystkich pomyślnych zadań aktualizacji bliźniaczych.|Brak wymiarów|
|jobs.failed|Tak|Zadania, które zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich zadań, które zakończyły się niepowodzeniem.|Brak wymiarów|
|jobs.listJobs.failure|Tak|Nieudane wywołania zadań listy|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań zadań listy.|Brak wymiarów|
|jobs.listJobs.success|Tak|Pomyślne wywołania zadań listy|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań zadań listy.|Brak wymiarów|
|jobs.queryJobs.failure|Tak|Zapytania dotyczące zadań, które zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich nieudanych wywołań zadań zapytań.|Brak wymiarów|
|jobs.queryJobs.success|Tak|Zapytania dotyczące zadań pomyślnych|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań zadań zapytań.|Brak wymiarów|
|RoutingDataSizeInBytesDelivered|Tak|Rozmiar komunikatu dostarczania routingu w bajtach (wersja zapoznawcza)|Bajty|Łącznie|Łączny rozmiar w bajtach komunikatów dostarczonych przez centrum IoT hub do punktu końcowego. Wymiary EndpointName i EndpointType mogą być używane do wyświetlania rozmiaru komunikatów w bajtach dostarczonych do różnych punktów końcowych. Wartość metryki zwiększa się dla każdego dostarczonego komunikatu, w tym jeśli komunikat jest dostarczany do wielu punktów końcowych lub jeśli komunikat jest dostarczany do tego samego punktu końcowego wiele razy.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Tak|Routing dostaw (wersja zapoznawcza)|Liczba|Łącznie|Liczba prób dostarczenia IoT Hub do wszystkich punktów końcowych przy użyciu routingu. Aby wyświetlić liczbę pomyślnych lub nieudanych prób, użyj wymiaru Wynik. Aby wyświetlić przyczynę awarii, na przykład nieprawidłową, porzuconą lub oddzieloną, użyj wymiaru FailureReasonCategory. Możesz również użyć wymiarów EndpointName i EndpointType, aby zrozumieć, ile komunikatów zostało dostarczonych do różnych punktów końcowych. Wartość metryki zwiększa się o jeden dla każdej próby dostarczenia, w tym jeśli komunikat jest dostarczany do wielu punktów końcowych lub jeśli komunikat jest dostarczany do tego samego punktu końcowego wiele razy.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Tak|Opóźnienie dostarczania routingu (wersja zapoznawcza)|Milisekund|Średnia|Średnie opóźnienie (w milisekundach) między przychodzącym komunikatem a IoT Hub komunikatów telemetrycznych do punktu końcowego. Możesz użyć wymiarów EndpointName i EndpointType, aby zrozumieć opóźnienie dla różnych punktów końcowych.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|Nie|Łączna liczba urządzeń|Liczba|Średnia|Liczba urządzeń zarejestrowanych w centrum IoT|Brak wymiarów|
|twinQueries.failure|Tak|Zapytania bliźniaczych reprezentacji z niepowodzeniem|Liczba|Łącznie|Liczba wszystkich nieudanych zapytań bliźniaczych reprezentacji.|Brak wymiarów|
|twinQueries.resultSize|Tak|Rozmiar wyniku zapytań bliźniaczych|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wyniku wszystkich pomyślnych zapytań bliźniaczych.|Brak wymiarów|
|twinQueries.success|Tak|Pomyślne zapytania bliźniaczych reprezentacji|Liczba|Łącznie|Liczba wszystkich pomyślnych zapytań bliźniaczych.|Brak wymiarów|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AttestationAttempts|Tak|Próby zaświadczenia|Liczba|Łącznie|Liczba prób zaświadczenia urządzenia|ProvisioningServiceName, Status, Protocol|
|DeviceAssignments (Przypisania urządzeń)|Tak|Przypisane urządzenia|Liczba|Łącznie|Liczba urządzeń przypisanych do centrum IoT|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Tak|Próby rejestracji|Liczba|Łącznie|Liczba prób rejestracji urządzeń|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ApiRequests|Tak|Żądania interfejsu API|Liczba|Łącznie|Liczba żądań interfejsu API dla operacji odczytu, Digital Twins, zapisu, usuwania i wykonywania zapytań.|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Tak|Wskaźnik niepowodzeń żądań interfejsu API|Procent|Średnia|Procent żądań interfejsu API odbieranych przez usługę dla wystąpienia, które zwracają kod odpowiedzi błędu wewnętrznego (500) dla operacji odczytu, zapisu, usuwania i wykonywania zapytań w Digital Twins api.|Operacja, uwierzytelnianie, protokół|
|ApiRequestsLatency|Tak|Opóźnienie żądań interfejsu API|Milisekund|Średnia|Czas odpowiedzi dla żądań interfejsu API, tj. od momentu, gdy żądanie zostanie odebrane przez usługę Azure Digital Twins do momentu, gdy usługa wyśle wynik powodzenia/Digital Twins operacji odczytu, zapisu, usuwania i wykonywania zapytań.|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Tak|Operacje interfejsu API rozliczeń|Liczba|Łącznie|Metryka rozliczeń dla liczby wszystkich żądań interfejsu API Azure Digital Twins usługi.|MeterId|
|BillingMessagesProcessed|Tak|Przetworzone komunikaty dotyczące rozliczeń|Liczba|Łącznie|Metryka rozliczeń dotycząca liczby komunikatów wysyłanych z usługi Azure Digital Twins do zewnętrznych punktów końcowych.|MeterId|
|BillingQueryUnits|Tak|Jednostki zapytań rozliczeń|Liczba|Łącznie|Liczba jednostek zapytania, obliczona wewnętrznie miara użycia zasobów usługi, która jest zużywana do wykonywania zapytań.|MeterId|
|IngressEvents|Tak|Zdarzenia danych przychodzących|Liczba|Łącznie|Liczba przychodzących zdarzeń telemetrii do Azure Digital Twins.|Wynik|
|IngressEventsFailureRate|Tak|Szybkość niepowodzeń zdarzeń przychodzących|Procent|Średnia|Procent przychodzących zdarzeń telemetrycznych, dla których usługa zwraca kod odpowiedzi błędu wewnętrznego (500).|Brak wymiarów|
|IngressEventsLatency|Tak|Opóźnienie zdarzeń przychodzących|Milisekund|Średnia|Czas od momentu dotarcie zdarzenia do momentu, gdy jest ono gotowe do wyjścia przez Azure Digital Twins, w którym usługa wysyła wynik powodzenia/powodzenia.|Wynik|
|ModelCount|Tak|Liczba modeli|Liczba|Łącznie|Łączna liczba modeli w Azure Digital Twins wystąpienia. Użyj tej metryki, aby określić, czy zbliżasz się do limitu usługi dla maksymalnej liczby modeli dozwolonych na wystąpienie.|Brak wymiarów|
|Routing|Tak|Komunikaty kierowane|Liczba|Łącznie|Liczba komunikatów wysyłanych do usługi platformy Azure punktu końcowego, takiej jak Centrum zdarzeń, Service Bus lub Event Grid.|EndpointType, Result|
|RoutingFailureRate|Tak|Szybkość niepowodzeń routingu|Procent|Średnia|Procent zdarzeń, które w wyniku błędu są kierowane z usługi Azure Digital Twins do usługi punktu końcowego platformy Azure, takiej jak Event Hub, Service Bus lub Event Grid.|Endpointtype|
|RoutingLatency|Tak|Opóźnienie routingu|Milisekund|Średnia|Upłynął czas między zdarzeniem przekierowywowym Azure Digital Twins do momentu jego przesyłania do usługi punktu końcowego platformy Azure, takiej jak Event Hub, Service Bus lub Event Grid.|EndpointType, Result|
|TwinCount|Tak|Liczba bliźniaczych reprezentacji|Liczba|Łącznie|Łączna liczba bliźniaczych reprezentacji w Azure Digital Twins wystąpienia. Użyj tej metryki, aby określić, czy zbliżasz się do limitu usługi dla maksymalnej dozwolonej liczby bliźniaczych reprezentacji na wystąpienie.|Brak wymiarów|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DodajRegion|Tak|Dodano region|Liczba|Liczba|Dodano region|Region (Region)|
|AutoscaleMaxThroughput|Nie|Maksymalna przepływność autoskalowania|Liczba|Maksimum|Maksymalna przepływność autoskalowania|DatabaseName, CollectionName|
|AvailableStorage|Nie|(przestarzałe) Dostępny magazyn|Bajty|Łącznie|"Dostępny magazyn" zostanie usunięty z Azure Monitor koniec września 2023 r. Cosmos DB magazynu kolekcji jest teraz nieograniczony. Jedynym ograniczeniem jest to, że rozmiar magazynu dla każdego klucza partycji logicznej wynosi 20 GB. Możesz włączyć funkcję PartitionKeyStatistics w dzienniku diagnostycznym, aby poznać zużycie magazynu dla kluczy partycji o najwyższej jakości. Aby uzyskać więcej informacji na temat Cosmos DB przydziału magazynu, zapoznaj się z tym doc https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Po wycofaniu pozostałe reguły alertów nadal zdefiniowane dla przestarzałej metryki zostaną automatycznie wyłączone po dacie zakończenia pracy.|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Nie|Zamknięcia połączeń Cassandra|Liczba|Łącznie|Liczba zamkniętych połączeń cassandra zgłaszanych z 1-minutowym poziomem szczegółowości|Region, ZamknięcieReason|
|CassandraConnectorAvgReplicationLatency|Nie|Średnia replikacja łącznika CassandraLatency|Milisekund|Średnia|Średnia replikacja łącznika CassandraLatency|Brak wymiarów|
|CassandraConnectorReplicationHealthStatus|Nie|Stan kondycji replikacji łącznika Cassandra|Liczba|Liczba|Stan kondycji replikacji łącznika Cassandra|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceCreate|Nie|Utworzono przestrzeń kluczy Cassandra|Liczba|Liczba|Utworzono przestrzeń kluczy Cassandra|Resourcename |
|CassandraKeyspaceDelete|Nie|Usunięto przestrzeń kluczy Cassandra|Liczba|Liczba|Usunięto przestrzeń kluczy Cassandra|Resourcename |
|CassandraKeyspaceThroughputUpdate|Nie|Zaktualizowano przepływność przestrzeni kluczy Cassandra|Liczba|Liczba|Zaktualizowano przepływność przestrzeni kluczy Cassandra|Resourcename |
|CassandraKeyspaceUpdate|Nie|Zaktualizowano przestrzeń kluczy Cassandra|Liczba|Liczba|Zaktualizowano przestrzeń kluczy Cassandra|Resourcename |
|CassandraRequestCharges|Nie|Opłaty za żądania cassandra|Liczba|Łącznie|Zużyte przez języki żądań Cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Nie|Żądania Cassandra|Liczba|Liczba|Liczba wykonanych żądań cassandra|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Nie|Utworzono tabelę cassandra|Liczba|Liczba|Utworzono tabelę cassandra|ResourceName, ChildResourceName, |
|CassandraTableDelete|Nie|Usunięto tabelę cassandra|Liczba|Liczba|Usunięto tabelę cassandra|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|Nie|Zaktualizowano przepływność tabeli Cassandra|Liczba|Liczba|Zaktualizowano przepływność tabeli Cassandra|ResourceName, ChildResourceName, |
|CassandraTableUpdate|Nie|Zaktualizowano tabelę cassandra|Liczba|Liczba|Zaktualizowano tabelę rozwiązania Cassandra|ResourceName, ChildResourceName, |
|CreateAccount|Tak|Utworzone konto|Liczba|Liczba|Utworzone konto|Brak wymiarów|
|DataUsage (DataUsage)|Nie|Użycie danych|Bajty|Łącznie|Łączne użycie danych zgłoszone na poziomie szczegółowości 5 minut|CollectionName, DatabaseName, Region|
|DedicatedGatewayRequests|Tak|DedicatedGatewayRequests|Liczba|Liczba|Żądania w dedykowanej bramie|DatabaseName, CollectionName, CacheExerc wywłaszczone, OperationName, Region|
|DeleteAccount|Tak|Konto usunięte|Liczba|Liczba|Konto usunięte|Brak wymiarów|
|DocumentCount|Nie|Liczba dokumentów|Liczba|Łącznie|Łączna liczba zgłoszonych dokumentów z 5-minutowym poziomem szczegółowości|CollectionName, DatabaseName, Region|
|DocumentQuota|Nie|Limit przydziału dokumentu|Bajty|Łącznie|Zgłaszany łączny limit przydziału magazynu z 5-minutową szczegółowością|CollectionName, DatabaseName, Region|
|GremlinDatabaseCreate|Nie|Utworzono bazę danych Gremlin|Liczba|Liczba|Utworzono bazę danych Gremlin|Resourcename |
|GremlinDatabaseDelete|Nie|Usunięto bazę danych Gremlin|Liczba|Liczba|Usunięto bazę danych Gremlin|Resourcename |
|GremlinDatabaseThroughputUpdate|Nie|Zaktualizowano przepływność bazy danych Gremlin|Liczba|Liczba|Zaktualizowano przepływność bazy danych Gremlin|Resourcename |
|GremlinDatabaseUpdate|Nie|Zaktualizowano bazę danych Gremlin|Liczba|Liczba|Zaktualizowano bazę danych Gremlin|Resourcename |
|GremlinGraphCreate|Nie|Utworzony graf Gremlin|Liczba|Liczba|Utworzony graf Gremlin|ResourceName, ChildResourceName, |
|GremlinGraphDelete|Nie|Usunięto graf Gremlin|Liczba|Liczba|Usunięto graf Gremlin|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|Nie|Zaktualizowano przepływność grafu Gremlin|Liczba|Liczba|Zaktualizowano przepływność grafu Gremlin|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|Nie|Aktualizacja grafu Gremlin|Liczba|Liczba|Aktualizacja grafu Gremlin|ResourceName, ChildResourceName, |
|IndexUsage|Nie|Użycie indeksu|Bajty|Łącznie|Łączne użycie indeksu zgłoszone na poziomie szczegółowości 5 minut|CollectionName, DatabaseName, Region|
|IntegratedCacheEvictedEntriesSize|Nie|IntegratedCacheEvictedEntriesSize|Bajty|Średnia|Rozmiar wpisów eksmitowanych ze zintegrowanej pamięci podręcznej|CacheType, Region|
|IntegratedCacheHitRate|Nie|IntegratedCacheHitRate|Procent|Średnia|Szybkość trafień pamięci podręcznej dla zintegrowanych pamięci podręcznych|CacheType, Region|
|IntegratedCacheSize|Nie|IntegratedCacheSize|Bajty|Średnia|Rozmiar zintegrowanych pamięci podręcznych dla dedykowanych żądań bramy|CacheType, Region|
|IntegratedCacheTTLExpirationCount|Nie|IntegratedCacheTTLExpirationCount|Liczba|Średnia|Liczba wpisów usuniętych ze zintegrowanej pamięci podręcznej z powodu wygaśnięcia czasu wygaśnięcia|CacheType, Region|
|MetadataRequests|Nie|Żądania metadanych|Liczba|Liczba|Liczba żądań metadanych. Cosmos DB przechowuje zbieranie metadanych systemu dla każdego konta, co umożliwia bezpłatne wyliczanie kolekcji, baz danych i ich konfiguracji.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoCollectionCreate|Nie|Utworzono kolekcję Mongo|Liczba|Liczba|Utworzono kolekcję Mongo|ResourceName, ChildResourceName, |
|MongoCollectionDelete|Nie|Usunięto kolekcję Mongo|Liczba|Liczba|Usunięto kolekcję Mongo|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|Nie|Zaktualizowano przepływność kolekcji Mongo|Liczba|Liczba|Zaktualizowano przepływność kolekcji Mongo|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|Nie|Zaktualizowano kolekcję Mongo|Liczba|Liczba|Zaktualizowano kolekcję Mongo|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|Nie|Usunięto bazę danych Mongo|Liczba|Liczba|Usunięto bazę danych Mongo|Resourcename |
|MongoDatabaseThroughputUpdate|Nie|Zaktualizowano przepływność bazy danych Mongo|Liczba|Liczba|Zaktualizowano przepływność bazy danych Mongo|Resourcename |
|MongoDBDatabaseTworzeń|Nie|Utworzono bazę danych Mongo|Liczba|Liczba|Utworzono bazę danych Mongo|Resourcename |
|MongoDBDatabaseUpdate|Nie|Zaktualizowano bazę danych Mongo|Liczba|Liczba|Zaktualizowano bazę danych Mongo|Resourcename |
|MongoRequestCharge|Tak|Mongo Request Charge|Liczba|Łącznie|Zużyte jednostki żądań Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|Tak|Żądania Mongo|Liczba|Liczba|Liczba wykonanych żądań Mongo|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequestsCount|Nie|(przestarzałe) Szybkość żądań Mongo|CountPerSecond|Średnia|Liczba żądań Mongo na sekundę|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|Nie|(przestarzałe) Szybkość żądań usunięcia mongo|CountPerSecond|Średnia|Żądanie usunięcia mongo na sekundę|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|Nie|(przestarzałe) Szybkość żądań wstawiania mongo|CountPerSecond|Średnia|Liczba wstawień mongo na sekundę|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|Nie|(przestarzałe) Szybkość żądań zapytań Mongo|CountPerSecond|Średnia|Żądanie zapytania Mongo na sekundę|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|Nie|(przestarzałe) Szybkość żądań aktualizacji mongo|CountPerSecond|Średnia|Żądanie aktualizacji Mongo na sekundę|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption|Nie|Znormalizowane zużycie ru|Procent|Maksimum|Maksymalna wartość procentowa użycia jednostki RU na minutę|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|Nie|Aprowizowana przepływność|Liczba|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|RegionFailover|Tak|Region w awarii|Liczba|Liczba|Region w awarii|Brak wymiarów|
|RemoveRegion|Tak|Usunięto region|Liczba|Liczba|Usunięto region|Region (Region)|
|ReplicationLatency|Tak|Opóźnienie replikacji P99|Milisekund|Średnia|Opóźnienie replikacji P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną|SourceRegion, TargetRegion|
|ServerSideLatency|Nie|Opóźnienie po stronie serwera|Milisekund|Średnia|Opóźnienie po stronie serwera|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability (Niedostępność usługi)|Nie|Dostępność usługi|Procent|Średnia|Poziom szczegółowości żądań konta na poziomie jednej godziny, dnia lub miesiąca|Brak wymiarów|
|SqlContainerCreate|Nie|Utworzony kontener SQL|Liczba|Liczba|Utworzony kontener SQL|ResourceName, ChildResourceName, |
|SqlContainerDelete|Nie|Usunięty kontener SQL|Liczba|Liczba|Usunięty kontener SQL|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|Nie|Zaktualizowano przepływność kontenera SQL|Liczba|Liczba|Zaktualizowano przepływność kontenera SQL|ResourceName, ChildResourceName, |
|SqlContainerUpdate|Nie|Zaktualizowano kontener SQL|Liczba|Liczba|Zaktualizowano kontener SQL|ResourceName, ChildResourceName, |
|SqlDatabaseTworzeń|Nie|Utworzono usługę SQL Database|Liczba|Liczba|Utworzono usługę SQL Database|Resourcename |
|SqlDatabaseDelete|Nie|Usunięto usługę SQL Database|Liczba|Liczba|Usunięto usługę SQL Database|Resourcename |
|SqlDatabaseThroughputUpdate|Nie|Zaktualizowano przepływność usługi SQL Database|Liczba|Liczba|Zaktualizowano przepływność usługi SQL Database|Resourcename |
|SqlDatabaseUpdate|Nie|Zaktualizowano usługę SQL Database|Liczba|Liczba|Zaktualizowano usługę SQL Database|Resourcename |
|TableTableCreate|Nie|Utworzono tabelę AzureTable|Liczba|Liczba|Utworzono tabelę AzureTable|Resourcename |
|TableTableDelete|Nie|Usunięto tabelę AzureTable|Liczba|Liczba|Usunięto tabelę AzureTable|Resourcename |
|TableTableThroughputUpdate|Nie|Zaktualizowano przepływność tabeli usługi AzureTable|Liczba|Liczba|Zaktualizowano przepływność tabeli usługi AzureTable|Resourcename |
|TableTableUpdate|Nie|Zaktualizowano tabelę AzureTable|Liczba|Liczba|Zaktualizowano tabelę AzureTable|Resourcename |
|TotalRequests|Tak|Łączna liczba żądań|Liczba|Liczba|Liczba wykonanych żądań|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|Tak|Łączna liczba jednostek żądania|Liczba|Łącznie|Zużyte jednostki żądań|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys|Tak|Zaktualizowano klucze kont|Liczba|Liczba|Zaktualizowano klucze kont|Keytype|
|UpdateAccountNetworkSettings|Tak|Zaktualizowano ustawienia sieciowe konta|Liczba|Liczba|Zaktualizowano ustawienia sieciowe konta|Brak wymiarów|
|UpdateAccountReplicationSettings|Tak|Zaktualizowano ustawienia replikacji konta|Liczba|Liczba|Zaktualizowano ustawienia replikacji konta|Brak wymiarów|
|UpdateDiagnosticsSettings|Nie|Zaktualizowano ustawienia diagnostyczne konta|Liczba|Liczba|Zaktualizowano ustawienia diagnostyczne konta|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Tak|Zaawansowane oceny filtrów|Liczba|Łącznie|Łączna liczba filtrów zaawansowanych ocenionych w subskrypcjach zdarzeń dla tego tematu.|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Tak|Zdarzenia utraconych liter|Liczba|Łącznie|Łączna liczba zdarzeń utraconych pasujących do tej subskrypcji zdarzeń|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nie|Zdarzenia niepowodzenie dostarczania|Liczba|Łącznie|Łączna liczba zdarzeń, których nie udało się dostarczyć do tej subskrypcji zdarzeń|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas trwania przetwarzania docelowego|Milisekund|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Tak|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowana do tej subskrypcji zdarzeń|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń, które zakończyły się niepowodzeniem|Liczba|Łącznie|Nie można opublikować w tym temacie łącznej liczby zdarzeń|Topic, ErrorType, Error|
|PublishSuccessCount|Tak|Opublikowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Temat|
|PublishSuccessLatencyInMs|Tak|Opóźnienie powodzenia publikowania|Milisekund|Łącznie|Opóźnienie powodzenia publikowania w milisekundach|Brak wymiarów|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych liter|Liczba|Łącznie|Łączna liczba zdarzeń utraconych pasujących do tej subskrypcji zdarzeń|DeadLetterReason|
|DeliveryAttemptFailCount|Nie|Zdarzenia niepowodzenie dostarczania|Liczba|Łącznie|Łączna liczba zdarzeń, których nie udało się dostarczyć do tej subskrypcji zdarzeń|Error, ErrorType|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Brak wymiarów|
|DestinationProcessingDurationInMs|Nie|Czas trwania przetwarzania docelowego|Milisekund|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Brak wymiarów|
|DroppedEventCount|Tak|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowana do tej subskrypcji zdarzeń|Brak wymiarów|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PublishFailCount|Tak|Publikowanie zdarzeń, które zakończyły się niepowodzeniem|Liczba|Łącznie|Nie można opublikować w tym temacie łącznej liczby zdarzeń|ErrorType, Error|
|PublishSuccessCount|Tak|Opublikowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Opóźnienie powodzenia publikowania|Milisekund|Łącznie|Opóźnienie powodzenia publikowania w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych liter|Liczba|Łącznie|Łączna liczba zdarzeń utraconych pasujących do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia, które zakończyły się niepowodzeniem dostarczania|Liczba|Łącznie|Łączna liczba zdarzeń nie może dostarczyć do tej subskrypcji zdarzeń|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas trwania przetwarzania docelowego|Milisekund|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Tak|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowana do tej subskrypcji zdarzeń|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|PublishFailCount|Tak|Publikowanie zdarzeń, które zakończyły się niepowodzeniem|Liczba|Łącznie|Publikowanie w tym temacie wszystkich zdarzeń nie powiodło się|ErrorType, Error|
|PublishSuccessCount|Tak|Opublikowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Opóźnienie powodzenia publikowania|Milisekund|Łącznie|Opóźnienie powodzenia publikowania w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Tak|Zaawansowane oceny filtrów|Liczba|Łącznie|Łączna liczba filtrów zaawansowanych ocenianych w subskrypcjach zdarzeń dla tego tematu.|EventSubscriptionName|
|DeadLetteredCount|Tak|Zdarzenia utraconych liter|Liczba|Łącznie|Łączna liczba zdarzeń utraconych pasujących do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia, które zakończyły się niepowodzeniem dostarczania|Liczba|Łącznie|Łączna liczba zdarzeń nie może dostarczyć do tej subskrypcji zdarzeń|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas trwania przetwarzania docelowego|Milisekund|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|DroppedEventCount|Tak|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowana do tej subskrypcji zdarzeń|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|PublishFailCount|Tak|Publikowanie zdarzeń, które zakończyły się niepowodzeniem|Liczba|Łącznie|Publikowanie w tym temacie wszystkich zdarzeń nie powiodło się|ErrorType, Error|
|PublishSuccessCount|Tak|Opublikowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Tak|Zaawansowane oceny filtrów|Liczba|Łącznie|Łączna liczba filtrów zaawansowanych ocenianych w subskrypcjach zdarzeń dla tego tematu.|EventSubscriptionName|
|DeadLetteredCount|Tak|Zdarzenia utraconych liter|Liczba|Łącznie|Łączna liczba zdarzeń utraconych pasujących do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia, które zakończyły się niepowodzeniem dostarczania|Liczba|Łącznie|Łączna liczba zdarzeń nie może dostarczyć do tej subskrypcji zdarzeń|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|DestinationProcessingDurationInMs|Nie|Czas trwania przetwarzania docelowego|Milisekund|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|DroppedEventCount|Tak|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowana do tej subskrypcji zdarzeń|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|PublishFailCount|Tak|Publikowanie zdarzeń, które zakończyły się niepowodzeniem|Liczba|Łącznie|Publikowanie w tym temacie wszystkich zdarzeń nie powiodło się|ErrorType, Error|
|PublishSuccessCount|Tak|Opublikowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Opóźnienie powodzenia publikowania|Milisekund|Łącznie|Opóźnienie powodzenia publikowania w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Tak|Zaawansowane oceny filtrów|Liczba|Łącznie|Łączna liczba filtrów zaawansowanych ocenianych w subskrypcjach zdarzeń dla tego tematu.|EventSubscriptionName|
|DeadLetteredCount|Tak|Zdarzenia utraconych liter|Liczba|Łącznie|Łączna liczba zdarzeń utraconych pasujących do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia niepowodzenie dostarczania|Liczba|Łącznie|Łączna liczba zdarzeń, których nie udało się dostarczyć do tej subskrypcji zdarzeń|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|DestinationProcessingDurationInMs|Nie|Czas trwania przetwarzania docelowego|Milisekund|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName (Nazwa subskrypcji zdarzeń)|
|DroppedEventCount|Tak|Porzucone zdarzenia|Liczba|Łącznie|Łączna liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowana do tej subskrypcji zdarzeń|EventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń, które zakończyły się niepowodzeniem|Liczba|Łącznie|Nie można opublikować w tym temacie łącznej liczby zdarzeń|ErrorType, Error|
|PublishSuccessCount|Tak|Opublikowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Opóźnienie powodzenia publikowania|Milisekund|Łącznie|Opóźnienie powodzenia publikowania w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej z subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Średnia|Łączna liczba aktywnych połączeń dla Microsoft.EventHub.||
|AvailableMemory|Nie|Dostępna pamięć|Procent|Maksimum|Dostępna pamięć dla klastra centrum zdarzeń jako procent całkowitej ilości pamięci.|Rola|
|CaptureBacklog|Nie|Przechwyć zaległości.|Liczba|Łącznie|Przechwyć zaległości dla Microsoft.EventHub.||
|CapturedBytes|Nie|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla Microsoft.EventHub.||
|CapturedMessages|Nie|Przechwycone komunikaty.|Liczba|Łącznie|Przechwycone komunikaty dla Microsoft.EventHub.||
|ConnectionsClosed|Nie|Zamknięte połączenia.|Liczba|Średnia|Połączenia zamknięte dla Microsoft.EventHub.||
|PołączeniaOtwierone|Nie|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla Microsoft.EventHub.||
|Procesor CPU|Nie|Procesor CPU|Procent|Maksimum|Użycie procesora CPU dla klastra centrum zdarzeń jako wartość procentowa|Rola|
|IncomingBytes|Tak|Bajty przychodzące.|Bajty|Łącznie|Bajty przychodzące dla Microsoft.EventHub.||
|IncomingMessages|Tak|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla Microsoft.EventHub.||
|IncomingRequests|Tak|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla Microsoft.EventHub.||
|Wychodzącebajty|Tak|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla Microsoft.EventHub.||
|WychodząceMessages|Tak|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla Microsoft.EventHub.||
|QuotaExceededErrors|Nie|Błędy przekroczenia limitu przydziału.|Liczba|Łącznie|Błędy przekroczenia limitu przydziału dla microsoft.eventhub.|Operationresult|
|ServerErrors|Nie|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla Microsoft.EventHub.|Operationresult|
|Rozmiar|Nie|Rozmiar|Bajty|Średnia|Rozmiar usługi EventHub w bajtach.|Rola|
|SuccessfulRequests|Nie|Żądania pomyślne|Liczba|Łącznie|Żądania pomyślne dla Microsoft.EventHub.|Operationresult|
|ThrottledRequests|Nie|Żądania ograniczone.|Liczba|Łącznie|Ograniczone żądania dla Microsoft.EventHub.|Operationresult|
|UserErrors|Nie|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkownika dla Microsoft.EventHub.|Operationresult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Średnia|Łączna liczba aktywnych połączeń dla Microsoft.EventHub.||
|CaptureBacklog|Nie|Przechwyć zaległości.|Liczba|Łącznie|Przechwyć zaległości dla Microsoft.EventHub.|EntityName (Nazwa jednostki)|
|CapturedBytes|Nie|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla Microsoft.EventHub.|Nazwa jednostki|
|CapturedMessages|Nie|Przechwycone komunikaty.|Liczba|Łącznie|Przechwycone komunikaty dla Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Nie|Zamknięte połączenia.|Liczba|Średnia|Połączenia zamknięte dla witryny Microsoft.EventHub.|EntityName|
|PołączeniaOtwierone|Nie|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla witryny Microsoft.EventHub.|EntityName|
|EMONTL|Tak|Archiwizowanie komunikatów listy prac (przestarzałe)|Liczba|Łącznie|Archiwizowanie komunikatów centrum zdarzeń na zaległości dla przestrzeni nazw (przestarzałe)||
|EHAMBS|Tak|Przepływność wiadomości archiwum (przestarzałe)|Bajty|Łącznie|Centrum zdarzeń zarchiwizowała przepływność komunikatów w przestrzeni nazw (przestarzałe)||
|EHAMSGS|Tak|Archiwizowanie komunikatów (przestarzałe)|Liczba|Łącznie|Zarchiwizowane komunikaty centrum zdarzeń w przestrzeni nazw (przestarzałe)||
|EHINBYTES|Tak|Bajty przychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)||
|EHINMBS|Tak|Bajty przychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw. Ta metryka jest przestarzała. Zamiast tego użyj metryki Bajty przychodzące (przestarzałe)||
|EHINMSGS|Tak|Komunikaty przychodzące (przestarzałe)|Liczba|Łącznie|Łączna liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)||
|EHOUTBYTES (EHOUTBAJTY)|Tak|Bajty wychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)||
|EHOUTMBS|Tak|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw. Ta metryka jest przestarzała. Zamiast tego użyj metryki Bajty wychodzące (przestarzałe)||
|EHOUTMSGS|Tak|Komunikaty wychodzące (przestarzałe)|Liczba|Łącznie|Łączna liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)||
|FAILREQ|Tak|Żądania nieudane (przestarzałe)|Liczba|Łącznie|Łączna liczba żądań, które zakończyły się niepowodzeniem dla przestrzeni nazw (przestarzałe)||
|IncomingBytes|Tak|Bajty przychodzące.|Bajty|Łącznie|Bajty przychodzące dla Microsoft.EventHub.|Nazwa jednostki|
|IncomingMessages|Tak|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla Microsoft.EventHub.|EntityName|
|IncomingRequests|Tak|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla Microsoft.EventHub.|EntityName|
|INMSGS|Tak|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Łączna liczba komunikatów przychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Zamiast tego użyj metryki Komunikaty przychodzące (przestarzałe)||
|INREQS|Tak|Żądania przychodzące (przestarzałe)|Liczba|Łącznie|Łączna liczba przychodzących żądań wysyłania dla przestrzeni nazw (przestarzałe)||
|INTERR|Tak|Wewnętrzne błędy serwera (przestarzałe)|Liczba|Łącznie|Łączna liczba wewnętrznych błędów serwera dla przestrzeni nazw (przestarzałe)||
|MISCERR|Tak|Inne błędy (przestarzałe)|Liczba|Łącznie|Łączna liczba żądań, które zakończyły się niepowodzeniem dla przestrzeni nazw (przestarzałe)||
|Wychodzące bajty|Tak|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla Microsoft.EventHub.|EntityName (Nazwa jednostki)|
|WychodząceMessages|Tak|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla Microsoft.EventHub.|EntityName (Nazwa jednostki)|
|OUTMSGS|Tak|Komunikaty wychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Łączna liczba komunikatów wychodzących dla przestrzeni nazw. Ta metryka jest przestarzała. Zamiast tego użyj metryki Komunikaty wychodzące (przestarzałe)||
|QuotaExceededErrors|Nie|Błędy przekroczenia limitu przydziału.|Liczba|Łącznie|Błędy przekroczenia limitu przydziału dla Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Nie|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla Microsoft.EventHub.|EntityName, OperationResult|
|Rozmiar|Nie|Rozmiar|Bajty|Średnia|Rozmiar usługi EventHub w bajtach.|EntityName|
|SuccessfulRequests|Nie|Żądania pomyślne|Liczba|Łącznie|Żądania pomyślne dla Microsoft.EventHub.|EntityName, OperationResult|
|SUCCREQ|Tak|Żądania pomyślne (przestarzałe)|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (przestarzałe)||
|SVRBSY|Tak|Błędy zajętości serwera (przestarzałe)|Liczba|Łącznie|Łączna liczba błędów zajętości serwera dla przestrzeni nazw (przestarzałe)||
|ThrottledRequests|Nie|Żądania ograniczone.|Liczba|Łącznie|Ograniczone żądania dotyczące witryny Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Nie|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkownika w witrynie Microsoft.EventHub.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Tak|Kategoryzowanie żądań bramy|Liczba|Łącznie|Liczba żądań bramy według kategorii (1xx/2xx/3xx/4xx/5xx)|Stan http|
|GatewayRequests|Tak|Żądania bramy|Liczba|Łącznie|Liczba żądań bramy|Stan http|
|KafkaRestProxy.ConsumerRequest.m1_delta|Tak|Żądanie konsumenta serwera proxy RESTPrzesłanie danych|CountPerSecond|Łącznie|Liczba żądań odbiorców do serwera proxy REST platformy Kafka|Maszyna, temat|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Tak|Żądania niepomyślne przez konsumenta serwera proxy REST|CountPerSecond|Łącznie|Wyjątki żądań odbiorców|Maszyna, temat|
|KafkaRestProxy.ConsumerRequestTime.p95|Tak|Żądanie konsumenta rest proxylatency|Milisekund|Średnia|Opóźnienie komunikatów w żądaniu użytkownika za pośrednictwem serwera proxy REST platformy Kafka|Maszyna, temat|
|KafkaRestProxy.ConsumerRequestWaitingInQueueTime.p95|Tak|Zaległość żądania konsumenta serwera proxy REST|Milisekund|Średnia|Długość kolejki serwera proxy REST konsumenta|Maszyna, temat|
|KafkaRestProxy.MessagesIn.m1_delta|Tak|Serwer proxy REST MessageThroughput producenta|CountPerSecond|Łącznie|Liczba komunikatów producenta za pośrednictwem serwera proxy REST platformy Kafka|Maszyna, temat|
|KafkaRestProxy.MessagesOut.m1_delta|Tak|REST proxy Consumer MessageThroughput|CountPerSecond|Łącznie|Liczba komunikatów odbiorców za pośrednictwem serwera proxy REST platformy Kafka|Maszyna, temat|
|KafkaRestProxy.OpenConnections|Tak|Współbieżne połączenia serwera proxy REST|Liczba|Łącznie|Liczba połączeń współbieżnych za pośrednictwem serwera proxy REST platformy Kafka|Maszyna, temat|
|KafkaRestProxy.ProducerRequest.m1_delta|Tak|REST proxy Producer RequestThroughput|CountPerSecond|Łącznie|Liczba żądań producentów do serwera proxy REST platformy Kafka|Maszyna, temat|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Tak|Żądania producentów nieudane dla serwera proxy REST|CountPerSecond|Łącznie|Wyjątki żądań producenta|Maszyna, temat|
|KafkaRestProxy.ProducerRequestTime.p95|Tak|REST proxy Producer RequestLatency|Milisekund|Średnia|Opóźnienie komunikatów w żądaniu producenta za pośrednictwem serwera proxy REST platformy Kafka|Maszyna, temat|
|KafkaRestProxy.ProducerRequestWaitingInQueueTime.p95|Tak|Zaległości żądań producenta serwera proxy REST|Milisekund|Średnia|Długość kolejki serwera proxy REST producenta|Maszyna, temat|
|NumActiveWorkers|Tak|Liczba aktywnych pracowników|Liczba|Maksimum|Liczba aktywnych pracowników|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Szybkość dostępności usługi.|Brak wymiarów|
|CosmosDbCollectionSize|Tak|Cosmos DB rozmiar kolekcji|Bajty|Łącznie|Rozmiar kolekcji zapasowej Cosmos DB w bajtach.|Brak wymiarów|
|CosmosDbIndexSize|Tak|Cosmos DB rozmiar indeksu|Bajty|Łącznie|Rozmiar zapasowej kolekcji Cosmos DB w bajtach.|Brak wymiarów|
|CosmosDbRequestCharge|Tak|Cosmos DB użycia ru|Liczba|Łącznie|Użycie ru żądań do kopii zapasowej usługi Cosmos DB.|Operation, ResourceType|
|CosmosDbRequests|Tak|Żądania Cosmos DB usługi|Liczba|Sum|Łączna liczba żądań do dostawcy Cosmos DB.|Operation, ResourceType|
|CosmosDbThrottleRate|Tak|Ograniczanie Cosmos DB usług|Liczba|Sum|Łączna liczba 429 odpowiedzi z kopii zapasowej usługi Cosmos DB.|Operation, ResourceType|
|IoTConnectorDeviceEvent|Tak|Liczba komunikatów przychodzących|Liczba|Sum|Łączna liczba komunikatów odebranych przez usługę Azure IoT Connector dla standardu FHIR przed normalizacją.|Operation, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Tak|Średnie opóźnienie etapu normalizacji|Milisekund|Średnia|Średni czas między czasem pozyskiwania zdarzenia a czasem przetworzenia zdarzenia na celu normalizację.|Operation, ConnectorName|
|IoTConnectorMeasurement|Tak|Liczba pomiarów|Liczba|Sum|Liczba znormalizowanych odczytów wartości odebranych w etapie konwersji FHIR platformy Azure IoT Connector for FHIR.|Operation, ConnectorName|
|IoTConnectorMeasurementGroup|Tak|Liczba grup komunikatów|Liczba|Sum|Łączna liczba unikatowych grup pomiarów w obrębie typu, urządzenia, pacjenta i skonfigurowanego okresu wygenerowanego przez etap konwersji FHIR.|Operation, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Tak|Średnie opóźnienie etapu grupy|Milisekund|Średnia|Okres między tym, kiedy IoT Connector otrzymał dane urządzenia, a kiedy dane są przetwarzane przez etap konwersji FHIR.|Operation, ConnectorName|
|IoTConnectorNormalizedEvent|Tak|Liczba znormalizowanych komunikatów|Liczba|Sum|Łączna liczba mapowanych znormalizowanych wartości wyprowadzonych z etapu normalizacji usługi Azure IoT Connector for FHIR.|Operation, ConnectorName|
|IoTConnectorTotalErrors|Tak|Łączna liczba błędów|Liczba|Sum|Łączna liczba błędów rejestrowanych przez usługę Azure IoT Connector for FHIR|Name, Operation, ErrorType, ErrorSeverity, ConnectorName|
|ServiceApiErrors|Tak|Błędy usługi|Liczba|Sum|Łączna liczba wewnętrznych błędów serwera wygenerowanych przez usługę.|Protocol, Authentication, Operation, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiLatency|Tak|Opóźnienie usługi|Milisekund|Średnia|Opóźnienie odpowiedzi usługi.|Protocol, Authentication, Operation, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Tak|Service Requests|Liczba|Sum|Łączna liczba żądań odebranych przez usługę.|Protocol, Authentication, Operation, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|TotalErrors|Tak|Całkowita liczba błędów|Liczba|Sum|Łączna liczba wewnętrznych błędów serwera napotkanych przez usługę.|Protocol, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Tak|Całkowite opóźnienie|Milisekund|Średnia|Opóźnienie odpowiedzi usługi.|Protokół|
|TotalRequests|Tak|Łączna liczba żądań|Liczba|Sum|Łączna liczba żądań odebranych przez usługę.|Protokół|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfunctions

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Tak|Średnie wykorzystanie CPU|Procent|Średnia|Łączna średnia wartość procentowa wykorzystania procesora wirtualnego w interwale minuty. Łączna liczba procesorów wirtualnych jest oparta na wartości skonfigurowanej przez użytkownika w definicji SKU. Dalsze filtrowanie można zastosować na podstawie nazwy roli zdefiniowanej w sku.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Tak|Średnie wykorzystanie CPU|Procent|Średnia|Łączna średnia wartość procentowa wykorzystania procesora wirtualnego w interwale minuty. Łączna liczba procesorów wirtualnych jest oparta na wartości skonfigurowanej przez użytkownika w definicji SKU. Dalsze filtrowanie można zastosować na podstawie nazwy roli zdefiniowanej w sku.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Wartość metryki|Tak|Próg metryki|Liczba|Średnia|Skonfigurowany próg automatycznego skalowania, gdy autoskalowanie jest skonfigurowane.|MetricTriggerRule|
|ObservedCapacity|Tak|Zaobserwowana pojemność|Liczba|Średnia|Pojemność zgłoszona do automatycznego skalowania podczas wykonywania.||
|ObservedMetricValue|Tak|Zaobserwowana wartość metryki|Liczba|Średnia|Wartość obliczana przez autoskalowanie podczas wykonywania|MetricTriggerSource|
|ScaleActionsInitiated|Tak|Zainicjowane akcje skalowania|Liczba|Łącznie|Kierunek operacji skalowania.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tak|Dostępność|Procent|Średnia|Procent pomyślnie ukończonych testów dostępności|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Nie|Testy dostępności|Liczba|Liczba|Liczba testów dostępności|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Tak|Czas trwania testu dostępności|Milisekund|Średnia|Czas trwania testu dostępności|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Tak|Czas połączenia sieciowego ładowania strony|Milisekund|Średnia|Czas między żądaniem użytkownika a połączeniem sieciowym. Obejmuje wyszukiwania DNS i połączenia transportu.|Brak wymiarów|
|browserTimings/processingDuration|Tak|Czas przetwarzania klienta|Milisekund|Średnia|Czas między odebraniem ostatniego bajtu dokumentu, aż do załadowania modelu DOM. Żądania asynchroniczne mogą nadal być przetwarzane.|Brak wymiarów|
|browserTimings/receiveDuration|Tak|Odbieranie czasu odpowiedzi|Milisekund|Średnia|Czas między pierwszym i ostatnim bajtem lub do momentu rozłączenia.|Brak wymiarów|
|browserTimings/sendDuration|Tak|Czas wysyłania żądania|Milisekund|Średnia|Czas między połączeniem sieciowym i odebraniem pierwszego bajtu.|Brak wymiarów|
|browserTimings/totalDuration|Tak|Czas ładowania strony przeglądarki|Milisekund|Średnia|Czas od żądania użytkownika do momentu załadowania modelu DOM, arkuszy stylów, skryptów i obrazów.|Brak wymiarów|
|zależności/liczba|Nie|Wywołania zależności|Liczba|Liczba|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|zależności/czas trwania|Tak|Czas trwania zależności|Milisekund|Średnia|Czas trwania wywołań wykonanych przez aplikację do zasobów zewnętrznych.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|zależności/niepowodzenie|Nie|Błędy wywołań zależności|Liczba|Liczba|Liczba nieudanych wywołań zależności wykonanych przez aplikację do zasobów zewnętrznych.|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|wyjątki/przeglądarka|Nie|Wyjątki przeglądarki|Liczba|Liczba|Liczba nieprzechyconych wyjątków zgłaszanych w przeglądarce.|cloud/roleName|
|wyjątki/liczba|Tak|Wyjątki|Liczba|Liczba|Łączna liczba wszystkich nieprzechwiczonych wyjątków.|cloud/roleName, cloud/roleInstance, client/type|
|wyjątki/serwer|Nie|Wyjątki serwera|Liczba|Liczba|Liczba nieprzechwconych wyjątków zgłaszanych w aplikacji serwera.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Tak|Wyświetlenia stron|Liczba|Liczba|Liczba wyświetleń strony.|operation/synthetic, cloud/roleName|
|pageViews/duration|Tak|Czas ładowania widoku strony|Milisekund|Średnia|Czas ładowania widoku strony|operation/synthetic, cloud/roleName|
|performanceCounters/exceptionsPerSecond|Tak|Szybkość wyjątków|CountPerSecond|Średnia|Liczba obsłużonych i nieobsłużonych wyjątków zgłaszanych do systemu Windows, w tym wyjątków .NET i wyjątków niezamówionych, które są konwertowane na wyjątki .NET.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Tak|Dostępna pamięć|Bajty|Średnia|Pamięć fizyczna jest natychmiast dostępna do przydzielenia do procesu lub do użytku systemowego.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Tak|Przetwarzanie procesora CPU|Procent|Średnia|Procent czasu, który upłynął, przez który wszystkie wątki procesu używały procesora do wykonywania instrukcji. Może to różnić się od 0 do 100. Ta metryka wskazuje wydajność samego procesu w3wp.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Tak|Szybkość we/wy procesu|BytesPerSecond|Średnia|Łączna liczba bajtów na sekundę odczytywanych i zapisywanych w plikach, sieci i urządzeniach.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tak|Czas procesora|Procent|Średnia|Procent czasu, który procesor spędza w bezczynnych wątkach.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Tak|Przetwarzanie bajtów prywatnych|Bajty|Średnia|Pamięć przypisana wyłącznie do procesów monitorowanych aplikacji.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Tak|Czas wykonywania żądania HTTP|Milisekund|Średnia|Czas wykonywania ostatniego żądania.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Tak|Żądania HTTP w kolejce aplikacji|Liczba|Średnia|Długość kolejki żądań aplikacji.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Tak|Szybkość żądań HTTP|CountPerSecond|Średnia|Szybkość wszystkich żądań do aplikacji na sekundę z ASP.NET.|cloud/roleInstance|
|żądania/liczba|Nie|Żądania serwera|Liczba|Liczba|Liczba ukończonych żądań HTTP.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|żądania/czas trwania|Tak|Czas odpowiedzi serwera|Milisekund|Średnia|Czas między odebraniem żądania HTTP a zakończeniem wysyłania odpowiedzi.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|żądania/niepowodzenie|Nie|Żądania zakończone niepowodzeniem|Liczba|Liczba|Liczba żądań HTTP oznaczonych jako nieudane. W większości przypadków są to żądania z kodem odpowiedzi >= 400 i nie są równe 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|żądania/szybkość|Nie|Szybkość żądań serwera|CountPerSecond|Średnia|Szybkość żądań serwera na sekundę|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|ślady/liczba|Tak|Ślady|Liczba|Liczba|Śledzenie liczby dokumentów|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|c2d.commands.failure|Tak|Wywołania poleceń, które zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich żądań poleceń, które zakończyły się niepowodzeniem zainicjowanych z IoT Central|Brak wymiarów|
|c2d.commands.requestSize|Tak|Rozmiar żądania w wywołaniach poleceń|Bajty|Łącznie|Rozmiar żądania wszystkich żądań poleceń zainicjowanych z IoT Central|Brak wymiarów|
|c2d.commands.responseSize|Tak|Rozmiar odpowiedzi w wywołaniach poleceń|Bajty|Łącznie|Rozmiar odpowiedzi wszystkich odpowiedzi na polecenia zainicjowane z IoT Central|Brak wymiarów|
|c2d.commands.success|Tak|Pomyślne wywołania poleceń|Liczba|Łącznie|Liczba wszystkich pomyślnych żądań poleceń zainicjowanych z IoT Central|Brak wymiarów|
|c2d.property.read.failure|Tak|Nieudane odczyty właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów właściwości zainicjowanych z IoT Central|Brak wymiarów|
|c2d.property.read.success|Tak|Pomyślne odczyty właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości zainicjowanych z IoT Central|Brak wymiarów|
|c2d.property.update.failure|Tak|Nieudane aktualizacje właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości, które zakończyły się niepowodzeniem zainicjowanych z IoT Central|Brak wymiarów|
|c2d.property.update.success|Tak|Pomyślne aktualizacje właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z IoT Central|Brak wymiarów|
|connectedDeviceCount|Nie|Łączna liczba połączonych urządzeń|Liczba|Średnia|Liczba urządzeń podłączonych do IoT Central|Brak wymiarów|
|d2c.property.read.failure|Tak|Nieudane odczyty właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych odczytów właściwości zainicjowanych z urządzeń|Brak wymiarów|
|d2c.property.read.success|Tak|Pomyślne odczyty właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości zainicjowanych z urządzeń|Brak wymiarów|
|d2c.property.update.failure|Tak|Aktualizacje właściwości urządzenia z urządzeń zakończyły się niepowodzeniem|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości, które zakończyły się niepowodzeniem zainicjowanych z urządzeń|Brak wymiarów|
|d2c.property.update.success|Tak|Pomyślne aktualizacje właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z urządzeń|Brak wymiarów|
|d2c.telemetry.ingress.allProtocol|Tak|Łączna liczba prób wysłania komunikatów telemetrii|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do IoT Central danych|Brak wymiarów|
|d2c.telemetry.ingress.success|Tak|Łączna liczba wysłanych komunikatów telemetrii|Liczba|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrii urządzenie-chmura do IoT Central aplikacji|Brak wymiarów|
|dataExport.error|Tak|Błędy eksportu danych|Liczba|Łącznie|Liczba błędów występujących podczas eksportowania danych|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|Tak|Przefiltrowane komunikaty eksportu danych|Liczba|Łącznie|Liczba komunikatów, które zostały przekazane przez filtry podczas eksportowania danych|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.received|Tak|Odebrane komunikaty eksportu danych|Liczba|Łącznie|Liczba komunikatów przychodzących do eksportu danych przed filtrowaniem i przetwarzaniem wzbogacania|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.written|Tak|Zapisane komunikaty eksportu danych|Liczba|Łącznie|Liczba komunikatów zapisywanych w miejscu docelowym|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.statusChange|Tak|Zmiana stanu eksportu danych|Liczba|Łącznie|Liczba zmian stanu|exportId, exportDisplayName, destinationId, destinationDisplayName, status|
|deviceDataUsage|Tak|Łączne użycie danych urządzenia|Bajty|Łącznie|Bajty przesyłane do i z dowolnego urządzenia podłączonego do IoT Central aplikacji|Brak wymiarów|
|provisionedDeviceCount|Nie|Łączna liczba aprowowanych urządzeń|Liczba|Średnia|Liczba urządzeń aprowowanych w IoT Central aplikacji|Brak wymiarów|

## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Nie|Ogólna dostępność usługi|Procent|Średnia|Dostępność żądań obsługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Tak|Łączna liczba trafień interfejsu API usługi|Liczba|Liczba|Łączna liczba trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Nie|Ogólne opóźnienie interfejsu API usługi|Milisekund|Średnia|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Tak|Łączna liczba wyników interfejsu API usługi|Liczba|Liczba|Liczba łącznych wyników interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Ogólna dostępność magazynu|Procent|Średnia|Magazyn żąda dostępności|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Nie|Ogólne nasycenie magazynu|Procent|Średnia|Używana pojemność magazynu|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Tak|Łączna liczba trafień interfejsu API usługi|Liczba|Liczba|Liczba trafień całkowitej liczby trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Tak|Ogólne opóźnienie interfejsu API usługi|Milisekund|Średnia|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Tak|Łączna liczba wyników interfejsu API usługi|Liczba|Liczba|Liczba łącznych wyników interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Tak|Łączna liczba rdzeni procesora CPU w połączonym klastrze|Liczba|Łącznie|Łączna liczba rdzeni procesora CPU w połączonym klastrze||


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BatchBlobCount|Tak|Liczba obiektów blob usługi Batch|Liczba|Średnia|Liczba źródeł danych w zagregowanej partii do pozyskiwania.|baza danych|
|BatchDuration|Tak|Czas trwania partii|Sekundy|Średnia|Czas trwania fazy agregacji w przepływie pozyskiwania.|baza danych|
|BatchesProcessed|Tak|Przetworzone partie|Liczba|Łącznie|Liczba partii zagregowanych do pozyskiwania. Typ przetwarzania wsadowego: czy partia osiągnęła limit czasu przetwarzania wsadowego, rozmiaru danych lub liczby plików ustawiony przez zasady przetwarzania wsadowego|Database, SealReason|
|Batchsize|Tak|Rozmiar wsadu|Bajty|Średnia|Nieskompresowany oczekiwany rozmiar danych w zagregowanej partii do pozyskiwania.|baza danych|
|BlobsDropped|Tak|Porzucone obiekty blob|Liczba|Łącznie|Liczba obiektów blob trwale odrzuconych przez składnik.|Database, ComponentType, ComponentName|
|BlobsProcessed|Tak|Przetworzone obiekty blob|Liczba|Łącznie|Liczba obiektów blob przetworzonych przez składnik.|Database, ComponentType, ComponentName|
|BlobsReceived|Tak|Odebrane obiekty blob|Liczba|Łącznie|Liczba obiektów blob odebranych ze strumienia wejściowego przez składnik.|Database, ComponentType, ComponentName|
|CacheUtilization|Tak|Wykorzystanie pamięci podręcznej|Procent|Średnia|Poziom wykorzystania w zakresie klastra|Brak wymiarów|
|ContinuousExportMaxLatenessMinutes|Tak|Maksymalne opóźnienie eksportu ciągłego|Liczba|Maksimum|Opóźnienie (w minutach) zgłoszone przez zadania eksportu ciągłego w klastrze|Brak wymiarów|
|ContinuousExportNumOfRecordsExported|Tak|Eksport ciągły — liczba wyeksportowanych rekordów|Liczba|Łącznie|Liczba wyeksportowanych rekordów wyzowanych dla każdego artefaktu magazynu zapisanego podczas operacji eksportowania|ContinuousExportName, Database|
|ContinuousExportPendingCount|Tak|Liczba oczekujących eksportów ciągłych|Liczba|Maksimum|Liczba oczekujących zadań eksportu ciągłego gotowych do wykonania|Brak wymiarów|
|ContinuousExportResult|Tak|Wynik eksportu ciągłego|Liczba|Liczba|Wskazuje, czy eksport ciągły zakończył się powodzeniem, czy niepowodzeniem|ContinuousExportName, Result, Database|
|Procesor CPU|Tak|Procesor CPU|Procent|Średnia|Poziom wykorzystania procesora CPU|Brak wymiarów|
|DiscoveryLatency|Tak|Opóźnienie odnajdywania|Sekundy|Średnia|Zgłaszane przez połączenia danych (jeśli istnieją). Czas w sekundach od momentu, gdy komunikat jest w kolejkach lub zdarzenie jest tworzone, dopóki nie zostanie odnaleziony przez połączenie danych. Ten czas nie jest uwzględniany w łącznym Azure Data Explorer pozyskiwania.|ComponentType, ComponentName|
|EventsDropped (Zamapowane zdarzenia)|Tak|Porzucone zdarzenia|Liczba|Łącznie|Liczba zdarzeń trwale porzucanych przez połączenie danych. Zostanie wysłana metryka wyniku pozyskiwania z przyczyną niepowodzenia.|ComponentType, ComponentName|
|EventsProcessed|Tak|Przetworzone zdarzenia|Liczba|Łącznie|Liczba zdarzeń przetworzonych przez klaster|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Tak|Przetworzone zdarzenia (dla usługi Event/IoT Hubs)|Liczba|Łącznie|Liczba zdarzeń przetworzonych przez klaster podczas ich pozyskania ze zdarzenia/IoT Hub|EventStatus|
|EventsReceived|Tak|Odebrane zdarzenia|Liczba|Łącznie|Liczba zdarzeń odebranych przez połączenie danych.|ComponentType, ComponentName|
|ExportUtilization|Tak|Wykorzystanie eksportu|Procent|Maksimum|Wykorzystanie eksportu|Brak wymiarów|
|IngestionLatencyInSeconds|Tak|Opóźnienie pozyskiwania|Sekundy|Średnia|Opóźnienie pozyskiwania danych od czasu odebrania danych w klastrze do momentu, gdy są gotowe do utworzenia zapytania. Okres opóźnienia pozyskiwania zależy od scenariusza pozyskiwania.|Brak wymiarów|
|IngestionResult|Tak|Wynik pozyskiwania|Liczba|Łącznie|Liczba operacji pozyskiwania|IngestionResultDetails|
|PozyskiwanieUtilization|Tak|Wykorzystanie pozyskiwania|Procent|Średnia|Stosunek użytych miejsc pozyskiwania w klastrze|Brak wymiarów|
|PozyskiwanieVolumeInMB|Tak|Wolumin pozyskiwania|Bajty|Łącznie|Ogólna ilość pozyskanych danych do klastra|baza danych|
|InstanceCount|Tak|Liczba wystąpień|Liczba|Średnia|Łączna liczba wystąpień|Brak wymiarów|
|Keepalive|Tak|Podtrzymanie aktywności|Liczba|Średnia|Sprawdzanie sanity wskazuje, że klaster odpowiada na zapytania|Brak wymiarów|
|MaterializedViewAgeMinutes|Tak|Zmaterializowany wiek widoku|Liczba|Średnia|Zmaterializowany wiek widoku w minutach|Database, MaterializedViewName|
|MaterializedViewDataLoss|Tak|Utrata danych zmaterializowanego widoku|Liczba|Maksimum|Wskazuje potencjalną utratę danych w zmaterializowanym widoku|Database, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|Tak|Zmaterializowane ponowne kompilowanie fragmentów widoku|Liczba|Średnia|Liczba przebudowanych zakresów|Database, MaterializedViewName|
|MaterializedViewHealth|Tak|Kondycja zmaterializowanego widoku|Liczba|Średnia|Kondycja zmaterializowanego widoku (1 dla dobrej kondycji, 0 w przypadku braku dobrej kondycji)|Database, MaterializedViewName|
|MaterializedViewRecordsInDelta|Tak|Zmaterializowane rekordy widoku w delcie|Liczba|Średnia|Liczba rekordów w niezmaterializowanej części widoku|Database, MaterializedViewName|
|MaterializedViewResult|Tak|Zmaterializowany wynik widoku|Liczba|Średnia|Wynik procesu materializacji|Database, MaterializedViewName, Result|
|QueryDuration|Tak|Czas trwania zapytania|Milisekund|Średnia|Czas trwania zapytań w sekundach|Querystatus|
|QueryResult (Wyniki zapytania)|Nie|Wynik zapytania|Liczba|Liczba|Łączna liczba zapytań.|Querystatus|
|QueueLength|Tak|Długość kolejki|Liczba|Średnia|Liczba oczekujących komunikatów w kolejce składnika.|Componenttype|
|QueueOldestMessage|Tak|Najstarszy komunikat w kolejce|Liczba|Średnia|Czas w sekundach od wstawienia najstarszego komunikatu w kolejce.|Componenttype|
|ReceivedDataSizeBytes|Tak|Odebrane bajty rozmiaru danych|Bajty|Średnia|Rozmiar danych odebranych przez połączenie danych. Jest to rozmiar strumienia danych lub rozmiar danych pierwotnych, jeśli został podany.|ComponentType, ComponentName|
|StageLatency|Tak|Opóźnienie etapu|Sekundy|Średnia|Skumulowany czas od odnalezienia komunikatu do momentu jego odbierania przez składnik raportowania do przetworzenia (czas odnajdywania jest ustawiany, gdy komunikat jest kolejkowany do kolejki pozyskiwania lub wykryty przez połączenie danych).|Database, ComponentType|
|ParowanieIngestRequestRate|Tak|Pozyskiwanie za pomocą przesyłania strumieniowego — liczba żądań|Liczba|RateRequestsPerSecond|Szybkość żądań pozysłania przesyłania strumieniowego (żądania na sekundę)|Brak wymiarów|
|StreamingIngestDataRate|Tak|Pozyskiwanie za pomocą przesyłania strumieniowego — szybkość danych|Liczba|Średnia|Szybkość pozysłania danych przez przesyłanie strumieniowe (MB na sekundę)|Brak wymiarów|
|StreamingIngestDuration|Tak|Pozyskiwanie za pomocą przesyłania strumieniowego — czas trwania|Milisekund|Średnia|Czas trwania pozysków przesyłanych strumieniowo w milisekundach|Brak wymiarów|
|Wyniki przesyłania strumieniowego|Tak|Wynik pozyskiwania za pomocą przesyłania strumieniowego|Liczba|Średnia|Wynik pozysanych danych przesyłanych strumieniowo|Wynik|
|TotalNumberOfConcurrentQueries|Tak|Łączna liczba zapytań współbieżnych|Liczba|Maksimum|Łączna liczba zapytań współbieżnych|Brak wymiarów|
|TotalNumberOfExtents|Tak|Łączna liczba zakresów|Liczba|Łącznie|Łączna liczba zakresów danych|Brak wymiarów|
|TotalNumberOfThrottledCommands|Tak|Łączna liczba poleceń z ograniczeniami|Liczba|Łącznie|Łączna liczba poleceń z ograniczeniami|Commandtype|
|TotalNumberOfThrottledQueries|Tak|Łączna liczba zapytań z ograniczeniami|Liczba|Maksimum|Łączna liczba zapytań z ograniczeniami|Brak wymiarów|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActionLatency|Tak|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie zakończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsCompleted|Tak|Ukończono akcje |Liczba|Łącznie|Liczba zakończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsFailed|Tak|Akcje nie powiodły się |Liczba|Łącznie|Liczba akcji przepływu pracy nie powiodła się.|Brak wymiarów|
|ActionsSkipped|Tak|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak wymiarów|
|ActionsStarted|Tak|Rozpoczęte akcje |Liczba|Łącznie|Liczba uruchomionych akcji przepływu pracy.|Brak wymiarów|
|ActionsSucceeded|Tak|Akcje zakończyły się pomyślnie |Liczba|Łącznie|Liczba akcji przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|ActionSuccessLatency|Tak|Opóźnienie powodzenia akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|ActionThrottledEvents|Tak|Zdarzenia z ograniczeniami akcji|Liczba|Łącznie|Liczba zdarzeń z ograniczeniami akcji przepływu pracy.|Brak wymiarów|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Tak|Użycie pamięci łącznika dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci łącznika dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Tak|Użycie procesora łącznika dla środowisko usługi integracji|Procent|Średnia|Użycie procesora łącznika dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Tak|Użycie pamięci przepływu pracy dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci przepływu pracy dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Tak|Użycie procesora przepływu pracy dla środowisko usługi integracji|Procent|Średnia|Użycie procesora przepływu pracy dla środowiska usługi integracji.|Brak wymiarów|
|RunFailurePercentage|Tak|Procent niepowodzenia uruchomienia|Procent|Łącznie|Procent przebiegów przepływu pracy nie powiódł się.|Brak wymiarów|
|RunLatency|Tak|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie zakończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCancelled|Tak|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCompleted|Tak|Ukończono przebiegi|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsFailed|Tak|Przebiegi nie powiodły się|Liczba|Łącznie|Liczba przebiegów przepływu pracy nie powiodła się.|Brak wymiarów|
|RunsStarted|Tak|Uruchomione przebiegi|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak wymiarów|
|RunsSucceeded|Tak|Przebiegi zakończyły się pomyślnie|Liczba|Łącznie|Liczba przebiegów przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|RunStartThrottledEvents|Tak|Uruchamianie zdarzeń z ograniczeniami uruchamiania|Liczba|Łącznie|Liczba zdarzeń ograniczenia uruchamiania przepływu pracy.|Brak wymiarów|
|RunSuccessLatency|Tak|Opóźnienie powodzenia uruchomienia|Sekundy|Średnia|Opóźnienie przebiegów pomyślnie uruchomionych przepływów pracy.|Brak wymiarów|
|RunThrottledEvents|Tak|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń z ograniczeniami przepływu pracy lub wyzwalaczy.|Brak wymiarów|
|TriggerFireLatency|Tak|Opóźnienie wyzwalania pożaru |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerLatency|Tak|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie zakończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|WyzwalaczeUzupełnione|Tak|Ukończono wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersFailed|Tak|Wyzwalacze nie powiodły się |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy nie powiodła się.|Brak wymiarów|
|WyzwalaczeFired|Tak|Wyzwalacze wyzwolone |Liczba|Łącznie|Liczba wyzwoleń przepływu pracy.|Brak wymiarów|
|TriggersSkipped|Tak|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersStarted|Tak|Uruchomione wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak wymiarów|
|WyzwalaczeSucceeded|Tak|Wyzwalacze zakończyły się pomyślnie |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|TriggerSuccessLatency|Tak|Opóźnienie powodzenia wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|TriggerThrottledEvents|Tak|Wyzwalanie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń z ograniczeniami wyzwalacza przepływu pracy.|Brak wymiarów|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActionLatency|Tak|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie zakończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsCompleted|Tak|Ukończono akcje |Liczba|Łącznie|Liczba zakończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsFailed|Tak|Akcje nie powiodły się |Liczba|Łącznie|Liczba akcji przepływu pracy, które zakończyły się niepowodzeniem.|Brak wymiarów|
|ActionsSkipped|Tak|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak wymiarów|
|ActionsStarted|Tak|Rozpoczęte akcje |Liczba|Łącznie|Liczba uruchomionych akcji przepływu pracy.|Brak wymiarów|
|ActionsSucceeded|Tak|Akcje zakończyły się pomyślnie |Liczba|Łącznie|Liczba akcji przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|ActionSuccessLatency|Tak|Opóźnienie powodzenia akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|ActionThrottledEvents|Tak|Zdarzenia z ograniczeniami akcji|Liczba|Łącznie|Liczba zdarzeń z ograniczeniami akcji przepływu pracy.|Brak wymiarów|
|BillableActionExecutions|Tak|Rozliczane wykonania akcji|Liczba|Łącznie|Liczba wykonań akcji przepływu pracy, które są rozliczane.|Brak wymiarów|
|BillableTriggerExecutions|Tak|Rozliczane wykonania wyzwalacza|Liczba|Łącznie|Liczba wykonań wyzwalacza przepływu pracy, które są rozliczane.|Brak wymiarów|
|BillingUsageNativeOperation|Tak|Użycie rozliczeń dla wykonań operacji natywnych|Liczba|Łącznie|Liczba wykonań operacji natywnych, które są rozliczane.|Brak wymiarów|
|BillingUsageStandardConnector|Tak|Użycie rozliczeń dla wykonań łącznika standardowego|Liczba|Łącznie|Liczba wykonań łącznika standardowego, które są rozliczane.|Brak wymiarów|
|BillingUsageStorageConsumption|Tak|Rozliczanie użycia dla wykonań użycia magazynu|Liczba|Łącznie|Liczba wykonań użycia magazynu, które są rozliczane.|Brak wymiarów|
|RunFailurePercentage|Tak|Procent niepowodzenia uruchomienia|Procent|Łącznie|Procent przebiegów przepływu pracy nie powiódł się.|Brak wymiarów|
|RunLatency|Tak|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie zakończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCancelled|Tak|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCompleted|Tak|Ukończono przebiegi|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsFailed|Tak|Przebiegi nie powiodły się|Liczba|Łącznie|Liczba przebiegów przepływu pracy nie powiodła się.|Brak wymiarów|
|RunsStarted|Tak|Uruchomione przebiegi|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak wymiarów|
|RunsSucceeded|Tak|Przebiegi zakończyły się pomyślnie|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończyła się pomyślnie.|Brak wymiarów|
|RunStartThrottledEvents|Tak|Uruchamianie zdarzeń z ograniczeniami uruchamiania|Liczba|Łącznie|Liczba zdarzeń ograniczenia uruchamiania przepływu pracy.|Brak wymiarów|
|RunSuccessLatency|Tak|Opóźnienie powodzenia uruchomienia|Sekundy|Średnia|Opóźnienie przebiegów pomyślnie uruchomionych przepływów pracy.|Brak wymiarów|
|RunThrottledEvents|Tak|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń z ograniczeniami przepływu pracy lub wyzwalaczy.|Brak wymiarów|
|TotalBillableExecutions|Tak|Łączna liczba rozliczanych wykonań|Liczba|Łącznie|Liczba wykonań przepływu pracy, które są rozliczane.|Brak wymiarów|
|TriggerFireLatency|Tak|Opóźnienie wyzwalania pożaru |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerLatency|Tak|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie zakończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|WyzwalaczeUzupełnione|Tak|Ukończono wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersFailed|Tak|Wyzwalacze nie powiodły się |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy nie powiodła się.|Brak wymiarów|
|TriggersFired|Tak|Wyzwalacze wyzwolone |Liczba|Łącznie|Liczba wyzwoleń przepływu pracy.|Brak wymiarów|
|TriggersSkipped|Tak|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersStarted|Tak|Uruchomione wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak wymiarów|
|WyzwalaczeSucceededed|Tak|Wyzwalacze zakończyły się pomyślnie |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończyła się pomyślnie.|Brak wymiarów|
|TriggerSuccessLatency|Tak|Opóźnienie powodzenia wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy, które zakończyły się pomyślnie.|Brak wymiarów|
|TriggerThrottledEvents|Tak|Wyzwalanie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń z ograniczeniami wyzwalacza przepływu pracy.|Brak wymiarów|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Rdzenie aktywne|Tak|Rdzenie aktywne|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Aktywne węzły|Tak|Aktywne węzły|Liczba|Średnia|Liczba węzłów acitve. Są to węzły, które aktywnie uruchamiają zadanie.|Scenariusz, ClusterName|
|Anulowanie żądanych przebiegów|Tak|Anulowanie żądanych przebiegów|Liczba|Łącznie|Liczba przebiegów, dla których zażądano anulowania dla tego obszaru roboczego. Liczba jest aktualizowana po otrzymaniu żądania anulowania dla przebiegu.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Anulowane przebiegi|Tak|Anulowane przebiegi|Liczba|Łącznie|Liczba anulowanych przebiegów dla tego obszaru roboczego. Liczba jest aktualizowana po pomyślnym anulowaniu uruchomienia.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Ukończone przebiegi|Tak|Ukończone przebiegi|Liczba|Łącznie|Liczba pomyślnych przebiegów dla tego obszaru roboczego. Liczba jest aktualizowana po zakończeniu uruchomienia i zebrania danych wyjściowych.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Użycie procesora CPU|Tak|Użycie procesora CPU|Liczba|Średnia|Procent wykorzystania w węźle procesora CPU. Wykorzystanie jest zgłaszane w odstępach jednej minuty.|Scenariusz, runId, NodeId, ClusterName|
|błędy|Tak|błędy|Liczba|Łącznie|Liczba błędów uruchamiania w tym obszarze roboczym. Liczba jest aktualizowana za każdym razem, gdy uruchomienie napotka błąd.|Scenariusz|
|Nieudane przebiegi|Tak|Nieudane przebiegi|Liczba|Łącznie|Liczba przebiegów dla tego obszaru roboczego nie powiodła się. Liczba jest aktualizowana w przypadku niepowodzenia uruchomienia.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Finalizowanie przebiegów|Tak|Finalizowanie przebiegów|Liczba|Łącznie|Liczba przebiegów wprowadzonych w stanie finalizacji dla tego obszaru roboczego. Liczba jest aktualizowana po zakończeniu uruchomienia, ale kolekcja danych wyjściowych jest nadal w toku.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Tak|GpuMemoryUtilization|Liczba|Średnia|Procent wykorzystania pamięci w węźle procesora GPU. Wykorzystanie jest zgłaszane w jednominutowych odstępach czasu.|Scenariusz, runId, NodeId, DeviceId, ClusterName|
|Wykorzystanie procesora GPU|Tak|Wykorzystanie procesora GPU|Liczba|Średnia|Procent wykorzystania w węźle procesora GPU. Wykorzystanie jest zgłaszane co minutę.|Scenariusz, runId, NodeId, DeviceId, ClusterName|
|Bezczynne rdzenie|Tak|Bezczynne rdzenie|Liczba|Średnia|Liczba bezczynnych rdzeni|Scenariusz, ClusterName|
|Węzły bezczynne|Tak|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów. Węzły bezczynne to węzły, które nie uruchamiają żadnych zadań, ale mogą akceptować nowe zadanie, jeśli jest dostępne.|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Tak|Opuszczanie rdzeni|Liczba|Średnia|Liczba opuszczających rdzeni|Scenariusz, ClusterName|
|Opuszczanie węzłów|Tak|Opuszczanie węzłów|Liczba|Średnia|Liczba pozostawienia węzłów. Pozostawienie węzłów to węzły, które właśnie zakończyły przetwarzanie zadania i przejdą do stanu bezczynności.|Scenariusz, ClusterName|
|Wdrażanie modelu nie powiodło się|Tak|Wdrażanie modelu nie powiodło się|Liczba|Łącznie|Liczba wdrożeń modeli, które nie powiodły się w tym obszarze roboczym|Scenariusz, kod stanu|
|Rozpoczęto wdrażanie modelu|Tak|Rozpoczęto wdrażanie modelu|Liczba|Łącznie|Liczba wdrożeń modeli uruchomionych w tym obszarze roboczym|Scenariusz|
|Wdrażanie modelu zakończyło się pomyślnie|Tak|Wdrażanie modelu zakończyło się pomyślnie|Liczba|Łącznie|Liczba wdrożeń modeli, które zakończyły się pomyślnie w tym obszarze roboczym|Scenariusz|
|Rejestracja modelu nie powiodła się|Tak|Rejestracja modelu nie powiodła się|Liczba|Łącznie|Liczba rejestracji modelu, które nie powiodły się w tym obszarze roboczym|Scenariusz, kod stanu|
|Rejestracja modelu zakończyła się pomyślnie|Tak|Rejestracja modelu zakończyła się pomyślnie|Liczba|Łącznie|Liczba rejestracji modelu, które zakończyły się pomyślnie w tym obszarze roboczym|Scenariusz|
|Nie odpowiada przebiegów|Tak|Nie odpowiada przebiegów|Liczba|Łącznie|Liczba przebiegów, które nie odpowiadają dla tego obszaru roboczego. Liczba jest aktualizowana, gdy przebieg ma stan Nie odpowiada.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Nie uruchomiliśmy przebiegów|Tak|Nie uruchomiliśmy przebiegów|Liczba|Łącznie|Liczba przebiegów w stanie Nie uruchomiliśmy dla tego obszaru roboczego. Liczba jest aktualizowana po otrzymaniu żądania utworzenia uruchomienia, ale informacje o uruchomieniu nie zostały jeszcze wypełnione. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Wywłaszcze rdzenie|Tak|Wywłaszcze rdzenie|Liczba|Średnia|Liczba wywłaszowanych rdzeni|Scenariusz, ClusterName|
|Wywłaszcze węzły|Tak|Wywłaszcze węzły|Liczba|Średnia|Liczba wywłaszowanych węzłów. Te węzły są węzłami o niskim priorytecie, które są wynoszalne z dostępnej puli węzłów.|Scenariusz, ClusterName|
|Przygotowywanie przebiegów|Tak|Przygotowywanie przebiegów|Liczba|Łącznie|Liczba przebiegów przygotowywanych do pracy w tym obszarze roboczym. Liczba jest aktualizowana, gdy przebieg przechodzi w stan Przygotowywanie podczas przygotowywania środowiska uruchomieniowego.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Przebiegi aprowowania|Tak|Przebiegi aprowowania|Liczba|Łącznie|Liczba przebiegów aprowizowania dla tego obszaru roboczego. Liczba jest aktualizowana, gdy przebieg oczekuje na utworzenie docelowego obiektu obliczeniowego lub aprowizowanie.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Przebiegi w kolejce|Tak|Przebiegi w kolejce|Liczba|Łącznie|Liczba przebiegów, które są w kolejce dla tego obszaru roboczego. Liczba jest aktualizowana, gdy przebieg znajduje się w kolejce w docelowym zamierzonych obiektach obliczeniowych. Może wystąpić podczas oczekiwania na gotowość wymaganych węzłów obliczeniowych.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Procent wykorzystania limitu przydziału|Tak|Procent wykorzystania limitu przydziału|Liczba|Średnia|Procent wykorzystanego limitu przydziału|Scenario, ClusterName, VmFamilyName, VmPriority|
|Uruchomione przebiegi|Tak|Uruchomione przebiegi|Liczba|Łącznie|Liczba przebiegów uruchomionych dla tego obszaru roboczego. Liczba jest aktualizowana po uruchomieniu uruchomienia na wymaganych zasobach.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Uruchamianie przebiegów|Tak|Uruchamianie przebiegów|Liczba|Łącznie|Liczba przebiegów uruchomionych dla tego obszaru roboczego. Liczba jest aktualizowana po wypełnieniu żądania utworzenia informacji o uruchomieniu, takich jak identyfikator uruchomienia|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Łączna liczba rdzeni|Tak|Łączna liczba rdzeni|Liczba|Średnia|Liczba całkowitych rdzeni|Scenariusz, ClusterName|
|Łączna liczba węzłów|Tak|Łączna liczba węzłów|Liczba|Średnia|Łączna liczba węzłów. Ta suma obejmuje niektóre z węzłów aktywnych, węzły bezczynne, węzły bezużyteczni, węzły wstępnie używane, pozostawiając węzły|Scenariusz, ClusterName|
|Rdzenie bezużytelne|Tak|Rdzenie bezużytelne|Liczba|Średnia|Liczba nienadatnych do użytku rdzeni|Scenariusz, ClusterName|
|Węzły bezużytelne|Tak|Węzły bezużytelne|Liczba|Średnia|Liczba węzłów, które nie mogą być używane. Węzły nienadzorowane nie działają z powodu niektórych nierozwiązanych problemów. Platforma Azure odzyska te węzły.|Scenariusz, ClusterName|
|Ostrzeżenia|Tak|Ostrzeżenia|Liczba|Łącznie|Liczba ostrzeżeń o uruchomieniu w tym obszarze roboczym. Liczba jest aktualizowana za każdym razem, gdy uruchomienie napotka ostrzeżenie.|Scenariusz|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Dostępność interfejsów API|ApiCategory, ApiName|
|Użycie|Nie|Użycie|Liczba|Liczba|Liczba wywołań interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AssetCount|Tak|Liczba zasobów|Liczba|Średnia|Ile zasobów zostało już utworzonych na bieżącym koncie usługi Media Service|Brak wymiarów|
|AssetQuota|Tak|Limit przydziału zasobów|Liczba|Średnia|Ile zasobów jest dozwolonych dla bieżącego konta usługi multimediów|Brak wymiarów|
|AssetQuotaUsedPercentage|Tak|Procent użytego limitu przydziału zasobów|Procent|Średnia|Procent użytych zasobów na bieżącym koncie usługi media service|Brak wymiarów|
|ChannelsAndLiveEventsCount|Tak|Liczba wydarzeń na żywo|Liczba|Średnia|Łączna liczba wydarzeń na żywo na bieżącym koncie usługi Media Services|Brak wymiarów|
|ContentKeyPolicyCount|Tak|Liczba zasad klucza zawartości|Liczba|Średnia|Ile zasad klucza zawartości zostało już utworzonych na bieżącym koncie usługi Media Service|Brak wymiarów|
|ContentKeyPolicyQuota|Tak|Limit przydziału zasad klucza zawartości|Liczba|Średnia|Ile jest dozwolonych polis klucza zawartości dla bieżącego konta usługi multimediów|Brak wymiarów|
|ContentKeyPolicyQuotaUsedPercentage|Tak|Procent użytego limitu przydziału zasad klucza zawartości|Procent|Średnia|Procent użytych zasad klucza zawartości na bieżącym koncie usługi Media Service|Brak wymiarów|
|MaxChannelsAndLiveEventsCount|Tak|Maksymalny limit przydziału wydarzeń na żywo|Liczba|Maksimum|Maksymalna dozwolona liczba wydarzeń na żywo na bieżącym koncie usługi Media Services|Brak wymiarów|
|MaxRunningChannelsAndLiveEventsCount|Tak|Maksymalny limit przydziału uruchomionych wydarzeń na żywo|Liczba|Maksimum|Maksymalna dozwolona liczba uruchomionych wydarzeń na żywo na bieżącym koncie usługi Media Services|Brak wymiarów|
|RunningChannelsAndLiveEventsCount|Tak|Liczba uruchomionych wydarzeń na żywo|Liczba|Średnia|Łączna liczba uruchomionych wydarzeń na żywo na bieżącym koncie usługi Media Services|Brak wymiarów|
|StreamingPolicyCount|Tak|Liczba zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego zostało już utworzonych na bieżącym koncie usługi Media Service|Brak wymiarów|
|StreamingPolicyQuota|Tak|Limit przydziału zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego jest dozwolonych dla bieżącego konta usługi multimediów|Brak wymiarów|
|StreamingPolicyQuotaUsedPercentage|Tak|Procent użytego limitu przydziału zasad przesyłania strumieniowego|Procent|Średnia|Procent użytych zasad przesyłania strumieniowego na bieżącym koncie usługi multimediów|Brak wymiarów|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|IngestBitrate|Tak|Transmisja bitów pozysłania wydarzenia na żywo|BitsPerSecond|Średnia|Przychodzący czas transmisji bitów pozytyw dla wydarzenia na żywo, w bitach na sekundę.|Nazwa ścieżki|
|IngestDriftValue|Tak|Wartość dryfu pozyskania wydarzenia na żywo|Sekundy|Maksimum|Dryf między sygnaturą czasową pozysłej zawartości a zegarem systemowym mierzonym w sekundach na minutę. Wartość niezerowa wskazuje, że pozysłana zawartość dociera wolniej niż czas zegara systemowego.|Nazwa ścieżki|
|IngestLastTimestamp|Tak|Znacznik czasu ostatniego pozysłania wydarzenia na żywo|Milisekund|Maksimum|Ostatni znacznik czasu pozysłany dla wydarzenia na żywo.|Nazwa ścieżki|
|LiveOutputLastTimestamp|Tak|Ostatni wyjściowy znacznik czasu|Milisekund|Maksimum|Sygnatura czasowa ostatniego fragmentu przekazanego do magazynu dla danych wyjściowych wydarzenia na żywo.|Nazwa ścieżki|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Procesor CPU|Tak|Użycie procesora|Procent|Średnia|Użycie procesora CPU dla punktów końcowych przesyłania strumieniowego w wersji Premium. Te dane nie są dostępne dla standardowych punktów końcowych przesyłania strumieniowego.|Brak wymiarów|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących w bajtach.|Format OutputFormat|
|EgressBandwidth|Nie|Przepustowość wychodzącą|BitsPerSecond|Średnia|Przepustowość wychodząca w bitach na sekundę.|Brak wymiarów|
|Żądania|Tak|Żądania|Liczba|Łącznie|Żądania do punktu końcowego przesyłania strumieniowego.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Tak|Opóźnienie od końca do końca|Milisekund|Średnia|Średnie opóźnienie dla żądań pomyślnych w milisekundach.|Format OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Tak|Aktywne sesje renderowania|Liczba|Średnia|Łączna liczba aktywnych sesji renderowania|SessionType, SDKVersion|
|AssetsConverted|Tak|Przekonwertowane zasoby|Liczba|Łącznie|Łączna liczba przekonwertowanych zasobów|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AnchorsCreated|Tak|Utworzone kotwice|Liczba|Łącznie|Liczba utworzonych kotwic|DeviceFamily, SDKVersion|
|AnchorsDeleted|Tak|Usunięto kotwice|Liczba|Łącznie|Liczba usuniętych kotwic|DeviceFamily, SDKVersion|
|AnchorsQueried|Tak|Kotwice, dla których zapytania są wyszukiwane|Liczba|Łącznie|Liczba zapytań Spatial Anchors zapytania|DeviceFamily, SDKVersion|
|AnchorsUpdated|Tak|Zaktualizowano kotwice|Liczba|Łącznie|Zaktualizowano liczbę kotwic|DeviceFamily, SDKVersion|
|PosesFound|Tak|Znaleziono poses|Liczba|Łącznie|Zwrócona liczba pozy|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Tak|Łączna liczba kotwic dobowych|Liczba|Średnia|Łączna liczba kotwic — codziennie|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Tak|Rozmiar przydzielony puli|Bajty|Średnia|Aprowizowany rozmiar tej puli|Brak wymiarów|
|VolumePoolAllocatedToVolumeThroughput|Tak|Przepływność przydzielona w puli|BytesPerSecond|Średnia|Suma przepływności wszystkich woluminów należących do puli|Brak wymiarów|
|VolumePoolAllocatedUsed|Tak|Pula przydzielona do rozmiaru woluminu|Bajty|Średnia|Przydzielony rozmiar puli|Brak wymiarów|
|VolumePoolProvisionedThroughput|Tak|Aprowizowana przepływność dla puli|BajtyPerSecond|Średnia|Aprowizowana przepływność tej puli|Brak wymiarów|
|VolumePoolTotalLogicalSize|Tak|Rozmiar zużytej puli|Bajty|Średnia|Suma rozmiaru logicznego wszystkich woluminów należących do puli|Brak wymiarów|
|VolumePoolTotalSnapshotSize|Tak|Łączny rozmiar migawki dla puli|Bajty|Średnia|Suma rozmiaru migawki wszystkich woluminów w tej puli|Brak wymiarów|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AverageReadLatency|Tak|Średnie opóźnienie odczytu|Milisekund|Średnia|Średnie opóźnienie odczytu w milisekundach na operację|Brak wymiarów|
|AverageWriteLatency|Tak|Średnie opóźnienie zapisu|Milisekund|Średnia|Średnie opóźnienie zapisu w milisekundach na operację|Brak wymiarów|
|CbsVolumeBackupActive|Tak|Wstrzymano tworzenie kopii zapasowej woluminu|Liczba|Średnia|Czy zasady tworzenia kopii zapasowych dla woluminu są wstrzymane? 0, jeśli tak, 1, jeśli nie.|Brak wymiarów|
|CbsVolumeLogicalBackupBytes|Tak|Bajty kopii zapasowej woluminu|Bajty|Średnia|Łączna liczba bajtów kopii zapasowej dla tego woluminu.|Brak wymiarów|
|CbsVolumeOperationComplete|Tak|Czy operacja tworzenia kopii zapasowej woluminu została ukończona|Liczba|Średnia|Czy ostatnia operacja tworzenia kopii zapasowej lub przywracania woluminu została ukończona pomyślnie? 1 jeśli tak, 0, jeśli nie.|Brak wymiarów|
|CbsVolumeOperationTransferredBytes|Tak|Kopia zapasowa woluminu ostatnio transferowanych bajtów|Bajty|Średnia|Całkowita liczba bajtów przetransferowanych dla ostatniej operacji tworzenia kopii zapasowej lub przywracania.|Brak wymiarów|
|CbsVolumeProtected|Tak|Czy włączono tworzenie kopii zapasowej woluminów|Liczba|Średnia|Czy dla woluminu jest włączona kopia zapasowa? 1 jeśli tak, 0, jeśli nie.|Brak wymiarów|
|OtherThroughput|Tak|Inna przepływność|BajtyPerSecond|Średnia|Inna przepływność (która nie jest odczytywana ani zapisywana) w bajtach na sekundę|Brak wymiarów|
|Odczyt Iops|Tak|Odczyt iops|CountPerSecond|Średnia|Odczyt operacji we/wy na sekundę|Brak wymiarów|
|ReadThroughput|Tak|Przepływność odczytu|BytesPerSecond|Średnia|Przepływność odczytu w bajtach na sekundę|Brak wymiarów|
|TotalThroughput|Tak|Łączna przepływność|BytesPerSecond|Średnia|Suma całej przepływności w bajtach na sekundę|Brak wymiarów|
|VolumeAllocatedSize|Tak|Rozmiar przydzielonego woluminu|Bajty|Średnia|Aprowizowany rozmiar woluminu|Brak wymiarów|
|VolumeConsumedSizePercentage|Tak|Procent zużytego rozmiaru woluminu|Procent|Średnia|Procent zużytego woluminu, w tym migawek.|Brak wymiarów|
|VolumeLogicalSize|Tak|Rozmiar zużytego woluminu|Bajty|Średnia|Rozmiar logiczny woluminu (używane bajty)|Brak wymiarów|
|VolumeSnapshotSize|Tak|Rozmiar migawki woluminu|Bajty|Średnia|Rozmiar wszystkich migawek w woluminie|Brak wymiarów|
|WriteIops|Tak|Write iops (Zapis we/wy na zapisu)|CountPerSecond|Średnia|Operacje we/wy zapisu na sekundę|Brak wymiarów|
|WriteThroughput|Tak|Przepływność zapisu|BajtyPerSecond|Średnia|Przepływność zapisu w bajtach na sekundę|Brak wymiarów|
|XregionReplicationHealthy|Tak|Czy stan replikacji woluminu jest w dobrej kondycji|Liczba|Średnia|Warunek relacji, 1 lub 0.|Brak wymiarów|
|XregionReplicationLagTime|Tak|Czas opóźnienia replikacji woluminu|Sekundy|Średnia|Czas w sekundach, o który dane dublowane są opóźniane za źródłem.|Brak wymiarów|
|XregionReplicationLastTransferDuration|Tak|Czas ostatniego transferu replikacji woluminu|Sekundy|Średnia|Czas w sekundach, który trwał ostatni transfer.|Brak wymiarów|
|XregionReplicationLastTransferSize|Tak|Rozmiar ostatniego transferu replikacji woluminu|Bajty|Średnia|Całkowita liczba bajtów przetransferowanych w ramach ostatniego transferu.|Brak wymiarów|
|XregionReplicationRelationshipProgress|Tak|Postęp replikacji woluminów|Bajty|Średnia|Łączna ilość danych przesyłanych dla bieżącej operacji transferu.|Brak wymiarów|
|XregionReplicationRelationshipTransferring|Tak|Czy transfer replikacji woluminu|Liczba|Średnia|Określa, czy stan replikacji woluminu to "transfer".|Brak wymiarów|
|XregionReplicationTotalTransferBytes|Tak|Łączny transfer replikacji woluminów|Bajty|Średnia|Skumulowane bajty przeniesione dla relacji.|Brak wymiarów|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Nie|Application Gateway łączny czas|Milisekund|Średnia|Średni czas przetwarzania żądania i wysłania jego odpowiedzi. Jest on obliczany jako średni interwał od Application Gateway odbierania pierwszego bajtu żądania HTTP do czasu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że zwykle obejmuje to czas przetwarzania Application Gateway, czas, w którym pakiety żądań i odpowiedzi są przesyłane przez sieć i czas odpowiedzi serwera zaplecza.|Odbiornik|
|AvgRequestCountPerHealthyHost|Nie|Żądania na minutę na hosta w dobrej kondycji|Liczba|Średnia|Średnia liczba żądań na minutę na hosta zaplecza w dobrej kondycji w puli|BackendSettingsPool|
|BackendConnectTime|Nie|Czas połączenia zaplecza|Milisekund|Średnia|Czas spędzony na nawiązywaniu połączenia z serwerem zaplecza|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Nie|Czas odpowiedzi pierwszego bajtu zaplecza|Milisekund|Średnia|Przedział czasu między rozpoczęciem nawiązywania połączenia z serwerem zaplecza a odebraniem pierwszego bajtu nagłówka odpowiedzi, co zbliża czas przetwarzania serwera zaplecza|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Nie|Czas ostatniego bajtu odpowiedzi zaplecza|Milisekund|Średnia|Przedział czasu między rozpoczęciem nawiązywania połączenia z serwerem zaplecza a odebraniem ostatniego bajtu treści odpowiedzi|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Tak|Stan odpowiedzi zaplecza|Liczba|Łącznie|Liczba kodów odpowiedzi HTTP generowanych przez elementy członkowskie zaplecza. Nie obejmuje to żadnych kodów odpowiedzi generowanych przez Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Tak|Web Application Firewall dystrybucja reguł zablokowanych żądań|Liczba|Łącznie|Web Application Firewall reguły żądań zablokowanych|RuleGroup, RuleId|
|BlockedReqCount|Tak|Web Application Firewall liczba zablokowanych żądań|Liczba|Łącznie|Web Application Firewall liczba zablokowanych żądań|Brak wymiarów|
|Bytesreceived|Tak|Odebrane bajty|Bajty|Łącznie|Całkowita liczba bajtów odebranych przez Application Gateway od klientów|Odbiornik|
|BytesSent|Tak|Wysłane bajty|Bajty|Łącznie|Całkowita liczba bajtów wysłanych przez Application Gateway do klientów|Odbiornik|
|CapacityUnits|Nie|Bieżące jednostki wydajności|Liczba|Średnia|Zużyte jednostki pojemności|Brak wymiarów|
|ClientRtt|Nie|RTT klienta|Milisekund|Średnia|Średni czas rundy między klientami a Application Gateway. Ta metryka wskazuje, jak długo trwa nawiązywanie połączeń i zwracanie potwierdzenia|Odbiornik|
|ComputeUnits|Nie|Bieżące jednostki obliczeniowe|Liczba|Średnia|Zużyte jednostki obliczeniowe|Brak wymiarów|
|Użycie procesora CPU|Nie|Wykorzystanie procesora|Procent|Średnia|Bieżące wykorzystanie procesora CPU Application Gateway|Brak wymiarów|
|CurrentConnections (Bieżące połączenia)|Tak|Bieżące połączenia|Liczba|Łącznie|Liczba bieżących połączeń ustanowionych za pomocą Application Gateway|Brak wymiarów|
|EstimatedBilledCapacityUnits|Nie|Szacowane rozliczane jednostki pojemności|Liczba|Średnia|Szacowane jednostki pojemności, które będą naliczane|Brak wymiarów|
|FailedRequests|Tak|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Liczba nieudanych żądań, które Application Gateway obsłużył|BackendSettingsPool|
|FixedBillableCapacityUnits|Nie|Jednostki stałej wydajności rozliczanej|Liczba|Średnia|Minimalna liczba jednostek wydajności, które będą naliczane|Brak wymiarów|
|HealthyHostCount|Tak|Liczba hostów w dobrej kondycji|Liczba|Średnia|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|MatchedCount|Tak|Web Application Firewall całkowitego rozkładu reguł|Liczba|Łącznie|Web Application Firewall całkowitej dystrybucji reguł dla ruchu przychodzącego|RuleGroup, RuleId|
|NewConnectionsPerSecond|Nie|Nowe połączenia na sekundę|CountPerSecond|Średnia|Nowe połączenia na sekundę ustanowione za pomocą Application Gateway|Brak wymiarów|
|ResponseStatus|Tak|Stan odpowiedzi|Liczba|Łącznie|Stan odpowiedzi HTTP zwrócony przez Application Gateway|HttpStatusGroup|
|Przepływność|Nie|Przepływność|BytesPerSecond|Średnia|Liczba bajtów na sekundę, które Application Gateway obsługiwane|Brak wymiarów|
|TlsProtocol|Tak|Protokół TLS klienta|Liczba|Łącznie|Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, który nawiąował połączenie z Application Gateway. Aby wyświetlić dystrybucję protokołu TLS, filtruj według wymiaru Protokół TLS.|Listener, TlsProtocol|
|TotalRequests|Tak|Łączna liczba żądań|Liczba|Łącznie|Liczba pomyślnych żądań, które Application Gateway obsłużył|BackendSettingsPool|
|UnhealthyHostCount|Tak|Liczba hostów w złej kondycji|Liczba|Średnia|Liczba hostów zaplecza w złej kondycji|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Tak|Liczba trafień reguł aplikacji|Liczba|Łącznie|Liczba trafień reguł aplikacji|Stan, przyczyna, protokół|
|DataProcessed|Tak|Ilość przetworzonych danych|Bajty|Łącznie|Łączna ilość danych przetworzonych przez tę zaporę|Brak wymiarów|
|FirewallHealth|Tak|Stan kondycji zapory|Procent|Średnia|Wskazuje ogólną kondycję tej zapory|Stan, przyczyna|
|NetworkRuleHit|Tak|Liczba trafień reguł sieci|Liczba|Łącznie|Liczba trafień reguł sieci|Stan, przyczyna, protokół|
|SNATPortUtilization|Tak|Wykorzystanie portów SNAT|Procent|Średnia|Procent wychodzących portów SNAT, które są obecnie używane|Protokół|
|Przepływność|Nie|Przepływność|BitsPerSecond|Średnia|Przepływność przetwarzana przez tę zaporę|Brak wymiarów|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|pingmesh|Nie|Stan komunikacji bastionu|Liczba|Średnia|Stan komunikacji to 1, jeśli cała komunikacja jest dobra, i 0, jeśli jest zła.||
|sesje|Nie|Liczba sesji|Liczba|Łącznie|Liczba sesji dla bastionu. Wyświetl w sumie i na wystąpienie.|host|
|suma|Tak|Łączna ilość pamięci|Liczba|Średnia|Statystyka całkowitej ilości pamięci.|host|
|usage_user|Nie|Używane procesory CPU|Liczba|Średnia|Statystyki użycia procesora CPU.|cpu, host|
|protokoły|Tak|Użyta pamięć|Liczba|Średnia|Statystyki użycia pamięci.|host|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Tak|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|Brak wymiarów|
|BitsOutPerSecond|Tak|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|Brak wymiarów|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|QueryVolume|Nie|Wolumin zapytania|Liczba|Łącznie|Liczba zapytań obsługiwanych dla strefy DNS|Brak wymiarów|
|RecordSetCapacityUtilization|Nie|Wykorzystanie pojemności zestawu rekordów|Procent|Maksimum|Procent pojemności zestawu rekordów wykorzystywanej przez strefę DNS|Brak wymiarów|
|RecordSetCount|Nie|Liczba zestawu rekordów|Liczba|Maksimum|Liczba zestawów rekordów w strefie DNS|Brak wymiarów|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ArpAvailability|Tak|Dostępność usługi ARP|Procent|Średnia|Dostępność ARP z MSEE do wszystkich elementów równorzędnych.|PeeringType, Peer|
|BgpAvailability|Tak|Dostępność protokołu BGP|Procent|Średnia|Dostępność protokołu BGP z routera MSEE dla wszystkich elementów równorzędnych.|PeeringType, Peer|
|BitsInPerSecond|Nie|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|PeeringType, DeviceRole|
|BitsOutPerSecond|Nie|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|Nie|GlobalReachBitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Nie|GlobalReachBitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Tak|DroppedInBitsPerSecond|BitsPerSecond|Średnia|Bity danych przychodzących porzucanych na sekundę|Brak wymiarów|
|QosDropBitsOutPerSecond|Tak|DroppedOutBitsPerSecond|BitsPerSecond|Średnia|Bity wychodzące danych porzucanych na sekundę|Brak wymiarów|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Tak|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|Brak wymiarów|
|BitsOutPerSecond|Tak|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|Brak wymiarów|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Nie|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|Nie|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertvert nieistnieące|Tak|Liczba tras anonsowanych do elementu równorzędnego (wersja zapoznawcza)|Liczba|Maksimum|Liczba tras anonsowanych do elementu równorzędnego przez bramę ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Tak|Liczba tras nauczone na komunikacji równorzędnej (wersja zapoznawcza)|Liczba|Maksimum|Liczba tras nauczone z elementu równorzędnego przez bramę ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Tak|Wykorzystanie procesora|Liczba|Średnia|Użycie procesora CPU bramy usługi ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|Nie|Częstotliwość zmian tras (wersja zapoznawcza)|Liczba|Łącznie|Częstotliwość zmian tras w bramie usługi ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|Nie|Liczba maszyn wirtualnych w Virtual Network (wersja zapoznawcza)|Liczba|Maksimum|Liczba maszyn wirtualnych w Virtual Network|Brak wymiarów|
|ExpressRouteGatewayPacketsPerSecond|Nie|Pakiety na sekundę|CountPerSecond|Średnia|Liczba pakietów bramy usługi ExpressRoute|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AdminState|Tak|AdminState|Liczba|Średnia|Stan administratora portu|Link|
|LineProtocol|Tak|LineProtocol|Liczba|Średnia|Stan protokołu liniowego portu|Link|
|PortBitsInPerSecond|Tak|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|Link|
|PortBitsOutPerSecond|Tak|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|Link|
|RxLightLevel|Tak|RxLightLevel|Liczba|Średnia|Poziom światła Rx w dBm|Link, Tor|
|TxLightLevel|Tak|TxLightLevel|Liczba|Średnia|Poziom światła TX w dBm|Link, Tor|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Tak|Procent kondycji zaplecza|Procent|Średnia|Procent pomyślnych sond kondycji z serwera proxy HTTP/S do zaplecza|Backend, BackendPool|
|BackendRequestCount|Tak|Liczba żądań zaplecza|Liczba|Łącznie|Liczba żądań wysłanych z serwera proxy HTTP/S do za zaplecza|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Tak|Opóźnienie żądania zaplecza|Milisekund|Średnia|Czas obliczony od momentu wysłania żądania przez serwer proxy HTTP/S do zaplecza do momentu, gdy serwer proxy HTTP/S odebrał ostatni bajt odpowiedzi z zaplecza|Zaplecze|
|BillableResponseSize|Tak|Rozliczany rozmiar odpowiedzi|Bajty|Łącznie|Liczba rozliczanych bajtów (co najmniej 2 KB na żądanie) wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Tak|Liczba żądań|Liczba|Łącznie|Liczba żądań klientów obsługiwanych przez serwer proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Tak|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysłanych jako żądania od klientów do serwera proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Tak|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Tak|Całkowite opóźnienie|Milisekund|Średnia|Czas obliczony od momentu otrzymania żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Tak|Web Application Firewall liczba żądań|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Nie|Przydzielone porty SNAT|Liczba|Średnia|Łączna liczba portów SNAT przydzielonych w okresie|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|Bytecount|Tak|Liczba bajtów|Bajty|Łącznie|Łączna liczba bajtów przesłanych w okresie|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Tak|Stan sondy kondycji|Liczba|Średnia|Średni Load Balancer kondycji na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|Liczba pakietów|Tak|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów przesłanych w okresie|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Tak|Liczba połączeń SNAT|Liczba|Łącznie|Łączna liczba nowych połączeń SNAT utworzonych w okresie|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Tak|SYN Count|Liczba|Łącznie|Łączna liczba pakietów SYN przesłanych w okresie|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|Nie|Używane porty SNAT|Liczba|Średnia|Łączna liczba portów SNAT używanych w okresie|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Tak|Dostępność ścieżki danych|Liczba|Średnia|Średnia Load Balancer dostępności ścieżki danych na czas trwania|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bytecount|Tak|Bajty|Bajty|Łącznie|Łączna liczba bajtów przesłanych w okresie|Protokół, kierunek|
|DatapathAvailability|Tak|Dostępność ścieżki danych (wersja zapoznawcza)|Liczba|Średnia|NAT Gateway dostępność ścieżki danych|Brak wymiarów|
|Liczba pakietów|Tak|Pakiety|Liczba|Łącznie|Łączna liczba pakietów przesłanych w okresie|Protokół, kierunek|
|PacketDropCount|Tak|Porzucone pakiety|Liczba|Łącznie|Liczba porzuconych pakietów|Brak wymiarów|
|SNATConnectionCount|Tak|Liczba połączeń SNAT|Liczba|Łącznie|Łączna liczba współbieżnych aktywnych połączeń|Protocol, ConnectionState|
|TotalConnectionCount|Tak|Łączna liczba połączeń SNAT|Liczba|Łącznie|Łączna liczba aktywnych połączeń SNAT|Protokół|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Tak|Odebrane bajty|Bajty|Łącznie|Liczba bajtów odebranych przez interfejs sieciowy|Brak wymiarów|
|BytesSentRate|Tak|Wysłane bajty|Bajty|Łącznie|Liczba bajtów wysłanych przez interfejs sieciowy|Brak wymiarów|
|PacketsReceivedRate|Tak|Odebrane pakiety|Liczba|Łącznie|Liczba pakietów odebranych przez interfejs sieciowy|Brak wymiarów|
|PacketsSentRate|Tak|Wysłane pakiety|Liczba|Łącznie|Liczba pakietów wysłanych przez interfejs sieciowy|Brak wymiarów|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Tak|Średni czas rundy (ms) (klasyczny)|Milisekund|Średnia|Średni czas rundy sieci (ms) dla sond monitorowania łączności wysyłanych między źródłem i miejscem docelowym|Brak wymiarów|
|ChecksFailedPercent|Tak|Procent nieudanych testów|Procent|Średnia|% nieudanych testów monitorowania łączności|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent (SondyFailedPercent)|Tak|% nieudanych sond (wersja klasyczna)|Procent|Średnia|% nieudanych sond monitorowania łączności|Brak wymiarów|
|RoundTripTimeMs|Tak|Round-Trip czasu (ms)|Milisekund|Średnia|Czas rundy w milisekundach dla kontroli monitorowania łączności|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|Testresult|Tak|Wynik testu|Liczba|Średnia|Wynik testu monitora połączeń|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|P2SBandwidth|Tak|Przepustowość P2S bramy|BytesPerSecond|Średnia|Średnia przepustowość bramy typu punkt-lokacja w bajtach na sekundę|Brak wymiarów|
|P2SConnectionCount|Tak|Liczba połączeń typu P2S|Liczba|Łącznie|Liczba połączeń punkt-lokacja bramy|Protokół|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|QueryVolume|Tak|Wolumin zapytań|Liczba|Łącznie|Liczba zapytań obsługiwanych dla Prywatna strefa DNS strefie|Brak wymiarów|
|RecordSetCapacityUtilization|Nie|Wykorzystanie pojemności zestawu rekordów|Procent|Maksimum|Procent pojemności zestawu rekordów wykorzystywanej przez strefę Prywatna strefa DNS rekordów|Brak wymiarów|
|RecordSetCount|Tak|Liczba zestawu rekordów|Liczba|Maksimum|Liczba zestawów rekordów w strefie Prywatna strefa DNS rekordu|Brak wymiarów|
|VirtualNetworkLinkCapacityUtilization|Nie|Virtual Network wykorzystania pojemności łącza|Procent|Maksimum|Procent pojemności Virtual Network Link wykorzystywanej przez strefę Prywatna strefa DNS danych|Brak wymiarów|
|VirtualNetworkLinkCount|Tak|Virtual Network liczby linków|Liczba|Maksimum|Liczba sieci wirtualnych połączonych ze strefą Prywatna strefa DNS wirtualnej|Brak wymiarów|
|VirtualNetworkWithRegistrationCapacityUtilization|Nie|Virtual Network wykorzystania pojemności linku rejestracji|Procent|Maksimum|Procent Virtual Network z pojemnością automatycznej rejestracji wykorzystywaną przez strefę Prywatna strefa DNS rejestracji|Brak wymiarów|
|VirtualNetworkWithRegistrationLinkCount|Tak|Virtual Network linku rejestracji|Liczba|Maksimum|Liczba sieci wirtualnych połączonych z strefą Prywatna strefa DNS z włączoną automatyczną rejestracją|Brak wymiarów|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PEBytesIn|Tak|Bajty w|Liczba|Łącznie|Łączna liczba bajtów wychodzącego|PrivateEndpointId|
|PEBytesOut|Tak|Bajty wychodzące|Liczba|Łącznie|Łączna liczba bajtów wychodzącego|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PLSBytesIn|Tak|Bajty w|Liczba|Łącznie|Całkowita liczba bajtów wychodzącego|PrivateLinkServiceId|
|PLSBytesOut|Tak|Bajty wychodzące|Liczba|Łącznie|Całkowita liczba bajtów wychodzącego|PrivateLinkServiceId|
|PLSNatPortsUsage|Tak|Użycie portów nat|Procent|Średnia|Użycie portów nat|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bytecount|Tak|Liczba bajtów|Bajty|Łącznie|Łączna liczba bajtów przesłanych w okresie|Port, kierunek|
|BytesDroppedDDoS|Tak|Liczba porzucanych bajtów przychodzących DDoS|BajtyPerSecond|Maksimum|Liczba porzucanych bajtów przychodzących DDoS|Brak wymiarów|
|BytesForwardedDDoS|Tak|Liczba bajtów przychodzących przesyłanych dalej DDoS|BytesPerSecond|Maksimum|Liczba bajtów przychodzących przesyłanych dalej DDoS|Brak wymiarów|
|BytesInDDoS|Tak|Liczba bajtów przychodzących DDoS|BytesPerSecond|Maksimum|Liczba bajtów przychodzących DDoS|Brak wymiarów|
|DDoSTriggerSYNPackets|Tak|Pakiety SYN ruchu przychodzącego do wyzwalania środków zaradczych dotyczących ataków DDoS|CountPerSecond|Maksimum|Pakiety SYN ruchu przychodzącego do wyzwalania środków zaradczych dotyczących ataków DDoS|Brak wymiarów|
|DDoSTriggerTCPPackets|Tak|Pakiety TCP ruchu przychodzącego do wyzwalania środków zaradczych dotyczących ataków DDoS|CountPerSecond|Maksimum|Pakiety TCP ruchu przychodzącego do wyzwalania środków zaradczych dotyczących ataków DDoS|Brak wymiarów|
|DDoSTriggerUDPPackets|Tak|Pakiety UDP ruchu przychodzącego do wyzwalania środków zaradczych dotyczących ataków DDoS|CountPerSecond|Maksimum|Pakiety UDP ruchu przychodzącego do wyzwalania środków zaradczych dotyczących ataków DDoS|Brak wymiarów|
|IfUnderDDoSAttack|Tak|Atak DDoS|Liczba|Maksimum|Atak DDoS|Brak wymiarów|
|Liczba pakietów|Tak|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów przesłanych w okresie|Port, kierunek|
|PacketsDroppedDDoS|Tak|Porzucone pakiety przychodzące DDoS|CountPerSecond|Maksimum|Porzucone pakiety przychodzące DDoS|Brak wymiarów|
|PacketsForwardedDDoS|Tak|Przekierowyne pakiety przychodzące DDoS|CountPerSecond|Maksimum|Przekierowyne pakiety przychodzące DDoS|Brak wymiarów|
|PacketsInDDoS|Tak|Liczba DDoS pakietów przychodzących|CountPerSecond|Maksimum|Liczba DDoS pakietów przychodzących|Brak wymiarów|
|SynCount|Tak|SYN Count|Liczba|Łącznie|Łączna liczba pakietów SYN przesłanych w okresie|Port, kierunek|
|TCPBytesDroppedDDoS|Tak|Liczba bajtów przychodzących TCP porzucanych przez protokół DDoS|BytesPerSecond|Maksimum|Liczba bajtów przychodzących TCP porzucanych przez protokół DDoS|Brak wymiarów|
|TCPBytesForwardedDDoS|Tak|Przychodzące bajty TCP przekazane przez protokół DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP przekazane przez protokół DDoS|Brak wymiarów|
|TCPBytesInDDoS|Tak|Liczba bajtów przychodzących protokołu TCP DDoS|BytesPerSecond|Maksimum|Liczba bajtów przychodzących PROTOKOŁU TCP (DDoS)|Brak wymiarów|
|TCPPacketsDroppedDDoS|Tak|Porzucone pakiety PRZYCHODZĄCE TCP DDoS|CountPerSecond|Maksimum|Porzucone DDoS przychodzących pakietów TCP|Brak wymiarów|
|TCPPacketsForwardedDDoS|Tak|Przychodzące pakiety TCP przekazane przez usługi DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP przekazane przez usługi DDoS|Brak wymiarów|
|TCPPacketsInDDoS|Tak|Przychodzące pakiety TCP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP DDoS|Brak wymiarów|
|UDPBytesDroppedDDoS|Tak|Liczba bajtów przychodzących UDP porzucanych przez DDoS|BajtyPerSecond|Maksimum|Liczba bajtów UDP przychodzących porzuconego protokołu DDoS|Brak wymiarów|
|UDPBytesForwardedDDoS|Tak|Liczba bajtów UDP przychodzących przesyłanych dalej DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP przekazane DDoS|Brak wymiarów|
|UDPBytesInDDoS|Tak|Liczba bajtów przychodzących UDP protokołu DDoS|BytesPerSecond|Maksimum|Liczba bajtów przychodzących UDP protokołu DDoS|Brak wymiarów|
|UDPPacketsDroppedDDoS|Tak|Porzucone pakiety przychodzące UDP DDoS|CountPerSecond|Maksimum|Porzucone pakiety przychodzące UDP DDoS|Brak wymiarów|
|UDPPacketsForwardedDDoS|Tak|Przychodzące pakiety UDP przekazane przez DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP przekazane DDoS|Brak wymiarów|
|UDPPacketsInDDoS|Tak|Przychodzące pakiety UDP — DDoS|CountPerSecond|Maksimum|Liczba zadań DDoS dla przychodzących pakietów UDP|Brak wymiarów|
|VipAvailability (Niedostępność adresów VIP)|Tak|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność adresu IP na czas trwania|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Tak|Stan punktu końcowego według punktu końcowego|Liczba|Maksimum|1, jeśli stan sondy punktu końcowego to "Włączone", w przeciwnym razie 0.|Nazwa punktu końcowego|
|QpsByEndpoint|Tak|Zapytania zwracane przez punkt końcowy|Liczba|Łącznie|Liczba zwrotów Traffic Manager punktu końcowego w danym okresie|Nazwa punktu końcowego|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AverageBandwidth|Tak|Przepustowość S2S bramy|BytesPerSecond|Średnia|Średnia przepustowość bramy typu lokacja-lokacja w bajtach na sekundę|Brak wymiarów|
|ExpressRouteGatewayCountOfRoutesAdvertvert nieistnieący|Tak|Liczba tras anonsowanych do elementu równorzędnego (wersja zapoznawcza)|Liczba|Maksimum|Liczba tras anonsowanych do elementu równorzędnego przez bramę ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Tak|Liczba tras nauczone z elementu równorzędnego (wersja zapoznawcza)|Liczba|Maksimum|Liczba tras nauczone z elementu równorzędnego przez bramę ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Tak|Wykorzystanie procesora|Liczba|Średnia|Użycie procesora CPU bramy usługi ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|Nie|Częstotliwość zmian tras (wersja zapoznawcza)|Liczba|Łącznie|Częstotliwość zmian tras w bramie usługi ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|Nie|Liczba maszyn wirtualnych w Virtual Network (wersja zapoznawcza)|Liczba|Maksimum|Liczba maszyn wirtualnych w Virtual Network|Brak wymiarów|
|ExpressRouteGatewayPacketsPerSecond|Nie|Pakiety na sekundę|CountPerSecond|Średnia|Liczba pakietów bramy usługi ExpressRoute|roleInstance|
|P2SBandwidth|Tak|Przepustowość P2S bramy|BajtyPerSecond|Średnia|Średnia przepustowość bramy typu punkt-lokacja w bajtach na sekundę|Brak wymiarów|
|P2SConnectionCount|Tak|Liczba połączeń typu P2S|Liczba|Maksimum|Liczba połączeń punkt-lokacja bramy|Protokół|
|TunnelAverageBandwidth|Tak|Przepustowość tunelu|BajtyPerSecond|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tak|Liczba bajtów ruchu wychodzącego dla tunelu|Bajty|Łącznie|Bajty wychodzące tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tak|Porzucone pakiety ruchu wychodzącego tunelu z powodu niezgodności TS|Liczba|Łącznie|Liczba porzuconych pakietów wychodzących z powodu niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tak|Liczba pakietów ruchu wychodzącego dla tunelu|Liczba|Łącznie|Liczba pakietów wychodzących tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tak|Liczba bajtów ruchu przychodzącego dla tunelu|Bajty|Łącznie|Liczba bajtów przychodzących tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tak|Porzucone pakiety ruchu przychodzącego tunelu z powodu niezgodności TS|Liczba|Łącznie|Liczba porzuconych pakietów przychodzących z powodu niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tak|Pakiety przychodzące tunelu|Liczba|Łącznie|Liczba pakietów przychodzących tunelu|ConnectionName, RemoteIP|
|TunnelNatAllocations|Nie|Alokacje nat tunelu|Liczba|Łącznie|Liczba alokacji dla reguły NAT w tunelu|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes|Nie|Bajty natywne tunelu|Bajty|Łącznie|Liczba bajtów, które zostały nakreślone w tunelu przez regułę NAT |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|Nie|Pakiety NATed tunelu|Liczba|Łącznie|Liczba pakietów, które zostały nakreślone w tunelu przez regułę NAT|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|Nie|Tunelowanie przepływów NAT|Liczba|Łącznie|Liczba przepływów NAT w tunelu według typu przepływu i reguły NAT|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|Nie|Porzucanie pakietów NAT tunelu|Liczba|Łącznie|Liczba pakietów NATed w tunelu, które spadły według typu porzucania i reguły NAT|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|Nie|Odwrotne nakreślone bajty tunelu|Bajty|Łącznie|Liczba bajtów, które zostały odwrócone na podstawie natłoku NAT w tunelu przez regułę NAT|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|Nie|Tunelowanie odwróconych pakietów NAT|Liczba|Łącznie|Liczba pakietów w tunelu, które zostały odwrócone przez regułę NAT|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tak|Czas rundy dla polecenia ping do maszyny wirtualnej|Milisekund|Średnia|Czas rundy dla ping wysłanych do docelowej maszyny wirtualnej|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Tak|Nieudane polecenia ping do maszyny wirtualnej|Procent|Średnia|Procent nieudanych ping do łącznej liczby wysłanych ping docelowej maszyny wirtualnej|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Komunikacja równorzędnaAvailability|Tak|Dostępność protokołu BGP|Procent|Średnia|Dostępność protokołu BGP między virtualRouter i zdalnych elementów równorzędnych|Element równorzędny|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AverageBandwidth|Tak|Przepustowość S2S bramy|BytesPerSecond|Średnia|Średnia przepustowość bramy typu lokacja-lokacja w bajtach na sekundę|Brak wymiarów|
|TunnelAverageBandwidth|Tak|Przepustowość tunelu|BajtyPerSecond|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tak|Liczba bajtów ruchu wychodzącego dla tunelu|Bajty|Łącznie|Bajty wychodzące tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tak|Porzucone pakiety ruchu wychodzącego tunelu z powodu niezgodności TS|Liczba|Łącznie|Liczba porzuconych pakietów wychodzących z powodu niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tak|Liczba pakietów ruchu wychodzącego dla tunelu|Liczba|Łącznie|Liczba pakietów wychodzących tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tak|Liczba bajtów ruchu przychodzącego dla tunelu|Bajty|Łącznie|Liczba bajtów przychodzących tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tak|Porzucone pakiety ruchu przychodzącego tunelu z powodu niezgodności TS|Liczba|Łącznie|Liczba porzuconych pakietów przychodzących z powodu niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tak|Pakiety przychodzące tunelu|Liczba|Łącznie|Liczba pakietów przychodzących tunelu|ConnectionName, RemoteIP|
|TunnelNatAllocations|Nie|Alokacje nat tunelu|Liczba|Łącznie|Liczba alokacji dla reguły NAT w tunelu|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes|Nie|Bajty natywne tunelu|Bajty|Łącznie|Liczba bajtów, które zostały na podstawie natłoku na tunelu przez regułę NAT |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|Nie|Pakiety NATed tunelowania|Liczba|Łącznie|Liczba pakietów, które zostały natchowane przez regułę NAT w tunelu|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|Nie|Tunelowanie przepływów NAT|Liczba|Łącznie|Liczba przepływów NAT w tunelu według typu przepływu i reguły NAT|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|Nie|Porzucanie pakietów NAT tunelu|Liczba|Łącznie|Liczba pakietów NATed w tunelu, które spadły według typu porzucania i reguły NAT|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|Nie|Odwrotne nakreślone bajty tunelu|Bajty|Łącznie|Liczba bajtów, które zostały odwrócone na podstawie natłoku NAT w tunelu przez regułę NAT|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|Nie|Tunelowanie odwróconych pakietów NAT|Liczba|Łącznie|Liczba pakietów w tunelu, które zostały odwrócone przez regułę NAT|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Przychodzące|Tak|Komunikaty przychodzące|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań interfejsu API wysyłania. |Brak wymiarów|
|incoming.all.failedrequests|Tak|Wszystkie żądania przychodzące, które zakończyły się niepowodzeniem|Liczba|Łącznie|Łączna liczba żądań przychodzących, które zakończyły się niepowodzeniem dla centrum powiadomień|Brak wymiarów|
|incoming.all.requests|Tak|Wszystkie żądania przychodzące|Liczba|Łącznie|Łączna liczba żądań przychodzących dla centrum powiadomień|Brak wymiarów|
|incoming.scheduled|Tak|Wysłane zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowane zaplanowane powiadomienia wypychane|Brak wymiarów|
|incoming.scheduled.cancel|Tak|Anulowane zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowane zaplanowane powiadomienia wypychane|Brak wymiarów|
|installation.all|Tak|Operacje zarządzania instalacją|Liczba|Łącznie|Operacje zarządzania instalacją|Brak wymiarów|
|installation.delete|Tak|Usuwanie operacji instalacji|Liczba|Łącznie|Usuwanie operacji instalacji|Brak wymiarów|
|installation.get|Tak|Uzyskiwanie operacji instalacji|Liczba|Łącznie|Uzyskiwanie operacji instalacji|Brak wymiarów|
|installation.patch|Tak|Operacje instalacji poprawek|Liczba|Łącznie|Operacje instalacji poprawek|Brak wymiarów|
|installation.upsert|Tak|Tworzenie lub aktualizowanie operacji instalacji|Liczba|Łącznie|Tworzenie lub aktualizowanie operacji instalacji|Brak wymiarów|
|notificationhub.pushes|Tak|Wszystkie powiadomienia wychodzące|Liczba|Łącznie|Wszystkie powiadomienia wychodzące centrum powiadomień|Brak wymiarów|
|outgoing.allpns.badorexpiredchannel|Tak|Błędy zły lub wygasły kanał|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ identyfikator kanału/tokenu/registrationId w rejestracji wygasł lub jest nieprawidłowy.|Brak wymiarów|
|outgoing.allpns.channelerror|Tak|Błędy kanału|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ kanał był nieprawidłowy, nie został skojarzony z poprawną ograniczoną lub wygasłą aplikacją.|Brak wymiarów|
|outgoing.allpns.invalidpayload|Tak|Błędy ładunku|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu zwrócenia błędu złego ładunku przez program PNS.|Brak wymiarów|
|outgoing.allpns.pnserror|Tak|Błędy zewnętrznego systemu powiadomień|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu problemu z komunikacją z siecią PNS (z wyłączeniem problemów z uwierzytelnianiem).|Brak wymiarów|
|outgoing.allpns.success|Tak|Powiadomienia pomyślne|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak wymiarów|
|outgoing.apns.badchannel|Tak|Błąd słabego kanału systemu APNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu nieprawidłowego tokenu (kod stanu usługi APNS: 8).|Brak wymiarów|
|outgoing.apns.expiredchannel|Tak|Błąd wygasłego kanału WPNS|Liczba|Łącznie|Liczba tokenów unieważnionych przez kanał opinii usługi APNS.|Brak wymiarów|
|outgoing.apns.invalidcredentials|Tak|Błędy autoryzacji WPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ usługi PNS nie zaakceptowały podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|outgoing.apns.invalidnotificationsize|Tak|Błąd nieprawidłowego rozmiaru powiadomienia WPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu zbyt dużego ładunku (kod stanu APNS: 7).|Brak wymiarów|
|outgoing.apns.pnserror|Tak|Błędy aPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu błędów komunikacji z apns.|Brak wymiarów|
|outgoing.apns.success|Tak|Pomyślne powiadomienia WPNS|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak wymiarów|
|outgoing.gcm.authenticationerror|Tak|Błędy uwierzytelniania usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ system powiadomień systemu plików nie zaakceptował podanych poświadczeń, poświadczenia są blokowane lub identyfikator SenderId nie jest poprawnie skonfigurowany w aplikacji (wynik GCM: MismatchedSenderId).|Brak wymiarów|
|outgoing.gcm.badchannel|Tak|Błąd złego kanału usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ identyfikator registrationId w rejestracji nie został rozpoznany (wynik gcm: nieprawidłowa rejestracja).|Brak wymiarów|
|outgoing.gcm.expiredchannel|Tak|Błąd wygasłego kanału usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ wartość registrationId w rejestracji wygasła (wynik usługi GCM: NotRegistered).|Brak wymiarów|
|outgoing.gcm.invalidcredentials|Tak|Błędy autoryzacji usługi GCM (nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ usługi PNS nie zaakceptowały podanych poświadczeń lub poświadczenia są blokowane.|Brak wymiarów|
|outgoing.gcm.invalidnotificationformat|Tak|Nieprawidłowy format powiadomień usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ ładunek nie został poprawnie sformatowany (wynik usługi GCM: InvalidDataKey lub InvalidTtl).|Brak wymiarów|
|outgoing.gcm.invalidnotificationsize|Tak|Błąd nieprawidłowego rozmiaru powiadomienia usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu zbyt dużego ładunku (wynik usługi GCM: MessageTooBig).|Brak wymiarów|
|outgoing.gcm.pnserror|Tak|Błędy usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu błędów komunikacji z gcm.|Brak wymiarów|
|outgoing.gcm.success|Tak|Powiadomienia o pomyślnych działaniach usługi GCM|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak wymiarów|
|outgoing.gcm.throttled|Tak|Powiadomienia o ograniczeniach usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu ograniczenia tej aplikacji przez usługę GCM (kod stanu usługi GCM: 501–599 lub wynik: Niedostępne).|Brak wymiarów|
|outgoing.gcm.wrongchannel|Tak|Błąd nieprawidłowego kanału usługi GCM|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ identyfikator registrationId w rejestracji nie jest skojarzony z bieżącą aplikacją (wynik GCM: InvalidPackageName).|Brak wymiarów|
|outgoing.mpns.authenticationerror|Tak|Błędy uwierzytelniania MPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ usługi PNS nie zaakceptowały podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|outgoing.mpns.badchannel|Tak|Błąd złego kanału MPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ nie rozpoznano channelURI w rejestracji (nie znaleziono stanu MPNS: 404).|Brak wymiarów|
|wychodzące.mpns.channeldisconnected|Tak|Kanał MPNS jest rozłączony|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ w rejestracji został odłączony numer CHANNELURI (nie znaleziono stanu MPNS: 412).|Brak wymiarów|
|outgoing.mpns.dropped|Tak|Porzucone powiadomienia MPNS|Liczba|Łącznie|Liczba wypchnięć, które zostały porzucone przez usługę MPNS (nagłówek odpowiedzi MPNS: X-NotificationStatus: QueueFull lub Suppressed).|Brak wymiarów|
|outgoing.mpns.invalidcredentials|Tak|Nieprawidłowe poświadczenia MPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ usługi PNS nie zaakceptowały podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|outgoing.mpns.invalidnotificationformat|Tak|Nieprawidłowy format powiadomień MPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu zbyt dużego ładunku powiadomienia.|Brak wymiarów|
|outgoing.mpns.pnserror|Tak|Błędy MPNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu błędów komunikacji z programem MPNS.|Brak wymiarów|
|outgoing.mpns.success|Tak|Powiadomienia o pomyślnych powiadomieniach mpns|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak wymiarów|
|outgoing.mpns.throttled|Tak|Powiadomienia o ograniczeniach mpns|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ mpns ogranicza tę aplikację (WNS MPNS: 406 Not Acceptable).|Brak wymiarów|
|outgoing.wns.authenticationerror|Tak|Błędy uwierzytelniania usługi WNS|Liczba|Łącznie|Powiadomienie nie jest dostarczane z powodu błędów komunikacji z nieprawidłowymi poświadczeniami usługi Windows Live lub nieprawidłowym tokenem.|Brak wymiarów|
|outgoing.wns.badchannel|Tak|Błąd złego kanału systemu WNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ nie rozpoznano channelURI w rejestracji (nie znaleziono stanu systemu WNS: 404).|Brak wymiarów|
|outgoing.wns.channeldisconnected|Tak|Odłączony kanał WNS|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ pole ChannelURI w rejestracji jest ograniczone (nagłówek odpowiedzi usługi WNS: X-WNS-DeviceConnectionStatus: disconnected).|Brak wymiarów|
|outgoing.wns.channelthrottled|Tak|Kanał WNS z ograniczeniami|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ pole ChannelURI w rejestracji jest ograniczone (nagłówek odpowiedzi WNS: X-WNS-NotificationStatus:channelThrottled).|Brak wymiarów|
|outgoing.wns.dropped|Tak|Porzucone powiadomienia WNS|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ w rejestracji jest ograniczona przepustowość (X-WNS-NotificationStatus: porzucone, ale nie X-WNS-DeviceConnectionStatus: disconnected).|Brak wymiarów|
|outgoing.wns.expiredchannel|Tak|Błąd wygasłego kanału WNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się z powodu wygaśnięcia channelURI (stan systemu WNS: 410 Nie powiodło się).|Brak wymiarów|
|outgoing.wns.invalidcredentials|Tak|Błędy autoryzacji usługi WNS (nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ usługi PNS nie zaakceptowały podanych poświadczeń lub poświadczenia są zablokowane. (Usługa Windows Live nie rozpoznaje poświadczeń).|Brak wymiarów|
|outgoing.wns.invalidnotificationformat|Tak|Nieprawidłowy format powiadomień systemu WNS|Liczba|Łącznie|Format powiadomienia jest nieprawidłowy (stan systemu WNS: 400). Należy pamiętać, że WNS nie odrzuca wszystkie nieprawidłowe ładunki.|Brak wymiarów|
|outgoing.wns.invalidnotificationsize|Tak|Błąd nieprawidłowego rozmiaru powiadomienia systemu WNS|Liczba|Łącznie|Ładunek powiadomienia jest zbyt duży (stan systemu WNS: 413).|Brak wymiarów|
|outgoing.wns.invalidtoken|Tak|Błędy autoryzacji usługi WNS (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do usługi WNS jest nieprawidłowy (stan usługi WNS: 401 Brak autoryzacji).|Brak wymiarów|
|outgoing.wns.pnserror|Tak|Błędy systemu WNS|Liczba|Łącznie|Powiadomienie nie jest dostarczane z powodu błędów komunikacji z usługą WNS.|Brak wymiarów|
|outgoing.wns.success|Tak|Powiadomienia o pomyślnym pomyślnym wytycym WNS|Liczba|Łącznie|Liczba wszystkich pomyślnych powiadomień.|Brak wymiarów|
|outgoing.wns.throttled|Tak|Powiadomienia z ograniczeniami usług WNS|Liczba|Łącznie|Liczba wypchnięć, które nie powiodły się, ponieważ usługi WNS ogranicza tę aplikację (stan usługi WNS: 406 Nie jest akceptowalny).|Brak wymiarów|
|outgoing.wns.tokenproviderunreachable|Tak|Błędy autoryzacji systemu WNS (nieosiągalne)|Liczba|Łącznie|Usługa Windows Live jest nieozywalna.|Brak wymiarów|
|outgoing.wns.wrongtoken|Tak|Błędy autoryzacji usługi WNS (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do usługi WNS jest prawidłowy, ale dla innej aplikacji (stan usługi WNS: 403 Zabronione). Może się tak zdarzyć, jeśli w rejestracji jest skojarzony z inną aplikacją. Sprawdź, czy aplikacja klienca jest skojarzona z tą samą aplikacją, której poświadczenia znajdują się w centrum powiadomień.|Brak wymiarów|
|registration.all|Tak|Operacje rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji rejestracji (tworzenie aktualizuje zapytania i usunięcia). |Brak wymiarów|
|registration.create|Tak|Operacje tworzenia rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych rejestracji.|Brak wymiarów|
|registration.delete|Tak|Operacje usuwania rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych usunięcia rejestracji.|Brak wymiarów|
|registration.get|Tak|Operacje odczytu rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych zapytań dotyczących rejestracji.|Brak wymiarów|
|registration.update|Tak|Operacje aktualizacji rejestracji|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji rejestracji.|Brak wymiarów|
|scheduled.pending|Tak|Oczekujące zaplanowane powiadomienia|Liczba|Łącznie|Oczekujące zaplanowane powiadomienia|Brak wymiarów|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Average_% dostępnej pamięci|Tak|% dostępnej pamięci|Liczba|Średnia|Average_% dostępnej pamięci|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% dostępnej przestrzeni wymiany|Tak|% dostępnej przestrzeni wymiany|Liczba|Średnia|Average_% dostępnej przestrzeni wymiany|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% zatwierdzone bajty w użyciu|Tak|Procent zatwierdzonego bajtów w użyciu|Liczba|Średnia|Average_% zatwierdzone bajty w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu DPC|Tak|Czas DPC (procent)|Liczba|Średnia|Average_% czasu DPC|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% wolnych i-węzłów|Tak|% wolnych i-węzłów|Liczba|Średnia|Average_% wolnych i-węzłów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% wolnego miejsca|Tak|% wolnego miejsca|Liczba|Średnia|Average_% wolnego miejsca|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu bezczynności|Tak|Czas bezczynności (%)|Liczba|Średnia|Average_% czasu bezczynności|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu przerwania|Tak|Czas przerwania (w procentach)|Liczba|Średnia|Average_% czasu przerwania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu oczekiwania we/wy|Tak|Procent czasu oczekiwania we/wy|Liczba|Średnia|Average_% czasu oczekiwania we/wy|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu|Tak|% czasu|Liczba|Średnia|Average_% czasu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu uprzywilejowanego|Tak|% czasu uprzywilejowanego|Liczba|Średnia|Average_% czasu uprzywilejowanego|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % czasu procesora|Tak|Czas procesora (%)|Liczba|Średnia|Average_ % czasu procesora|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% używanych i-wode|Tak|% używanych i-wy|Liczba|Średnia|Average_% używanych i-wode|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% używanej pamięci|Tak|% używanej pamięci|Liczba|Średnia|Average_% używanej pamięci|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% używanego miejsca|Tak|% używanego miejsca|Liczba|Średnia|Average_% używanego miejsca|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% używanej przestrzeni wymiany|Tak|Procent użytej przestrzeni wymiany|Liczba|Średnia|Average_% używanej przestrzeni wymiany|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu użytkownika|Tak|% czasu użytkownika|Liczba|Średnia|Average_% czasu użytkownika|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Tak|Dostępna pamięć w MB|Liczba|Średnia|Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB pamięci|Tak|Dostępna pamięć (MB)|Liczba|Średnia|Average_Available mb pamięci|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available mb/s|Tak|Zamiana dostępnych mb|Liczba|Średnia|Average_Available mb/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. wartość operacji dysku na sek./odczyt|Tak|Średnia liczba sekund dysku/odczyt|Liczba|Średnia|Average_Avg. wartość operacji dysku na sek./odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Transfery dysku/s|Tak|Średnia liczba dysków na sekundę/transfer|Liczba|Średnia|Average_Avg. Transfery dysku/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. dysku na sek./zapis|Tak|Średnia liczba dysków na sekundę/zapis|Liczba|Średnia|Average_Avg. dysku na sek./zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes odebrane/s|Tak|Bajty odebrane/s|Liczba|Średnia|Average_Bytes odebrane/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes wysłane/s|Tak|Bajty wysłane/s|Liczba|Średnia|Average_Bytes wysłane/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes łącznie/s|Tak|Całkowita liczba bajtów/s|Liczba|Średnia|Average_Bytes łącznie/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current długość kolejki dysku|Tak|Bieżąca długość kolejki dysku|Liczba|Średnia|Average_Current długość kolejki dysku|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk bajtów odczytu/s|Tak|Bajty odczytu dysku/s|Liczba|Średnia|Average_Disk bajtów odczytu/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk odczytu/s|Tak|Odczyty dysku/s|Liczba|Średnia|Average_Disk odczyty/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk transfery/s|Tak|Transfery dysków/s|Liczba|Średnia|Average_Disk transfery/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk bajtów zapisu na sekundę|Tak|Bajty zapisu dysku/s|Liczba|Średnia|Average_Disk bajtów zapisu na sekundę|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk zapisu/s|Tak|Zapis na dysku/s|Liczba|Średnia|Average_Disk zapisu/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Tak|Bezpłatne megabajty|Liczba|Średnia|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci fizycznej|Tak|Bezpłatna pamięć fizyczna|Liczba|Średnia|Average_Free pamięci fizycznej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free spacji w plikach stronicowania|Tak|Wolne miejsce w plikach stronicowania|Liczba|Średnia|Average_Free spacji w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci wirtualnej|Tak|Bezpłatna pamięć wirtualna|Liczba|Średnia|Average_Free pamięci wirtualnej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical dysku w bajtach/s|Tak|Bajty dysku logicznego/s|Liczba|Średnia|Average_Logical dysku w bajtach/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page odczytu/s|Tak|Odczyty stron/s|Liczba|Średnia|Average_Page odczytu/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page zapisu na sekundę|Tak|Zapis stron/s|Liczba|Średnia|Average_Page zapisu/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Tak|Strony/s|Liczba|Średnia|Average_Pages/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct uprzywilejowany czas|Tak|Pct Privileged Time|Liczba|Średnia|Average_Pct uprzywilejowany czas|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct czas użytkownika|Tak|Czas użytkownika pct|Liczba|Średnia|Average_Pct czas użytkownika|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical bajtów dysku na sekundę|Tak|Bajty dysku fizycznego/s|Liczba|Średnia|Average_Physical bajtów dysku na sekundę|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Tak|Procesy|Liczba|Średnia|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor długość kolejki|Tak|Długość kolejki procesora|Liczba|Średnia|Average_Processor długość kolejki|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Tak|Rozmiar przechowywany w plikach stronicowania|Liczba|Średnia|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajtów|Tak|Całkowita liczba bajtów|Liczba|Średnia|Average_Total bajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|odebrane Average_Total bajtów|Tak|Całkowita liczba odebranych bajtów|Liczba|Średnia|odebrane Average_Total bajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total przesłanych bajtów|Tak|Całkowita liczba przesłanych bajtów|Liczba|Średnia|Average_Total przesłanych bajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizje|Tak|Łączna liczba kolizji|Liczba|Średnia|Average_Total kolizje|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total odebrane pakiety|Tak|Łączna liczba odebranych pakietów|Liczba|Średnia|Average_Total odebrane pakiety|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total przesyłane pakiety|Tak|Łączna liczba przesłanych pakietów|Liczba|Średnia|Average_Total przesyłane pakiety|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total błędy rx|Tak|Łączna liczba błędów rx|Liczba|Średnia|Average_Total błędy rx|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total błędów TX|Tak|Łączna liczba błędów TX|Liczba|Średnia|Average_Total błędów TX|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Tak|Czas pracy|Liczba|Średnia|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used mb (swap space)|Tak|Używane miejsce wymiany MB|Liczba|Średnia|Average_Used mb /s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used pamięci (kBytes)|Tak|Używana pamięć (kb/s)|Liczba|Średnia|Average_Used pamięci (kBytes)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used pamięci (MB)|Tak|Używana pamięć (MB)|Liczba|Średnia|Average_Used pamięci (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Tak|Użytkownicy|Liczba|Średnia|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual pamięci współdzielone|Tak|Wirtualna pamięć współdzielona|Liczba|Średnia|Average_Virtual współdzielona|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zdarzenie|Tak|Zdarzenie|Liczba|Średnia|Zdarzenie|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Puls|Tak|Puls|Liczba|Łącznie|Puls|Computer, OSType, Version, SourceComputerId|
|Aktualizacja|Tak|Aktualizacja|Liczba|Średnia|Aktualizacja|Komputer, Produkt, Klasyfikacja, UpdateState, Opcjonalne, Zatwierdzone|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Tak|Szybkość ruchu wychodzącego|BitsPerSecond|Średnia|Szybkość ruchu wychodzącego w bitach na sekundę|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Tak|Szybkość ruchu wychodzącego|BitsPerSecond|Średnia|Szybkość ruchu wychodzącego w bitach na sekundę|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability|Tak|Dostępność sesji|Liczba|Średnia|Dostępność sesji komunikacji równorzędnej|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PrefixLatency|Tak|Opóźnienie prefiksu|Milisekund|Średnia|Opóźnienie prefiksu mediany|PrefixName (Nazwa prefiksu)|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|memory_metric|Tak|Pamięć (Gen1)|Bajty|Średnia|Memory (pamięć). Zakres 0–3 GB dla A1, 0–5 GB dla A2, 0–10 GB dla A3, 0–25 GB dla A4, 0–50 GB dla A5 i 0–100 GB dla A6. Obsługiwane tylko w Power BI Embedded generacji 1.|Brak wymiarów|
|memory_thrashing_metric|Tak|Przerzucanie pamięci (zestawy danych) (Gen1)|Procent|Średnia|Średni czas przerzucania pamięci. Obsługiwane tylko w Power BI Embedded generacji 1.|Brak wymiarów|
|qpu_high_utilization_metric|Tak|Wysokie wykorzystanie procesora QPU (Gen1)|Liczba|Łącznie|Wysokie wykorzystanie QPU w ostatniej minucie, 1 dla wysokiego wykorzystania QPU, w przeciwnym razie 0. Obsługiwane tylko w Power BI Embedded generacji 1.|Brak wymiarów|
|QueryDuration|Tak|Czas trwania zapytania (zestawy danych) (Gen1)|Milisekund|Średnia|Czas trwania zapytania języka DAX w ostatnim interwale. Obsługiwane tylko w Power BI Embedded generacji 1.|Brak wymiarów|
|QueryPoolJobQueueLength|Tak|Długość kolejki zadań puli zapytań (zestawy danych) (Gen1)|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytania. Obsługiwane tylko w Power BI Embedded generacji 1.|Brak wymiarów|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ScanCancelled|Tak|Skanowanie zostało anulowane|Liczba|Łącznie|Wskazuje liczbę anulowanych skanowań.||
|ScanCompleted|Tak|Ukończono skanowanie|Liczba|Łącznie|Wskazuje liczbę pomyślnie zakończonych skanowań.||
|ScanFailed (Nie można przeskanować)|Tak|Skanowanie nie powiodło się|Liczba|Łącznie|Wskazuje liczbę nieudanych skanowań.||
|ScanTimeTaken|Tak|Czas skanowania|Sekundy|Łącznie|Wskazuje łączny czas skanowania w sekundach.||


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Łącznie|Łączna liczba połączeń ActiveConnections dla microsoft.relay.|EntityName (Nazwa jednostki)|
|ActiveListeners|Nie|ActiveListeners|Liczba|Łącznie|Łączna liczba activelistenerów dla microsoft.relay.|EntityName|
|BytesTransferred|Tak|BytesTransferred|Bajty|Łącznie|Łączna liczba bajtówTransferred dla microsoft.relay.|EntityName|
|ListenerConnections-ClientError|Nie|ListenerConnections-ClientError|Liczba|Łącznie|ClientError on ListenerConnections for Microsoft.Relay (KlientError na listenerConnections for Microsoft.Relay).|EntityName, OperationResult|
|ListenerConnections-ServerError|Nie|ListenerConnections-ServerError|Liczba|Łącznie|ServerError on ListenerConnections for Microsoft.Relay .ServerError on ListenerConnections for Microsoft.Relay (SerwerError na listenerConnections for Microsoft.Relay).|EntityName, OperationResult|
|ListenerConnections-Success|Nie|ListenerConnections-Success|Liczba|Łącznie|Successful ListenerConnections for Microsoft.Relay (Pomyślne połączenia odbiornika dla microsoft.relay).|EntityName, OperationResult|
|ListenerConnections-TotalRequests|Nie|ListenerConnections-TotalRequests|Liczba|Łącznie|Total ListenerConnections for Microsoft.Relay (Łączna liczba połączeń odbiorników dla usługi Microsoft.Relay).|EntityName (Nazwa jednostki)|
|ListenerDisconnects|Nie|ListenerDisconnects|Liczba|Łącznie|Total ListenerDisconnects for Microsoft.Relay (Łączna liczba połączeń Odbiornika dla usługi Microsoft.Relay).|EntityName (Nazwa jednostki)|
|SenderConnections-ClientError|Nie|SenderConnections-ClientError|Liczba|Łącznie|ClientError on SenderConnections for Microsoft.Relay (Usługa ClientError w systemie SenderConnections dla usługi Microsoft.Relay).|EntityName, OperationResult|
|SenderConnections-ServerError|Nie|SenderConnections-ServerError|Liczba|Łącznie|ServerError on SenderConnections for Microsoft.Relay (ServerError on SenderConnections for Microsoft.Relay).|EntityName, OperationResult|
|SenderConnections-Success|Nie|SenderConnections-Success|Liczba|Łącznie|Successful SenderConnections for Microsoft.Relay (Pomyślne połączenia SenderConnections dla usługi Microsoft.Relay).|EntityName, OperationResult|
|SenderConnections-TotalRequests|Nie|SenderConnections-TotalRequests|Liczba|Łącznie|Łączna liczba żądań SenderConnections dla microsoft.relay.|EntityName|
|SenderDisconnects|Nie|SenderDisconnects|Liczba|Łącznie|Łączna liczba połączeń SenderDisconnects dla microsoft.relay.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Opóźnienie|Tak|Dane opóźnień żądań przychodzących HTTP|Liczba|Średnia|Dane opóźnień żądań przychodzących HTTP|Metoda, Przestrzeń nazw, RequestRegion, ResourceType, Microsoft.SubscriptionId|
|Ruch|Tak|Ruch|Liczba|Średnia|Ruch HTTP|RequestRegion, StatusCode, StatusCodeClass, ResourceType, Namespace, Microsoft.SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|SearchLatency|Tak|Opóźnienie wyszukiwania|Sekundy|Średnia|Średnie opóźnienie wyszukiwania dla usługi wyszukiwania|Brak wymiarów|
|SearchQueriesPerSecond|Tak|Zapytania wyszukiwania na sekundę|CountPerSecond|Średnia|Zapytania wyszukiwania na sekundę dla usługi wyszukiwania|Brak wymiarów|
|ThrottledSearchQueriesPercentage|Tak|Procent zapytań wyszukiwania z ograniczeniami|Procent|Średnia|Procent zapytań wyszukiwania, które zostały ograniczone dla usługi wyszukiwania|Brak wymiarów|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Łącznie|Łączna liczba aktywnych połączeń dla Microsoft.ServiceBus.||
|ActiveMessages|Nie|Liczba aktywnych komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba aktywnych komunikatów w kolejce/temacie.|EntityName (Nazwa jednostki)|
|ConnectionsClosed|Nie|Zamknięte połączenia.|Liczba|Średnia|Połączenia zamknięte dla Microsoft.ServiceBus.|EntityName|
|PołączeniaOtwierone|Nie|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla Microsoft.ServiceBus.|Nazwa jednostki|
|CPUXNS|Nie|Procesor CPU (przestarzałe)|Procent|Maksimum|Metryka użycia procesora CPU przestrzeni nazw usługi Service Bus w chmurze Premium. Ta metryka jest przestarzała. Zamiast tego użyj metryki procesora CPU (NamespaceCpuUsage).|Replika|
|DeadletteredMessages|Nie|Liczba utraconych komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba utraconych komunikatów w kolejce/temacie.|EntityName|
|IncomingMessages|Tak|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla Microsoft.ServiceBus.|EntityName|
|IncomingRequests|Tak|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla Microsoft.ServiceBus.|Nazwa jednostki|
|Komunikaty|Nie|Liczba komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba komunikatów w kolejce/temacie.|EntityName (Nazwa jednostki)|
|NamespaceCpuUsage|Nie|Procesor CPU|Procent|Maksimum|Metryka użycia procesora CPU przestrzeni nazw usługi Service Bus w chmurze Premium.|Replika|
|NamespaceMemoryUsage|Nie|Użycie pamięci|Procent|Maksimum|Metryka użycia pamięci przestrzeni nazw usługi Service Bus w chmurze w chmurze.|Replika|
|WychodząceMessages|Tak|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla Microsoft.ServiceBus.|EntityName (Nazwa jednostki)|
|ScheduledMessages|Nie|Liczba zaplanowanych komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba zaplanowanych komunikatów w kolejce/temacie.|EntityName (Nazwa jednostki)|
|ServerErrors (ServerErrors)|Nie|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla Microsoft.ServiceBus.|EntityName, OperationResult|
|Rozmiar|Nie|Rozmiar|Bajty|Średnia|Rozmiar kolejki/tematu w bajtach.|EntityName (Nazwa jednostki)|
|SuccessfulRequests|Nie|Żądania pomyślne|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw|EntityName, OperationResult|
|ThrottledRequests|Nie|Żądania ograniczone.|Liczba|Łącznie|Ograniczone żądania dla Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Nie|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkownika dla Microsoft.ServiceBus.|EntityName, OperationResult|
|WSXNS|Nie|Użycie pamięci (przestarzałe)|Procent|Maksimum|Metryka użycia pamięci przestrzeni nazw premium usługi Service Bus. Ta metryka jest przestarzała. Zamiast tego użyj metryki Użycie pamięci (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActualCpu|Nie|ActualCpu|Liczba|Średnia|Rzeczywiste użycie procesora CPU w milirdzeniach|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|Nie|ActualMemory|Bajty|Średnia|Rzeczywiste użycie pamięci w MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|Nie|AllocatedCpu|Liczba|Średnia|Procesor przydzielony do tego kontenera z rdzeniami milisekwu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|Nie|AllocatedMemory|Bajty|Średnia|Pamięć przydzielona do tego kontenera w MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|Nie|ApplicationStatus|Liczba|Średnia|Stan Service Fabric Mesh aplikacji|ApplicationName, Status|
|ContainerStatus|Nie|ContainerStatus|Liczba|Średnia|Stan kontenera w Service Fabric Mesh aplikacji|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|Wykorzystanie procesora CPU|Nie|Wykorzystanie procesora CPU|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako procent przydzielonego procesora CPU|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|Użycie pamięci|Nie|Użycie pamięci|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako procent przydzielonego procesora CPU|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|Nie|RestartCount|Liczba|Średnia|Liczba ponownego uruchamiania kontenera w Service Fabric Mesh aplikacji|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|Nie|ServiceReplicaStatus|Liczba|Średnia|Stan kondycji repliki usługi w Service Fabric Mesh aplikacji|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ServiceStatus|Nie|ServiceStatus|Liczba|Średnia|Stan kondycji usługi w Service Fabric Mesh aplikacji|ApplicationName, Status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ConnectionCount|Tak|Liczba połączeń|Liczba|Maksimum|Ilość połączenia użytkownika.|Punkt końcowy|
|InboundTraffic|Tak|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|Brak wymiarów|
|MessageCount|Tak|Liczba komunikatów|Liczba|Łącznie|Łączna liczba komunikatów.|Brak wymiarów|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący usługi|Brak wymiarów|
|SystemErrors|Tak|Błędy systemu|Procent|Maksimum|Procent błędów systemowych|Brak wymiarów|
|UserErrors|Tak|Błędy użytkownika|Procent|Maksimum|Procent błędów użytkowników|Brak wymiarów|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|InboundTraffic|Tak|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|Brak wymiarów|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący usługi|Brak wymiarów|
|TotalConnectionCount|Tak|Liczba połączeń|Liczba|Maksimum|Ilość połączenia użytkownika.|Brak wymiarów|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Tak|Średni procent użycia procesora CPU|Procent|Średnia|Średni procent użycia procesora CPU|Brak wymiarów|
|io_bytes_read|Tak|Odczytane bajty we/wy|Bajty|Średnia|Odczytane bajty we/wy|Brak wymiarów|
|io_bytes_written|Tak|Zapisane bajty we/wy|Bajty|Średnia|Zapisane bajty we/wy|Brak wymiarów|
|io_requests|Tak|Liczba żądań we/wy|Liczba|Średnia|Liczba żądań we/wy|Brak wymiarów|
|reserved_storage_mb|Tak|Zarezerwowane miejsce do magazynowania|Liczba|Średnia|Zarezerwowane miejsce do magazynowania|Brak wymiarów|
|storage_space_used_mb|Tak|Używane miejsce do magazynowania|Liczba|Średnia|Używane miejsce do magazynowania|Brak wymiarów|
|virtual_core_count|Tak|Liczba rdzeni wirtualnych|Liczba|Średnia|Liczba rdzeni wirtualnych|Brak wymiarów|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_queries|Tak|Aktywne zapytania|Liczba|Łącznie|Aktywne zapytania we wszystkich grupach obciążeń. Dotyczy tylko magazynów danych.|Brak wymiarów|
|allocated_data_storage|Tak|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielony magazyn danych. Nie dotyczy magazynów danych.|Brak wymiarów|
|app_cpu_billed|Tak|Rozliczane procesory CPU aplikacji|Liczba|Łącznie|Rozliczane procesory CPU aplikacji. Dotyczy bez serwera baz danych.|Brak wymiarów|
|app_cpu_percent|Tak|Procent użycia procesora CPU aplikacji|Procent|Średnia|Procent użycia procesora CPU aplikacji. Dotyczy bez serwera baz danych.|Brak wymiarów|
|app_memory_percent|Tak|Procent pamięci aplikacji|Procent|Średnia|Procent pamięci aplikacji. Dotyczy bez serwera baz danych.|Brak wymiarów|
|base_blob_size_bytes|Tak|Rozmiar podstawowego magazynu obiektów blob|Bajty|Maksimum|Rozmiar podstawowego magazynu obiektów blob. Dotyczy baz danych w hiperskali.|Brak wymiarów|
|blocked_by_firewall|Tak|Zablokowany przez zaporę|Liczba|Łącznie|Zablokowany przez zaporę|Brak wymiarów|
|cache_hit_percent|Tak|Procent trafień w pamięci podręcznej|Procent|Maksimum|Wartość procentowa w pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak wymiarów|
|cache_used_percent|Tak|Procent użytej pamięci podręcznej|Procent|Maksimum|Procent użytej pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak wymiarów|
|connection_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|connection_successful|Tak|Pomyślne połączenia|Liczba|Łącznie|Pomyślne połączenia|Brak wymiarów|
|cpu_limit|Tak|Limit procesora CPU|Liczba|Średnia|Limit procesora CPU. Dotyczy baz danych opartych na rdzeniu wirtualnych.|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|cpu_used|Tak|Używane procesory CPU|Liczba|Średnia|Używane procesory CPU. Dotyczy baz danych opartych na rdzeniu wirtualnych.|Brak wymiarów|
|Zakleszczenie|Tak|Zakleszczenia|Liczba|Łącznie|Zakleszczenia. Nie dotyczy magazynów danych.|Brak wymiarów|
|diff_backup_size_bytes|Tak|Rozmiar magazynu różnicowej kopii zapasowej|Bajty|Maksimum|Skumulowany rozmiar magazynu różnicowej kopii zapasowej. Dotyczy baz danych opartych na rdzeniu wirtualnych. Nie dotyczy baz danych w hiperskali.|Brak wymiarów|
|dtu_consumption_percent|Tak|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dtu_limit|Tak|DTU Limit|Liczba|Średnia|Limit jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dtu_used|Tak|Używane jednostki DTU|Liczba|Średnia|Używane jednostki DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dwu_consumption_percent|Tak|Procent jednostek DWU|Procent|Maksimum|Procent jednostek DWU. Dotyczy tylko magazynów danych.|Brak wymiarów|
|dwu_limit|Tak|Limit jednostek DWU|Liczba|Maksimum|Limit jednostek DWU. Dotyczy tylko magazynów danych.|Brak wymiarów|
|dwu_used|Tak|Używane jednostek DWU|Liczba|Maksimum|Używana jednostka DWU. Dotyczy tylko magazynów danych.|Brak wymiarów|
|full_backup_size_bytes|Tak|Rozmiar magazynu pełnej kopii zapasowej|Bajty|Maksimum|Skumulowany rozmiar magazynu pełnej kopii zapasowej. Dotyczy baz danych opartych na rdzeniu wirtualnych. Nie dotyczy baz danych w hiperskali.|Brak wymiarów|
|local_tempdb_usage_percent|Tak|Procent lokalnej bazy danych tempdb|Procent|Średnia|Procent lokalnej bazy danych tempdb. Dotyczy tylko magazynów danych.|Brak wymiarów|
|log_backup_size_bytes|Tak|Rozmiar magazynu kopii zapasowej dziennika|Bajty|Maksimum|Skumulowany rozmiar magazynu kopii zapasowej dziennika. Dotyczy baz danych opartych na rdzeniu wirtualnych i hiperskali.|Brak wymiarów|
|log_write_percent|Tak|Procent we/wy dziennika|Procent|Średnia|Procent we/wy dziennika. Nie dotyczy magazynów danych.|Brak wymiarów|
|memory_usage_percent|Tak|Procent pamięci|Procent|Maksimum|Procent pamięci. Dotyczy tylko magazynów danych.|Brak wymiarów|
|physical_data_read_percent|Tak|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|Brak wymiarów|
|queued_queries|Tak|Zapytania w kolejce|Liczba|Łącznie|Zapytania w kolejce we wszystkich grupach obciążeń. Dotyczy tylko magazynów danych.|Brak wymiarów|
|sessions_percent|Tak|Procent sesji|Procent|Średnia|Procent sesji. Nie dotyczy magazynów danych.|Brak wymiarów|
|snapshot_backup_size_bytes|Tak|Rozmiar magazynu kopii zapasowej migawki|Bajty|Maksimum|Skumulowany rozmiar magazynu kopii zapasowej migawki. Dotyczy baz danych w hiperskali.|Brak wymiarów|
|sqlserver_process_core_percent|Tak|SQL Server procent rdzeni procesu|Procent|Maksimum|Użycie procesora jako procentu procesu usługi SQL DB. Nie dotyczy magazynów danych.|Brak wymiarów|
|sqlserver_process_memory_percent|Tak|SQL Server procent pamięci procesu|Procent|Maksimum|Użycie pamięci jako procentu procesu bazy danych SQL. Nie dotyczy magazynów danych.|Brak wymiarów|
|magazyn|Tak|Używane miejsce na dane|Bajty|Maksimum|Używane miejsce na dane. Nie dotyczy magazynów danych.|Brak wymiarów|
|storage_percent|Tak|Procent użytej przestrzeni danych|Procent|Maksimum|Procent użytej przestrzeni danych. Nie dotyczy magazynów danych ani baz danych w hiperskali.|Brak wymiarów|
|tempdb_data_size|Tak|Rozmiar pliku danych bazy danych Tempdb (kilobajty)|Liczba|Maksimum|Miejsce używane w plikach danych tempdb w kilobajtach. Nie dotyczy magazynów danych.|Brak wymiarów|
|tempdb_log_size|Tak|Rozmiar pliku dziennika bazy danych Tempdb (kilobajty)|Liczba|Maksimum|Miejsce używane w pliku dziennika transakcji bazy danych tempdb w kilobajtach. Nie dotyczy magazynów danych.|Brak wymiarów|
|tempdb_log_used_percent|Tak|Procent używanego dziennika bazy danych tempdb|Procent|Maksimum|Procent użytego miejsca w pliku dziennika transakcji bazy danych tempdb. Nie dotyczy magazynów danych.|Brak wymiarów|
|wlg_active_queries|Tak|Aktywne zapytania grup obciążeń|Liczba|Łącznie|Aktywne zapytania w grupie obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Tak|Limity czasu zapytań grupy obciążeń|Liczba|Łącznie|Zapytania, dla których uchybnił czas dla grupy obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Tak|Alokacja grupy obciążeń według procentu systemu|Procent|Maksimum|Przydzielony procent zasobów względem całego systemu na grupę obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Tak|Alokacja grupy obciążeń według procentowego limitu zasobów|Procent|Maksimum|Przydzielony procent zasobów względem określonych zasobów limitu na grupę obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Tak|Procent zasobów efektywnego limitu|Procent|Maksimum|Twardy limit procentu zasobów dozwolonych dla grupy obciążeń, z uwzględnieniem minimalnej efektywnej wartości procentowej zasobów przydzielonych dla innych grup obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Tak|Procent efektywnego minimalnego zasobu|Procent|Maksimum|Minimalna wartość procentowa zasobów zarezerwowanych i odizolowanych dla grupy obciążeń z uwzględnieniem minimalnego poziomu usług. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Tak|Zapytania w kolejce grup obciążeń|Liczba|Łącznie|Zapytania w kolejce w grupie obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|workers_percent|Tak|Procent pracowników|Procent|Średnia|Procent pracowników. Nie dotyczy magazynów danych.|Brak wymiarów|
|xtp_storage_percent|Tak|In-Memory magazynu OLTP|Procent|Średnia|In-Memory procent magazynu OLTP. Nie dotyczy magazynów danych.|Brak wymiarów|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|allocated_data_storage|Tak|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielone miejsce na dane|Brak wymiarów|
|allocated_data_storage_percent|Tak|Procent przydzielonego miejsca na dane|Procent|Maksimum|Procent przydzielonego miejsca na dane|Brak wymiarów|
|cpu_limit|Tak|Limit procesora CPU|Liczba|Średnia|Limit procesora CPU. Dotyczy elastycznych pul opartych na rdzeniu wirtualnych.|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|cpu_used|Tak|Używane procesory CPU|Liczba|Średnia|Używane procesory CPU. Dotyczy elastycznych pul opartych na rdzeniu wirtualnych.|Brak wymiarów|
|database_allocated_data_storage|Nie|Przydzielone miejsce na dane|Bajty|Średnia|Przydzielone miejsce na dane|DatabaseResourceId|
|database_cpu_limit|Nie|Limit procesora CPU|Liczba|Średnia|Limit procesora CPU|DatabaseResourceId|
|database_cpu_percent|Nie|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|DatabaseResourceId|
|database_cpu_used|Nie|Używane procesory CPU|Liczba|Średnia|Używane procesory CPU|DatabaseResourceId|
|database_dtu_consumption_percent|Nie|Procent użycia jednostek DTU|Procent|Średnia|Procent użycia jednostek DTU|DatabaseResourceId|
|database_eDTU_used|Nie|Użyty eDTU|Liczba|Średnia|Użyty eDTU|DatabaseResourceId|
|database_log_write_percent|Nie|Procent we/wy dziennika|Procent|Średnia|Procent we/wy dziennika|DatabaseResourceId|
|database_physical_data_read_percent|Nie|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|DatabaseResourceId|
|database_sessions_percent|Nie|Procent sesji|Procent|Średnia|Procent sesji|DatabaseResourceId|
|database_storage_used|Nie|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|DatabaseResourceId|
|database_workers_percent|Nie|Procent pracowników|Procent|Średnia|Procent pracowników|DatabaseResourceId|
|dtu_consumption_percent|Tak|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak wymiarów|
|eDTU_limit|Tak|Limit liczby eDTU|Liczba|Średnia|Limit liczby eDTU. Dotyczy elastycznych pul opartych na jednostkach DTU.|Brak wymiarów|
|eDTU_used|Tak|Użyty eDTU|Liczba|Średnia|Używana jest eDTU. Dotyczy elastycznych pul opartych na jednostkach DTU.|Brak wymiarów|
|log_write_percent|Tak|Procent we/wy dziennika|Procent|Średnia|Procent we/wy dziennika|Brak wymiarów|
|physical_data_read_percent|Tak|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|Brak wymiarów|
|sessions_percent|Tak|Procent sesji|Procent|Średnia|Procent sesji|Brak wymiarów|
|sqlserver_process_core_percent|Tak|SQL Server procent rdzeni procesu|Procent|Maksimum|Użycie procesora jako procentu procesu usługi SQL DB. Dotyczy elastycznych pul.|Brak wymiarów|
|sqlserver_process_memory_percent|Tak|SQL Server procent pamięci procesu|Procent|Maksimum|Użycie pamięci jako wartość procentowa procesu usługi SQL DB. Dotyczy pul elastycznych.|Brak wymiarów|
|storage_limit|Tak|Maksymalny rozmiar danych|Bajty|Średnia|Maksymalny rozmiar danych|Brak wymiarów|
|storage_percent|Tak|Procent użytej przestrzeni danych|Procent|Średnia|Procent użytej przestrzeni danych|Brak wymiarów|
|storage_used|Tak|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|Brak wymiarów|
|tempdb_data_size|Tak|Rozmiar pliku danych bazy danych Tempdb (kilobajty)|Liczba|Maksimum|Miejsce używane w plikach danych tempdb w kilobajtach.|Brak wymiarów|
|tempdb_log_size|Tak|Rozmiar pliku dziennika bazy danych Tempdb (kilobajty)|Liczba|Maksimum|Miejsce używane w pliku dziennika transakcji bazy danych tempdb w kilobajtach.|Brak wymiarów|
|tempdb_log_used_percent|Tak|Procent używanego dziennika bazy danych tempdb|Procent|Maksimum|Procent użytego miejsca w pliku dziennika transakcji bazy danych tempdb|Brak wymiarów|
|workers_percent|Tak|Procent pracowników|Procent|Średnia|Procent pracowników|Brak wymiarów|
|xtp_storage_percent|Tak|In-Memory magazynu OLTP|Procent|Średnia|In-Memory magazynu OLTP|Brak wymiarów|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje ruch wychodzący do klienta zewnętrznego z usługi Azure Storage, a także ruch wychodzący na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie, w milisekundach, opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Tak|Używana pojemność|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont usługi Premium Storage i kont usługi Blob Storage jest ona taka sama jak BlobCapacity lub FileCapacity.|Brak wymiarów|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|BlobCapacity|Nie|Pojemność obiektu blob|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez konto magazynu Blob service w bajtach.|BlobType, Tier|
|BlobCount|Nie|Liczba obiektów blob|Liczba|Średnia|Liczba obiektów blob przechowywanych na koncie magazynu.|BlobType, Tier|
|BlobProvisionedSize|Nie|Aprowizowany rozmiar obiektu blob|Bajty|Średnia|Ilość magazynu zaaprowizowana w magazynie konta magazynu w Blob service w bajtach.|BlobType, Tier|
|ContainerCount|Tak|Liczba kontenerów obiektów blob|Liczba|Średnia|Liczba kontenerów na koncie magazynu.|Brak wymiarów|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje ruch wychodzący do klienta zewnętrznego z usługi Azure Storage, a także ruch wychodzący na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|IndexCapacity|Nie|Pojemność indeksu|Bajty|Średnia|Ilość miejsca używanego przez Azure Data Lake Storage Gen2 indeks hierarchiczny.|Brak wymiarów|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie, w milisekundach, opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication, FileShare|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje ruch wychodzący do klienta zewnętrznego z usługi Azure Storage, a także ruch wychodzący na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|Nie|Pojemność pliku|Bajty|Średnia|Ilość miejsca w magazynie plików używanego przez konto magazynu.|Udział plików|
|FileCount|Nie|Liczba plików|Liczba|Średnia|Liczba plików na koncie magazynu.|Udział plików|
|FileShareCapacityQuota|Nie|Limit przydziału pojemności udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udział plików|
|FileShareCount|Nie|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików na koncie magazynu.|Brak wymiarów|
|Udział plikówProvisionediOPS|Nie|Aprowizowana we/wy udziału plików|Bajty|Średnia|Podstawowa liczba aprowowanych liczby we/wy na koncie magazynu plików w chmurze w chmurze Premium. Ta liczba jest obliczana na podstawie aprowizowanych rozmiarów (limitów przydziału) pojemności udziału.|Udział plików|
|FileShareSnapshotCount|Nie|Liczba migawek udziałów plików|Liczba|Średnia|Liczba migawek w udziałach w usłudze Plików konta magazynu.|Udział plików|
|FileShareSnapshotSize|Nie|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udział plików|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication, FileShare|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie, w milisekundach, opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication, FileShare|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje ruch wychodzący do klienta zewnętrznego z usługi Azure Storage, a także ruch wychodzący na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Tak|Pojemność kolejki|Bajty|Średnia|Ilość magazynu kolejek używanego przez konto magazynu.|Brak wymiarów|
|QueueCount|Tak|Liczba kolejek|Liczba|Średnia|Liczba kolejek na koncie magazynu.|Brak wymiarów|
|QueueMessageCount|Tak|Liczba komunikatów w kolejce|Liczba|Średnia|Liczba niewydajnych komunikatów w kolejce na koncie magazynu.|Brak wymiarów|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie, w milisekundach, opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności dla usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje ruch wychodzący do klienta zewnętrznego z usługi Azure Storage, a także ruch wychodzący na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych przychodzących w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|Milisekund|Średnia|Średnie, w milisekundach, opóźnienie na poziomie end-to-end dla żądań pomyślnych wykonanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Tak|Opóźnienie serwera powodzenia|Milisekund|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|GeoType, ApiName, Authentication|
|TableCapacity|Tak|Pojemność tabeli|Bajty|Średnia|Ilość miejsca w magazynie tabel używanego przez konto magazynu.|Brak wymiarów|
|TableCount|Tak|Liczba tabel|Liczba|Średnia|Liczba tabel na koncie magazynu.|Brak wymiarów|
|TableEntityCount|Tak|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli na koncie magazynu.|Brak wymiarów|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru ResponseType jako liczby różnych typów odpowiedzi.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ClientIOPS|Tak|Łączna liczba iOPS klienta|Liczba|Średnia|Szybkość operacji na plikach klienta przetwarzanych przez pamięć podręczną.|Brak wymiarów|
|ClientLatency|Tak|Średnie opóźnienie klienta|Milisekund|Średnia|Średnie opóźnienie operacji na plikach klienta w pamięci podręcznej.|Brak wymiarów|
|ClientLockiOPS|Tak|Client Lock IOPS|CountPerSecond|Średnia|Operacje blokowania plików klienta na sekundę.|Brak wymiarów|
|ClientMetadataReadIOPS|Tak|Client Metadata Read IOPS|CountPerSecond|Średnia|Szybkość operacji na plikach klienta wysyłanych do pamięci podręcznej, z wyłączeniem odczytów danych, które nie modyfikują stanu trwałego.|Brak wymiarów|
|ClientMetadataWriteIOPS|Tak|Client Metadata Write IOPS|CountPerSecond|Średnia|Szybkość operacji na plikach klienta wysyłanych do pamięci podręcznej, z wyłączeniem operacji zapisu danych, które modyfikują stan trwały.|Brak wymiarów|
|ClientReadiOPS|Tak|Client Read IOPS|CountPerSecond|Średnia|Operacje odczytu klienta na sekundę.|Brak wymiarów|
|ClientReadThroughput|Tak|Średnia przepływność odczytu pamięci podręcznej|BytesPerSecond|Średnia|Szybkość transferu danych odczytu klienta.|Brak wymiarów|
|ClientWriteiOPS|Tak|Client Write IOPS|CountPerSecond|Średnia|Operacje zapisu klienta na sekundę.|Brak wymiarów|
|ClientWriteThroughput|Tak|Średnia przepływność zapisu w pamięci podręcznej|BytesPerSecond|Średnia|Szybkość transferu danych zapisu klienta.|Brak wymiarów|
|StorageTargetAsyncWriteThroughput|Tak|Asynchroniczna przepływność zapisu storageTarget|BytesPerSecond|Średnia|Szybkość asynchronicznego zapisu danych w pamięci podręcznej do określonego celu magazynu. Są to opportunistyczne zapisu, które nie powodują blokowania klientów.|StorageTarget|
|StorageTargetFillThroughput|Tak|StorageTarget Fill Throughput|BytesPerSecond|Średnia|Szybkość, z jaka pamięć podręczna odczytuje dane z dysku StorageTarget w celu obsługi chybień pamięci podręcznej.|StorageTarget|
|StorageTargetHealth|Tak|Kondycja docelowa magazynu|Liczba|Średnia|Wyniki logiczne testu łączności między pamięcią podręczną i celami magazynu.|Brak wymiarów|
|StorageTargetiOPS|Tak|Łączna liczba pamięci masowejPozyskane we/wy na dysku|Liczba|Średnia|Szybkość wszystkich operacji na plikach, które pamięć podręczna wysyła do określonego celu magazynu.|StorageTarget|
|StorageTargetLatency|Tak|Opóźnienie celu magazynu|Milisekund|Średnia|Średnie opóźnienie rundy dla wszystkich operacji na plikach, które pamięć podręczna wysyła do częściowego dysku StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Tak|StorageTarget Metadata Read IOPS|CountPerSecond|Średnia|Szybkość operacji na plikach, które nie modyfikują stanu trwałego i z wyłączeniem operacji odczytu wysyłanej przez pamięć podręczną do określonego celu storageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Tak|StorageTarget Metadata Write IOPS|CountPerSecond|Średnia|Szybkość operacji na plikach, które modyfikują stan trwały i wykluczają operację zapisu wysyłaną przez pamięć podręczną do określonego celu StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Tak|Przepływność odczytu z wyprzedzeniem dla dysku StorageTarget|BytesPerSecond|Średnia|Szybkość, z jaka pamięć podręczna odczytuje dane z celu StorageTarget.|StorageTarget|
|StorageTargetReadiOPS|Tak|StorageTarget Read IOPS|CountPerSecond|Średnia|Szybkość operacji odczytu plików, które pamięć podręczna wysyła do określonego celu magazynu.|StorageTarget|
|StorageTargetSyncWriteThroughput|Tak|Przepływność zapisu synchronicznego storageTarget|BytesPerSecond|Średnia|Szybkość, z jaka pamięć podręczna synchronicznie zapisuje dane do określonego celu magazynu. Są to zapis, które powodują zablokowanie klientów.|StorageTarget|
|StorageTargetTotalReadThroughput|Tak|StorageTarget Total Read Throughput|BytesPerSecond|Średnia|Łączna szybkość odczytywania danych z określonego celu magazynu przez pamięć podręczną.|StorageTarget|
|StorageTargetTotalWriteThroughput|Tak|StorageTarget Total Write Throughput|BajtyPerSecond|Średnia|Łączna szybkość zapisu danych w pamięci podręcznej w określonym celu storageTarget.|StorageTarget|
|StorageTargetWriteiOPS|Tak|StorageTarget Write IOPS|Liczba|Średnia|Szybkość operacji zapisu plików, które pamięć podręczna wysyła do określonego celu storageTarget.|StorageTarget|
|Czas pracy|Tak|Czas pracy|Liczba|Średnia|Wyniki logiczne testu łączności między pamięcią podręczną i systemem monitorowania.|Brak wymiarów|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Tak|Wynik sesji synchronizacji|Liczba|Średnia|Metryka, która rejestruje wartość 1 za każdym razem, gdy punkt końcowy serwera pomyślnie ukończy sesję synchronizacji z punktem końcowym w chmurze|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Tak|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku przetransferowany dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecallComputedSuccessRate|Tak|Wskaźnik powodzenia wycofywania dla warstw w chmurze|Procent|Średnia|Procent wszystkich odwoływek, które zostały pomyślnie|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Tak|Rozmiar odwołań warstw w chmurze według aplikacji|Bajty|Łącznie|Rozmiar danych przywołynych przez aplikację|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Tak|Rozmiar wycofywania warstw w chmurze|Bajty|Łącznie|Rozmiar odwołanych danych|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Tak|Odwoływanie się do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych przywołynych przez serwer|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Tak|Przepływność odwoływania do warstwy w chmurze|BajtyPerSecond|Średnia|Rozmiar przepływności odwoływania danych|SyncGroupName, ServerName|
|StorageSyncServerServerServertbeat|Tak|Stan online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy ponownie zmigrowany serwer pomyślnie rejestruje puls w punkcie końcowym chmury|ServerName|
|StorageSyncSessionAppliedFilesCount|Tak|Zsynchronizowane pliki|Liczba|Łącznie|Liczba zsynchronizowanych plików|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSessionPerItemErrorsCount|Tak|Pliki nie są synchronizowane|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Server Wyliczy|Tak|Stan online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy ponownie zmigrowany serwer pomyślnie rejestruje puls w punkcie końcowym chmury|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Tak|Odwoływanie się do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych przywołynych przez serwer|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Tak|Bajty zsynchronizowane|Bajty|Łącznie|Łączny rozmiar pliku przetransferowany dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Tak|Zsynchronizowane pliki|Liczba|Łącznie|Liczba zsynchronizowanych plików|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Tak|Pliki nie są synchronizowane|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Tak|Bajty zsynchronizowane|Bajty|Łącznie|Łączny rozmiar pliku przetransferowanych dla sesji synchronizacji|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Tak|Zsynchronizowane pliki|Liczba|Łącznie|Liczba zsynchronizowanych plików|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Tak|Pliki nie są synchronizowane|Liczba|Łącznie|Nie można zsynchronizować liczby plików|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Tak|Żądania funkcji, które zakończyły się niepowodzeniem|Liczba|Łącznie|Żądania funkcji, które zakończyły się niepowodzeniem|LogicalName, PartitionId|
|AMLCalloutInputEvents|Tak|Zdarzenia funkcji|Liczba|Łącznie|Zdarzenia funkcji|LogicalName, PartitionId|
|AMLCalloutRequests|Tak|Żądania funkcji|Liczba|Łącznie|Żądania funkcji|LogicalName, PartitionId|
|ConversionErrors|Tak|Błędy konwersji danych|Liczba|Łącznie|Błędy konwersji danych|LogicalName, PartitionId|
|DeserializationError|Tak|Błędy deserializacji danych wejściowych|Liczba|Łącznie|Błędy deserializacji danych wejściowych|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Tak|Zdarzenia poza kolejnością|Liczba|Łącznie|Zdarzenia poza kolejnością|LogicalName, PartitionId|
|EarlyInputEvents|Tak|Wczesne zdarzenia wejściowe|Liczba|Łącznie|Wczesne zdarzenia wejściowe|LogicalName, PartitionId|
|błędy|Tak|Błędy środowiska uruchomieniowego|Liczba|Łącznie|Błędy środowiska uruchomieniowego|LogicalName, PartitionId|
|InputEventBytes|Tak|Bajty zdarzeń wejściowych|Bajty|Łącznie|Bajty zdarzeń wejściowych|LogicalName, PartitionId|
|InputEvents|Tak|Zdarzenia wejściowe|Liczba|Łącznie|Zdarzenia wejściowe|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Tak|Backlogged Input Events|Liczba|Maksimum|Backlogged Input Events|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Tak|Odebrane źródła danych wejściowych|Liczba|Łącznie|Odebrane źródła danych wejściowych|LogicalName, PartitionId|
|LateInputEvents|Tak|Późne zdarzenia wejściowe|Liczba|Łącznie|Późne zdarzenia wejściowe|LogicalName, PartitionId|
|OutputEvents|Tak|Zdarzenia wyjściowe|Liczba|Łącznie|Zdarzenia wyjściowe|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Tak|Opóźnienie limitu|Sekundy|Maksimum|Opóźnienie limitu|LogicalName, PartitionId|
|ProcessCPUUsagePercentage|Tak|Procent wykorzystania procesora CPU (wersja zapoznawcza)|Procent|Maksimum|Procent wykorzystania procesora CPU (wersja zapoznawcza)|LogicalName, PartitionId|
|Wykorzystanie zasobów|Tak|Procent wykorzystania SU|Procent|Maksimum|Procent wykorzystania SU|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces
|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|Nie|Przetworzone dane (w bajtach)|Bajty|Łącznie|Ilość danych przetwarzanych przez zapytania|Brak wymiarów|
|BuiltinSqlPoolLoginAttempts|Nie|Próby logowania|Liczba|Łącznie|Liczba prób logowania, które zakończyły się niepowodzeniem lub zakończyły się niepowodzeniem|Wynik|
|BuiltinSqlPoolRequestsEnded|Nie|Żądania zakończone|Liczba|Łącznie|Liczba żądań, które zakończyły się powodzeniem, niepowodzeniem lub zostały anulowane|Wynik|
|IntegrationActivityRunsEnded|Nie|Zakończone przebiegi działania|Liczba|Łącznie|Liczba działań integracji, które zakończyły się powodzeniem, niepowodzeniem lub zostały anulowane|Result, FailureType, Activity, ActivityType, Pipeline|
|IntegrationPipelineRunsEnded|Nie|Uruchomienia potoków zakończyły się|Liczba|Łącznie|Liczba uruchomień potoku integracji, które zakończyły się powodzeniem, niepowodzeniem lub zostały anulowane|Result, FailureType, Pipeline|
|IntegrationTriggerRunsEnded|Nie|Przebiegi wyzwalacza zakończone|Liczba|Łącznie|Liczba wyzwalaczy integracji, które zakończyły się powodzeniem, niepowodzeniem lub zostały anulowane|Result, FailureType, Trigger|
|SQLStreamingBackloggedInputEventSources|Nie|Zdarzenia wejściowe ze zdjętą flagą backlogged (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba źródeł zdarzeń wejściowych, które są ponownie wlodowane.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingConversionErrors|Nie|Błędy konwersji danych (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba zdarzeń wyjściowych, których nie można przekonwertować na oczekiwany schemat wyjściowy. Zasady błędów można zmienić na "Upuść", aby usunąć zdarzenia, które napotkają ten scenariusz.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingDeserializationError|Nie|Błędy deserializacji danych wejściowych (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba zdarzeń wejściowych, których nie można deserializacji.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingEarlyInputEvents|Nie|Wczesne zdarzenia wejściowe (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba zdarzeń wejściowych, które według zasad wczesnego przylotu są uznawane za wczesne w porównaniu z czasem przybycia.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventBytes|Nie|Bajty zdarzeń wejściowych (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Ilość danych odebranych przez zadanie przesyłania strumieniowego w bajtach. Może to służyć do sprawdzania, czy zdarzenia są wysyłane do źródła danych wejściowych.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEvents|Nie|Zdarzenia wejściowe (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba zdarzeń wejściowych.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventsSourcesPerSecond|Nie|Odebrane źródła danych wejściowych (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba źródeł zdarzeń wejściowych na sekundę.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingLateInputEvents|Nie|Późne zdarzenia wejściowe (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba zdarzeń wejściowych, dla których czas aplikacji jest uznawany za opóźniony w porównaniu z czasem przylotu zgodnie z zasadami późnego przylotu.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutOfOrderEvents|Nie|Zdarzenia poza kolejnością (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba zdarzeń centrum zdarzeń (serializowanych komunikatów) odebranych przez kartę wejściową centrum zdarzeń, odebranych poza kolejnością, które zostały porzucone lub mają skorygowany znacznik czasu na podstawie zasad porządkowania zdarzeń.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputEvents|Nie|Zdarzenia wyjściowe (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Liczba zdarzeń wyjściowych.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputWatermarkDelaySeconds|Nie|Opóźnienie znaku wodnego (wersja zapoznawcza)|Liczba|Maksimum|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Opóźnienie wyjściowego limitu w sekundach.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingResourceUtilization|Nie|Procent wykorzystania zasobów (wersja zapoznawcza)|Procent|Maksimum|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia.
 Użycie zasobów wyrażone jako wartość procentowa. Wysokie wykorzystanie wskazuje, że zadanie używa wartości zbliżonej do maksymalnej przydzielonej zasobów.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingRuntimeErrors|Nie|Błędy środowiska uruchomieniowego (wersja zapoznawcza)|Liczba|Łącznie|Jest to metryka w wersji zapoznawczej dostępna w regionach Wschodnie stany USA i Europa Zachodnia. Łączna liczba błędów związanych z przetwarzaniem zapytań (z wyłączeniem błędów znalezionych podczas zjechania zdarzeń lub wyprowadzania wyników).|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|Nie|Przydzielone rdzenie wirtualne|Liczba|Maksimum|Przydzielone rdzenie wirtualne dla puli Apache Spark wirtualnej|SubmitterId|
|BigDataPoolAllocatedMemory|Nie|Przydzielona pamięć (GB)|Liczba|Maksimum|Przydzielona pamięć dla puli Apach Spark (GB)|SubmitterId|
|BigDataPoolApplicationsActive|Nie|Aktywne Apache Spark aplikacji|Liczba|Maksimum|Łączna liczba aktywnych Apache Spark aplikacji puli|JobState|
|BigDataPoolApplicationsEnded|Nie|Zakończone Apache Spark aplikacji|Liczba|Łącznie|Liczba zakończonych Apache Spark puli|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Zapytania aktywne|Nie|Aktywne zapytania|Liczba|Łącznie|Aktywne zapytania. Użycie tej metryki niefiltrowanych i nieudlitowanych powoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie|IsUserDefined|
|AdaptiveCacheHitPercent|Nie|Procent trafień adaptacyjnej pamięci podręcznej|Procent|Maksimum|Mierzy, jak dobrze obciążenia wykorzystują adaptacyjną pamięć podręczną. Użyj tej metryki z metryką procentową trafień pamięci podręcznej, aby określić, czy skalować w celu dodatkowej pojemności, czy ponownie uruchomić obciążenia w celu zbudowania pamięci podręcznej|Brak wymiarów|
|AdaptiveCacheUsedPercent|Nie|Procent używanej adaptacyjnej pamięci podręcznej|Procent|Maksimum|Mierzy, jak dobrze obciążenia wykorzystują adaptacyjną pamięć podręczną. Użyj tej metryki z metryką procentową użycia pamięci podręcznej, aby określić, czy skalować w celu dodatkowej pojemności, czy ponownie uruchomić obciążenia w celu zbudowania pamięci podręcznej|Brak wymiarów|
|Połączenia|Tak|Połączenia|Liczba|Łącznie|Liczba logowań łącznie do puli SQL|Wynik|
|ConnectionsBlockedByFirewall|Nie|Połączenia zablokowane przez zaporę|Liczba|Łącznie|Liczba połączeń zablokowanych przez reguły zapory. Ponownie omów zasady kontroli dostępu dla puli SQL i monitoruj te połączenia, jeśli liczba jest wysoka|Brak wymiarów|
|CPUPercent (CpuPercent)|Nie|Procent użycia procesora CPU|Procent|Maksimum|Wykorzystanie procesora CPU we wszystkich węzłach w puli SQL|Brak wymiarów|
|Dwulimit|Nie|Limit jednostek DWU|Liczba|Maksimum|Cel poziomu usługi puli SQL|Brak wymiarów|
|Dwuuzysłowe|Nie|Używane jednostek DWU|Liczba|Maksimum|Reprezentuje odzwierciedlenie wysokiego poziomu użycia w puli SQL. Mierzone według limitu jednostek DWU * Procent jednostek DWU|Brak wymiarów|
|DWUUsedPercent|Nie|Procent użytych jednostek DWU|Procent|Maksimum|Reprezentuje odzwierciedlenie wysokiego poziomu użycia w puli SQL. Mierzone przez pomiar maksymalnego odsetka użycia procesora CPU do wartości procentowej liczby we/wy danych|Brak wymiarów|
|LocalTempDBUsedPercent|Nie|Procent użytej lokalnej bazy danych tempdb|Procent|Maksimum|Wykorzystanie lokalnej bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut|Brak wymiarów|
|MemoryUsedPercent|Nie|Procent użycia pamięci|Procent|Maksimum|Wykorzystanie pamięci we wszystkich węzłach w puli SQL|Brak wymiarów|
|QueuedQueries|Nie|Zapytania w kolejce|Liczba|Łącznie|Skumulowana liczba żądań w kolejce po osiągnięciu maksymalnego limitu współbieżności|IsUserDefined|
|WLGActiveQueries|Nie|Aktywne zapytania grupy obciążeń|Liczba|Łącznie|Aktywne zapytania w grupie obciążeń. Użycie tej metryki niefiltrowanych i nieudlitowanych powoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Nie|Limity czasu zapytań grupy obciążeń|Liczba|Łącznie|Zapytania dotyczące grupy obciążeń, dla których uchybno czas. Limity czasu zapytania zgłoszone przez tę metrykę są zgłaszane dopiero po zakończeniu wykonywania zapytania (nie obejmują czasu oczekiwania z powodu blokowania lub oczekiwania na zasoby)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|Nie|Alokacja grupy obciążeń według maksymalnego procentu zasobów|Procent|Maksimum|Przedstawia procentową alokację zasobów względem efektywnego procentu zasobów limitu na grupę obciążeń. Ta metryka zapewnia efektywne wykorzystanie grupy obciążeń|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Nie|Alokacja grup obciążeń według procentu systemu|Procent|Maksimum|Procentowa alokacja zasobów względem całego systemu|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Nie|Procent zasobów efektywnego limitu|Procent|Maksimum|Procent efektywnego zasobu limitu dla grupy obciążeń. Jeśli istnieją inne grupy obciążeń z wartością min_percentage_resource > 0, effective_cap_percentage_resource jest obniżana proporcjonalnie|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|Nie|Procent efektywnego minimalnego zasobu|Procent|Maksimum|Efektywne ustawienie minimalnej wartości procentowej zasobów dozwolone z uwzględnieniem poziomu usług i ustawień grupy obciążeń. Efektywną min_percentage_resource można dostosować na niższych poziomach usług|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Nie|Zapytania w kolejce grup obciążeń|Liczba|Łącznie|Skumulowana liczba żądań w kolejce po osiągnięciu maksymalnego limitu współbieżności|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Tak|Odebrane bajty danych przychodzących|Bajty|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Brak wymiarów|
|IngressReceivedInvalidMessages|Tak|Ruch przychodzący odebrał nieprawidłowe komunikaty|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze wszystkich źródeł zdarzeń centrum zdarzeń lub centrum IoT|Brak wymiarów|
|IngressReceivedMessages|Tak|Odebrane komunikaty przychodzące|Liczba|Łącznie|Liczba komunikatów odczytanych ze wszystkich źródeł zdarzeń centrum zdarzeń lub centrum IoT|Brak wymiarów|
|IngressReceivedMessagesCountLag|Tak|Liczba odebranych komunikatów przychodzących : Opóźnienie|Liczba|Średnia|Różnica między numerem sekwencji ostatniego komunikatu w sekwencji w sekwencji w partycji źródła zdarzeń a liczbą komunikatów sekwencji przetwarzanych w danych przychodzących|Brak wymiarów|
|IngressReceivedMessagesTimeLag|Tak|Opóźnienie czasu odebranych komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, przez który komunikat jest kolejkowany w źródle zdarzeń, a czasem przetwarzania w danych przychodzących|Brak wymiarów|
|IngressStoredBytes|Tak|Ruch przychodzący przechowywany w bajtach|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressStoredEvents|Tak|Zdarzenia przechowywane dla danych przychodzących|Liczba|Łącznie|Liczba spłaszczonych zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|WarmStorageMaxProperties|Tak|Maksymalne właściwości magazynu gorącego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla SKU S1/S2 i maksymalna liczba właściwości dozwolonych przez magazyn ciepły dla SKU Z płatnością godziną|Brak wymiarów|
|WarmStorageUsedProperties|Tak|Używane właściwości magazynu gorącego |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla SKU S1/S2 i liczba właściwości używanych przez magazyn ciepły dla SKU płatności według płatności|Brak wymiarów|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Tak|Odebrane bajty danych przychodzących|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzeń|Brak wymiarów|
|IngressReceivedInvalidMessages|Tak|Ruch przychodzący odebrał nieprawidłowe komunikaty|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze źródła zdarzeń|Brak wymiarów|
|IngressReceivedMessages|Tak|Odebrane komunikaty przychodzące|Liczba|Łącznie|Liczba komunikatów odczytanych ze źródła zdarzeń|Brak wymiarów|
|IngressReceivedMessagesCountLag|Tak|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencji ostatniego komunikatu w sekwencji w sekwencji w partycji źródła zdarzeń a liczbą komunikatów sekwencji przetwarzanych w danych przychodzących|Brak wymiarów|
|IngressReceivedMessagesTimeLag|Tak|Opóźnienie czasu odebranych komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, przez który komunikat jest kolejkowany w źródle zdarzeń, a czasem przetwarzania w danych przychodzących|Brak wymiarów|
|IngressStoredBytes|Tak|Ruch przychodzący przechowywany w bajtach|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressStoredEvents|Tak|Zdarzenia przechowywane dla danych przychodzących|Liczba|Łącznie|Liczba spłaszczonych zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|WarmStorageMaxProperties|Tak|Maksymalne właściwości magazynu gorącego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla SKU S1/S2 i maksymalna liczba właściwości dozwolonych przez magazyn ciepły dla SKU Z płatnością godziną|Brak wymiarów|
|WarmStorageUsedProperties|Tak|Używane właściwości magazynu gorącego |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla SKU S1/S2 i liczba właściwości używanych przez magazyn ciepły dla SKU płatności według płatności|Brak wymiarów|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami odczytu w okresie próbek.|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Średnia liczba operacji we/wy odczytu w poprzednim okresie próby. Należy pamiętać, że te operacje mogą mieć zmienny rozmiar.|Brak wymiarów|
|Bajty zapisu na dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami zapisu w okresie próbkowania.|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Średnia liczba operacji zapisu we/wy w poprzednim okresie próby. Należy pamiętać, że te operacje mogą mieć zmienny rozmiar.|Brak wymiarów|
|DiskReadBytesPerSecond|Tak|Bajty odczytu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Brak wymiarów|
|DiskReadLatency|Tak|Opóźnienie odczytu dysku|Milisekund|Średnia|Całkowite opóźnienie odczytu. Suma opóźnień odczytu urządzenia i jądra.|Brak wymiarów|
|DiskReadOperations|Tak|Operacje odczytu z dysku|Liczba|Łącznie|Liczba operacji we/wy odczytu w poprzednim przykładowym okresie. Należy pamiętać, że te operacje mogą mieć zmienny rozmiar.|Brak wymiarów|
|DiskWriteBytesPerSecond|Tak|Bajty zapisu na dysku/s|BajtyPerSecond|Średnia|Średnia przepływność dysku z powodu operacji zapisu w okresie próbkowania.|Brak wymiarów|
|DiskWriteLatency|Tak|Opóźnienie zapisu na dysku|Milisekund|Średnia|Całkowite opóźnienie zapisu. Suma opóźnień zapisu urządzenia i jądra.|Brak wymiarów|
|DiskWriteOperations|Tak|Operacje zapisu na dysku|Liczba|Łącznie|Liczba operacji zapisu we/wy w poprzednim przykładowym okresie. Należy pamiętać, że te operacje mogą mieć zmienny rozmiar.|Brak wymiarów|
|MemoryActive|Tak|Aktywna pamięć|Bajty|Średnia|Ilość pamięci używanej przez maszynę wirtualną w ostatnich małych oknach czasu. Jest to "true" liczba pamięci, która jest obecnie potrzebna maszynie wirtualnej. Ponadto nieużywana pamięć może zostać zamieniona lub zamieniona balonem bez wpływu na wydajność gościa.|Brak wymiarów|
|MemoryGranted|Tak|Udzielono pamięci|Bajty|Średnia|Ilość pamięci przyznanej maszynie wirtualnej przez hosta. Pamięć nie jest udzielana hostowi do momentu dotknięcia go raz, a pamięć może zostać zamieniona lub zamieniona balonem, jeśli vMkernel potrzebuje pamięci.|Brak wymiarów|
|MemoryUsed|Tak|Używana pamięć|Bajty|Średnia|Ilość pamięci maszyny, która jest w użyciu przez maszynę wirtualną.|Brak wymiarów|
|Sieć — wejście|Tak|Sieć — wejście|Bajty|Łącznie|Łączna przepływność sieci dla odebranego ruchu.|Brak wymiarów|
|Sieć — wyjście|Tak|Sieć — wyjście|Bajty|Łącznie|Łączna przepływność sieci dla przesyłanego ruchu.|Brak wymiarów|
|NetworkInBytesPerSecond|Tak|Sieć w bajtach/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla odebranego ruchu.|Brak wymiarów|
|NetworkOutBytesPerSecond|Tak|Bajty wychodzące sieci/s|BajtyPerSecond|Średnia|Średnia przepływność sieci dla przesyłanego ruchu.|Brak wymiarów|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Użycie procesora CPU. Ta wartość jest raportowa w 100% reprezentująca wszystkie rdzenie procesora w systemie. Na przykład dwukierunkowa maszyna wirtualna używająca 50% systemu z czterema rdzeniami całkowicie używa dwóch rdzeni.|Brak wymiarów|
|PercentageCpuReady|Tak|Procent gotowego procesora CPU|Milisekund|Łącznie|Czas gotowości to czas poświęcany na oczekiwanie, aż procesory CPU staną się dostępne w przeszłości interwału aktualizacji.|Brak wymiarów|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActiveRequests|Tak|Aktywne żądania (przestarzałe)|Liczba|Łącznie|Aktywne żądania|Wystąpienie|
|AverageResponseTime|Tak|Średni czas odpowiedzi (przestarzałe)|Sekundy|Średnia|Średni czas obsługi żądań przez frontonie w sekundach.|Wystąpienie|
|Bytesreceived|Tak|Dane w|Bajty|Łącznie|Średnia przepustowość przychodząca używana we wszystkich frontoniach w programie MiB.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Średnia przepustowość przychodząca używana we wszystkich frontonie w programie MiB.|Wystąpienie|
|CpuPercentage|Tak|Procent użycia procesora CPU|Procent|Średnia|Średnie użycie procesora CPU we wszystkich wystąpieniach frontonia.|Wystąpienie|
|DiskQueueLength|Tak|Długość kolejki dysku|Liczba|Średnia|Średnia liczba żądań odczytu i zapisu, które zostały w kolejce w magazynie. Duża długość kolejki dysku wskazuje aplikację, która może spowalniać z powodu nadmiernej ilości we/wy dysku.|Wystąpienie|
|Http101|Tak|Http 101|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 101.|Wystąpienie|
|Http2xx|Tak|Http 2xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 200, ale < 300.|Wystąpienie|
|Http3xx|Tak|Http 3xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 300, ale < 400.|Wystąpienie|
|Http401|Tak|Http 401|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 401.|Wystąpienie|
|Http403|Tak|Http 403|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 403.|Wystąpienie|
|Http404|Tak|Http 404|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 404.|Wystąpienie|
|Http406|Tak|Http 406|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 406.|Wystąpienie|
|Http4xx|Tak|Http 4xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 400, ale < 500.|Wystąpienie|
|Http5xx|Tak|Błędy serwera HTTP|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 500, ale < 600.|Wystąpienie|
|HttpQueueLength|Tak|Długość kolejki HTTP|Liczba|Średnia|Średnia liczba żądań HTTP, które przed ich zrealizowaniem musiała się znaleźć w kolejce. Wysoka lub rosnąca długość kolejki HTTP jest objawem planu pod dużym obciążeniem.|Wystąpienie|
|HttpResponseTime|Tak|Czas odpowiedzi|Sekundy|Średnia|Czas obsługi żądań przez frontonie w sekundach.|Wystąpienie|
|LargeAppServicePlanInstances|Tak|Duże App Service planu rozwoju|Liczba|Średnia|Duże App Service planu rozwoju|Brak wymiarów|
|MediumAppServicePlanInstances|Tak|Pracownicy plan App Service średniej|Liczba|Średnia|Pracownicy plan App Service średniej|Brak wymiarów|
|PamięćPercentage|Tak|Procent pamięci|Procent|Średnia|Średnia pamięć używana we wszystkich wystąpieniach frontonia.|Wystąpienie|
|Żądania|Tak|Żądania|Liczba|Łącznie|Całkowita liczba żądań niezależnie od ich wynikowego kodu stanu HTTP.|Wystąpienie|
|SmallAppServicePlanInstances|Tak|Małe App Service pracowników planu rozwoju|Liczba|Średnia|Małe App Service planu rozwoju|Brak wymiarów|
|TotalFrontEnds|Tak|Łączna liczba frontonów|Liczba|Średnia|Łączna liczba frontonów|Brak wymiarów|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CpuPercentage (Procesor CpuPercentage)|Tak|Procent użycia procesora CPU|Procent|Średnia|Średnie użycie procesora CPU we wszystkich wystąpieniach puli procesów roboczych.|Wystąpienie|
|MemoryPercentage (Ilość pamięci)|Tak|Procent pamięci|Procent|Średnia|Średnia pamięć używana we wszystkich wystąpieniach puli procesów roboczych.|Wystąpienie|
|WorkersAvailable|Tak|Dostępne pracownicy|Liczba|Średnia|Dostępne pracownicy|Brak wymiarów|
|WorkersTotal|Tak|Łączna liczba pracowników|Liczba|Średnia|Łączna liczba pracowników|Brak wymiarów|
|WorkersUsed|Tak|Używane pracownicy|Liczba|Średnia|Używane pracownicy|Brak wymiarów|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bytesreceived|Tak|Dane w|Bajty|Łącznie|Średnia przepustowość przychodząca używana we wszystkich wystąpieniach planu.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Średnia przepustowość wychodząca używana we wszystkich wystąpieniach planu.|Wystąpienie|
|CpuPercentage|Tak|Procent użycia procesora CPU|Procent|Średnia|Średnie użycie procesora CPU we wszystkich wystąpieniach planu.|Wystąpienie|
|DiskQueueLength|Tak|Długość kolejki dysku|Liczba|Średnia|Średnia liczba żądań odczytu i zapisu, które zostały w kolejce w magazynie. Duża długość kolejki dysku wskazuje aplikację, która może spowalniać z powodu nadmiernej ilości we/wy dysku.|Wystąpienie|
|HttpQueueLength|Tak|Długość kolejki HTTP|Liczba|Średnia|Średnia liczba żądań HTTP, które trzeba było znaleźć w kolejce przed ich zrealizowaniem. Wysoka lub rosnąca długość kolejki HTTP to objaw intensywnego obciążenia planu.|Wystąpienie|
|MemoryPercentage (Ilość pamięci)|Tak|Procent pamięci|Procent|Średnia|Średnia pamięć używana we wszystkich wystąpieniach planu.|Wystąpienie|
|SocketInboundAll|Tak|SocketInboundAll|Liczba|Średnia|SocketInboundAll|Wystąpienie|
|SocketLoopback|Tak|SocketLoopback|Liczba|Średnia|SocketLoopback|Wystąpienie|
|SocketOutboundAll|Tak|SocketOutboundAll|Liczba|Średnia|SocketOutboundAll|Wystąpienie|
|SocketOutboundEstablished|Tak|SocketOutboundEstablished|Liczba|Średnia|SocketOutboundEstablished|Wystąpienie|
|SocketOutboundTimeWait|Tak|SocketOutboundTimeWait|Liczba|Średnia|SocketOutboundTimeWait|Wystąpienie|
|TcpCloseWait|Tak|Oczekiwanie na zamknięcie protokołu TCP|Liczba|Średnia|Oczekiwanie na zamknięcie protokołu TCP|Wystąpienie|
|TcpClosing|Tak|Zamykanie protokołu TCP|Liczba|Średnia|Zamykanie protokołu TCP|Wystąpienie|
|TcpEstablished|Tak|Ustanowiono protokół TCP|Liczba|Średnia|Ustanowiono protokół TCP|Wystąpienie|
|TcpFinWait1|Tak|Tcp Fin Wait 1|Liczba|Średnia|Tcp Fin Wait 1|Wystąpienie|
|TcpFinWait2|Tak|Tcp Fin Wait 2|Liczba|Średnia|Tcp Fin Wait 2|Wystąpienie|
|TcpLastAck|Tak|TCP Last Ack|Liczba|Średnia|Ostatniack TCP|Wystąpienie|
|TcpSynReceived|Tak|Odebrano syn PROTOKOŁU TCP|Liczba|Średnia|Odebrano syn PROTOKOŁU TCP|Wystąpienie|
|TcpSynSent|Tak|Wysłane połączenie TCP Syn|Liczba|Średnia|Wysłane połączenie TCP Syn|Wystąpienie|
|TcpTimeWait|Tak|Oczekiwanie na czas protokołu TCP|Liczba|Średnia|Oczekiwanie na czas protokołu TCP|Wystąpienie|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Połączenia aplikacji|Tak|Połączenia|Liczba|Średnia|Liczba powiązanych gniazd istniejących w piaskownicy (w3wp.exe i jej procesy podrzędne). Powiązane gniazdo jest tworzone przez wywołanie interfejsów API bind()/connect() i pozostaje do momentu zamknięcia tego gniazda za pomocą funkcji CloseHandle()/closesocket().|Wystąpienie|
|AverageMemoryWorkingSet|Tak|Średni zestaw roboczy pamięci|Bajty|Średnia|Średnia ilość pamięci używanej przez aplikację w megabajtach (MiB).|Wystąpienie|
|AverageResponseTime|Tak|Średni czas odpowiedzi (przestarzałe)|Sekundy|Średnia|Średni czas obsługi żądań przez aplikację w sekundach.|Wystąpienie|
|Bytesreceived|Tak|Dane w|Bajty|Łącznie|Przepustowość przychodząca zużywana przez aplikację w programie MiB.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Ilość przepustowości wychodzącej używanej przez aplikację w programie MiB.|Wystąpienie|
|Czas procesora CPU|Tak|Czas procesora CPU|Sekundy|Łącznie|Ilość zużywanego przez aplikację procesora CPU w sekundach. Aby uzyskać więcej informacji na temat tej metryki. Nie dotyczy Azure Functions. Zobacz https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (czas procesora CPU a procent użycia procesora CPU).|Wystąpienie|
|CurrentAssemblies|Tak|Bieżące zestawy|Liczba|Średnia|Bieżąca liczba zestawów załadowanych we wszystkich domenach aplikacji w tej aplikacji.|Wystąpienie|
|System plikówUsuń|Tak|Użycie systemu plików|Bajty|Średnia|Procent przydziału systemu plików używanego przez aplikację.|Brak wymiarów|
|FunctionExecutionCount|Tak|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji. Dotyczy tylko Azure Functions.|Wystąpienie|
|FunctionExecutionUnits|Tak|Jednostki wykonywania funkcji|Liczba|Łącznie|Jednostki wykonywania funkcji. Dotyczy tylko Azure Functions.|Wystąpienie|
|Gen0Collections|Tak|Odzyskiwanie pamięci 0. generacji|Liczba|Łącznie|Liczba odśmiecania obiektów generacji 0 od momentu rozpoczęcia procesu aplikacji. Komputery gcs wyższej generacji obejmują wszystkie niższej generacji.|Wystąpienie|
|Gen1Collections|Tak|Odzyskiwanie pamięci 1. generacji|Liczba|Łącznie|Liczba odśmiecania obiektów generacji 1 od momentu rozpoczęcia procesu aplikacji. Procesory GC większej generacji obejmują wszystkie konsole GC niższej generacji.|Wystąpienie|
|Gen2Collections|Tak|Odzyskiwanie pamięci 2. generacji|Liczba|Łącznie|Liczba odśmiecania pamięci obiektów generacji 2 od momentu rozpoczęcia procesu aplikacji.|Wystąpienie|
|Handles|Tak|Liczba dojść|Liczba|Średnia|Łączna liczba dojść aktualnie otwartych przez proces aplikacji.|Wystąpienie|
|HealthCheckStatus|Tak|Stan kontroli kondycji|Liczba|Średnia|Stan kontroli kondycji|Wystąpienie|
|Http101|Tak|Http 101|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 101.|Wystąpienie|
|Http2xx|Tak|Http 2xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 200, ale < 300.|Wystąpienie|
|Http3xx|Tak|Http 3xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 300, ale < 400.|Wystąpienie|
|Http401|Tak|Http 401|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 401.|Wystąpienie|
|Http403|Tak|Http 403|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 403.|Wystąpienie|
|Http404|Tak|Http 404|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 404.|Wystąpienie|
|Http406|Tak|Http 406|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 406.|Wystąpienie|
|Http4xx|Tak|Http 4xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 400, ale < 500.|Wystąpienie|
|Http5xx|Tak|Błędy serwera HTTP|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 500, ale < 600.|Wystąpienie|
|HttpResponseTime|Tak|Czas odpowiedzi|Sekundy|Średnia|Czas obsługi żądań przez aplikację w sekundach.|Wystąpienie|
|IoOtherBytesPerSecond|Tak|Liczba innych bajtów we/wy na sekundę|BajtyPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia bajty do operacji we/wy, które nie obejmują danych, takich jak operacje sterowania.|Wystąpienie|
|IoOtherOperationsPerSecond|Tak|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje We/Wy, które nie są operacjami odczytu ani zapisu.|Wystąpienie|
|IoReadBytesPerSecond|Tak|Liczba bajtów odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji odczytuje bajty z operacji we/wy.|Wystąpienie|
|IoReadOperationsPerSecond|Tak|Operacje we/wy odczytu na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje we/wy odczytu.|Wystąpienie|
|IoWriteBytesPerSecond|Tak|Liczba bajtów zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji zapisuje bajty do operacji we/wy.|Wystąpienie|
|IoWriteOperationsPerSecond|Tak|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje we/wy zapisu.|Wystąpienie|
|MemoryWorkingSet|Tak|Zestaw roboczy pamięci|Bajty|Średnia|Bieżąca ilość pamięci używanej przez aplikację w programie MiB.|Wystąpienie|
|PrivateBytes|Tak|Bajty prywatne|Bajty|Średnia|Bajty prywatne to bieżący rozmiar (w bajtach) pamięci przydzielonej przez proces aplikacji, która nie może być współużytkowana z innymi procesami.|Wystąpienie|
|Żądania|Tak|Żądania|Liczba|Łącznie|Całkowita liczba żądań niezależnie od ich wynikowego kodu stanu HTTP.|Wystąpienie|
|RequestsInApplicationQueue|Tak|Żądania w kolejce aplikacji|Liczba|Średnia|Liczba żądań w kolejce żądań aplikacji.|Wystąpienie|
|Wątki|Tak|Liczba wątków|Liczba|Średnia|Liczba wątków aktualnie aktywnych w procesie aplikacji.|Wystąpienie|
|TotalAppDomains|Tak|Łączna liczba domen aplikacji|Liczba|Średnia|Bieżąca liczba domen aplikacji załadowanych w tej aplikacji.|Wystąpienie|
|TotalAppDomainsUnloaded|Tak|Łączna liczba niezaładowanych domen aplikacji|Liczba|Średnia|Łączna liczba niezaładowanych domen aplikacji od momentu uruchomienia aplikacji.|Wystąpienie|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Połączenia aplikacji|Tak|Połączenia|Liczba|Średnia|Liczba powiązanych gniazd istniejących w piaskownicy (w3wp.exe i jej procesy podrzędne). Powiązane gniazdo jest tworzone przez wywołanie interfejsów API bind()/connect() i pozostaje do momentu zamknięcia tego gniazda za pomocą funkcji CloseHandle()/closesocket().|Wystąpienie|
|AverageMemoryWorkingSet|Tak|Średni zestaw roboczy pamięci|Bajty|Średnia|Średnia ilość pamięci używanej przez aplikację w megabajtach (MiB).|Wystąpienie|
|AverageResponseTime|Tak|Średni czas odpowiedzi (przestarzałe)|Sekundy|Średnia|Średni czas obsługi żądań przez aplikację w sekundach.|Wystąpienie|
|Bytesreceived|Tak|Dane w|Bajty|Łącznie|Przepustowość przychodząca zużywana przez aplikację w programie MiB.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Ilość przepustowości wychodzącej używanej przez aplikację w programie MiB.|Wystąpienie|
|Czas procesora CPU|Tak|Czas procesora CPU|Sekundy|Łącznie|Ilość zużywanego przez aplikację procesora CPU w sekundach. Aby uzyskać więcej informacji na temat tej metryki. Nie dotyczy Azure Functions. Zobacz https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (czas procesora CPU a procent użycia procesora CPU).|Wystąpienie|
|CurrentAssemblies|Tak|Bieżące zestawy|Liczba|Średnia|Bieżąca liczba zestawów załadowanych we wszystkich domenach aplikacji w tej aplikacji.|Wystąpienie|
|System plikówUsage|Tak|Użycie systemu plików|Bajty|Średnia|Procent przydziału systemu plików używanego przez aplikację.|Brak wymiarów|
|FunctionExecutionCount|Tak|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|FunctionExecutionUnits|Tak|Jednostki wykonywania funkcji|Liczba|Łącznie|Jednostki wykonywania funkcji|Wystąpienie|
|Gen0Collections|Tak|Odzyskiwanie pamięci 0. generacji|Liczba|Łącznie|Liczba odśmiecania obiektów generacji 0 od momentu rozpoczęcia procesu aplikacji. Komputery gcs wyższej generacji obejmują wszystkie niższej generacji.|Wystąpienie|
|Gen1Collections|Tak|Odzyskiwanie pamięci 1. generacji|Liczba|Łącznie|Liczba odbierania pamięci przez obiekty generacji 1 od początku procesu aplikacji. Procesory GC większej generacji obejmują wszystkie niższej generacji.|Wystąpienie|
|Gen2Collections|Tak|Odzyskiwanie pamięci 2. generacji|Liczba|Łącznie|Liczba odśmiecania pamięci obiektów generacji 2 od momentu rozpoczęcia procesu aplikacji.|Wystąpienie|
|Handles|Tak|Liczba dojść|Liczba|Średnia|Łączna liczba dojść aktualnie otwartych przez proces aplikacji.|Wystąpienie|
|HealthCheckStatus|Tak|Stan kontroli kondycji|Liczba|Średnia|Stan kontroli kondycji|Wystąpienie|
|Http101|Tak|Http 101|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 101.|Wystąpienie|
|Http2xx|Tak|Http 2xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 200, ale < 300.|Wystąpienie|
|Http3xx|Tak|Http 3xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 300, ale < 400.|Wystąpienie|
|Http401|Tak|Http 401|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 401.|Wystąpienie|
|Http403|Tak|Http 403|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 403.|Wystąpienie|
|Http404|Tak|Http 404|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 404.|Wystąpienie|
|Http406|Tak|Http 406|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP 406.|Wystąpienie|
|Http4xx|Tak|Http 4xx|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 400, ale < 500.|Wystąpienie|
|Http5xx|Tak|Błędy serwera HTTP|Liczba|Łącznie|Liczba żądań, których wynikiem jest kod stanu HTTP = 500, ale < 600.|Wystąpienie|
|HttpResponseTime|Tak|Czas odpowiedzi|Sekundy|Średnia|Czas obsługi żądań przez aplikację w sekundach.|Wystąpienie|
|IoOtherBytesPerSecond|Tak|Liczba innych bajtów we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia bajty do operacji we/wy, które nie obejmują danych, takich jak operacje sterowania.|Wystąpienie|
|IoOtherOperationsPerSecond|Tak|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje We/Wy, które nie są operacjami odczytu ani zapisu.|Wystąpienie|
|IoReadBytesPerSecond|Tak|Liczba bajtów odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji odczytuje bajty z operacji we/wy.|Wystąpienie|
|IoReadOperationsPerSecond|Tak|Operacje we/wy odczytu na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje we/wy odczytu.|Wystąpienie|
|IoWriteBytesPerSecond|Tak|Liczba bajtów zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji zapisuje bajty do operacji we/wy.|Wystąpienie|
|IoWriteOperationsPerSecond|Tak|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje we/wy zapisu.|Wystąpienie|
|MemoryWorkingSet|Tak|Zestaw roboczy pamięci|Bajty|Średnia|Bieżąca ilość pamięci używanej przez aplikację w programie MiB.|Wystąpienie|
|PrivateBytes|Tak|Bajty prywatne|Bajty|Średnia|Bajty prywatne to bieżący rozmiar (w bajtach) pamięci przydzielonej przez proces aplikacji, która nie może być współużytkowana z innymi procesami.|Wystąpienie|
|Żądania|Tak|Żądania|Liczba|Łącznie|Całkowita liczba żądań niezależnie od ich wynikowego kodu stanu HTTP.|Wystąpienie|
|RequestsInApplicationQueue|Tak|Żądania w kolejce aplikacji|Liczba|Średnia|Liczba żądań w kolejce żądań aplikacji.|Wystąpienie|
|Wątki|Tak|Liczba wątków|Liczba|Średnia|Liczba wątków aktualnie aktywnych w procesie aplikacji.|Wystąpienie|
|TotalAppDomains|Tak|Łączna liczba domen aplikacji|Liczba|Średnia|Bieżąca liczba domen aplikacji załadowanych w tej aplikacji.|Wystąpienie|
|TotalAppDomainsUnloaded|Tak|Łączna liczba niezaładowanych domen aplikacji|Liczba|Średnia|Łączna liczba niezaładowanych domen aplikacji od momentu uruchomienia aplikacji.|Wystąpienie|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|Metric|Można eksportować za pośrednictwem ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|BytesSent|Wystąpienie|
|FunctionErrors (Funkcje funkcji)|Tak|FunctionErrors (Funkcje funkcji)|Liczba|Łącznie|FunctionErrors (Funkcje funkcji)|Wystąpienie|
|FunctionHits|Tak|FunctionHits|Liczba|Łącznie|FunctionHits|Wystąpienie|
|SiteErrors|Tak|SiteErrors|Liczba|Łącznie|SiteErrors|Wystąpienie|
|SiteHits|Tak|SiteHits|Liczba|Łącznie|SiteHits|Wystąpienie|

## <a name="next-steps"></a>Następne kroki

- [Przeczytaj o metrykach w Azure Monitor](../data-platform.md)
- [Tworzenie alertów dotyczących metryk](../alerts/alerts-overview.md)
- [Eksportowanie metryk do magazynu, centrum zdarzeń lub usługi Log Analytics](../essentials/platform-logs-overview.md)
