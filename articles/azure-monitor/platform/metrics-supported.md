---
title: Azure Monitor obsługiwane metryki według typu zasobu
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 3d7ab9d4e7a7c560fa05bdc06c7d1c357a2c2767
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196630"
---
# <a name="supported-metrics-with-azure-monitor"></a>Obsługiwane metryki z Azure Monitor

> [!NOTE]
> Ta lista jest generowana automatycznie z poziomu interfejsu API REST metryk Azure Monitor. Wszelkie modyfikacje wprowadzone do tej listy za pośrednictwem usługi GitHub mogą być zapisywane bez ostrzeżenia. Skontaktuj się z autorem tego artykułu, aby uzyskać szczegółowe informacje na temat sposobu wprowadzania trwałych aktualizacji.

Azure Monitor oferuje kilka sposobów współpracy z metrykami, w tym wykresów w portalu, uzyskiwania do nich dostępu za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. 

Ten artykuł to kompletna lista wszystkich platform (które są zbierane automatycznie), które są obecnie dostępne w ramach skonsolidowanego potoku metryki Azure Monitor. Lista została ostatnio zaktualizowana 27 marca, 2020. Metryki zostały zmienione lub dodane po tej dacie mogą nie być wyświetlane poniżej. Aby wykonać zapytanie o i uzyskać dostęp do listy metryk programowo, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Inne metryki, których nie ma na tej liście, mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

Metryki są zorganizowane według dostawców zasobów i typów zasobów. Aby uzyskać listę usług i dostawców zasobów, które należą do nich, zobacz [dostawcy zasobów dla usług platformy Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 


## <a name="guest-os-metrics"></a>Metryki systemu operacyjnego gościa

Metryki dla systemu operacyjnego gościa (systemu operacyjnego gościa) działającego w usłudze Azure Virtual Machines, Service Fabric i Cloud Services **nie** są wyświetlane w tym miejscu. Zamiast tego metryki wydajności systemu operacyjnego gościa muszą być zbierane za pomocą co najmniej jednego agenta, który działa w systemie lub w ramach systemu operacyjnego gościa.  Metryki systemu operacyjnego gościa obejmują liczniki wydajności, które śledzą użycie procesora CPU gościa lub pamięci, z których oba są często używane do automatycznego skalowania lub generowania alertów.  Korzystając z [rozszerzenia Diagnostyka Azure](diagnostics-extension-overview.md), można wysyłać metryki wydajności systemu operacyjnego gościa do tej samej bazy danych, w której są przechowywane metryki platformy. Kieruje metryki systemu operacyjnego gościa za pomocą interfejsu API [metryk niestandardowych](metrics-custom-overview.md) . Następnie możesz wykresów, alertów i w inny sposób używać metryk systemu operacyjnego gościa, takich jak metryki platformy. Aby uzyskać więcej informacji, zobacz [Omówienie agentów monitorowania](agents-overview.md).    

## <a name="routing-platform-metrics-to-other-locations"></a>Metryki platformy routingu do innych lokalizacji

[Ustawienia diagnostyczne](diagnostic-settings.md) umożliwiają kierowanie metryk platformy do usługi Azure Storage, dzienników Azure monitor (a tym samym log Analytics) i centrów zdarzeń.  

Istnieją pewne ograniczenia dotyczące tego, co może być kierowane i formularz, w którym są przechowywane. 
- Nie wszystkie metryki są eksportowane do innych lokalizacji. Aby uzyskać listę metryk platformy, które można eksportować za pośrednictwem ustawień diagnostycznych, zobacz [ten artykuł](metrics-supported-export-diagnostic-settings.md).

- Wysyłanie metryk wielowymiarowych do innych lokalizacji za pośrednictwem ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
*Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|qpu_metric|QPU|Liczba|Średnia|QPU. Zakres 0-100 dla S1, 0-200 dla S2 i 0-400 dla S4|ServerResourceType|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Memory (pamięć). Zakres 0-25 GB dla S1, 0-50 GB dla S2 i 0-100 GB dla S4|ServerResourceType|
|private_bytes_metric|Bajty prywatne|Bajty|Średnia|Bajty prywatne.|ServerResourceType|
|virtual_bytes_metric|Bajty wirtualne|Bajty|Średnia|Bajty wirtualne.|ServerResourceType|
|TotalConnectionRequests|Łączna liczba żądań połączenia|Liczba|Średnia|Łączna liczba żądań połączenia. Są to wejścia.|ServerResourceType|
|SuccessfullConnectionsPerSec|Udane połączenia na sekundę|CountPerSecond|Średnia|Szybkość pomyślnych połączeń.|ServerResourceType|
|TotalConnectionFailures|Łączna liczba błędów połączenia|Liczba|Średnia|Łączna liczba nieudanych prób połączenia.|ServerResourceType|
|CurrentUserSessions|Bieżące sesje użytkowników|Liczba|Średnia|Bieżąca liczba ustanowionych sesji użytkowników.|ServerResourceType|
|QueryPoolBusyThreads|Wątki zajęte w puli zapytań|Liczba|Średnia|Liczba zajętych wątków w puli wątków zapytań.|ServerResourceType|
|CommandPoolJobQueueLength|Długość kolejki zadań puli poleceń|Liczba|Średnia|Liczba zadań w kolejce puli wątków poleceń.|ServerResourceType|
|ProcessingPoolJobQueueLength|Długość kolejki zadań puli przetwarzania|Liczba|Średnia|Liczba zadań innych niż zadania we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|Wartości CurrentConnections|Połączenie: bieżące połączenia|Liczba|Średnia|Bieżąca liczba ustanowionych połączeń klientów.|ServerResourceType|
|CleanerCurrentPrice|Pamięć: bieżąca cena oczyszczarki|Liczba|Średnia|Bieżąca cena pamięci, $/Byte/Time, znormalizowana do 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Pamięć: zmniejszanie ilości pamięci czyszczącej|Bajty|Średnia|Ilość pamięci, w bajtach, podlegająca przeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|CleanerMemoryNonshrinkable|Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia|Bajty|Średnia|Ilość pamięci (w bajtach), która nie podlega wyprzeczyszczaniu przez oczyszczarkę w tle.|ServerResourceType|
|MemoryUsage|Pamięć: użycie pamięci|Bajty|Średnia|Użycie pamięci przez proces serwera jako używany do obliczania ceny pamięci czyszczącej. Równa się licznikowi Process\PrivateBytes i rozmiarowi danych mapowanych na pamięć, ignorowanie wszelkich pamięci, które zostały zamapowane lub przydzielone przez aparat analityczny w pamięci xVelocity (VertiPaq) przekraczający limit pamięci aparatu xVelocity.|ServerResourceType|
|MemoryLimitHard|Pamięć: sztywny limit pamięci|Bajty|Średnia|Limit pamięci twardej z pliku konfiguracji.|ServerResourceType|
|MemoryLimitHigh|Pamięć: limit pamięci jest wysoki|Bajty|Średnia|Górny limit pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitLow|Pamięć: limit pamięci — niski|Bajty|Średnia|Limit braku pamięci z pliku konfiguracji.|ServerResourceType|
|MemoryLimitVertiPaq|Pamięć: limit pamięci — tryb VertiPaq|Bajty|Średnia|Limit w pamięci z pliku konfiguracji.|ServerResourceType|
|Limit przydziału|Pamięć: limit przydziału|Bajty|Średnia|Bieżący limit pamięci (w bajtach). Przydział pamięci jest również nazywany przydziałem pamięci lub rezerwacją pamięci.|ServerResourceType|
|QuotaBlocked|Pamięć: zablokowany limit przydziału|Liczba|Średnia|Bieżąca liczba żądań przydziałów blokowanych do momentu zwolnienia innych limitów przydziału pamięci.|ServerResourceType|
|VertiPaqNonpaged|Pamięć: VertiPaq niestronicowana|Bajty|Średnia|Bajty pamięci zablokowane w zestawie roboczym do użycia przez aparat w pamięci.|ServerResourceType|
|VertiPaqPaged|Pamięć: stronicowana na stronie VertiPaq|Bajty|Średnia|Bajty stronicowanej pamięci w użyciu dla danych w pamięci.|ServerResourceType|
|RowsReadPerSec|Przetwarzanie: odczytane wiersze na sekundę|CountPerSecond|Średnia|Współczynnik wierszy odczytanych ze wszystkich relacyjnych baz danych.|ServerResourceType|
|RowsConvertedPerSec|Przetwarzanie: przekonwertowane wiersze na sekundę|CountPerSecond|Średnia|Szybkość wierszy przekonwertowanych podczas przetwarzania.|ServerResourceType|
|RowsWrittenPerSec|Przetwarzanie: liczba wierszy na sekundę|CountPerSecond|Średnia|Częstotliwość wierszy zapisywana podczas przetwarzania.|ServerResourceType|
|CommandPoolBusyThreads|Wątki: zajęte wątki w puli poleceń|Liczba|Średnia|Liczba zajętych wątków w puli wątków poleceń.|ServerResourceType|
|CommandPoolIdleThreads|Wątki: bezczynne wątki w puli poleceń|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków poleceń.|ServerResourceType|
|LongParsingBusyThreads|Wątki: zajęte wątki o długotrwałej analizie|Liczba|Średnia|Liczba zajętych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingIdleThreads|Wątki: długotrwałe wątki bezczynne analizy|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o długotrwałej analizie.|ServerResourceType|
|LongParsingJobQueueLength|Wątki: Długość kolejki zadań o długotrwałej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o długotrwałej analizie.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Wątki: zajęte wątki innych niż we/wy puli przetwarzania|Liczba|Średnia|Liczba wątków uruchamiających zadania inne niż we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Wątki: Długość kolejki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba zadań we/wy w kolejce puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Wątki: wątki zadań we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Wątki: bezczynne wątki inne niż we/wy puli przetwarzania|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków przetwarzania dedykowanych dla zadań innych niż zadania we/wy.|ServerResourceType|
|QueryPoolIdleThreads|Wątki: bezczynne wątki puli zapytań|Liczba|Średnia|Liczba bezczynnych wątków dla zadań we/wy w puli wątków przetwarzania.|ServerResourceType|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytań.|ServerResourceType|
|ShortParsingBusyThreads|Wątki: zajęte wątki z krótkim analizowaniem|Liczba|Średnia|Liczba zajętych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingIdleThreads|Wątki: bezczynne wątki z krótkim analizowaniem|Liczba|Średnia|Liczba bezczynnych wątków w puli wątków o krótkiej analizie.|ServerResourceType|
|ShortParsingJobQueueLength|Wątki: Długość kolejki zadań o krótkiej analizie|Liczba|Średnia|Liczba zadań w kolejce puli wątków o krótkiej analizie.|ServerResourceType|
|memory_thrashing_metric|Przeładowywanie pamięci|Wartość procentowa|Średnia|Średnia pamięć migotanie.|ServerResourceType|
|mashup_engine_qpu_metric|M QPU aparatu|Liczba|Średnia|QPU użycie przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_memory_metric|Pamięć aparatu M|Bajty|Średnia|Użycie pamięci przez procesy aparatu mashupów|ServerResourceType|
|mashup_engine_private_bytes_metric|Liczba prywatnych bajtów aparatu M|Bajty|Średnia|Użycie bajtów prywatnych przez procesy aparatu mashupów.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Liczba bajtów wirtualnych aparatu M|Bajty|Średnia|Użycie bajtów wirtualnych przez procesy aparatu mashupów.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalRequests|Łączna liczba żądań bramy (przestarzałe)|Liczba|Łącznie|Liczba żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|Żądania successfulrequests|Pomyślne żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba pomyślnych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|UnauthorizedRequests|Nieautoryzowane żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba nieautoryzowanych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|FailedRequests|Nieudane żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba błędów w żądaniach bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|OtherRequests|Inne żądania bramy (przestarzałe)|Liczba|Łącznie|Liczba innych żądań bramy — zamiast tego użyj metryki żądania wielowymiarowego z wymiarem GatewayResponseCodeCategory|Lokalizacja, nazwa hosta|
|Czas trwania|Całkowity czas trwania żądań bramy|)|Średnia|Całkowity czas trwania żądań bramy w milisekundach|Lokalizacja, nazwa hosta|
|BackendDuration|Czas trwania żądań wewnętrznej bazy danych|)|Średnia|Czas trwania żądań zaplecza w milisekundach|Lokalizacja, nazwa hosta|
|Pojemność|Pojemność|Wartość procentowa|Średnia|Metryka wykorzystania dla usługi ApiManagement|Lokalizacja|
|EventHubTotalEvents|Łączna liczba zdarzeń EventHub|Liczba|Łącznie|Liczba zdarzeń wysyłanych do centrum EventHub|Lokalizacja|
|EventHubSuccessfulEvents|Pomyślne zdarzenia EventHub|Liczba|Łącznie|Liczba pomyślnych zdarzeń EventHub|Lokalizacja|
|EventHubTotalFailedEvents|Niepowodzenie zdarzeń EventHub|Liczba|Łącznie|Liczba zakończonych niepowodzeniem zdarzeń EventHub|Lokalizacja|
|EventHubRejectedEvents|Odrzucone zdarzenia EventHub|Liczba|Łącznie|Liczba odrzuconych zdarzeń EventHub (niewłaściwa konfiguracja lub nieautoryzowana)|Lokalizacja|
|EventHubThrottledEvents|Ograniczone zdarzenia EventHub|Liczba|Łącznie|Liczba zdarzeń zdarzenia EventHub z ograniczeniami|Lokalizacja|
|EventHubTimedoutEvents|Przekroczono limit czasu zdarzeń EventHub|Liczba|Łącznie|Liczba przekroczeń limitu czasu zdarzeń EventHub|Lokalizacja|
|EventHubDroppedEvents|Opuszczone zdarzenia EventHub|Liczba|Łącznie|Liczba pominiętych zdarzeń z powodu osiągnięcia limitu rozmiaru kolejki|Lokalizacja|
|EventHubTotalBytesSent|Rozmiar zdarzeń EventHub|Bajty|Łącznie|Łączny rozmiar zdarzeń EventHub w bajtach|Lokalizacja|
|Żądania|Żądania|Liczba|Łącznie|Metryki żądania bramy z wieloma wymiarami|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|NetworkConnectivity|Stan łączności sieciowej zasobów (wersja zapoznawcza)|Liczba|Łącznie|Stan połączenia sieciowego zależnych typów zasobów z usługi API Management|Lokalizacja, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Liczba|Liczba|Łączna liczba przychodzących żądań HTTP.|Stanu|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Liczba|Średnia|Opóźnienie dla żądania HTTP.|Stanu|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Sprężyna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Procent użycia procesora systemu|Wartość procentowa|Średnia|Ostatnie użycie procesora CPU całego systemu|Nazwa_aplikacji, pod|
|AppCpuUsagePercentage|Procent użycia procesora aplikacji|Wartość procentowa|Średnia|Procent użycia procesora CPU JVM aplikacji|Nazwa_aplikacji, pod|
|AppMemoryCommitted|Przypisana pamięć aplikacji|Bajty|Średnia|Pamięć przypisana do JVM w bajtach|Nazwa_aplikacji, pod|
|AppMemoryUsed|Użyta pamięć aplikacji|Bajty|Średnia|Pamięć aplikacji użyta w bajtach|Nazwa_aplikacji, pod|
|AppMemoryMax|Maksymalna ilość pamięci aplikacji|Bajty|Maksimum|Maksymalna ilość pamięci w bajtach, która może być używana do zarządzania pamięcią|Nazwa_aplikacji, pod|
|MaxOldGenMemoryPoolBytes|Maksymalna dostępna wartość starego rozmiaru danych generacji|Bajty|Średnia|Maksymalny rozmiar puli pamięci starej generacji|Nazwa_aplikacji, pod|
|OldGenMemoryPoolBytes|Stary rozmiar danych generacji|Bajty|Średnia|Rozmiar starej puli pamięci generacji po pełnej operacji GC|Nazwa_aplikacji, pod|
|OldGenPromotedBytes|Podwyższ poziom do starego rozmiaru danych generacji|Bajty|Maksimum|Liczba pozytywnych wzrostów rozmiaru starej puli pamięci generacji przed GC do po GC|Nazwa_aplikacji, pod|
|YoungGenPromotedBytes|Podwyższ poziom do rozmiaru danych dla małych generacji|Bajty|Maksimum|Zwiększa się w celu zwiększenia rozmiaru puli pamięci młodej generacji po jednej operacji GC do wcześniejszej|Nazwa_aplikacji, pod|
|GCPauseTotalCount|Liczba wstrzymań GC|Liczba|Łącznie|Liczba wstrzymań GC|Nazwa_aplikacji, pod|
|GCPauseTotalTime|Całkowity czas wstrzymania odzyskiwania pamięci|)|Łącznie|Całkowity czas wstrzymania odzyskiwania pamięci|Nazwa_aplikacji, pod|
|TomcatSentBytes|Całkowita liczba wysłanych bajtów Tomcat|Bajty|Łącznie|Całkowita liczba wysłanych bajtów Tomcat|Nazwa_aplikacji, pod|
|TomcatReceivedBytes|Całkowita liczba odebranych bajtów Tomcat|Bajty|Łącznie|Całkowita liczba odebranych bajtów Tomcat|Nazwa_aplikacji, pod|
|TomcatRequestTotalTime|Łączny czas żądania Tomcat|)|Łącznie|Łączny czas żądania Tomcat|Nazwa_aplikacji, pod|
|TomcatRequestTotalCount|Łączna liczba żądań Tomcat|Liczba|Łącznie|Łączna liczba żądań Tomcat|Nazwa_aplikacji, pod|
|TomcatResponseAvgTime|Średni czas żądania Tomcat|)|Średnia|Średni czas żądania Tomcat|Nazwa_aplikacji, pod|
|TomcatRequestMaxTime|Maksymalny czas żądania Tomcat|)|Maksimum|Maksymalny czas żądania Tomcat|Nazwa_aplikacji, pod|
|TomcatErrorCount|Błąd globalny Tomcat|Liczba|Łącznie|Błąd globalny Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionActiveMaxCount|Maksymalna liczba aktywnych sesji Tomcat|Liczba|Łącznie|Maksymalna liczba aktywnych sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionAliveMaxTime|Maksymalny czas aktywności sesji Tomcat|)|Maksimum|Maksymalny czas aktywności sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionActiveCurrentCount|Liczba aktywności sesji Tomcat|Liczba|Łącznie|Liczba aktywności sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionCreatedCount|Liczba utworzonych sesji Tomcat|Liczba|Łącznie|Liczba utworzonych sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionExpiredCount|Liczba wygasłych sesji Tomcat|Liczba|Łącznie|Liczba wygasłych sesji Tomcat|Nazwa_aplikacji, pod|
|TomcatSessionRejectedCount|Liczba odrzuconych sesji Tomcat|Liczba|Łącznie|Liczba odrzuconych sesji Tomcat|Nazwa_aplikacji, pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob|Łączna liczba zadań|Liczba|Łącznie|Całkowita liczba zadań|Element Runbook, stan|
|TotalUpdateDeploymentRuns|Łączna liczba przebiegów wdrażania aktualizacji|Liczba|Łącznie|Łączna liczba przebiegów wdrażania aktualizacji oprogramowania|. Softwareupdateconfigurationname, stan|
|TotalUpdateDeploymentMachineRuns|Łączna liczba przebiegów wdrażania aktualizacji maszyny|Liczba|Łącznie|Łączna liczba uruchomień maszyn wdrożenia aktualizacji oprogramowania w ramach wdrożenia aktualizacji oprogramowania|. Softwareupdateconfigurationname, status, TargetComputer,. Softwareupdateconfigurationrunid|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft. Batch/batchAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CoreCount|Liczba dedykowanych rdzeni|Liczba|Łącznie|Łączna liczba rdzeni dedykowanych na koncie wsadowym|Brak|
|TotalNodeCount|Liczba dedykowanych węzłów|Liczba|Łącznie|Łączna liczba węzłów dedykowanych na koncie wsadowym|Brak|
|LowPriorityCoreCount|LowPriority rdzeń|Liczba|Łącznie|Łączna liczba rdzeni o niskim priorytecie na koncie wsadowym|Brak|
|TotalLowPriorityNodeCount|Liczba węzłów o niskim priorytecie|Liczba|Łącznie|Łączna liczba węzłów o niskim priorytecie na koncie wsadowym|Brak|
|CreatingNodeCount|Tworzenie liczby węzłów|Liczba|Łącznie|Liczba tworzonych węzłów|Brak|
|StartingNodeCount|Początkowa liczba węzłów|Liczba|Łącznie|Liczba początkowych węzłów|Brak|
|WaitingForStartTaskNodeCount|Oczekiwanie na liczbę węzłów zadania uruchamiania|Liczba|Łącznie|Liczba węzłów oczekujących na zakończenie zadania uruchamiania|Brak|
|StartTaskFailedNodeCount|Uruchamianie zadania nie powiodło się liczba węzłów|Liczba|Łącznie|Liczba węzłów, w których zadanie uruchomieniowe nie powiodło się|Brak|
|IdleNodeCount|Liczba węzłów bezczynności|Liczba|Łącznie|Liczba bezczynnych węzłów|Brak|
|OfflineNodeCount|Liczba węzłów w trybie offline|Liczba|Łącznie|Liczba węzłów offline|Brak|
|RebootingNodeCount|Ponowny rozruch liczby węzłów|Liczba|Łącznie|Liczba ponownych uruchomień węzłów|Brak|
|ReimagingNodeCount|Liczba węzłów regraficznych|Liczba|Łącznie|Liczba węzłów regraficznych|Brak|
|RunningNodeCount|Liczba uruchomionych węzłów|Liczba|Łącznie|Liczba uruchomionych węzłów|Brak|
|LeavingPoolNodeCount|Opuszczanie liczby węzłów puli|Liczba|Łącznie|Liczba węzłów opuszczających pulę|Brak|
|UnusableNodeCount|Liczba węzłów, których nie można użyć|Liczba|Łącznie|Liczba nieużywanych węzłów|Brak|
|PreemptedNodeCount|Liczba przeniesiona węzłów|Liczba|Łącznie|Liczba przeniesiona węzłów|Brak|
|TaskStartEvent|Zdarzenia uruchamiania zadania|Liczba|Łącznie|Łączna liczba rozpoczętych zadań|poolId, jobId|
|TaskCompleteEvent|Zdarzenia ukończenia zadania|Liczba|Łącznie|Całkowita liczba ukończonych zadań|poolId, jobId|
|TaskFailEvent|Zdarzenia błędów zadań|Liczba|Łącznie|Łączna liczba zadań zakończonych niepowodzeniem w stanie niepowodzenia|poolId, jobId|
|PoolCreateEvent|Zdarzenia tworzenia puli|Liczba|Łącznie|Łączna liczba utworzonych pul|poolId|
|PoolResizeStartEvent|Zdarzenia rozpoczęcia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba rozpoczętych rozmiarów puli|poolId|
|PoolResizeCompleteEvent|Zdarzenia ukończenia zmiany rozmiaru puli|Liczba|Łącznie|Łączna liczba zakończono zmienionych rozmiarów puli|poolId|
|PoolDeleteStartEvent|Zdarzenia uruchamiania usuwania puli|Liczba|Łącznie|Łączna liczba uruchomionych usunięć pul|poolId|
|PoolDeleteCompleteEvent|Zdarzenia ukończenia usuwania puli|Liczba|Łącznie|Łączna liczba ukończonych usunięć pul|poolId|
|JobDeleteCompleteEvent|Zdarzenia ukończenia usuwania zadania|Liczba|Łącznie|Całkowita liczba pomyślnie usuniętych zadań.|Zadanie|
|JobDeleteStartEvent|Zdarzenia rozpoczęcia usuwania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały zażądane do usunięcia.|Zadanie|
|JobDisableCompleteEvent|Zdarzenia ukończenia wyłączania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie wyłączone.|Zadanie|
|JobDisableStartEvent|Wyłącz zdarzenia uruchamiania dla zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały zlecone do wyłączenia.|Zadanie|
|JobStartEvent|Zdarzenia uruchamiania zadania|Liczba|Łącznie|Łączna liczba zadań, które zostały pomyślnie uruchomione.|Zadanie|
|JobTerminateCompleteEvent|Zdarzenia ukończenia kończenia zadania|Liczba|Łącznie|Łączna liczba zadań zakończonych pomyślnie.|Zadanie|
|JobTerminateStartEvent|Zdarzenia rozpoczęcia zakończenia zadania|Liczba|Łącznie|Całkowita liczba zadań, które zostały zażądane do zakończenia.|Zadanie|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. Batchai Job/obszary robocze

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przesłane zadanie|Przesłane zadanie|Liczba|Łącznie|Liczba przesłanych zadań|Scenariusz, ClusterName|
|Ukończono zadanie|Ukończono zadanie|Liczba|Łącznie|Liczba ukończonych zadań|Scenariusz, ClusterName, ResultType|
|Łączna liczba węzłów|Łączna liczba węzłów|Liczba|Średnia|Łączna liczba węzłów|Scenariusz, ClusterName|
|Aktywne węzły|Aktywne węzły|Liczba|Średnia|Liczba uruchomionych węzłów|Scenariusz, ClusterName|
|Węzły bezczynne|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów|Scenariusz, ClusterName|
|Węzły niezdatne do użytku|Węzły niezdatne do użytku|Liczba|Średnia|Liczba nieużywanych węzłów|Scenariusz, ClusterName|
|Zastępujące węzły|Zastępujące węzły|Liczba|Średnia|Liczba przeniesiona węzłów|Scenariusz, ClusterName|
|Opuszczanie węzłów|Opuszczanie węzłów|Liczba|Średnia|Liczba wychodzących węzłów|Scenariusz, ClusterName|
|Łączna liczba rdzeni|Łączna liczba rdzeni|Liczba|Średnia|Łączna liczba rdzeni|Scenariusz, ClusterName|
|Aktywne rdzenie|Aktywne rdzenie|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Rdzenie bezczynne|Rdzenie bezczynne|Liczba|Średnia|Liczba rdzeni bezczynnych|Scenariusz, ClusterName|
|Rdzenie, których nie można używać|Rdzenie, których nie można używać|Liczba|Średnia|Liczba rdzeni, których nie można używać|Scenariusz, ClusterName|
|Występujące rdzenie|Występujące rdzenie|Liczba|Średnia|Liczba przeniesiona rdzeni|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Opuszczanie rdzeni|Liczba|Średnia|Liczba rdzeni wychodzących|Scenariusz, ClusterName|
|Procent wykorzystania przydziałów|Procent wykorzystania przydziałów|Liczba|Średnia|Procent wykorzystania przydziałów|Scenariusz, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. łańcucha bloków/blockchainMembers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Procent użycia procesora CPU|Wartość procentowa|Maksimum|Procent użycia procesora CPU|Węzeł|
|MemoryUsage|Użycie pamięci|Bajty|Średnia|Użycie pamięci|Węzeł|
|MemoryLimit|Limit pamięci|Bajty|Średnia|Limit pamięci|Węzeł|
|MemoryUsagePercentageInDouble|Procent użycia pamięci|Wartość procentowa|Średnia|Procent użycia pamięci|Węzeł|
|StorageUsage|Użycie magazynu|Bajty|Średnia|Użycie magazynu|Węzeł|
|IOReadBytes|Bajty odczytu we/wy|Bajty|Łącznie|Bajty odczytu we/wy|Węzeł|
|IOWriteBytes|Bajty zapisu we/wy|Bajty|Łącznie|Bajty zapisu we/wy|Węzeł|
|ConnectionAccepted|Zaakceptowane połączenia|Liczba|Łącznie|Zaakceptowane połączenia|Węzeł|
|ConnectionHandled|Obsłużone połączenia|Liczba|Łącznie|Obsłużone połączenia|Węzeł|
|ConnectionActive|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Węzeł|
|RequestHandled|Obsłużone żądania|Liczba|Łącznie|Obsłużone żądania|Węzeł|
|ProcessedBlocks|Przetworzone bloki|Liczba|Łącznie|Przetworzone bloki|Węzeł|
|ProcessedTransactions|Przetworzone transakcje|Liczba|Łącznie|Przetworzone transakcje|Węzeł|
|PendingTransactions|Oczekujące transakcje|Liczba|Średnia|Oczekujące transakcje|Węzeł|
|QueuedTransactions|Transakcje w kolejce|Liczba|Średnia|Transakcje w kolejce|Węzeł|



## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedclients|Połączeni klienci|Liczba|Maksimum||ShardId|
|totalcommandsprocessed|Łączna liczba operacji|Liczba|Łącznie||ShardId|
|cachehits|Trafienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachemisses|Chybienia w pamięci podręcznej|Liczba|Łącznie||ShardId|
|cachemissrate|Współczynnik chybień w pamięci podręcznej|Wartość procentowa|cachemissrate||ShardId|
|GetCommands|Pobrania|Liczba|Łącznie||ShardId|
|SetCommands|Zestawy|Liczba|Łącznie||ShardId|
|operationsPerSecond|Liczba operacji na sekundę|Liczba|Maksimum||ShardId|
|evictedkeys|Wykluczone klucze|Liczba|Łącznie||ShardId|
|totalkeys|Łączna liczba kluczy|Liczba|Maksimum||ShardId|
|expiredkeys|Wygasłe klucze|Liczba|Łącznie||ShardId|
|usedmemory|Użyta pamięć|Bajty|Maksimum||ShardId|
|usedmemorypercentage|Wartość procentowa używanej pamięci|Wartość procentowa|Maksimum||ShardId|
|usedmemoryRss|Używana pamięć RSS|Bajty|Maksimum||ShardId|
|serverLoad|Obciążenie serwera|Wartość procentowa|Maksimum||ShardId|
|cacheWrite|Zapis pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|cacheRead|Odczyt pamięci podręcznej|BytesPerSecond|Maksimum||ShardId|
|percentProcessorTime|Procesor CPU|Wartość procentowa|Maksimum||ShardId|
|cacheLatency|Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)|Liczba|Średnia||ShardId|
|błędy|Errors|Liczba|Maksimum||ShardId, ErrorType|
|connectedclients0|Połączeni klienci (fragmentu 0)|Liczba|Maksimum||Brak|
|totalcommandsprocessed0|Łączna liczba operacji (fragmentu 0)|Liczba|Łącznie||Brak|
|cachehits0|Trafienia pamięci podręcznej (fragmentu 0)|Liczba|Łącznie||Brak|
|cachemisses0|Chybienia w pamięci podręcznej (fragmentu 0)|Liczba|Łącznie||Brak|
|getcommands0|Pobiera (fragmentu 0)|Liczba|Łącznie||Brak|
|setcommands0|Zestawy (fragmentu 0)|Liczba|Łącznie||Brak|
|operationsPerSecond0|Operacje na sekundę (fragmentu 0)|Liczba|Maksimum||Brak|
|evictedkeys0|Wykluczone klucze (fragmentu 0)|Liczba|Łącznie||Brak|
|totalkeys0|Łączna liczba kluczy (fragmentu 0)|Liczba|Maksimum||Brak|
|expiredkeys0|Wygasłe klucze (fragmentu 0)|Liczba|Łącznie||Brak|
|usedmemory0|Używana pamięć (fragmentu 0)|Bajty|Maksimum||Brak|
|usedmemoryRss0|Używana pamięć RSS (fragmentu 0)|Bajty|Maksimum||Brak|
|serverLoad0|Ładowanie serwera (fragmentu 0)|Wartość procentowa|Maksimum||Brak|
|cacheWrite0|Zapis pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Brak|
|cacheRead0|Odczyt pamięci podręcznej (fragmentu 0)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime0|Procesor CPU (fragmentu 0)|Wartość procentowa|Maksimum||Brak|
|connectedclients1|Połączeni klienci (fragmentu 1)|Liczba|Maksimum||Brak|
|totalcommandsprocessed1|Łączna liczba operacji (fragmentu 1)|Liczba|Łącznie||Brak|
|cachehits1|Trafienia pamięci podręcznej (fragmentu 1)|Liczba|Łącznie||Brak|
|cachemisses1|Chybienia w pamięci podręcznej (fragmentu 1)|Liczba|Łącznie||Brak|
|getcommands1|Pobiera (fragmentu 1)|Liczba|Łącznie||Brak|
|setcommands1|Zestawy (fragmentu 1)|Liczba|Łącznie||Brak|
|operationsPerSecond1|Operacje na sekundę (fragmentu 1)|Liczba|Maksimum||Brak|
|evictedkeys1|Wykluczone klucze (fragmentu 1)|Liczba|Łącznie||Brak|
|totalkeys1|Łączna liczba kluczy (fragmentu 1)|Liczba|Maksimum||Brak|
|expiredkeys1|Wygasłe klucze (fragmentu 1)|Liczba|Łącznie||Brak|
|usedmemory1|Używana pamięć (fragmentu 1)|Bajty|Maksimum||Brak|
|usedmemoryRss1|Używana pamięć RSS (fragmentu 1)|Bajty|Maksimum||Brak|
|serverLoad1|Ładowanie serwera (fragmentu 1)|Wartość procentowa|Maksimum||Brak|
|cacheWrite1|Zapisywanie pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Brak|
|cacheRead1|Odczyt pamięci podręcznej (fragmentu 1)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime1|Procesor CPU (fragmentu 1)|Wartość procentowa|Maksimum||Brak|
|connectedclients2|Połączeni klienci (fragmentu 2)|Liczba|Maksimum||Brak|
|totalcommandsprocessed2|Łączna liczba operacji (fragmentu 2)|Liczba|Łącznie||Brak|
|cachehits2|Trafienia pamięci podręcznej (fragmentu 2)|Liczba|Łącznie||Brak|
|cachemisses2|Chybienia w pamięci podręcznej (fragmentu 2)|Liczba|Łącznie||Brak|
|getcommands2|Pobiera (fragmentu 2)|Liczba|Łącznie||Brak|
|setcommands2|Zestawy (fragmentu 2)|Liczba|Łącznie||Brak|
|operationsPerSecond2|Operacje na sekundę (fragmentu 2)|Liczba|Maksimum||Brak|
|evictedkeys2|Wykluczone klucze (fragmentu 2)|Liczba|Łącznie||Brak|
|totalkeys2|Łączna liczba kluczy (fragmentu 2)|Liczba|Maksimum||Brak|
|expiredkeys2|Wygasłe klucze (fragmentu 2)|Liczba|Łącznie||Brak|
|usedmemory2|Używana pamięć (fragmentu 2)|Bajty|Maksimum||Brak|
|usedmemoryRss2|Używana pamięć RSS (fragmentu 2)|Bajty|Maksimum||Brak|
|serverLoad2|Ładowanie serwera (fragmentu 2)|Wartość procentowa|Maksimum||Brak|
|cacheWrite2|Zapisywanie pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Brak|
|cacheRead2|Odczyt pamięci podręcznej (fragmentu 2)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime2|Procesor CPU (fragmentu 2)|Wartość procentowa|Maksimum||Brak|
|connectedclients3|Połączeni klienci (fragmentu 3)|Liczba|Maksimum||Brak|
|totalcommandsprocessed3|Łączna liczba operacji (fragmentu 3)|Liczba|Łącznie||Brak|
|cachehits3|Trafienia pamięci podręcznej (fragmentu 3)|Liczba|Łącznie||Brak|
|cachemisses3|Chybienia w pamięci podręcznej (fragmentu 3)|Liczba|Łącznie||Brak|
|getcommands3|Pobiera (fragmentu 3)|Liczba|Łącznie||Brak|
|setcommands3|Zestawy (fragmentu 3)|Liczba|Łącznie||Brak|
|operationsPerSecond3|Operacje na sekundę (fragmentu 3)|Liczba|Maksimum||Brak|
|evictedkeys3|Wykluczone klucze (fragmentu 3)|Liczba|Łącznie||Brak|
|totalkeys3|Łączna liczba kluczy (fragmentu 3)|Liczba|Maksimum||Brak|
|expiredkeys3|Wygasłe klucze (fragmentu 3)|Liczba|Łącznie||Brak|
|usedmemory3|Używana pamięć (fragmentu 3)|Bajty|Maksimum||Brak|
|usedmemoryRss3|Używana pamięć RSS (fragmentu 3)|Bajty|Maksimum||Brak|
|serverLoad3|Ładowanie serwera (fragmentu 3)|Wartość procentowa|Maksimum||Brak|
|cacheWrite3|Zapis w pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Brak|
|cacheRead3|Odczyt pamięci podręcznej (fragmentu 3)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime3|Procesor CPU (fragmentu 3)|Wartość procentowa|Maksimum||Brak|
|connectedclients4|Połączeni klienci (fragmentu 4)|Liczba|Maksimum||Brak|
|totalcommandsprocessed4|Łączna liczba operacji (fragmentu 4)|Liczba|Łącznie||Brak|
|cachehits4|Trafienia pamięci podręcznej (fragmentu 4)|Liczba|Łącznie||Brak|
|cachemisses4|Chybienia w pamięci podręcznej (fragmentu 4)|Liczba|Łącznie||Brak|
|getcommands4|Pobiera (fragmentu 4)|Liczba|Łącznie||Brak|
|setcommands4|Zestawy (fragmentu 4)|Liczba|Łącznie||Brak|
|operationsPerSecond4|Operacje na sekundę (fragmentu 4)|Liczba|Maksimum||Brak|
|evictedkeys4|Wykluczone klucze (fragmentu 4)|Liczba|Łącznie||Brak|
|totalkeys4|Łączna liczba kluczy (fragmentu 4)|Liczba|Maksimum||Brak|
|expiredkeys4|Wygasłe klucze (fragmentu 4)|Liczba|Łącznie||Brak|
|usedmemory4|Używana pamięć (fragmentu 4)|Bajty|Maksimum||Brak|
|usedmemoryRss4|Używana pamięć RSS (fragmentu 4)|Bajty|Maksimum||Brak|
|serverLoad4|Ładowanie serwera (fragmentu 4)|Wartość procentowa|Maksimum||Brak|
|cacheWrite4|Zapis pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Brak|
|cacheRead4|Odczyt pamięci podręcznej (fragmentu 4)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime4|Procesor CPU (fragmentu 4)|Wartość procentowa|Maksimum||Brak|
|connectedclients5|Połączeni klienci (fragmentu 5)|Liczba|Maksimum||Brak|
|totalcommandsprocessed5|Łączna liczba operacji (fragmentu 5)|Liczba|Łącznie||Brak|
|cachehits5|Trafienia pamięci podręcznej (fragmentu 5)|Liczba|Łącznie||Brak|
|cachemisses5|Chybienia w pamięci podręcznej (fragmentu 5)|Liczba|Łącznie||Brak|
|getcommands5|Pobiera (fragmentu 5)|Liczba|Łącznie||Brak|
|setcommands5|Zestawy (fragmentu 5)|Liczba|Łącznie||Brak|
|operationsPerSecond5|Operacje na sekundę (fragmentu 5)|Liczba|Maksimum||Brak|
|evictedkeys5|Wykluczone klucze (fragmentu 5)|Liczba|Łącznie||Brak|
|totalkeys5|Łączna liczba kluczy (fragmentu 5)|Liczba|Maksimum||Brak|
|expiredkeys5|Wygasłe klucze (fragmentu 5)|Liczba|Łącznie||Brak|
|usedmemory5|Używana pamięć (fragmentu 5)|Bajty|Maksimum||Brak|
|usedmemoryRss5|Używana pamięć RSS (fragmentu 5)|Bajty|Maksimum||Brak|
|serverLoad5|Ładowanie serwera (fragmentu 5)|Wartość procentowa|Maksimum||Brak|
|cacheWrite5|Zapis w pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Brak|
|cacheRead5|Odczyt pamięci podręcznej (fragmentu 5)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime5|Procesor CPU (fragmentu 5)|Wartość procentowa|Maksimum||Brak|
|connectedclients6|Połączeni klienci (fragmentu 6)|Liczba|Maksimum||Brak|
|totalcommandsprocessed6|Łączna liczba operacji (fragmentu 6)|Liczba|Łącznie||Brak|
|cachehits6|Trafienia pamięci podręcznej (fragmentu 6)|Liczba|Łącznie||Brak|
|cachemisses6|Chybienia w pamięci podręcznej (fragmentu 6)|Liczba|Łącznie||Brak|
|getcommands6|Pobiera (fragmentu 6)|Liczba|Łącznie||Brak|
|setcommands6|Zestawy (fragmentu 6)|Liczba|Łącznie||Brak|
|operationsPerSecond6|Operacje na sekundę (fragmentu 6)|Liczba|Maksimum||Brak|
|evictedkeys6|Wykluczone klucze (fragmentu 6)|Liczba|Łącznie||Brak|
|totalkeys6|Łączna liczba kluczy (fragmentu 6)|Liczba|Maksimum||Brak|
|expiredkeys6|Wygasłe klucze (fragmentu 6)|Liczba|Łącznie||Brak|
|usedmemory6|Używana pamięć (fragmentu 6)|Bajty|Maksimum||Brak|
|usedmemoryRss6|Używana pamięć RSS (fragmentu 6)|Bajty|Maksimum||Brak|
|serverLoad6|Ładowanie serwera (fragmentu 6)|Wartość procentowa|Maksimum||Brak|
|cacheWrite6|Zapis w pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Brak|
|cacheRead6|Odczyt pamięci podręcznej (fragmentu 6)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime6|Procesor CPU (fragmentu 6)|Wartość procentowa|Maksimum||Brak|
|connectedclients7|Połączeni klienci (fragmentu 7)|Liczba|Maksimum||Brak|
|totalcommandsprocessed7|Łączna liczba operacji (fragmentu 7)|Liczba|Łącznie||Brak|
|cachehits7|Trafienia pamięci podręcznej (fragmentu 7)|Liczba|Łącznie||Brak|
|cachemisses7|Chybienia w pamięci podręcznej (fragmentu 7)|Liczba|Łącznie||Brak|
|getcommands7|Pobiera (fragmentu 7)|Liczba|Łącznie||Brak|
|setcommands7|Zestawy (fragmentu 7)|Liczba|Łącznie||Brak|
|operationsPerSecond7|Operacje na sekundę (fragmentu 7)|Liczba|Maksimum||Brak|
|evictedkeys7|Wykluczone klucze (fragmentu 7)|Liczba|Łącznie||Brak|
|totalkeys7|Łączna liczba kluczy (fragmentu 7)|Liczba|Maksimum||Brak|
|expiredkeys7|Wygasłe klucze (fragmentu 7)|Liczba|Łącznie||Brak|
|usedmemory7|Używana pamięć (fragmentu 7)|Bajty|Maksimum||Brak|
|usedmemoryRss7|Używana pamięć RSS (fragmentu 7)|Bajty|Maksimum||Brak|
|serverLoad7|Ładowanie serwera (fragmentu 7)|Wartość procentowa|Maksimum||Brak|
|cacheWrite7|Zapis w pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Brak|
|cacheRead7|Odczyt pamięci podręcznej (fragmentu 7)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime7|Procesor CPU (fragmentu 7)|Wartość procentowa|Maksimum||Brak|
|connectedclients8|Połączeni klienci (fragmentu 8)|Liczba|Maksimum||Brak|
|totalcommandsprocessed8|Łączna liczba operacji (fragmentu 8)|Liczba|Łącznie||Brak|
|cachehits8|Trafienia pamięci podręcznej (fragmentu 8)|Liczba|Łącznie||Brak|
|cachemisses8|Chybienia w pamięci podręcznej (fragmentu 8)|Liczba|Łącznie||Brak|
|getcommands8|Pobiera (fragmentu 8)|Liczba|Łącznie||Brak|
|setcommands8|Zestawy (fragmentu 8)|Liczba|Łącznie||Brak|
|operationsPerSecond8|Operacje na sekundę (fragmentu 8)|Liczba|Maksimum||Brak|
|evictedkeys8|Wykluczone klucze (fragmentu 8)|Liczba|Łącznie||Brak|
|totalkeys8|Łączna liczba kluczy (fragmentu 8)|Liczba|Maksimum||Brak|
|expiredkeys8|Wygasłe klucze (fragmentu 8)|Liczba|Łącznie||Brak|
|usedmemory8|Używana pamięć (fragmentu 8)|Bajty|Maksimum||Brak|
|usedmemoryRss8|Używana pamięć RSS (fragmentu 8)|Bajty|Maksimum||Brak|
|serverLoad8|Ładowanie serwera (fragmentu 8)|Wartość procentowa|Maksimum||Brak|
|cacheWrite8|Zapis w pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Brak|
|cacheRead8|Odczyt pamięci podręcznej (fragmentu 8)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime8|Procesor CPU (fragmentu 8)|Wartość procentowa|Maksimum||Brak|
|connectedclients9|Połączeni klienci (fragmentu 9)|Liczba|Maksimum||Brak|
|totalcommandsprocessed9|Łączna liczba operacji (fragmentu 9)|Liczba|Łącznie||Brak|
|cachehits9|Trafienia pamięci podręcznej (fragmentu 9)|Liczba|Łącznie||Brak|
|cachemisses9|Chybienia w pamięci podręcznej (fragmentu 9)|Liczba|Łącznie||Brak|
|getcommands9|Pobiera (fragmentu 9)|Liczba|Łącznie||Brak|
|setcommands9|Zestawy (fragmentu 9)|Liczba|Łącznie||Brak|
|operationsPerSecond9|Operacje na sekundę (fragmentu 9)|Liczba|Maksimum||Brak|
|evictedkeys9|Wykluczone klucze (fragmentu 9)|Liczba|Łącznie||Brak|
|totalkeys9|Łączna liczba kluczy (fragmentu 9)|Liczba|Maksimum||Brak|
|expiredkeys9|Wygasłe klucze (fragmentu 9)|Liczba|Łącznie||Brak|
|usedmemory9|Używana pamięć (fragmentu 9)|Bajty|Maksimum||Brak|
|usedmemoryRss9|Używana pamięć RSS (fragmentu 9)|Bajty|Maksimum||Brak|
|serverLoad9|Ładowanie serwera (fragmentu 9)|Wartość procentowa|Maksimum||Brak|
|cacheWrite9|Zapis w pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Brak|
|cacheRead9|Odczyt pamięci podręcznej (fragmentu 9)|BytesPerSecond|Maksimum||Brak|
|percentProcessorTime9|Procesor CPU (fragmentu 9)|Wartość procentowa|Maksimum||Brak|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|Brak|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|Brak|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|Brak|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|Brak|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|Brak|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|Brak|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|Brak|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domainNames/gniazda/role

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Wartość procentowa przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne.|RoleInstanceId|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący).|RoleInstanceId|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący).|RoleInstanceId|
|Bajty odczytu dysku/s|Odczyt dysku|BytesPerSecond|Średnia|Średnia liczba bajtów odczytanych z dysku w okresie monitorowania.|RoleInstanceId|
|Bajty zapisu dysku/s|Zapis na dysku|BytesPerSecond|Średnia|Średnia liczba bajtów zapisywana na dysku w okresie monitorowania.|RoleInstanceId|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu na dysku.|RoleInstanceId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Liczba operacji we/wy zapisu na dysku.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Używana pojemność|Bajty|Średnia|Używana pojemność konta|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Pojemność obiektu BLOB|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Blob service konta magazynu w bajtach.|Blobtype, warstwa|
|BlobCount|Liczba obiektów BLOB|Liczba|Średnia|Liczba obiektów BLOB w Blob service konta magazynu.|Blobtype, warstwa|
|ContainerCount|Liczba kontenerów obiektów BLOB|Liczba|Średnia|Liczba kontenerów w Blob service konta magazynu.|Brak|
|IndexCapacity|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez ADLS Gen2 (hierarchiczny) indeks w bajtach.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Pojemność tabeli|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Table service konta magazynu w bajtach.|Brak|
|TableCount|Liczba tabel|Liczba|Średnia|Liczba tabel w Table service konta magazynu.|Brak|
|TableEntityCount|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli w Table service konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Udziału|
|FileCount|Liczba plików|Liczba|Średnia|Liczba plików w usłudze plików konta magazynu.|Udziału|
|FileShareCount|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|Brak|
|FileShareSnapshotCount|Liczba migawek udziału plików|Liczba|Średnia|Liczba migawek znajdujących się w udziale w usłudze plików konta magazynu.|Udziału|
|FileShareSnapshotSize|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udziału|
|FileShareQuota|Rozmiar przydziału udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udziału|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, Authentication, przeudziale|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotyp, ApiName, uwierzytelnianie, przeudziale|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Pojemność kolejki|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługa kolejki konta magazynu w bajtach.|Brak|
|QueueCount|Liczba kolejek|Liczba|Średnia|Liczba kolejek w usługa kolejki konta magazynu.|Brak|
|QueueMessageCount|Liczba komunikatów w kolejce|Liczba|Średnia|Przybliżona liczba komunikatów w kolejce w usługa kolejki konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Kompleksowe opóźnienie pomyślnych żądań kierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalCalls|Łączna liczba wywołań|Liczba|Łącznie|Łączna liczba wywołań.|ApiName, OperationName, region|
|SuccessfulCalls|Pomyślne wywołania|Liczba|Łącznie|Liczba pomyślnych wywołań.|ApiName, OperationName, region|
|TotalErrors|Łączna liczba błędów|Liczba|Łącznie|Łączna liczba wywołań z odpowiedzią na błąd (kod odpowiedzi HTTP 4xx lub 5xx).|ApiName, OperationName, region|
|BlockedCalls|Zablokowane wywołania|Liczba|Łącznie|Liczba wywołań, które przekroczyły limit szybkości lub limitu przydziału.|ApiName, OperationName, region|
|Błędy servererrors|Błędy serwera|Liczba|Łącznie|Liczba wywołań z błędem wewnętrznym usługi (5xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|ClientErrors|Błędy klienta|Liczba|Łącznie|Liczba wywołań z błędem po stronie klienta (4xx kodu odpowiedzi HTTP).|ApiName, OperationName, region|
|Dane|Dane w|Bajty|Łącznie|Rozmiar danych przychodzących w bajtach.|ApiName, OperationName, region|
|DataOut|Dane wychodzące|Bajty|Łącznie|Rozmiar danych wychodzących w bajtach.|ApiName, OperationName, region|
|Opóźnienie|Opóźnienie|)|Średnia|Opóźnienie w milisekundach.|ApiName, OperationName, region|
|TotalTokenCalls|Łączna liczba wywołań tokenów|Liczba|Łącznie|Łączna liczba wywołań tokenów.|ApiName, OperationName, region|
|CharactersTranslated|Znaki tłumaczone|Liczba|Łącznie|Całkowita liczba znaków w żądaniu tekstu przychodzącego.|ApiName, OperationName, region|
|CharactersTrained|Znaki przeszkolone|Liczba|Łącznie|Łączna liczba znaków przeszkolonych.|ApiName, OperationName, region|
|SpeechSessionDuration|Czas trwania sesji mowy|Sekundy|Łącznie|Łączny czas trwania sesji mowy (w sekundach).|ApiName, OperationName, region|
|TotalTransactions|Łączna liczba transakcji|Liczba|Łącznie|Łączna liczba transakcji.|Brak|
|ProcessedImages|Przetworzone obrazy|Liczba|Łącznie| Liczba transakcji do przetwarzania obrazów.|ApiName, FeatureName, Channel, region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Brak|
|Sieć — wejście|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|Brak|
|Sieć — wyjście|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|Brak|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Brak|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Brak|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|Brak|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|Brak|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych [(przestarzałe)](portal-disk-metrics-deprecation.md)] (Portal-Disk-Metrics-deprecation.MD)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|TWORZONA|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|TWORZONA|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak|
|Przepływy przychodzące|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Brak|
|Przepływy wychodzące|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|Brak|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Brak|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Brak|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Brak|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak|
|Sieć łącznie|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak|
|Całkowita liczba sieci|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Brak|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|VMName|
|Sieć — wejście|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|VMName|
|Sieć — wyjście|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|VMName|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|VMName|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|VMName|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|VMName|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|VMName|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|Jednostka LUN, VMName|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|Jednostka LUN, VMName|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|Jednostka LUN, VMName|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|VMName|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|VMName|
|Przepływy przychodzące|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|VMName|
|Przepływy wychodzące|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|VMName|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|VMName|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Jednostka LUN, VMName|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|VMName|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|VMName|
|Sieć łącznie|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|VMName|
|Całkowita liczba sieci|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Procent przyznanych jednostek obliczeniowych, które są obecnie używane przez maszyny wirtualne|Brak|
|Sieć — wejście|Sieć jest rozliczana (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący) (przestarzałe)|Brak|
|Sieć — wyjście|Sieć — do rozliczania (przestarzałe)|Bajty|Łącznie|Liczba bajtów rozliczanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący) (przestarzałe)|Brak|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Bajty odczytane z dysku w okresie monitorowania|Brak|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Bajty zapisywane na dysku w okresie monitorowania|Brak|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Operacje we/wy odczytu dysku|Brak|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Operacje we/wy zapisu na dysku|Brak|
|Pozostałe kredyty procesora CPU|Pozostałe kredyty procesora CPU|Liczba|Średnia|Łączna liczba kredytów dostępnych dla serii|Brak|
|Wykorzystane środki CPU|Wykorzystane środki CPU|Liczba|Średnia|Łączna liczba kredytów zużytych przez maszynę wirtualną|Brak|
|Bajty odczytu na dysk/s|Bajty odczytu dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|SlotId|
|Bajty zapisu na dysku/s|Bajty zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje odczytu na dysk/s|Operacje odczytu z dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku danych/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|SlotId|
|Na dysk głębokość kolejki|Głębokość kolejki dysku danych [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|SlotId|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu z dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Bajty zapisu na dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje odczytu na dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s [(przestarzałe)](portal-disk-metrics-deprecation.md)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Głębokość kolejki systemu operacyjnego na dysku|Głębokość kolejki dysku systemu operacyjnego [(przestarzałe)](portal-disk-metrics-deprecation.md)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak|
|Bajty odczytu dysku danych/s|Bajty odczytu dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania|TWORZONA|
|Bajty zapisu na dysku danych/s|Bajty zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje odczytu z dysku danych/s|Operacje odczytu z dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Operacje zapisu na dysku danych/s|Operacje zapisu na dysku danych/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania|TWORZONA|
|Głębokość kolejki dysku danych|Głębokość kolejki dysku danych (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku danych (lub długość kolejki)|TWORZONA|
|Bajty odczytu dysku systemu operacyjnego/s|Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty/s odczytane z pojedynczego dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Bajty zapisu dysku systemu operacyjnego/s|Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Bajty na sekundę zapisywane na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje odczytu z dysku systemu operacyjnego/s|Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Odczyt operacji we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Operacje zapisu na dysku systemu operacyjnego/s|Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)|CountPerSecond|Średnia|Zapisuj operacje we/wy na pojedynczym dysku w okresie monitorowania dla dysku systemu operacyjnego|Brak|
|Głębokość kolejki dysku systemu operacyjnego|Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)|Liczba|Średnia|Głębokość kolejki dysku systemu operacyjnego (lub długość kolejki)|Brak|
|Przepływy przychodzące|Przepływy przychodzące|Liczba|Średnia|Przepływy przychodzące to liczba bieżących przepływów w kierunku przychodzącym (ruch przychodzący do maszyny wirtualnej)|Brak|
|Przepływy wychodzące|Przepływy wychodzące|Liczba|Średnia|Przepływy wychodzące to liczba bieżących przepływów w kierunku ruchu wychodzącego (ruch wychodzący z maszyny wirtualnej)|Brak|
|Maksymalny współczynnik tworzenia przepływów przychodzących|Maksymalny współczynnik tworzenia przepływów przychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów przychodzących (ruch przychodzący do maszyny wirtualnej)|Brak|
|Maksymalny współczynnik tworzenia przepływów wychodzących|Maksymalny współczynnik tworzenia przepływów wychodzących|CountPerSecond|Średnia|Maksymalna szybkość tworzenia przepływów wychodzących (ruch wychodzący z maszyny wirtualnej)|Brak|
|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium|TWORZONA|
|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium|Brak|
|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)|Wartość procentowa|Średnia|Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium|Brak|
|Sieć łącznie|Sieć łącznie|Bajty|Łącznie|Liczba bajtów odebranych na wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch przychodzący)|Brak|
|Całkowita liczba sieci|Całkowita liczba sieci|Bajty|Łącznie|Liczba bajtów wysłanych we wszystkich interfejsach sieciowych przez maszyny wirtualne (ruch wychodzący)|Brak|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage|Użycie procesora|Liczba|Średnia|Użycie procesora CPU we wszystkich rdzeniach w millicores.|containerName|
|MemoryUsage|Użycie pamięci|Bajty|Średnia|Całkowite użycie pamięci w bajcie.|containerName|
|NetworkBytesReceivedPerSecond|Bajty odebrane przez sieć na sekundę|Bajty|Średnia|Bajty odebrane przez sieć na sekundę.|Brak|
|NetworkBytesTransmittedPerSecond|Bajty przesyłane przez sieć na sekundę|Bajty|Średnia|Bajty przesyłane przez sieć na sekundę.|Brak|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/rejestry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount|Łączna liczba ściągania|Liczba|Średnia|Całkowita liczba ściągania obrazów|Brak|
|SuccessfulPullCount|Pomyślna liczba ściągania|Liczba|Średnia|Liczba pomyślnych operacji ściągania obrazu|Brak|
|TotalPushCount|Łączna liczba wypychanych|Liczba|Średnia|Liczba wypchnięciów obrazów łącznie|Brak|
|SuccessfulPushCount|Liczba wypychanych zakończonych powodzeniem|Liczba|Średnia|Liczba pomyślnych operacji wypychania obrazu|Brak|
|RunDuration|Czas trwania przebiegu|)|Łącznie|Czas trwania przebiegu w milisekundach|Brak|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. ContainerService/managedClusters

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Liczba|Średnia|Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze|Brak|
|kube_node_status_allocatable_memory_bytes|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Bajty|Średnia|Łączna ilość dostępnej pamięci w zarządzanym klastrze|Brak|
|kube_pod_status_ready|Liczba zasobników w stanie gotowe|Liczba|Średnia|Liczba zasobników w stanie gotowe|Przestrzeń nazw, pod|
|kube_node_status_condition|Stany różnych warunków węzła|Liczba|Średnia|Stany różnych warunków węzła|warunek, stan, status2, węzeł|
|kube_pod_status_phase|Liczba etapów według fazy|Liczba|Średnia|Liczba etapów według fazy|faza, przestrzeń nazw, pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfullRequests|Żądania zakończone powodzeniem|Liczba|Łącznie|Pomyślne żądania wykonywane przez niestandardowego dostawcę|HttpMethod, CallPath, StatusCode|
|FailedRequests|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Pobiera dostępne dzienniki dla niestandardowych dostawców zasobów|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|NICReadThroughput|Przepływność odczytu (Sieć)|BytesPerSecond|Średnia|Przepływność odczytu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|NICWriteThroughput|Przepływność zapisu (Sieć)|BytesPerSecond|Średnia|Przepływność zapisu interfejsu sieciowego na urządzeniu w okresie sprawozdawczym dla wszystkich woluminów w bramie.|InstanceName|
|CloudReadThroughputPerShare|Przepływność pobierania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność pobierania na platformę Azure z udziału w okresie raportowania.|Udostępnij|
|CloudUploadThroughputPerShare|Przepływność przekazywania w chmurze (udział)|BytesPerSecond|Średnia|Przepływność przekazywania do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|BytesUploadedToCloudPerShare|Przekazane bajty w chmurze (udział)|Bajty|Średnia|Całkowita liczba bajtów przekazanych do platformy Azure z udziału w okresie raportowania.|Udostępnij|
|Łączna pojemność|Całkowita pojemność|Bajty|Średnia|Całkowita pojemność|Brak|
|AvailableCapacity|Dostępna pojemność|Bajty|Średnia|Dostępna pojemność w bajtach w okresie raportowania.|Brak|
|CloudUploadThroughput|Przepływność przekazywania w chmurze|BytesPerSecond|Średnia|W chmurze przekazano przepływność na platformę Azure w okresie raportowania.|Brak|
|CloudReadThroughput|Przepływność pobierania w chmurze|BytesPerSecond|Średnia|Chmura pobiera przepływność na platformę Azure w okresie raportowania.|Brak|
|BytesUploadedToCloud|Przekazane bajty w chmurze (urządzenie)|Bajty|Średnia|Całkowita liczba bajtów przekazana na platformę Azure z urządzenia w okresie raportowania.|Brak|
|HyperVVirtualProcessorUtilization|Obliczenia brzegowe — procentowy procesor CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|InstanceName|
|HyperVMemoryUtilization|Obliczenia brzegowe — użycie pamięci|Wartość procentowa|Średnia|Ilość pamięci RAM w użyciu|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft. datacatalog/wykazy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Dystrybucja zasobów według klasyfikacji|Liczba|Łącznie|Wskazuje liczbę zasobów z przypisaną określoną klasyfikacją, tj. są one klasyfikowane przy użyciu tej etykiety.|Klasyfikacja, Źródło|
|AssetDistributionByStorageType|Dystrybucja zasobów według typu magazynu|Liczba|Łącznie|Wskazuje liczbę zasobów z określonym typem magazynu.|StorageType|
|NumberOfAssetsWithClassifications|Liczba zasobów z co najmniej jedną klasyfikacją|Liczba|Średnia|Wskazuje liczbę zasobów z co najmniej jedną klasyfikacją tagów.|Brak|
|ScanCancelled|Anulowano skanowanie|Liczba|Łącznie|Wskazuje liczbę anulowanych skanów.|Brak|
|ScanCompleted|Ukończono skanowanie|Liczba|Łącznie|Wskazuje, ile skanów zostało ukończonych pomyślnie.|Brak|
|ScanFailed|Skanowanie nie powiodło się|Liczba|Łącznie|Wskazuje liczbę operacji skanowania zakończonych niepowodzeniem.|Brak|
|ScanTimeTaken|Czas skanowania|Sekundy|Łącznie|Wskazuje łączny czas (w sekundach) skanowania.|Brak|
|CatalogActiveUsers|Aktywni użytkownicy codziennie|Liczba|Łącznie|Liczba aktywnych użytkowników dziennie|Brak|
|CatalogUsage|Dystrybucja użycia według operacji|Liczba|Łącznie|Wskaż liczbę operacji wprowadzanych przez użytkownika do wykazu, tj. dostępu, wyszukiwania, słownika.|Operacja|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FailedRuns|Nieudane uruchomienia|Liczba|Łącznie||potokname, ActivityName|
|SuccessfulRuns|Pomyślne uruchomienia|Liczba|Łącznie||potokname, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabryki

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metryki uruchomionych potoków zakończonych niepowodzeniem|Liczba|Łącznie||FailureType, nazwa|
|PipelineSucceededRuns|Metryki uruchamiania potoków zakończonych powodzeniem|Liczba|Łącznie||FailureType, nazwa|
|PipelineCancelledRuns|Metryki uruchomień potoku|Liczba|Łącznie||FailureType, nazwa|
|ActivityFailedRuns|Metryki uruchamiania działań zakończonych niepowodzeniem|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivitySucceededRuns|Metryki uruchamiania działań zakończonych powodzeniem|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|ActivityCancelledRuns|Metryki uruchomień działań|Liczba|Łącznie||ActivityType, Potokname, FailureType, nazwa|
|TriggerFailedRuns|Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem|Liczba|Łącznie||Nazwa, Niepowodzenie|
|TriggerSucceededRuns|Wyzwalacze uruchomienia wyzwalają metryki|Liczba|Łącznie||Nazwa, Niepowodzenie|
|TriggerCancelledRuns|Anulowane metryki uruchamiania wyzwalacza|Liczba|Łącznie||Nazwa, Niepowodzenie|
|IntegrationRuntimeCpuPercentage|Użycie procesora Integration Runtime|Wartość procentowa|Średnia||IntegrationRuntimeName, nodename|
|IntegrationRuntimeAvailableMemory|Dostępna pamięć środowiska Integration Runtime|Bajty|Średnia||IntegrationRuntimeName, nodename|
|IntegrationRuntimeAverageTaskPickupDelay|Czas trwania kolejki Integration Runtime|Sekundy|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Długość kolejki środowiska Integration Runtime|Liczba|Średnia||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Liczba dostępnych węzłów Integration Runtime|Liczba|Średnia||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maksymalna dozwolona liczba jednostek|Liczba|Maksimum||Brak|
|MaxAllowedFactorySizeInGbUnits|Maksymalny dozwolony rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak|
|ResourceCount|Łączna liczba jednostek|Liczba|Maksimum||Brak|
|FactorySizeInGbUnits|Łączny rozmiar fabryki (jednostka GB)|Liczba|Maksimum||Brak|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|JobEndedSuccess|Zadania zakończone powodzeniem|Liczba|Łącznie|Liczba zadań zakończonych powodzeniem.|Brak|
|JobEndedFailure|Zadania zakończone niepowodzeniem|Liczba|Łącznie|Liczba zadań zakończonych niepowodzeniem.|Brak|
|JobEndedCancelled|Anulowane zadania|Liczba|Łącznie|Liczba anulowanych zadań.|Brak|
|JobAUEndedSuccess|Czas pomyślnej aktualizacji|Sekundy|Łącznie|Łączny czas dla zadań zakończonych powodzeniem.|Brak|
|JobAUEndedFailure|Czas niepowodzenia|Sekundy|Łącznie|Łączny czas AU zadań zakończonych niepowodzeniem.|Brak|
|JobAUEndedCancelled|Anulowano czas aktualizacji|Sekundy|Łącznie|Łączny czas AU dla anulowanych zadań.|Brak|
|JobStage|Zadania na etapie|Liczba|Łącznie|Liczba zadań na każdym etapie.|Brak|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. kontach datalakestore/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalStorage|Łączny rozmiar magazynu|Bajty|Maksimum|Łączna ilość danych przechowywanych na koncie.|Brak|
|Zapisywana|Zapisywane dane|Bajty|Łącznie|Całkowita ilość danych zapisywana na konto.|Brak|
|Odczyt DataReady|Odczytane dane|Bajty|Łącznie|Łączna ilość danych odczytanych z konta.|Brak|
|WriteRequests|Żądania zapisu|Liczba|Łącznie|Liczba żądań zapisu danych na koncie.|Brak|
|ReadRequests|Żądania odczytu|Liczba|Łącznie|Liczba żądań odczytu danych do konta.|Brak|


## <a name="microsoftdatashareaccounts"></a>Microsoft. dataudział/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ShareCount|Wysłane udziały|Liczba|Maksimum|Liczba wysłanych udziałów na koncie|ShareName|
|ShareSubscriptionCount|Otrzymane udziały|Liczba|Maksimum|Liczba odebranych udziałów na koncie|ShareSubscriptionName|
|SucceededShareSynchronizations|Pomyślnie wysłano migawki udziału|Liczba|Liczba|Liczba przesłanych pomyślnie migawek udziału w ramach konta|Brak|
|FailedShareSynchronizations|Nie można wykonać migawek wysłanych udziałów|Liczba|Liczba|Liczba wysłanych migawek zakończonych niepowodzeniem na koncie|Brak|
|SucceededShareSubscriptionSynchronizations|Odebrane migawki udziału zostały pomyślnie zakończone|Liczba|Liczba|Liczba pomyślnie odebranych migawek udziału na koncie|Brak|
|FailedShareSubscriptionSynchronizations|Odebrane migawki nie powiodły się|Liczba|Liczba|Liczba odebranych migawek zakończonych niepowodzeniem w ramach konta|Brak|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|io_consumption_percent|Procent operacji we/wy|Wartość procentowa|Średnia|Procent operacji we/wy|Brak|
|storage_percent|Procent magazynu|Wartość procentowa|Średnia|Procent magazynu|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|storage_limit|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Wartość procentowa|Średnia|Procent magazynu dzienników serwera|Brak|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Średnia|Limit magazynowania dziennika serwera|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|io_consumption_percent|Procent operacji we/wy|Wartość procentowa|Średnia|Procent operacji we/wy|Brak|
|storage_percent|Procent magazynu|Wartość procentowa|Średnia|Procent magazynu|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|storage_limit|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Wartość procentowa|Średnia|Procent magazynu dzienników serwera|Brak|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Maksimum|Limit magazynowania dziennika serwera|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|seconds_behind_master|Opóźnienie replikacji w sekundach|Liczba|Maksimum|Opóźnienie replikacji w sekundach|Brak|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|io_consumption_percent|Procent operacji we/wy|Wartość procentowa|Średnia|Procent operacji we/wy|Brak|
|storage_percent|Procent magazynu|Wartość procentowa|Średnia|Procent magazynu|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|storage_limit|Limit magazynu|Bajty|Maksimum|Limit magazynu|Brak|
|serverlog_storage_percent|Procent magazynu dzienników serwera|Wartość procentowa|Średnia|Procent magazynu dzienników serwera|Brak|
|serverlog_storage_usage|Używany magazyn dzienników serwera|Bajty|Średnia|Używany magazyn dzienników serwera|Brak|
|serverlog_storage_limit|Limit magazynowania dziennika serwera|Bajty|Maksimum|Limit magazynowania dziennika serwera|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|backup_storage_used|Używany magazyn kopii zapasowych|Bajty|Średnia|Używany magazyn kopii zapasowych|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak|
|pg_replica_log_delay_in_seconds|Zwłoka repliki|Sekundy|Maksimum|Opóźnienie repliki w sekundach|Brak|
|pg_replica_log_delay_in_bytes|Maksymalne opóźnienie między replikami|Bajty|Maksimum|Opóźnienie w bajtach najbardziej opóźnionej repliki|Brak|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|Wejścia|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak|
|storage_percent|Procent magazynu|Wartość procentowa|Średnia|Procent magazynu|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|memory_percent|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Brak|
|Wejścia|Liczba operacji we/wy na sekundę|Liczba|Średnia|Operacje we/wy na sekundę|Brak|
|storage_percent|Procent magazynu|Wartość procentowa|Średnia|Procent magazynu|Brak|
|storage_used|Używany magazyn|Bajty|Średnia|Używany magazyn|Brak|
|active_connections|Aktywne połączenia|Liczba|Średnia|Aktywne połączenia|Brak|
|network_bytes_egress|Sieć — wyjście|Bajty|Łącznie|Sieć poza aktywnymi połączeniami|Brak|
|network_bytes_ingress|Sieć — wejście|Bajty|Łącznie|Sieć w ramach aktywnych połączeń|Brak|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|connections_succeeded|Połączenia zakończone powodzeniem|Liczba|Łącznie|Połączenia zakończone powodzeniem|Brak|
|maximum_used_transactionIDs|Maksymalna liczba używanych identyfikatorów transakcji|Liczba|Średnia|Maksymalna liczba używanych identyfikatorów transakcji|Brak|





## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|D2C. telemetrię. allProtocol|Próby wysłania komunikatów telemetrycznych|Liczba|Łącznie|Liczba komunikatów telemetrycznych z urządzenia do chmury, które próbowano wysłać do centrum IoT Hub|Brak|
|D2C. telemetrię. dane wejściowe. sukces|Wysłane komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie wysłanych komunikatów telemetrycznych z urządzenia do chmury do centrum IoT Hub|Brak|
|C2D. Commands. wyjście. Complete. Success|Zakończono dostarczanie komunikatów C2D|Liczba|Łącznie|Liczba dostaw komunikatów z chmury do urządzenia ukończonych pomyślnie przez urządzenie|Brak|
|C2D. Commands. wyjście. Abandon. Success|Porzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia porzuconych przez urządzenie|Brak|
|C2D. Commands. wyjście. Odrzuć. sukces|Odrzucone komunikaty C2D|Liczba|Łącznie|Liczba komunikatów z chmury do urządzenia odrzuconych przez urządzenie|Brak|
|C2DMessagesExpired|Komunikaty C2D wygasły (wersja zapoznawcza)|Liczba|Łącznie|Liczba wygasłych komunikatów z chmury do urządzenia|Brak|
|Devices. totalDevices|Łączna liczba urządzeń (przestarzałe)|Liczba|Łącznie|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|Devices. connectedDevices. allProtocol|Podłączone urządzenia (przestarzałe) |Liczba|Łącznie|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|
|D2C. telemetrię. ruch wychodzący. sukces|Routing: dostarczono komunikaty telemetryczne|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wszystkich punktów końcowych używających routingu IoT Hub. Jeśli komunikat jest kierowany do wielu punktów końcowych, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia. Jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy, ta wartość zwiększa się o jeden dla każdego pomyślnego dostarczenia.|Brak|
|D2C. dane telemetryczne. wychodzące. upuszczone|Routing: porzucone komunikaty telemetryczne |Liczba|Łącznie|Liczba porzuconych komunikatów przez IoT Hub Routing ze względu na martwe punkty końcowe. Ta wartość nie zlicza komunikatów dostarczonych do trasy rezerwowej, ponieważ opuszczone wiadomości nie są tam dostarczane.|Brak|
|D2C. dane telemetryczne. wychodzące. oddzielone|Routing: oddzielone komunikaty telemetryczne |Liczba|Łącznie|Liczba oddzielonych komunikatów przez IoT Hub Routing, ponieważ nie są one zgodne z żadną regułą routingu (w tym regułą rezerwową). |Brak|
|D2C. Telemetria. ruch wychodzący. nieprawidłowe|Routing: komunikaty telemetryczne są niezgodne|Liczba|Łącznie|Liczba przypadków, gdy Routing IoT Hub nie mógł dostarczyć komunikatów z powodu niezgodności z punktem końcowym. Ta wartość nie obejmuje ponownych prób.|Brak|
|D2C. telemetrię. ruch wychodzący. Fallback|Routing: komunikaty dostarczane do powrotu|Liczba|Łącznie|Liczba IoT Hub komunikatów dostarczonych przez funkcję routingu do punktu końcowego skojarzonego z trasą rezerwową.|Brak|
|D2C. endpoints. wychodzące. eventHubs|Routing: komunikaty dostarczane do centrum zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych centrum zdarzeń w usłudze IoT Hub Routing.|Brak|
|D2C. endpoints. opóźnienie. eventHubs|Routing: opóźnienie komunikatu dla centrum zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów przychodzących w punkcie końcowym centrum zdarzeń.|Brak|
|D2C. endpoints. wychodzące. serviceBusQueues|Routing: komunikaty dostarczone do kolejki Service Bus|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do Service Bus punktów końcowych kolejki w usłudze IoT Hub Routing.|Brak|
|D2C. endpoints. opóźnienie. serviceBusQueues|Routing: opóźnienie komunikatu dla kolejki Service Bus|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i telemetrii komunikatów przychodzących do punktu końcowego kolejki Service Bus.|Brak|
|D2C. endpoints. wychodzące. serviceBusTopics|Routing: komunikaty dostarczane do Service Bus tematu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów przez IoT Hub Routing do Service Bus punktów końcowych tematu.|Brak|
|D2C. endpoints. opóźnienie. serviceBusTopics|Routing: opóźnienie komunikatu dla Service Bus tematu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym tematu Service Bus.|Brak|
|D2C. endpoints. wychodzące. wbudowane. Events|Routing: komunikaty dostarczane do komunikatów/zdarzeń|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do wbudowanego punktu końcowego (komunikaty/zdarzenia) IoT Hub Routing.|Brak|
|D2C. endpoints. opóźnienie. wbudowane. Events|Routing: opóźnienie komunikatów dla komunikatów/zdarzeń|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w wbudowanym punkcie końcowym (komunikaty/zdarzenia).|Brak|
|D2C. endpoints. ruch wychodzący. Storage|Routing: komunikaty dostarczane do magazynu|Liczba|Łącznie|Liczba pomyślnie dostarczonych komunikatów do punktów końcowych usługi Routing IoT Hub.|Brak|
|D2C. endpoints. opóźnienie. Storage|Routing: opóźnienie komunikatu dla magazynu|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym magazynu.|Brak|
|D2C. endpoints. ruch wychodzący. Storage. Bytes|Routing: dane dostarczane do magazynu|Bajty|Łącznie|Ilość danych (w bajtach) IoT Hub Routing dostarczany do punktów końcowych magazynu.|Brak|
|D2C. endpoints. dane wyjściowe. Storage. blob|Routing: obiekty blob dostarczane do magazynu|Liczba|Łącznie|Ile razy usługa Routing IoT Hub dostarczać obiekty blob do punktów końcowych magazynu.|Brak|
|EventGridDeliveries|Dostawy Event Grid (wersja zapoznawcza)|Liczba|Łącznie|Liczba zdarzeń IoT Hub opublikowanych do Event Grid. Użyj wymiaru wynik dla liczby żądań zakończonych powodzeniem i niepowodzeniem. Wymiar EventType przedstawia typ zdarzenia ( https://aka.ms/ioteventgrid) .|ResourceId, wynik, typ zdarzenia|
|EventGridLatency|Opóźnienie Event Grid (wersja zapoznawcza)|)|Średnia|Średnie opóźnienie (w milisekundach) od momentu wygenerowania zdarzenia usługi IoT Hub po opublikowaniu zdarzenia w Event Grid. Ta liczba jest średnia między wszystkimi typami zdarzeń. Użyj wymiaru EventType, aby zobaczyć opóźnienie określonego typu zdarzenia.|ResourceId, EventType|
|RoutingDeliveries|Dostawy routingu (wersja zapoznawcza)|)|Łącznie|Liczba prób dostarczenia komunikatów do wszystkich punktów końcowych przy użyciu routingu przez IoT Hub. Aby sprawdzić liczbę prób zakończonych powodzeniem lub nieudanych, użyj wymiaru wynik. Aby zobaczyć przyczynę niepowodzenia, na przykład nieprawidłowe, porzucone lub oddzielone, użyj wymiaru FailureReasonCategory. Można również użyć wymiarów EndpointName i EndpointType, aby zrozumieć, ile komunikatów zostało dostarczonych do różnych punktów końcowych. Wartość metryki zwiększa się o jeden dla każdej próby dostarczenia, w tym, jeśli wiadomość jest dostarczana do wielu punktów końcowych, lub jeśli wiadomość jest przekazywana do tego samego punktu końcowego wiele razy.|ResourceId, EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Opóźnienie dostarczania routingu (wersja zapoznawcza)|)|Średnia|Średnie opóźnienie (w milisekundach) między komunikatami przychodzącymi do IoT Hub i komunikatów telemetrycznych w punkcie końcowym. Można użyć wymiarów EndpointName i EndpointType, aby zrozumieć opóźnienia dla różnych punktów końcowych.|ResourceId, EndpointType, EndpointName, RoutingSource|
|D2C. splot. Read. Success|Pomyślne odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich udanych operacji zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Read. Failure|Nieudane odczyty sznurów z urządzeń|Liczba|Łącznie|Liczba wszystkich nieudanych operacji zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Read. size|Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych przez urządzenia sznurów.|Brak|
|D2C. splot. Update. Success|Pomyślne aktualizacje bliźniaczych urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Update. Failure|Niepowodzenie aktualizacji bliźniaczych z urządzeń|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji dwuosiowych zainicjowanych przez urządzenie.|Brak|
|D2C. splot. Update. size|Rozmiar aktualizacji bliźniaczych z urządzeń|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu dwuosiowego zainicjowanego przez urządzenie.|Brak|
|C2D. Methods. Success|Pomyślne wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich pomyślnych wywołań metody bezpośredniej.|Brak|
|C2D. Methods. Failure|Nieudane wywołania metody bezpośredniej|Liczba|Łącznie|Liczba wszystkich wywołań metod bezpośrednich zakończonych niepowodzeniem.|Brak|
|C2D. Methods. requestSize|Rozmiar żądania wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich pomyślnych żądań metody bezpośredniej.|Brak|
|C2D. Methods. responseSize|Rozmiar odpowiedzi wywołań metody bezpośredniej|Bajty|Średnia|Średnia, minimum i maksimum wszystkich zakończonych powodzeniem odpowiedzi metody bezpośredniej.|Brak|
|C2D. splot. Read. Success|Pomyślne odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych powodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|C2D. splot. Read. Failure|Nieudane odczyty sznurów z zaplecza|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem odczytów dwuosiowych zainicjowanych z powrotem.|Brak|
|C2D. splot. Read. size|Rozmiar odpowiedzi na odwrocie od zaplecza|Bajty|Średnia|Średnia, minimum i maksimum wszystkich udanych zainicjowanych z powrotem sznurów danych.|Brak|
|C2D. splot. Update. Success|Pomyślne aktualizacje bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|
|C2D. splot. Update. Failure|Niepowodzenie aktualizacji bliźniaczych z zaplecza|Liczba|Łącznie|Liczba wszystkich niezakończonych niepowodzeniem aktualizacji typu bliźniaczych zainicjowanych z powrotem.|Brak|
|C2D. splot. Update. size|Rozmiar aktualizacji przędzy od zaplecza|Bajty|Średnia|Średni, minimalny i maksymalny rozmiar wszystkich pomyślnych aktualizacji typu sznurka zainicjowanych z powrotem.|Brak|
|twinQueries. Success|Pomyślne zapytania bliźniaczy|Liczba|Łącznie|Liczba wszystkich udanych zapytań bliźniaczych.|Brak|
|twinQueries. Failure|Niepowodzenie zapytań bliźniaczych|Liczba|Łącznie|Liczba wszystkich zakończonych niepowodzeniem zapytań bliźniaczych.|Brak|
|twinQueries.resultSize|Rozmiar wyniku zapytań bliźniaczych|Bajty|Średnia|Średnia, minimalna i maksymalna wielkość wyniku wszystkich udanych zapytań bliźniaczych.|Brak|
|Jobs. createTwinUpdateJob. Success|Pomyślne utworzenie dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich udanych tworzenia zadań aktualizacji z przędzą.|Brak|
|Jobs. createTwinUpdateJob. Failure|Nie można utworzyć dwuosiowych zadań aktualizacji|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań aktualizacji z przędzą.|Brak|
|Jobs. createDirectMethodJob. Success|Pomyślne utworzenie zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich pomyślnych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|Jobs. createDirectMethodJob. Failure|Nie można utworzyć zadań wywołania metody|Liczba|Łącznie|Liczba wszystkich nieudanych operacji tworzenia zadań wywołania metody bezpośredniej.|Brak|
|Jobs. listJobs. Success|Pomyślne wywołania do zadań na liście|Liczba|Łącznie|Liczba wszystkich udanych wywołań do listy zadań.|Brak|
|Jobs. listJobs. Failure|Wywołania zakończone niepowodzeniem do listy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wyświetlenia listy zadań.|Brak|
|Jobs. cancelJob. Success|Pomyślne anulowania zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do anulowania zadania.|Brak|
|Jobs. cancelJob. Failure|Nieudane anulowania zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu anulowania zadania.|Brak|
|Jobs. queryJobs. Success|Pomyślne zapytania dotyczące zadań|Liczba|Łącznie|Liczba wszystkich udanych wywołań do zadań zapytań.|Brak|
|Jobs. queryJobs. Failure|Nieudane kwerendy zadań|Liczba|Łącznie|Liczba wszystkich wywołań zakończonych niepowodzeniem w celu wykonywania zapytań dotyczących zadań.|Brak|
|zadania. ukończone|Ukończone zadania|Liczba|Łącznie|Liczba wszystkich ukończonych zadań.|Brak|
|zadania. Niepowodzenie|Zadania zakończone niepowodzeniem|Liczba|Łącznie|Liczba wszystkich zadań zakończonych niepowodzeniem.|Brak|
|D2C. telemetrię. sendThrottle|Liczba błędów ograniczania|Liczba|Łącznie|Liczba błędów ograniczania z powodu ograniczeń przepływności urządzenia|Brak|
|dailyMessageQuotaUsed|Całkowita liczba użytych komunikatów|Liczba|Średnia|Łączna liczba użytych komunikatów|Brak|
|deviceDataUsage|Całkowite użycie danych urządzenia|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|deviceDataUsageV2|Całkowite użycie danych urządzenia (wersja zapoznawcza)|Bajty|Łącznie|Bajty przesłane do i z dowolnych urządzeń podłączonych do usługi IotHub|Brak|
|totalDeviceCount|Łączna liczba urządzeń (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń zarejestrowanych w usłudze IoT Hub|Brak|
|connectedDeviceCount|Podłączone urządzenia (wersja zapoznawcza)|Liczba|Średnia|Liczba urządzeń podłączonych do centrum IoT Hub|Brak|
|komputerów|Metryki konfiguracji|Liczba|Łącznie|Metryki dla operacji konfiguracji|Brak|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RegistrationAttempts|Próby rejestracji|Liczba|Łącznie|Liczba prób przeprowadzenia rejestracji urządzeń|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Przypisane urządzenia|Liczba|Łącznie|Liczba urządzeń przypisanych do centrum IoT Hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Próby zaświadczania|Liczba|Łącznie|Liczba podjętych prób zaświadczania urządzenia|ProvisioningServiceName, stan, protokół|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft. DocumentDB/databaseAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Addregion|Dodano region|Liczba|Liczba|Dodano region|Region|
|AvailableStorage|Dostępny magazyn|Bajty|Łącznie|Łączna ilość dostępnego magazynu zgłoszona z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|CassandraConnectionClosures|Zamknięcia połączeń Cassandra|Liczba|Łącznie|Liczba zamkniętych połączeń Cassandra, które zostały zgłoszone z dokładnością do 1 minuty|APIType, region, ClosureReason|
|CassandraKeyspaceDelete|Cassandra usunięto przestrzeń kluczy|Liczba|Liczba|Cassandra usunięto przestrzeń kluczy|ResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra przepustowość obszaru kluczy|Liczba|Liczba|Cassandra przepustowość obszaru kluczy|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra miejsce na dysku|Liczba|Liczba|Cassandra miejsce na dysku|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Opłaty za żądania Cassandra|Liczba|Łącznie|Jednostek ru zużyte dla żądań Cassandra|APIType, DatabaseName, CollectionName, region, OperationType, ResourceType|
|CassandraRequests|Żądania Cassandra|Liczba|Liczba|Liczba wykonanych żądań Cassandra|APIType, DatabaseName, CollectionName, region, OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Usunięto tabelę Cassandra|Liczba|Liczba|Usunięto tabelę Cassandra|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Zaktualizowano przepływność tabeli Cassandra|Liczba|Liczba|Zaktualizowano przepływność tabeli Cassandra|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Zaktualizowano tabelę Cassandra|Liczba|Liczba|Zaktualizowano tabelę Cassandra|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|Konto|Utworzono konto|Liczba|Liczba|Utworzono konto|Brak|
|Datausage|Użycie danych|Bajty|Łącznie|Całkowite użycie danych zgłoszone z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DeleteAccount|Konto zostało usunięte|Liczba|Liczba|Konto zostało usunięte|Brak|
|DocumentCount|Liczba dokumentów|Liczba|Łącznie|Łączna liczba dokumentów raportowana z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|DocumentQuota|Przydział dokumentu|Bajty|Łącznie|Łączny przydział magazynu zgłoszony z dokładnością do 5 minut|CollectionName, DatabaseName, region|
|GremlinDatabaseDelete|Baza danych Gremlin została usunięta|Liczba|Liczba|Baza danych Gremlin została usunięta|ResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Zaktualizowano przepływność bazy danych Gremlin|Liczba|Liczba|Zaktualizowano przepływność bazy danych Gremlin|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Baza danych Gremlin została zaktualizowana|Liczba|Liczba|Baza danych Gremlin została zaktualizowana|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Wykres Gremlin został usunięty|Liczba|Liczba|Wykres Gremlin został usunięty|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Zaktualizowano przepływność grafu Gremlin|Liczba|Liczba|Zaktualizowano przepływność grafu Gremlin|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Zaktualizowano Graf Gremlin|Liczba|Liczba|Zaktualizowano Graf Gremlin|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Użycie indeksu|Bajty|Łącznie|Całkowite użycie indeksów zgłoszone na 5 minut|CollectionName, DatabaseName, region|
|MetadataRequests|Żądania metadanych|Liczba|Liczba|Liczba żądań metadanych. Cosmos DB przechowuje zbieranie metadanych systemu dla każdego konta, które pozwala na Wyliczanie kolekcji, baz danych itp. i ich konfiguracji bez opłat.|DatabaseName, CollectionName, region, StatusCode, rola|
|MongoCollectionDelete|Kolekcja Mongo została usunięta|Liczba|Liczba|Kolekcja Mongo została usunięta|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Zaktualizowano przepływność kolekcji Mongo|Liczba|Liczba|Zaktualizowano przepływność kolekcji Mongo|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Aktualizacja kolekcji Mongo|Liczba|Liczba|Aktualizacja kolekcji Mongo|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Baza danych Mongo została zaktualizowana|Liczba|Liczba|Baza danych Mongo została zaktualizowana|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Baza danych Mongo została usunięta|Liczba|Liczba|Baza danych Mongo została usunięta|ResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Zaktualizowano przepływność bazy danych Mongo|Liczba|Liczba|Zaktualizowano przepływność bazy danych Mongo|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Opłata żądania Mongo|Liczba|Łącznie|Wykorzystane jednostki żądania Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode, status|
|MongoRequests|Żądania Mongo|Liczba|Liczba|Liczba wykonanych żądań Mongo|DatabaseName, CollectionName, region, CommandName, ErrorCode, status|
|MongoRequestsCount|Częstotliwość żądania Mongo|CountPerSecond|Średnia|Liczba żądań Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo — wskaźnik żądania usunięcia|CountPerSecond|Średnia|Żądanie usunięcia Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsInsert|Liczba żądań wstawienia Mongo|CountPerSecond|Średnia|Liczba operacji wstawiania Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsQuery|Częstotliwość żądań zapytań Mongo|CountPerSecond|Średnia|Żądania zapytania Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|MongoRequestsUpdate|Częstotliwość żądań aktualizacji Mongo|CountPerSecond|Średnia|Żądanie aktualizacji Mongo na sekundę|DatabaseName, CollectionName, region, CommandName, ErrorCode|
|NormalizedRUConsumption|Znormalizowane użycie RU|Wartość procentowa|Maksimum|Maksymalna wartość procentowa zużycia RU na minutę|CollectionName, DatabaseName, region|
|ProvisionedThroughput|Aprowizowana przepływność|Liczba|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|RegionFailover|Region w trybie failover|Liczba|Liczba|Region w trybie failover|Brak|
|RemoveRegion|Usunięto region|Liczba|Liczba|Usunięto region|Region|
|ReplicationLatency|Opóźnienie replikacji poziomie P99|)|Średnia|Opóźnienie replikacji poziomie P99 w regionach źródłowym i docelowym dla konta z obsługą geograficzną|SourceRegion,TargetRegion|
|ServerSideLatency|Opóźnienie po stronie serwera|)|Średnia|Opóźnienie po stronie serwera|DatabaseName, CollectionName, region, Connectionmode, OperationType, PublicAPIType|
|Dostępność|Dostępność usługi|Wartość procentowa|Średnia|Dostępność żądania konta o jednej godzinie, dniu lub o dokładności|Brak|
|SqlContainerDelete|Usunięto kontener SQL|Liczba|Liczba|Usunięto kontener SQL|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|Zaktualizowano przepływność kontenera SQL|Liczba|Liczba|Zaktualizowano przepływność kontenera SQL|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|Zaktualizowano kontener SQL|Liczba|Liczba|Zaktualizowano kontener SQL|ResourceName, ChildResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|Baza danych SQL została usunięta|Liczba|Liczba|Baza danych SQL została usunięta|ResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|Zaktualizowano przepływność bazy danych SQL|Liczba|Liczba|Zaktualizowano przepływność bazy danych SQL|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|Zaktualizowano bazę danych SQL|Liczba|Liczba|Zaktualizowano bazę danych SQL|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|Usunięto tabelę platformy Azure|Liczba|Liczba|Usunięto tabelę platformy Azure|ResourceName, rodzaju interfejsu API, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|Zaktualizowano przepływność tabeli usługi Azure Table|Liczba|Liczba|Zaktualizowano przepływność tabeli usługi Azure Table|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|Zaktualizowano tabelę platformy Azure|Liczba|Liczba|Zaktualizowano tabelę platformy Azure|ResourceName, rodzaju interfejsu API, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Łączna liczba jednostek żądania|Liczba|Łącznie|Wykorzystane jednostki żądania|DatabaseName, CollectionName, region, StatusCode, OperationType, status|
|TotalRequests|Łączna liczba żądań|Liczba|Liczba|Liczba wykonanych żądań|DatabaseName, CollectionName, region, StatusCode, OperationType, status|
|UpdateAccountKeys|Zaktualizowano klucze konta|Liczba|Liczba|Zaktualizowano klucze konta|KeyType|
|UpdateAccountNetworkSettings|Zaktualizowano ustawienia sieci konta|Liczba|Liczba|Zaktualizowano ustawienia sieci konta|Brak|
|UpdateAccountReplicationSettings|Zaktualizowano ustawienia replikacji konta|Liczba|Liczba|Zaktualizowano ustawienia replikacji konta|Brak|
|UpdateDiagnosticsSettings|Zaktualizowano ustawienia diagnostyczne konta|Liczba|Liczba|Zaktualizowano ustawienia diagnostyczne konta|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/usługi

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TransactionCount|Liczba transakcji|Liczba|Liczba|Łączna liczba transakcji|TransactionCount|
|SuccessCount|Liczba powodzeń|Liczba|Liczba|Liczba zakończonych pomyślnie transakcji|SuccessCount|
|FailureCount|Liczba niepowodzeń|Liczba|Liczba|Liczba transakcji zakończonych niepowodzeniem|FailureCount|
|SuccessLatency|Opóźnienie sukcesu|)|Średnia|Opóźnienie transakcji zakończonych powodzeniem|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domeny

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Temat|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|Temat, Błądtype, błąd|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak|
|MatchedEventCount|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Temat, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Temat, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|Temat, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/tematy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak|
|MatchedEventCount|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason,EventSubscriptionName|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak|
|MatchedEventCount|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|EventSubscriptionName|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|EventSubscriptionName|
|DroppedEventCount|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason,EventSubscriptionName|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Dopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń dopasowanych do tej subskrypcji zdarzeń|Brak|
|DeliveryAttemptFailCount|Zdarzenia zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których dostarczenie do tej subskrypcji zdarzeń nie powiodło się|Błąd, Błądtype|
|DeliverySuccessCount|Dostarczone zdarzenia|Liczba|Łącznie|Całkowita liczba zdarzeń dostarczonych do tej subskrypcji zdarzeń|Brak|
|DestinationProcessingDurationInMs|Czas przetwarzania docelowego|)|Średnia|Czas trwania przetwarzania docelowego w milisekundach|Brak|
|DroppedEventCount|Opuszczone zdarzenia|Liczba|Łącznie|Całkowita liczba porzuconych zdarzeń pasujących do tej subskrypcji zdarzeń|DropReason|
|DeadLetteredCount|Zdarzenia utraconych wiadomości|Liczba|Łącznie|Łączna liczba utraconych zdarzeń, które pasują do tej subskrypcji zdarzeń|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Zdarzenia opublikowane|Liczba|Łącznie|Łączna liczba zdarzeń opublikowanych w tym temacie|Brak|
|PublishFailCount|Publikowanie zdarzeń zakończonych niepowodzeniem|Liczba|Łącznie|Całkowita liczba zdarzeń, których publikowanie nie powiodło się w tym temacie|ErrorType, błąd|
|UnmatchedEventCount|Niedopasowane zdarzenia|Liczba|Łącznie|Łączna liczba zdarzeń, które nie pasują do żadnej subskrypcji zdarzeń dla tego tematu|Brak|
|PublishSuccessLatencyInMs|Czas oczekiwania na pomyślne publikowanie|)|Łącznie|Opóźnienie sukcesu publikacji w milisekundach|Brak|




## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Liczba|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Błędy servererrors|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Błędy usererrors|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału.|Liczba|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|ThrottledRequests|Żądania z ograniczeniami.|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|EntityName, klasy OperationResult|
|Żądania incomingrequests|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty incomingmessages|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|EntityName|
|Komunikaty outgoingmessages|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Bajty incomingbytes|Bajty przychodzące.|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|EntityName|
|Bajty outgoingbytes|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Liczba|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|Brak|
|Połączenia connectionsopened|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub.|EntityName|
|Połączenia connectionsclosed|Połączenia zamknięte.|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub.|EntityName|
|CaptureBacklog|Zaległości przechwytywania.|Liczba|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|EntityName|
|CapturedMessages|Przechwycone komunikaty.|Liczba|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|EntityName|
|CapturedBytes|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|EntityName|
|Rozmiar|Rozmiar|Bajty|Średnia|Rozmiar centrum EventHub w bajtach.|EntityName|
|INREQS|Żądania przychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba żądań wysłania przychodzącego dla przestrzeni nazw (przestarzałe)|Brak|
|SUCCREQ|Żądania zakończone powodzeniem (przestarzałe)|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw (przestarzałe)|Brak|
|FAILREQ|Nieudane żądania (przestarzałe)|Liczba|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Brak|
|SVRBSY|Błędy zajęte przez serwer (przestarzałe)|Liczba|Łącznie|Łączna liczba błędów zajętości serwera dla przestrzeni nazw (przestarzałe)|Brak|
|MIĘDZY|Wewnętrzne błędy serwera (przestarzałe)|Liczba|Łącznie|Łączna liczba błędów wewnętrznego serwera dla przestrzeni nazw (przestarzałe)|Brak|
|MISCERR|Inne błędy (przestarzałe)|Liczba|Łącznie|Łączna liczba nieudanych żądań dla przestrzeni nazw (przestarzałe)|Brak|
|INMSGS|Komunikaty przychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów przychodzących (przestarzałe)|Brak|
|EHINMSGS|Komunikaty przychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów przychodzących dla przestrzeni nazw (przestarzałe)|Brak|
|OUTMSGS|Wiadomości wychodzące (przestarzałe) (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki komunikatów wychodzących (przestarzałe)|Brak|
|EHOUTMSGS|Wiadomości wychodzące (przestarzałe)|Liczba|Łącznie|Całkowita liczba komunikatów wychodzących dla przestrzeni nazw (przestarzałe)|Brak|
|EHINMBS|Przychodzące bajty (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów przychodzących (przestarzałe)|Brak|
|EHINBYTES|Przychodzące bajty (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów przychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Brak|
|EHOUTMBS|Bajty wychodzące (przestarzałe) (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw. Ta Metryka jest przestarzała. Zamiast tego użyj metryki bajtów wychodzących (przestarzałe)|Brak|
|EHOUTBYTES|Bajty wychodzące (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów wychodzących centrum zdarzeń dla przestrzeni nazw (przestarzałe)|Brak|
|EHABL|Archiwum komunikatów zaległości (przestarzałe)|Liczba|Łącznie|Komunikaty archiwum centrum zdarzeń w zaległości dla przestrzeni nazw (przestarzałe)|Brak|
|EHAMSGS|Komunikaty archiwalne (przestarzałe)|Liczba|Łącznie|Zarchiwizowane komunikaty centrum zdarzeń w przestrzeni nazw (przestarzałe)|Brak|
|EHAMBS|Przepływność komunikatów archiwalnych (przestarzałe)|Bajty|Łącznie|Przepływność komunikatów archiwalnych centrum zdarzeń w przestrzeni nazw (przestarzałe)|Brak|

## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Liczba|Łącznie|Pomyślne żądania dla elementu Microsoft. EventHub.|Klasy OperationResult|
|Błędy servererrors|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. EventHub.|Klasy OperationResult|
|Błędy usererrors|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. EventHub.|Klasy OperationResult|
|Błędy quotaexceedederrors|Błędy przekroczenia limitu przydziału.|Liczba|Łącznie|Przekroczono limit przydziału dla elementu Microsoft. EventHub.|Klasy OperationResult|
|ThrottledRequests|Żądania z ograniczeniami.|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. EventHub.|Klasy OperationResult|
|Żądania incomingrequests|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla elementu Microsoft. EventHub.|Brak|
|Komunikaty incomingmessages|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. EventHub.|Brak|
|Komunikaty outgoingmessages|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. EventHub.|Brak|
|Bajty incomingbytes|Bajty przychodzące.|Bajty|Łącznie|Przychodzące bajty dla elementu Microsoft. EventHub.|Brak|
|Bajty outgoingbytes|Bajty wychodzące.|Bajty|Łącznie|Bajty wychodzące dla elementu Microsoft. EventHub.|Brak|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Liczba|Średnia|Łączna liczba aktywnych połączeń dla elementu Microsoft. EventHub.|Brak|
|Połączenia connectionsopened|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. EventHub.|Brak|
|Połączenia connectionsclosed|Połączenia zamknięte.|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. EventHub.|Brak|
|CaptureBacklog|Zaległości przechwytywania.|Liczba|Łącznie|Zaległości przechwytywania dla elementu Microsoft. EventHub.|Brak|
|CapturedMessages|Przechwycone komunikaty.|Liczba|Łącznie|Przechwycone komunikaty dla elementu Microsoft. EventHub.|Brak|
|CapturedBytes|Przechwycone bajty.|Bajty|Łącznie|Przechwycone bajty dla elementu Microsoft. EventHub.|Brak|
|Procesor CPU|Procesor CPU|Wartość procentowa|Maksimum|Użycie procesora CPU przez klaster centrum zdarzeń jako wartość procentowa|Rola|
|AvailableMemory|Dostępna pamięć|Wartość procentowa|Maksimum|Dostępna pamięć dla klastra centrum zdarzeń jako procent całkowitej ilości pamięci.|Rola|
|Rozmiar|Rozmiar centrum EventHub w bajtach.|Bajty|Średnia|Rozmiar centrum EventHub w bajtach.|Rola|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/Klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|GatewayRequests|Żądania bramy|Liczba|Łącznie|Liczba żądań bramy|Wartości httpStatus|
|CategorizedGatewayRequests|Skategoryzowane żądania bramy|Liczba|Łącznie|Liczba żądań bramy według kategorii (1XX/2xx/3xx/4xx/5xx)|Wartości httpStatus|
|NumActiveWorkers|Liczba aktywnych procesów roboczych|Liczba|Maksimum|Liczba aktywnych procesów roboczych|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ObservedMetricValue|Obserwowana wartość metryki|Liczba|Średnia|Wartość obliczona przez automatyczne skalowanie podczas wykonywania|MetricTriggerSource|
|MetricThreshold|Próg metryki|Liczba|Średnia|Skonfigurowany próg automatycznego skalowania podczas uruchamiania automatycznego skalowania.|MetricTriggerRule|
|ObservedCapacity|Zaobserwowana pojemność|Liczba|Średnia|Pojemność zgłoszona do automatycznego skalowania podczas jego wykonywania.|Brak|
|ScaleActionsInitiated|Zainicjowane akcje skalowania|Liczba|Łącznie|Kierunek operacji skalowania.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/składniki

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostępność|Wartość procentowa|Średnia|Procent pomyślnie ukończonych testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja|
|availabilityResults/liczba|Testy dostępności|Liczba|Liczba|Liczba testów dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|availabilityResults/czas trwania|Czas trwania testu dostępności|)|Średnia|Czas trwania testu dostępności|availabilityResult/nazwa, availabilityResult/lokalizacja, availabilityResult/sukces|
|browserTimings/networkDuration|Czas połączenia sieciowego ładowania strony|)|Średnia|Czas między żądaniem użytkownika a połączeniem sieciowym. Obejmuje wyszukiwanie DNS i połączenie transportowe.|Brak|
|browserTimings/processingDuration|Czas przetwarzania klienta|)|Średnia|Czas między odebraniem ostatniego bajtu dokumentu do momentu załadowania modelu DOM. Żądania asynchroniczne nadal mogą być przetwarzane.|Brak|
|browserTimings/receiveDuration|Czas odpowiedzi na odebranie|)|Średnia|Czas między pierwszym i ostatnim bajtem lub do momentu odłączenia.|Brak|
|browserTimings/sendDuration|Czas żądania wysłania|)|Średnia|Czas między połączeniem sieciowym i odebraniem pierwszego bajtu.|Brak|
|browserTimings/totalDuration|Czas ładowania strony w przeglądarce|)|Średnia|Czas od żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów.|Brak|
|zależności/liczba|Wywołania zależności|Liczba|Liczba|Liczba wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, zależność/resultCode, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/czas trwania|Czas trwania zależności|)|Średnia|Czas trwania wywołań wykonanych przez aplikację do zasobów zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, zależność/resultCode, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|zależności/niepowodzenie|Błędy wywołań zależności|Liczba|Liczba|Liczba wywołań zależności zakończonych niepowodzeniem wykonanych przez aplikację w zasobach zewnętrznych.|zależność/typ, zależność/performanceBucket, zależność/powodzenie, zależność/cel, zależność/resultCode, operacja/syntetyczne, Chmura/roleInstance, Chmura/rolename|
|pageViews/liczba|Wyświetlenia stron|Liczba|Liczba|Liczba wyświetleń stron.|Operacja/syntetyczne, Chmura/rolename|
|pageViews/czas trwania|Czas ładowania widoku strony|)|Średnia|Czas ładowania widoku strony|Operacja/syntetyczne, Chmura/rolename|
|Liczniki wydajności/requestExecutionTime|Czas wykonywania żądania HTTP|)|Średnia|Czas wykonywania najnowszego żądania.|Chmura/roleInstance|
|Liczniki wydajności/requestsInQueue|Żądania HTTP w kolejce aplikacji|Liczba|Średnia|Długość kolejki żądań aplikacji.|Chmura/roleInstance|
|Liczniki wydajności/requestsPerSecond|Częstotliwość żądań HTTP|CountPerSecond|Średnia|Szybkość wszystkich żądań wysyłanych do aplikacji w ciągu sekundy od ASP.NET.|Chmura/roleInstance|
|Liczniki wydajności/exceptionsPerSecond|Częstotliwość wyjątków|CountPerSecond|Średnia|Liczba obsłużonych i nieobsłużonych wyjątków zgłoszonych w systemie Windows, łącznie z wyjątkami platformy .NET i niezarządzanymi wyjątkami przekonwertowanymi na wyjątki platformy .NET.|Chmura/roleInstance|
|Liczniki wydajności/processIOBytesPerSecond|Szybkość operacji we/wy procesu|BytesPerSecond|Średnia|Łączna liczba bajtów odczytanych i zapisywana w plikach, sieci i urządzeniach.|Chmura/roleInstance|
|Liczniki wydajności/processCpuPercentage|Procesor CPU procesu|Wartość procentowa|Średnia|Wyrażony w procentach czas, przez jaki wszystkie wątki procesów używały procesora do wykonywania instrukcji. Może się to różnić od od 0 do 100. Ta Metryka wskazuje na wydajność samego procesu w3wp.|Chmura/roleInstance|
|Liczniki wydajności/processorCpuPercentage|Czas procesora|Wartość procentowa|Średnia|Procent czasu spędzanego przez procesor w wątkach, które nie są bezczynne.|Chmura/roleInstance|
|Liczniki wydajności/memoryAvailableBytes|Dostępna pamięć|Bajty|Średnia|Pamięć fizyczna natychmiast dostępna do przydzielenia do procesu lub do użycia przez system.|Chmura/roleInstance|
|Liczniki wydajności/processPrivateBytes|Prywatne bajty procesu|Bajty|Średnia|Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji.|Chmura/roleInstance|
|żądania/czas trwania|Czas odpowiedzi serwera|)|Średnia|Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|Liczba żądań na sekundę|Żądania serwera|Liczba|Liczba|Liczba ukończonych żądań HTTP.|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|żądania/niepowodzenie|Żądania zakończone niepowodzeniem|Liczba|Liczba|Liczba żądań HTTP oznaczonych jako zakończone niepowodzeniem. W większości przypadków są to żądania z kodem odpowiedzi >= 400, a nie równe 401.|żądanie/performanceBucket, żądanie/resultCode, żądanie/sukces, operacja/syntetyczne, Cloud/roleInstance, Cloud/rolename|
|żądania/częstotliwość|Liczba żądań serwera|CountPerSecond|Średnia|Liczba żądań serwera na sekundę|żądanie/performanceBucket, żądanie/resultCode, Operation/syntetyczne, Cloud/roleInstance, żądanie/powodzenie, Chmura/rolename|
|wyjątki/liczba|Wyjątki|Liczba|Liczba|Łączna liczba wszystkich nieprzechwyconych wyjątków.|Chmura/rolename, Cloud/roleInstance, klient/typ|
|wyjątki/przeglądarka|Wyjątki przeglądarki|Liczba|Liczba|Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce.|Klient/isserwer, Chmura/rolename|
|wyjątki/serwer|Wyjątki serwera|Liczba|Liczba|Liczba nieprzechwyconych wyjątków zgłoszonych w aplikacji serwera.|Klient/isserwer, Chmura/rolename, Cloud/roleInstance|
|ślady/liczba|Ślady|Liczba|Liczba|Liczba dokumentów śledzenia|Trace/severityLevel, Operations/syntetyczne, Cloud/rolename, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedDeviceCount|Łączna liczba połączonych urządzeń|Liczba|Średnia|Liczba urządzeń podłączonych do IoT Central|Brak|
|C2D. Property. Read. Success|Pomyślne odczytywanie właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości inicjowanych z IoT Central|Brak|
|C2D. Property. Read. Failure|Niepowodzenie odczytywania właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich odczytów właściwości zakończonych niepowodzeniem inicjowanych z IoT Central|Brak|
|D2C. Property. Read. Success|Pomyślne odczyty właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych odczytów właściwości zainicjowanych z urządzeń|Brak|
|D2C. Property. Read. Failure|Niepowodzenie odczytywania właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich odczytów właściwości zakończonych niepowodzeniem zainicjowanych z urządzeń|Brak|
|C2D. Property. Update. Success|Pomyślne aktualizacje właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z IoT Central|Brak|
|C2D. Property. Update. Failure|Nieudane aktualizacje właściwości urządzenia z IoT Central|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości zakończonych niepowodzeniem zainicjowanych z IoT Central|Brak|
|D2C. Property. Update. Success|Pomyślne aktualizacje właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich pomyślnych aktualizacji właściwości zainicjowanych z urządzeń|Brak|
|D2C. Property. Update. Failure|Nieudane aktualizacje właściwości urządzenia z urządzeń|Liczba|Łącznie|Liczba wszystkich aktualizacji właściwości zakończonych niepowodzeniem zainicjowanych z urządzeń|Brak|


## <a name="microsoftkeyvaultvaults"></a>Microsoft./magazyny kluczy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServiceApiHit|Całkowita liczba trafień interfejsu API usługi|Liczba|Liczba|Łączna liczba trafień interfejsu API usługi|ActivityType, ActivityName|
|ServiceApiLatency|Ogólne opóźnienie interfejsu API usługi|)|Średnia|Ogólne opóźnienie żądań interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Łączna liczba wyników interfejsu API usługi|Liczba|Liczba|Łączna liczba wyników interfejsu API usługi|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Ogólne nasycenie magazynu|Wartość procentowa|Średnia|Używane pojemność magazynu|ActivityType, ActivityName, TransactionType|
|Dostępność|Ogólna dostępność magazynu|Wartość procentowa|Średnia|Dostępność żądań magazynu|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/klastry

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CacheUtilization|Użycie pamięci podręcznej|Wartość procentowa|Średnia|Poziom użycia w zakresie klastra|Brak|
|QueryDuration|Czas trwania zapytania|)|Średnia|Czas trwania zapytań (w sekundach)|QueryStatus|
|IngestionUtilization|Wykorzystanie pozyskiwania|Wartość procentowa|Średnia|Współczynnik używania miejsc pozyskiwania w klastrze|Brak|
|Utrzymywani|Utrzymywanie aktywności|Liczba|Średnia|Sprawdzenie Sanity wskazuje, że klaster reaguje na zapytania|Brak|
|IngestionVolumeInMB|Wolumin pozyskiwania (w MB)|Liczba|Łącznie|Ogólna ilość danych pozyskiwanych w klastrze (w MB)|baza danych|
|IngestionLatencyInSeconds|Opóźnienie pozyskiwania (w sekundach)|Sekundy|Średnia|Czas pozyskiwania ze źródła (np. komunikat jest w centrum EventHub) do klastra w ciągu kilku sekund|Brak|
|EventsProcessedForEventHubs|Zdarzenia przetwarzane (dla centrów zdarzeń/IoT)|Liczba|Łącznie|Liczba zdarzeń przetwarzanych przez klaster podczas pozyskiwania ze zdarzenia/IoT Hub|EventStatus|
|IngestionResult|Wynik pozyskiwania|Liczba|Liczba|Liczba operacji pozyskiwania|IngestionResultDetails|
|Procesor CPU|Procesor CPU|Wartość procentowa|Średnia|Poziom użycia procesora CPU|Brak|
|ContinuousExportNumOfRecordsExported|Eksport ciągły — liczba eksportowanych rekordów|Liczba|Łącznie|Liczba wyeksportowanych rekordów, które są generowane dla każdego artefaktu magazynu zapisanego podczas operacji eksportowania|ContinuousExportName, baza danych|
|ExportUtilization|Użycie eksportu|Wartość procentowa|Maksimum|Użycie eksportu|Brak|
|ContinuousExportPendingCount|Liczba oczekujących eksportu ciągłego|Liczba|Maksimum|Liczba oczekujących zadań eksportu ciągłego gotowych do wykonania|Brak|
|ContinuousExportMaxLatenessMinutes|Maksymalna liczba opóźnień eksportu ciągłego|Liczba|Maksimum|Opóźnienie (w minutach) raportowane przez zadania eksportu ciągłego w klastrze|Brak|
|ContinuousExportResult|Wynik eksportu ciągłego|Liczba|Liczba|Wskazuje, czy ciągły eksport zakończył się powodzeniem, czy niepowodzeniem|ContinuousExportName, wynik, baza danych|
|StreamingIngestDuration|Czas trwania pozyskiwania strumieniowego|)|Średnia|Czas trwania pozyskiwania strumieniowego w milisekundach|Brak|
|StreamingIngestDataRate|Szybkość danych pozyskiwania przesyłania strumieniowego|Liczba|Średnia|Szybkość danych pozyskiwania przesyłania strumieniowego (MB na sekundę)|Brak|
|SteamingIngestRequestRate|Szybkość żądania pozyskiwania strumieniowego|Liczba|RateRequestsPerSecond|Szybkość żądania pozyskiwania strumieniowego (żądania na sekundę)|Brak|
|StreamingIngestResults|Wynik pozyskiwania strumieniowego|Liczba|Średnia|Wynik pozyskiwania strumieniowego|Wynik|
|TotalNumberOfConcurrentQueries|Łączna liczba współbieżnych zapytań|Liczba|Łącznie|Łączna liczba współbieżnych zapytań|Brak|
|TotalNumberOfThrottledQueries|Łączna liczba zapytań z ograniczeniami|Liczba|Łącznie|Łączna liczba zapytań z ograniczeniami|Brak|
|TotalNumberOfThrottledCommands|Łączna liczba poleceń z ograniczeniami|Liczba|Łącznie|Łączna liczba poleceń z ograniczeniami|CommandType|
|TotalNumberOfExtents|Łączna Liczba zakresów|Liczba|Łącznie|Łączna Liczba zakresów danych|Brak|
|InstanceCount|Liczba wystąpień|Liczba|Średnia|Łączna liczba wystąpień|Brak|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/przepływy pracy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Uruchomienia uruchomione|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak|
|RunsCompleted|Przebiegi zakończone|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Brak|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak|
|RunsCancelled|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak|
|RunLatency|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Brak|
|RunSuccessLatency|Opóźnienie sukcesu przebiegu|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Brak|
|RunThrottledEvents|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Brak|
|RunStartThrottledEvents|Uruchom zdarzenia uruchamiania z ograniczeniami|Liczba|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|Brak|
|RunFailurePercentage|Procent niepowodzeń przebiegu|Wartość procentowa|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak|
|ActionsStarted|Akcje uruchomione |Liczba|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Brak|
|ActionsCompleted|Wykonane akcje |Liczba|Łącznie|Liczba ukończonych akcji przepływu pracy.|Brak|
|ActionsSucceeded|Akcje zakończone powodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Brak|
|ActionsFailed|Akcje zakończone niepowodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Brak|
|ActionsSkipped|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak|
|ActionLatency|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Brak|
|ActionSuccessLatency|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Brak|
|ActionThrottledEvents|Zdarzenia ograniczające akcję|Liczba|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Brak|
|TriggersStarted|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak|
|TriggersCompleted|Ukończone wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggersSucceeded|Wyzwalacze zakończone powodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak|
|TriggersFailed|Wyzwalacze zakończone niepowodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Brak|
|TriggersSkipped|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak|
|TriggersFired|Wyzwolone wyzwalacze |Liczba|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Brak|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggerFireLatency|Opóźnienie wyzwalania wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak|
|TriggerThrottledEvents|Wyzwalaj zdarzenia ograniczające|Liczba|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Brak|
|BillableActionExecutions|Rozliczane wykonania akcji|Liczba|Łącznie|Liczba rozliczanych wykonań akcji przepływu pracy.|Brak|
|BillableTriggerExecutions|Rozliczane wykonania wyzwalacza|Liczba|Łącznie|Liczba rozliczanych wykonań wyzwalacza przepływu pracy.|Brak|
|TotalBillableExecutions|Łączna Liczba wykonań rozliczanych|Liczba|Łącznie|Liczba rozliczanych wykonań przepływu pracy.|Brak|
|BillingUsageNativeOperation|Użycie rozliczeń dla natywnych wykonań operacji|Liczba|Łącznie|Liczba rozliczanych wykonań operacji natywnych.|Brak|
|BillingUsageStandardConnector|Użycie rozliczeń dla wykonywania łączników standardowych|Liczba|Łącznie|Liczba rozliczanych wykonań łączników standardowych.|Brak|
|BillingUsageStorageConsumption|Użycie rozliczeń dla wykonań zużycia magazynu|Liczba|Łącznie|Liczba rozliczanych wykonań zużycia magazynu.|Brak|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Uruchomienia uruchomione|Liczba|Łącznie|Liczba uruchomionych przebiegów przepływu pracy.|Brak|
|RunsCompleted|Przebiegi zakończone|Liczba|Łącznie|Liczba ukończonych przebiegów przepływu pracy.|Brak|
|RunsSucceeded|Przebiegi zakończone powodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych powodzeniem.|Brak|
|RunsFailed|Przebiegi zakończone niepowodzeniem|Liczba|Łącznie|Liczba przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak|
|RunsCancelled|Przebiegi anulowane|Liczba|Łącznie|Liczba anulowanych przebiegów przepływu pracy.|Brak|
|RunLatency|Opóźnienie uruchamiania|Sekundy|Średnia|Opóźnienie ukończonych przebiegów przepływu pracy.|Brak|
|RunSuccessLatency|Opóźnienie sukcesu przebiegu|Sekundy|Średnia|Opóźnienie przebiegów przepływu pracy zakończonych powodzeniem.|Brak|
|RunThrottledEvents|Uruchamianie zdarzeń z ograniczeniami|Liczba|Łącznie|Liczba zdarzeń związanych ze zdarzeniami przepływu pracy lub wyzwalaczem.|Brak|
|RunStartThrottledEvents|Uruchom zdarzenia uruchamiania z ograniczeniami|Liczba|Łącznie|Liczba uruchomionych zdarzeń ograniczających przepływ pracy.|Brak|
|RunFailurePercentage|Procent niepowodzeń przebiegu|Wartość procentowa|Łącznie|Procent przebiegów przepływu pracy zakończonych niepowodzeniem.|Brak|
|ActionsStarted|Akcje uruchomione |Liczba|Łącznie|Liczba rozpoczętych akcji przepływu pracy.|Brak|
|ActionsCompleted|Wykonane akcje |Liczba|Łącznie|Liczba ukończonych akcji przepływu pracy.|Brak|
|ActionsSucceeded|Akcje zakończone powodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych powodzeniem.|Brak|
|ActionsFailed|Akcje zakończone niepowodzeniem |Liczba|Łącznie|Liczba akcji przepływu pracy zakończonych niepowodzeniem.|Brak|
|ActionsSkipped|Pominięte akcje |Liczba|Łącznie|Liczba pominiętych akcji przepływu pracy.|Brak|
|ActionLatency|Opóźnienie akcji |Sekundy|Średnia|Opóźnienie ukończonych akcji przepływu pracy.|Brak|
|ActionSuccessLatency|Opóźnienie sukcesu akcji |Sekundy|Średnia|Opóźnienie akcji przepływu pracy zakończonych powodzeniem.|Brak|
|ActionThrottledEvents|Zdarzenia ograniczające akcję|Liczba|Łącznie|Liczba zdarzeń ograniczających przepływ pracy...|Brak|
|TriggersStarted|Uruchomiono wyzwalacze |Liczba|Łącznie|Liczba uruchomionych wyzwalaczy przepływu pracy.|Brak|
|TriggersCompleted|Ukończone wyzwalacze |Liczba|Łącznie|Liczba ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggersSucceeded|Wyzwalacze zakończone powodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak|
|TriggersFailed|Wyzwalacze zakończone niepowodzeniem |Liczba|Łącznie|Liczba wyzwalaczy przepływu pracy zakończonych niepowodzeniem.|Brak|
|TriggersSkipped|Pominięte wyzwalacze|Liczba|Łącznie|Liczba pominiętych wyzwalaczy przepływu pracy.|Brak|
|TriggersFired|Wyzwolone wyzwalacze |Liczba|Łącznie|Liczba wyzwolonych wyzwalaczy przepływu pracy.|Brak|
|TriggerLatency|Opóźnienie wyzwalacza |Sekundy|Średnia|Opóźnienie ukończonych wyzwalaczy przepływu pracy.|Brak|
|TriggerFireLatency|Opóźnienie wyzwalania wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalanych wyzwalaczy przepływu pracy.|Brak|
|TriggerSuccessLatency|Opóźnienie sukcesu wyzwalacza |Sekundy|Średnia|Opóźnienie wyzwalaczy przepływu pracy zakończonych powodzeniem.|Brak|
|TriggerThrottledEvents|Wyzwalaj zdarzenia ograniczające|Liczba|Łącznie|Liczba zdarzeń ograniczenia wyzwalacza przepływu pracy.|Brak|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Użycie procesora przepływu pracy dla środowisko usługi integracji|Wartość procentowa|Średnia|Użycie procesora przez przepływ pracy dla środowiska usługi integracji.|Brak|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Użycie pamięci przez przepływ pracy dla środowisko usługi integracji|Wartość procentowa|Średnia|Użycie pamięci przez przepływ pracy dla środowiska usługi integracji.|Brak|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Użycie procesora przez łącznik dla środowisko usługi integracji|Wartość procentowa|Średnia|Użycie procesora przez łącznik dla środowiska usługi integracji.|Brak|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Użycie pamięci przez łącznik dla środowisko usługi integracji|Wartość procentowa|Średnia|Użycie pamięci przez łącznik dla środowiska usługi integracji.|Brak|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/obszary robocze

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Anulowane przebiegi|Anulowane przebiegi|Liczba|Łącznie|Liczba anulowanych przebiegów dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Anulowanie żądanych przebiegów|Anulowanie żądanych przebiegów|Liczba|Łącznie|Liczba uruchomień, dla których zażądano anulowania dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Ukończone uruchomienia|Ukończone uruchomienia|Liczba|Łącznie|Liczba przebiegów pomyślnie ukończonych dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Nieudane uruchomienia|Nieudane uruchomienia|Liczba|Łącznie|Liczba uruchomień dla tego obszaru roboczego nie powiodła się|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Finalizowanie przebiegów|Finalizowanie przebiegów|Liczba|Łącznie|Liczba przebiegów w trakcie finalizowania stanu dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Przebiegi nie odpowiadają|Przebiegi nie odpowiadają|Liczba|Łącznie|Liczba przebiegów, które nie odpowiadają w tym obszarze roboczym|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Uruchomienia nieuruchomione|Uruchomienia nieuruchomione|Liczba|Łącznie|Liczba przebiegów w stanie nieuruchomionym dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Przygotowywanie przebiegów|Przygotowywanie przebiegów|Liczba|Łącznie|Liczba przebiegów przygotowywania dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Uruchomienia aprowizacji|Uruchomienia aprowizacji|Liczba|Łącznie|Liczba uruchomień, które są inicjowane dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Uruchomienia w kolejce|Uruchomienia w kolejce|Liczba|Łącznie|Liczba uruchomień umieszczonych w kolejce dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Uruchomione uruchomienia|Uruchomione uruchomienia|Liczba|Łącznie|Liczba rozpoczętych uruchomień dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Uruchamianie przebiegów|Uruchamianie przebiegów|Liczba|Łącznie|Liczba rozpoczętych uruchomień dla tego obszaru roboczego|Scenariusz, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Errors|Errors|Liczba|Łącznie|Liczba błędów uruchomienia w tym obszarze roboczym|Scenariusz|
|Ostrzeżenia|Ostrzeżenia|Liczba|Łącznie|Liczba ostrzeżeń uruchamiania w tym obszarze roboczym|Scenariusz|
|Rejestrowanie modelu powiodło się|Rejestrowanie modelu powiodło się|Liczba|Łącznie|Liczba rejestracji modelu zakończonych powodzeniem w tym obszarze roboczym|Scenariusz|
|Nie można zarejestrować modelu|Nie można zarejestrować modelu|Liczba|Łącznie|Liczba rejestracji modelu zakończonych niepowodzeniem w tym obszarze roboczym|Scenariusz, StatusCode|
|Uruchomiono Wdrażanie modelu|Uruchomiono Wdrażanie modelu|Liczba|Łącznie|Liczba rozpoczętych wdrożeń modelu w tym obszarze roboczym|Scenariusz|
|Wdrażanie modelu powiodło się|Wdrażanie modelu powiodło się|Liczba|Łącznie|Liczba wdrożeń modelu, które zakończyły się pomyślnie w tym obszarze roboczym|Scenariusz|
|Wdrażanie modelu nie powiodło się|Wdrażanie modelu nie powiodło się|Liczba|Łącznie|Liczba wdrożeń modelu, które zakończyły się niepowodzeniem w tym obszarze roboczym|Scenariusz, StatusCode|
|Łączna liczba węzłów|Łączna liczba węzłów|Liczba|Średnia|Łączna liczba węzłów. Ta suma obejmuje niektóre aktywne węzły, bezczynne węzły, węzły niezdatne do użycia, węzły Premepted, pozostawiając węzły|Scenariusz, ClusterName|
|Aktywne węzły|Aktywne węzły|Liczba|Średnia|Liczba węzłów Active. Są to węzły, w których aktywnie uruchomiono zadanie.|Scenariusz, ClusterName|
|Węzły bezczynne|Węzły bezczynne|Liczba|Średnia|Liczba bezczynnych węzłów. Węzły bezczynne są węzłami, w których nie są uruchomione żadne zadania, ale mogą akceptować nowe zadanie, jeśli jest dostępne.|Scenariusz, ClusterName|
|Węzły niezdatne do użytku|Węzły niezdatne do użytku|Liczba|Średnia|Liczba nieużywanych węzłów. Węzły, które nie są użyteczne, nie działają z powodu problemu z nierozpoznawalne. Platforma Azure przeprowadzi odtwarzanie tych węzłów.|Scenariusz, ClusterName|
|Zastępujące węzły|Zastępujące węzły|Liczba|Średnia|Liczba przeniesiona węzłów. Te węzły są węzłami o niskim priorytecie, które znajdują się poza dostępną pulą węzłów.|Scenariusz, ClusterName|
|Opuszczanie węzłów|Opuszczanie węzłów|Liczba|Średnia|Liczba pozostałych węzłów. Opuszczenie węzłów to węzły, które po prostu zakończyły przetwarzanie zadania i przechodzą w stan bezczynności.|Scenariusz, ClusterName|
|Łączna liczba rdzeni|Łączna liczba rdzeni|Liczba|Średnia|Łączna liczba rdzeni|Scenariusz, ClusterName|
|Aktywne rdzenie|Aktywne rdzenie|Liczba|Średnia|Liczba aktywnych rdzeni|Scenariusz, ClusterName|
|Rdzenie bezczynne|Rdzenie bezczynne|Liczba|Średnia|Liczba rdzeni bezczynnych|Scenariusz, ClusterName|
|Rdzenie, których nie można używać|Rdzenie, których nie można używać|Liczba|Średnia|Liczba rdzeni, których nie można używać|Scenariusz, ClusterName|
|Występujące rdzenie|Występujące rdzenie|Liczba|Średnia|Liczba przeniesiona rdzeni|Scenariusz, ClusterName|
|Opuszczanie rdzeni|Opuszczanie rdzeni|Liczba|Średnia|Liczba rdzeni wychodzących|Scenariusz, ClusterName|
|Procent wykorzystania przydziałów|Procent wykorzystania przydziałów|Liczba|Średnia|Procent wykorzystania przydziałów|Scenariusz, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Liczba|Średnia|Procesor CPU (wersja zapoznawcza)|Scenariusz, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Liczba|Średnia|Procesor GPU (wersja zapoznawcza)|Scenariusz, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Sposób użycia|Sposób użycia|Liczba|Liczba|Liczba wywołań interfejsu API|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Dostępność interfejsów API|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/MediaServices/streamingEndpoints

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach.|OutputFormat|
|SuccessE2ELatency|Pomyślne zakończenie oczekiwania|)|Średnia|Średnie opóźnienie dla pomyślnych żądań w milisekundach.|OutputFormat|
|Żądania|Żądania|Liczba|Łącznie|Żądania do punktu końcowego przesyłania strumieniowego.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/MediaServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AssetQuota|Przydział zasobów|Liczba|Średnia|Ile zasobów jest dozwolonych dla bieżącego konta usługi multimediów|Brak|
|AssetCount|Liczba zasobów|Liczba|Średnia|Ile zasobów zostało już utworzonych na bieżącym koncie usługi multimediów|Brak|
|AssetQuotaUsedPercentage|Procent użycia przydziału zasobów|Wartość procentowa|Średnia|Procent użycia zasobów w bieżącym koncie usługi multimediów|Brak|
|ContentKeyPolicyQuota|Przydział zasad dotyczących kluczy zawartości|Liczba|Średnia|Ile zasad kluczy zawartości jest dozwolonych dla bieżącego konta usługi multimediów|Brak|
|ContentKeyPolicyCount|Liczba zasad dotyczących kluczy zawartości|Liczba|Średnia|Ile zasad kluczy zawartości zostało już utworzonych na bieżącym koncie usługi multimediów|Brak|
|ContentKeyPolicyQuotaUsedPercentage|Procent użycia przydziału zasad klucza zawartości|Wartość procentowa|Średnia|Procent użycia zasad klucza zawartości w bieżącym koncie usługi multimediów|Brak|
|StreamingPolicyQuota|Limit przydziału zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego jest dozwolonych dla bieżącego konta usługi multimediów|Brak|
|StreamingPolicyCount|Liczba zasad przesyłania strumieniowego|Liczba|Średnia|Ile zasad przesyłania strumieniowego zostało już utworzonych na bieżącym koncie usługi multimediów|Brak|
|StreamingPolicyQuotaUsedPercentage|Procent użycia limitu przydziału zasad przesyłania strumieniowego|Wartość procentowa|Średnia|Procent użycia zasad przesyłania strumieniowego w bieżącym koncie usługi multimediów|Brak|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AssetsConverted|Przekonwertowane zasoby|Liczba|Łącznie|Łączna liczba przekonwertowanych elementów zawartości|Identyfikator AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Aktywne sesje renderowania|Liczba|Łącznie|Łączna liczba aktywnych sesji renderowania|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/woluminy

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageReadLatency|Średnie opóźnienie odczytu|)|Średnia|Średnie opóźnienie odczytu w milisekundach na operację|Brak|
|AverageWriteLatency|Średnie opóźnienie zapisu|)|Średnia|Średnie opóźnienie zapisu w milisekundach na operację|Brak|
|VolumeLogicalSize|Rozmiar zużytego woluminu|Bajty|Średnia|Rozmiar logiczny woluminu (zużyte bajty)|Brak|
|VolumeSnapshotSize|Rozmiar migawki woluminu|Bajty|Średnia|Rozmiar wszystkich migawek w woluminie|Brak|
|ReadIops|Odczyt operacji we/wy|CountPerSecond|Średnia|Operacje odczytu/out na sekundę|Brak|
|WriteIops|Zapisz operacje we/wy|CountPerSecond|Średnia|Operacje zapisu/wylogowania na sekundę|Brak|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pula przydzielono do rozmiaru woluminu|Bajty|Średnia|Przydzielono używany rozmiar puli|Brak|
|VolumePoolTotalLogicalSize|Rozmiar zużytego puli|Bajty|Średnia|Suma rozmiaru logicznego wszystkich woluminów należących do puli|Brak|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate|Bajty wysłane|Bajty|Łącznie|Liczba bajtów wysłanych przez interfejs sieciowy|Brak|
|BytesReceivedRate|Bajty odebrane|Bajty|Łącznie|Liczba bajtów odebranych przez interfejs sieciowy|Brak|
|PacketsSentRate|Wysłane pakiety|Liczba|Łącznie|Liczba pakietów wysłanych przez interfejs sieciowy|Brak|
|PacketsReceivedRate|Odebrane pakiety|Liczba|Łącznie|Liczba pakietów odebranych przez interfejs sieciowy|Brak|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/loadBalancers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|VipAvailability|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność ścieżki danych Load Balancer na czas trwania|FrontendIPAddress,FrontendPort|
|DipAvailability|Stan sondy kondycji|Liczba|Średnia|Średni stan sondy kondycji Load Balancer na czas trwania|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Liczba bajtów|Liczba|Łącznie|Łączna liczba bajtów przesłanych w okresie|FrontendIPAddress, FrontendPort, kierunek|
|PacketCount|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SYNCount|Liczba SYN|Liczba|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|FrontendIPAddress, FrontendPort, kierunek|
|SnatConnectionCount|Liczba połączeń z translatorem adresów sieciowych|Liczba|Łącznie|Łączna liczba nowych połączeń z przyłączaniem do adresów w czasie|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Przydzielono porty przydziałów (wersja zapoznawcza)|Liczba|Łącznie|Łączna liczba portów przydzieloną porty adresów sieciowych w okresie|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Używane porty (wersja zapoznawcza)|Liczba|Łącznie|Łączna liczba portów przydziałów adresów sieciowych używanych w czasie|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Wolumin zapytania|Liczba|Łącznie|Liczba zapytań obsłużonych dla strefy DNS|Brak|
|RecordSetCount|Liczba zestawów rekordów|Liczba|Maksimum|Liczba zestawów rekordów w strefie DNS|Brak|
|RecordSetCapacityUtilization|Wykorzystanie pojemności zestawu rekordów|Wartość procentowa|Maksimum|Procent pojemności zestawu rekordów używany przez strefę DNS|Brak|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/adresów publicipaddress

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PacketsInDDoS|Pakiety przychodzące DDoS|CountPerSecond|Maksimum|Pakiety przychodzące DDoS|Brak|
|PacketsDroppedDDoS|Odrzucone pakiety przychodzące DDoS|CountPerSecond|Maksimum|Odrzucone pakiety przychodzące DDoS|Brak|
|PacketsForwardedDDoS|Przekazane pakiety przychodzące DDoS|CountPerSecond|Maksimum|Przekazane pakiety przychodzące DDoS|Brak|
|TCPPacketsInDDoS|Przychodzące pakiety TCP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP DDoS|Brak|
|TCPPacketsDroppedDDoS|Liczba porzuconych pakietów TCP przychodzących DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów TCP przychodzących DDoS|Brak|
|TCPPacketsForwardedDDoS|Przychodzące pakiety TCP przesłane dalej DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP przesłane dalej DDoS|Brak|
|UDPPacketsInDDoS|Przychodzące pakiety UDP DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS|Brak|
|UDPPacketsDroppedDDoS|Liczba porzuconych pakietów przychodzących UDP DDoS|CountPerSecond|Maksimum|Liczba porzuconych pakietów przychodzących UDP DDoS|Brak|
|UDPPacketsForwardedDDoS|Przychodzące pakiety UDP DDoS przesłane dalej|CountPerSecond|Maksimum|Przychodzące pakiety UDP DDoS przesłane dalej|Brak|
|BytesInDDoS|Bajty przychodzące DDoS|BytesPerSecond|Maksimum|Bajty przychodzące DDoS|Brak|
|BytesDroppedDDoS|Bajty przychodzące opuszczone DDoS|BytesPerSecond|Maksimum|Bajty przychodzące opuszczone DDoS|Brak|
|BytesForwardedDDoS|Przekazane bajty przychodzące DDoS|BytesPerSecond|Maksimum|Przekazane bajty przychodzące DDoS|Brak|
|TCPBytesInDDoS|Przychodzące bajty TCP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty TCP DDoS|Brak|
|TCPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących protokołu TCP DDoS|Brak|
|TCPBytesForwardedDDoS|Przekazane DDoS przychodzące bajty TCP|BytesPerSecond|Maksimum|Przekazane DDoS przychodzące bajty TCP|Brak|
|UDPBytesInDDoS|Przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przychodzące bajty UDP DDoS|Brak|
|UDPBytesDroppedDDoS|Liczba porzuconych bajtów przychodzących UDP DDoS|BytesPerSecond|Maksimum|Liczba porzuconych bajtów przychodzących UDP DDoS|Brak|
|UDPBytesForwardedDDoS|Przekazane przychodzące bajty UDP DDoS|BytesPerSecond|Maksimum|Przekazane przychodzące bajty UDP DDoS|Brak|
|IfUnderDDoSAttack|W obszarze atak DDoS|Liczba|Maksimum|W obszarze atak DDoS|Brak|
|DDoSTriggerTCPPackets|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS|Brak|
|DDoSTriggerUDPPackets|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|CountPerSecond|Maksimum|Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS|Brak|
|DDoSTriggerSYNPackets|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|CountPerSecond|Maksimum|Pakiety przychodzących SYN wyzwalające łagodzenie DDoS|Brak|
|VipAvailability|Dostępność ścieżki danych|Liczba|Średnia|Średnia dostępność adresów IP na czas trwania|Port|
|ByteCount|Liczba bajtów|Liczba|Łącznie|Łączna liczba bajtów przesłanych w okresie|Port, kierunek|
|PacketCount|Liczba pakietów|Liczba|Łącznie|Łączna liczba pakietów wysłanych w czasie|Port, kierunek|
|SynCount|Liczba SYN|Liczba|Łącznie|Łączna liczba pakietów SYN wysłanych w czasie|Port, kierunek|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Czas błądzenia dla poleceń ping dla maszyny wirtualnej|)|Średnia|Czas błądzenia dla poleceń ping wysyłanych do docelowej maszyny wirtualnej|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Nieudane polecenia ping do maszyny wirtualnej|Wartość procentowa|Średnia|Procent liczby zakończonych niepowodzeniem poleceń ping do całkowitego wysłania poleceń ping dla docelowej maszyny wirtualnej|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit|Liczba trafień reguł aplikacji|Liczba|Łącznie|Liczba trafień reguł aplikacji|Stan, powód, protokół|
|NetworkRuleHit|Liczba trafień reguł sieci|Liczba|Łącznie|Liczba trafień reguł sieci|Stan, powód, protokół|
|FirewallHealth|Stan kondycji zapory|Wartość procentowa|Średnia|Stan kondycji zapory|Stan, Przyczyna|
|Przetwarzanie dataprocessed|Przetworzone dane|Bajty|Łącznie|Łączna ilość danych przetworzonych przez zaporę|Brak|
|SNATPortUtilization|Wykorzystanie portów przez przytranslatora adresów sieciowych|Wartość procentowa|Średnia|Wykorzystanie portów przez przytranslatora adresów sieciowych|Brak|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|BytesPerSecond|Średnia|Liczba bajtów na sekundę obsłużonych przez Application Gateway|Brak|
|UnhealthyHostCount|Liczba hostów w złej kondycji|Liczba|Średnia|Liczba hostów zaplecza w złej kondycji|BackendSettingsPool|
|HealthyHostCount|Liczba hostów w dobrej kondycji|Liczba|Średnia|Liczba hostów zaplecza w dobrej kondycji|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Liczba|Łącznie|Liczba pomyślnych żądań obsłużonych przez Application Gateway|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Liczba żądań na minutę na hosta w dobrej kondycji|Liczba|Średnia|Średnia liczba żądań na minutę na hosta zaplecza w dobrej kondycji w puli|BackendSettingsPool|
|FailedRequests|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Liczba żądań zakończonych niepowodzeniem, które zostały obsłużone przez Application Gateway|BackendSettingsPool|
|ResponseStatus|Stan odpowiedzi|Liczba|Łącznie|Stan odpowiedzi HTTP zwrócony przez Application Gateway|HttpStatusGroup|
|Wartości CurrentConnections|Bieżące połączenia|Liczba|Łącznie|Liczba bieżących połączeń ustanowionych z Application Gateway|Brak|
|NewConnectionsPerSecond|Nowe połączenia na sekundę|CountPerSecond|Średnia|Nowe połączenia na sekundę ustanowione przy użyciu Application Gateway|Brak|
|CpuUtilization|Wykorzystanie procesora|Wartość procentowa|Średnia|Bieżące wykorzystanie procesora CPU Application Gateway|Brak|
|CapacityUnits|Bieżące jednostki wydajności|Liczba|Średnia|Zużyte jednostki wydajności|Brak|
|FixedBillableCapacityUnits|Jednostki stałej wydajności rozliczanej|Liczba|Średnia|Minimalna jednostka pojemności, która zostanie obciążona|Brak|
|EstimatedBilledCapacityUnits|Szacowane jednostki pojemności|Liczba|Średnia|Szacowane jednostki wydajności, które będą obciążane|Brak|
|ComputeUnits|Bieżące jednostki obliczeniowe|Liczba|Średnia|Wykorzystane jednostki obliczeniowe|Brak|
|BackendResponseStatus|Stan odpowiedzi zaplecza|Liczba|Łącznie|Liczba kodów odpowiedzi HTTP wygenerowanych przez składowe zaplecza. Nie obejmuje to żadnych kodów odpowiedzi wygenerowanych przez Application Gateway.|BackendServer, ustawień httpsettings elementu, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protokół TLS klienta|Liczba|Łącznie|Liczba żądań TLS i innych niż TLS zainicjowanych przez klienta, które ustanowiły połączenie z Application Gateway. Aby wyświetlić dystrybucję protokołu TLS, należy filtrować według protokołu TLS wymiaru.|Odbiornik, TlsProtocol|
|BytesSent|Bajty wysłane|Bajty|Łącznie|Całkowita liczba bajtów wysłanych przez Application Gateway do klientów|Odbiornik|
|BytesReceived|Bajty odebrane|Bajty|Łącznie|Całkowita liczba bajtów odebranych przez Application Gateway od klientów|Odbiornik|
|ClientRtt|Czas RTT klienta|)|Średnia|Średni czas błądzenia między klientami a Application Gateway. Ta Metryka wskazuje, jak długo trwa ustanawianie połączeń i zwracanie potwierdzeń|Odbiornik|
|ApplicationGatewayTotalTime|Łączny czas Application Gateway|)|Średnia|Średni czas przetwarzania żądania i jego odpowiedź do wysłania. Ta wartość jest obliczana jako średnia interwału od momentu, kiedy Application Gateway otrzymuje pierwszy bajt żądania HTTP do momentu zakończenia operacji wysyłania odpowiedzi. Należy pamiętać, że zwykle obejmuje to czas przetwarzania Application Gateway, czas, przez który pakiety żądań i odpowiedzi są przesyłane przez sieć i czas odpowiedzi serwera wewnętrznej bazy danych.|Odbiornik|
|BackendConnectTime|Czas połączenia z zapleczem|)|Średnia|Czas nawiązywania połączenia z serwerem wewnętrznej bazy danych|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|BackendFirstByteResponseTime|Czas odpowiedzi na pierwszy bajt zaplecza|)|Średnia|Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania pierwszego bajtu nagłówka odpowiedzi, który zbliża czas przetwarzania serwera wewnętrznej bazy danych|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|BackendLastByteResponseTime|Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych|)|Średnia|Interwał czasu między rozpoczęciem ustanawiania połączenia z serwerem zaplecza i otrzymywania ostatniego bajtu treści odpowiedzi|Listener, BackendServer, ustawień httpsettings elementu, BackendHttpSetting|
|MatchedCount|Dystrybucja reguł dla zapory aplikacji sieci Web v1|Liczba|Łącznie|Dystrybucja reguły dla ruchu przychodzącego w zaporze aplikacji sieci Web v1|Rule, RuleId|
|BlockedCount|Dystrybucja reguł żądań zablokowanych przez zaporę aplikacji sieci Web 1|Liczba|Łącznie|Dystrybucja reguł żądań zablokowanych przez zaporę aplikacji sieci Web 1|Rule, RuleId|
|BlockedReqCount|Liczba żądań blokowania dla zapory aplikacji sieci Web v1|Liczba|Łącznie|Liczba żądań blokowania dla zapory aplikacji sieci Web v1|Brak|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageBandwidth|Przepustowość S2S bramy|BytesPerSecond|Średnia|Średnia przepustowość lokacja-lokacja bramy w bajtach na sekundę|Brak|
|P2SBandwidth|Przepustowość P2S bramy|BytesPerSecond|Średnia|Średnia przepustowość typu punkt-lokacja bramy w bajtach na sekundę|Brak|
|P2SConnectionCount|Liczba połączeń P2S|Liczba|Maksimum|Liczba połączeń punkt-lokacja bramy|Protocol (Protokół)|
|TunnelAverageBandwidth|Przepustowość tunelu|BytesPerSecond|Średnia|Średnia przepustowość tunelu w bajtach na sekundę|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bajty wychodzące tunelu|Bajty|Łącznie|Wychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bajty transferu danych wejściowych tunelu|Bajty|Łącznie|Przychodzące bajty tunelu|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pakiety wychodzące tuneli|Liczba|Łącznie|Liczba wychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pakiety przychodzące tunelu|Liczba|Łącznie|Liczba przychodzących pakietów tunelu|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Porzucanie pakietów wychodzących przez tunelowanie|Liczba|Łącznie|Liczba porzucenia pakietów wychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Wyrzucanie niezgodności pakietów przez tunelowanie|Liczba|Łącznie|Liczba porzucenia pakietów przychodzących z niezgodności selektora ruchu tunelu|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Liczba|Średnia|Poziom oświetlenia odbierania w dBm|Link, tor|
|TxLightLevel|TxLightLevel|Liczba|Średnia|Poziom oświetlenia TX w dBm|Link, tor|
|AdminState|AdminState|Liczba|Średnia|Stan administratora portu|Łącze|
|LineProtocol|LineProtocol|Liczba|Średnia|Stan protokołu wiersza portu|Łącze|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|Łącze|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|Łącze|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|PeeredCircuitSKey|
|BgpAvailability|Dostępność protokołu BGP|Wartość procentowa|Średnia|Dostępność protokołu BGP od MSEE do wszystkich elementów równorzędnych.|PeeringType, element równorzędny|
|ArpAvailability|Dostępność protokołu ARP|Wartość procentowa|Średnia|Dostępność protokołu ARP od MSEE do wszystkich elementów równorzędnych.|PeeringType, element równorzędny|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Średnia|Liczba porzuconych bitów danych przychodzących na sekundę|Brak|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Średnia|Liczba porzuconych bitów danych wyjściowych na sekundę|Brak|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|Brak|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|Brak|

## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|Brak|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|Brak|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Usługa BITS na sekundę na platformie Azure|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Ruch przychodzący z platformy Azure na sekundę|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QpsByEndpoint|Zwrócone zapytania według punktu końcowego|Liczba|Łącznie|Liczba zwróconych punktów końcowych Traffic Manager w danym przedziale czasu|Nazwapunktukoncowego|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Stan punktu końcowego według punktu końcowego|Liczba|Maksimum|1 Jeśli sonda punktu końcowego ma stan "włączone", 0 w przeciwnym razie.|Nazwapunktukoncowego|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sond nie powiodło się|Wartość procentowa|Średnia|% sond monitorowania łączności nie powiodło się|Brak|
|AverageRoundtripMs|Średni czas błądzenia (MS)|)|Średnia|Średni czas błądzenia sieci (MS) dla sond monitorowania łączności przesyłanych między źródłem a miejscem docelowym|Brak|
|ChecksFailedPercent|Sprawdza procent niepowodzenia (wersja zapoznawcza)|Wartość procentowa|Średnia|% testów monitorowania łączności nie powiodło się|SourceAddress, SourceName, identyfikator sourceresourceid, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Czas błądzenia (MS) (wersja zapoznawcza)|)|Średnia|Czas błądzenia w milisekundach dla kontroli monitorowania łączności|SourceAddress, SourceName, identyfikator sourceresourceid, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/usługi frontdoor

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|RequestCount|Liczba żądań|Liczba|Łącznie|Liczba żądań klientów obsłużonych przez serwer proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Rozmiar żądania|Bajty|Łącznie|Liczba bajtów wysłanych jako żądania od klientów do serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Rozmiar odpowiedzi|Bajty|Łącznie|Liczba bajtów wysłanych jako odpowiedzi z serwera proxy HTTP/S do klientów|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Rozmiar odpowiedzi do rozliczenia|Bajty|Łącznie|Liczba bajtów rozliczanych (minimalna 2KB na żądanie) wysyłanych jako odpowiedzi z serwera proxy HTTP/S do klientów.|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Liczba żądań wewnętrznej bazy danych|Liczba|Łącznie|Liczba żądań wysyłanych z serwera proxy HTTP/S do frontonu|Wartości httpStatus, HttpStatusGroup, zaplecze|
|BackendRequestLatency|Opóźnienie żądania wewnętrznej bazy danych|)|Średnia|Czas obliczony od momentu wysłania żądania przez serwer proxy HTTP/S do zaplecza do momentu odebrania przez serwer proxy HTTP/S ostatniego bajtu odpowiedzi z zaplecza|Zaplecze|
|TotalLatency|Łączne opóźnienie|)|Średnia|Czas obliczony od momentu odebrania żądania klienta przez serwer proxy HTTP/S do momentu potwierdzenia przez klienta ostatniego bajtu odpowiedzi z serwera proxy HTTP/S|Wartości httpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Procent kondycji zaplecza|Wartość procentowa|Średnia|Procent pomyślnych sond kondycji z serwera proxy HTTP/S do frontonu|Zaplecze, ustawień httpsettings elementu|
|WebApplicationFirewallRequestCount|Liczba żądań zapory aplikacji sieci Web|Liczba|Łącznie|Liczba żądań klientów przetworzonych przez zaporę aplikacji sieci Web|PolicyName, RuleName, Akcja|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Wolumin zapytania|Liczba|Łącznie|Liczba zapytań obsłużonych dla strefy Prywatna strefa DNS|Brak|
|RecordSetCount|Liczba zestawów rekordów|Liczba|Maksimum|Liczba zestawów rekordów w strefie Prywatna strefa DNS|Brak|
|RecordSetCapacityUtilization|Wykorzystanie pojemności zestawu rekordów|Wartość procentowa|Maksimum|Procent pojemności zestawu rekordów używany przez strefę Prywatna strefa DNS|Brak|
|VirtualNetworkLinkCount|Liczba linków Virtual Network|Liczba|Maksimum|Liczba sieci wirtualnych połączonych ze strefą Prywatna strefa DNS|Brak|
|VirtualNetworkLinkCapacityUtilization|Wykorzystanie pojemności Virtual Network linku|Wartość procentowa|Maksimum|Procent pojemności Virtual Network łącz wykorzystany przez strefę Prywatna strefa DNS|Brak|
|VirtualNetworkWithRegistrationLinkCount|Liczba linków rejestracji Virtual Network|Liczba|Maksimum|Liczba sieci wirtualnych połączonych ze strefą Prywatna strefa DNS z włączoną funkcją autorejestracji|Brak|
|VirtualNetworkWithRegistrationCapacityUtilization|Wykorzystanie pojemności łącza rejestracji Virtual Network|Wartość procentowa|Maksimum|Procent linku Virtual Network z pojemnością autorejestracji używaną przez strefę Prywatna strefa DNS|Brak|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Rejestracja. wszystkie|Operacje rejestracji|Liczba|Łącznie|Liczba wszystkich operacji rejestracji zakończonych powodzeniem (Tworzenie aktualizacji zapytań i usunięć). |Brak|
|Rejestracja. Tworzenie|Operacje tworzenia rejestracji|Liczba|Łącznie|Liczba wszystkich operacji tworzenia rejestracji zakończonych powodzeniem.|Brak|
|Rejestracja. Aktualizacja|Operacje aktualizacji rejestracji|Liczba|Łącznie|Liczba wszystkich aktualizacji rejestracji zakończonych powodzeniem.|Brak|
|Rejestracja. Get|Operacje odczytu rejestracji|Liczba|Łącznie|Liczba wszystkich zapytań rejestracji zakończonych powodzeniem.|Brak|
|Rejestracja. Usuwanie|Operacje usuwania rejestracji|Liczba|Łącznie|Liczba wszystkich operacji usunięcia rejestracji zakończonych powodzeniem.|Brak|
|przychodzące|Komunikaty przychodzące|Liczba|Łącznie|Liczba wszystkich pomyślnie wysłanych wywołań interfejsu API. |Brak|
|przychodzące. zaplanowane|Przesłane zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowano zaplanowane powiadomienia wypychane|Brak|
|przychodzące. zaplanowane. Cancel|Anulowano zaplanowane powiadomienia wypychane|Liczba|Łącznie|Anulowano zaplanowane powiadomienia wypychane|Brak|
|zaplanowane. oczekujące|Oczekujące zaplanowane powiadomienia|Liczba|Łącznie|Oczekujące zaplanowane powiadomienia|Brak|
|Instalacja. wszystkie|Operacje zarządzania instalacją|Liczba|Łącznie|Operacje zarządzania instalacją|Brak|
|Instalacja. Get|Pobierz operacje instalacji|Liczba|Łącznie|Pobierz operacje instalacji|Brak|
|Instalacja. upsert|Tworzenie lub aktualizowanie operacji instalacji|Liczba|Łącznie|Tworzenie lub aktualizowanie operacji instalacji|Brak|
|Instalacja. poprawka|Operacje instalacji poprawek|Liczba|Łącznie|Operacje instalacji poprawek|Brak|
|Instalacja. Delete|Usuwanie operacji instalacji|Liczba|Łącznie|Usuwanie operacji instalacji|Brak|
|wychodzący. allpns. Success|Powiadomienia zakończone powodzeniem|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak|
|wychodzący. allpns. invalidpayload|Błędy ładunku|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ PNS zwrócił zły błąd ładunku.|Brak|
|wychodzący. allpns. pnserror|Błędy zewnętrznego systemu powiadomień|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ wystąpił problem z komunikacją z PNS (wyklucza problemy z uwierzytelnianiem).|Brak|
|wychodzący. allpns. channelerror|Błędy kanałów|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ kanał był nieprawidłowy, nie jest skojarzony z poprawną aplikacją lub wygasłą.|Brak|
|wychodzący. allpns. badorexpiredchannel|Błędy nieprawidłowych lub wygasłych kanałów|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ kanał/token/Identyfikator rejestracji w rejestracji został wygasły lub nieprawidłowy.|Brak|
|wychodzący. WNS. Success|WNS pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak|
|wychodzący. WNS. invalidcredentials|WNS błędy autoryzacji (Nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane. (Usługa Windows Live nie rozpoznaje poświadczeń).|Brak|
|wychodzący. WNS. badchannel|Błąd nieprawidłowego kanału WNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (WNS status: 404).|Brak|
|wychodzący. WNS. expiredchannel|Błąd WNS kanału wygasłego|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia identyfikator channeluri (stan WNS: 410).|Brak|
|wychodzące. WNS. dławienia|WNS powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ WNS ogranicza tę aplikację (stan WNS: 406 nie akceptowalny).|Brak|
|wychodzący. WNS. tokenproviderunreachable|Błędy autoryzacji WNS (nieosiągalne)|Liczba|Łącznie|Usługa Windows Live jest nieosiągalna.|Brak|
|wychodzący. WNS. invalidtoken|Błędy autoryzacji WNS (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do WNS jest nieprawidłowy (stan WNS: 401 Brak autoryzacji).|Brak|
|wychodzący. WNS. wrongtoken|WNS błędy autoryzacji (nieprawidłowy token)|Liczba|Łącznie|Token dostarczony do WNS jest prawidłowy, ale dla innej aplikacji (stan WNS: 403 zabroniony). Może się tak zdarzyć, jeśli identyfikator channeluri w rejestracji jest skojarzony z inną aplikacją. Sprawdź, czy aplikacja kliencka jest skojarzona z tą samą aplikacją, której poświadczenia znajdują się w centrum powiadomień.|Brak|
|wychodzący. WNS. invalidnotificationformat|Nieprawidłowy format powiadomienia WNS|Liczba|Łącznie|Format powiadomienia jest nieprawidłowy (stan WNS: 400). Należy pamiętać, że WNS nie odrzuca wszystkich nieprawidłowych ładunków.|Brak|
|wychodzący. WNS. invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia WNS|Liczba|Łącznie|Ładunek powiadomienia jest zbyt duży (stan WNS: 413).|Brak|
|wychodzący. WNS. channelthrottled|Ograniczenie kanału WNS|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (nagłówek odpowiedzi WNS: X-WNS-NotificationStatus: channelThrottled).|Brak|
|wychodzący. WNS. channeldisconnected|Kanał WNS został odłączony|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (WNS w nagłówku odpowiedzi: X-WNS-DeviceConnectionStatus: disconnected).|Brak|
|wychodzące. WNS. upuszczone|WNS usunięte powiadomienia|Liczba|Łącznie|Powiadomienie zostało porzucone, ponieważ identyfikator channeluri w rejestracji jest ograniczany (X-WNS-NotificationStatus: porzucone, ale nie X-WNS-DeviceConnectionStatus: disconnected).|Brak|
|wychodzący. WNS. pnserror|Błędy WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z WNS.|Brak|
|wychodzący. WNS. authenticationerror|Błędy uwierzytelniania WNS|Liczba|Łącznie|Powiadomienie nie zostało dostarczone z powodu błędów komunikacji z nieprawidłowymi poświadczeniami usługi Windows Live lub nieprawidłowym tokenem.|Brak|
|wychodzące. APN. sukces|Pomyślne powiadomienia usługi APNS|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak|
|wychodzące. APNs. invalidcredentials|Błędy autoryzacji usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak|
|wychodzące. APNs. badchannel|Błąd nieprawidłowego kanału usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ token jest nieprawidłowy (kod stanu usługi APNS: 8).|Brak|
|wychodzące. APNs. expiredchannel|Błąd wygasłego kanału usługi APNS|Liczba|Łącznie|Liczba tokenów unieważnionych przez kanał opinii usługi APNS.|Brak|
|wychodzące. APNs. invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (kod stanu usługi APNS: 7).|Brak|
|wychodzące. APNs. pnserror|Błędy usługi APNS|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem z powodu błędów komunikacji z usługą APNS.|Brak|
|wychodzący. GCM. Success|GCM pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak|
|wychodzący. GCM. invalidcredentials|GCM błędy autoryzacji (Nieprawidłowe poświadczenia)|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak|
|wychodzący. GCM. badchannel|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie został rozpoznany (wynik GCM: nieprawidłowa rejestracja).|Brak|
|wychodzący. GCM. expiredchannel|Błąd GCM kanału wygasłego|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu wygaśnięcia Identyfikator rejestracji w rejestracji (wynik GCM: NotRegistered).|Brak|
|wychodzące. GCM. dławienia|GCM powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ GCM ograniczenie tej aplikacji (kod stanu GCM: 501-599 lub wynik: niedostępne).|Brak|
|wychodzący. GCM. invalidnotificationformat|Nieprawidłowy format powiadomienia GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek nie został poprawnie sformatowany (wynik GCM: InvalidDataKey lub InvalidTtl).|Brak|
|wychodzący. GCM. invalidnotificationsize|Błąd nieprawidłowego rozmiaru powiadomienia GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ ładunek jest zbyt duży (wynik GCM: MessageTooBig).|Brak|
|wychodzący. GCM. wrongchannel|Błąd nieprawidłowego kanału GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator rejestracji w rejestracji nie jest skojarzony z bieżącą aplikacją (GCM Result: InvalidPackageName).|Brak|
|wychodzący. GCM. pnserror|Błędy GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z GCM.|Brak|
|wychodzący. GCM. authenticationerror|Błędy uwierzytelniania GCM|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń, a SenderId nie jest poprawnie skonfigurowana w aplikacji (wynik GCM: MismatchedSenderId).|Brak|
|wychodzący. usługi MPNS. Success|USŁUGI MPNS pomyślne powiadomienia|Liczba|Łącznie|Liczba wszystkich udanych powiadomień.|Brak|
|wychodzący. usługi MPNS. invalidcredentials|USŁUGI MPNS nieprawidłowe poświadczenia|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak|
|wychodzący. usługi MPNS. badchannel|Błąd nieprawidłowego kanału usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji nie zostały rozpoznane (usługi MPNS status: 404).|Brak|
|wychodzące. usługi MPNS. dławienia|USŁUGI MPNS powiadomienia z ograniczeniami|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ usługi MPNS ogranicza tę aplikację (WNS usługi MPNS: 406 nieakceptowalny).|Brak|
|wychodzący. usługi MPNS. invalidnotificationformat|Nieprawidłowy format powiadomienia usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów zakończonych niepowodzeniem, ponieważ ładunek powiadomienia był zbyt duży.|Brak|
|wychodzący. usługi MPNS. channeldisconnected|Kanał usługi MPNS został odłączony|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ identyfikator channeluri w rejestracji został rozłączony (stan usługi MPNS: 412 nie został znaleziony).|Brak|
|wychodzące. usługi MPNS. upuszczone|USŁUGI MPNS usunięte powiadomienia|Liczba|Łącznie|Liczba wypchnięciów, które zostały porzucone przez usługi MPNS (nagłówek odpowiedzi usługi MPNS: X-NotificationStatus: QueueFull lub pomijane).|Brak|
|wychodzący. usługi MPNS. pnserror|Błędy usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem z powodu błędów komunikacji z usługi MPNS.|Brak|
|wychodzący. usługi MPNS. authenticationerror|Błędy uwierzytelniania usługi MPNS|Liczba|Łącznie|Liczba wypchnięciów, które zakończyły się niepowodzeniem, ponieważ PNS nie zaakceptował podanych poświadczeń lub poświadczenia są zablokowane.|Brak|
|notificationhub. pushs|Wszystkie powiadomienia wychodzące|Liczba|Łącznie|Wszystkie powiadomienia wychodzące centrum powiadomień|Brak|
|przychodzące. ALL. Requests|Wszystkie żądania przychodzące|Liczba|Łącznie|Łączna liczba żądań przychodzących dla centrum powiadomień|Brak|
|przychodzące. ALL. failedrequests|Wszystkie przychodzące żądania zakończone niepowodzeniem|Liczba|Łącznie|Łączna liczba nieudanych żądań przychodzących dla centrum powiadomień|Brak|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/obszary robocze

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_% bezpłatne węzłów i|% Wolnego węzłów i|Liczba|Średnia|Average_% bezpłatne węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wolne miejsce w Average_%|Wolne miejsce (%)|Liczba|Średnia|Wolne miejsce w Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ użyto węzłów i|% Użytych węzłów i|Liczba|Średnia|Average_ użyto węzłów i|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używane miejsce Average_%|Zajęte miejsce (%)|Liczba|Średnia|Używane miejsce Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odczytu Average_Disk/s|Bajty odczytu dysku/s|Liczba|Średnia|Bajty odczytu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Disk/s|Odczyty dysku/s|Liczba|Średnia|Odczyty Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Transfery Average_Disk/s|Transfery dysku/s|Liczba|Średnia|Transfery Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty zapisu Average_Disk/s|Bajty zapisu dysku/s|Liczba|Średnia|Bajty zapisu Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Disk/s|Zapisy dysku/s|Liczba|Średnia|Zapisy Average_Disk/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabajtów|Wolne megabajty|Liczba|Średnia|Average_Free megabajtów|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Logical/s|Bajty dysku logicznego/s|Liczba|Średnia|Bajty dysku Average_Logical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępna pamięć Average_%|Dostępna pamięć (%)|Liczba|Średnia|Dostępna pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Dostępny obszar wymiany Average_%|Dostępny obszar wymiany (%)|Liczba|Średnia|Dostępny obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Użyta pamięć Average_%|Używana pamięć (%)|Liczba|Średnia|Użyta pamięć Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Używany obszar wymiany Average_%|Używany obszar wymiany (%)|Liczba|Średnia|Używany obszar wymiany Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Available MB|Dostępna pamięć (MB)|Liczba|Średnia|Pamięć Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zamiana Average_Available MB|Dostępny obszar wymiany (MB)|Liczba|Średnia|Zamiana Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odczyty Average_Page/s|Odczyty stron/s|Liczba|Średnia|Odczyty Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Zapisy Average_Page/s|Zapisy stron/s|Liczba|Średnia|Zapisy Average_Page/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/s|Stron/s|Liczba|Średnia|Average_Pages/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przestrzeń wymiany Average_Used MB|Używany obszar wymiany (MB)|Liczba|Średnia|Przestrzeń wymiany Average_Used MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used pamięci (MB)|Używana pamięć (MB)|Liczba|Średnia|Pamięć Average_Used pamięci (MB)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Przesyłane Average_Total bajty|Całkowita liczba przesłanych bajtów|Liczba|Średnia|Przesyłane Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty odebrane Average_Total|Całkowita liczba odebranych bajtów|Liczba|Średnia|Bajty odebrane Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total bajty|Łączna liczba bajtów|Liczba|Średnia|Average_Total bajty|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total przesyłane pakiety|Całkowita liczba przesłanych pakietów|Liczba|Średnia|Average_Total przesyłane pakiety|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane pakiety Average_Total|Całkowita liczba odebranych pakietów|Liczba|Średnia|Odebrane pakiety Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total błędy odbierania|Całkowita liczba błędów odbierania|Liczba|Średnia|Average_Total błędy odbierania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Błędy wysyłania Average_Total|Całkowita liczba błędów transmisji|Liczba|Średnia|Błędy wysyłania Average_Total|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kolizje|Łączna liczba kolizji|Liczba|Średnia|Average_Total kolizje|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. wartość operacji dysku na sek./odczyt|Średni czas dysku w s/odczyt|Liczba|Średnia|Average_Avg. wartość operacji dysku na sek./odczyt|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Transfery dysku/s|Średni czas dysku w s/transfer|Liczba|Średnia|Average_Avg. Transfery dysku/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. dysku na sek./zapis|Średni czas dysku w s/zapis|Liczba|Średnia|Average_Avg. dysku na sek./zapis|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Bajty dysku Average_Physical/s|Bajty dysku fizycznego/s|Liczba|Średnia|Bajty dysku Average_Physical/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct uprzywilejowany czas|Czas uprzywilejowany PCT|Liczba|Średnia|Average_Pct uprzywilejowany czas|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_Pct|Czas użytkownika PCT|Liczba|Średnia|Czas użytkownika Average_Pct|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Pamięć Average_Used w kilobajtach|Używana pamięć (w kilobajtach)|Liczba|Średnia|Pamięć Average_Used w kilobajtach|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual pamięci współdzielonej|Wirtualna pamięć udostępniona|Liczba|Średnia|Average_Virtual pamięci współdzielonej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas DPC Average_%|Czas DPC (%)|Liczba|Średnia|Czas DPC Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas bezczynności Average_%|Czas bezczynności (%)|Liczba|Średnia|Czas bezczynności Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas przerwań (%)|Czas przerwań (%)|Liczba|Średnia|Average_ czas przerwań (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas oczekiwania operacji we/wy Average_%|Czas oczekiwania operacji we/wy (%)|Liczba|Średnia|Czas oczekiwania operacji we/wy Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu całkiem|% Całkiem czasu|Liczba|Średnia|Average_% czasu całkiem|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% czasu uprzywilejowanego|Czas uprzywilejowany (%)|Liczba|Średnia|Average_% czasu uprzywilejowanego|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ czas procesora (%)|Czas procesora (%)|Liczba|Średnia|Average_ czas procesora (%)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Czas użytkownika Average_%|Czas użytkownika (%)|Liczba|Średnia|Czas użytkownika Average_%|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięci fizycznej|Wolna pamięć fizyczna|Liczba|Średnia|Average_Free pamięci fizycznej|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free miejsce w plikach stronicowania|Wolne miejsce w plikach stronicowania|Liczba|Średnia|Average_Free miejsce w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free pamięć wirtualną|Wolna pamięć wirtualna|Liczba|Średnia|Average_Free pamięć wirtualną|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Liczba|Średnia|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size przechowywane w plikach stronicowania|Rozmiar zapisany w plikach stronicowania|Liczba|Średnia|Average_Size przechowywane w plikach stronicowania|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Czas|Liczba|Średnia|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Użytkownicy|Liczba|Średnia|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current długość kolejki dysku|Bieżąca długość kolejki dysku|Liczba|Średnia|Average_Current długość kolejki dysku|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Dostępna pamięć (MB)|Liczba|Średnia|Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% zadeklarowanych bajtów w użyciu|% Zadeklarowanych bajtów w użyciu|Liczba|Średnia|Average_% zadeklarowanych bajtów w użyciu|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Odebrane Average_Bytes/s|Bajty odebrane/s|Liczba|Średnia|Odebrane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Wysłane Average_Bytes/s|Bajty wysłane/s|Liczba|Średnia|Wysłane Average_Bytes/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes całkowita/s|Całkowita liczba bajtów/s|Liczba|Średnia|Average_Bytes całkowita/s|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Długość kolejki Average_Processor|Długość kolejki procesora|Liczba|Średnia|Długość kolejki Average_Processor|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Puls|Puls|Liczba|Łącznie|Puls|Komputer, OSType, wersja, SourceComputerId|
|Aktualizowanie|Aktualizowanie|Liczba|Średnia|Aktualizowanie|Komputer, produkt, klasyfikacja, UpdateState, opcjonalne, zatwierdzone|
|Wydarzenie|Wydarzenie|Liczba|Średnia|Wydarzenie|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. Komunikacja równorzędna/peeringServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|PrefixLatency|Opóźnienie prefiksu|)|Średnia|Średnie opóźnienie prefiksu|Prefiksname|

## <a name="microsoftpeeringpeerings"></a>Microsoft. Komunikacja równorzędna/Komunikacja równorzędna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Dostępność sesji v4|Wartość procentowa|Średnia|Dostępność sesji v4|ConnectionId|
|SessionAvailabilityV6|Dostępność sesji w wersji 6|Wartość procentowa|Średnia|Dostępność sesji V6|ConnectionId|
|IngressTrafficRate|Szybkość ruchu przychodzącego|BitsPerSecond|Średnia|Szybkość ruchu przychodzącego w bitach na sekundę|ConnectionId|
|EgressTrafficRate|Szybkość ruchu wychodzącego|BitsPerSecond|Średnia|Szybkość ruchu wychodzącego w bitach na sekundę|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/pojemności

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryDuration|Czas trwania zapytania|)|Średnia|Czas trwania zapytania języka DAX w ostatnim interwale|Brak wymiarów|
|QueryPoolJobQueueLength|Wątki: Długość kolejki zadań puli zapytań|Liczba|Średnia|Liczba zadań w kolejce puli wątków zapytań.|Brak wymiarów|
|qpu_high_utilization_metric|Wysokie wykorzystanie jednostek QPU|Liczba|Łącznie|QPU wysokie użycie w ciągu ostatnich minut, 1 do dużego użycia QPU, w przeciwnym razie 0|Brak wymiarów|
|memory_metric|Memory (Pamięć)|Bajty|Średnia|Memory (pamięć). Zakres 0-3 GB dla a1, 0-5 GB dla a2, 0-10 GB dla a3, 0-25 GB dla A4, 0-50 GB dla A5 i 0-100 GB dla A6|Brak wymiarów|
|memory_thrashing_metric|Przeładowywanie pamięci|Wartość procentowa|Średnia|Średnia pamięć migotanie.|Brak wymiarów|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/konta

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Dystrybucja zasobów według klasyfikacji|Liczba|Łącznie|Wskazuje liczbę zasobów z przypisaną określoną klasyfikacją, tj. są one klasyfikowane przy użyciu tej etykiety.|Klasyfikacja, źródło, identyfikator zasobu|
|AssetDistributionByStorageType|Dystrybucja zasobów według typu magazynu|Liczba|Łącznie|Wskazuje liczbę zasobów z określonym typem magazynu.|StorageType, ResourceId|
|CatalogActiveUsers|Aktywni użytkownicy codziennie|Liczba|Łącznie|Liczba aktywnych użytkowników dziennie|ResourceId|
|CatalogUsage|Dystrybucja użycia według operacji|Liczba|Łącznie|Wskaż liczbę operacji wprowadzanych przez użytkownika do wykazu, tj. dostępu, wyszukiwania, słownika.|Operacja, identyfikator zasobu|
|NumberOfAssetsWithClassifications|Liczba zasobów z co najmniej jedną klasyfikacją|Liczba|Średnia|Wskazuje liczbę zasobów z co najmniej jedną klasyfikacją tagów.|ResourceId|
|ScanCancelled|Anulowano skanowanie|Liczba|Łącznie|Wskazuje liczbę anulowanych skanów.|ResourceId|
|ScanCompleted|Ukończono skanowanie|Liczba|Łącznie|Wskazuje, ile skanów zostało ukończonych pomyślnie.|ResourceId|
|ScanFailed|Skanowanie nie powiodło się|Liczba|Łącznie|Wskazuje liczbę operacji skanowania zakończonych niepowodzeniem.|ResourceId|
|ScanTimeTaken|Czas skanowania|Sekundy|Łącznie|Wskazuje łączny czas (w sekundach) skanowania.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections — sukces|ListenerConnections — sukces|Liczba|Łącznie|Pomyślne ListenerConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|ListenerConnections — błąd clienterror|ListenerConnections — błąd clienterror|Liczba|Łącznie|Błąd clienterror on ListenerConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|ListenerConnections — błąd servererror|ListenerConnections — błąd servererror|Liczba|Łącznie|Błąd servererror on ListenerConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|SenderConnections — sukces|SenderConnections — sukces|Liczba|Łącznie|Pomyślne SenderConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|SenderConnections — błąd clienterror|SenderConnections — błąd clienterror|Liczba|Łącznie|Błąd clienterror on SenderConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|SenderConnections — błąd servererror|SenderConnections — błąd servererror|Liczba|Łącznie|Błąd servererror on SenderConnections dla elementu Microsoft. Relay.|EntityName, klasy OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Liczba|Łącznie|Łącznie ListenerConnections dla elementu Microsoft. Relay.|EntityName|
|SenderConnections — TotalRequests|SenderConnections — TotalRequests|Liczba|Łącznie|Łączna liczba żądań SenderConnections dla elementu Microsoft. Relay.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Liczba|Łącznie|Łącznie połączeń ActiveConnections dla elementu Microsoft. Relay.|EntityName|
|ActiveListeners|ActiveListeners|Liczba|Łącznie|Łącznie ActiveListeners dla elementu Microsoft. Relay.|EntityName|
|BytesTransferred|BytesTransferred|Liczba|Łącznie|Łącznie BytesTransferred dla elementu Microsoft. Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Liczba|Łącznie|Łącznie ListenerDisconnects dla elementu Microsoft. Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Liczba|Łącznie|Łącznie SenderDisconnects dla elementu Microsoft. Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft. Search/searchServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency|Opóźnienie wyszukiwania|Sekundy|Średnia|Średnie opóźnienie wyszukiwania dla usługi wyszukiwania|Brak|
|SearchQueriesPerSecond|Zapytania wyszukiwania na sekundę|CountPerSecond|Średnia|Zapytania wyszukiwania na sekundę dla usługi wyszukiwania|Brak|
|ThrottledSearchQueriesPercentage|Procent zapytań wyszukiwania z ograniczeniami|Wartość procentowa|Średnia|Procent zapytań wyszukiwania, które zostały ograniczone dla usługi wyszukiwania|Brak|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. ServiceBus/przestrzenie nazw

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania successfulrequests|Żądania zakończone powodzeniem|Liczba|Łącznie|Łączna liczba pomyślnych żądań dla przestrzeni nazw|EntityName, klasy OperationResult|
|Błędy servererrors|Błędy serwera.|Liczba|Łącznie|Błędy serwera dla elementu Microsoft. ServiceBus.|EntityName, klasy OperationResult|
|Błędy usererrors|Błędy użytkownika.|Liczba|Łącznie|Błędy użytkowników dla elementu Microsoft. ServiceBus.|EntityName, klasy OperationResult|
|ThrottledRequests|Żądania z ograniczeniami.|Liczba|Łącznie|Żądania ograniczone dla elementu Microsoft. ServiceBus.|EntityName, klasy OperationResult|
|Żądania incomingrequests|Żądania przychodzące|Liczba|Łącznie|Żądania przychodzące dla Microsoft. ServiceBus.|EntityName|
|Komunikaty incomingmessages|Komunikaty przychodzące|Liczba|Łącznie|Komunikaty przychodzące dla elementu Microsoft. ServiceBus.|EntityName|
|Komunikaty outgoingmessages|Komunikaty wychodzące|Liczba|Łącznie|Komunikaty wychodzące dla elementu Microsoft. ServiceBus.|EntityName|
|Połączeń ActiveConnections|Połączeń ActiveConnections|Liczba|Łącznie|Całkowita liczba aktywnych połączeń dla elementu Microsoft. ServiceBus.|Brak|
|Połączenia connectionsopened|Otwarte połączenia.|Liczba|Średnia|Połączenia otwarte dla elementu Microsoft. ServiceBus.|EntityName|
|Połączenia connectionsclosed|Połączenia zamknięte.|Liczba|Średnia|Połączenia zamknięte dla elementu Microsoft. ServiceBus.|EntityName|
|Rozmiar|Rozmiar|Bajty|Średnia|Rozmiar kolejki/tematu w bajtach.|EntityName|
|Komunikaty|Liczba komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba komunikatów w kolejce/temacie.|EntityName|
|ActiveMessages|Liczba aktywnych komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba aktywnych komunikatów w kolejce/temacie.|EntityName|
|DeadletteredMessages|Liczba utraconych wiadomości w kolejce/temacie.|Liczba|Średnia|Liczba utraconych wiadomości w kolejce/temacie.|EntityName|
|ScheduledMessages|Liczba zaplanowanych komunikatów w kolejce/temacie.|Liczba|Średnia|Liczba zaplanowanych komunikatów w kolejce/temacie.|EntityName|
|NamespaceCpuUsage|Procesor CPU|Wartość procentowa|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus.|Replika|
|NamespaceMemoryUsage|Użycie pamięci|Wartość procentowa|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus.|Replika|
|CPUXNS|PROCESOR (przestarzałe)|Wartość procentowa|Maksimum|Metryka użycia procesora przestrzeni nazw w warstwie Premium usługi Service Bus. Ta Metryka to przestarzałe. Użyj zamiast tego metryki procesora (NamespaceCpuUsage).|Replika|
|WSXNS|Użycie pamięci (przestarzałe)|Wartość procentowa|Maksimum|Metryka użycia pamięci przestrzeni nazw Premium usługi Service Bus. Ta Metryka jest przestarzała. Użyj zamiast tego metryki użycie pamięci (NamespaceMemoryUsage).|Replika|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/aplikacje

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Liczba|Średnia|Procesor CPU przydzielony do tego kontenera w rdzeniach Milli|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|AllocatedMemory|AllocatedMemory|Bajty|Średnia|Pamięć przypisana do tego kontenera w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualCpu|ActualCpu|Liczba|Średnia|Rzeczywiste użycie procesora CPU w rdzeniach Milli|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ActualMemory|ActualMemory|Bajty|Średnia|Rzeczywiste użycie pamięci w MB|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|CpuUtilization|CpuUtilization|Wartość procentowa|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|MemoryUtilization|MemoryUtilization|Wartość procentowa|Średnia|Użycie procesora CPU dla tego kontenera jako wartości procentowej AllocatedCpu|ApplicationName, ServiceName, CodePackageName, servicereplicaname|
|ApplicationStatus|ApplicationStatus|Liczba|Średnia|Stan aplikacji siatki Service Fabric|ApplicationName, status|
|Stan servicestatus|Stan servicestatus|Liczba|Średnia|Stan kondycji usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Liczba|Średnia|Stan kondycji repliki usługi w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname|
|ContainerStatus|ContainerStatus|Liczba|Średnia|Stan kontenera w aplikacji Service Fabric siatki|ApplicationName, ServiceName, CodePackageName, servicereplicaname, status|
|RestartCount|RestartCount|Liczba|Średnia|Liczba ponownych uruchomień kontenera w aplikacji Service Fabric siatki|ApplicationName, status, ServiceName, servicereplicaname, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/sygnalizujący

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ConnectionCount|Liczba połączeń|Liczba|Maksimum|Ilość połączenia użytkownika.|Endpoint|
|MessageCount|Liczba komunikatów|Liczba|Łącznie|Całkowita ilość komunikatów.|Brak|
|InboundTraffic|Ruch przychodzący|Bajty|Łącznie|Ruch przychodzący usługi|Brak|
|OutboundTraffic|Ruch wychodzący|Bajty|Łącznie|Ruch wychodzący usługi|Brak|
|Błędy usererrors|Błędy użytkownika|Wartość procentowa|Maksimum|Procent błędów użytkownika|Brak|
|SystemErrors|Błędy systemu|Wartość procentowa|Maksimum|Procent błędów systemu|Brak|



## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/serwery/bazy danych

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Wartość procentowa|Średnia|Procent użycia operacji we/wy na danych|Brak|
|log_write_percent|Procent operacji we/wy dziennika|Wartość procentowa|Średnia|Procent operacji we/wy dziennika. Nie dotyczy hurtowni danych.|Brak|
|dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak|
|magazyn|Używane miejsce na dane|Bajty|Maksimum|Używane miejsce na dane. Nie dotyczy hurtowni danych.|Brak|
|connection_successful|Udane połączenia|Liczba|Łącznie|Udane połączenia|Brak|
|connection_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|Brak|
|blocked_by_firewall|Zablokowane przez zaporę|Liczba|Łącznie|Zablokowane przez zaporę|Brak|
|stanu|Zakleszczenia|Liczba|Łącznie|Zakleszczenia. Nie dotyczy hurtowni danych.|Brak|
|storage_percent|Procent użytego miejsca na danych|Wartość procentowa|Maksimum|Procent użytego miejsca na danych. Nie dotyczy magazynów danych ani baz danych w skali.|Brak|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Wartość procentowa|Średnia|Procent magazynu OLTP w pamięci. Nie dotyczy hurtowni danych.|Brak|
|workers_percent|Procent pracowników|Wartość procentowa|Średnia|Procent pracowników. Nie dotyczy hurtowni danych.|Brak|
|sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji. Nie dotyczy hurtowni danych.|Brak|
|dtu_limit|Limit jednostek DTU|Liczba|Średnia|Limit jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak|
|dtu_used|Używane jednostki DTU|Liczba|Średnia|Użyto jednostek DTU. Dotyczy baz danych opartych na jednostkach DTU.|Brak|
|cpu_limit|Limit CPU|Liczba|Średnia|Limit czasu procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Brak|
|cpu_used|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU. Dotyczy baz danych opartych na rdzeń wirtualny.|Brak|
|dwu_limit|Limit jednostek dwu|Liczba|Maksimum|Limit jednostek dwu. Dotyczy tylko magazynów danych.|Brak|
|dwu_consumption_percent|JEDNOSTEK dwu procent|Wartość procentowa|Maksimum|Procent jednostek dwu. Dotyczy tylko magazynów danych.|Brak|
|dwu_used|JEDNOSTEK dwu używane|Liczba|Maksimum|JEDNOSTEK dwu. Dotyczy tylko magazynów danych.|Brak|
|cache_hit_percent|Procent trafień w pamięci podręcznej|Wartość procentowa|Maksimum|Procent trafień w pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak|
|cache_used_percent|Procent użycia pamięci podręcznej|Wartość procentowa|Maksimum|Procent użycia pamięci podręcznej. Dotyczy tylko magazynów danych.|Brak|
|sqlserver_process_core_percent<sup>1</sup> |Procent podstawowych procesów SQL Server|Wartość procentowa|Maksimum|Procent użycia procesora CPU dla procesu SQL Server, mierzony przez system operacyjny.|Brak|
|sqlserver_process_memory_percent<sup>1</sup> |Procent pamięci procesu SQL Server|Wartość procentowa|Maksimum|Procent użycia pamięci dla procesu SQL Server, mierzony przez system operacyjny.|Brak|
|tempdb_data_size<sup>2</sup> |Rozmiar pliku danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku danych tempdb kilobajtów.|Brak|
|tempdb_log_size<sup>2</sup> |Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku dziennika bazy danych tempdb kilobajtów.|Brak|
|tempdb_log_used_percent<sup>2</sup> |Użyto dziennika% tempdb|Wartość procentowa|Maksimum|Użyto dziennika bazy danych tempdb.|Brak|
|local_tempdb_usage_percent|Procent lokalnej bazy danych tempdb|Wartość procentowa|Średnia|Procent lokalnej bazy danych tempdb. Dotyczy tylko magazynów danych.|Brak|
|app_cpu_billed|Rozliczane użycie procesora przez aplikację|Liczba|Łącznie|Rozliczane użycie procesora przez aplikację. Dotyczy bezserwerowych baz danych.|Brak|
|app_cpu_percent|Procent użycia procesora CPU aplikacji|Wartość procentowa|Średnia|Procent użycia procesora CPU aplikacji. Dotyczy bezserwerowych baz danych.|Brak|
|app_memory_percent|Procent pamięci aplikacji|Wartość procentowa|Średnia|Procent pamięci aplikacji. Dotyczy bezserwerowych baz danych.|Brak|
|allocated_data_storage|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielony magazyn danych. Nie dotyczy hurtowni danych.|Brak|
|memory_usage_percent|Procent pamięci|Wartość procentowa|Maksimum|Procent pamięci. Dotyczy tylko magazynów danych.|Brak|
|dw_backup_size_gb|Rozmiar magazynu danych|Liczba|Łącznie|Rozmiar magazynu danych składa się z rozmiaru danych i dziennika transakcji. Metryka jest uwzględniana w części "magazyn" rachunku. Dotyczy tylko magazynów danych.|Brak|
|dw_snapshot_size_gb|Rozmiar magazynu migawek|Liczba|Łącznie|Rozmiar magazynu migawek to rozmiar przyrostowych zmian przechwytywanych przez migawki w celu utworzenia zdefiniowanych przez użytkownika i automatycznych punktów przywracania. Metryka jest uwzględniana w części "magazyn" rachunku. Dotyczy tylko magazynów danych.|Brak|
|dw_geosnapshot_size_gb|Rozmiar magazynu odzyskiwania po awarii|Liczba|Łącznie|Rozmiar magazynu odzyskiwania po awarii jest odzwierciedlany na rachunku jako magazyn odzyskiwania po awarii. Dotyczy tylko magazynów danych.|Brak|
|wlg_allocation_relative_to_system_percent|Alokacja grupy obciążeń według wartości procentowej systemu|Wartość procentowa|Maksimum|Przydzielono procent zasobów względem całego systemu na grupę obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Alokacja grupy obciążeń według procentu zasobów zakończenia|Wartość procentowa|Maksimum|Przydzielono procent zasobów względem określonych zasobów Cap dla każdej grupy obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Aktywne zapytania grupy obciążenia|Liczba|Łącznie|Aktywne zapytania w grupie obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Zakolejkowane zapytania grupy obciążenia|Liczba|Łącznie|Zakolejkowane zapytania w grupie obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefined|
|active_queries|Aktywne zapytania|Liczba|Łącznie|Aktywne zapytania dla wszystkich grup obciążeń. Dotyczy tylko magazynów danych.|Brak|
|queued_queries|Zakolejkowane zapytania|Liczba|Łącznie|Zakolejkowane zapytania we wszystkich grupach obciążeń. Dotyczy tylko magazynów danych.|Brak|
|wlg_active_queries_timeouts|Limity czasu zapytania grupy obciążeń|Liczba|Łącznie|Zapytania, które przekroczyły limit czasu dla grupy obciążenia. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Efektywny minimalny procent zasobów|Wartość procentowa|Maksimum|Minimalny procent zasobów zarezerwowanych i odizolowanych dla grupy obciążenia, biorąc pod uwagę minimalny poziom usług. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Procent zasobów obowiązujących dla limitu|Wartość procentowa|Maksimum|Stały limit procentu zasobów dozwolonych dla grupy obciążenia, biorąc pod uwagę efektywną minimalną stawkę procentową zasobów przydzieloną dla innych grup obciążeń. Dotyczy tylko magazynów danych.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Rozmiar magazynu pełnej kopii zapasowej|Bajty|Maksimum|Łączny rozmiar magazynu pełnej kopii zapasowej. Dotyczy baz danych opartych na rdzeń wirtualny. Nie dotyczy baz danych w skali.|Brak|
|diff_backup_size_bytes|Różnicowy rozmiar magazynu kopii zapasowych|Bajty|Maksimum|Skumulowany, zróżnicowany rozmiar magazynu kopii zapasowych. Dotyczy baz danych opartych na rdzeń wirtualny. Nie dotyczy baz danych w skali.|Brak|
|log_backup_size_bytes|Rozmiar magazynu kopii zapasowej dziennika|Bajty|Maksimum|Rozmiar magazynu kopii zapasowej dziennika zbiorczego. Dotyczy baz danych opartych na rdzeń wirtualny i w skali.|Brak|
|snapshot_backup_size_bytes|Rozmiar magazynu kopii zapasowej migawek|Bajty|Maksimum|Rozmiar magazynu kopii zapasowej migawki skumulowanej. Dotyczy baz danych w skali.|Brak|
|base_blob_size_bytes|Rozmiar podstawowego magazynu obiektów BLOB|Bajty|Maksimum|Rozmiar podstawowego magazynu obiektów BLOB. Dotyczy baz danych w skali.|Brak|

<sup>1</sup> ta Metryka jest dostępna dla baz danych korzystających z modelu zakupów rdzeń wirtualny o wartości 2 rdzeni wirtualnych i wyższych lub 200 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU. 

<sup>Ta</sup> Metryka jest dostępna dla baz danych korzystających z modelu zakupów rdzeń wirtualny o wartości 2 rdzeni wirtualnych i wyższych lub 200 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU. Ta Metryka jest obecnie niedostępna dla baz danych lub magazynów danych w skali.

## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/serwery/elasticPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Brak|
|database_cpu_percent|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|DatabaseResourceId|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Wartość procentowa|Średnia|Procent użycia operacji we/wy na danych|Brak|
|database_physical_data_read_percent|Procent użycia operacji we/wy na danych|Wartość procentowa|Średnia|Procent użycia operacji we/wy na danych|DatabaseResourceId|
|log_write_percent|Procent operacji we/wy dziennika|Wartość procentowa|Średnia|Procent operacji we/wy dziennika|Brak|
|database_log_write_percent|Procent operacji we/wy dziennika|Wartość procentowa|Średnia|Procent operacji we/wy dziennika|DatabaseResourceId|
|dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent jednostek DTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak|
|database_dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent użycia jednostek DTU|DatabaseResourceId|
|storage_percent|Procent użytego miejsca na danych|Wartość procentowa|Średnia|Procent użytego miejsca na danych|Brak|
|workers_percent|Procent pracowników|Wartość procentowa|Średnia|Procent pracowników|Brak|
|database_workers_percent|Procent pracowników|Wartość procentowa|Średnia|Procent pracowników|DatabaseResourceId|
|sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji|Brak|
|database_sessions_percent|Procent sesji|Wartość procentowa|Średnia|Procent sesji|DatabaseResourceId|
|eDTU_limit|limit liczby jednostek eDTU|Liczba|Średnia|limit liczby jednostek eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak|
|storage_limit|Maksymalny rozmiar danych|Bajty|Średnia|Maksymalny rozmiar danych|Brak|
|eDTU_used|użyta wartość eDTU|Liczba|Średnia|użyta wartość eDTU. Dotyczy pul elastycznych opartych na jednostkach DTU.|Brak|
|database_eDTU_used|użyta wartość eDTU|Liczba|Średnia|użyta wartość eDTU|DatabaseResourceId|
|storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|Brak|
|database_storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|DatabaseResourceId|
|xtp_storage_percent|Procent magazynu OLTP w pamięci|Wartość procentowa|Średnia|Procent magazynu OLTP w pamięci|Brak|
|cpu_limit|Limit CPU|Liczba|Średnia|Limit czasu procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Brak|
|database_cpu_limit|Limit CPU|Liczba|Średnia|Limit CPU|DatabaseResourceId|
|cpu_used|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU. Dotyczy pul elastycznych opartych na rdzeń wirtualny.|Brak|
|database_cpu_used|Użycie procesora CPU|Liczba|Średnia|Użycie procesora CPU|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Procent podstawowych procesów SQL Server|Wartość procentowa|Maksimum|Procent użycia procesora CPU dla procesu SQL Server, mierzony przez system operacyjny. Dotyczy pul elastycznych. |Brak|
|sqlserver_process_memory_percent<sup>1</sup>|Procent pamięci procesu SQL Server|Wartość procentowa|Maksimum|Procent użycia pamięci dla procesu SQL Server, mierzony przez system operacyjny. Dotyczy pul elastycznych. |Brak|
|tempdb_data_size<sup>2</sup>|Rozmiar pliku danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku danych tempdb kilobajtów.|Brak|
|tempdb_log_size<sup>2</sup>|Rozmiar pliku dziennika bazy danych tempdb kilobajtów|Liczba|Maksimum|Rozmiar pliku dziennika bazy danych tempdb kilobajtów. |Brak|
|tempdb_log_used_percent<sup>2</sup>|Użyto dziennika% tempdb|Wartość procentowa|Maksimum|Użyto dziennika bazy danych tempdb.|Brak|
|allocated_data_storage|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielono miejsce na danych|Brak|
|database_allocated_data_storage|Przydzielono miejsce na danych|Bajty|Średnia|Przydzielono miejsce na danych|DatabaseResourceId|
|allocated_data_storage_percent|Procent przydzielonych przestrzeni danych|Wartość procentowa|Maksimum|Procent przydzielonych przestrzeni danych|Brak|

<sup>1</sup> ta Metryka jest dostępna dla baz danych korzystających z modelu zakupów rdzeń wirtualny o wartości 2 rdzeni wirtualnych i wyższych lub 200 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU. 

<sup>Ta</sup> Metryka jest dostępna dla baz danych korzystających z modelu zakupów rdzeń wirtualny o wartości 2 rdzeni wirtualnych i wyższych lub 200 jednostek DTU i wyższych dla modeli zakupów opartych na jednostkach DTU. Ta Metryka jest obecnie niedostępna dla baz danych w skali.


## <a name="microsoftsqlservers"></a>Microsoft. SQL/serwery

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent użycia jednostek DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Procent użycia jednostek DTU|Wartość procentowa|Średnia|Procent użycia jednostek DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|ElasticPoolResourceId|
|database_storage_used|Używane miejsce na dane|Bajty|Średnia|Używane miejsce na dane|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|Używane jednostki DTU|Liczba|Średnia|Używane jednostki DTU|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|virtual_core_count|Liczba rdzeni wirtualnych|Liczba|Średnia|Liczba rdzeni wirtualnych|Brak|
|avg_cpu_percent|Średni procent procesora CPU|Wartość procentowa|Średnia|Średni procent procesora CPU|Brak|
|reserved_storage_mb|Zarezerwowane miejsce w magazynie|Liczba|Średnia|Zarezerwowane miejsce w magazynie|Brak|
|storage_space_used_mb|Używane miejsce do magazynowania|Liczba|Średnia|Używane miejsce do magazynowania|Brak|
|io_requests|Liczba żądań we/wy|Liczba|Średnia|Liczba żądań we/wy|Brak|
|io_bytes_read|Odczytane bajty we/wy|Bajty|Średnia|Odczytane bajty we/wy|Brak|
|io_bytes_written|Bajty we/wy zapisywane|Bajty|Średnia|Bajty we/wy zapisywane|Brak|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Używana pojemność|Bajty|Średnia|Używana pojemność konta|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Pojemność obiektu BLOB|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Blob service konta magazynu w bajtach.|Blobtype, warstwa|
|BlobCount|Liczba obiektów BLOB|Liczba|Średnia|Liczba obiektów BLOB w Blob service konta magazynu.|Blobtype, warstwa|
|ContainerCount|Liczba kontenerów obiektów BLOB|Liczba|Średnia|Liczba kontenerów w Blob service konta magazynu.|Brak|
|IndexCapacity|Pojemność indeksu|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez ADLS Gen2 (hierarchiczny) indeks w bajtach.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Pojemność tabeli|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez Table service konta magazynu w bajtach.|Brak|
|TableCount|Liczba tabel|Liczba|Średnia|Liczba tabel w Table service konta magazynu.|Brak|
|TableEntityCount|Liczba jednostek tabeli|Liczba|Średnia|Liczba jednostek tabeli w Table service konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|Pojemność plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługę plików konta magazynu w bajtach.|Udziału|
|FileCount|Liczba plików|Liczba|Średnia|Liczba plików w usłudze plików konta magazynu.|Udziału|
|FileShareCount|Liczba udziałów plików|Liczba|Średnia|Liczba udziałów plików w usłudze plików konta magazynu.|Brak|
|FileShareSnapshotCount|Liczba migawek udziału plików|Liczba|Średnia|Liczba migawek znajdujących się w udziale w usłudze plików konta magazynu.|Udziału|
|FileShareSnapshotSize|Rozmiar migawki udziału plików|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez migawki w usłudze plików konta magazynu w bajtach.|Udziału|
|FileShareQuota|Rozmiar przydziału udziału plików|Bajty|Średnia|Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach.|Udziału|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, Authentication, przeudziale|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotyp, ApiName, uwierzytelnianie, przeudziale|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotyp, ApiName, uwierzytelnianie, przeudziale|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Pojemność kolejki|Bajty|Średnia|Ilość miejsca do magazynowania używanego przez usługa kolejki konta magazynu w bajtach.|Brak|
|QueueCount|Liczba kolejek|Liczba|Średnia|Liczba kolejek w usługa kolejki konta magazynu.|Brak|
|QueueMessageCount|Liczba komunikatów w kolejce|Liczba|Średnia|Przybliżona liczba komunikatów w kolejce w usługa kolejki konta magazynu.|Brak|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Użyj wymiaru responsetype dla liczby różnych typów odpowiedzi.|Responsetype, geotype, ApiName, uwierzytelnianie|
|Ruch przychodzący|Ruch przychodzący|Bajty|Łącznie|Ilość danych wejściowych w bajtach. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|Geotype, ApiName, uwierzytelnianie|
|Ruch wychodzący|Ruch wychodzący|Bajty|Łącznie|Ilość danych wyjściowych w bajtach. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|Geotype, ApiName, uwierzytelnianie|
|SuccessServerLatency|Opóźnienie serwera dla powodzenia|)|Średnia|Średnie opóźnienie używane przez usługę Azure Storage do przetworzenia pomyślnego żądania w milisekundach. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|Geotype, ApiName, uwierzytelnianie|
|SuccessE2ELatency|Opóźnienie E2E dla powodzenia|)|Średnia|Średni czas oczekiwania na zakończenie pomyślnych żądań skierowanych do usługi magazynu lub określonej operacji interfejsu API (w milisekundach). Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|Geotype, ApiName, uwierzytelnianie|
|Dostępność|Dostępność|Wartość procentowa|Średnia|Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|Geotype, ApiName, uwierzytelnianie|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/pamięci podręczne

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ClientIOPS|Łączna liczba operacji we/wy klienta|Liczba|Średnia|Częstotliwość operacji plików klienta przetwarzanych przez pamięć podręczną.|Brak|
|ClientLatency|Średnie opóźnienie klienta|)|Średnia|Średnie opóźnienie operacji plików klienta w pamięci podręcznej magazynu.|Brak|
|ClientReadIOPS|Liczba operacji we/wy odczytu klienta|CountPerSecond|Średnia|Operacje odczytu klienta na sekundę.|Brak|
|ClientReadThroughput|Średnia przepływność odczytu pamięci podręcznej|BytesPerSecond|Średnia|Szybkość transferu danych odczytu klienta.|Brak|
|ClientWriteIOPS|Liczba operacji we/wy zapisu klienta|CountPerSecond|Średnia|Operacje zapisu klienta na sekundę.|Brak|
|ClientWriteThroughput|Średnia przepływność zapisu w pamięci podręcznej|BytesPerSecond|Średnia|Szybkość transferu danych przez zapis klienta.|Brak|
|ClientMetadataReadIOPS|Liczba operacji we/wy odczytu metadanych klienta|CountPerSecond|Średnia|Częstotliwość operacji plików klienta wysyłanych do pamięci podręcznej, z wyłączeniem odczytów danych, które nie modyfikują trwałego stanu.|Brak|
|ClientMetadataWriteIOPS|Liczba IOPS zapisu metadanych klienta|CountPerSecond|Średnia|Szybkość operacji plików klienta wysyłanych do pamięci podręcznej, z wyjątkiem zapisów danych, które modyfikują stan trwały.|Brak|
|ClientLockIOPS|Liczba operacji we/wy blokady klienta|CountPerSecond|Średnia|Operacje blokowania plików klienta na sekundę.|Brak|
|StorageTargetHealth|Kondycja docelowa magazynu|Liczba|Średnia|Wartość logiczna wyników testu łączności między pamięcią podręczną i magazynem.|Brak|
|Czas|Czas|Liczba|Średnia|Wartość logiczna wyników testu łączności między pamięcią podręczną i systemem monitorowania.|Brak|
|StorageTargetIOPS|Łączna liczba operacji we/wy StorageTarget|Liczba|Średnia|Szybkość wszystkich operacji na plikach wysyłanych przez pamięć podręczną do określonego StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget zapisu IOPS|Liczba|Średnia|Częstotliwość operacji zapisu plików wysyłanych przez pamięć podręczną do określonego StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Przepływność zapisu asynchronicznego StorageTarget|BytesPerSecond|Średnia|Częstotliwość asynchronicznego zapisywania danych w pamięci podręcznej w określonym StorageTarget. Są to zapisy oportunistyczne, które nie powodują blokowania klientów.|StorageTarget|
|StorageTargetSyncWriteThroughput|Przepływność zapisu synchronicznego StorageTarget|BytesPerSecond|Średnia|Częstotliwość synchronicznego zapisywania danych w pamięci podręcznej w określonym StorageTarget. Są to zapisy, które powodują blokowanie klientów.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget całkowita przepływność zapisu|BytesPerSecond|Średnia|Łączna szybkość, z jaką pamięć podręczna zapisuje dane do określonego StorageTarget.|StorageTarget|
|StorageTargetLatency|Opóźnienie StorageTarget|)|Średnia|Średnie opóźnienie błądzenia wszystkich operacji plików wysyłanych przez pamięć podręczną do partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Liczba operacji we/wy odczytu metadanych StorageTarget|CountPerSecond|Średnia|Częstotliwość operacji na plikach, które nie modyfikują stanu trwałego i z wyłączeniem operacji odczytu, którą pamięć podręczna wysyła do określonego StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Liczba IOPS zapisu w metadanych StorageTarget|CountPerSecond|Średnia|Częstotliwość wykonywania operacji na plikach, która modyfikuje trwały stan i wykluczając operację zapisu, którą pamięć podręczna wysyła do określonego StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget operacji we/wy odczytu|CountPerSecond|Średnia|Częstotliwość operacji odczytu plików wysyłanych przez pamięć podręczną do określonego StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget z wyprzedzeniem|BytesPerSecond|Średnia|Częstotliwość odczytywania przez opportunisticly pamięci podręcznej danych z StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Przepływność wypełnienia StorageTarget|BytesPerSecond|Średnia|Częstotliwość odczytywania przez pamięć podręczną danych z StorageTarget w celu obsługi chybień w pamięci podręcznej.|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget całkowita przepływność odczytu|BytesPerSecond|Średnia|Łączna szybkość, z jaką pamięć podręczna odczytuje dane z określonego StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Wynik synchronizacji sesji|Liczba|Średnia|Metryka, która rejestruje wartość 1 za każdym razem, gdy punkt końcowy serwera pomyślnie ukończy sesję synchronizacji z punktem końcowym w chmurze|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Synchronizowane pliki|Liczba|Łącznie|Liczba synchronizowanych plików|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Pliki, których nie należy synchronizować|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Stan online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Odwołanie do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Rozmiar odwołania do warstw w chmurze|Bajty|Łącznie|Rozmiar danych, które zostały odwołane|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Przepływność odwołań do warstw w chmurze|BytesPerSecond|Średnia|Rozmiar przepływności operacji odwoływania danych|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Rozmiar odwołania do warstw w chmurze według aplikacji|Bajty|Łącznie|Rozmiar danych, które zostały wywołane przez aplikację|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Synchronizowane pliki|Liczba|Łącznie|Liczba synchronizowanych plików|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Pliki, których nie należy synchronizować|Liczba|Łącznie|Nie można zsynchronizować liczby plików|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Synchronizowane pliki|Liczba|Łącznie|Liczba synchronizowanych plików|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Pliki, których nie należy synchronizować|Liczba|Łącznie|Nie można zsynchronizować liczby plików|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Zsynchronizowane bajty|Bajty|Łącznie|Łączny rozmiar pliku transferowanego dla sesji synchronizacji|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerHeartbeat|Stan online serwera|Liczba|Maksimum|Metryka, która rejestruje wartość 1 za każdym razem, gdy serwer resigtered pomyślnie rejestruje puls przy użyciu punktu końcowego w chmurze|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Odwołanie do warstw w chmurze|Bajty|Łącznie|Łączny rozmiar danych, które zostały wywołane przez serwer|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|ResourceUtilization|Użycie SU%|Wartość procentowa|Maksimum|Użycie SU%|Logicznaname, PartitionId|
|InputEvents|Zdarzenia wejściowe|Liczba|Łącznie|Zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventBytes|Bajty zdarzeń wejściowych|Bajty|Łącznie|Bajty zdarzeń wejściowych|Logicznaname, PartitionId|
|LateInputEvents|Opóźnione zdarzenia wejściowe|Liczba|Łącznie|Opóźnione zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputEvents|Zdarzenia wyjściowe|Liczba|Łącznie|Zdarzenia wyjściowe|Logicznaname, PartitionId|
|ConversionErrors|Błędy konwersji danych|Liczba|Łącznie|Błędy konwersji danych|Logicznaname, PartitionId|
|Errors|Błędy środowiska uruchomieniowego|Liczba|Łącznie|Błędy środowiska uruchomieniowego|Logicznaname, PartitionId|
|DroppedOrAdjustedEvents|Zdarzenia poza kolejnością|Liczba|Łącznie|Zdarzenia poza kolejnością|Logicznaname, PartitionId|
|AMLCalloutRequests|Żądania funkcji|Liczba|Łącznie|Żądania funkcji|Logicznaname, PartitionId|
|AMLCalloutFailedRequests|Nieudane żądania funkcji|Liczba|Łącznie|Nieudane żądania funkcji|Logicznaname, PartitionId|
|AMLCalloutInputEvents|Zdarzenia funkcji|Liczba|Łącznie|Zdarzenia funkcji|Logicznaname, PartitionId|
|DeserializationError|Błędy deserializacji danych wejściowych|Liczba|Łącznie|Błędy deserializacji danych wejściowych|Logicznaname, PartitionId|
|EarlyInputEvents|Wczesne zdarzenia wejściowe|Liczba|Łącznie|Wczesne zdarzenia wejściowe|Logicznaname, PartitionId|
|OutputWatermarkDelaySeconds|Opóźnienie znaku wodnego|Sekundy|Maksimum|Opóźnienie znaku wodnego|Logicznaname, PartitionId|
|InputEventsSourcesBacklogged|Zaległe zdarzenia wejściowe|Liczba|Maksimum|Zaległe zdarzenia wejściowe|Logicznaname, PartitionId|
|InputEventsSourcesPerSecond|Odebrane źródła danych wejściowych|Liczba|Łącznie|Odebrane źródła danych wejściowych|Logicznaname, PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/obszary robocze

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Zakończono przebiegi potoku|Liczba|Łącznie|Liczba uruchomień potoków aranżacji zakończonych powodzeniem, zakończonych niepowodzeniem lub anulowanych|Wynik, Niepowodzenie, potok|
|OrchestrationActivityRunsEnded|Zakończone uruchomienia działań|Liczba|Łącznie|Liczba działań aranżacji zakończonych powodzeniem, zakończonych niepowodzeniem lub anulowanych|Wynik, Niepowodzenie, działanie, ActivityType, potok|
|OrchestrationTriggersEnded|Zakończone wyzwalacze|Liczba|Łącznie|Liczba wyzwalaczy aranżacji, które zakończyły się powodzeniem, zakończyły się niepowodzeniem lub anulowane|Wynik, Niepowodzenie, wyzwalacz|
|SQLOnDemandLoginAttempts|Próby logowania|Liczba|Łącznie|Liczba prób logowania, które zakończone lub zakończyły się niepowodzeniem|Wynik|
|SQLOnDemandQueriesEnded|Zakończono zapytania|Liczba|Łącznie|Liczba zapytań zakończonych powodzeniem, zakończonych niepowodzeniem lub anulowanych|Wynik|
|SQLOnDemandQueryProcessedBytes|Przetworzone dane|Bajty|Łącznie|Ilość danych przetworzonych przez zapytania|Brak|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|SparkJobsEnded|Aplikacje zakończone|Liczba|Łącznie|Liczba zakończonych aplikacji|Typu zadania, wynik zadania|
|CoresCapacity|Pojemność rdzeni|Liczba|Maksimum|Pojemność rdzeni|Brak|
|MemoryCapacityGB|Pojemność pamięci (GB)|Liczba|Maksimum|Pojemność pamięci (GB)|Brak|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/obszary robocze/xmlpools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DWULimit|Limit jednostek dwu|Liczba|Maksimum|Cel poziomu usługi puli SQL|Brak|
|DWUUsed|JEDNOSTEK dwu używane|Liczba|Maksimum|Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzone przez limit jednostek dwu * jednostek dwu procent|Brak|
|DWUUsedPercent|JEDNOSTEK dwu procent użycia|Wartość procentowa|Maksimum|Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzone przez przejęcie wartości procentowej procentu procesora CPU i operacji we/wy danych|Brak|
|ConnectionsBlockedByFirewall|Połączenia zablokowane przez zaporę|Liczba|Łącznie|Liczba połączeń blokowanych przez reguły zapory. Odwiedzaj ponownie zasady kontroli dostępu dla puli SQL i monitoruj te połączenia, jeśli liczba jest wysoka|Brak|
|AdaptiveCacheHitPercent|Odsetek trafień adaptacyjnej pamięci podręcznej|Wartość procentowa|Maksimum|Mierzy, jak dobre obciążenia korzystają z adaptacyjnej pamięci podręcznej. Użyj tej metryki z metryką procentu trafień w pamięci podręcznej, aby określić, czy skalować w celu uzyskania dodatkowej pojemności czy ponownie uruchamiać obciążenia, aby podręczna|Brak|
|AdaptiveCacheUsedPercent|Procent użycia adaptacyjnej pamięci podręcznej|Wartość procentowa|Maksimum|Mierzy, jak dobre obciążenia korzystają z adaptacyjnej pamięci podręcznej. Użyj tej metryki z metryką procentową wykorzystania pamięci podręcznej, aby określić, czy skalowanie ma być skalowane w celu uzyskania dodatkowej pojemności czy ponownego uruchomienia obciążeń w celu uzyskania|Brak|
|LocalTempDBUsedPercent|Procent użycia lokalnej bazy danych tempdb|Wartość procentowa|Maksimum|Użycie lokalnej bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut.|Brak|
|MemoryUsedPercent|Procent wykorzystania pamięci|Wartość procentowa|Maksimum|Użycie pamięci we wszystkich węzłach w puli SQL|Brak|
|Połączenia|Połączenia|Liczba|Łącznie|Łączna Liczba logowań do puli SQL|Wynik|
|WLGActiveQueries|Aktywne zapytania grupy obciążenia|Liczba|Łącznie|Aktywne zapytania w grupie obciążenia. Użycie tej metryki nie zostało odfiltrowane i rozdzielenie spowoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie.|IsUserDefined, obciążenie|
|WLGActiveQueriesTimeouts|Limity czasu zapytania grupy obciążeń|Liczba|Łącznie|Zapytania dotyczące grupy obciążeń, które przekroczyły limit czasu. Limity czasu zapytania zgłoszone przez tę metrykę są wykonywane dopiero po rozpoczęciu wykonywania zapytania (nie obejmują czasu oczekiwania z powodu blokady lub oczekiwania na zasoby)|IsUserDefined, obciążenie|
|WLGAllocationBySystemPercent|Alokacja grupy obciążeń według wartości procentowej systemu|Wartość procentowa|Maksimum|Procent alokacji zasobów względem całego systemu|IsUserDefined, obciążenie|
|WLGAllocationByMaxResourcePercent|Alokacja grupy obciążeń według maksymalnego procentu zasobów|Wartość procentowa|Maksimum|Wyświetla procentową alokację zasobów względem procentu zasobów obowiązujących dla grupy obciążenia. Ta Metryka zapewnia efektywne wykorzystanie grupy obciążeń|IsUserDefined, obciążenie|
|WLGEffectiveCapResourcePercent|Procent zasobów obowiązujących dla limitu|Wartość procentowa|Maksimum|Procent zasobów obowiązujących dla grupy obciążenia. Jeśli istnieją inne grupy obciążeń z min_percentage_resource > 0, effective_cap_percentage_resource jest obniżane proporcjonalnie|IsUserDefined, obciążenie|
|wlg_effective_min_resource_percent|Efektywny minimalny procent zasobów|Wartość procentowa|Minimalne|Ustawienie efektywnej minimalnej wartości procentowej zasobu może uwzględniać ustawienia poziomu usług i grupy obciążeń. Efektywne min_percentage_resource można zwiększyć na niższych poziomach usługi|IsUserDefined, obciążenie|
|WLGQueuedQueries|Zakolejkowane zapytania grupy obciążenia|Liczba|Łącznie|Skumulowana liczba żądań umieszczonych w kolejce po osiągnięciu maksymalnego limitu współbieżności|IsUserDefined, obciążenie|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/środowiska

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Liczba|Łącznie|Liczba komunikatów odczytanych ze wszystkich źródeł zdarzeń centrum zdarzeń lub Centrum IoT Hub|Brak|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytywanych ze wszystkich centrów zdarzeń lub źródeł zdarzeń usługi IoT Hub|Brak|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze wszystkich źródeł zdarzeń|Brak|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Liczba|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Brak|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Brak|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia a sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Brak|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Brak|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Brak|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/Environments/eventsources

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Liczba|Łącznie|Liczba komunikatów odczytanych ze źródła zdarzeń|Brak|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze źródła zdarzeń|Brak|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzenia|Brak|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|Brak|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Liczba|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|Brak|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|Brak|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia a sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|Brak|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|Brak|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|Brak|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Bajty odczytu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Brak|
|DiskWriteBytesPerSecond|Bajty zapisu dysku/s|BytesPerSecond|Średnia|Średnia przepływność dysku spowodowana operacją zapisu w okresie próbnym.|Brak|
|Bajty odczytu dysku|Bajty odczytu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami odczytu w okresie próbkowania.|Brak|
|Bajty zapisu dysku|Bajty zapisu dysku|Bajty|Łącznie|Łączna przepływność dysku spowodowana operacjami zapisu w okresie próbkowania.|Brak|
|DiskReadOperations|Operacje odczytu z dysku|Liczba|Łącznie|Liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak|
|DiskWriteOperations|Operacje zapisu na dysku|Liczba|Łącznie|Liczba operacji zapisu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak|
|Operacje odczytu z dysku/s|Operacje odczytu z dysku/s|CountPerSecond|Średnia|Średnia liczba operacji odczytu we/wy w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak|
|Operacje zapisu na dysku/s|Operacje zapisu na dysku/s|CountPerSecond|Średnia|Średnia liczba operacji we/wy zapisu w poprzednim okresie próbkowania. Należy pamiętać, że operacje te mogą mieć rozmiar zmienny.|Brak|
|DiskReadLatency|Opóźnienie odczytu dysku|)|Średnia|Całkowite opóźnienie odczytu. Suma opóźnień odczytu urządzenia i jądra.|Brak|
|DiskWriteLatency|Opóźnienie zapisu na dysku|)|Średnia|Całkowite opóźnienie zapisu. Suma opóźnień między urządzeniem i zapisem jądra.|Brak|
|NetworkInBytesPerSecond|Sieć w bajtach/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla odebranego ruchu.|Brak|
|NetworkOutBytesPerSecond|Bajty wychodzące z sieci/s|BytesPerSecond|Średnia|Średnia przepływność sieci dla przesyłanego ruchu.|Brak|
|Sieć — wejście|Sieć — wejście|Bajty|Łącznie|Całkowita przepływność sieci dla odebranego ruchu.|Brak|
|Sieć — wyjście|Sieć — wyjście|Bajty|Łącznie|Całkowita przepustowość sieci przesyłanych danych.|Brak|
|MemoryUsed|Używana pamięć|Bajty|Średnia|Ilość pamięci maszyny używanej przez maszynę wirtualną.|Brak|
|MemoryGranted|Przydzielone pamięci|Bajty|Średnia|Ilość pamięci przydzielonej maszynie wirtualnej przez hosta. Pamięć nie jest przydzielana do hosta, dopóki nie zostanie ona nałożona jeden raz, a przyznana pamięć może zostać zamieniony lub zaznaczono w dymku, jeśli VMkernel wymaga pamięci.|Brak|
|MemoryActive|Pamięć aktywna|Bajty|Średnia|Ilość pamięci używanej przez maszynę wirtualną w minionym małym oknie czasu. Jest to wartość "prawda" ilości pamięci wymaganej przez maszynę wirtualną. Dodatkowa niewykorzystana pamięć może zostać zastąpiona lub zaprowadzona bez wpływu na wydajność gościa.|Brak|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Wartość procentowa|Średnia|Użycie procesora CPU. Ta wartość jest raportowana przy użyciu 100% reprezentujących wszystkie rdzenie procesora w systemie. Przykładowo 2-kierunkowa maszyna wirtualna używająca 50% systemu z czterema rdzeniem korzysta całkowicie z dwóch rdzeni.|Brak|
|PercentageCpuReady|Procent gotowych do użycia procesora|)|Łącznie|Czas gotowości to czas oczekiwania, aż procesor CPU stanie się dostępny w okresie ostatniej aktualizacji.|Brak|

## <a name="microsoftwebserverfarms"></a>Microsoft. Web/dopuszczalna

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Liczba|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Liczba|Średnia|Długość kolejki http|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|TcpSynSent|Wysłano pakiet TCP syn|Liczba|Średnia|Wysłano pakiet TCP syn|Wystąpienie|
|TcpSynReceived|Odebrano pakiet TCP syn|Liczba|Średnia|Odebrano pakiet TCP syn|Wystąpienie|
|TcpEstablished|Nawiązano ruch TCP|Liczba|Średnia|Nawiązano ruch TCP|Wystąpienie|
|TcpFinWait1|Oczekiwanie na TCP FIN 1|Liczba|Średnia|Oczekiwanie na TCP FIN 1|Wystąpienie|
|TcpFinWait2|Oczekiwanie na TCP fin 2|Liczba|Średnia|Oczekiwanie na TCP fin 2|Wystąpienie|
|TcpClosing|Zamykanie protokołu TCP|Liczba|Średnia|Zamykanie protokołu TCP|Wystąpienie|
|TcpCloseWait|Oczekiwanie na zamknięcie protokołu TCP|Liczba|Średnia|Oczekiwanie na zamknięcie protokołu TCP|Wystąpienie|
|TcpLastAck|Ostatnie potwierdzenie TCP|Liczba|Średnia|Ostatnie potwierdzenie TCP|Wystąpienie|
|TcpTimeWait|Czas oczekiwania TCP|Liczba|Średnia|Czas oczekiwania TCP|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft. Web/Sites (z wyjątkiem funkcji) 

> [!NOTE]
> **Użycie systemu plików** to nowa Metryka, która jest wdrażana globalnie, nie jest oczekiwane żadne dane, chyba że listy dozwolonych się do prywatnej wersji zapoznawczej.

> [!IMPORTANT]
> **Średni czas odpowiedzi** będzie przestarzały, aby uniknąć pomyłek w agregacji metryk. Użyj **czasu odpowiedzi** jako zamiennika.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Liczba|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Liczba|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Liczba|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|HttpResponseTime|Czas odpowiedzi|Sekundy|Łącznie|Czas odpowiedzi|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi (przestarzałe)|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|AppConnections|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Handles|Liczba dojść|Liczba|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Liczba|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Liczba|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|
|HealthCheckStatus|Stan sprawdzania kondycji|Liczba|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|FileSystemUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|Brak|

## <a name="microsoftwebsites-functions"></a>Microsoft. Web/Sites (funkcje)

> [!NOTE]
> **Użycie systemu plików** to nowa Metryka, która jest wdrażana globalnie, nie jest oczekiwane żadne dane, chyba że listy dozwolonych się do prywatnej wersji zapoznawczej.

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonywania funkcji|MB/milisekundy|Łącznie|[Jednostki wykonywania funkcji](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Liczba|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|
|HealthCheckStatus|Stan sprawdzania kondycji|Liczba|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|FileSystemUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|Brak|

## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/miejsca

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|Czas procesora CPU|Sekundy|Łącznie|Czas procesora CPU|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Liczba|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Liczba|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Liczba|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|MemoryWorkingSet|Zestaw roboczy pamięci|Bajty|Średnia|Zestaw roboczy pamięci|Wystąpienie|
|AverageMemoryWorkingSet|Średni zestaw roboczy pamięci|Bajty|Średnia|Średni zestaw roboczy pamięci|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|HttpResponseTime|Czas odpowiedzi|Sekundy|Średnia|Czas odpowiedzi|Wystąpienie|
|FunctionExecutionUnits|Jednostki wykonywania funkcji|Liczba|Łącznie|Jednostki wykonywania funkcji|Wystąpienie|
|FunctionExecutionCount|Liczba wykonań funkcji|Liczba|Łącznie|Liczba wykonań funkcji|Wystąpienie|
|AppConnections|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Handles|Liczba dojść|Liczba|Średnia|Liczba dojść|Wystąpienie|
|Wątki|Liczba wątków|Liczba|Średnia|Liczba wątków|Wystąpienie|
|PrivateBytes|Bajty prywatne|Bajty|Średnia|Bajty prywatne|Wystąpienie|
|IoReadBytesPerSecond|Bajty odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty odczytu we/wy na sekundę|Wystąpienie|
|IoWriteBytesPerSecond|Bajty zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Bajty zapisu we/wy na sekundę|Wystąpienie|
|IoOtherBytesPerSecond|Inne bajty we/wy na sekundę|BytesPerSecond|Łącznie|Inne bajty we/wy na sekundę|Wystąpienie|
|IoReadOperationsPerSecond|Operacje odczytu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje odczytu we/wy na sekundę|Wystąpienie|
|IoWriteOperationsPerSecond|Operacje zapisu we/wy na sekundę|BytesPerSecond|Łącznie|Operacje zapisu we/wy na sekundę|Wystąpienie|
|IoOtherOperationsPerSecond|Inne operacje we/wy na sekundę|BytesPerSecond|Łącznie|Inne operacje we/wy na sekundę|Wystąpienie|
|RequestsInApplicationQueue|Żądania w kolejce aplikacji|Liczba|Średnia|Żądania w kolejce aplikacji|Wystąpienie|
|CurrentAssemblies|Bieżące zestawy|Liczba|Średnia|Bieżące zestawy|Wystąpienie|
|TotalAppDomains|Łączna liczba domen aplikacji|Liczba|Średnia|Łączna liczba domen aplikacji|Wystąpienie|
|TotalAppDomainsUnloaded|Całkowita liczba zwolnionych domen aplikacji|Liczba|Średnia|Całkowita liczba zwolnionych domen aplikacji|Wystąpienie|
|Gen0Collections|Zbieranie elementów bezużytecznych generacji 0|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 0|Wystąpienie|
|Gen1Collections|Wyrzucanie elementów bezużytecznych generacji 1|Liczba|Łącznie|Wyrzucanie elementów bezużytecznych generacji 1|Wystąpienie|
|Gen2Collections|Zbieranie elementów bezużytecznych generacji 2|Liczba|Łącznie|Zbieranie elementów bezużytecznych generacji 2|Wystąpienie|
|HealthCheckStatus|Stan sprawdzania kondycji|Liczba|Średnia|Stan sprawdzania kondycji|Wystąpienie|
|FileSystemUsage|Użycie systemu plików|Bajty|Średnia|Użycie systemu plików|Brak|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Dane w|Bajty|Łącznie|Dane w|Wystąpienie|
|BytesSent|Dane wychodzące|Bajty|Łącznie|Dane wychodzące|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|HTTP 401|Liczba|Łącznie|HTTP 401|Wystąpienie|
|Http403|HTTP 403|Liczba|Łącznie|HTTP 403|Wystąpienie|
|Http404|HTTP 404|Liczba|Łącznie|HTTP 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Błędy serwera http|Liczba|Łącznie|Błędy serwera http|Wystąpienie|
|AverageResponseTime|Średni czas odpowiedzi|Sekundy|Średnia|Średni czas odpowiedzi|Wystąpienie|
|CpuPercentage|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
|DiskQueueLength|Długość kolejki dysku|Liczba|Średnia|Długość kolejki dysku|Wystąpienie|
|HttpQueueLength|Długość kolejki http|Liczba|Średnia|Długość kolejki http|Wystąpienie|
|ActiveRequests|Aktywne żądania|Liczba|Łącznie|Aktywne żądania|Wystąpienie|
|TotalFrontEnds|Łączne frontony|Liczba|Średnia|Łączne frontony|Brak|
|SmallAppServicePlanInstances|Niewielka App Service planowanie procesów roboczych|Liczba|Średnia|Niewielka App Service planowanie procesów roboczych|Brak|
|MediumAppServicePlanInstances|Średni App Service pracowników planu|Liczba|Średnia|Średni App Service pracowników planu|Brak|
|LargeAppServicePlanInstances|Duże App Service planowanie procesów roboczych|Liczba|Średnia|Duże App Service planowanie procesów roboczych|Brak|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metryka|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|Wymiary|
|---|---|---|---|---|---|
|Łączna|Łączna liczba procesów roboczych|Liczba|Średnia|Łączna liczba procesów roboczych|Brak|
|WorkersAvailable|Dostępni pracownicy|Liczba|Średnia|Dostępni pracownicy|Brak|
|WorkersUsed|Używani pracownicy|Liczba|Średnia|Używani pracownicy|Brak|
|CpuPercentage|Procent użycia procesora CPU|Wartość procentowa|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Procent pamięci|Wartość procentowa|Średnia|Procent pamięci|Wystąpienie|
## <a name="next-steps"></a>Następne kroki
* [Przeczytaj informacje o metrykach w Azure Monitor](data-platform.md)
* [Tworzenie alertów dotyczących metryk](alerts-overview.md)
* [Eksportowanie metryk do magazynu, centrum zdarzeń lub Log Analytics](platform-logs-overview.md)

