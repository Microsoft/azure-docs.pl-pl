---
title: Azure Monitor obsługiwane metryki według typu zasobu
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 12/09/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 04f6cdae8a7601f94251516cf5c3c1fab07994a6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929110"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki z Azure Monitor

> [!NOTE]
> Ta lista jest generowana automatycznie. Wszelkie modyfikacje wprowadzone do tej listy za pośrednictwem usługi GitHub mogą być zapisywane bez ostrzeżenia. Skontaktuj się z autorem tego artykułu, aby uzyskać szczegółowe informacje na temat sposobu wprowadzania trwałych aktualizacji.

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. 

Ten artykuł to kompletna lista wszystkich platform (które są zbierane automatycznie), które są obecnie dostępne w ramach skonsolidowanego potoku metryki Azure Monitor. Metryki zmienione lub dodane po dacie w górnej części tego artykułu mogą nie wyglądać jeszcze poniżej. Aby wykonać zapytanie o i uzyskać dostęp do listy metryk programowo, użyj [wersji interfejsu api 2018-01-01](/rest/api/monitor/metricdefinitions). Inne metryki, których nie ma na tej liście, mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

Metryki są zorganizowane według dostawców zasobów i typów zasobów. Aby uzyskać listę usług i dostawców zasobów oraz typy, które należą do nich, zobacz [dostawcy zasobów dla usług platformy Azure](../../azure-resource-manager/management/azure-services-resource-providers.md).  

## <a name="exporting-platform-metrics-to-other-locations"></a>Eksportowanie metryk platformy do innych lokalizacji

Metryki platformy z potoku usługi Azure monitor można wyeksportować do innych lokalizacji na jeden z dwóch sposobów.
1. Korzystanie z [interfejsu API REST metryk](/rest/api/monitor/metrics/list)
2. Używanie [ustawień diagnostycznych](diagnostic-settings.md) do kierowania metryk platformy do programu 
    - Azure Storage
    - Dzienniki Azure Monitor (a tym samym Log Analytics)
    - Centra zdarzeń, w których można uzyskać dostęp do systemów innych niż firmy Microsoft 

Korzystanie z ustawień diagnostycznych to najprostszy sposób na kierowanie metryk, ale istnieją pewne ograniczenia: 

- **Niektóre nie do eksportowania** — wszystkie metryki są eksportowane przy użyciu interfejsu API REST, ale niektórych nie można eksportować za pomocą ustawień diagnostycznych ze względu na złożonego w zapleczu Azure monitor. Kolumna, którą można *eksportować za pomocą ustawień diagnostycznych* w tabelach poniżej, umożliwia wyeksportowanie metryk w ten sposób.  

- **Metryki wielowymiarowe** — wysyłanie metryk wielowymiarowych do innych lokalizacji za pośrednictwem ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach. *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.

## <a name="guest-os-and-host-os-metrics"></a>System operacyjny gościa i metryki systemu operacyjnego hosta

> [!WARNING]
> Metryki dla systemu operacyjnego gościa (systemu operacyjnego gościa) działającego w usłudze Azure Virtual Machines, Service Fabric i Cloud Services **nie** są wyświetlane w tym miejscu. Metryki systemu operacyjnego gościa muszą być zbierane za pomocą co najmniej jednego agenta, który działa w systemie lub w ramach systemu operacyjnego gościa.  Metryki systemu operacyjnego gościa obejmują liczniki wydajności, które śledzą użycie procesora CPU gościa lub pamięci, z których oba są często używane do automatycznego skalowania lub generowania alertów. 
>
> **Metryki systemu operacyjnego hosta są dostępne i znajdują się poniżej.** Nie są one takie same. Metryki systemu operacyjnego hosta odnoszą się do sesji funkcji Hyper-V obsługującej sesję systemu operacyjnego gościa. 

> [!TIP]
> Najlepszym rozwiązaniem jest użycie i skonfigurowanie [rozszerzenia Diagnostyka Azure](diagnostics-extension-overview.md) w celu wysyłania metryk wydajności systemu operacyjnego gościa do tej samej Azure Monitorj bazy danych metryk, w której są przechowywane metryki platformy. Rozszerzenie kieruje metryki systemu operacyjnego gościa za pomocą interfejsu API [metryk niestandardowych](metrics-custom-overview.md) . Następnie możesz wykresów, alertów i w inny sposób używać metryk systemu operacyjnego gościa, takich jak metryki platformy. Alternatywnie można także użyć agenta Log Analytics do wysyłania metryk systemu operacyjnego gościa do Azure Monitor dzienników/Log Analytics. Można wykonywać zapytania dotyczące tych metryk w połączeniu z danymi niemetrycznymi. 

Aby uzyskać ważne informacje dodatkowe, zobacz [Monitorowanie agentów — Omówienie](agents-overview.md).

## <a name="table-formatting"></a>Formatowanie tabeli

> [!IMPORTANT] 
> Ta najnowsza aktualizacja dodaje nową kolumnę i ponownie porządkuje metryki jako alfabetyczne. Dodatkowe informacje oznaczają, że poniższe tabele mogą mieć poziomy pasek przewijania u dołu, w zależności od szerokości okna przeglądarki. Jeśli uważasz, że brakuje informacji, użyj paska przewijania, aby wyświetlić całości tabeli.

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/serwery

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Tak|Pamięć: bieżąca cena oczyszczarki|Liczba|Średnia|Bieżąca cena pamięci, $/Byte/Time, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Tak|Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia|Bajty|Średnia|Ilość pamięci (w bajtach), która nie podlega wyprzeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|CleanerMemoryShrinkable|Tak|Pamięć: zmniejszanie ilości pamięci czyszczącej|Bajty|Średnia|Ilość pamięci, w bajtach, podlegająca przeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|CommandPoolBusyThreads|Tak|Wątki: zajęte wątki w puli poleceń|Liczba|Średnia|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Tak|Wątki: bezczynne wątki w puli poleceń|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolJobQueueLength|Tak|Długość kolejki zadań puli poleceń|Liczba|Średnia|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|Wartości CurrentConnections|Tak|Połączenie: bieżące połączenia|Liczba|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|ServerResourceType|
|CurrentUserSessions|Tak|Bieżące sesje użytkowników|Liczba|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|LongParsingBusyThreads|Tak|Wątki: zajęte wątki o długotrwałej analizie|Liczba|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingIdleThreads|Tak|Wątki: długotrwałe wątki bezczynne analizy|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingJobQueueLength|Tak|Wątki: Długość kolejki zadań o długotrwałej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|ServerResourceType|
|mashup_engine_memory_metric|Tak|Pamięć aparatu M|Bajty|Średnia|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_private_bytes_metric|Tak|Liczba prywatnych bajtów aparatu M|Bajty|Średnia|Użycie bajtów prywatnych przez procesy aparatu mashupów.|ServerResourceType|
|mashup_engine_qpu_metric|Tak|M QPU aparatu|Liczba|Średnia|QPU użycie przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Tak|Liczba bajtów wirtualnych aparatu M|Bajty|Średnia|Użycie bajtów wirtualnych przez procesy aparatu mashupów.|ServerResourceType|
|memory_metric|Tak|Memory (Pamięć)|Bajty|Średnia|Memory (pamięć). Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|ServerResourceType|
|memory_thrashing_metric|Tak|Przeładowywanie pamięci|Procent|Średnia|Średnia pamięć migotanie.|ServerResourceType|
|MemoryLimitHard|Tak|Pamięć: sztywny limit pamięci|Bajty|Średnia|Limit pamięci twardej z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Tak|Pamięć: limit pamięci jest wysoki|Bajty|Średnia|Górny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Tak|Pamięć: limit pamięci — niski|Bajty|Średnia|Limit braku pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Tak|Pamięć: limit pamięci — tryb VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryUsage|Tak|Pamięć: użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera jako używany do obliczania ceny pamięci czyszczącej. Równa się licznikowi Process\PrivateBytes i rozmiarowi danych mapowanych na pamięć, ignorowanie wszelkich pamięci, które zostały zamapowane lub przydzielone przez aparat analityczny w pamięci xVelocity (VertiPaq) przekraczający limit pamięci aparatu xVelocity.|ServerResourceType|
|private_bytes_metric|Tak|Bajty prywatne|Bajty|Średnia|Bajty prywatne.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Tak|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Tak|Wątki: zajęte wątki innych niż we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania inne niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Tak|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Tak|Wątki: bezczynne wątki inne niż we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż zadania we/wy.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Tak|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolJobQueueLength|Tak|Długość kolejki zadań puli przetwarzania|Liczba|Średnia|Liczba zadań innych niż zadania we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|qpu_metric|Tak|QPU|Liczba|Średnia|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|ServerResourceType|
|QueryPoolBusyThreads|Tak|Wątki zajęte w puli zapytań|Liczba|Średnia|Liczba zajętych wątków w puli wątków zapytań.|ServerResourceType|
|QueryPoolIdleThreads|Tak|Wątki: bezczynne wątki puli zapytań|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Tak|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytań.|ServerResourceType|
|limit przydziału|Tak|Pamięć: limit przydziału|Bajty|Średnia|Bieżący limit pamięci (w bajtach). Przydział pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|ServerResourceType|
|QuotaBlocked|Tak|Pamięć: zablokowany limit przydziału|Liczba|Średnia|Bieżąca liczba żądań przydziałów blokowanych do momentu zwolnienia innych limitów przydziału pamięci.|ServerResourceType|
|RowsConvertedPerSec|Tak|Przetwarzanie: przekonwertowane wiersze na sekundę|CountPerSecond|Średnia|Szybkość wierszy przekonwertowanych podczas przetwarzania.|ServerResourceType|
|RowsReadPerSec|Tak|Przetwarzanie: odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsWrittenPerSec|Tak|Przetwarzanie: liczba wierszy na sekundę|CountPerSecond|Średnia|Częstotliwość wierszy zapisywana podczas przetwarzania.|ServerResourceType|
|ShortParsingBusyThreads|Tak|Wątki: zajęte wątki z krótkim analizowaniem|Liczba|Średnia|Liczba zajętych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingIdleThreads|Tak|Wątki: bezczynne wątki z krótkim analizowaniem|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingJobQueueLength|Tak|Wątki: Długość kolejki zadań o krótkiej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o krótkiej analizie.|ServerResourceType|
|SuccessfullConnectionsPerSec|Tak|Udane połączenia na sekundę|CountPerSecond|Średnia|Szybkość pomyślnych połączeń.|ServerResourceType|
|TotalConnectionFailures|Tak|Łączna liczba błędów połączenia|Liczba|Średnia|Łączna liczba nieudanych prób połączenia.|ServerResourceType|
|TotalConnectionRequests|Tak|Łączna liczba żądań połączenia|Liczba|Średnia|Łączna liczba żądań połączenia. Są to wejścia.|ServerResourceType|
|VertiPaqNonpaged|Tak|Pamięć: VertiPaq niestronicowana|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Tak|Pamięć: stronicowana na stronie VertiPaq|Bajty|Średnia|Bajty stronicowanej pamięci w użyciu dla danych w pamięci.|ServerResourceType|
|virtual_bytes_metric|Tak|Bajty wirtualne|Bajty|Średnia|Bajty wirtualne.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BackendDuration|Tak|Czas trwania żądań wewnętrznej bazy danych|)|Średnia|Czas trwania żądań zaplecza w milisekundach|Lokalizacja, nazwa hosta|
|Pojemność|Tak|Pojemność|Procent|Średnia|Metryka wykorzystania dla usługi ApiManagement|Lokalizacja|
|Czas trwania|Tak|Całkowity czas trwania żądań bramy|)|Średnia|Całkowity czas trwania żądań bramy w milisekundach|Lokalizacja, nazwa hosta|
|EventHubDroppedEvents|Tak|Opuszczone zdarzenia EventHub|Liczba|Łącznie|Liczba pominiętych zdarzeń z powodu osiągnięcia limitu rozmiaru kolejki|Lokalizacja|
|EventHubRejectedEvents|Tak|Odrzucone zdarzenia EventHub|Liczba|Łącznie|Liczba odrzuconych zdarzeń EventHub (niewłaściwa konfiguracja lub nieautoryzowana)|Lokalizacja|
|EventHubSuccessfulEvents|Tak|Pomyślne zdarzenia EventHub|Liczba|Łącznie|Liczba pomyślnych zdarzeń EventHub|Lokalizacja|
|EventHubThrottledEvents|Tak|Ograniczone zdarzenia EventHub|Liczba|Łącznie|Liczba zdarzeń zdarzenia EventHub z ograniczeniami|Lokalizacja|
|EventHubTimedoutEvents|Tak|Przekroczono limit czasu zdarzeń EventHub|Liczba|Łącznie|Liczba przekroczeń limitu czasu zdarzeń EventHub|Lokalizacja|
|EventHubTotalBytesSent|Tak|Rozmiar zdarzeń EventHub|Bajty|Łącznie|Łączny rozmiar zdarzeń EventHub w bajtach|Lokalizacja|
|EventHubTotalEvents|Tak|Łączna liczba zdarzeń EventHub|Liczba|Łącznie|Liczba zdarzeń wysyłanych do centrum EventHub|Lokalizacja|
|EventHubTotalFailedEvents|Tak|Niepowodzenie zdarzeń EventHub|Liczba|Łącznie|Liczba zakończonych niepowodzeniem zdarzeń EventHub|Lokalizacja|
|FailedRequests|Tak|Nieudane żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba błędów w żądaniach bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|NetworkConnectivity|Tak|Stan łączności sieciowej zasobów (wersja zapoznawcza)|Liczba|Średnia|Stan połączenia sieciowego zależnych typów zasobów z usługi API Management|Lokalizacja, ResourceType|
|OtherRequests|Tak|Inne żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba innych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|Żądania|Tak|Żądania|Liczba|Łącznie|Metryki żądania bramy z wieloma wymiarami|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|Żądania successfulrequests|Tak|Pomyślne żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba pomyślnych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|TotalRequests|Tak|Łączna liczba żądań bramy (przestarzałe)|Liczba|Łącznie|Liczba żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|UnauthorizedRequests|Tak|Nieautoryzowane żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba nieautoryzowanych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Tak|HttpIncomingRequestCount|Liczba|Liczba|Łączna liczba przychodzących żądań HTTP.|StatusCode, uwierzytelnianie|
|HttpIncomingRequestDuration|Tak|HttpIncomingRequestDuration|Liczba|Średnia|Opóźnienie dla żądania HTTP.|StatusCode, uwierzytelnianie|
|ThrottledHttpRequestCount|Tak|ThrottledHttpRequestCount|Liczba|Liczba|Żądania HTTP z ograniczeniami.|Brak wymiarów|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Sprężyna

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|JVM. GC. Live. Data. size|Tak|JVM. GC. Live. Data. size|Bajty|Średnia|Rozmiar starej puli pamięci generacji po pełnej operacji GC|Wdrożenie, nazwa_aplikacji, pod|
|JVM. GC. max. Data. size|Tak|JVM. GC. max. Data. size|Bajty|Średnia|Maksymalny rozmiar puli pamięci starej generacji|Wdrożenie, nazwa_aplikacji, pod|
|JVM. GC. Memory. przydzielono|Tak|JVM. GC. Memory. przydzielono|Bajty|Maksimum|Zwiększa się w celu zwiększenia rozmiaru puli pamięci młodej generacji po jednej operacji GC do wcześniejszej|Wdrożenie, nazwa_aplikacji, pod|
|JVM. GC. Memory. awansowana|Tak|JVM. GC. Memory. awansowana|Bajty|Maksimum|Liczba pozytywnych wzrostów rozmiaru starej puli pamięci generacji przed GC do po GC|Wdrożenie, nazwa_aplikacji, pod|
|JVM. GC. Pause. Total. Count|Tak|JVM. GC. Pause. Total. Count|Liczba|Łącznie|Liczba wstrzymań GC|Wdrożenie, nazwa_aplikacji, pod|
|JVM. GC. Pause. Total. Time|Tak|JVM. GC. Pause. Total. Time|)|Łącznie|Całkowity czas wstrzymania odzyskiwania pamięci|Wdrożenie, nazwa_aplikacji, pod|
|JVM. Memory. Committed|Tak|JVM. Memory. Committed|Bajty|Średnia|Pamięć przypisana do JVM w bajtach|Wdrożenie, nazwa_aplikacji, pod|
|JVM. Memory. Max|Tak|JVM. Memory. Max|Bajty|Maksimum|Maksymalna ilość pamięci w bajtach, która może być używana do zarządzania pamięcią|Wdrożenie, nazwa_aplikacji, pod|
|JVM. Memory. użyty|Tak|JVM. Memory. użyty|Bajty|Średnia|Pamięć aplikacji użyta w bajtach|Wdrożenie, nazwa_aplikacji, pod|
|Process. CPU. Usage|Tak|Process. CPU. Usage|Procent|Średnia|Ostatnie użycie procesora CPU przez proces JVM|Wdrożenie, nazwa_aplikacji, pod|
|System. CPU. Usage|Tak|System. CPU. Usage|Procent|Średnia|Ostatnie użycie procesora CPU całego systemu|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Global. Error|Tak|Tomcat. Global. Error|Liczba|Łącznie|Błąd globalny Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Global. Receives|Tak|Tomcat. Global. Receives|Bajty|Łącznie|Całkowita liczba odebranych bajtów Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Global. Request. AVG. Time|Tak|Tomcat. Global. Request. AVG. Time|)|Średnia|Średni czas żądania Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Global. Request. Max|Tak|Tomcat. Global. Request. Max|)|Maksimum|Maksymalny czas żądania Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Global. Request. Total. Count|Tak|Tomcat. Global. Request. Total. Count|Liczba|Łącznie|Łączna liczba żądań Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Global. Request. Total. Time|Tak|Tomcat. Global. Request. Total. Time|)|Łącznie|Łączny czas żądania Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Global. sent|Tak|Tomcat. Global. sent|Bajty|Łącznie|Całkowita liczba wysłanych bajtów Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Sessions. Active. Current|Tak|Tomcat. Sessions. Active. Current|Liczba|Łącznie|Liczba aktywnych sesji Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Sessions. Active. Max|Tak|Tomcat. Sessions. Active. Max|Liczba|Łącznie|Maksymalna liczba aktywnych sesji Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Sessions. Alive. Max|Tak|Tomcat. Sessions. Alive. Max|)|Maksimum|Maksymalny czas aktywności sesji Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Sessions. Created|Tak|Tomcat. Sessions. Created|Liczba|Łącznie|Liczba utworzonych sesji Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Sessions. wygasła|Tak|Tomcat. Sessions. wygasła|Liczba|Łącznie|Liczba wygasłych sesji Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Sessions. rejected|Tak|Tomcat. Sessions. rejected|Liczba|Łącznie|Liczba odrzuconych sesji Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|tomcat.threads.config. Max|Tak|tomcat.threads.config. Max|Liczba|Łącznie|Maksymalna liczba wątków w konfiguracji Tomcat|Wdrożenie, nazwa_aplikacji, pod|
|Tomcat. Threads. Current|Tak|Tomcat. Threads. Current|Liczba|Łącznie|Tomcat bieżąca liczba wątków|Wdrożenie, nazwa_aplikacji, pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|TotalJob|Tak|Łączna liczba zadań|Liczba|Łącznie|Całkowita liczba zadań|Element Runbook, stan|
|TotalUpdateDeploymentMachineRuns|Tak|Łączna liczba przebiegów wdrażania aktualizacji maszyny|Liczba|Łącznie|Łączna liczba uruchomień maszyn wdrożenia aktualizacji oprogramowania w ramach wdrożenia aktualizacji oprogramowania|. Softwareupdateconfigurationname, status, TargetComputer,. Softwareupdateconfigurationrunid|
|TotalUpdateDeploymentRuns|Tak|Łączna liczba przebiegów wdrażania aktualizacji|Liczba|Łącznie|Łączna liczba przebiegów wdrażania aktualizacji oprogramowania|. Softwareupdateconfigurationname, stan|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CoreCount|Nie|Liczba dedykowanych rdzeni|Liczba|Łącznie|Łączna liczba rdzeni dedykowanych na koncie wsadowym|Brak wymiarów|
|CreatingNodeCount|Nie|Tworzenie liczby węzłów|Liczba|Łącznie|Liczba tworzonych węzłów|Brak wymiarów|
|IdleNodeCount|Nie|Liczba węzłów bezczynności|Liczba|Łącznie|Liczba bezczynnych węzłów|Brak wymiarów|
|JobDeleteCompleteEvent|Tak|Zdarzenia ukończenia usuwania zadania|Liczba|Łącznie|Całkowita liczba pomyślnie usuniętych zadań.|Zadanie|
|JobDeleteStartEvent|Tak|Zdarzenia rozpoczęcia usuwania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały zażądane do usunięcia.|Zadanie|
|JobDisableCompleteEvent|Tak|Zdarzenia ukończenia wyłączania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie wyłączone.|Zadanie|
|JobDisableStartEvent|Tak|Wyłącz zdarzenia uruchamiania dla zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały zlecone do wyłączenia.|Zadanie|
|JobStartEvent|Tak|Zdarzenia uruchamiania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie uruchomione.|Zadanie|
|JobTerminateCompleteEvent|Tak|Zdarzenia ukończenia kończenia zadania|Liczba|Łącznie|Łączna liczba zadań zakończonych pomyślnie.|Zadanie|
|JobTerminateStartEvent|Tak|Zdarzenia rozpoczęcia zakończenia zadania|Liczba|Łącznie|Całkowita liczba zadań, które zostały zażądane do zakończenia.|Zadanie|
|LeavingPoolNodeCount|Nie|Opuszczanie liczby węzłów puli|Liczba|Łącznie|Liczba węzłów opuszczających pulę|Brak wymiarów|
|LowPriorityCoreCount|Nie|LowPriority rdzeń|Liczba|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie wsadowym|Brak wymiarów|
|OfflineNodeCount|Nie|Liczba węzłów w trybie offline|Liczba|Łącznie|Liczba węzłów offline|Brak wymiarów|
|PoolCreateEvent|Tak|Zdarzenia tworzenia puli|Liczba|Łącznie|Łączna liczba utworzonych pul|poolId|
|PoolDeleteCompleteEvent|Tak|Zdarzenia ukończenia usuwania puli|Liczba|Łącznie|Łączna liczba ukończonych usunięć pul|poolId|
|PoolDeleteStartEvent|Tak|Zdarzenia uruchamiania usuwania puli|Liczba|Łącznie|Łączna liczba uruchomionych usunięć pul|poolId|
|PoolResizeCompleteEvent|Tak|Zdarzenia ukończenia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba zakończono zmienionych rozmiarów puli|poolId|
|PoolResizeStartEvent|Tak|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba rozpoczętych rozmiarów puli|poolId|
|PreemptedNodeCount|Nie|Liczba przeniesiona węzłów|Liczba|Łącznie|Liczba przeniesiona węzłów|Brak wymiarów|
|RebootingNodeCount|Nie|Ponowny rozruch liczby węzłów|Liczba|Łącznie|Liczba ponownych uruchomień węzłów|Brak wymiarów|
|ReimagingNodeCount|Nie|Liczba węzłów regraficznych|Liczba|Łącznie|Liczba węzłów regraficznych|Brak wymiarów|
|RunningNodeCount|Nie|Liczba uruchomionych węzłów|Liczba|Łącznie|Liczba uruchomionych węzłów|Brak wymiarów|
|StartingNodeCount|Nie|Początkowa liczba węzłów|Liczba|Łącznie|Liczba początkowych węzłów|Brak wymiarów|
|StartTaskFailedNodeCount|Nie|Uruchamianie zadania nie powiodło się liczba węzłów|Liczba|Łącznie|Liczba węzłów, w których zadanie uruchomieniowe nie powiodło się|Brak wymiarów|
|TaskCompleteEvent|Tak|Zdarzenia ukończenia zadania|Liczba|Łącznie|Całkowita liczba ukończonych zadań|poolId, jobId|
|TaskFailEvent|Tak|Zdarzenia błędów zadań|Liczba|Łącznie|Łączna liczba zadań zakończonych niepowodzeniem w stanie niepowodzenia|poolId, jobId|
|TaskStartEvent|Tak|Zdarzenia uruchamiania zadania|Liczba|Łącznie|Łączna liczba rozpoczętych zadań|poolId, jobId|
|TotalLowPriorityNodeCount|Nie|Liczba węzłów Low-Priority|Liczba|Łącznie|Łączna liczba węzłów o niskim priorytecie na koncie wsadowym|Brak wymiarów|
|TotalNodeCount|Nie|Liczba dedykowanych węzłów|Liczba|Łącznie|Łączna liczba węzłów dedykowanych na koncie wsadowym|Brak wymiarów|
|UnusableNodeCount|Nie|Liczba węzłów, których nie można użyć|Liczba|Łącznie|Liczba nieużywanych węzłów|Brak wymiarów|
|WaitingForStartTaskNodeCount|Nie|Oczekiwanie na liczbę węzłów zadania uruchamiania|Liczba|Łącznie|Liczba węzłów oczekujących na zakończenie zadania uruchamiania|Brak wymiarów|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/obszary robocze

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne rdzenie|Tak|Aktywne rdzenie|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Aktywne węzły|Tak|Aktywne węzły|Liczba|Średnia|Liczba uruchomionych węzłów|Scenariusz, ClusterName|
|Rdzenie bezczynne|Tak|Rdzenie bezczynne|Liczba|Średnia|Liczba rdzeni bezczynnych|Scenariusz, ClusterName|
|Węzły bezczynne|Tak|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów|Scenariusz, ClusterName|
|Ukończono zadanie|Tak|Ukończono zadanie|Liczba|Łącznie|Liczba ukończonych zadań|Scenariusz, ClusterName, ResultType|
|Przesłane zadanie|Tak|Przesłane zadanie|Liczba|Łącznie|Liczba przesłanych zadań|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Tak|Opuszczanie rdzeni|Liczba|Średnia|Liczba rdzeni wychodzących|Scenariusz, ClusterName|
|Opuszczanie węzłów|Tak|Opuszczanie węzłów|Liczba|Średnia|Liczba wychodzących węzłów|Scenariusz, ClusterName|
|Występujące rdzenie|Tak|Występujące rdzenie|Liczba|Średnia|Liczba przeniesiona rdzeni|Scenariusz, ClusterName|
|Zastępujące węzły|Tak|Zastępujące węzły|Liczba|Średnia|Liczba przeniesiona węzłów|Scenariusz, ClusterName|
|Procent wykorzystania przydziałów|Tak|Procent wykorzystania przydziałów|Liczba|Średnia|Procent wykorzystania przydziałów|Scenariusz, ClusterName, VmFamilyName, VmPriority|
|Łączna liczba rdzeni|Tak|Łączna liczba rdzeni|Liczba|Średnia|Łączna liczba rdzeni|Scenariusz, ClusterName|
|Łączna liczba węzłów|Tak|Łączna liczba węzłów|Liczba|Średnia|Łączna liczba węzłów|Scenariusz, ClusterName|
|Rdzenie, których nie można używać|Tak|Rdzenie, których nie można używać|Liczba|Średnia|Liczba rdzeni, których nie można używać|Scenariusz, ClusterName|
|Węzły niezdatne do użytku|Tak|Węzły niezdatne do użytku|Liczba|Średnia|Liczba nieużywanych węzłów|Scenariusz, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. łańcucha bloków/blockchainMembers

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Tak|BroadcastProcessedCountDisplayName|Liczba|Średnia|Liczba przetworzonych transakcji.|Węzeł, kanał, typ, stan|
|ConnectionAccepted|Tak|Zaakceptowane połączenia|Liczba|Łącznie|Zaakceptowane połączenia|Węzeł|
|ConnectionActive|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Węzeł|
|ConnectionHandled|Tak|Obsłużone połączenia|Liczba|Łącznie|Obsłużone połączenia|Węzeł|
|ConsensusEtcdraftCommittedBlockNumber|Tak|ConsensusEtcdraftCommittedBlockNumberDisplayName|Liczba|Średnia|Numer bloku dla najnowszej zatwierdzonego bloku.|Węzeł, kanał|
|CpuUsagePercentageInDouble|Tak|Procent użycia procesora CPU|Procent|Maksimum|Procent użycia procesora CPU|Węzeł|
|EndorserEndorsementFailures|Tak|EndorserEndorsementFailuresDisplayName|Liczba|Średnia|Liczba zakończonych niepowodzeniem potwierdzeń.|Node, Channel, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Tak|GossipLeaderElectionLeaderDisplayName|Liczba|Średnia|Element równorzędny to lider (1) lub Flaga monitująca (0).|Węzeł, kanał|
|GossipMembershipTotalPeersKnown|Tak|GossipMembershipTotalPeersKnownDisplayName|Liczba|Średnia|Łączna liczba znanych elementów równorzędnych.|Węzeł, kanał|
|GossipStateHeight|Tak|GossipStateHeightDisplayName|Liczba|Średnia|Bieżąca wysokość księgi.|Węzeł, kanał|
|IOReadBytes|Tak|Bajty odczytu we/wy|Bajty|Łącznie|Bajty odczytu we/wy|Węzeł|
|IOWriteBytes|Tak|Bajty zapisu we/wy|Bajty|Łącznie|Bajty zapisu we/wy|Węzeł|
|LedgerTransactionCount|Tak|LedgerTransactionCountDisplayName|Liczba|Średnia|Liczba przetworzonych transakcji.|Node, Channel, transaction_type, chaincode, validation_code|
|MemoryLimit|Tak|Limit pamięci|Bajty|Średnia|Limit pamięci|Węzeł|
|MemoryUsage|Tak|Użycie pamięci|Bajty|Średnia|Użycie pamięci|Węzeł|
|MemoryUsagePercentageInDouble|Tak|Procent użycia pamięci|Procent|Średnia|Procent użycia pamięci|Węzeł|
|PendingTransactions|Tak|Oczekujące transakcje|Liczba|Średnia|Oczekujące transakcje|Węzeł|
|ProcessedBlocks|Tak|Przetworzone bloki|Liczba|Łącznie|Przetworzone bloki|Węzeł|
|ProcessedTransactions|Tak|Przetworzone transakcje|Liczba|Łącznie|Przetworzone transakcje|Węzeł|
|QueuedTransactions|Tak|Transakcje w kolejce|Liczba|Średnia|Transakcje w kolejce|Węzeł|
|RequestHandled|Tak|Obsłużone żądania|Liczba|Łącznie|Obsłużone żądania|Węzeł|
|StorageUsage|Tak|Użycie magazynu|Bajty|Średnia|Użycie magazynu|Węzeł|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|allcachehits|Tak|Trafienia pamięci podręcznej (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, port, podstawowy|
|allcachemisses|Tak|Chybienia w pamięci podręcznej (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, port, podstawowy|
|allcacheRead|Tak|Odczyt pamięci podręcznej (na podstawie wystąpienia)|BytesPerSecond|Maksimum||ShardId, port, podstawowy|
|allcacheWrite|Tak|Zapis w pamięci podręcznej (na podstawie wystąpienia)|BytesPerSecond|Maksimum||ShardId, port, podstawowy|
|allconnectedclients|Tak|Połączeni klienci (oparte na wystąpieniach)|Liczba|Maksimum||ShardId, port, podstawowy|
|allevictedkeys|Tak|Wykluczone klucze (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, port, podstawowy|
|allexpiredkeys|Tak|Wygasłe klucze (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, port, podstawowy|
|allgetcommands|Tak|Pobiera (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, port, podstawowy|
|alloperationsPerSecond|Tak|Operacje na sekundę (oparte na wystąpieniach)|Liczba|Maksimum||ShardId, port, podstawowy|
|allserverLoad|Tak|Ładowanie serwera (oparte na wystąpieniach)|Procent|Maksimum||ShardId, port, podstawowy|
|allsetcommands|Tak|Zestawy (oparte na wystąpieniach)|Liczba|Łącznie||ShardId, port, podstawowy|
|alltotalcommandsprocessed|Tak|Łączna liczba operacji (opartych na wystąpieniach)|Liczba|Łącznie||ShardId, port, podstawowy|
|alltotalkeys|Tak|Łączna liczba kluczy (oparta na wystąpieniach)|Liczba|Maksimum||ShardId, port, podstawowy|
|allusedmemory|Tak|Używana pamięć (oparta na wystąpieniach)|Bajty|Maksimum||ShardId, port, podstawowy|
|allusedmemorypercentage|Tak|Procent używanej pamięci (na podstawie wystąpienia)|Procent|Maksimum||ShardId, port, podstawowy|
|allusedmemoryRss|Tak|Używana pamięć RSS (oparta na wystąpieniach)|Bajty|Maksimum||ShardId, port, podstawowy|
|cachehits|Tak|Trafienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachehits0|Tak|Trafienia pamięci podręcznej (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|cachehits1|Tak|Trafienia pamięci podręcznej (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|cachehits2|Tak|Trafienia pamięci podręcznej (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|cachehits3|Tak|Trafienia pamięci podręcznej (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|cachehits4|Tak|Trafienia pamięci podręcznej (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|cachehits5|Tak|Trafienia pamięci podręcznej (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|cachehits6|Tak|Trafienia pamięci podręcznej (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|cachehits7|Tak|Trafienia pamięci podręcznej (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|cachehits8|Tak|Trafienia pamięci podręcznej (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|cachehits9|Tak|Trafienia pamięci podręcznej (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|cacheLatency|Tak|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Liczba|Średnia||ShardId|
|cachemisses|Tak|Chybienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachemisses0|Tak|Chybienia w pamięci podręcznej (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|cachemisses1|Tak|Chybienia w pamięci podręcznej (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|cachemisses2|Tak|Chybienia w pamięci podręcznej (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|cachemisses3|Tak|Chybienia w pamięci podręcznej (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|cachemisses4|Tak|Chybienia w pamięci podręcznej (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|cachemisses5|Tak|Chybienia w pamięci podręcznej (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|cachemisses6|Tak|Chybienia w pamięci podręcznej (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|cachemisses7|Tak|Chybienia w pamięci podręcznej (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|cachemisses8|Tak|Chybienia w pamięci podręcznej (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|cachemisses9|Tak|Chybienia w pamięci podręcznej (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|cachemissrate|Tak|Współczynnik chybień w pamięci podręcznej|Procent|cachemissrate||ShardId|
|cacheRead|Tak|Odczyt pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheRead0|Tak|Odczyt pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead1|Tak|Odczyt pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead2|Tak|Odczyt pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead3|Tak|Odczyt pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead4|Tak|Odczyt pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead5|Tak|Odczyt pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead6|Tak|Odczyt pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead7|Tak|Odczyt pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead8|Tak|Odczyt pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheRead9|Tak|Odczyt pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite|Tak|Zapis w pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheWrite0|Tak|Zapis pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite1|Tak|Zapisywanie pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite2|Tak|Zapisywanie pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite3|Tak|Zapis w pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite4|Tak|Zapis pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite5|Tak|Zapis w pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite6|Tak|Zapis w pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite7|Tak|Zapis w pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite8|Tak|Zapis w pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Brak wymiarów|
|cacheWrite9|Tak|Zapis w pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Brak wymiarów|
|connectedclients|Tak|Połączeni klienci|Liczba|Maksimum||ShardId|
|connectedclients0|Tak|Połączeni klienci (fragmentu 0)|Liczba|Maksimum||Brak wymiarów|
|connectedclients1|Tak|Połączeni klienci (fragmentu 1)|Liczba|Maksimum||Brak wymiarów|
|connectedclients2|Tak|Połączeni klienci (fragmentu 2)|Liczba|Maksimum||Brak wymiarów|
|connectedclients3|Tak|Połączeni klienci (fragmentu 3)|Liczba|Maksimum||Brak wymiarów|
|connectedclients4|Tak|Połączeni klienci (fragmentu 4)|Liczba|Maksimum||Brak wymiarów|
|connectedclients5|Tak|Połączeni klienci (fragmentu 5)|Liczba|Maksimum||Brak wymiarów|
|connectedclients6|Tak|Połączeni klienci (fragmentu 6)|Liczba|Maksimum||Brak wymiarów|
|connectedclients7|Tak|Połączeni klienci (fragmentu 7)|Liczba|Maksimum||Brak wymiarów|
|connectedclients8|Tak|Połączeni klienci (fragmentu 8)|Liczba|Maksimum||Brak wymiarów|
|connectedclients9|Tak|Połączeni klienci (fragmentu 9)|Liczba|Maksimum||Brak wymiarów|
|błędy|Tak|błędy|Liczba|Maksimum||ShardId, ErrorType|
|evictedkeys|Tak|Wykluczone klucze|Liczba|Łącznie||ShardId|
|evictedkeys0|Tak|Wykluczone klucze (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys1|Tak|Wykluczone klucze (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys2|Tak|Wykluczone klucze (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys3|Tak|Wykluczone klucze (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys4|Tak|Wykluczone klucze (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys5|Tak|Wykluczone klucze (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys6|Tak|Wykluczone klucze (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys7|Tak|Wykluczone klucze (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys8|Tak|Wykluczone klucze (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|evictedkeys9|Tak|Wykluczone klucze (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys|Tak|Wygasłe klucze|Liczba|Łącznie||ShardId|
|expiredkeys0|Tak|Wygasłe klucze (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys1|Tak|Wygasłe klucze (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys2|Tak|Wygasłe klucze (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys3|Tak|Wygasłe klucze (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys4|Tak|Wygasłe klucze (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys5|Tak|Wygasłe klucze (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys6|Tak|Wygasłe klucze (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys7|Tak|Wygasłe klucze (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys8|Tak|Wygasłe klucze (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|expiredkeys9|Tak|Wygasłe klucze (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|GetCommands|Tak|Pobrania|Liczba|Łącznie||ShardId|
|getcommands0|Tak|Pobiera (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|getcommands1|Tak|Pobiera (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|getcommands2|Tak|Pobiera (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|getcommands3|Tak|Pobiera (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|getcommands4|Tak|Pobiera (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|getcommands5|Tak|Pobiera (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|getcommands6|Tak|Pobiera (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|getcommands7|Tak|Pobiera (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|getcommands8|Tak|Pobiera (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|getcommands9|Tak|Pobiera (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|operationsPerSecond|Tak|Liczba operacji na sekundę|Liczba|Maksimum||ShardId|
|operationsPerSecond0|Tak|Operacje na sekundę (fragmentu 0)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond1|Tak|Operacje na sekundę (fragmentu 1)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond2|Tak|Operacje na sekundę (fragmentu 2)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond3|Tak|Operacje na sekundę (fragmentu 3)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond4|Tak|Operacje na sekundę (fragmentu 4)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond5|Tak|Operacje na sekundę (fragmentu 5)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond6|Tak|Operacje na sekundę (fragmentu 6)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond7|Tak|Operacje na sekundę (fragmentu 7)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond8|Tak|Operacje na sekundę (fragmentu 8)|Liczba|Maksimum||Brak wymiarów|
|operationsPerSecond9|Tak|Operacje na sekundę (fragmentu 9)|Liczba|Maksimum||Brak wymiarów|
|percentProcessorTime|Tak|Procesor CPU|Procent|Maksimum||ShardId|
|percentProcessorTime0|Tak|Procesor CPU (fragmentu 0)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime1|Tak|Procesor CPU (fragmentu 1)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime2|Tak|Procesor CPU (fragmentu 2)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime3|Tak|Procesor CPU (fragmentu 3)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime4|Tak|Procesor CPU (fragmentu 4)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime5|Tak|Procesor CPU (fragmentu 5)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime6|Tak|Procesor CPU (fragmentu 6)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime7|Tak|Procesor CPU (fragmentu 7)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime8|Tak|Procesor CPU (fragmentu 8)|Procent|Maksimum||Brak wymiarów|
|percentProcessorTime9|Tak|Procesor CPU (fragmentu 9)|Procent|Maksimum||Brak wymiarów|
|serverLoad|Tak|Obciążenie serwera|Procent|Maksimum||ShardId|
|serverLoad0|Tak|Ładowanie serwera (fragmentu 0)|Procent|Maksimum||Brak wymiarów|
|serverLoad1|Tak|Ładowanie serwera (fragmentu 1)|Procent|Maksimum||Brak wymiarów|
|serverLoad2|Tak|Ładowanie serwera (fragmentu 2)|Procent|Maksimum||Brak wymiarów|
|serverLoad3|Tak|Ładowanie serwera (fragmentu 3)|Procent|Maksimum||Brak wymiarów|
|serverLoad4|Tak|Ładowanie serwera (fragmentu 4)|Procent|Maksimum||Brak wymiarów|
|serverLoad5|Tak|Ładowanie serwera (fragmentu 5)|Procent|Maksimum||Brak wymiarów|
|serverLoad6|Tak|Ładowanie serwera (fragmentu 6)|Procent|Maksimum||Brak wymiarów|
|serverLoad7|Tak|Ładowanie serwera (fragmentu 7)|Procent|Maksimum||Brak wymiarów|
|serverLoad8|Tak|Ładowanie serwera (fragmentu 8)|Procent|Maksimum||Brak wymiarów|
|serverLoad9|Tak|Ładowanie serwera (fragmentu 9)|Procent|Maksimum||Brak wymiarów|
|SetCommands|Tak|Zestawy|Liczba|Łącznie||ShardId|
|setcommands0|Tak|Zestawy (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|setcommands1|Tak|Zestawy (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|setcommands2|Tak|Zestawy (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|setcommands3|Tak|Zestawy (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|setcommands4|Tak|Zestawy (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|setcommands5|Tak|Zestawy (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|setcommands6|Tak|Zestawy (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|setcommands7|Tak|Zestawy (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|setcommands8|Tak|Zestawy (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|setcommands9|Tak|Zestawy (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed|Tak|Łączna liczba operacji|Liczba|Łącznie||ShardId|
|totalcommandsprocessed0|Tak|Łączna liczba operacji (fragmentu 0)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed1|Tak|Łączna liczba operacji (fragmentu 1)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed2|Tak|Łączna liczba operacji (fragmentu 2)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed3|Tak|Łączna liczba operacji (fragmentu 3)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed4|Tak|Łączna liczba operacji (fragmentu 4)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed5|Tak|Łączna liczba operacji (fragmentu 5)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed6|Tak|Łączna liczba operacji (fragmentu 6)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed7|Tak|Łączna liczba operacji (fragmentu 7)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed8|Tak|Łączna liczba operacji (fragmentu 8)|Liczba|Łącznie||Brak wymiarów|
|totalcommandsprocessed9|Tak|Łączna liczba operacji (fragmentu 9)|Liczba|Łącznie||Brak wymiarów|
|totalkeys|Tak|Łączna liczba kluczy|Liczba|Maksimum||ShardId|
|totalkeys0|Tak|Łączna liczba kluczy (fragmentu 0)|Liczba|Maksimum||Brak wymiarów|
|totalkeys1|Tak|Łączna liczba kluczy (fragmentu 1)|Liczba|Maksimum||Brak wymiarów|
|totalkeys2|Tak|Łączna liczba kluczy (fragmentu 2)|Liczba|Maksimum||Brak wymiarów|
|totalkeys3|Tak|Łączna liczba kluczy (fragmentu 3)|Liczba|Maksimum||Brak wymiarów|
|totalkeys4|Tak|Łączna liczba kluczy (fragmentu 4)|Liczba|Maksimum||Brak wymiarów|
|totalkeys5|Tak|Łączna liczba kluczy (fragmentu 5)|Liczba|Maksimum||Brak wymiarów|
|totalkeys6|Tak|Łączna liczba kluczy (fragmentu 6)|Liczba|Maksimum||Brak wymiarów|
|totalkeys7|Tak|Łączna liczba kluczy (fragmentu 7)|Liczba|Maksimum||Brak wymiarów|
|totalkeys8|Tak|Łączna liczba kluczy (fragmentu 8)|Liczba|Maksimum||Brak wymiarów|
|totalkeys9|Tak|Łączna liczba kluczy (fragmentu 9)|Liczba|Maksimum||Brak wymiarów|
|usedmemory|Tak|Użyta pamięć|Bajty|Maksimum||ShardId|
|usedmemory0|Tak|Używana pamięć (fragmentu 0)|Bajty|Maksimum||Brak wymiarów|
|usedmemory1|Tak|Używana pamięć (fragmentu 1)|Bajty|Maksimum||Brak wymiarów|
|usedmemory2|Tak|Używana pamięć (fragmentu 2)|Bajty|Maksimum||Brak wymiarów|
|usedmemory3|Tak|Używana pamięć (fragmentu 3)|Bajty|Maksimum||Brak wymiarów|
|usedmemory4|Tak|Używana pamięć (fragmentu 4)|Bajty|Maksimum||Brak wymiarów|
|usedmemory5|Tak|Używana pamięć (fragmentu 5)|Bajty|Maksimum||Brak wymiarów|
|usedmemory6|Tak|Używana pamięć (fragmentu 6)|Bajty|Maksimum||Brak wymiarów|
|usedmemory7|Tak|Używana pamięć (fragmentu 7)|Bajty|Maksimum||Brak wymiarów|
|usedmemory8|Tak|Używana pamięć (fragmentu 8)|Bajty|Maksimum||Brak wymiarów|
|usedmemory9|Tak|Używana pamięć (fragmentu 9)|Bajty|Maksimum||Brak wymiarów|
|usedmemorypercentage|Tak|Procentowe użycie pamięci|Procent|Maksimum||ShardId|
|usedmemoryRss|Tak|Używana pamięć RSS|Bajty|Maksimum||ShardId|
|usedmemoryRss0|Tak|Używana pamięć RSS (fragmentu 0)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss1|Tak|Używana pamięć RSS (fragmentu 1)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss2|Tak|Używana pamięć RSS (fragmentu 2)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss3|Tak|Używana pamięć RSS (fragmentu 3)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss4|Tak|Używana pamięć RSS (fragmentu 4)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss5|Tak|Używana pamięć RSS (fragmentu 5)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss6|Tak|Używana pamięć RSS (fragmentu 6)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss7|Tak|Używana pamięć RSS (fragmentu 7)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss8|Tak|Używana pamięć RSS (fragmentu 8)|Bajty|Maksimum||Brak wymiarów|
|usedmemoryRss9|Tak|Używana pamięć RSS (fragmentu 9)|Bajty|Maksimum||Brak wymiarów|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Tak|Liczba żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/gniazda/role

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku/s|Nie|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|RoleInstanceId|
|Bajty zapisu dysku/s|Nie|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|RoleInstanceId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|RoleInstanceId|
|Sieć — wejście|Tak|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Tak|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku/s|Nie|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|Brak wymiarów|
|Bajty zapisu dysku/s|Nie|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|Brak wymiarów|
|Sieć — wejście|Tak|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|Brak wymiarów|
|Sieć — wyjście|Tak|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|Brak wymiarów|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|Brak wymiarów|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|UsedCapacity|Nie|Używana pojemność|Bajty|Średnia|Używana pojemność konta|Brak wymiarów|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|BlobCapacity|Nie|Pojemność obiektu BLOB|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Blob service konta magazynu w bajtach.|Blobtype, warstwa|
|BlobCount|Nie|Liczba obiektów BLOB|Liczba|Średnia|Liczba obiektów BLOB w Blob service konta magazynu.|Blobtype, warstwa|
|ContainerCount|Tak|Liczba kontenerów obiektów BLOB|Liczba|Średnia|Liczba kontenerów w Blob service konta magazynu.|Brak wymiarów|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|IndexCapacity|Nie|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez ADLS Gen2 (hierarchiczny) indeks w bajtach.|Brak wymiarów|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|FileCapacity|Nie|Pojemność plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Udział plików|
|FileCount|Nie|Liczba plików|Liczba|Średnia|Liczba plików w usłudze plików konta magazynu.|Udział plików|
|FileShareCount|Nie|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|Brak wymiarów|
|FileShareQuota|Nie|Rozmiar przydziału udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udział plików|
|FileShareSnapshotCount|Nie|Liczba migawek udziału plików|Liczba|Średnia|Liczba migawek znajdujących się w udziale w usłudze plików konta magazynu.|Udział plików|
|FileShareSnapshotSize|Nie|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udział plików|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, Authentication, przeudziale|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|QueueCapacity|Tak|Pojemność kolejki|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługa kolejki konta magazynu w bajtach.|Brak wymiarów|
|QueueCount|Tak|Liczba kolejek|Liczba|Średnia|Liczba kolejek w usługa kolejki konta magazynu.|Brak wymiarów|
|QueueMessageCount|Tak|Liczba komunikatów w kolejce|Liczba|Średnia|Przybliżona liczba komunikatów w kolejce w usługa kolejki konta magazynu.|Brak wymiarów|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|TableCapacity|Tak|Pojemność tabeli|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Table service konta magazynu w bajtach.|Brak wymiarów|
|TableCount|Tak|Liczba tabel|Liczba|Średnia|Liczba tabel w Table service konta magazynu.|Brak wymiarów|
|TableEntityCount|Tak|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli w Table service konta magazynu.|Brak wymiarów|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konta

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BlockedCalls|Tak|Zablokowane wywołania|Liczba|Łącznie|Liczba wywołań, które przekroczyły limit szybkości lub limitu przydziału.|ApiName, OperationName, region|
|CharactersTrained|Tak|Znaki przeszkolone|Liczba|Łącznie|Łączna liczba znaków przeszkolonych.|ApiName, OperationName, region|
|CharactersTranslated|Tak|Znaki tłumaczone|Liczba|Łącznie|Całkowita liczba znaków w żądaniu tekstu przychodzącego.|ApiName, OperationName, region|
|ClientErrors|Tak|Błędy klienta|Liczba|Łącznie|Liczba wywołań z błędem po stronie klienta (4xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|Dane|Tak|Dane w|Bajty|Łącznie|Rozmiar danych przychodzących w bajtach.|ApiName, OperationName, region|
|DataOut|Tak|Dane wychodzące|Bajty|Łącznie|Rozmiar danych wychodzących w bajtach.|ApiName, OperationName, region|
|Opóźnienie|Tak|Opóźnienie|)|Średnia|Opóźnienie w milisekundach.|ApiName, OperationName, region|
|ProcessedImages|Tak|Przetworzone obrazy|Liczba|Łącznie|Liczba transakcji do przetwarzania obrazów.|ApiName, FeatureName, UsageChannel, region|
|Błędy servererrors|Tak|Błędy serwera|Liczba|Łącznie|Liczba wywołań z błędem wewnętrznym usługi (5xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|SpeechSessionDuration|Tak|Czas trwania sesji mowy|Sekundy|Łącznie|Łączny czas trwania sesji mowy (w sekundach).|ApiName, OperationName, region|
|SuccessfulCalls|Tak|Pomyślne wywołania|Liczba|Łącznie|Liczba pomyślnych wywołań.|ApiName, OperationName, region|
|TotalCalls|Tak|Łączna liczba wywołań|Liczba|Łącznie|Łączna liczba wywołań.|ApiName, OperationName, region|
|TotalErrors|Tak|Całkowita liczba błędów|Liczba|Łącznie|Łączna liczba wywołań z odpowiedzią na błąd (kod odpowiedzi HTTP 4xx lub 5xx).|ApiName, OperationName, region|
|TotalTokenCalls|Tak|Łączna liczba wywołań tokenów|Liczba|Łącznie|Łączna liczba wywołań tokenów.|ApiName, OperationName, region|
|TotalTransactions|Tak|Łączna liczba transakcji|Liczba|Łącznie|Łączna liczba transakcji.|Brak wymiarów|


## <a name="microsoftcomputecloudservices"></a>Microsoft. COMPUTE/cloudServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|RoleInstanceId|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|RoleInstanceId|
|Bajty zapisu dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|RoleInstanceId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|RoleInstanceId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|RoleInstanceId|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Wykorzystane środki CPU|Tak|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak wymiarów|
|Pozostałe kredyty procesora CPU|Tak|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak wymiarów|
|Procent wykorzystania przepustowości dysku danych|Tak|Procent wykorzystania przepustowości dysku danych|Procent|Średnia|Procent zużywanej przepustowości dysku danych na minutę|TWORZONA|
|Procent zużytych operacji we/wy dysku danych|Tak|Procent zużytych operacji we/wy dysku danych|Procent|Średnia|Procent zajętego miejsca na dysku danych na minutę|TWORZONA|
|Głębokość kolejki dysku danych|Tak|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|TWORZONA|
|Bajty odczytu dysku danych/s|Tak|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|TWORZONA|
|Operacje odczytu z dysku danych/s|Tak|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Bajty zapisu na dysku danych/s|Tak|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje zapisu na dysku danych/s|Tak|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|Brak wymiarów|
|Bajty zapisu dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|Brak wymiarów|
|Przepływy przychodzące|Tak|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Tak|Maksymalny współczynnik tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Sieć — wejście|Tak|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|Brak wymiarów|
|Sieć łącznie|Tak|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak wymiarów|
|Sieć — wyjście|Tak|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|Brak wymiarów|
|Całkowita liczba sieci|Tak|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Brak wymiarów|
|Procent wykorzystania przepustowości dysku systemu operacyjnego|Tak|Procent wykorzystania przepustowości dysku systemu operacyjnego|Procent|Średnia|Procent zajętej przepustowości dysku systemu operacyjnego na minutę|TWORZONA|
|Procent zużytych operacji we/wy dysku systemu operacyjnego|Tak|Procent zużytych operacji we/wy dysku systemu operacyjnego|Procent|Średnia|Procent zajętych dysków systemu operacyjnego I/OS na minutę|TWORZONA|
|Głębokość kolejki dysku systemu operacyjnego|Tak|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Tak|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Głębokość kolejki systemu operacyjnego na dysku|Tak|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu na dysku systemu operacyjnego/s|Tak|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Przepływy wychodzące|Tak|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Tak|Maksymalny współczynnik tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Na dysk głębokość kolejki|Tak|Głębokość kolejki dysku danych (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu na dysk/s|Tak|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Tak|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Tak|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Brak wymiarów|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Tak|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Tak|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Tak|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Brak wymiarów|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Tak|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak wymiarów|
|Procent wykorzystania przepustowości w pamięci podręcznej maszyny wirtualnej|Tak|Procent wykorzystania przepustowości w pamięci podręcznej maszyny wirtualnej|Procent|Średnia|Procent przepustowości dysku w pamięci podręcznej zużywanej przez maszynę wirtualną|Brak wymiarów|
|Procent zużytych operacji wejścia/wyjścia w pamięci maszyny wirtualnej|Tak|Procent zużytych operacji wejścia/wyjścia w pamięci maszyny wirtualnej|Procent|Średnia|Procent liczby operacji we/wy na sekundę używanych przez maszynę wirtualną|Brak wymiarów|
|Procent wykorzystania przepustowości niebuforowanej przez maszynę wirtualną|Tak|Procent wykorzystania przepustowości niebuforowanej przez maszynę wirtualną|Procent|Średnia|Procent niebuforowanej przepustowości dysku zużywanej przez maszynę wirtualną|Brak wymiarów|
|Procent zużytych operacji we/wy pamięci podręcznej maszyny wirtualnej|Tak|Procent zużytych operacji we/wy pamięci podręcznej maszyny wirtualnej|Procent|Średnia|Procent niebuforowanych operacji we/wy dysku zużywanych przez maszynę wirtualną|Brak wymiarów|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Wykorzystane środki CPU|Tak|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak wymiarów|
|Pozostałe kredyty procesora CPU|Tak|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak wymiarów|
|Głębokość kolejki dysku danych|Tak|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostka LUN, VMName|
|Bajty odczytu dysku danych/s|Tak|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje odczytu z dysku danych/s|Tak|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Bajty zapisu na dysku danych/s|Tak|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje zapisu na dysku danych/s|Tak|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|VMName|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|VMName|
|Bajty zapisu dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|VMName|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|VMName|
|Przepływy przychodzące|Tak|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Tak|Maksymalny współczynnik tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|VMName|
|Sieć — wejście|Tak|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|VMName|
|Sieć łącznie|Tak|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Sieć — wyjście|Tak|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|VMName|
|Całkowita liczba sieci|Tak|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|
|Głębokość kolejki dysku systemu operacyjnego|Tak|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|VMName|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje odczytu z dysku systemu operacyjnego/s|Tak|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Bajty zapisu dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Głębokość kolejki systemu operacyjnego na dysku|Tak|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu na dysku systemu operacyjnego/s|Tak|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Przepływy wychodzące|Tak|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Tak|Maksymalny współczynnik tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|VMName|
|Na dysk głębokość kolejki|Tak|Głębokość kolejki dysku danych (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu na dysk/s|Tak|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Tak|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Tak|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|VMName|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Tak|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Tak|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Tak|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|VMName|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Tak|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Wykorzystane środki CPU|Tak|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak wymiarów|
|Pozostałe kredyty procesora CPU|Tak|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak wymiarów|
|Głębokość kolejki dysku danych|Tak|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|TWORZONA|
|Bajty odczytu dysku danych/s|Tak|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|TWORZONA|
|Operacje odczytu z dysku danych/s|Tak|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Bajty zapisu na dysku danych/s|Tak|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje zapisu na dysku danych/s|Tak|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|Brak wymiarów|
|Bajty zapisu dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|Brak wymiarów|
|Przepływy przychodzące|Tak|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Tak|Maksymalny współczynnik tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Brak wymiarów|
|Sieć — wejście|Tak|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|Brak wymiarów|
|Sieć łącznie|Tak|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak wymiarów|
|Sieć — wyjście|Tak|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|Brak wymiarów|
|Całkowita liczba sieci|Tak|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Brak wymiarów|
|Głębokość kolejki dysku systemu operacyjnego|Tak|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu z dysku systemu operacyjnego/s|Tak|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Głębokość kolejki systemu operacyjnego na dysku|Tak|Głębokość kolejki dysku systemu operacyjnego (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak wymiarów|
|Bajty odczytu dysku systemu operacyjnego/s|Tak|Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje odczytu na dysku systemu operacyjnego/s|Tak|Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Bajty zapisu na dysku systemu operacyjnego/s|Tak|Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Operacje zapisu na dysku systemu operacyjnego/s|Tak|Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak wymiarów|
|Przepływy wychodzące|Tak|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Tak|Maksymalny współczynnik tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Brak wymiarów|
|Na dysk głębokość kolejki|Tak|Głębokość kolejki dysku danych (przestarzałe)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu na dysk/s|Tak|Bajty odczytu dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Tak|Operacje odczytu z dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Tak|Bajty zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku danych/s (przestarzałe)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Brak wymiarów|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Tak|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Tak|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Procent|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Tak|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Brak wymiarów|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Tak|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Procent|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak wymiarów|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CpuUsage|Tak|Użycie procesora|Liczba|Średnia|Użycie procesora CPU we wszystkich rdzeniach w millicores.|containerName|
|MemoryUsage|Tak|Użycie pamięci|Bajty|Średnia|Całkowite użycie pamięci w bajcie.|containerName|
|NetworkBytesReceivedPerSecond|Tak|Bajty odebrane przez sieć na sekundę|Bajty|Średnia|Bajty odebrane przez sieć na sekundę.|Brak wymiarów|
|NetworkBytesTransmittedPerSecond|Tak|Bajty przesyłane przez sieć na sekundę|Bajty|Średnia|Bajty przesyłane przez sieć na sekundę.|Brak wymiarów|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/rejestry

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Tak|Nieznanej obiektu agentpool procesora CPU|Sekundy|Łącznie|Nieznanej obiektu agentpool czasu procesora (w sekundach)|Brak wymiarów|
|RunDuration|Tak|Czas trwania przebiegu|)|Łącznie|Czas trwania przebiegu w milisekundach|Brak wymiarów|
|SuccessfulPullCount|Tak|Pomyślna liczba ściągania|Liczba|Średnia|Liczba pomyślnych operacji ściągania obrazu|Brak wymiarów|
|SuccessfulPushCount|Tak|Liczba wypychanych zakończonych powodzeniem|Liczba|Średnia|Liczba pomyślnych operacji wypychania obrazu|Brak wymiarów|
|TotalPullCount|Tak|Łączna liczba ściągania|Liczba|Średnia|Całkowita liczba ściągania obrazów|Brak wymiarów|
|TotalPushCount|Tak|Łączna liczba wypychanych|Liczba|Średnia|Liczba wypchnięciów obrazów łącznie|Brak wymiarów|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Nie|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Liczba|Średnia|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Brak wymiarów|
|kube_node_status_allocatable_memory_bytes|Nie|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Bajty|Średnia|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Brak wymiarów|
|kube_node_status_condition|Nie|Stany różnych warunków węzła|Liczba|Średnia|Stany różnych warunków węzła|warunek, stan, status2, węzeł|
|kube_pod_status_phase|Nie|Liczba etapów według fazy|Liczba|Średnia|Liczba etapów według fazy|faza, przestrzeń nazw, pod|
|kube_pod_status_ready|Nie|Liczba zasobników w stanie gotowe|Liczba|Średnia|Liczba zasobników w stanie gotowe|Przestrzeń nazw, pod, warunek|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|FailedRequests|Tak|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Pobiera dostępne dzienniki dla niestandardowych dostawców zasobów|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Tak|Żądania pomyślne|Liczba|Łącznie|Pomyślne żądania wykonywane przez niestandardowego dostawcę|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AvailableCapacity|Tak|Dostępna pojemność|Bajty|Średnia|Dostępna pojemność w bajtach w okresie raportowania.|Brak wymiarów|
|BytesUploadedToCloud|Tak|Przekazane bajty w chmurze (urządzenie)|Bajty|Średnia|Całkowita liczba bajtów przekazana na platformę Azure z urządzenia w okresie raportowania.|Brak wymiarów|
|BytesUploadedToCloudPerShare|Tak|Przekazane bajty w chmurze (udział)|Bajty|Średnia|Całkowita liczba bajtów przekazanych do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|CloudReadThroughput|Tak|Przepływność pobierania w chmurze|BytesPerSecond|Średnia|Chmura pobiera przepływność na platformę Azure w okresie raportowania.|Brak wymiarów|
|CloudReadThroughputPerShare|Tak|Przepływność pobierania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność pobierania na platformę Azure z udziału w okresie raportowania.|Udostępnij|
|CloudUploadThroughput|Tak|Przepływność przekazywania w chmurze|BytesPerSecond|Średnia|W chmurze przekazano przepływność na platformę Azure w okresie raportowania.|Brak wymiarów|
|CloudUploadThroughputPerShare|Tak|Przepływność przekazywania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność przekazywania do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|HyperVMemoryUtilization|Tak|Obliczenia brzegowe — użycie pamięci|Procent|Średnia|Ilość pamięci RAM w użyciu|InstanceName|
|HyperVVirtualProcessorUtilization|Tak|Obliczenia brzegowe — procentowy procesor CPU|Procent|Średnia|Procent użycia procesora CPU|InstanceName|
|NICReadThroughput|Tak|Przepływność odczytu (Sieć)|BytesPerSecond|Średnia|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|NICWriteThroughput|Tak|Przepływność zapisu (Sieć)|BytesPerSecond|Średnia|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|Łączna pojemność|Tak|Całkowita pojemność|Bajty|Średnia|Całkowita pojemność|Brak wymiarów|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactors

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|FailedRuns|Tak|Nieudane uruchomienia|Liczba|Łącznie||potokname, ActivityName|
|SuccessfulRuns|Tak|Pomyślne uruchomienia|Liczba|Łącznie||potokname, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabryki

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Tak|Metryki uruchomień działań|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivityFailedRuns|Tak|Metryki uruchamiania działań zakończonych niepowodzeniem|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivitySucceededRuns|Tak|Metryki uruchamiania działań zakończonych powodzeniem|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|FactorySizeInGbUnits|Tak|Łączny rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak wymiarów|
|IntegrationRuntimeAvailableMemory|Tak|Dostępna pamięć środowiska Integration Runtime|Bajty|Średnia||IntegrationRuntimeName, nodename|
|IntegrationRuntimeAvailableNodeNumber|Tak|Liczba dostępnych węzłów Integration Runtime|Liczba|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Tak|Czas trwania kolejki Integration Runtime|Sekundy|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Tak|Użycie procesora Integration Runtime|Procent|Średnia||IntegrationRuntimeName, nodename|
|IntegrationRuntimeQueueLength|Tak|Długość kolejki środowiska Integration Runtime|Liczba|Średnia||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Tak|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak wymiarów|
|MaxAllowedResourceCount|Tak|Maksymalna dozwolona liczba jednostek|Liczba|Maksimum||Brak wymiarów|
|PipelineCancelledRuns|Tak|Metryki uruchomień potoku|Liczba|Łącznie||FailureType, nazwa|
|PipelineFailedRuns|Tak|Metryki uruchomionych potoków zakończonych niepowodzeniem|Liczba|Łącznie||FailureType, nazwa|
|PipelineSucceededRuns|Tak|Metryki uruchamiania potoków zakończonych powodzeniem|Liczba|Łącznie||FailureType, nazwa|
|ResourceCount|Tak|Łączna liczba jednostek|Liczba|Maksimum||Brak wymiarów|
|TriggerCancelledRuns|Tak|Anulowane metryki uruchamiania wyzwalacza|Liczba|Łącznie||Nazwa, Niepowodzenie|
|TriggerFailedRuns|Tak|Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem|Liczba|Łącznie||Nazwa, Niepowodzenie|
|TriggerSucceededRuns|Tak|Wyzwalacze uruchomienia wyzwalają metryki|Liczba|Łącznie||Nazwa, Niepowodzenie|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. kontach datalakestore/konta

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Odczyt DataReady|Tak|Odczytane dane|Bajty|Łącznie|Łączna ilość danych odczytanych z konta.|Brak wymiarów|
|Zapisywana|Tak|Zapisywane dane|Bajty|Łącznie|Całkowita ilość danych zapisywana na konto.|Brak wymiarów|
|ReadRequests|Tak|Żądania odczytu|Liczba|Łącznie|Liczba żądań odczytu danych do konta.|Brak wymiarów|
|TotalStorage|Tak|Łączny rozmiar magazynu|Bajty|Maksimum|Łączna ilość danych przechowywanych na koncie.|Brak wymiarów|
|WriteRequests|Tak|Żądania zapisu|Liczba|Łącznie|Liczba żądań zapisu danych na koncie.|Brak wymiarów|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/serwery

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|io_consumption_percent|Tak|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|seconds_behind_master|Tak|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak wymiarów|
|serverlog_storage_limit|Tak|Limit magazynowania dziennika serwera|Bajty|Średnia|Limit magazynowania dziennika serwera|Brak wymiarów|
|serverlog_storage_percent|Tak|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Tak|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|storage_limit|Tak|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/serwery

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|io_consumption_percent|Tak|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|seconds_behind_master|Tak|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak wymiarów|
|serverlog_storage_limit|Tak|Limit magazynowania dziennika serwera|Bajty|Maksimum|Limit magazynowania dziennika serwera|Brak wymiarów|
|serverlog_storage_percent|Tak|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Tak|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|storage_limit|Tak|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|connections_succeeded|Tak|Połączenia zakończone powodzeniem|Liczba|Łącznie|Połączenia zakończone powodzeniem|Brak wymiarów|
|cpu_credits_consumed|Tak|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów używanych przez serwer bazy danych|Brak wymiarów|
|cpu_credits_remaining|Tak|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|disk_queue_depth|Tak|Głębokość kolejki dysku|Liczba|Średnia|Liczba oczekujących operacji we/wy na dysk danych|Brak wymiarów|
|Wejścia|Tak|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak wymiarów|
|maximum_used_transactionIDs|Tak|Maksymalna liczba używanych identyfikatorów transakcji|Liczba|Średnia|Maksymalna liczba używanych identyfikatorów transakcji|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|read_iops|Tak|Odczyt operacji we/wy|Liczba|Średnia|Liczba operacji odczytu we/wy dysku danych na sekundę|Brak wymiarów|
|read_throughput|Tak|Bajty przepływności odczytu/s|Liczba|Średnia|Bajty odczytane na sekundę z dysku danych w okresie monitorowania|Brak wymiarów|
|storage_free|Tak|Wolne miejsce w magazynie|Bajty|Średnia|Wolne miejsce w magazynie|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|
|txlogs_storage_used|Tak|Używany magazyn dziennika transakcji|Bajty|Średnia|Używany magazyn dziennika transakcji|Brak wymiarów|
|write_iops|Tak|Zapisz operacje we/wy|Liczba|Średnia|Liczba operacji zapisu we/wy dysku danych na sekundę|Brak wymiarów|
|write_throughput|Tak|Bajty przepustowości zapisu/s|Liczba|Średnia|Bajty zapisywane na sekundę na dysku z danymi w okresie monitorowania|Brak wymiarów|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/serwery

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|backup_storage_used|Tak|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak wymiarów|
|connections_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|io_consumption_percent|Tak|Procent operacji we/wy|Procent|Średnia|Procent operacji we/wy|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|pg_replica_log_delay_in_bytes|Tak|Maksymalne opóźnienie między replikami|Bajty|Maksimum|Opóźnienie w bajtach najbardziej opóźnionej repliki|Brak wymiarów|
|pg_replica_log_delay_in_seconds|Tak|Zwłoka repliki|Sekundy|Maksimum|Opóźnienie repliki w sekundach|Brak wymiarów|
|serverlog_storage_limit|Tak|Limit magazynowania dziennika serwera|Bajty|Maksimum|Limit magazynowania dziennika serwera|Brak wymiarów|
|serverlog_storage_percent|Tak|Procent magazynu dzienników serwera|Procent|Średnia|Procent magazynu dzienników serwera|Brak wymiarów|
|serverlog_storage_usage|Tak|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak wymiarów|
|storage_limit|Tak|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_connections|Tak|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|Wejścia|Tak|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak wymiarów|
|memory_percent|Tak|Procent pamięci|Procent|Średnia|Procent pamięci|Brak wymiarów|
|network_bytes_egress|Tak|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak wymiarów|
|network_bytes_ingress|Tak|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak wymiarów|
|storage_percent|Tak|Procent magazynu|Procent|Średnia|Procent magazynu|Brak wymiarów|
|storage_used|Tak|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak wymiarów|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|C2D. Commands. wyjście. Abandon. Success|Tak|Porzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak wymiarów|
|C2D. Commands. wyjście. Complete. Success|Tak|Zakończono dostarczanie komunikatów C2D|Liczba|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Brak wymiarów|
|C2D. Commands. wyjście. Odrzuć. sukces|Tak|Odrzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak wymiarów|
|C2D. Methods. Failure|Tak|Nieudane wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Brak wymiarów|
|C2D. Methods. requestSize|Tak|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Brak wymiarów|
|C2D. Methods. responseSize|Tak|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|Brak wymiarów|
|C2D. Methods. Success|Tak|Pomyślne wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak wymiarów|
|C2D. splot. Read. Failure|Tak|Nieudane odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Read. size|Tak|Rozmiar odpowiedzi na odwrocie od zaplecza|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|Brak wymiarów|
|C2D. splot. Read. Success|Tak|Pomyślne odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Update. Failure|Tak|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Update. size|Tak|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|Brak wymiarów|
|C2D. splot. Update. Success|Tak|Pomyślne aktualizacje bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak wymiarów|
|C2DMessagesExpired|Tak|Wygasłe komunikaty C2D|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak wymiarów|
|komputerów|Tak|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracji|Brak wymiarów|
|connectedDeviceCount|Nie|Połączone urządzenia|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|Brak wymiarów|
|D2C. endpoints. wychodzące. wbudowane. Events|Tak|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing.|Brak wymiarów|
|D2C. endpoints. wychodzące. eventHubs|Tak|Routing: komunikaty dostarczane do centrum zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|Brak wymiarów|
|D2C. endpoints. wychodzące. serviceBusQueues|Tak|Routing: komunikaty dostarczone do kolejki Service Bus|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Brak wymiarów|
|D2C. endpoints. wychodzące. serviceBusTopics|Tak|Routing: komunikaty dostarczane do Service Bus tematu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Brak wymiarów|
|D2C. endpoints. ruch wychodzący. Storage|Tak|Routing: komunikaty dostarczane do magazynu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Brak wymiarów|
|D2C. endpoints. dane wyjściowe. Storage. blob|Tak|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Brak wymiarów|
|D2C. endpoints. ruch wychodzący. Storage. Bytes|Tak|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Brak wymiarów|
|D2C. endpoints. opóźnienie. wbudowane. Events|Tak|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia).|Brak wymiarów|
|D2C. endpoints. opóźnienie. eventHubs|Tak|Routing: opóźnienie komunikatu dla centrum zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|Brak wymiarów|
|D2C. endpoints. opóźnienie. serviceBusQueues|Tak|Routing: opóźnienie komunikatu dla kolejki Service Bus|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Brak wymiarów|
|D2C. endpoints. opóźnienie. serviceBusTopics|Tak|Routing: opóźnienie komunikatu dla Service Bus tematu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|Brak wymiarów|
|D2C. endpoints. opóźnienie. Storage|Tak|Routing: opóźnienie komunikatu dla magazynu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|Brak wymiarów|
|D2C. dane telemetryczne. wychodzące. upuszczone|Tak|Routing: porzucone komunikaty telemetryczne |Liczba|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Brak wymiarów|
|D2C. telemetrię. ruch wychodzący. Fallback|Tak|Routing: komunikaty dostarczane do powrotu|Liczba|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Brak wymiarów|
|D2C. Telemetria. ruch wychodzący. nieprawidłowe|Tak|Routing: komunikaty telemetryczne są niezgodne|Liczba|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak wymiarów|
|D2C. dane telemetryczne. wychodzące. oddzielone|Tak|Routing: oddzielone komunikaty telemetryczne |Liczba|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |Brak wymiarów|
|D2C. telemetrię. ruch wychodzący. sukces|Tak|Routing: dostarczono komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak wymiarów|
|D2C. telemetrię. allProtocol|Tak|Próby wysłania komunikatów telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Brak wymiarów|
|D2C. telemetrię. sendThrottle|Tak|Liczba błędów ograniczania|Liczba|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Brak wymiarów|
|D2C. telemetrię. dane wejściowe. sukces|Tak|Wysłane komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Brak wymiarów|
|D2C. splot. Read. Failure|Tak|Nieudane odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Brak wymiarów|
|D2C. splot. Read. size|Tak|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|Brak wymiarów|
|D2C. splot. Read. Success|Tak|Pomyślne odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Brak wymiarów|
|D2C. splot. Update. Failure|Tak|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Brak wymiarów|
|D2C. splot. Update. size|Tak|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|Brak wymiarów|
|D2C. splot. Update. Success|Tak|Pomyślne aktualizacje bliźniaczych urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Brak wymiarów|
|dailyMessageQuotaUsed|Tak|Całkowita liczba użytych komunikatów|Liczba|Maksimum|Łączna liczba użytych komunikatów|Brak wymiarów|
|deviceDataUsage|Tak|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak wymiarów|
|deviceDataUsageV2|Tak|Całkowite użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak wymiarów|
|Devices. connectedDevices. allProtocol|Tak|Podłączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Brak wymiarów|
|Devices. totalDevices|Tak|Łączna liczba urządzeń (przestarzałe)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak wymiarów|
|EventGridDeliveries|Tak|Dostawy Event Grid|Liczba|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia ( https://aka.ms/ioteventgrid) .|Wynik, typ zdarzenia|
|EventGridLatency|Tak|Opóźnienie Event Grid|)|Średnia|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|Typ zdarzenia|
|Jobs. cancelJob. Failure|Tak|Nieudane anulowania zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Brak wymiarów|
|Jobs. cancelJob. Success|Tak|Pomyślne anulowania zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Brak wymiarów|
|zadania. ukończone|Tak|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak wymiarów|
|Jobs. createDirectMethodJob. Failure|Tak|Nie można utworzyć zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak wymiarów|
|Jobs. createDirectMethodJob. Success|Tak|Pomyślne utworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak wymiarów|
|Jobs. createTwinUpdateJob. Failure|Tak|Nie można utworzyć dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Brak wymiarów|
|Jobs. createTwinUpdateJob. Success|Tak|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Brak wymiarów|
|zadania. Niepowodzenie|Tak|Zadania zakończone niepowodzeniem|Liczba|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Brak wymiarów|
|Jobs. listJobs. Failure|Tak|Wywołania zakończone niepowodzeniem do listy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Brak wymiarów|
|Jobs. listJobs. Success|Tak|Pomyślne wywołania do zadań na liście|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak wymiarów|
|Jobs. queryJobs. Failure|Tak|Nieudane kwerendy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Brak wymiarów|
|Jobs. queryJobs. Success|Tak|Pomyślne zapytania dotyczące zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Brak wymiarów|
|RoutingDataSizeInBytesDelivered|Tak|Rozmiar komunikatu dostarczania routingu w bajtach (wersja zapoznawcza)|Bajty|Łącznie|Łączny rozmiar w bajtach komunikatów dostarczonych przez Centrum IoT Hub do punktu końcowego. Można użyć wymiarów EndpointName i EndpointType, aby wyświetlić rozmiar komunikatów w bajtach dostarczonych do różnych punktów końcowych. Wartość metryki zwiększa się dla każdego dostarczonego komunikatu, w tym jeśli wiadomość jest dostarczana do wielu punktów końcowych lub jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Tak|Dostawy routingu (wersja zapoznawcza)|Liczba|Łącznie|Liczba prób dostarczenia komunikatów do wszystkich punktów końcowych przy użyciu routingu przez IoT Hub. Aby sprawdzić liczbę prób zakończonych powodzeniem lub nieudanych, użyj wymiaru wynik. Aby zobaczyć przyczynę niepowodzenia, na przykład nieprawidłowe, porzucone lub oddzielone, użyj wymiaru FailureReasonCategory. Można również użyć wymiarów EndpointName i EndpointType, aby zrozumieć, ile komunikatów zostało dostarczonych do różnych punktów końcowych. Wartość metryki zwiększa się o jeden dla każdej próby dostarczenia, w tym, jeśli wiadomość jest dostarczana do wielu punktów końcowych, lub jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Tak|Opóźnienie dostarczania routingu (wersja zapoznawcza)|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym. Można użyć wymiarów EndpointName i EndpointType, aby zrozumieć opóźnienia dla różnych punktów końcowych.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|Nie|Łączna liczba urządzeń|Liczba|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak wymiarów|
|twinQueries. Failure|Tak|Niepowodzenie zapytań bliźniaczych|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Brak wymiarów|
|twinQueries.resultSize|Tak|Rozmiar wyniku zapytań bliźniaczych|Bajty|Średnia|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Brak wymiarów|
|twinQueries. Success|Tak|Pomyślne zapytania bliźniaczy|Liczba|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Brak wymiarów|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AttestationAttempts|Tak|Próby zaświadczania|Liczba|Łącznie|Liczba podjętych prób zaświadczania urządzenia|ProvisioningServiceName, stan, protokół|
|DeviceAssignments|Tak|Przypisane urządzenia|Liczba|Łącznie|Liczba urządzeń przypisanych do centrum IoT Hub|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Tak|Próby rejestracji|Liczba|Łącznie|Liczba prób przeprowadzenia rejestracji urządzeń|ProvisioningServiceName, IotHubName, status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Addregion|Tak|Dodano region|Liczba|Liczba|Dodano region|Region|
|AutoscaleMaxThroughput|Nie|Maksymalna przepływność skalowania automatycznego|Liczba|Maksimum|Maksymalna przepływność skalowania automatycznego|DatabaseName, CollectionName|
|AvailableStorage|Nie|przestarzałe Dostępny magazyn|Bajty|Łącznie|"Dostępny magazyn" zostanie usunięty z Azure Monitor na koniec września 2023. Rozmiar magazynu kolekcji Cosmos DB jest teraz nieograniczony. Jedynym ograniczeniem jest to, że rozmiar magazynu dla każdego klucza partycji logicznej to 20 GB. Możesz włączyć PartitionKeyStatistics w dzienniku diagnostycznym, aby poznać użycie magazynu dla najważniejszych kluczy partycji. Aby uzyskać więcej informacji na temat przydziału magazynu Cosmos DB, zobacz ten dokument https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Po zaniechaniu pozostałe reguły alertów nadal zdefiniowane na zaniechanej metryce będą automatycznie wyłączać datę zakończenia.|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Nie|Zamknięcia połączeń Cassandra|Liczba|Łącznie|Liczba zamkniętych połączeń Cassandra, które zostały zgłoszone z dokładnością do 1 minuty|Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|Nie|Średni ReplicationLatency łącznika Cassandra|)|Średnia|Średni ReplicationLatency łącznika Cassandra|Brak wymiarów|
|CassandraConnectorReplicationHealthStatus|Nie|Stan kondycji replikacji łącznika Cassandra|Liczba|Liczba|Stan kondycji replikacji łącznika Cassandra|NotStarted, ReplicationInProgress, błąd|
|CassandraKeyspaceCreate|Nie|Utworzono przestrzeń kluczy Cassandra|Liczba|Liczba|Utworzono przestrzeń kluczy Cassandra|Source |
|CassandraKeyspaceDelete|Nie|Cassandra usunięto przestrzeń kluczy|Liczba|Liczba|Cassandra usunięto przestrzeń kluczy|Source |
|CassandraKeyspaceThroughputUpdate|Nie|Cassandra przepustowość obszaru kluczy|Liczba|Liczba|Cassandra przepustowość obszaru kluczy|Source |
|CassandraKeyspaceUpdate|Nie|Cassandra miejsce na dysku|Liczba|Liczba|Cassandra miejsce na dysku|Source |
|CassandraRequestCharges|Nie|Opłaty za żądania Cassandra|Liczba|Łącznie|Jednostek ru zużyte dla żądań Cassandra|DatabaseName, CollectionName, region, OperationType, ResourceType|
|CassandraRequests|Nie|Żądania Cassandra|Liczba|Liczba|Liczba wykonanych żądań Cassandra|DatabaseName, CollectionName, region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Nie|Utworzono tabelę Cassandra|Liczba|Liczba|Utworzono tabelę Cassandra|ResourceName, ChildResourceName, |
|CassandraTableDelete|Nie|Usunięto tabelę Cassandra|Liczba|Liczba|Usunięto tabelę Cassandra|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|Nie|Zaktualizowano przepływność tabeli Cassandra|Liczba|Liczba|Zaktualizowano przepływność tabeli Cassandra|ResourceName, ChildResourceName, |
|CassandraTableUpdate|Nie|Zaktualizowano tabelę Cassandra|Liczba|Liczba|Zaktualizowano tabelę Cassandra|ResourceName, ChildResourceName, |
|Konto|Tak|Utworzono konto|Liczba|Liczba|Utworzono konto|Brak wymiarów|
|Datausage|Nie|Użycie danych|Bajty|Łącznie|Całkowite użycie danych zgłoszone z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DeleteAccount|Tak|Konto zostało usunięte|Liczba|Liczba|Konto zostało usunięte|Brak wymiarów|
|DocumentCount|Nie|Liczba dokumentów|Liczba|Łącznie|Łączna liczba dokumentów raportowana z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Nie|Przydział dokumentu|Bajty|Łącznie|Łączny przydział magazynu zgłoszony z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|GremlinDatabaseCreate|Nie|Utworzono bazę danych Gremlin|Liczba|Liczba|Utworzono bazę danych Gremlin|Source |
|GremlinDatabaseDelete|Nie|Baza danych Gremlin została usunięta|Liczba|Liczba|Baza danych Gremlin została usunięta|Source |
|GremlinDatabaseThroughputUpdate|Nie|Zaktualizowano przepływność bazy danych Gremlin|Liczba|Liczba|Zaktualizowano przepływność bazy danych Gremlin|Source |
|GremlinDatabaseUpdate|Nie|Baza danych Gremlin została zaktualizowana|Liczba|Liczba|Baza danych Gremlin została zaktualizowana|Source |
|GremlinGraphCreate|Nie|Utworzono Graf Gremlin|Liczba|Liczba|Utworzono Graf Gremlin|ResourceName, ChildResourceName, |
|GremlinGraphDelete|Nie|Wykres Gremlin został usunięty|Liczba|Liczba|Wykres Gremlin został usunięty|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|Nie|Zaktualizowano przepływność grafu Gremlin|Liczba|Liczba|Zaktualizowano przepływność grafu Gremlin|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|Nie|Zaktualizowano Graf Gremlin|Liczba|Liczba|Zaktualizowano Graf Gremlin|ResourceName, ChildResourceName, |
|IndexUsage|Nie|Użycie indeksu|Bajty|Łącznie|Całkowite użycie indeksów zgłoszone na 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Nie|Żądania metadanych|Liczba|Liczba|Liczba żądań metadanych. Cosmos DB przechowuje zbieranie metadanych systemu dla każdego konta, które pozwala na Wyliczanie kolekcji, baz danych itp. i ich konfiguracji bez opłat.|DatabaseName, CollectionName, region, StatusCode, |
|MongoCollectionCreate|Nie|Utworzono kolekcję Mongo|Liczba|Liczba|Utworzono kolekcję Mongo|ResourceName, ChildResourceName, |
|MongoCollectionDelete|Nie|Kolekcja Mongo została usunięta|Liczba|Liczba|Kolekcja Mongo została usunięta|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|Nie|Zaktualizowano przepływność kolekcji Mongo|Liczba|Liczba|Zaktualizowano przepływność kolekcji Mongo|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|Nie|Aktualizacja kolekcji Mongo|Liczba|Liczba|Aktualizacja kolekcji Mongo|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|Nie|Baza danych Mongo została usunięta|Liczba|Liczba|Baza danych Mongo została usunięta|Source |
|MongoDatabaseThroughputUpdate|Nie|Zaktualizowano przepływność bazy danych Mongo|Liczba|Liczba|Zaktualizowano przepływność bazy danych Mongo|Source |
|MongoDBDatabaseCreate|Nie|Utworzono bazę danych Mongo|Liczba|Liczba|Utworzono bazę danych Mongo|Source |
|MongoDBDatabaseUpdate|Nie|Baza danych Mongo została zaktualizowana|Liczba|Liczba|Baza danych Mongo została zaktualizowana|Source |
|MongoRequests|Tak|Żądania Mongo|Liczba|Liczba|Liczba wykonanych żądań Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode, status|
|NormalizedRUConsumption|Nie|Znormalizowane użycie RU|Procent|Maksimum|Maksymalna wartość procentowa zużycia RU na minutę|CollectionName, DatabaseName, region, PartitionKeyRangeId|
|ProvisionedThroughput|Nie|Aprowizowana przepływność|Liczba|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|RegionFailover|Tak|Region w trybie failover|Liczba|Liczba|Region w trybie failover|Brak wymiarów|
|RemoveRegion|Tak|Usunięto region|Liczba|Liczba|Usunięto region|Region|
|ReplicationLatency|Tak|Opóźnienie replikacji poziomie P99|)|Średnia|Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną|SourceRegion, TargetRegion|
|ServerSideLatency|Nie|Opóźnienie po stronie serwera|)|Średnia|Opóźnienie po stronie serwera|DatabaseName, CollectionName, region, Connectionmode, OperationType, PublicAPIType|
|Dostępność|Nie|Dostępność usługi|Procent|Średnia|Dostępność żądania konta o jednej godzinie, dniu lub o dokładności|Brak wymiarów|
|SqlContainerCreate|Nie|Utworzono kontener SQL|Liczba|Liczba|Utworzono kontener SQL|ResourceName, ChildResourceName, |
|SqlContainerDelete|Nie|Usunięto kontener SQL|Liczba|Liczba|Usunięto kontener SQL|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|Nie|Zaktualizowano przepływność kontenera SQL|Liczba|Liczba|Zaktualizowano przepływność kontenera SQL|ResourceName, ChildResourceName, |
|SqlContainerUpdate|Nie|Zaktualizowano kontener SQL|Liczba|Liczba|Zaktualizowano kontener SQL|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|Nie|Utworzono bazę danych SQL|Liczba|Liczba|Utworzono bazę danych SQL|Source |
|SqlDatabaseDelete|Nie|Baza danych SQL została usunięta|Liczba|Liczba|Baza danych SQL została usunięta|Source |
|SqlDatabaseThroughputUpdate|Nie|Zaktualizowano przepływność bazy danych SQL|Liczba|Liczba|Zaktualizowano przepływność bazy danych SQL|Source |
|SqlDatabaseUpdate|Nie|Zaktualizowano bazę danych SQL|Liczba|Liczba|Zaktualizowano bazę danych SQL|Source |
|TableTableCreate|Nie|Utworzono tabelę Azure|Liczba|Liczba|Utworzono tabelę Azure|Source |
|TableTableDelete|Nie|Usunięto tabelę platformy Azure|Liczba|Liczba|Usunięto tabelę platformy Azure|Source |
|TableTableThroughputUpdate|Nie|Zaktualizowano przepływność tabeli usługi Azure Table|Liczba|Liczba|Zaktualizowano przepływność tabeli usługi Azure Table|Source |
|TableTableUpdate|Nie|Zaktualizowano tabelę platformy Azure|Liczba|Liczba|Zaktualizowano tabelę platformy Azure|Source |
|TotalRequests|Tak|Łączna liczba żądań|Liczba|Liczba|Liczba wykonanych żądań|DatabaseName, CollectionName, region, StatusCode, OperationType, status|
|TotalRequestUnits|Tak|Łączna liczba jednostek żądania|Liczba|Łącznie|Wykorzystane jednostki żądania|DatabaseName, CollectionName, region, StatusCode, OperationType, status|
|UpdateAccountKeys|Tak|Zaktualizowano klucze konta|Liczba|Liczba|Zaktualizowano klucze konta|KeyType|
|UpdateAccountNetworkSettings|Tak|Zaktualizowano ustawienia sieci konta|Liczba|Liczba|Zaktualizowano ustawienia sieci konta|Brak wymiarów|
|UpdateAccountReplicationSettings|Tak|Zaktualizowano ustawienia replikacji konta|Liczba|Liczba|Zaktualizowano ustawienia replikacji konta|Brak wymiarów|
|UpdateDiagnosticsSettings|Nie|Zaktualizowano ustawienia diagnostyczne konta|Liczba|Liczba|Zaktualizowano ustawienia diagnostyczne konta|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domeny

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Temat, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|Temat, Błądtype, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Temat|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Błąd, Błądtype|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Brak wymiarów|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Brak wymiarów|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Brak wymiarów|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|EventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Tak|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Nie|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Tak|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Nie|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Tak|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason, EventSubscriptionName|
|MatchedEventCount|Tak|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|EventSubscriptionName|
|PublishFailCount|Tak|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|PublishSuccessCount|Tak|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak wymiarów|
|PublishSuccessLatencyInMs|Tak|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak wymiarów|
|UnmatchedEventCount|Tak|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak wymiarów|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/klastry

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|Brak wymiarów|
|AvailableMemory|Nie|Dostępna pamięć|Procent|Maksimum|Dostępna pamięć dla klastra centrum zdarzeń jako procent całkowitej ilości pamięci.|Rola|
|CaptureBacklog|Nie|Zaległości przechwytywania.|Liczba|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|Brak wymiarów|
|CapturedBytes|Nie|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|Brak wymiarów|
|CapturedMessages|Nie|Przechwycone komunikaty.|Liczba|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|Brak wymiarów|
|Połączenia connectionsclosed|Nie|Zamknięte połączenia.|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub.|Brak wymiarów|
|Połączenia connectionsopened|Nie|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub.|Brak wymiarów|
|Procesor CPU|Nie|Procesor CPU|Procent|Maksimum|Użycie procesora CPU przez klaster centrum zdarzeń jako wartość procentowa|Rola|
|Bajty incomingbytes|Tak|Bajty przychodzące.|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|Brak wymiarów|
|Komunikaty incomingmessages|Tak|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|Brak wymiarów|
|Żądania incomingrequests|Tak|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|Brak wymiarów|
|Bajty outgoingbytes|Tak|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|Brak wymiarów|
|Komunikaty outgoingmessages|Tak|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|Brak wymiarów|
|Błędy quotaexceedederrors|Nie|Błędy przekroczenia limitu przydziału.|Liczba|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|Brak wymiarów|
|Błędy servererrors|Nie|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|Brak wymiarów|
|Rozmiar|Nie|Rozmiar|Bajty|Średnia|Rozmiar centrum EventHub w bajtach.|Rola|
|Żądania successfulrequests|Nie|Żądania pomyślne|Liczba|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|Brak wymiarów|
|ThrottledRequests|Nie|Żądania ograniczone.|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|Brak wymiarów|
|Błędy usererrors|Nie|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|Brak wymiarów|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/przestrzenie nazw

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|Brak wymiarów|
|CaptureBacklog|Nie|Zaległości przechwytywania.|Liczba|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|EntityName|
|CapturedBytes|Nie|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|EntityName|
|CapturedMessages|Nie|Przechwycone komunikaty.|Liczba|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|EntityName|
|Połączenia connectionsclosed|Nie|Zamknięte połączenia.|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub.|EntityName|
|Połączenia connectionsopened|Nie|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub.|EntityName|
|EHABL|Tak|Archiwum komunikatów zaległości (przestarzałe)|Liczba|Łącznie|Komunikaty archiwum centrum zdarzeń w zaległości dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHAMBS|Tak|Przepływność komunikatów archiwalnych (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów archiwalnych centrum zdarzeń w przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHAMSGS|Tak|Komunikaty archiwalne (przestarzałe)|Liczba|Łącznie|Zarchiwizowane komunikaty centrum zdarzeń w przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHINBYTES|Tak|Przychodzące bajty (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHINMBS|Tak|Przychodzące bajty (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów przychodzących (przestarzałe)|Brak wymiarów|
|EHINMSGS|Tak|Komunikaty przychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHOUTBYTES|Tak|Bajty wychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|EHOUTMBS|Tak|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów wychodzących (przestarzałe)|Brak wymiarów|
|EHOUTMSGS|Tak|Wiadomości wychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|FAILREQ|Tak|Nieudane żądania (przestarzałe)|Liczba|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|Bajty incomingbytes|Tak|Bajty przychodzące.|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty incomingmessages|Tak|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Żądania incomingrequests|Tak|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|EntityName|
|INMSGS|Tak|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów przychodzących (przestarzałe)|Brak wymiarów|
|INREQS|Tak|Żądania przychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba żądań wysłania przychodzącego dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|MIĘDZY|Tak|Wewnętrzne błędy serwera (przestarzałe)|Liczba|Łącznie|Łączna liczba błędów wewnętrznego serwera dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|MISCERR|Tak|Inne błędy (przestarzałe)|Liczba|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|Bajty outgoingbytes|Tak|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty outgoingmessages|Tak|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|OUTMSGS|Tak|Wiadomości wychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów wychodzących (przestarzałe)|Brak wymiarów|
|Błędy quotaexceedederrors|Nie|Błędy przekroczenia limitu przydziału.|Liczba|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|EntityName |
|Błędy servererrors|Nie|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|EntityName |
|Rozmiar|Nie|Rozmiar|Bajty|Średnia|Rozmiar centrum EventHub w bajtach.|EntityName|
|Żądania successfulrequests|Nie|Żądania pomyślne|Liczba|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|EntityName |
|SUCCREQ|Tak|Żądania zakończone powodzeniem (przestarzałe)|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|SVRBSY|Tak|Błędy zajęte przez serwer (przestarzałe)|Liczba|Łącznie|Łączna liczba błędów zajętości serwera dla przestrzeni nazw (przestarzałe)|Brak wymiarów|
|ThrottledRequests|Nie|Żądania ograniczone.|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|EntityName |
|Błędy usererrors|Nie|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|EntityName |


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/Klastry

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Tak|Skategoryzowane żądania bramy|Liczba|Łącznie|Liczba żądań bramy według kategorii (1XX/2xx/3xx/4xx/5xx)|Wartości httpStatus|
|GatewayRequests|Tak|Żądania bramy|Liczba|Łącznie|Liczba żądań bramy|Wartości httpStatus|
|NumActiveWorkers|Tak|Liczba aktywnych procesów roboczych|Liczba|Maksimum|Liczba aktywnych procesów roboczych|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|MetricThreshold|Tak|Próg metryki|Liczba|Średnia|Skonfigurowany próg automatycznego skalowania podczas uruchamiania automatycznego skalowania.|MetricTriggerRule|
|ObservedCapacity|Tak|Zaobserwowana pojemność|Liczba|Średnia|Pojemność zgłoszona do automatycznego skalowania podczas jego wykonywania.|Brak wymiarów|
|ObservedMetricValue|Tak|Obserwowana wartość metryki|Liczba|Średnia|Wartość obliczona przez automatyczne skalowanie podczas wykonywania|MetricTriggerSource|
|ScaleActionsInitiated|Tak|Zainicjowane akcje skalowania|Liczba|Łącznie|Kierunek operacji skalowania.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Tak|Dostępność|Procent|Średnia|Procent pomyślnie ukończonych testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja|
|availabilityResults/liczba|Nie|Testy dostępności|Liczba|Liczba|Liczba testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|availabilityResults/czas trwania|Tak|Czas trwania testu dostępności|)|Średnia|Czas trwania testu dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|browserTimings/networkDuration|Tak|Czas połączenia sieciowego ładowania strony|)|Średnia|Czas między żądaniem użytkownika a połączeniem sieciowym. Obejmuje wyszukiwanie DNS i połączenie transportowe.|Brak wymiarów|
|browserTimings/processingDuration|Tak|Czas przetwarzania klienta|)|Średnia|Czas między odebraniem ostatniego bajtu dokumentu do momentu załadowania modelu DOM. Żądania asynchroniczne nadal mogą być przetwarzane.|Brak wymiarów|
|browserTimings/receiveDuration|Tak|Czas odpowiedzi na odebranie|)|Średnia|Czas między pierwszym i ostatnim bajtem lub do momentu odłączenia.|Brak wymiarów|
|browserTimings/sendDuration|Tak|Czas żądania wysłania|)|Średnia|Czas między połączeniem sieciowym i odebraniem pierwszego bajtu.|Brak wymiarów|
|browserTimings/totalDuration|Tak|Czas ładowania strony w przeglądarce|)|Średnia|Czas od żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów.|Brak wymiarów|
|zależności/liczba|Nie|Wywołania zależności|Liczba|Liczba|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, zależność/resultCode, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/czas trwania|Tak|Czas trwania zależności|)|Średnia|Czas trwania wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, zależność/resultCode, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/niepowodzenie|Nie|Błędy wywołań zależności|Liczba|Liczba|Liczba wywołań zależności zakończonych niepowodzeniem wykonanych przez aplikację w zasobach zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/cel, zależność/resultCode, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|wyjątki/przeglądarka|Nie|Wyjątki przeglądarki|Liczba|Liczba|Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce.|Chmura/rolename|
|wyjątki/liczba|Tak|Wyjątki|Liczba|Liczba|Łączna liczba wszystkich nieprzechwyconych wyjątków.|Chmura/rolename, Cloud/roleInstance, klient/typ|
|wyjątki/serwer|Nie|Wyjątki serwera|Liczba|Liczba|Liczba nieprzechwyconych wyjątków zgłoszonych w aplikacji serwera.|Chmura/rolename, Cloud/roleInstance|
|pageViews/liczba|Tak|Wyświetlenia stron|Liczba|Liczba|Liczba wyświetleń stron.|Operacja/syntetyczne, Chmura/rolename|
|pageViews/czas trwania|Tak|Czas ładowania widoku strony|)|Średnia|Czas ładowania widoku strony|Operacja/syntetyczne, Chmura/rolename|
|Liczniki wydajności/exceptionsPerSecond|Tak|Częstotliwość wyjątków|CountPerSecond|Średnia|Liczba obsłużonych i nieobsłużonych wyjątków zgłoszonych w systemie Windows, łącznie z wyjątkami platformy .NET i niezarządzanymi wyjątkami przekonwertowanymi na wyjątki platformy .NET.|Chmura/roleInstance|
|Liczniki wydajności/memoryAvailableBytes|Tak|Dostępna pamięć|Bajty|Średnia|Pamięć fizyczna natychmiast dostępna do przydzielenia do procesu lub do użycia przez system.|Chmura/roleInstance|
|Liczniki wydajności/processCpuPercentage|Tak|Procesor CPU procesu|Procent|Średnia|Wyrażony w procentach czas, przez jaki wszystkie wątki procesów używały procesora do wykonywania instrukcji. Może się to różnić od od 0 do 100. Ta Metryka wskazuje na wydajność samego procesu w3wp.|Chmura/roleInstance|
|Liczniki wydajności/processIOBytesPerSecond|Tak|Szybkość operacji we/wy procesu|BytesPerSecond|Średnia|Łączna liczba bajtów odczytanych i zapisywana w plikach, sieci i urządzeniach.|Chmura/roleInstance|
|Liczniki wydajności/processorCpuPercentage|Tak|Czas procesora|Procent|Średnia|Procent czasu spędzanego przez procesor w wątkach, które nie są bezczynne.|Chmura/roleInstance|
|Liczniki wydajności/processPrivateBytes|Tak|Prywatne bajty procesu|Bajty|Średnia|Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji.|Chmura/roleInstance|
|Liczniki wydajności/requestExecutionTime|Tak|Czas wykonywania żądania HTTP|)|Średnia|Czas wykonywania najnowszego żądania.|Chmura/roleInstance|
|Liczniki wydajności/requestsInQueue|Tak|Żądania HTTP w kolejce aplikacji|Liczba|Średnia|Długość kolejki żądań aplikacji.|Chmura/roleInstance|
|Liczniki wydajności/requestsPerSecond|Tak|Częstotliwość żądań HTTP|CountPerSecond|Średnia|Szybkość wszystkich żądań wysyłanych do aplikacji w ciągu sekundy od ASP.NET.|Chmura/roleInstance|
|Liczba żądań na sekundę|Nie|Żądania serwera|Liczba|Liczba|Liczba ukończonych żądań HTTP.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|żądania/czas trwania|Tak|Czas odpowiedzi serwera|)|Średnia|Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|żądania/niepowodzenie|Nie|Żądania zakończone niepowodzeniem|Liczba|Liczba|Liczba żądań HTTP oznaczonych jako zakończone niepowodzeniem. W większości przypadków są to żądania z kodem odpowiedzi >= 400, a nie równe 401.|żądania/performanceBucket, Request/resultCode, Operation/syntetyczne, Cloud/roleInstance, Cloud/rolename|
|żądania/częstotliwość|Nie|Liczba żądań serwera|CountPerSecond|Średnia|Liczba żądań serwera na sekundę|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|ślady/liczba|Tak|Ślady|Liczba|Liczba|Liczba dokumentów śledzenia|Trace/severityLevel, Operations/syntetyczne, Cloud/rolename, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|C2D. Property. Read. Failure|Tak|Niepowodzenie odczytywania właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich odczytów właściwości zakończonych niepowodzeniem inicjowanych z IoT Central|Brak wymiarów|
|C2D. Property. Read. Success|Tak|Pomyślne odczytywanie właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości inicjowanych z IoT Central|Brak wymiarów|
|C2D. Property. Update. Failure|Tak|Nieudane aktualizacje właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości zakończonych niepowodzeniem zainicjowanych z IoT Central|Brak wymiarów|
|C2D. Property. Update. Success|Tak|Pomyślne aktualizacje właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z IoT Central|Brak wymiarów|
|connectedDeviceCount|Nie|Łączna liczba połączonych urządzeń|Liczba|Średnia|Liczba urządzeń podłączonych do IoT Central|Brak wymiarów|
|D2C. Property. Read. Failure|Tak|Niepowodzenie odczytywania właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich odczytów właściwości zakończonych niepowodzeniem zainicjowanych z urządzeń|Brak wymiarów|
|D2C. Property. Read. Success|Tak|Pomyślne odczyty właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości zainicjowanych z urządzeń|Brak wymiarów|
|D2C. Property. Update. Failure|Tak|Nieudane aktualizacje właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości zakończonych niepowodzeniem zainicjowanych z urządzeń|Brak wymiarów|
|D2C. Property. Update. Success|Tak|Pomyślne aktualizacje właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z urządzeń|Brak wymiarów|


## <a name="microsoftkeyvaultvaults"></a>Microsoft./magazyny kluczy

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Ogólna dostępność magazynu|Procent|Średnia|Dostępność żądań magazynu|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Nie|Ogólne nasycenie magazynu|Procent|Średnia|Używane pojemność magazynu|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Tak|Całkowita liczba trafień interfejsu API usługi|Liczba|Liczba|Łączna liczba trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Tak|Ogólne opóźnienie interfejsu API usługi|)|Średnia|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Tak|Łączna liczba wyników interfejsu API usługi|Liczba|Liczba|Łączna liczba wyników interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/klastry

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BatchBlobCount|Tak|Liczba obiektów BLOB partii|Liczba|Średnia|Liczba źródeł danych w zagregowanej partii na potrzeby pozyskiwania.|Baza danych|
|BatchDuration|Tak|Czas trwania partii|Sekundy|Średnia|Czas trwania fazy agregacji w przepływie pozyskiwania.|Baza danych|
|BatchesProcessed|Tak|Przetworzone partie|Liczba|Średnia|Liczba partii zagregowanych na potrzeby pozyskiwania. Typ wsadu: czy partia osiągnęła czas wsadowy, rozmiar danych lub limit liczby plików ustawiony przez zasady wsadowe|Baza danych, SealReason|
|BatchSize|Tak|Rozmiar wsadu|Bajty|Średnia|Nieskompresowany oczekiwany rozmiar danych w zagregowanej partii na potrzeby pozyskiwania.|Baza danych|
|BlobsProcessed|Tak|Przetworzone obiekty blob|Liczba|Średnia|Liczba obiektów BLOB przetworzonych przez składnik.|Baza danych, Składniktype, ElementName|
|BlobsReceived|Tak|Odebrane obiekty blob|Liczba|Średnia|Liczba obiektów BLOB odebranych ze strumienia wejściowego przez składnik.|Baza danych, Składniktype, ElementName|
|BlobsRejected|Tak|Odrzucone obiekty blob|Liczba|Średnia|Liczba obiektów BLOB trwale odrzuconych przez składnik.|Baza danych, Składniktype, ElementName|
|CacheUtilization|Tak|Użycie pamięci podręcznej|Procent|Średnia|Poziom użycia w zakresie klastra|Brak wymiarów|
|ContinuousExportMaxLatenessMinutes|Tak|Maksymalna liczba opóźnień eksportu ciągłego|Liczba|Maksimum|Opóźnienie (w minutach) raportowane przez zadania eksportu ciągłego w klastrze|Brak wymiarów|
|ContinuousExportNumOfRecordsExported|Tak|Eksport ciągły — liczba eksportowanych rekordów|Liczba|Łącznie|Liczba wyeksportowanych rekordów, które są generowane dla każdego artefaktu magazynu zapisanego podczas operacji eksportowania|ContinuousExportName, baza danych|
|ContinuousExportPendingCount|Tak|Liczba oczekujących eksportu ciągłego|Liczba|Maksimum|Liczba oczekujących zadań eksportu ciągłego gotowych do wykonania|Brak wymiarów|
|ContinuousExportResult|Tak|Wynik eksportu ciągłego|Liczba|Liczba|Wskazuje, czy ciągły eksport zakończył się powodzeniem, czy niepowodzeniem|ContinuousExportName, wynik, baza danych|
|Procesor CPU|Tak|Procesor CPU|Procent|Średnia|Poziom użycia procesora CPU|Brak wymiarów|
|CumulativeLatency|Tak|Skumulowane opóźnienie|Sekundy|Średnia|Łączny czas od momentu, gdy komunikat zostanie odnaleziony, dopóki nie zostanie odebrany przez składnik raportowania do przetworzenia (czas odnajdywania jest ustawiany, gdy wiadomość zostanie przewidziana dla kolejki pozyskiwania lub wykryta przez połączenie danych).|Baza danych, Składniktype|
|DiscoveryLatency|Tak|Opóźnienie odnajdywania|Sekundy|Średnia|Zgłoszone przez połączenia danych (jeśli istnieją). Czas w sekundach od momentu, gdy komunikat jest w kolejce lub zdarzenie jest tworzone, dopóki nie zostanie odnalezione przez połączenie danych. Ten czas nie jest uwzględniony w łącznym czasie pozyskiwania Eksplorator danych platformy Azure.|ElementType, ComponentName|
|EventsProcessedForEventHubs|Tak|Zdarzenia przetwarzane (dla centrów zdarzeń/IoT)|Liczba|Łącznie|Liczba zdarzeń przetwarzanych przez klaster podczas pozyskiwania ze zdarzenia/IoT Hub|EventStatus|
|ExportUtilization|Tak|Wykorzystanie eksportu|Procent|Maksimum|Użycie eksportu|Brak wymiarów|
|IngestionLatencyInSeconds|Tak|Opóźnienie pozyskiwania|Sekundy|Średnia|Czas pozyskiwania ze źródła (np. komunikat jest w centrum EventHub) do klastra w ciągu kilku sekund|Brak wymiarów|
|IngestionResult|Tak|Wynik pozyskiwania|Liczba|Liczba|Liczba operacji pozyskiwania|IngestionResultDetails|
|IngestionUtilization|Tak|Wykorzystanie pozyskiwania|Procent|Średnia|Współczynnik używania miejsc pozyskiwania w klastrze|Brak wymiarów|
|IngestionVolumeInMB|Tak|Wolumin pozyskiwania (w MB)|Bajty|Łącznie|Ogólna ilość danych pozyskiwanych w klastrze (w MB)|Brak wymiarów|
|InstanceCount|Tak|Liczba wystąpień|Liczba|Średnia|Łączna liczba wystąpień|Brak wymiarów|
|Utrzymywani|Tak|Utrzymywanie aktywności|Liczba|Średnia|Sprawdzenie Sanity wskazuje, że klaster reaguje na zapytania|Brak wymiarów|
|MaterializedViewAgeMinutes|Tak|Wiek widoku z materiałami|Liczba|Średnia|Wiek widoku z materiałami w minutach|Baza danych, MaterializedViewName|
|MaterializedViewDataLoss|Tak|Niematerialna utrata danych widoku|Liczba|Maksimum|Wskazuje potencjalną utratę danych w widoku z materiałami|Baza danych, MaterializedViewName, rodzaj|
|MaterializedViewExtentsRebuild|Tak|Ponowne kompilowanie zakresów widoku materiałów|Liczba|Średnia|Liczba ponownych kompilacji zakresów|Baza danych, MaterializedViewName|
|MaterializedViewHealth|Tak|Kondycja widoku z materiałami|Liczba|Średnia|Kondycja widoku z materiałami (1 w dobrej kondycji, 0 w przypadku braku kondycji)|Baza danych, MaterializedViewName|
|MaterializedViewRecordsInDelta|Tak|W przypadku różnicowych rekordów widoku|Liczba|Średnia|Liczba rekordów w niemateriałowej części widoku|Baza danych, MaterializedViewName|
|MaterializedViewResult|Tak|Wynikowy widok z materiałami|Liczba|Średnia|Wynik procesu materializację|Baza danych, MaterializedViewName, wynik|
|QueryDuration|Tak|Czas trwania zapytania|)|Średnia|Czas trwania zapytań (w sekundach)|QueryStatus|
|QueryResult|Nie|Wynik zapytania|Liczba|Liczba|Łączna liczba zapytań.|Stan|
|SteamingIngestRequestRate|Tak|Pozyskiwanie za pomocą przesyłania strumieniowego — liczba żądań|Liczba|RateRequestsPerSecond|Szybkość żądania pozyskiwania strumieniowego (żądania na sekundę)|Brak wymiarów|
|StreamingIngestDataRate|Tak|Pozyskiwanie za pomocą przesyłania strumieniowego — szybkość danych|Liczba|Średnia|Szybkość danych pozyskiwania przesyłania strumieniowego (MB na sekundę)|Brak wymiarów|
|StreamingIngestDuration|Tak|Pozyskiwanie za pomocą przesyłania strumieniowego — czas trwania|)|Średnia|Czas trwania pozyskiwania strumieniowego w milisekundach|Brak wymiarów|
|StreamingIngestResults|Tak|Wynik pozyskiwania za pomocą przesyłania strumieniowego|Liczba|Średnia|Wynik pozyskiwania strumieniowego|Wynik|
|TotalNumberOfConcurrentQueries|Tak|Łączna liczba współbieżnych zapytań|Liczba|Łącznie|Łączna liczba współbieżnych zapytań|Brak wymiarów|
|TotalNumberOfExtents|Tak|Łączna Liczba zakresów|Liczba|Łącznie|Łączna Liczba zakresów danych|Brak wymiarów|
|TotalNumberOfThrottledCommands|Tak|Łączna liczba poleceń z ograniczeniami|Liczba|Łącznie|Łączna liczba poleceń z ograniczeniami|CommandType|
|TotalNumberOfThrottledQueries|Tak|Łączna liczba zapytań z ograniczeniami|Liczba|Łącznie|Łączna liczba zapytań z ograniczeniami|Brak wymiarów|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActionLatency|Tak|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsCompleted|Tak|Wykonane akcje |Liczba|Łącznie|Liczba ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsFailed|Tak|Akcje zakończone niepowodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|ActionsSkipped|Tak|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak wymiarów|
|ActionsStarted|Tak|Akcje uruchomione |Liczba|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Brak wymiarów|
|ActionsSucceeded|Tak|Akcje zakończone powodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionSuccessLatency|Tak|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionThrottledEvents|Tak|Zdarzenia ograniczające akcję|Liczba|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Brak wymiarów|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Tak|Użycie pamięci przez łącznik dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci przez łącznik dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Tak|Użycie procesora przez łącznik dla środowisko usługi integracji|Procent|Średnia|Użycie procesora przez łącznik dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Tak|Użycie pamięci przez przepływ pracy dla środowisko usługi integracji|Procent|Średnia|Użycie pamięci przez przepływ pracy dla środowiska usługi integracji.|Brak wymiarów|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Tak|Użycie procesora przepływu pracy dla środowisko usługi integracji|Procent|Średnia|Użycie procesora przez przepływ pracy dla środowiska usługi integracji.|Brak wymiarów|
|RunFailurePercentage|Tak|Procent niepowodzeń przebiegu|Procent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|RunLatency|Tak|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCancelled|Tak|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCompleted|Tak|Przebiegi zakończone|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsFailed|Tak|Przebiegi zakończone niepowodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|RunsStarted|Tak|Uruchomienia uruchomione|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak wymiarów|
|RunsSucceeded|Tak|Przebiegi zakończone powodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunStartThrottledEvents|Tak|Uruchom zdarzenia uruchamiania z ograniczeniami|Liczba|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|Brak wymiarów|
|RunSuccessLatency|Tak|Opóźnienie sukcesu przebiegu|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunThrottledEvents|Tak|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Brak wymiarów|
|TriggerFireLatency|Tak|Opóźnienie wyzwalania wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerLatency|Tak|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersCompleted|Tak|Ukończone wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersFailed|Tak|Wyzwalacze zakończone niepowodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|TriggersFired|Tak|Wyzwolone wyzwalacze |Liczba|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersSkipped|Tak|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersStarted|Tak|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersSucceeded|Tak|Wyzwalacze zakończone powodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggerSuccessLatency|Tak|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggerThrottledEvents|Tak|Wyzwalaj zdarzenia ograniczające|Liczba|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Brak wymiarów|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/przepływy pracy

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActionLatency|Tak|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsCompleted|Tak|Wykonane akcje |Liczba|Łącznie|Liczba ukończonych akcji przepływu pracy.|Brak wymiarów|
|ActionsFailed|Tak|Akcje zakończone niepowodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|ActionsSkipped|Tak|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak wymiarów|
|ActionsStarted|Tak|Akcje uruchomione |Liczba|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Brak wymiarów|
|ActionsSucceeded|Tak|Akcje zakończone powodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionSuccessLatency|Tak|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|ActionThrottledEvents|Tak|Zdarzenia ograniczające akcję|Liczba|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Brak wymiarów|
|BillableActionExecutions|Tak|Rozliczane wykonania akcji|Liczba|Łącznie|Liczba rozliczanych wykonań akcji przepływu pracy.|Brak wymiarów|
|BillableTriggerExecutions|Tak|Rozliczane wykonania wyzwalacza|Liczba|Łącznie|Liczba rozliczanych wykonań wyzwalacza przepływu pracy.|Brak wymiarów|
|BillingUsageNativeOperation|Tak|Użycie rozliczeń dla natywnych wykonań operacji|Liczba|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|Brak wymiarów|
|BillingUsageStandardConnector|Tak|Użycie rozliczeń dla wykonywania łączników standardowych|Liczba|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Brak wymiarów|
|BillingUsageStorageConsumption|Tak|Użycie rozliczeń dla wykonań zużycia magazynu|Liczba|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|Brak wymiarów|
|RunFailurePercentage|Tak|Procent niepowodzeń przebiegu|Procent|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|RunLatency|Tak|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCancelled|Tak|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak wymiarów|
|RunsCompleted|Tak|Przebiegi zakończone|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak wymiarów|
|RunsFailed|Tak|Przebiegi zakończone niepowodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|RunsStarted|Tak|Uruchomienia uruchomione|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak wymiarów|
|RunsSucceeded|Tak|Przebiegi zakończone powodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunStartThrottledEvents|Tak|Uruchom zdarzenia uruchamiania z ograniczeniami|Liczba|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|Brak wymiarów|
|RunSuccessLatency|Tak|Opóźnienie sukcesu przebiegu|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|RunThrottledEvents|Tak|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Brak wymiarów|
|TotalBillableExecutions|Tak|Łączna Liczba wykonań rozliczanych|Liczba|Łącznie|Liczba rozliczanych wykonań przepływu pracy.|Brak wymiarów|
|TriggerFireLatency|Tak|Opóźnienie wyzwalania wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggerLatency|Tak|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersCompleted|Tak|Ukończone wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersFailed|Tak|Wyzwalacze zakończone niepowodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Brak wymiarów|
|TriggersFired|Tak|Wyzwolone wyzwalacze |Liczba|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersSkipped|Tak|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersStarted|Tak|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak wymiarów|
|TriggersSucceeded|Tak|Wyzwalacze zakończone powodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggerSuccessLatency|Tak|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak wymiarów|
|TriggerThrottledEvents|Tak|Wyzwalaj zdarzenia ograniczające|Liczba|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Brak wymiarów|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/obszary robocze

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne rdzenie|Tak|Aktywne rdzenie|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Aktywne węzły|Tak|Aktywne węzły|Liczba|Średnia|Liczba węzłów Active. Są to węzły, w których aktywnie uruchomiono zadanie.|Scenariusz, ClusterName|
|Anulowanie żądanych przebiegów|Tak|Anulowanie żądanych przebiegów|Liczba|Łącznie|Liczba uruchomień, dla których zażądano anulowania dla tego obszaru roboczego. Licznik jest aktualizowany po odebraniu żądania anulowania dla uruchomienia.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Anulowane przebiegi|Tak|Anulowane przebiegi|Liczba|Łącznie|Liczba anulowanych przebiegów dla tego obszaru roboczego. Licznik jest aktualizowany w przypadku pomyślnego anulowania przebiegu.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Ukończone uruchomienia|Tak|Ukończone uruchomienia|Liczba|Łącznie|Liczba przebiegów pomyślnie ukończonych dla tego obszaru roboczego. Licznik jest aktualizowany po zakończeniu przebiegu i zebraniu danych wyjściowych.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|CpuUtilization|Tak|CpuUtilization|Liczba|Średnia|Procent użycia w węźle procesora CPU. Wykorzystanie jest zgłaszane w odstępach jednej minuty.|Scenariusz, runId, NodeId, ClusterName|
|błędy|Tak|błędy|Liczba|Łącznie|Liczba błędów uruchomienia w tym obszarze roboczym. Liczba jest aktualizowana za każdym razem, gdy wystąpi błąd.|Scenariusz|
|Nieudane uruchomienia|Tak|Nieudane uruchomienia|Liczba|Łącznie|Liczba uruchomień dla tego obszaru roboczego nie powiodła się. Licznik jest aktualizowany w przypadku niepowodzenia przebiegu.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Finalizowanie przebiegów|Tak|Finalizowanie przebiegów|Liczba|Łącznie|Liczba przebiegów w trakcie finalizowania stanu dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg został ukończony, ale kolekcja wyjściowa jest nadal w toku.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|GpuUtilization|Tak|GpuUtilization|Liczba|Średnia|Procent użycia w węźle procesora GPU. Wykorzystanie jest zgłaszane w odstępach jednej minuty.|Scenariusz, runId, NodeId, DeviceId, ClusterName|
|Rdzenie bezczynne|Tak|Rdzenie bezczynne|Liczba|Średnia|Liczba rdzeni bezczynnych|Scenariusz, ClusterName|
|Węzły bezczynne|Tak|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów. Węzły bezczynne są węzłami, w których nie są uruchomione żadne zadania, ale mogą akceptować nowe zadanie, jeśli jest dostępne.|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Tak|Opuszczanie rdzeni|Liczba|Średnia|Liczba rdzeni wychodzących|Scenariusz, ClusterName|
|Opuszczanie węzłów|Tak|Opuszczanie węzłów|Liczba|Średnia|Liczba pozostałych węzłów. Opuszczenie węzłów to węzły, które po prostu zakończyły przetwarzanie zadania i przechodzą w stan bezczynności.|Scenariusz, ClusterName|
|Wdrażanie modelu nie powiodło się|Tak|Wdrażanie modelu nie powiodło się|Liczba|Łącznie|Liczba wdrożeń modelu, które zakończyły się niepowodzeniem w tym obszarze roboczym|Scenariusz, StatusCode|
|Uruchomiono Wdrażanie modelu|Tak|Uruchomiono Wdrażanie modelu|Liczba|Łącznie|Liczba rozpoczętych wdrożeń modelu w tym obszarze roboczym|Scenariusz|
|Wdrażanie modelu powiodło się|Tak|Wdrażanie modelu powiodło się|Liczba|Łącznie|Liczba wdrożeń modelu, które zakończyły się pomyślnie w tym obszarze roboczym|Scenariusz|
|Nie można zarejestrować modelu|Tak|Nie można zarejestrować modelu|Liczba|Łącznie|Liczba rejestracji modelu zakończonych niepowodzeniem w tym obszarze roboczym|Scenariusz, StatusCode|
|Rejestrowanie modelu powiodło się|Tak|Rejestrowanie modelu powiodło się|Liczba|Łącznie|Liczba rejestracji modelu zakończonych powodzeniem w tym obszarze roboczym|Scenariusz|
|Przebiegi nie odpowiadają|Tak|Przebiegi nie odpowiadają|Liczba|Łącznie|Liczba przebiegów, które nie odpowiadają w tym obszarze roboczym. Licznik jest aktualizowany, gdy przebieg nie odpowiada.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Uruchomienia nieuruchomione|Tak|Uruchomienia nieuruchomione|Liczba|Łącznie|Liczba przebiegów w stanie nieuruchomionym dla tego obszaru roboczego. Licznik jest aktualizowany, gdy zostanie odebrane żądanie utworzenia przebiegu, ale informacje o uruchomieniu nie zostały jeszcze wypełnione. |Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Występujące rdzenie|Tak|Występujące rdzenie|Liczba|Średnia|Liczba przeniesiona rdzeni|Scenariusz, ClusterName|
|Zastępujące węzły|Tak|Zastępujące węzły|Liczba|Średnia|Liczba przeniesiona węzłów. Te węzły są węzłami o niskim priorytecie, które znajdują się poza dostępną pulą węzłów.|Scenariusz, ClusterName|
|Przygotowywanie przebiegów|Tak|Przygotowywanie przebiegów|Liczba|Łącznie|Liczba przebiegów przygotowywania dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg przechodzi do stanu przygotowywania podczas przygotowywania środowiska uruchomieniowego.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Uruchomienia aprowizacji|Tak|Uruchomienia aprowizacji|Liczba|Łącznie|Liczba uruchomień, które są inicjowane dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg oczekuje na utworzenie obiektu docelowego obliczeń lub inicjowanie obsługi.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Uruchomienia w kolejce|Tak|Uruchomienia w kolejce|Liczba|Łącznie|Liczba uruchomień umieszczonych w kolejce dla tego obszaru roboczego. Licznik jest aktualizowany, gdy przebieg zostanie umieszczony w kolejce w celu obliczenia. Może wystąpić podczas oczekiwania na gotowość wymaganych węzłów obliczeniowych.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Procent wykorzystania przydziałów|Tak|Procent wykorzystania przydziałów|Liczba|Średnia|Procent wykorzystania przydziałów|Scenariusz, ClusterName, VmFamilyName, VmPriority|
|Uruchomione uruchomienia|Tak|Uruchomione uruchomienia|Liczba|Łącznie|Liczba przebiegów uruchomionych dla tego obszaru roboczego. Licznik jest aktualizowany po rozpoczęciu uruchamiania w wymaganych zasobach.|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Uruchamianie przebiegów|Tak|Uruchamianie przebiegów|Liczba|Łącznie|Liczba rozpoczętych uruchomień dla tego obszaru roboczego. Licznik jest aktualizowany po zapisaniu żądania utworzenia i uruchomienia informacji, takich jak identyfikator przebiegu|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType, Eksperymentname|
|Łączna liczba rdzeni|Tak|Łączna liczba rdzeni|Liczba|Średnia|Łączna liczba rdzeni|Scenariusz, ClusterName|
|Łączna liczba węzłów|Tak|Łączna liczba węzłów|Liczba|Średnia|Łączna liczba węzłów. Ta suma obejmuje niektóre aktywne węzły, bezczynne węzły, węzły niezdatne do użycia, węzły Premepted, pozostawiając węzły|Scenariusz, ClusterName|
|Rdzenie, których nie można używać|Tak|Rdzenie, których nie można używać|Liczba|Średnia|Liczba rdzeni, których nie można używać|Scenariusz, ClusterName|
|Węzły niezdatne do użytku|Tak|Węzły niezdatne do użytku|Liczba|Średnia|Liczba nieużywanych węzłów. Węzły, które nie są użyteczne, nie działają z powodu problemu z nierozpoznawalne. Platforma Azure przeprowadzi odtwarzanie tych węzłów.|Scenariusz, ClusterName|
|Ostrzeżenia|Tak|Ostrzeżenia|Liczba|Łącznie|Liczba ostrzeżeń uruchamiania w tym obszarze roboczym. Liczba jest aktualizowana za każdym razem, gdy uruchomienie napotka ostrzeżenie.|Scenariusz|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konta

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Dostępność interfejsów API|ApiCategory, ApiName|
|Użycie|Nie|Użycie|Liczba|Liczba|Liczba wywołań interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AssetCount|Tak|Liczba zasobów|Liczba|Średnia|Ile zasobów zostało już utworzonych na bieżącym koncie usługi multimediów|Brak wymiarów|
|AssetQuota|Tak|Przydział zasobów|Liczba|Średnia|Ile zasobów jest dozwolonych dla bieżącego konta usługi multimediów|Brak wymiarów|
|AssetQuotaUsedPercentage|Tak|Procent użycia przydziału zasobów|Procent|Średnia|Procent użycia zasobów w bieżącym koncie usługi multimediów|Brak wymiarów|
|ContentKeyPolicyCount|Tak|Liczba zasad dotyczących kluczy zawartości|Liczba|Średnia|Ile zasad kluczy zawartości zostało już utworzonych na bieżącym koncie usługi multimediów|Brak wymiarów|
|ContentKeyPolicyQuota|Tak|Przydział zasad dotyczących kluczy zawartości|Liczba|Średnia|Ile zasad kluczy zawartości jest dozwolonych dla bieżącego konta usługi multimediów|Brak wymiarów|
|ContentKeyPolicyQuotaUsedPercentage|Tak|Procent użycia przydziału zasad klucza zawartości|Procent|Średnia|Procent użycia zasad klucza zawartości w bieżącym koncie usługi multimediów|Brak wymiarów|
|StreamingPolicyCount|Tak|Liczba zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego zostało już utworzonych na bieżącym koncie usługi multimediów|Brak wymiarów|
|StreamingPolicyQuota|Tak|Limit przydziału zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego jest dozwolonych dla bieżącego konta usługi multimediów|Brak wymiarów|
|StreamingPolicyQuotaUsedPercentage|Tak|Procent użycia limitu przydziału zasad przesyłania strumieniowego|Procent|Średnia|Procent użycia zasad przesyłania strumieniowego w bieżącym koncie usługi multimediów|Brak wymiarów|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/streamingEndpoints

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach.|OutputFormat|
|Żądania|Tak|Żądania|Liczba|Łącznie|Żądania do punktu końcowego przesyłania strumieniowego.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Tak|Pomyślne zakończenie oczekiwania|)|Średnia|Średnie opóźnienie dla pomyślnych żądań w milisekundach.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Tak|Rozmiar przydzielony puli|Bajty|Średnia|Rozmiar aprowizacji tej puli|Brak wymiarów|
|VolumePoolAllocatedUsed|Tak|Pula przydzielono do rozmiaru woluminu|Bajty|Średnia|Przydzielono używany rozmiar puli|Brak wymiarów|
|VolumePoolTotalLogicalSize|Tak|Rozmiar zużytego puli|Bajty|Średnia|Suma rozmiaru logicznego wszystkich woluminów należących do puli|Brak wymiarów|
|VolumePoolTotalSnapshotSize|Tak|Łączny rozmiar migawki dla puli|Bajty|Średnia|Suma rozmiarów migawek wszystkich woluminów w tej puli|Brak wymiarów|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/woluminy

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AverageReadLatency|Tak|Średnie opóźnienie odczytu|)|Średnia|Średnie opóźnienie odczytu w milisekundach na operację|Brak wymiarów|
|AverageWriteLatency|Tak|Średnie opóźnienie zapisu|)|Średnia|Średnie opóźnienie zapisu w milisekundach na operację|Brak wymiarów|
|CbsVolumeBackupActive|Tak|Stan aktywności kopii zapasowej woluminu|Liczba|Średnia|Czy kopia zapasowa jest aktualnie wstrzymana dla woluminu.|Brak wymiarów|
|CbsVolumeLogicalBackupBytes|Tak|Utworzono kopię zapasową bajtów logicznych|Bajty|Średnia|Łączna liczba bajtów unskompresowano/unencrypted, których kopia zapasowa jest tworzona dla tego woluminu.|Brak wymiarów|
|CbsVolumeOperationComplete|Tak|Stan operacji|Liczba|Średnia|Jest ostatnią operacją tworzenia kopii zapasowej/przywracania.|Brak wymiarów|
|CbsVolumeOperationTransferredBytes|Tak|Bajty przesłane dla operacji|Bajty|Średnia|Łączna liczba bajtów przesłanych dla ostatniej operacji tworzenia kopii zapasowej/przywracania.|Brak wymiarów|
|CbsVolumeProtected|Tak|Stan chroniony woluminem|Liczba|Średnia|Jest woluminem chronionym przez usługę kopii zapasowej w chmurze.|Brak wymiarów|
|ReadIops|Tak|Odczyt operacji we/wy|CountPerSecond|Średnia|Operacje odczytu/out na sekundę|Brak wymiarów|
|VolumeAllocatedSize|Tak|Rozmiar przydzielony woluminu|Bajty|Średnia|Rozmiar aprowizacji woluminu|Brak wymiarów|
|VolumeLogicalSize|Tak|Rozmiar zużytego woluminu|Bajty|Średnia|Rozmiar logiczny woluminu (zużyte bajty)|Brak wymiarów|
|VolumeSnapshotSize|Tak|Rozmiar migawki woluminu|Bajty|Średnia|Rozmiar wszystkich migawek w woluminie|Brak wymiarów|
|WriteIops|Tak|Zapisz operacje we/wy|CountPerSecond|Średnia|Operacje zapisu/wylogowania na sekundę|Brak wymiarów|
|XregionReplicationHealthy|Tak|Kondycja stanu replikacji woluminu|Liczba|Średnia|Warunek relacji, 1 lub 0.|Brak wymiarów|
|XregionReplicationLagTime|Tak|Czas zwłoki replikacji woluminu|Sekundy|Średnia|Czas (w sekundach), przez który dane na duplikacie spowolnienia za źródłem.|Brak wymiarów|
|XregionReplicationLastTransferDuration|Tak|Czas trwania ostatniego transferu replikacji woluminu|Sekundy|Średnia|Czas (w sekundach) oczekiwania na ukończenie ostatniego transferu.|Brak wymiarów|
|XregionReplicationLastTransferSize|Tak|Rozmiar ostatniego transferu replikacji woluminu|Bajty|Średnia|Całkowita liczba bajtów przetransferowanych w ramach ostatniego transferu.|Brak wymiarów|
|XregionReplicationRelationshipProgress|Tak|Postęp replikacji woluminu|Bajty|Średnia|Łączna ilość danych przesłanych dla bieżącej operacji transferu.|Brak wymiarów|
|XregionReplicationRelationshipTransferring|Tak|Czy transfer replikacji woluminu|Liczba|Średnia|Czy stan replikacji woluminu to "Transfer".|Brak wymiarów|
|XregionReplicationTotalTransferBytes|Tak|Łączny transfer replikacji woluminów|Bajty|Średnia|Skumulowane Bajty przesłane dla relacji.|Brak wymiarów|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Nie|Łączny czas Application Gateway|)|Średnia|Średni czas przetwarzania żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako średnia interwału od momentu, kiedy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że zwykle obejmuje to czas przetwarzania Application Gateway, czas, przez który pakiety żądań i odpowiedzi są przesyłane przez sieć i czas odpowiedzi serwera wewnętrznej bazy danych.|Odbiornik|
|AvgRequestCountPerHealthyHost|Nie|Liczba żądań na minutę na hosta w dobrej kondycji|Liczba|Średnia|Średnia liczba żądań na minutę na hosta zaplecza w dobrej kondycji w puli|BackendSettingsPool|
|BackendConnectTime|Nie|Czas połączenia z zapleczem|)|Średnia|Czas nawiązywania połączenia z serwerem wewnętrznej bazy danych|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|BackendFirstByteResponseTime|Nie|Czas odpowiedzi na pierwszy bajt zaplecza|)|Średnia|Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania pierwszego bajtu nagłówka odpowiedzi, który zbliża czas przetwarzania serwera wewnętrznej bazy danych|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|BackendLastByteResponseTime|Nie|Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych|)|Średnia|Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania ostatniego bajtu treści odpowiedzi|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|BackendResponseStatus|Tak|Stan odpowiedzi zaplecza|Liczba|Łącznie|Liczba kodów odpowiedzi HTTP wygenerowanych przez składowe zaplecza. Nie obejmuje to żadnych kodów odpowiedzi wygenerowanych przez Application Gateway.|BackendServer, ustawień httpsettings elementu, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Tak|Dystrybucja reguły zablokowanych żądań zapory aplikacji sieci Web|Liczba|Łącznie|Dystrybucja reguły zablokowanych żądań zapory aplikacji sieci Web|Rule, RuleId|
|BlockedReqCount|Tak|Liczba zablokowanych żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba zablokowanych żądań zapory aplikacji sieci Web|Brak wymiarów|
|BytesReceived|Tak|Bajty odebrane|Bajty|Łącznie|Całkowita liczba bajtów odebranych przez Application Gateway od klientów|Odbiornik|
|BytesSent|Tak|Bajty wysłane|Bajty|Łącznie|Całkowita liczba bajtów wysłanych przez Application Gateway do klientów|Odbiornik|
|CapacityUnits|Nie|Bieżące jednostki wydajności|Liczba|Średnia|Zużyte jednostki wydajności|Brak wymiarów|
|ClientRtt|Nie|Czas RTT klienta|)|Średnia|Średni czas błądzenia między klientami a Application Gateway. Ta Metryka wskazuje, jak długo trwa ustanawianie połączeń i zwracanie potwierdzeń|Odbiornik|
|ComputeUnits|Nie|Bieżące jednostki obliczeniowe|Liczba|Średnia|Wykorzystane jednostki obliczeniowe|Brak wymiarów|
|CpuUtilization|Nie|Wykorzystanie procesora|Procent|Średnia|Bieżące wykorzystanie procesora CPU Application Gateway|Brak wymiarów|
|Wartości CurrentConnections|Tak|Bieżące połączenia|Liczba|Łącznie|Liczba bieżących połączeń ustanowionych z Application Gateway|Brak wymiarów|
|EstimatedBilledCapacityUnits|Nie|Szacowane jednostki pojemności|Liczba|Średnia|Szacowane jednostki wydajności, które będą obciążane|Brak wymiarów|
|FailedRequests|Tak|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Liczba żądań zakończonych niepowodzeniem, które zostały obsłużone przez Application Gateway|BackendSettingsPool|
|FixedBillableCapacityUnits|Nie|Jednostki stałej wydajności rozliczanej|Liczba|Średnia|Minimalna jednostka pojemności, która zostanie obciążona|Brak wymiarów|
|HealthyHostCount|Tak|Liczba hostów w dobrej kondycji|Liczba|Średnia|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|MatchedCount|Tak|Dystrybucja reguł dla zapory aplikacji sieci Web|Liczba|Łącznie|Dystrybucja reguły dla ruchu przychodzącego przez zaporę aplikacji sieci Web|Rule, RuleId|
|NewConnectionsPerSecond|Nie|Nowe połączenia na sekundę|CountPerSecond|Średnia|Nowe połączenia na sekundę ustanowione przy użyciu Application Gateway|Brak wymiarów|
|ResponseStatus|Tak|Stan odpowiedzi|Liczba|Łącznie|Stan odpowiedzi HTTP zwrócony przez Application Gateway|HttpStatusGroup|
|Przepływność|Nie|Przepływność|BytesPerSecond|Średnia|Liczba bajtów na sekundę obsłużonych przez Application Gateway|Brak wymiarów|
|TlsProtocol|Tak|Protokół TLS klienta|Liczba|Łącznie|Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, które ustanowiły połączenie z Application Gateway. Aby wyświetlić dystrybucję protokołu TLS, należy filtrować według protokołu TLS wymiaru.|Odbiornik, TlsProtocol|
|TotalRequests|Tak|Łączna liczba żądań|Liczba|Łącznie|Liczba pomyślnych żądań obsłużonych przez Application Gateway|BackendSettingsPool|
|UnhealthyHostCount|Tak|Liczba hostów w złej kondycji|Liczba|Średnia|Liczba hostów zaplecza w złej kondycji|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Tak|Liczba trafień reguł aplikacji|Liczba|Łącznie|Liczba trafień reguł aplikacji|Stan, powód, protokół|
|Przetwarzanie dataprocessed|Tak|Ilość przetworzonych danych|Bajty|Łącznie|Łączna ilość danych przetworzonych przez tę zaporę|Brak wymiarów|
|FirewallHealth|Tak|Stan kondycji zapory|Procent|Średnia|Wskazuje ogólną kondycję tej zapory|Stan, Przyczyna|
|NetworkRuleHit|Tak|Liczba trafień reguł sieci|Liczba|Łącznie|Liczba trafień reguł sieci|Stan, powód, protokół|
|SNATPortUtilization|Tak|Wykorzystanie portów przez przytranslatora adresów sieciowych|Procent|Średnia|Procent aktualnie używanych portów przychodzących do ruchu przychodzącego|Protokół|
|Przepływność|Nie|Przepływność|BitsPerSecond|Średnia|Przepływność przetwarzana przez tę zaporę|Brak wymiarów|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Tak|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|Brak wymiarów|
|BitsOutPerSecond|Tak|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|Brak wymiarów|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|QueryVolume|Tak|Wolumin zapytania|Liczba|Łącznie|Liczba zapytań obsłużonych dla strefy DNS|Brak wymiarów|
|RecordSetCapacityUtilization|Nie|Wykorzystanie pojemności zestawu rekordów|Procent|Maksimum|Procent pojemności zestawu rekordów używany przez strefę DNS|Brak wymiarów|
|RecordSetCount|Tak|Liczba zestawów rekordów|Liczba|Maksimum|Liczba zestawów rekordów w strefie DNS|Brak wymiarów|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ArpAvailability|Tak|Dostępność protokołu ARP|Procent|Średnia|Dostępność protokołu ARP od MSEE do wszystkich elementów równorzędnych.|PeeringType, element równorzędny|
|BgpAvailability|Tak|Dostępność protokołu BGP|Procent|Średnia|Dostępność protokołu BGP od MSEE do wszystkich elementów równorzędnych.|PeeringType, element równorzędny|
|BitsInPerSecond|Nie|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|PeeringType, DeviceRole|
|BitsOutPerSecond|Nie|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|Nie|GlobalReachBitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Nie|GlobalReachBitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Tak|DroppedInBitsPerSecond|BitsPerSecond|Średnia|Liczba porzuconych bitów danych przychodzących na sekundę|Brak wymiarów|
|QosDropBitsOutPerSecond|Tak|DroppedOutBitsPerSecond|BitsPerSecond|Średnia|Liczba porzuconych bitów danych wyjściowych na sekundę|Brak wymiarów|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Tak|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|Brak wymiarów|
|BitsOutPerSecond|Tak|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|Brak wymiarów|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Nie|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|Nie|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AdminState|Tak|AdminState|Liczba|Średnia|Stan administratora portu|Link|
|LineProtocol|Tak|LineProtocol|Liczba|Średnia|Stan protokołu wiersza portu|Link|
|PortBitsInPerSecond|Tak|BitsInPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu przychodzącego platformy Azure na sekundę|Link|
|PortBitsOutPerSecond|Tak|BitsOutPerSecond|BitsPerSecond|Średnia|Liczba bitów ruchu wychodzącego platformy Azure na sekundę|Link|
|RxLightLevel|Tak|RxLightLevel|Liczba|Średnia|Poziom oświetlenia odbierania w dBm|Link, tor|
|TxLightLevel|Tak|TxLightLevel|Liczba|Średnia|Poziom oświetlenia TX w dBm|Link, tor|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Tak|Procent kondycji zaplecza|Procent|Średnia|Procent pomyślnych sond kondycji z serwera proxy HTTP/S do frontonu|Zaplecze, ustawień httpsettings elementu|
|BackendRequestCount|Tak|Liczba żądań wewnętrznej bazy danych|Liczba|Łącznie|Liczba żądań wysyłanych z serwera proxy HTTP/S do frontonu|Wartości httpStatus, HttpStatusGroup, zaplecze|
|BackendRequestLatency|Tak|Opóźnienie żądania wewnętrznej bazy danych|)|Średnia|Czas obliczony od momentu wysłania żądania przez serwer proxy HTTP/S do zaplecza do momentu odebrania przez serwer proxy HTTP/S ostatniego bajtu odpowiedzi z zaplecza|Zaplecze|
|BillableResponseSize|Tak|Rozmiar odpowiedzi do rozliczenia|Bajty|Łącznie|Liczba bajtów rozliczanych (minimalna 2KB na żądanie) wysyłanych jako odpowiedzi z serwera proxy HTTP/S do klientów.|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Tak|Liczba żądań|Liczba|Łącznie|Liczba żądań klientów obsłużonych przez serwer proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Tak|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysłanych jako żądania od klientów do serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Tak|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Tak|Łączne opóźnienie|)|Średnia|Czas obliczony od momentu odebrania żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Tak|Liczba żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Nie|Przydzielono porty przydziałów adresów sieciowych|Liczba|Średnia|Łączna liczba portów przydzieloną porty adresów sieciowych w okresie|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Tak|Liczba bajtów|Bajty|Łącznie|Łączna liczba bajtów przesłanych w okresie|FrontendIPAddress, FrontendPort, kierunek|
|DipAvailability|Tak|Stan sondy kondycji|Liczba|Średnia|Średni stan sondy kondycji Load Balancer na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Tak|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SnatConnectionCount|Tak|Liczba połączeń z translatorem adresów sieciowych|Liczba|Łącznie|Łączna liczba nowych połączeń z przyłączaniem do adresów w czasie|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Tak|Liczba SYN|Liczba|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|UsedSnatPorts|Nie|Używane porty|Liczba|Średnia|Łączna liczba portów przydziałów adresów sieciowych używanych w czasie|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Tak|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność ścieżki danych Load Balancer na czas trwania|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Tak|Bajty odebrane|Bajty|Łącznie|Liczba bajtów odebranych przez interfejs sieciowy|Brak wymiarów|
|BytesSentRate|Tak|Bajty wysłane|Bajty|Łącznie|Liczba bajtów wysłanych przez interfejs sieciowy|Brak wymiarów|
|PacketsReceivedRate|Tak|Odebrane pakiety|Liczba|Łącznie|Liczba pakietów odebranych przez interfejs sieciowy|Brak wymiarów|
|PacketsSentRate|Tak|Wysłane pakiety|Liczba|Łącznie|Liczba pakietów wysłanych przez interfejs sieciowy|Brak wymiarów|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Tak|Średni czas błądzenia (MS)|)|Średnia|Średni czas błądzenia sieci (MS) dla sond monitorowania łączności przesyłanych między źródłem a miejscem docelowym|Brak wymiarów|
|ChecksFailedPercent|Tak|Sprawdza procent niepowodzenia (wersja zapoznawcza)|Procent|Średnia|% testów monitorowania łączności nie powiodło się|SourceAddress, SourceName, identyfikator sourceresourceid, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Tak|% Sond nie powiodło się|Procent|Średnia|% sond monitorowania łączności nie powiodło się|Brak wymiarów|
|RoundTripTimeMs|Tak|Czas Round-Trip (MS) (wersja zapoznawcza)|)|Średnia|Czas błądzenia w milisekundach dla kontroli monitorowania łączności|SourceAddress, SourceName, identyfikator sourceresourceid, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/adresów publicipaddress

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ByteCount|Tak|Liczba bajtów|Bajty|Łącznie|Łączna liczba bajtów przesłanych w okresie|Port, kierunek|
|BytesDroppedDDoS|Tak|Bajty przychodzące opuszczone DDoS|BytesPerSecond|Maksimum|Bajty przychodzące opuszczone DDoS|Brak wymiarów|
|BytesForwardedDDoS|Tak|Przekazane bajty przychodzące DDoS|BytesPerSecond|Maksimum|Przekazane bajty przychodzące DDoS|Brak wymiarów|
|BytesInDDoS|Tak|Bajty przychodzące DDoS|BytesPerSecond|Maksimum|Bajty przychodzące DDoS|Brak wymiarów|
|DDoSTriggerSYNPackets|Tak|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|CountPerSecond|Maksimum|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|Brak wymiarów|
|DDoSTriggerTCPPackets|Tak|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|Brak wymiarów|
|DDoSTriggerUDPPackets|Tak|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|Brak wymiarów|
|IfUnderDDoSAttack|Tak|W obszarze atak DDoS|Liczba|Maksimum|W obszarze atak DDoS|Brak wymiarów|
|PacketCount|Tak|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów wysłanych w czasie|Port, kierunek|
|PacketsDroppedDDoS|Tak|Odrzucone pakiety przychodzące DDoS|CountPerSecond|Maksimum|Odrzucone pakiety przychodzące DDoS|Brak wymiarów|
|PacketsForwardedDDoS|Tak|Przekazane pakiety przychodzące DDoS|CountPerSecond|Maksimum|Przekazane pakiety przychodzące DDoS|Brak wymiarów|
|PacketsInDDoS|Tak|Pakiety przychodzące DDoS|CountPerSecond|Maksimum|Pakiety przychodzące DDoS|Brak wymiarów|
|SynCount|Tak|Liczba SYN|Liczba|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|Port, kierunek|
|TCPBytesDroppedDDoS|Tak|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|Brak wymiarów|
|TCPBytesForwardedDDoS|Tak|Przekazane DDoS przychodzące bajty TCP|BytesPerSecond|Maksimum|Przekazane DDoS przychodzące bajty TCP|Brak wymiarów|
|TCPBytesInDDoS|Tak|Przychodzące bajty TCP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP DDoS|Brak wymiarów|
|TCPPacketsDroppedDDoS|Tak|Liczba porzuconych pakietów TCP przychodzących DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów TCP przychodzących DDoS|Brak wymiarów|
|TCPPacketsForwardedDDoS|Tak|Przychodzące pakiety TCP przesłane dalej DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP przesłane dalej DDoS|Brak wymiarów|
|TCPPacketsInDDoS|Tak|Przychodzące pakiety TCP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP DDoS|Brak wymiarów|
|UDPBytesDroppedDDoS|Tak|Liczba porzuconych bajtów przychodzących UDP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących UDP DDoS|Brak wymiarów|
|UDPBytesForwardedDDoS|Tak|Przekazane przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przekazane przychodzące bajty UDP DDoS|Brak wymiarów|
|UDPBytesInDDoS|Tak|Przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP DDoS|Brak wymiarów|
|UDPPacketsDroppedDDoS|Tak|Liczba porzuconych pakietów przychodzących UDP DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów przychodzących UDP DDoS|Brak wymiarów|
|UDPPacketsForwardedDDoS|Tak|Przychodzące pakiety UDP DDoS przesłane dalej|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS przesłane dalej|Brak wymiarów|
|UDPPacketsInDDoS|Tak|Przychodzące pakiety UDP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS|Brak wymiarów|
|VipAvailability|Tak|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność adresów IP na czas trwania|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Tak|Stan punktu końcowego według punktu końcowego|Liczba|Maksimum|1 Jeśli sonda punktu końcowego ma stan "włączone", 0 w przeciwnym razie.|Nazwapunktukoncowego|
|QpsByEndpoint|Tak|Zwrócone zapytania według punktu końcowego|Liczba|Łącznie|Liczba zwróconych punktów końcowych Traffic Manager w danym przedziale czasu|Nazwapunktukoncowego|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AverageBandwidth|Tak|Przepustowość S2S bramy|BytesPerSecond|Średnia|Średnia przepustowość lokacja-lokacja bramy w bajtach na sekundę|Brak wymiarów|
|P2SBandwidth|Tak|Przepustowość P2S bramy|BytesPerSecond|Średnia|Średnia przepustowość typu punkt-lokacja bramy w bajtach na sekundę|Brak wymiarów|
|P2SConnectionCount|Tak|Liczba połączeń typu P2S|Liczba|Maksimum|Liczba połączeń punkt-lokacja bramy|Protokół|
|TunnelAverageBandwidth|Tak|Przepustowość tunelu|BytesPerSecond|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tak|Liczba bajtów ruchu wychodzącego dla tunelu|Bajty|Łącznie|Bajty wychodzące tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tak|Porzucone pakiety ruchu wychodzącego tunelu z powodu niezgodności TS|Liczba|Łącznie|Liczba porzuconych pakietów wychodzących z powodu niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tak|Liczba pakietów ruchu wychodzącego dla tunelu|Liczba|Łącznie|Liczba pakietów wychodzących tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tak|Liczba bajtów ruchu przychodzącego dla tunelu|Bajty|Łącznie|Liczba bajtów przychodzących tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tak|Porzucone pakiety ruchu przychodzącego tunelu z powodu niezgodności TS|Liczba|Łącznie|Liczba porzuconych pakietów przychodzących z powodu niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tak|Pakiety przychodzące tunelu|Liczba|Łącznie|Liczba pakietów przychodzących tunelu|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tak|Czas błądzenia dla poleceń ping dla maszyny wirtualnej|)|Średnia|Czas błądzenia dla poleceń ping wysyłanych do docelowej maszyny wirtualnej|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Tak|Nieudane polecenia ping do maszyny wirtualnej|Procent|Średnia|Procent liczby zakończonych niepowodzeniem poleceń ping do całkowitego wysłania poleceń ping dla docelowej maszyny wirtualnej|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|przychodzące|Tak|Komunikaty przychodzące|Liczba|Łącznie|Liczba wszystkich pomyślnie wysłanych wywołań interfejsu API. |Brak wymiarów|
|przychodzące. ALL. failedrequests|Tak|Wszystkie przychodzące żądania zakończone niepowodzeniem|Liczba|Łącznie|Łączna liczba nieudanych żądań przychodzących dla centrum powiadomień|Brak wymiarów|
|przychodzące. ALL. Requests|Tak|Wszystkie żądania przychodzące|Liczba|Łącznie|Łączna liczba żądań przychodzących dla centrum powiadomień|Brak wymiarów|
|przychodzące. zaplanowane|Tak|Przesłane zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowano zaplanowane powiadomienia wypychane|Brak wymiarów|
|przychodzące. zaplanowane. Cancel|Tak|Anulowano zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowano zaplanowane powiadomienia wypychane|Brak wymiarów|
|Instalacja. wszystkie|Tak|Operacje zarządzania instalacją|Liczba|Łącznie|Operacje zarządzania instalacją|Brak wymiarów|
|Instalacja. Delete|Tak|Usuwanie operacji instalacji|Liczba|Łącznie|Usuwanie operacji instalacji|Brak wymiarów|
|Instalacja. Get|Tak|Pobierz operacje instalacji|Liczba|Łącznie|Pobierz operacje instalacji|Brak wymiarów|
|Instalacja. poprawka|Tak|Operacje instalacji poprawek|Liczba|Łącznie|Operacje instalacji poprawek|Brak wymiarów|
|Instalacja. upsert|Tak|Tworzenie lub aktualizowanie operacji instalacji|Liczba|Łącznie|Tworzenie lub aktualizowanie operacji instalacji|Brak wymiarów|
|notificationhub. pushs|Tak|Wszystkie powiadomienia wychodzące|Liczba|Łącznie|Wszystkie powiadomienia wychodzące centrum powiadomień|Brak wymiarów|
|wychodzący. allpns. badorexpiredchannel|Tak|Błędy nieprawidłowych lub wygasłych kanałów|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ kanał/token/Identyfikator rejestracji w rejestracji został wygasły lub nieprawidłowy.|Brak wymiarów|
|wychodzący. allpns. channelerror|Tak|Błędy kanałów|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ kanał był nieprawidłowy, nie jest skojarzony z poprawną aplikacją lub wygasłą.|Brak wymiarów|
|wychodzący. allpns. invalidpayload|Tak|Błędy ładunku|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ PNS zwrócił zły błąd ładunku.|Brak wymiarów|
|wychodzący. allpns. pnserror|Tak|Błędy zewnętrznego systemu powiadomień|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ wystąpił problem z komunikacją z PNS (wyklucza problemy z uwierzytelnianiem).|Brak wymiarów|
|wychodzący. allpns. Success|Tak|Powiadomienia zakończone powodzeniem|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzące. APNs. badchannel|Tak|Błąd nieprawidłowego kanału usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ token jest nieprawidłowy (kod stanu usługi APNS: 8).|Brak wymiarów|
|wychodzące. APNs. expiredchannel|Tak|Błąd wygasłego kanału usługi APNS|Liczba|Łącznie|Liczba tokenów unieważnionych przez kanał opinii usługi APNS.|Brak wymiarów|
|wychodzące. APNs. invalidcredentials|Tak|Błędy autoryzacji usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|wychodzące. APNs. invalidnotificationsize|Tak|Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (kod stanu usługi APNS: 7).|Brak wymiarów|
|wychodzące. APNs. pnserror|Tak|Błędy usługi APNS|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem z powodu błędów komunikacji z usługą APNS.|Brak wymiarów|
|wychodzące. APN. sukces|Tak|Pomyślne powiadomienia usługi APNS|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzący. GCM. authenticationerror|Tak|Błędy uwierzytelniania GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń, a SenderId nie jest poprawnie skonfigurowana w aplikacji (wynik GCM: MismatchedSenderId).|Brak wymiarów|
|wychodzący. GCM. badchannel|Tak|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie został rozpoznany (wynik GCM: nieprawidłowa rejestracja).|Brak wymiarów|
|wychodzący. GCM. expiredchannel|Tak|Błąd GCM kanału wygasłego|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia Identyfikator rejestracji w rejestracji (wynik GCM: NotRegistered).|Brak wymiarów|
|wychodzący. GCM. invalidcredentials|Tak|GCM błędy autoryzacji (Nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|wychodzący. GCM. invalidnotificationformat|Tak|Nieprawidłowy format powiadomienia GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek nie został poprawnie sformatowany (wynik GCM: InvalidDataKey lub InvalidTtl).|Brak wymiarów|
|wychodzący. GCM. invalidnotificationsize|Tak|Błąd nieprawidłowego rozmiaru powiadomienia GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (wynik GCM: MessageTooBig).|Brak wymiarów|
|wychodzący. GCM. pnserror|Tak|Błędy GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z GCM.|Brak wymiarów|
|wychodzący. GCM. Success|Tak|GCM pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzące. GCM. dławienia|Tak|GCM powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ GCM ograniczenie tej aplikacji (kod stanu GCM: 501-599 lub wynik: niedostępne).|Brak wymiarów|
|wychodzący. GCM. wrongchannel|Tak|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie jest skojarzony z bieżącą aplikacją (GCM Result: InvalidPackageName).|Brak wymiarów|
|wychodzący. usługi MPNS. authenticationerror|Tak|Błędy uwierzytelniania usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|wychodzący. usługi MPNS. badchannel|Tak|Błąd nieprawidłowego kanału usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (usługi MPNS status: 404).|Brak wymiarów|
|wychodzący. usługi MPNS. channeldisconnected|Tak|Kanał usługi MPNS został odłączony|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji został rozłączony (stan usługi MPNS: 412 nie został znaleziony).|Brak wymiarów|
|wychodzące. usługi MPNS. upuszczone|Tak|USŁUGI MPNS usunięte powiadomienia|Liczba|Łącznie|Liczba wypchnięciów, które zostały porzucone przez usługi MPNS (nagłówek odpowiedzi usługi MPNS: X-NotificationStatus: QueueFull lub pomijane).|Brak wymiarów|
|wychodzący. usługi MPNS. invalidcredentials|Tak|USŁUGI MPNS nieprawidłowe poświadczenia|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak wymiarów|
|wychodzący. usługi MPNS. invalidnotificationformat|Tak|Nieprawidłowy format powiadomienia usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży.|Brak wymiarów|
|wychodzący. usługi MPNS. pnserror|Tak|Błędy usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z usługi MPNS.|Brak wymiarów|
|wychodzący. usługi MPNS. Success|Tak|USŁUGI MPNS pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzące. usługi MPNS. dławienia|Tak|USŁUGI MPNS powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ usługi MPNS ogranicza tę aplikację (WNS usługi MPNS: 406 nieakceptowalny).|Brak wymiarów|
|wychodzący. WNS. authenticationerror|Tak|Błędy uwierzytelniania WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z nieprawidłowymi poświadczeniami usługi Windows Live lub nieprawidłowym tokenem.|Brak wymiarów|
|wychodzący. WNS. badchannel|Tak|Błąd nieprawidłowego kanału WNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (WNS status: 404).|Brak wymiarów|
|wychodzący. WNS. channeldisconnected|Tak|Kanał WNS został odłączony|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (WNS w nagłówku odpowiedzi: X-WNS-DeviceConnectionStatus: disconnected).|Brak wymiarów|
|wychodzący. WNS. channelthrottled|Tak|Ograniczenie kanału WNS|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (nagłówek odpowiedzi WNS: X-WNS-NotificationStatus: channelThrottled).|Brak wymiarów|
|wychodzące. WNS. upuszczone|Tak|WNS usunięte powiadomienia|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (X-WNS-NotificationStatus: porzucone, ale nie X-WNS-DeviceConnectionStatus: disconnected).|Brak wymiarów|
|wychodzący. WNS. expiredchannel|Tak|Błąd WNS kanału wygasłego|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia identyfikator channeluri (stan WNS: 410).|Brak wymiarów|
|wychodzący. WNS. invalidcredentials|Tak|WNS błędy autoryzacji (Nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane. (Usługa Windows Live nie rozpoznaje poświadczeń).|Brak wymiarów|
|wychodzący. WNS. invalidnotificationformat|Tak|Nieprawidłowy format powiadomienia WNS|Liczba|Łącznie|Format powiadomienia jest nieprawidłowy (stan WNS: 400). Należy pamiętać, że WNS nie odrzuca wszystkich nieprawidłowych ładunków.|Brak wymiarów|
|wychodzący. WNS. invalidnotificationsize|Tak|Błąd nieprawidłowego rozmiaru powiadomienia WNS|Liczba|Łącznie|Ładunek powiadomienia jest zbyt duży (stan WNS: 413).|Brak wymiarów|
|wychodzący. WNS. invalidtoken|Tak|Błędy autoryzacji WNS (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do WNS jest nieprawidłowy (stan WNS: 401 Brak autoryzacji).|Brak wymiarów|
|wychodzący. WNS. pnserror|Tak|Błędy WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z WNS.|Brak wymiarów|
|wychodzący. WNS. Success|Tak|WNS pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak wymiarów|
|wychodzące. WNS. dławienia|Tak|WNS powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ WNS ogranicza tę aplikację (stan WNS: 406 nie akceptowalny).|Brak wymiarów|
|wychodzący. WNS. tokenproviderunreachable|Tak|Błędy autoryzacji WNS (nieosiągalne)|Liczba|Łącznie|Usługa Windows Live jest nieosiągalna.|Brak wymiarów|
|wychodzący. WNS. wrongtoken|Tak|WNS błędy autoryzacji (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do WNS jest prawidłowy, ale dla innej aplikacji (stan WNS: 403 zabroniony). Może się tak zdarzyć, jeśli identyfikator channeluri w rejestracji jest skojarzony z inną aplikacją. Sprawdź, czy aplikacja kliencka jest skojarzona z tą samą aplikacją, której poświadczenia znajdują się w centrum powiadomień.|Brak wymiarów|
|Rejestracja. wszystkie|Tak|Operacje rejestracji|Liczba|Łącznie|Liczba wszystkich operacji rejestracji zakończonych powodzeniem (Tworzenie aktualizacji zapytań i usunięć). |Brak wymiarów|
|Rejestracja. Tworzenie|Tak|Operacje tworzenia rejestracji|Liczba|Łącznie|Liczba wszystkich operacji tworzenia rejestracji zakończonych powodzeniem.|Brak wymiarów|
|Rejestracja. Usuwanie|Tak|Operacje usuwania rejestracji|Liczba|Łącznie|Liczba wszystkich operacji usunięcia rejestracji zakończonych powodzeniem.|Brak wymiarów|
|Rejestracja. Get|Tak|Operacje odczytu rejestracji|Liczba|Łącznie|Liczba wszystkich zapytań rejestracji zakończonych powodzeniem.|Brak wymiarów|
|Rejestracja. Aktualizacja|Tak|Operacje aktualizacji rejestracji|Liczba|Łącznie|Liczba wszystkich aktualizacji rejestracji zakończonych powodzeniem.|Brak wymiarów|
|zaplanowane. oczekujące|Tak|Oczekujące zaplanowane powiadomienia|Liczba|Łącznie|Oczekujące zaplanowane powiadomienia|Brak wymiarów|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/obszary robocze

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępna pamięć Average_%|Tak|Dostępna pamięć (%)|Liczba|Średnia|Dostępna pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępny obszar wymiany Average_%|Tak|Dostępny obszar wymiany (%)|Liczba|Średnia|Dostępny obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% zadeklarowanych bajtów w użyciu|Tak|% Zadeklarowanych bajtów w użyciu|Liczba|Średnia|Average_% zadeklarowanych bajtów w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas DPC Average_%|Tak|Czas DPC (%)|Liczba|Średnia|Czas DPC Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bezpłatne węzłów i|Tak|% Wolnego węzłów i|Liczba|Średnia|Average_% bezpłatne węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|Tak|Wolne miejsce (%)|Liczba|Średnia|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas bezczynności Average_%|Tak|Czas bezczynności (%)|Liczba|Średnia|Czas bezczynności Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas przerwań (%)|Tak|Czas przerwań (%)|Liczba|Średnia|Average_ czas przerwań (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas oczekiwania operacji we/wy Average_%|Tak|Czas oczekiwania operacji we/wy (%)|Liczba|Średnia|Czas oczekiwania operacji we/wy Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu całkiem|Tak|% Całkiem czasu|Liczba|Średnia|Average_% czasu całkiem|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu uprzywilejowanego|Tak|Czas uprzywilejowany (%)|Liczba|Średnia|Average_% czasu uprzywilejowanego|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Tak|Czas procesora (%)|Liczba|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ użyto węzłów i|Tak|% Użytych węzłów i|Liczba|Średnia|Average_ użyto węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Użyta pamięć Average_%|Tak|Używana pamięć (%)|Liczba|Średnia|Użyta pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używane miejsce Average_%|Tak|Zajęte miejsce (%)|Liczba|Średnia|Używane miejsce Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar wymiany Average_%|Tak|Używany obszar wymiany (%)|Liczba|Średnia|Używany obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_%|Tak|Czas użytkownika (%)|Liczba|Średnia|Czas użytkownika Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Tak|Dostępna pamięć w MB|Liczba|Średnia|Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Available MB|Tak|Dostępna pamięć (MB)|Liczba|Średnia|Pamięć Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zamiana Average_Available MB|Tak|Dostępny obszar wymiany (MB)|Liczba|Średnia|Zamiana Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. wartość operacji dysku na sek./odczyt|Tak|Średni czas dysku w s/odczyt|Liczba|Średnia|Average_Avg. wartość operacji dysku na sek./odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Transfery dysku/s|Tak|Średni czas dysku w s/transfer|Liczba|Średnia|Average_Avg. Transfery dysku/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. dysku na sek./zapis|Tak|Średni czas dysku w s/zapis|Liczba|Średnia|Average_Avg. dysku na sek./zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane Average_Bytes/s|Tak|Bajty odebrane/s|Liczba|Średnia|Odebrane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wysłane Average_Bytes/s|Tak|Bajty wysłane/s|Liczba|Średnia|Wysłane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes całkowita/s|Tak|Całkowita liczba bajtów/s|Liczba|Średnia|Average_Bytes całkowita/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current długość kolejki dysku|Tak|Bieżąca długość kolejki dysku|Liczba|Średnia|Average_Current długość kolejki dysku|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odczytu Average_Disk/s|Tak|Bajty odczytu dysku/s|Liczba|Średnia|Bajty odczytu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Tak|Odczyty dysku/s|Liczba|Średnia|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Transfery Average_Disk/s|Tak|Transfery dysku/s|Liczba|Średnia|Transfery Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty zapisu Average_Disk/s|Tak|Bajty zapisu dysku/s|Liczba|Średnia|Bajty zapisu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Disk/s|Tak|Zapisy dysku/s|Liczba|Średnia|Zapisy Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Tak|Wolne megabajty|Liczba|Średnia|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci fizycznej|Tak|Wolna pamięć fizyczna|Liczba|Średnia|Average_Free pamięci fizycznej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free miejsce w plikach stronicowania|Tak|Wolne miejsce w plikach stronicowania|Liczba|Średnia|Average_Free miejsce w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięć wirtualną|Tak|Wolna pamięć wirtualna|Liczba|Średnia|Average_Free pamięć wirtualną|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Logical/s|Tak|Bajty dysku logicznego/s|Liczba|Średnia|Bajty dysku Average_Logical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Page/s|Tak|Odczyty stron/s|Liczba|Średnia|Odczyty Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Page/s|Tak|Zapisy stron/s|Liczba|Średnia|Zapisy Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Tak|Stron/s|Liczba|Średnia|Average_Pages/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct uprzywilejowany czas|Tak|Czas uprzywilejowany PCT|Liczba|Średnia|Average_Pct uprzywilejowany czas|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_Pct|Tak|Czas użytkownika PCT|Liczba|Średnia|Czas użytkownika Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Physical/s|Tak|Bajty dysku fizycznego/s|Liczba|Średnia|Bajty dysku Average_Physical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Tak|Procesy|Liczba|Średnia|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki Average_Processor|Tak|Długość kolejki procesora|Liczba|Średnia|Długość kolejki Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Tak|Rozmiar zapisany w plikach stronicowania|Liczba|Średnia|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Tak|Łączna liczba bajtów|Liczba|Średnia|Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odebrane Average_Total|Tak|Całkowita liczba odebranych bajtów|Liczba|Średnia|Bajty odebrane Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przesyłane Average_Total bajty|Tak|Całkowita liczba przesłanych bajtów|Liczba|Średnia|Przesyłane Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizje|Tak|Łączna liczba kolizji|Liczba|Średnia|Average_Total kolizje|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane pakiety Average_Total|Tak|Całkowita liczba odebranych pakietów|Liczba|Średnia|Odebrane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total przesyłane pakiety|Tak|Całkowita liczba przesłanych pakietów|Liczba|Średnia|Average_Total przesyłane pakiety|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total błędy odbierania|Tak|Całkowita liczba błędów odbierania|Liczba|Średnia|Average_Total błędy odbierania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy wysyłania Average_Total|Tak|Całkowita liczba błędów transmisji|Liczba|Średnia|Błędy wysyłania Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Tak|Czas pracy|Liczba|Średnia|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przestrzeń wymiany Average_Used MB|Tak|Używany obszar wymiany (MB)|Liczba|Średnia|Przestrzeń wymiany Average_Used MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used w kilobajtach|Tak|Używana pamięć (w kilobajtach)|Liczba|Średnia|Pamięć Average_Used w kilobajtach|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used pamięci (MB)|Tak|Używana pamięć (MB)|Liczba|Średnia|Pamięć Average_Used pamięci (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Tak|Użytkownicy|Liczba|Średnia|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual pamięci współdzielonej|Tak|Wirtualna pamięć udostępniona|Liczba|Średnia|Average_Virtual pamięci współdzielonej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wydarzenie|Tak|Wydarzenie|Liczba|Średnia|Wydarzenie|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Puls|Tak|Puls|Liczba|Łącznie|Puls|Komputer, OSType, wersja, SourceComputerId|
|Aktualizacja|Tak|Aktualizacja|Liczba|Średnia|Aktualizacja|Komputer, produkt, klasyfikacja, UpdateState, opcjonalne, zatwierdzone|


## <a name="microsoftpeeringpeerings"></a>Microsoft. Komunikacja równorzędna/Komunikacja równorzędna

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Tak|Szybkość ruchu wychodzącego|BitsPerSecond|Średnia|Szybkość ruchu wychodzącego w bitach na sekundę|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Tak|Szybkość ruchu przychodzącego|BitsPerSecond|Średnia|Szybkość ruchu przychodzącego w bitach na sekundę|ConnectionId, SessionIp, TrafficClass|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. Komunikacja równorzędna/peeringServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|PrefixLatency|Tak|Opóźnienie prefiksu|)|Średnia|Średnie opóźnienie prefiksu|Prefiksname|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/pojemności

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|memory_metric|Tak|Memory (Pamięć)|Bajty|Średnia|Memory (pamięć). Zakres 0-3 GB dla a1, 0-5 GB dla a2, 0-10 GB dla a3, 0-25 GB dla A4, 0-50 GB dla A5 i 0-100 GB dla A6|Brak wymiarów|
|memory_thrashing_metric|Tak|Migotanie pamięci (zbiory danych)|Procent|Średnia|Średnia pamięć migotanie.|Brak wymiarów|
|qpu_high_utilization_metric|Tak|Wysokie wykorzystanie jednostek QPU|Liczba|Łącznie|QPU wysokie użycie w ciągu ostatnich minut, 1 do dużego użycia QPU, w przeciwnym razie 0|Brak wymiarów|
|QueryDuration|Tak|Czas trwania zapytania (zestawy danych)|)|Średnia|Czas trwania zapytania języka DAX w ostatnim interwale|Brak wymiarów|
|QueryPoolJobQueueLength|Tak|Długość kolejki zadań puli zapytań (zbiory danych)|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytań.|Brak wymiarów|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/przestrzenie nazw

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Łącznie|Łącznie połączeń ActiveConnections dla elementu Microsoft. Relay.|EntityName|
|ActiveListeners|Nie|ActiveListeners|Liczba|Łącznie|Łącznie ActiveListeners dla elementu Microsoft. Relay.|EntityName|
|BytesTransferred|Tak|BytesTransferred|Bajty|Łącznie|Łącznie BytesTransferred dla elementu Microsoft. Relay.|EntityName|
|ListenerConnections-ClientError|Nie|ListenerConnections-ClientError|Liczba|Łącznie|Błąd clienterror on ListenerConnections dla elementu Microsoft. Relay.|EntityName |
|ListenerConnections-ServerError|Nie|ListenerConnections-ServerError|Liczba|Łącznie|Błąd servererror on ListenerConnections dla elementu Microsoft. Relay.|EntityName |
|ListenerConnections-Success|Nie|ListenerConnections-Success|Liczba|Łącznie|Pomyślne ListenerConnections dla elementu Microsoft. Relay.|EntityName |
|ListenerConnections-TotalRequests|Nie|ListenerConnections-TotalRequests|Liczba|Łącznie|Łącznie ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|ListenerDisconnects|Nie|ListenerDisconnects|Liczba|Łącznie|Łącznie ListenerDisconnects dla elementu Microsoft. Relay.|EntityName|
|SenderConnections-ClientError|Nie|SenderConnections-ClientError|Liczba|Łącznie|Błąd clienterror on SenderConnections dla elementu Microsoft. Relay.|EntityName |
|SenderConnections-ServerError|Nie|SenderConnections-ServerError|Liczba|Łącznie|Błąd servererror on SenderConnections dla elementu Microsoft. Relay.|EntityName |
|SenderConnections-Success|Nie|SenderConnections-Success|Liczba|Łącznie|Pomyślne SenderConnections dla elementu Microsoft. Relay.|EntityName |
|SenderConnections-TotalRequests|Nie|SenderConnections-TotalRequests|Liczba|Łącznie|Łączna liczba żądań SenderConnections dla elementu Microsoft. Relay.|EntityName|
|SenderDisconnects|Nie|SenderDisconnects|Liczba|Łącznie|Łącznie SenderDisconnects dla elementu Microsoft. Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|SearchLatency|Tak|Opóźnienie wyszukiwania|Sekundy|Średnia|Średnie opóźnienie wyszukiwania dla usługi wyszukiwania|Brak wymiarów|
|SearchQueriesPerSecond|Tak|Zapytania wyszukiwania na sekundę|CountPerSecond|Średnia|Zapytania wyszukiwania na sekundę dla usługi wyszukiwania|Brak wymiarów|
|ThrottledSearchQueriesPercentage|Tak|Procent zapytań wyszukiwania z ograniczeniami|Procent|Średnia|Procent zapytań wyszukiwania, które zostały ograniczone dla usługi wyszukiwania|Brak wymiarów|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/przestrzenie nazw

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Aktywne połączenia|Nie|Aktywne połączenia|Liczba|Łącznie|Całkowita liczba aktywnych połączeń dla elementu Microsoft. ServiceBus.|Brak wymiarów|
|ActiveMessages|Nie|Liczba aktywnych komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba aktywnych komunikatów w kolejce/temacie.|EntityName|
|Połączenia connectionsclosed|Nie|Zamknięte połączenia.|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. ServiceBus.|EntityName|
|Połączenia connectionsopened|Nie|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. ServiceBus.|EntityName|
|CPUXNS|Nie|PROCESOR (przestarzałe)|Procent|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus. Ta Metryka to przestarzałe. Użyj zamiast tego metryki procesora (NamespaceCpuUsage).|Brak wymiarów|
|DeadletteredMessages|Nie|Liczba utraconych wiadomości w kolejce/temacie.|Liczba|Średnia|Liczba utraconych wiadomości w kolejce/temacie.|EntityName|
|Komunikaty incomingmessages|Tak|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. ServiceBus.|EntityName|
|Żądania incomingrequests|Tak|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla Microsoft. ServiceBus.|EntityName|
|Komunikaty|Nie|Liczba komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba komunikatów w kolejce/temacie.|EntityName|
|NamespaceCpuUsage|Nie|Procesor CPU|Procent|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus.|Brak wymiarów|
|NamespaceMemoryUsage|Nie|Użycie pamięci|Procent|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus.|Brak wymiarów|
|Komunikaty outgoingmessages|Tak|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. ServiceBus.|EntityName|
|ScheduledMessages|Nie|Liczba zaplanowanych komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba zaplanowanych komunikatów w kolejce/temacie.|EntityName|
|Błędy servererrors|Nie|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. ServiceBus.|EntityName |
|Rozmiar|Nie|Rozmiar|Bajty|Średnia|Rozmiar kolejki/tematu w bajtach.|EntityName|
|Żądania successfulrequests|Nie|Żądania pomyślne|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw|EntityName |
|ThrottledRequests|Nie|Żądania ograniczone.|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. ServiceBus.|EntityName |
|Błędy usererrors|Nie|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. ServiceBus.|EntityName |
|WSXNS|Nie|Użycie pamięci (przestarzałe)|Procent|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus. Ta Metryka jest przestarzała. Użyj zamiast tego metryki użycie pamięci (NamespaceMemoryUsage).|Brak wymiarów|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplikacje

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActualCpu|Nie|ActualCpu|Liczba|Średnia|Rzeczywiste użycie procesora CPU w rdzeniach Milli|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualMemory|Nie|ActualMemory|Bajty|Średnia|Rzeczywiste użycie pamięci w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|AllocatedCpu|Nie|AllocatedCpu|Liczba|Średnia|Procesor CPU przydzielony do tego kontenera w rdzeniach Milli|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|AllocatedMemory|Nie|AllocatedMemory|Bajty|Średnia|Pamięć przypisana do tego kontenera w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ApplicationStatus|Nie|ApplicationStatus|Liczba|Średnia|Stan aplikacji siatki Service Fabric|ApplicationName, status|
|ContainerStatus|Nie|ContainerStatus|Liczba|Średnia|Stan kontenera w aplikacji Service Fabric siatki|ApplicationName, ServiceName, CodePackageName, servicereplicaname, status|
|CpuUtilization|Nie|CpuUtilization|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|MemoryUtilization|Nie|MemoryUtilization|Procent|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|RestartCount|Nie|RestartCount|Liczba|Średnia|Liczba ponownych uruchomień kontenera w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname, CodePackageName|
|ServiceReplicaStatus|Nie|ServiceReplicaStatus|Liczba|Średnia|Stan kondycji repliki usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname|
|Stan servicestatus|Nie|Stan servicestatus|Liczba|Średnia|Stan kondycji usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/sygnalizujący

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ConnectionCount|Tak|Liczba połączeń|Liczba|Maksimum|Ilość połączenia użytkownika.|Punkt końcowy|
|InboundTraffic|Tak|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|Brak wymiarów|
|MessageCount|Tak|Liczba komunikatów|Liczba|Łącznie|Całkowita ilość komunikatów.|Brak wymiarów|
|OutboundTraffic|Tak|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący usługi|Brak wymiarów|
|SystemErrors|Tak|Błędy systemu|Procent|Maksimum|Procent błędów systemu|Brak wymiarów|
|Błędy usererrors|Tak|Błędy użytkownika|Procent|Maksimum|Procent błędów użytkownika|Brak wymiarów|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Tak|Średni procent procesora CPU|Procent|Średnia|Średni procent procesora CPU|Brak wymiarów|
|io_bytes_read|Tak|Odczytane bajty we/wy|Bajty|Średnia|Odczytane bajty we/wy|Brak wymiarów|
|io_bytes_written|Tak|Bajty we/wy zapisywane|Bajty|Średnia|Bajty we/wy zapisywane|Brak wymiarów|
|io_requests|Tak|Liczba żądań we/wy|Liczba|Średnia|Liczba żądań we/wy|Brak wymiarów|
|reserved_storage_mb|Tak|Zarezerwowane miejsce w magazynie|Liczba|Średnia|Zarezerwowane miejsce w magazynie|Brak wymiarów|
|storage_space_used_mb|Tak|Używane miejsce do magazynowania|Liczba|Średnia|Używane miejsce do magazynowania|Brak wymiarów|
|virtual_core_count|Tak|Liczba rdzeni wirtualnych|Liczba|Średnia|Liczba rdzeni wirtualnych|Brak wymiarów|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/serwery/bazy danych

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|active_queries|Tak|Aktywne zapytania|Liczba|Łącznie|Aktywne zapytania dla wszystkich grup obciążeń. Dotyczy tylko magazynów danych.|Brak wymiarów|
|allocated_data_storage|Tak|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielony magazyn danych. Nie dotyczy hurtowni danych.|Brak wymiarów|
|app_cpu_billed|Tak|Rozliczane użycie procesora przez aplikację|Liczba|Łącznie|Rozliczane użycie procesora przez aplikację. Dotyczy bezserwerowych baz danych.|Brak wymiarów|
|app_cpu_percent|Tak|Procent użycia procesora CPU aplikacji|Procent|Średnia|Procent użycia procesora CPU aplikacji. Dotyczy bezserwerowych baz danych.|Brak wymiarów|
|app_memory_percent|Tak|Procent pamięci aplikacji|Procent|Średnia|Procent pamięci aplikacji. Dotyczy bezserwerowych baz danych.|Brak wymiarów|
|base_blob_size_bytes|Tak|Rozmiar podstawowego magazynu obiektów BLOB|Bajty|Maksimum|Rozmiar podstawowego magazynu obiektów BLOB. Dotyczy baz danych w skali.|Brak wymiarów|
|blocked_by_firewall|Tak|Zablokowany przez zaporę|Liczba|Łącznie|Zablokowany przez zaporę|Brak wymiarów|
|cache_hit_percent|Tak|Procent trafień w pamięci podręcznej|Procent|Maksimum|Procent trafień w pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak wymiarów|
|cache_used_percent|Tak|Procent użycia pamięci podręcznej|Procent|Maksimum|Procent użycia pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak wymiarów|
|connection_failed|Tak|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak wymiarów|
|connection_successful|Tak|Udane połączenia|Liczba|Łącznie|Udane połączenia|Brak wymiarów|
|cpu_limit|Tak|Limit CPU|Liczba|Średnia|Limit czasu procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|cpu_used|Tak|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Brak wymiarów|
|stanu|Tak|Zakleszczenia|Liczba|Łącznie|Zakleszczenia. Nie dotyczy hurtowni danych.|Brak wymiarów|
|diff_backup_size_bytes|Tak|Różnicowy rozmiar magazynu kopii zapasowych|Bajty|Maksimum|Skumulowany, zróżnicowany rozmiar magazynu kopii zapasowych. Dotyczy baz danych opartych na rdzeń wirtualny. Nie dotyczy baz danych w skali.|Brak wymiarów|
|dtu_consumption_percent|Tak|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dtu_limit|Tak|Limit jednostek DTU|Liczba|Średnia|Limit jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dtu_used|Tak|Używane jednostki DTU|Liczba|Średnia|Użyto jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak wymiarów|
|dwu_consumption_percent|Tak|JEDNOSTEK dwu procent|Procent|Maksimum|Procent jednostek dwu. Dotyczy tylko magazynów danych.|Brak wymiarów|
|dwu_limit|Tak|Limit jednostek dwu|Liczba|Maksimum|Limit jednostek dwu. Dotyczy tylko magazynów danych.|Brak wymiarów|
|dwu_used|Tak|JEDNOSTEK dwu używane|Liczba|Maksimum|JEDNOSTEK dwu. Dotyczy tylko magazynów danych.|Brak wymiarów|
|full_backup_size_bytes|Tak|Rozmiar magazynu pełnej kopii zapasowej|Bajty|Maksimum|Łączny rozmiar magazynu pełnej kopii zapasowej. Dotyczy baz danych opartych na rdzeń wirtualny. Nie dotyczy baz danych w skali.|Brak wymiarów|
|local_tempdb_usage_percent|Tak|Procent lokalnej bazy danych tempdb|Procent|Średnia|Procent lokalnej bazy danych tempdb. Dotyczy tylko magazynów danych.|Brak wymiarów|
|log_backup_size_bytes|Tak|Rozmiar magazynu kopii zapasowej dziennika|Bajty|Maksimum|Rozmiar magazynu kopii zapasowej dziennika zbiorczego. Dotyczy baz danych opartych na rdzeń wirtualny i w skali.|Brak wymiarów|
|log_write_percent|Tak|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika. Nie dotyczy hurtowni danych.|Brak wymiarów|
|memory_usage_percent|Tak|Procent pamięci|Procent|Maksimum|Procent pamięci. Dotyczy tylko magazynów danych.|Brak wymiarów|
|physical_data_read_percent|Tak|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|Brak wymiarów|
|queued_queries|Tak|Zakolejkowane zapytania|Liczba|Łącznie|Zakolejkowane zapytania we wszystkich grupach obciążeń. Dotyczy tylko magazynów danych.|Brak wymiarów|
|sessions_percent|Tak|Procent sesji|Procent|Średnia|Procent sesji. Nie dotyczy hurtowni danych.|Brak wymiarów|
|snapshot_backup_size_bytes|Tak|Rozmiar magazynu kopii zapasowej migawek|Bajty|Maksimum|Rozmiar magazynu kopii zapasowej migawki skumulowanej. Dotyczy baz danych w skali.|Brak wymiarów|
|sqlserver_process_core_percent|Tak|Procent podstawowych procesów SQL Server|Procent|Maksimum|Użycie procesora jako procent procesu bazy danych SQL. Nie dotyczy hurtowni danych.|Brak wymiarów|
|sqlserver_process_memory_percent|Tak|Procent pamięci procesu SQL Server|Procent|Maksimum|Użycie pamięci jako procent procesu bazy danych SQL. Nie dotyczy hurtowni danych.|Brak wymiarów|
|magazyn|Tak|Używane miejsce na dane|Bajty|Maksimum|Używane miejsce na dane. Nie dotyczy hurtowni danych.|Brak wymiarów|
|storage_percent|Tak|Procent użytego miejsca na danych|Procent|Maksimum|Procent użytego miejsca na danych. Nie dotyczy magazynów danych ani baz danych w skali.|Brak wymiarów|
|tempdb_data_size|Tak|Rozmiar pliku danych tempdb kilobajtów|Liczba|Maksimum|Miejsce używane w plikach danych tempdb w kilobajtach. Nie dotyczy hurtowni danych.|Brak wymiarów|
|tempdb_log_size|Tak|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Liczba|Maksimum|Miejsce używane w pliku dziennika transakcji bazy danych tempdb w kilobajtach. Nie dotyczy hurtowni danych.|Brak wymiarów|
|tempdb_log_used_percent|Tak|Użyto dziennika% tempdb|Procent|Maksimum|Procent zajętego miejsca w pliku dziennika transakcji bazy danych tempdb. Nie dotyczy hurtowni danych.|Brak wymiarów|
|wlg_active_queries|Tak|Aktywne zapytania grupy obciążenia|Liczba|Łącznie|Aktywne zapytania w grupie obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Tak|Limity czasu zapytania grupy obciążeń|Liczba|Łącznie|Zapytania, które przekroczyły limit czasu dla grupy obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Tak|Alokacja grupy obciążeń według wartości procentowej systemu|Procent|Maksimum|Przydzielono procent zasobów względem całego systemu na grupę obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Tak|Alokacja grupy obciążeń według procentu zasobów zakończenia|Procent|Maksimum|Przydzielono procent zasobów względem określonych zasobów Cap dla każdej grupy obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Tak|Procent zasobów obowiązujących dla limitu|Procent|Maksimum|Stały limit procentu zasobów dozwolonych dla grupy obciążenia, biorąc pod uwagę efektywną minimalną stawkę procentową zasobów przydzieloną dla innych grup obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Tak|Efektywny minimalny procent zasobów|Procent|Maksimum|Minimalny procent zasobów zarezerwowanych i odizolowanych dla grupy obciążenia, biorąc pod uwagę minimalny poziom usług. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Tak|Zakolejkowane zapytania grupy obciążenia|Liczba|Łącznie|Zakolejkowane zapytania w grupie obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName, IsUserDefined|
|workers_percent|Tak|Procent pracowników|Procent|Średnia|Procent pracowników. Nie dotyczy hurtowni danych.|Brak wymiarów|
|xtp_storage_percent|Tak|Procent magazynu OLTP In-Memory|Procent|Średnia|Procent magazynu OLTP In-Memory. Nie dotyczy hurtowni danych.|Brak wymiarów|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/serwery/elasticPools

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|allocated_data_storage|Tak|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielono miejsce na danych|Brak wymiarów|
|allocated_data_storage_percent|Tak|Procent przydzielonych przestrzeni danych|Procent|Maksimum|Procent przydzielonych przestrzeni danych|Brak wymiarów|
|cpu_limit|Tak|Limit CPU|Liczba|Średnia|Limit czasu procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Brak wymiarów|
|cpu_percent|Tak|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Brak wymiarów|
|cpu_used|Tak|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Brak wymiarów|
|database_allocated_data_storage|Nie|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielono miejsce na danych|DatabaseResourceId|
|database_cpu_limit|Nie|Limit CPU|Liczba|Średnia|Limit CPU|DatabaseResourceId|
|database_cpu_percent|Nie|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|DatabaseResourceId|
|database_cpu_used|Nie|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU|DatabaseResourceId|
|database_dtu_consumption_percent|Nie|Procent użycia jednostek DTU|Procent|Średnia|Procent użycia jednostek DTU|DatabaseResourceId|
|database_eDTU_used|Nie|użyta wartość eDTU|Liczba|Średnia|użyta wartość eDTU|DatabaseResourceId|
|database_log_write_percent|Nie|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika|DatabaseResourceId|
|database_physical_data_read_percent|Nie|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|DatabaseResourceId|
|database_sessions_percent|Nie|Procent sesji|Procent|Średnia|Procent sesji|DatabaseResourceId|
|database_storage_used|Nie|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|DatabaseResourceId|
|database_workers_percent|Nie|Procent pracowników|Procent|Średnia|Procent pracowników|DatabaseResourceId|
|dtu_consumption_percent|Tak|Procent użycia jednostek DTU|Procent|Średnia|Procent jednostek DTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak wymiarów|
|eDTU_limit|Tak|limit liczby jednostek eDTU|Liczba|Średnia|limit liczby jednostek eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak wymiarów|
|eDTU_used|Tak|użyta wartość eDTU|Liczba|Średnia|użyta wartość eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak wymiarów|
|log_write_percent|Tak|Procent operacji we/wy dziennika|Procent|Średnia|Procent operacji we/wy dziennika|Brak wymiarów|
|physical_data_read_percent|Tak|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|Brak wymiarów|
|sessions_percent|Tak|Procent sesji|Procent|Średnia|Procent sesji|Brak wymiarów|
|sqlserver_process_core_percent|Tak|Procent podstawowych procesów SQL Server|Procent|Maksimum|Użycie procesora jako procent procesu bazy danych SQL. Dotyczy pul elastycznych.|Brak wymiarów|
|sqlserver_process_memory_percent|Tak|Procent pamięci procesu SQL Server|Procent|Maksimum|Użycie pamięci jako procent procesu bazy danych SQL. Dotyczy pul elastycznych.|Brak wymiarów|
|storage_limit|Tak|Maksymalny rozmiar danych|Bajty|Średnia|Maksymalny rozmiar danych|Brak wymiarów|
|storage_percent|Tak|Procent użytego miejsca na danych|Procent|Średnia|Procent użytego miejsca na danych|Brak wymiarów|
|storage_used|Tak|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|Brak wymiarów|
|tempdb_data_size|Tak|Rozmiar pliku danych tempdb kilobajtów|Liczba|Maksimum|Miejsce używane w plikach danych tempdb w kilobajtach.|Brak wymiarów|
|tempdb_log_size|Tak|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Liczba|Maksimum|Miejsce używane w pliku dziennika transakcji bazy danych tempdb w kilobajtach.|Brak wymiarów|
|tempdb_log_used_percent|Tak|Użyto dziennika% tempdb|Procent|Maksimum|Procent zajętego miejsca w pliku dziennika transakcji tempdb|Brak wymiarów|
|workers_percent|Tak|Procent pracowników|Procent|Średnia|Procent pracowników|Brak wymiarów|
|xtp_storage_percent|Tak|Procent magazynu OLTP In-Memory|Procent|Średnia|Procent magazynu OLTP In-Memory|Brak wymiarów|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje wyjście z klienta zewnętrznego z usługi Azure Storage, a także dane wychodzące na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|UsedCapacity|Nie|Używana pojemność|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont magazynu w warstwie Premium i kont usługi BLOB Storage jest to taka sama jak BlobCapacity lub FileCapacity.|Brak wymiarów|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|BlobCapacity|Nie|Pojemność obiektu BLOB|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Blob service konta magazynu w bajtach.|Blobtype, warstwa|
|BlobCount|Nie|Liczba obiektów BLOB|Liczba|Średnia|Liczba obiektów BLOB przechowywanych na koncie magazynu.|Blobtype, warstwa|
|BlobProvisionedSize|Nie|Rozmiar aprowizacji obiektów BLOB|Bajty|Średnia|Ilość miejsca w magazynie zainicjowana na Blob service konta magazynu w bajtach.|Blobtype, warstwa|
|ContainerCount|Tak|Liczba kontenerów obiektów BLOB|Liczba|Średnia|Liczba kontenerów na koncie magazynu.|Brak wymiarów|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje wyjście z klienta zewnętrznego z usługi Azure Storage, a także dane wychodzące na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|IndexCapacity|Nie|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Azure Data Lake Storage Gen2 indeks hierarchiczny.|Brak wymiarów|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje wyjście z klienta zewnętrznego z usługi Azure Storage, a także dane wychodzące na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|FileCapacity|Nie|Pojemność plików|Bajty|Średnia|Ilość miejsca w magazynie plików używanego przez konto magazynu.|Udział plików|
|FileCount|Nie|Liczba plików|Liczba|Średnia|Liczba plików na koncie magazynu.|Udział plików|
|FileShareCapacityQuota|Nie|Przydział pojemności udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udział plików|
|FileShareCount|Nie|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików na koncie magazynu.|Brak wymiarów|
|FileShareProvisionedIOPS|Nie|Liczba operacji we/wy udostępniania udziałów plików|Bajty|Średnia|Numer linii bazowej dla udziału plików w warstwie Premium na koncie magazynu plików w warstwie Premium. Ta liczba jest obliczana na podstawie rozmiaru alokowanego (przydziału) pojemności udziału.|Udział plików|
|FileShareSnapshotCount|Nie|Liczba migawek udziału plików|Liczba|Średnia|Liczba migawek znajdujących się w udziale w usłudze plików konta magazynu.|Udział plików|
|FileShareSnapshotSize|Nie|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udział plików|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, Authentication, przeudziale|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje wyjście z klienta zewnętrznego z usługi Azure Storage, a także dane wychodzące na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|QueueCapacity|Tak|Pojemność kolejki|Bajty|Średnia|Ilość magazynu kolejki używanego przez konto magazynu.|Brak wymiarów|
|QueueCount|Tak|Liczba kolejek|Liczba|Średnia|Liczba kolejek na koncie magazynu.|Brak wymiarów|
|QueueMessageCount|Tak|Liczba komunikatów w kolejce|Liczba|Średnia|Liczba niewygasłych komunikatów w kolejce na koncie magazynu.|Brak wymiarów|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Dostępność|Tak|Dostępność|Procent|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Tak|Ruch wychodzący|Bajty|Łącznie|Ilość danych wychodzących. Ta liczba obejmuje wyjście z klienta zewnętrznego z usługi Azure Storage, a także dane wychodzące na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Tak|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Tak|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Tak|Opóźnienie serwera sukcesu|)|Średnia|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|TableCapacity|Tak|Pojemność tabeli|Bajty|Średnia|Ilość magazynu tabel używanego przez konto magazynu.|Brak wymiarów|
|TableCount|Tak|Liczba tabel|Liczba|Średnia|Liczba tabel na koncie magazynu.|Brak wymiarów|
|TableEntityCount|Tak|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli na koncie magazynu.|Brak wymiarów|
|Transakcje|Tak|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Tak|Wynik synchronizacji sesji|Liczba|Średnia|Metryka, która rejestruje wartość 1 za każdym razem, gdy punkt końcowy serwera pomyślnie ukończy sesję synchronizacji z punktem końcowym w chmurze|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Tak|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Tak|Rozmiar odwołania do warstw w chmurze według aplikacji|Bajty|Łącznie|Rozmiar danych, które zostały wywołane przez aplikację|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Tak|Rozmiar odwołania do warstw w chmurze|Bajty|Łącznie|Rozmiar danych, które zostały odwołane|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Tak|Odwołanie do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Tak|Przepływność odwołań do warstw w chmurze|BytesPerSecond|Średnia|Rozmiar przepływności operacji odwoływania danych|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Tak|Stan online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Tak|Synchronizowane pliki|Liczba|Łącznie|Liczba synchronizowanych plików|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Tak|Pliki, których nie należy synchronizować|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Tak|Stan online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Tak|Odwołanie do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Tak|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Tak|Synchronizowane pliki|Liczba|Łącznie|Liczba synchronizowanych plików|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Tak|Pliki, których nie należy synchronizować|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Tak|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Tak|Synchronizowane pliki|Liczba|Łącznie|Liczba synchronizowanych plików|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Tak|Pliki, których nie należy synchronizować|Liczba|Łącznie|Nie można zsynchronizować liczby plików|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Tak|Nieudane żądania funkcji|Liczba|Łącznie|Nieudane żądania funkcji|Logicznaname, PartitionId|
|AMLCalloutInputEvents|Tak|Zdarzenia funkcji|Liczba|Łącznie|Zdarzenia funkcji|Logicznaname, PartitionId|
|AMLCalloutRequests|Tak|Żądania funkcji|Liczba|Łącznie|Żądania funkcji|Logicznaname, PartitionId|
|ConversionErrors|Tak|Błędy konwersji danych|Liczba|Łącznie|Błędy konwersji danych|Logicznaname, PartitionId|
|DeserializationError|Tak|Błędy deserializacji danych wejściowych|Liczba|Łącznie|Błędy deserializacji danych wejściowych|Logicznaname, PartitionId|
|DroppedOrAdjustedEvents|Tak|Zdarzenia poza kolejnością|Liczba|Łącznie|Zdarzenia poza kolejnością|Logicznaname, PartitionId|
|EarlyInputEvents|Tak|Wczesne zdarzenia wejściowe|Liczba|Łącznie|Wczesne zdarzenia wejściowe|Logicznaname, PartitionId|
|błędy|Tak|Błędy środowiska uruchomieniowego|Liczba|Łącznie|Błędy środowiska uruchomieniowego|Logicznaname, PartitionId|
|InputEventBytes|Tak|Bajty zdarzeń wejściowych|Bajty|Łącznie|Bajty zdarzeń wejściowych|Logicznaname, PartitionId|
|InputEvents|Tak|Zdarzenia wejściowe|Liczba|Łącznie|Zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventsSourcesBacklogged|Tak|Zaległe zdarzenia wejściowe|Liczba|Maksimum|Zaległe zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventsSourcesPerSecond|Tak|Odebrane źródła danych wejściowych|Liczba|Łącznie|Odebrane źródła danych wejściowych|Logicznaname, PartitionId|
|LateInputEvents|Tak|Opóźnione zdarzenia wejściowe|Liczba|Łącznie|Opóźnione zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputEvents|Tak|Zdarzenia wyjściowe|Liczba|Łącznie|Zdarzenia wyjściowe|Logicznaname, PartitionId|
|OutputWatermarkDelaySeconds|Tak|Opóźnienie znaku wodnego|Sekundy|Maksimum|Opóźnienie znaku wodnego|Logicznaname, PartitionId|
|ResourceUtilization|Tak|Użycie SU%|Procent|Maksimum|Użycie SU%|Logicznaname, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/obszary robocze

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|Nie|Przetworzone dane (bajty)|Bajty|Łącznie|Ilość danych przetworzonych przez zapytania|Brak wymiarów|
|BuiltinSqlPoolLoginAttempts|Nie|Próby logowania|Liczba|Łącznie|Liczba prób logowania, które zakończone lub zakończyły się niepowodzeniem|Wynik|
|BuiltinSqlPoolRequestsEnded|Nie|Zakończone żądania|Liczba|Łącznie|Liczba żądań, które zakończyły się powodzeniem, Zakończono niepowodzeniem lub zostały anulowane|Wynik|
|IntegrationActivityRunsEnded|Nie|Zakończone uruchomienia działań|Liczba|Łącznie|Liczba działań integracyjnych zakończonych powodzeniem, zakończonych niepowodzeniem lub anulowanych|Wynik, Niepowodzenie, działanie, ActivityType, potok|
|IntegrationPipelineRunsEnded|Nie|Zakończono przebiegi potoku|Liczba|Łącznie|Liczba uruchomień potoków integracji zakończonych powodzeniem, zakończonych niepowodzeniem lub anulowanych|Wynik, Niepowodzenie, potok|
|IntegrationTriggerRunsEnded|Nie|Zakończone przebiegi wyzwalacza|Liczba|Łącznie|Liczba wyzwalaczy integracji zakończonych powodzeniem, zakończonych niepowodzeniem lub anulowanych|Wynik, Niepowodzenie, wyzwalacz|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|Nie|Rdzeni wirtualnych przydzielono|Liczba|Maksimum|Przydzielono rdzeni wirtualnych dla puli Apache Spark|SubmitterId|
|BigDataPoolAllocatedMemory|Nie|Przydzielono pamięć (GB)|Liczba|Maksimum|Przydzieloną pamięć dla puli Spark Apach (GB)|SubmitterId|
|BigDataPoolApplicationsActive|Nie|Aktywne Apache Spark aplikacje|Liczba|Liczba|Łączna liczba aktywnych aplikacji puli Apache Spark|JobState|
|BigDataPoolApplicationsEnded|Nie|Zakończono Apache Spark aplikacje|Liczba|Liczba|Liczba zakończonych aplikacji puli Apache Spark|Typu zadania, wynik zadania|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/obszary robocze/xmlpools

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActiveQueries|Nie|Aktywne zapytania|Liczba|Łącznie|Aktywne zapytania. Użycie tej metryki nie zostało odfiltrowane i rozdzielenie spowoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie.|IsUserDefined|
|AdaptiveCacheHitPercent|Nie|Odsetek trafień adaptacyjnej pamięci podręcznej|Procent|Maksimum|Mierzy, jak dobre obciążenia korzystają z adaptacyjnej pamięci podręcznej. Użyj tej metryki z metryką procentu trafień w pamięci podręcznej, aby określić, czy skalować w celu uzyskania dodatkowej pojemności czy ponownie uruchamiać obciążenia, aby podręczna|Brak wymiarów|
|AdaptiveCacheUsedPercent|Nie|Procent użycia adaptacyjnej pamięci podręcznej|Procent|Maksimum|Mierzy, jak dobre obciążenia korzystają z adaptacyjnej pamięci podręcznej. Użyj tej metryki z metryką procentową wykorzystania pamięci podręcznej, aby określić, czy skalowanie ma być skalowane w celu uzyskania dodatkowej pojemności czy ponownego uruchomienia obciążeń w celu uzyskania|Brak wymiarów|
|Połączenia|Tak|Połączenia|Liczba|Łącznie|Łączna Liczba logowań do puli SQL|Wynik|
|ConnectionsBlockedByFirewall|Nie|Połączenia zablokowane przez zaporę|Liczba|Łącznie|Liczba połączeń blokowanych przez reguły zapory. Odwiedzaj ponownie zasady kontroli dostępu dla puli SQL i monitoruj te połączenia, jeśli liczba jest wysoka|Brak wymiarów|
|CPUPercent|Nie|Procent użycia procesora CPU|Procent|Maksimum|Użycie procesora CPU we wszystkich węzłach w puli SQL|Brak wymiarów|
|DWULimit|Nie|Limit jednostek dwu|Liczba|Maksimum|Cel poziomu usługi puli SQL|Brak wymiarów|
|DWUUsed|Nie|JEDNOSTEK dwu używane|Liczba|Maksimum|Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzone przez limit jednostek dwu * jednostek dwu procent|Brak wymiarów|
|DWUUsedPercent|Nie|JEDNOSTEK dwu procent użycia|Procent|Maksimum|Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzone przez przejęcie wartości procentowej procentu procesora CPU i operacji we/wy danych|Brak wymiarów|
|LocalTempDBUsedPercent|Nie|Procent użycia lokalnej bazy danych tempdb|Procent|Maksimum|Użycie lokalnej bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut.|Brak wymiarów|
|MemoryUsedPercent|Nie|Procent wykorzystania pamięci|Procent|Maksimum|Użycie pamięci we wszystkich węzłach w puli SQL|Brak wymiarów|
|QueuedQueries|Nie|Zakolejkowane zapytania|Liczba|Łącznie|Skumulowana liczba żądań umieszczonych w kolejce po osiągnięciu maksymalnego limitu współbieżności|IsUserDefined|
|wlg_effective_min_resource_percent|Tak|Efektywny minimalny procent zasobów|Procent|Minimum|Ustawienie efektywnej minimalnej wartości procentowej zasobu może uwzględniać ustawienia poziomu usług i grupy obciążeń. Efektywne min_percentage_resource można zwiększyć na niższych poziomach usługi|IsUserDefined, obciążenie|
|WLGActiveQueries|Nie|Aktywne zapytania grupy obciążenia|Liczba|Łącznie|Aktywne zapytania w grupie obciążenia. Użycie tej metryki nie zostało odfiltrowane i rozdzielenie spowoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie.|IsUserDefined, obciążenie|
|WLGActiveQueriesTimeouts|Nie|Limity czasu zapytania grupy obciążeń|Liczba|Łącznie|Zapytania dotyczące grupy obciążeń, które przekroczyły limit czasu. Limity czasu zapytania zgłoszone przez tę metrykę są wykonywane dopiero po rozpoczęciu wykonywania zapytania (nie obejmują czasu oczekiwania z powodu blokady lub oczekiwania na zasoby)|IsUserDefined, obciążenie|
|WLGAllocationByMaxResourcePercent|Nie|Alokacja grupy obciążeń według maksymalnego procentu zasobów|Procent|Maksimum|Wyświetla procentową alokację zasobów względem procentu zasobów obowiązujących dla grupy obciążenia. Ta Metryka zapewnia efektywne wykorzystanie grupy obciążeń|IsUserDefined, obciążenie|
|WLGAllocationBySystemPercent|Nie|Alokacja grupy obciążeń według wartości procentowej systemu|Procent|Maksimum|Procent alokacji zasobów względem całego systemu|IsUserDefined, obciążenie|
|WLGEffectiveCapResourcePercent|Nie|Procent zasobów obowiązujących dla limitu|Procent|Maksimum|Procent zasobów obowiązujących dla grupy obciążenia. Jeśli istnieją inne grupy obciążeń z min_percentage_resource > 0, effective_cap_percentage_resource jest obniżane proporcjonalnie|IsUserDefined, obciążenie|
|WLGQueuedQueries|Nie|Zakolejkowane zapytania grupy obciążenia|Liczba|Łącznie|Skumulowana liczba żądań umieszczonych w kolejce po osiągnięciu maksymalnego limitu współbieżności|IsUserDefined, obciążenie|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/środowiska

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Tak|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Brak wymiarów|
|IngressReceivedInvalidMessages|Tak|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytywanych ze wszystkich centrów zdarzeń lub źródeł zdarzeń usługi IoT Hub|Brak wymiarów|
|IngressReceivedMessages|Tak|Odebrane komunikaty transferu danych przychodzących|Liczba|Łącznie|Liczba komunikatów odczytanych ze wszystkich źródeł zdarzeń centrum zdarzeń lub Centrum IoT Hub|Brak wymiarów|
|IngressReceivedMessagesCountLag|Tak|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia a sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Brak wymiarów|
|IngressReceivedMessagesTimeLag|Tak|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Brak wymiarów|
|IngressStoredBytes|Tak|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressStoredEvents|Tak|Zdarzenia związane z transferem danych przychodzących|Liczba|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|WarmStorageMaxProperties|Tak|Maksymalna liczba właściwości magazynu ciepłego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Brak wymiarów|
|WarmStorageUsedProperties|Tak|Właściwości używanej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Brak wymiarów|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Tak|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzenia|Brak wymiarów|
|IngressReceivedInvalidMessages|Tak|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze źródła zdarzeń|Brak wymiarów|
|IngressReceivedMessages|Tak|Odebrane komunikaty transferu danych przychodzących|Liczba|Łącznie|Liczba komunikatów odczytanych ze źródła zdarzeń|Brak wymiarów|
|IngressReceivedMessagesCountLag|Tak|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia a sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Brak wymiarów|
|IngressReceivedMessagesTimeLag|Tak|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Brak wymiarów|
|IngressStoredBytes|Tak|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|IngressStoredEvents|Tak|Zdarzenia związane z transferem danych przychodzących|Liczba|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Brak wymiarów|
|WarmStorageMaxProperties|Tak|Maksymalna liczba właściwości magazynu ciepłego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Brak wymiarów|
|WarmStorageUsedProperties|Tak|Właściwości używanej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Brak wymiarów|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|Bajty odczytu dysku|Tak|Bajty odczytu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Brak wymiarów|
|Operacje odczytu z dysku/s|Tak|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Średnia liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|Bajty zapisu dysku|Tak|Bajty zapisu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami zapisu w okresie próbkowania.|Brak wymiarów|
|Operacje zapisu na dysku/s|Tak|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Średnia liczba operacji we/wy zapisu w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|DiskReadBytesPerSecond|Tak|Bajty odczytu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Brak wymiarów|
|DiskReadLatency|Tak|Opóźnienie odczytu dysku|)|Średnia|Całkowite opóźnienie odczytu. Suma opóźnień odczytu urządzenia i jądra.|Brak wymiarów|
|DiskReadOperations|Tak|Operacje odczytu z dysku|Liczba|Łącznie|Liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|DiskWriteBytesPerSecond|Tak|Bajty zapisu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacją zapisu w okresie próbnym.|Brak wymiarów|
|DiskWriteLatency|Tak|Opóźnienie zapisu na dysku|)|Średnia|Całkowite opóźnienie zapisu. Suma opóźnień między urządzeniem i zapisem jądra.|Brak wymiarów|
|DiskWriteOperations|Tak|Operacje zapisu na dysku|Liczba|Łącznie|Liczba operacji zapisu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak wymiarów|
|MemoryActive|Tak|Pamięć aktywna|Bajty|Średnia|Ilość pamięci używanej przez maszynę wirtualną w minionym małym oknie czasu. Jest to wartość "prawda" ilości pamięci wymaganej przez maszynę wirtualną. Dodatkowa niewykorzystana pamięć może zostać zastąpiona lub zaprowadzona bez wpływu na wydajność gościa.|Brak wymiarów|
|MemoryGranted|Tak|Przydzielone pamięci|Bajty|Średnia|Ilość pamięci przydzielonej maszynie wirtualnej przez hosta. Pamięć nie jest przydzielana do hosta, dopóki nie zostanie ona nałożona jeden raz, a przyznana pamięć może zostać zamieniony lub zaznaczono w dymku, jeśli VMkernel wymaga pamięci.|Brak wymiarów|
|MemoryUsed|Tak|Używana pamięć|Bajty|Średnia|Ilość pamięci maszyny używanej przez maszynę wirtualną.|Brak wymiarów|
|Sieć — wejście|Tak|Sieć — wejście|Bajty|Łącznie|Całkowita przepływność sieci dla odebranego ruchu.|Brak wymiarów|
|Sieć — wyjście|Tak|Sieć — wyjście|Bajty|Łącznie|Całkowita przepustowość sieci przesyłanych danych.|Brak wymiarów|
|NetworkInBytesPerSecond|Tak|Sieć w bajtach/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla odebranego ruchu.|Brak wymiarów|
|NetworkOutBytesPerSecond|Tak|Bajty wychodzące z sieci/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla przesyłanego ruchu.|Brak wymiarów|
|Procentowe użycie procesora CPU|Tak|Procentowe użycie procesora CPU|Procent|Średnia|Użycie procesora CPU. Ta wartość jest raportowana przy użyciu 100% reprezentujących wszystkie rdzenie procesora w systemie. Przykładowo 2-kierunkowa maszyna wirtualna używająca 50% systemu z czterema rdzeniem korzysta całkowicie z dwóch rdzeni.|Brak wymiarów|
|PercentageCpuReady|Tak|Procent gotowych do użycia procesora|)|Łącznie|Czas gotowości to czas oczekiwania, aż procesor CPU stanie się dostępny w okresie ostatniej aktualizacji.|Brak wymiarów|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|ActiveRequests|Tak|Aktywne żądania (przestarzałe)|Liczba|Łącznie|Aktywne żądania|Wystąpienie|
|AverageResponseTime|Tak|Średni czas odpowiedzi (przestarzałe)|Sekundy|Średnia|Średni czas wykonywania żądań w sekundach przez fronton.|Wystąpienie|
|BytesReceived|Tak|Dane w|Bajty|Łącznie|Średnia przepustowość przychodząca używana przez wszystkie frontony w MiB.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Średnia przepustowość przychodząca używana na całym frontonie w MiB.|Wystąpienie|
|CpuPercentage|Tak|Procent użycia procesora CPU|Procent|Średnia|Średni procesor używany we wszystkich wystąpieniach frontonu.|Wystąpienie|
|DiskQueueLength|Tak|Długość kolejki dysku|Liczba|Średnia|Średnia liczba żądań odczytu i zapisu, które zostały dodane do kolejki w magazynie. Duża długość kolejki dysku to wskazanie aplikacji, która może ulec spowolnieniu z powodu nadmiernej liczby operacji we/wy dysku.|Wystąpienie|
|Http101|Tak|Http 101|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 101.|Wystąpienie|
|Http2xx|Tak|Http 2xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 200, ale < 300.|Wystąpienie|
|Http3xx|Tak|Http 3xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 300, ale < 400.|Wystąpienie|
|Http401|Tak|HTTP 401|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 401.|Wystąpienie|
|Http403|Tak|HTTP 403|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 403.|Wystąpienie|
|Http404|Tak|HTTP 404|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 404.|Wystąpienie|
|Http406|Tak|Http 406|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 406.|Wystąpienie|
|Http4xx|Tak|Http 4xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 400, ale < 500.|Wystąpienie|
|Http5xx|Tak|Błędy serwera http|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 500, ale < 600.|Wystąpienie|
|HttpQueueLength|Tak|Długość kolejki http|Liczba|Średnia|Średnia liczba żądań HTTP, które musiały zostać obsłużone w kolejce przed ich ukończeniem. Duża lub większa długość kolejki HTTP jest objawem planu pod dużym obciążeniem.|Wystąpienie|
|LargeAppServicePlanInstances|Tak|Duże App Service planowanie procesów roboczych|Liczba|Średnia|Duże App Service planowanie procesów roboczych|Brak wymiarów|
|MediumAppServicePlanInstances|Tak|Średni App Service pracowników planu|Liczba|Średnia|Średni App Service pracowników planu|Brak wymiarów|
|MemoryPercentage|Tak|Procent pamięci|Procent|Średnia|Średnia pamięć użyta we wszystkich wystąpieniach frontonu.|Wystąpienie|
|Żądania|Tak|Żądania|Liczba|Łącznie|Całkowita liczba żądań niezależnie od ich wyników w kodzie stanu HTTP.|Wystąpienie|
|SmallAppServicePlanInstances|Tak|Niewielka App Service planowanie procesów roboczych|Liczba|Średnia|Niewielka App Service planowanie procesów roboczych|Brak wymiarów|
|TotalFrontEnds|Tak|Łączne frontony|Liczba|Średnia|Łączne frontony|Brak wymiarów|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|CpuPercentage|Tak|Procent użycia procesora CPU|Procent|Średnia|Średni procesor używany przez wszystkie wystąpienia puli procesów roboczych.|Wystąpienie|
|MemoryPercentage|Tak|Procent pamięci|Procent|Średnia|Średnia pamięć użyta we wszystkich wystąpieniach puli procesów roboczych.|Wystąpienie|
|WorkersAvailable|Tak|Dostępni pracownicy|Liczba|Średnia|Dostępni pracownicy|Brak wymiarów|
|Łączna|Tak|Łączna liczba procesów roboczych|Liczba|Średnia|Łączna liczba procesów roboczych|Brak wymiarów|
|WorkersUsed|Tak|Używani pracownicy|Liczba|Średnia|Używani pracownicy|Brak wymiarów|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/dopuszczalna

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|BytesReceived|Tak|Dane w|Bajty|Łącznie|Średnia przepustowość przychodząca używana przez wszystkie wystąpienia planu.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Średnia wychodząca przepustowość używana we wszystkich wystąpieniach planu.|Wystąpienie|
|CpuPercentage|Tak|Procent użycia procesora CPU|Procent|Średnia|Średni procesor używany przez wszystkie wystąpienia planu.|Wystąpienie|
|DiskQueueLength|Tak|Długość kolejki dysku|Liczba|Średnia|Średnia liczba żądań odczytu i zapisu, które zostały dodane do kolejki w magazynie. Duża długość kolejki dysku to wskazanie aplikacji, która może ulec spowolnieniu z powodu nadmiernej liczby operacji we/wy dysku.|Wystąpienie|
|HttpQueueLength|Tak|Długość kolejki http|Liczba|Średnia|Średnia liczba żądań HTTP, które musiały zostać obsłużone w kolejce przed ich ukończeniem. Duża lub większa długość kolejki HTTP jest objawem planu pod dużym obciążeniem.|Wystąpienie|
|MemoryPercentage|Tak|Procent pamięci|Procent|Średnia|Średnia pamięć użyta we wszystkich wystąpieniach planu.|Wystąpienie|
|SocketInboundAll|Tak|SocketInboundAll|Liczba|Średnia|SocketInboundAll|Wystąpienie|
|SocketLoopback|Tak|SocketLoopback|Liczba|Średnia|SocketLoopback|Wystąpienie|
|SocketOutboundAll|Tak|SocketOutboundAll|Liczba|Średnia|SocketOutboundAll|Wystąpienie|
|SocketOutboundEstablished|Tak|SocketOutboundEstablished|Liczba|Średnia|SocketOutboundEstablished|Wystąpienie|
|SocketOutboundTimeWait|Tak|SocketOutboundTimeWait|Liczba|Średnia|SocketOutboundTimeWait|Wystąpienie|
|TcpCloseWait|Tak|Oczekiwanie na zamknięcie protokołu TCP|Liczba|Średnia|Oczekiwanie na zamknięcie protokołu TCP|Wystąpienie|
|TcpClosing|Tak|Zamykanie protokołu TCP|Liczba|Średnia|Zamykanie protokołu TCP|Wystąpienie|
|TcpEstablished|Tak|Nawiązano ruch TCP|Liczba|Średnia|Nawiązano ruch TCP|Wystąpienie|
|TcpFinWait1|Tak|Oczekiwanie na TCP FIN 1|Liczba|Średnia|Oczekiwanie na TCP FIN 1|Wystąpienie|
|TcpFinWait2|Tak|Oczekiwanie na TCP fin 2|Liczba|Średnia|Oczekiwanie na TCP fin 2|Wystąpienie|
|TcpLastAck|Tak|Ostatnie potwierdzenie TCP|Liczba|Średnia|Ostatnie potwierdzenie TCP|Wystąpienie|
|TcpSynReceived|Tak|Odebrano pakiet TCP syn|Liczba|Średnia|Odebrano pakiet TCP syn|Wystąpienie|
|TcpSynSent|Tak|Wysłano pakiet TCP syn|Liczba|Średnia|Wysłano pakiet TCP syn|Wystąpienie|
|TcpTimeWait|Tak|Czas oczekiwania TCP|Liczba|Średnia|Czas oczekiwania TCP|Wystąpienie|


## <a name="microsoftwebsites"></a>Microsoft. Web/witryny

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AppConnections|Tak|Połączenia|Liczba|Średnia|Liczba powiązanych gniazd istniejących w piaskownicy (w3wp.exe i jej procesów podrzędnych). Powiązane gniazdo jest tworzone przez wywoływanie interfejsów API bind ()/Connect () i pozostanie do momentu zamknięcia wskazanego gniazda przy użyciu funkcji CloseHandle ()/closesocket ().|Wystąpienie|
|AverageMemoryWorkingSet|Tak|Średni zestaw roboczy pamięci|Bajty|Średnia|Średnia ilość pamięci używanej przez aplikację w megabajtach (MiB).|Wystąpienie|
|AverageResponseTime|Tak|Średni czas odpowiedzi (przestarzałe)|Sekundy|Średnia|Średni czas, w którym aplikacja będzie obsługiwała żądania (w sekundach).|Wystąpienie|
|BytesReceived|Tak|Dane w|Bajty|Łącznie|Ilość przychodzącej przepustowości zużywanej przez aplikację w usłudze MiB.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Ilość wychodzącej przepustowości zużywanej przez aplikację w usłudze MiB.|Wystąpienie|
|CpuTime|Tak|Czas procesora CPU|Sekundy|Łącznie|Ilość procesora CPU zużywanego przez aplikację w sekundach. Aby uzyskać więcej informacji na temat tej metryki. Zobacz https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (czas procesora CPU a procent procesora).|Wystąpienie|
|CurrentAssemblies|Tak|Bieżące zestawy|Liczba|Średnia|Bieżąca liczba zestawów załadowanych we wszystkich domenach aplikacji.|Wystąpienie|
|FileSystemUsage|Tak|Użycie systemu plików|Bajty|Średnia|Procent przydziału systemu plików zużywany przez aplikację.|Brak wymiarów|
|FunctionExecutionCount|Tak|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|FunctionExecutionUnits|Tak|Jednostki wykonywania funkcji|Liczba|Łącznie|Jednostki wykonywania funkcji|Wystąpienie|
|Gen0Collections|Tak|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Liczba obiektów generacji 0, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji. Wyższa generacja operacje odzyskiwania pamięci obejmuje wszystkie niższe operacje odzyskiwania pamięci generacji.|Wystąpienie|
|Gen1Collections|Tak|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Liczba obiektów generacji 1, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji. Wyższa generacja operacje odzyskiwania pamięci obejmuje wszystkie niższe operacje odzyskiwania pamięci generacji.|Wystąpienie|
|Gen2Collections|Tak|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Liczba obiektów generacji 2, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji.|Wystąpienie|
|Handles|Tak|Liczba dojść|Liczba|Średnia|Łączna liczba dojść aktualnie otwartych przez proces aplikacji.|Wystąpienie|
|HealthCheckStatus|Tak|Stan sprawdzania kondycji|Liczba|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|Http101|Tak|Http 101|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 101.|Wystąpienie|
|Http2xx|Tak|Http 2xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 200, ale < 300.|Wystąpienie|
|Http3xx|Tak|Http 3xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 300, ale < 400.|Wystąpienie|
|Http401|Tak|HTTP 401|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 401.|Wystąpienie|
|Http403|Tak|HTTP 403|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 403.|Wystąpienie|
|Http404|Tak|HTTP 404|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 404.|Wystąpienie|
|Http406|Tak|Http 406|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 406.|Wystąpienie|
|Http4xx|Tak|Http 4xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 400, ale < 500.|Wystąpienie|
|Http5xx|Tak|Błędy serwera http|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 500, ale < 600.|Wystąpienie|
|HttpResponseTime|Tak|Czas odpowiedzi|Sekundy|Średnia|Czas potrzebny na obsługę żądań w aplikacji (w sekundach).|Wystąpienie|
|IoOtherBytesPerSecond|Tak|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wysyła bajty do operacji we/wy, które nie obejmują danych, takich jak operacje sterowania.|Wystąpienie|
|IoOtherOperationsPerSecond|Tak|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wysyła operacje we/wy, które nie są operacjami odczytu lub zapisu.|Wystąpienie|
|IoReadBytesPerSecond|Tak|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji odczytuje bajty z operacji we/wy.|Wystąpienie|
|IoReadOperationsPerSecond|Tak|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje we/wy odczytu.|Wystąpienie|
|IoWriteBytesPerSecond|Tak|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji zapisuje bajty w operacjach we/wy.|Wystąpienie|
|IoWriteOperationsPerSecond|Tak|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wysyła operacje we/wy zapisu.|Wystąpienie|
|MemoryWorkingSet|Tak|Zestaw roboczy pamięci|Bajty|Średnia|Bieżąca ilość pamięci używanej przez aplikację w usłudze MiB.|Wystąpienie|
|PrivateBytes|Tak|Bajty prywatne|Bajty|Średnia|Bajty prywatne to bieżący rozmiar (w bajtach) pamięci przydzielonej przez proces aplikacji, która nie może być współużytkowana z innymi procesami.|Wystąpienie|
|Żądania|Tak|Żądania|Liczba|Łącznie|Całkowita liczba żądań niezależnie od ich wyników w kodzie stanu HTTP.|Wystąpienie|
|RequestsInApplicationQueue|Tak|Żądania w kolejce aplikacji|Liczba|Średnia|Liczba żądań w kolejce żądań aplikacji.|Wystąpienie|
|Wątki|Tak|Liczba wątków|Liczba|Średnia|Liczba wątków aktywnych obecnie w procesie aplikacji.|Wystąpienie|
|TotalAppDomains|Tak|Łączna liczba domen aplikacji|Liczba|Średnia|Bieżąca liczba domen aplikacji załadowanych w tej aplikacji.|Wystąpienie|
|TotalAppDomainsUnloaded|Tak|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba elementów AppDomain zwolnionych od początku aplikacji.|Wystąpienie|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/miejsca

|Metryka|Eksportować za pomocą ustawień diagnostycznych?|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|---|
|AppConnections|Tak|Połączenia|Liczba|Średnia|Liczba powiązanych gniazd istniejących w piaskownicy (w3wp.exe i jej procesów podrzędnych). Powiązane gniazdo jest tworzone przez wywoływanie interfejsów API bind ()/Connect () i pozostanie do momentu zamknięcia wskazanego gniazda przy użyciu funkcji CloseHandle ()/closesocket ().|Wystąpienie|
|AverageMemoryWorkingSet|Tak|Średni zestaw roboczy pamięci|Bajty|Średnia|Średnia ilość pamięci używanej przez aplikację w megabajtach (MiB).|Wystąpienie|
|AverageResponseTime|Tak|Średni czas odpowiedzi (przestarzałe)|Sekundy|Średnia|Średni czas, w którym aplikacja będzie obsługiwała żądania (w sekundach).|Wystąpienie|
|BytesReceived|Tak|Dane w|Bajty|Łącznie|Ilość przychodzącej przepustowości zużywanej przez aplikację w usłudze MiB.|Wystąpienie|
|BytesSent|Tak|Dane wychodzące|Bajty|Łącznie|Ilość wychodzącej przepustowości zużywanej przez aplikację w usłudze MiB.|Wystąpienie|
|CpuTime|Tak|Czas procesora CPU|Sekundy|Łącznie|Ilość procesora CPU zużywanego przez aplikację w sekundach. Aby uzyskać więcej informacji na temat tej metryki. Zobacz https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (czas procesora CPU a procent procesora).|Wystąpienie|
|CurrentAssemblies|Tak|Bieżące zestawy|Liczba|Średnia|Bieżąca liczba zestawów załadowanych we wszystkich domenach aplikacji.|Wystąpienie|
|FileSystemUsage|Tak|Użycie systemu plików|Bajty|Średnia|Procent przydziału systemu plików zużywany przez aplikację.|Brak wymiarów|
|FunctionExecutionCount|Tak|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|FunctionExecutionUnits|Tak|Jednostki wykonywania funkcji|Liczba|Łącznie|Jednostki wykonywania funkcji|Wystąpienie|
|Gen0Collections|Tak|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Liczba obiektów generacji 0, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji. Wyższa generacja operacje odzyskiwania pamięci obejmuje wszystkie niższe operacje odzyskiwania pamięci generacji.|Wystąpienie|
|Gen1Collections|Tak|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Liczba obiektów generacji 1, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji. Wyższa generacja operacje odzyskiwania pamięci obejmuje wszystkie niższe operacje odzyskiwania pamięci generacji.|Wystąpienie|
|Gen2Collections|Tak|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Liczba obiektów generacji 2, które są zbierane jako elementy bezużyteczne od momentu rozpoczęcia procesu aplikacji.|Wystąpienie|
|Handles|Tak|Liczba dojść|Liczba|Średnia|Łączna liczba dojść aktualnie otwartych przez proces aplikacji.|Wystąpienie|
|HealthCheckStatus|Tak|Stan sprawdzania kondycji|Liczba|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|Http101|Tak|Http 101|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 101.|Wystąpienie|
|Http2xx|Tak|Http 2xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 200, ale < 300.|Wystąpienie|
|Http3xx|Tak|Http 3xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 300, ale < 400.|Wystąpienie|
|Http401|Tak|HTTP 401|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 401.|Wystąpienie|
|Http403|Tak|HTTP 403|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 403.|Wystąpienie|
|Http404|Tak|HTTP 404|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 404.|Wystąpienie|
|Http406|Tak|Http 406|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP 406.|Wystąpienie|
|Http4xx|Tak|Http 4xx|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 400, ale < 500.|Wystąpienie|
|Http5xx|Tak|Błędy serwera http|Liczba|Łącznie|Liczba żądań, które wynikają z kodu stanu HTTP = 500, ale < 600.|Wystąpienie|
|HttpResponseTime|Tak|Czas odpowiedzi|Sekundy|Średnia|Czas potrzebny na obsługę żądań w aplikacji (w sekundach).|Wystąpienie|
|IoOtherBytesPerSecond|Tak|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wysyła bajty do operacji we/wy, które nie obejmują danych, takich jak operacje sterowania.|Wystąpienie|
|IoOtherOperationsPerSecond|Tak|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wysyła operacje we/wy, które nie są operacjami odczytu lub zapisu.|Wystąpienie|
|IoReadBytesPerSecond|Tak|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji odczytuje bajty z operacji we/wy.|Wystąpienie|
|IoReadOperationsPerSecond|Tak|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wystawia operacje we/wy odczytu.|Wystąpienie|
|IoWriteBytesPerSecond|Tak|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji zapisuje bajty w operacjach we/wy.|Wystąpienie|
|IoWriteOperationsPerSecond|Tak|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Szybkość, z jaką proces aplikacji wysyła operacje we/wy zapisu.|Wystąpienie|
|MemoryWorkingSet|Tak|Zestaw roboczy pamięci|Bajty|Średnia|Bieżąca ilość pamięci używanej przez aplikację w usłudze MiB.|Wystąpienie|
|PrivateBytes|Tak|Bajty prywatne|Bajty|Średnia|Bajty prywatne to bieżący rozmiar (w bajtach) pamięci przydzielonej przez proces aplikacji, która nie może być współużytkowana z innymi procesami.|Wystąpienie|
|Żądania|Tak|Żądania|Liczba|Łącznie|Całkowita liczba żądań niezależnie od ich wyników w kodzie stanu HTTP.|Wystąpienie|
|RequestsInApplicationQueue|Tak|Żądania w kolejce aplikacji|Liczba|Średnia|Liczba żądań w kolejce żądań aplikacji.|Wystąpienie|
|Wątki|Tak|Liczba wątków|Liczba|Średnia|Liczba wątków aktywnych obecnie w procesie aplikacji.|Wystąpienie|
|TotalAppDomains|Tak|Łączna liczba domen aplikacji|Liczba|Średnia|Bieżąca liczba domen aplikacji załadowanych w tej aplikacji.|Wystąpienie|
|TotalAppDomainsUnloaded|Tak|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba elementów AppDomain zwolnionych od początku aplikacji.|Wystąpienie|


## <a name="next-steps"></a>Następne kroki

- [Przeczytaj informacje o metrykach w Azure Monitor](data-platform.md)
- [Tworzenie alertów dotyczących metryk](alerts-overview.md)
- [Eksportowanie metryk do magazynu, centrum zdarzeń lub Log Analytics](platform-logs-overview.md)
