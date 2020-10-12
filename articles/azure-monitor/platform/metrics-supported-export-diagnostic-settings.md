---
title: Zachowanie eksportowania metryk z wartościami NULL i zero
description: Dyskusja wartości NULL i zero podczas eksportowania metryk i wskaźnika do listy metryk, które nie są eksportowane.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: ca6acb97e52123a0663d988b3f217d305bce2c4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131688"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metryki platformy w usłudze Azure Monitor, które można eksportować za pośrednictwem ustawień diagnostycznych

Azure Monitor domyślnie udostępnia [metryki platformy](data-platform-metrics.md) bez konfiguracji. Zapewnia kilka sposobów współpracy z metrykami platformy, w tym wykresów w portalu, uzyskiwania dostępu do nich za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Zobacz [metryki — obsługa](metrics-supported.md) pełnej listy metryk platformy dostępnych obecnie w ramach skonsolidowanego potoku metryki Azure monitor. Aby wykonać zapytanie o i uzyskać dostęp do tych metryk, użyj [wersji interfejsu api 2018-01-01](/rest/api/monitor/metricdefinitions). Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>Metryki, które nie są eksportowane za pośrednictwem ustawień diagnostycznych

Zawartość użyta w tej lokalizacji została przeniesiona na [listę obsługiwanych metryk Azure monitor](metrics-supported.md#exporting-platform-metrics-to-other-locations).

Podczas eksportowania metryk przy użyciu ustawień diagnostycznych są dostępne ograniczenia. Wszystkie metryki są eksportowane przy użyciu interfejsu API REST. 

## <a name="exported-zero-vs-null-values"></a>Wyeksportowane wartości zerowe a NULL 

Metryki mają różne zachowanie w przypadku wartości 0 lub NULL.  Niektóre metryki raportują 0, gdy nie są uzyskiwane żadne dane, na przykład metryki dotyczące błędów HTTP. Inne metryki przechowują wartości NULL, gdy nie są uzyskiwane żadne dane, ponieważ może to wskazywać, że zasób jest w trybie offline. Różnicę można zobaczyć w przypadku wykresów tych metryk z wartościami NULL wyświetlanymi jako [linie kreskowane](metrics-troubleshoot.md#chart-shows-dashed-line). 

Gdy metryki platformy można eksportować za pomocą ustawień diagnostycznych, są one zgodne z zachowaniem metryki. Oznacza to, że eksportują wartości NULL, gdy zasób nie wysyła żadnych danych.  Eksportują "0" tylko wtedy, gdy zostały faktycznie wyemitowane przez bazowego zasobu. 

W przypadku usunięcia grupy zasobów lub określonego zasobu dane metryk z zasobów, których to dotyczy, nie są już wysyłane do ustawień diagnostycznych. Oznacza to, że nie jest już wyświetlany w Event Hubs, konta usługi Azure Storage i Log Analytics obszary robocze.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Metryki używane do eksportowania wartości zero dla wartości NULL

Przed 1 czerwca 2020, poniższe metryki **służą do** emisji "0", gdy nie było danych. Te zera można następnie wyeksportować do systemów podrzędnych, takich jak Log Analytics i Azure Storage.  Takie zachowanie powodowało kilka pomyłek między rzeczywistym "0s" (emitowanym przez zasób) i interpretowane jako "0s" (wartości NULL), dlatego zachowanie zostało zmienione tak, aby pasowało do tej metryki źródłowej, jak wspomniano w poprzedniej sekcji. 

Zmiana wystąpiła we wszystkich chmurach publicznych i prywatnych.

Zmiana nie miała wpływu na działanie żadnego z następujących środowisk: 
   - Dzienniki zasobów platformy wyeksportowane za pomocą ustawień diagnostycznych
   - Wykresy metryk w Eksplorator metryk
   - Alerty dotyczące metryk platformy
 
Poniżej znajduje się lista metryk, których zachowanie zostało zmienione. 

| ResourceType                    | Metryka               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Nieautoryzowane żądania bramy (przestarzałe)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Łączna liczba żądań bramy (przestarzałe)  | 
| Microsoft.ApiManagement/service | Żądania successfulrequests |  Pomyślne żądania bramy (przestarzałe)  | 
| Microsoft.ApiManagement/service | Żądania |  Żądania  | 
| Microsoft.ApiManagement/service | OtherRequests |  Inne żądania bramy (przestarzałe)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Nieudane żądania bramy (przestarzałe)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Niepowodzenie zdarzeń EventHub  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Łączna liczba zdarzeń EventHub  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Rozmiar zdarzeń EventHub  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Przekroczono limit czasu zdarzeń EventHub  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Ograniczone zdarzenia EventHub  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Pomyślne zdarzenia EventHub  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  Odrzucone zdarzenia EventHub  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Opuszczone zdarzenia EventHub  | 
| Microsoft.ApiManagement/service | Czas trwania |  Całkowity czas trwania żądań bramy  | 
| Microsoft.ApiManagement/service | BackendDuration |  Czas trwania żądań wewnętrznej bazy danych  | 
| Microsoft. DBforMariaDB/serwery | storage_used |  Używany magazyn  | 
| Microsoft. DBforMariaDB/serwery | storage_percent |  Procent magazynu  | 
| Microsoft. DBforMariaDB/serwery | storage_limit |  Limit magazynu  | 
| Microsoft. DBforMariaDB/serwery | serverlog_storage_usage |  Używany magazyn dzienników serwera  | 
| Microsoft. DBforMariaDB/serwery | serverlog_storage_percent |  Procent magazynu dzienników serwera  | 
| Microsoft. DBforMariaDB/serwery | serverlog_storage_limit |  Limit magazynowania dziennika serwera  | 
| Microsoft. DBforMariaDB/serwery | seconds_behind_master |  Opóźnienie replikacji w sekundach  | 
| Microsoft. DBforMariaDB/serwery | network_bytes_ingress |  Sieć — wejście  | 
| Microsoft. DBforMariaDB/serwery | network_bytes_egress |  Sieć — wyjście  | 
| Microsoft. DBforMariaDB/serwery | memory_percent |  Procent pamięci  | 
| Microsoft. DBforMariaDB/serwery | io_consumption_percent |  Procent operacji we/wy  | 
| Microsoft. DBforMariaDB/serwery | cpu_percent |  Procent użycia procesora CPU  | 
| Microsoft. DBforMariaDB/serwery | connections_failed |  Połączenia zakończone niepowodzeniem  | 
| Microsoft. DBforMariaDB/serwery | backup_storage_used |  Używany magazyn kopii zapasowych  | 
| Microsoft. DBforMariaDB/serwery | active_connections |  Aktywne połączenia  | 
| Microsoft. DBforMySQL/serwery | storage_used |  Używany magazyn  | 
| Microsoft. DBforMySQL/serwery | storage_percent |  Procent magazynu  | 
| Microsoft. DBforMySQL/serwery | storage_limit |  Limit magazynu  | 
| Microsoft. DBforMySQL/serwery | serverlog_storage_usage |  Używany magazyn dzienników serwera  | 
| Microsoft. DBforMySQL/serwery | serverlog_storage_percent |  Procent magazynu dzienników serwera  | 
| Microsoft. DBforMySQL/serwery | serverlog_storage_limit |  Limit magazynowania dziennika serwera  | 
| Microsoft. DBforMySQL/serwery | seconds_behind_master |  Opóźnienie replikacji w sekundach  | 
| Microsoft. DBforMySQL/serwery | network_bytes_ingress |  Sieć — wejście  | 
| Microsoft. DBforMySQL/serwery | network_bytes_egress |  Sieć — wyjście  | 
| Microsoft. DBforMySQL/serwery | memory_percent |  Procent pamięci  | 
| Microsoft. DBforMySQL/serwery | io_consumption_percent |  Procent operacji we/wy  | 
| Microsoft. DBforMySQL/serwery | cpu_percent |  Procent użycia procesora CPU  | 
| Microsoft. DBforMySQL/serwery | connections_failed |  Połączenia zakończone niepowodzeniem  | 
| Microsoft. DBforMySQL/serwery | backup_storage_used |  Używany magazyn kopii zapasowych  | 
| Microsoft. DBforMySQL/serwery | active_connections |  Aktywne połączenia  | 
| Microsoft. Devices/konto | digitaltwins. Telemetry. nodes |  Symbol zastępczy telemetrii cyfrowego węzła bliźniaczych reprezentacji  | 
| Microsoft. Devices/IotHubs | twinQueries. Success |  Pomyślne zapytania bliźniaczy  | 
| Microsoft. Devices/IotHubs | twinQueries.resultSize |  Rozmiar wyniku zapytań bliźniaczych  | 
| Microsoft. Devices/IotHubs | twinQueries. Failure |  Niepowodzenie zapytań bliźniaczych  | 
| Microsoft. Devices/IotHubs | Jobs. queryJobs. Success |  Pomyślne zapytania dotyczące zadań  | 
| Microsoft. Devices/IotHubs | Jobs. queryJobs. Failure |  Nieudane kwerendy zadań  | 
| Microsoft. Devices/IotHubs | Jobs. listJobs. Success |  Pomyślne wywołania do zadań na liście  | 
| Microsoft. Devices/IotHubs | Jobs. listJobs. Failure |  Wywołania zakończone niepowodzeniem do listy zadań  | 
| Microsoft. Devices/IotHubs | zadania. Niepowodzenie |  Zadania zakończone niepowodzeniem  | 
| Microsoft. Devices/IotHubs | Jobs. createTwinUpdateJob. Success |  Pomyślne utworzenie dwuosiowych zadań aktualizacji  | 
| Microsoft. Devices/IotHubs | Jobs. createTwinUpdateJob. Failure |  Nie można utworzyć dwuosiowych zadań aktualizacji  | 
| Microsoft. Devices/IotHubs | Jobs. createDirectMethodJob. Success |  Pomyślne utworzenie zadań wywołania metody  | 
| Microsoft. Devices/IotHubs | Jobs. createDirectMethodJob. Failure |  Nie można utworzyć zadań wywołania metody  | 
| Microsoft. Devices/IotHubs | zadania. ukończone |  Ukończone zadania  | 
| Microsoft. Devices/IotHubs | Jobs. cancelJob. Success |  Pomyślne anulowania zadań  | 
| Microsoft. Devices/IotHubs | Jobs. cancelJob. Failure |  Nieudane anulowania zadań  | 
| Microsoft. Devices/IotHubs | EventGridLatency |  Opóźnienie Event Grid (wersja zapoznawcza)  | 
| Microsoft. Devices/IotHubs | EventGridDeliveries |  Dostawy Event Grid (wersja zapoznawcza)  | 
| Microsoft. Devices/IotHubs | Devices. totalDevices |  Łączna liczba urządzeń (przestarzałe)  | 
| Microsoft. Devices/IotHubs | Devices. connectedDevices. allProtocol |  Podłączone urządzenia (przestarzałe)   | 
| Microsoft. Devices/IotHubs | deviceDataUsageV2 |  Całkowite użycie danych urządzenia (wersja zapoznawcza)  | 
| Microsoft. Devices/IotHubs | deviceDataUsage |  Całkowite użycie danych urządzenia  | 
| Microsoft. Devices/IotHubs | dailyMessageQuotaUsed |  Całkowita liczba użytych komunikatów  | 
| Microsoft. Devices/IotHubs | D2C. splot. Update. Success |  Pomyślne aktualizacje bliźniaczych urządzeń  | 
| Microsoft. Devices/IotHubs | D2C. splot. Update. size |  Rozmiar aktualizacji bliźniaczych z urządzeń  | 
| Microsoft. Devices/IotHubs | D2C. splot. Update. Failure |  Niepowodzenie aktualizacji bliźniaczych z urządzeń  | 
| Microsoft. Devices/IotHubs | D2C. splot. Read. Success |  Pomyślne odczyty sznurów z urządzeń  | 
| Microsoft. Devices/IotHubs | D2C. splot. Read. size |  Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń  | 
| Microsoft. Devices/IotHubs | D2C. splot. Read. Failure |  Nieudane odczyty sznurów z urządzeń  | 
| Microsoft. Devices/IotHubs | D2C. telemetrię. dane wejściowe. sukces |  Wysłane komunikaty telemetryczne  | 
| Microsoft. Devices/IotHubs | D2C. telemetrię. sendThrottle |  Liczba błędów ograniczania  | 
| Microsoft. Devices/IotHubs | D2C. telemetrię. allProtocol |  Próby wysłania komunikatów telemetrycznych  | 
| Microsoft. Devices/IotHubs | D2C. telemetrię. ruch wychodzący. sukces |  Routing: dostarczono komunikaty telemetryczne  | 
| Microsoft. Devices/IotHubs | D2C. dane telemetryczne. wychodzące. oddzielone |  Routing: oddzielone komunikaty telemetryczne   | 
| Microsoft. Devices/IotHubs | D2C. Telemetria. ruch wychodzący. nieprawidłowe |  Routing: komunikaty telemetryczne są niezgodne  | 
| Microsoft. Devices/IotHubs | D2C. telemetrię. ruch wychodzący. Fallback |  Routing: komunikaty dostarczane do powrotu  | 
| Microsoft. Devices/IotHubs | D2C. dane telemetryczne. wychodzące. upuszczone |  Routing: porzucone komunikaty telemetryczne   | 
| Microsoft. Devices/IotHubs | D2C. endpoints. opóźnienie. Storage |  Routing: opóźnienie komunikatu dla magazynu  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. opóźnienie. serviceBusTopics |  Routing: opóźnienie komunikatu dla Service Bus tematu  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. opóźnienie. serviceBusQueues |  Routing: opóźnienie komunikatu dla kolejki Service Bus  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. opóźnienie. eventHubs |  Routing: opóźnienie komunikatu dla centrum zdarzeń  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. opóźnienie. wbudowane. Events |  Routing: opóźnienie komunikatów dla komunikatów/zdarzeń  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. ruch wychodzący. Storage. Bytes |  Routing: dane dostarczane do magazynu  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. dane wyjściowe. Storage. blob |  Routing: obiekty blob dostarczane do magazynu  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. ruch wychodzący. Storage |  Routing: komunikaty dostarczane do magazynu  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. wychodzące. serviceBusTopics |  Routing: komunikaty dostarczane do Service Bus tematu  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. wychodzące. serviceBusQueues |  Routing: komunikaty dostarczone do kolejki Service Bus  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. wychodzące. eventHubs |  Routing: komunikaty dostarczane do centrum zdarzeń  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. wychodzące. wbudowane. Events |  Routing: komunikaty dostarczane do komunikatów/zdarzeń  | 
| Microsoft. Devices/IotHubs | komputerów |  Metryki konfiguracji  | 
| Microsoft. Devices/IotHubs | C2DMessagesExpired |  Komunikaty C2D wygasły (wersja zapoznawcza)  | 
| Microsoft. Devices/IotHubs | C2D. splot. Update. Success |  Pomyślne aktualizacje bliźniaczych z zaplecza  | 
| Microsoft. Devices/IotHubs | C2D. splot. Update. size |  Rozmiar aktualizacji przędzy od zaplecza  | 
| Microsoft. Devices/IotHubs | C2D. splot. Update. Failure |  Niepowodzenie aktualizacji bliźniaczych z zaplecza  | 
| Microsoft. Devices/IotHubs | C2D. splot. Read. Success |  Pomyślne odczyty sznurów z zaplecza  | 
| Microsoft. Devices/IotHubs | C2D. splot. Read. size |  Rozmiar odpowiedzi na odwrocie od zaplecza  | 
| Microsoft. Devices/IotHubs | C2D. splot. Read. Failure |  Nieudane odczyty sznurów z zaplecza  | 
| Microsoft. Devices/IotHubs | C2D. Methods. Success |  Pomyślne wywołania metody bezpośredniej  | 
| Microsoft. Devices/IotHubs | C2D. Methods. responseSize |  Rozmiar odpowiedzi wywołań metody bezpośredniej  | 
| Microsoft. Devices/IotHubs | C2D. Methods. requestSize |  Rozmiar żądania wywołań metody bezpośredniej  | 
| Microsoft. Devices/IotHubs | C2D. Methods. Failure |  Nieudane wywołania metody bezpośredniej  | 
| Microsoft. Devices/IotHubs | C2D. Commands. wyjście. Odrzuć. sukces |  Odrzucone komunikaty C2D  | 
| Microsoft. Devices/IotHubs | C2D. Commands. wyjście. Complete. Success |  Zakończono dostarczanie komunikatów C2D  | 
| Microsoft. Devices/IotHubs | C2D. Commands. wyjście. Abandon. Success |  Porzucone komunikaty C2D  | 
| Microsoft. Devices/provisioningServices | RegistrationAttempts |  Próby rejestracji  | 
| Microsoft. Devices/provisioningServices | DeviceAssignments |  Przypisane urządzenia  | 
| Microsoft. Devices/provisioningServices | AttestationAttempts |  Próby zaświadczania  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Łączna liczba jednostek żądania  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Łączna liczba żądań  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Żądania Mongo  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Opłata żądania Mongo  | 
| Microsoft. EventGrid/domeny | PublishSuccessLatencyInMs |  Czas oczekiwania na pomyślne publikowanie  | 
| Microsoft. EventGrid/domeny | PublishSuccessCount |  Zdarzenia opublikowane  | 
| Microsoft. EventGrid/domeny | PublishFailCount |  Publikowanie zdarzeń zakończonych niepowodzeniem  | 
| Microsoft. EventGrid/domeny | MatchedEventCount |  Dopasowane zdarzenia  | 
| Microsoft. EventGrid/domeny | DroppedEventCount |  Opuszczone zdarzenia  | 
| Microsoft. EventGrid/domeny | DeliverySuccessCount |  Dostarczone zdarzenia  | 
| Microsoft. EventGrid/domeny | DeadLetteredCount |  Zdarzenia utraconych wiadomości  | 
| Microsoft. EventGrid/eventSubscriptions | MatchedEventCount |  Dopasowane zdarzenia  | 
| Microsoft. EventGrid/eventSubscriptions | DroppedEventCount |  Opuszczone zdarzenia  | 
| Microsoft. EventGrid/eventSubscriptions | DeliverySuccessCount |  Dostarczone zdarzenia  | 
| Microsoft. EventGrid/eventSubscriptions | DeadLetteredCount |  Zdarzenia utraconych wiadomości  | 
| Microsoft. EventGrid/extensionTopics | UnmatchedEventCount |  Niedopasowane zdarzenia  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Czas oczekiwania na pomyślne publikowanie  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessCount |  Zdarzenia opublikowane  | 
| Microsoft. EventGrid/extensionTopics | PublishFailCount |  Publikowanie zdarzeń zakończonych niepowodzeniem  | 
| Microsoft. EventGrid/tematy | UnmatchedEventCount |  Niedopasowane zdarzenia  | 
| Microsoft. EventGrid/tematy | PublishSuccessLatencyInMs |  Czas oczekiwania na pomyślne publikowanie  | 
| Microsoft. EventGrid/tematy | PublishSuccessCount |  Zdarzenia opublikowane  | 
| Microsoft. EventGrid/tematy | PublishFailCount |  Publikowanie zdarzeń zakończonych niepowodzeniem  | 
| Microsoft. EventHub/klastry | Komunikaty outgoingmessages |  Komunikaty wychodzące  | 
| Microsoft. EventHub/klastry | Bajty outgoingbytes |  Bajty wychodzące.  | 
| Microsoft. EventHub/klastry | Żądania incomingrequests |  Żądania przychodzące  | 
| Microsoft. EventHub/klastry | Komunikaty incomingmessages |  Komunikaty przychodzące  | 
| Microsoft. EventHub/klastry | Bajty incomingbytes |  Bajty przychodzące.  | 
| Microsoft. EventHub/przestrzenie nazw | SVRBSY |  Błędy zajęte przez serwer (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | SUCCREQ |  Żądania zakończone powodzeniem (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | OUTMSGS |  Wiadomości wychodzące (przestarzałe) (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | Komunikaty outgoingmessages |  Komunikaty wychodzące  | 
| Microsoft. EventHub/przestrzenie nazw | Bajty outgoingbytes |  Bajty wychodzące.  | 
| Microsoft. EventHub/przestrzenie nazw | MISCERR |  Inne błędy (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | MIĘDZY |  Wewnętrzne błędy serwera (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | INREQS |  Żądania przychodzące (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | INMSGS |  Komunikaty przychodzące (przestarzałe) (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | Żądania incomingrequests |  Żądania przychodzące  | 
| Microsoft. EventHub/przestrzenie nazw | Komunikaty incomingmessages |  Komunikaty przychodzące  | 
| Microsoft. EventHub/przestrzenie nazw | Bajty incomingbytes |  Bajty przychodzące.  | 
| Microsoft. EventHub/przestrzenie nazw | FAILREQ |  Nieudane żądania (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHOUTMSGS |  Wiadomości wychodzące (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHOUTMBS |  Bajty wychodzące (przestarzałe) (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHOUTBYTES |  Bajty wychodzące (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHINMSGS |  Komunikaty przychodzące (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHINMBS |  Przychodzące bajty (przestarzałe) (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHINBYTES |  Przychodzące bajty (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHAMSGS |  Komunikaty archiwalne (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHAMBS |  Przepływność komunikatów archiwalnych (przestarzałe)  | 
| Microsoft. EventHub/przestrzenie nazw | EHABL |  Archiwum komunikatów zaległości (przestarzałe)  | 
| Microsoft. HDInsight/Klastry | NumActiveWorkers |  Liczba aktywnych procesów roboczych  | 
| Microsoft. HDInsight/Klastry | GatewayRequests |  Żądania bramy  | 
| Microsoft. HDInsight/Klastry | CategorizedGatewayRequests |  Skategoryzowane żądania bramy  | 
| Microsoft. Insights/AutoscaleSettings | ScaleActionsInitiated |  Zainicjowane akcje skalowania  | 
| Microsoft. Insights/składniki | ślady/liczba |  Ślady  | 
| Microsoft. Insights/składniki | Liczniki wydajności/requestsPerSecond |  Częstotliwość żądań HTTP  | 
| Microsoft. Insights/składniki | Liczniki wydajności/requestsInQueue |  Żądania HTTP w kolejce aplikacji  | 
| Microsoft. Insights/składniki | Liczniki wydajności/exceptionsPerSecond |  Częstotliwość wyjątków  | 
| Microsoft. Insights/składniki | pageViews/liczba |  Wyświetlenia stron  | 
| Microsoft. Insights/składniki | wyjątki/liczba |  Wyjątki  | 
| Microsoft. Kusto/klastry | StreamingIngestResults |  Wynik pozyskiwania za pomocą przesyłania strumieniowego  | 
| Microsoft. Kusto/klastry | StreamingIngestDuration |  Pozyskiwanie za pomocą przesyłania strumieniowego — czas trwania  | 
| Microsoft. Kusto/klastry | StreamingIngestDataRate |  Pozyskiwanie za pomocą przesyłania strumieniowego — szybkość danych  | 
| Microsoft. Kusto/klastry | SteamingIngestRequestRate |  Pozyskiwanie za pomocą przesyłania strumieniowego — liczba żądań  | 
| Microsoft. Kusto/klastry | QueryDuration |  Czas trwania zapytania  | 
| Microsoft. Kusto/klastry | Utrzymywani |  Utrzymywanie aktywności  | 
| Microsoft. Kusto/klastry | IngestionVolumeInMB |  Wolumin pozyskiwania (w MB)  | 
| Microsoft. Kusto/klastry | IngestionUtilization |  Wykorzystanie pozyskiwania  | 
| Microsoft. Kusto/klastry | IngestionResult |  Wynik pozyskiwania  | 
| Microsoft. Kusto/klastry | IngestionLatencyInSeconds |  Opóźnienie pozyskiwania (w sekundach)  | 
| Microsoft. Kusto/klastry | ExportUtilization |  Wykorzystanie eksportu  | 
| Microsoft. Kusto/klastry | EventsProcessedForEventHubs |  Zdarzenia przetwarzane (dla centrów zdarzeń/IoT)  | 
| Microsoft. Kusto/klastry | Procesor CPU |  Procesor CPU  | 
| Microsoft. Kusto/klastry | ContinuousExportResult |  Wynik eksportu ciągłego  | 
| Microsoft. Kusto/klastry | ContinuousExportPendingCount |  Liczba oczekujących eksportu ciągłego  | 
| Microsoft. Kusto/klastry | ContinuousExportNumOfRecordsExported |  Eksport ciągły — liczba eksportowanych rekordów  | 
| Microsoft. Kusto/klastry | ContinuousExportMaxLatenessMinutes |  Maksymalna liczba minut opóźnienia eksportu  | 
| Microsoft. Kusto/klastry | CacheUtilization |  Użycie pamięci podręcznej  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Wyzwalaj zdarzenia ograniczające  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSucceeded |  Wyzwalacze zakończone powodzeniem   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersStarted |  Uruchomiono wyzwalacze   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSkipped |  Pominięte wyzwalacze  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFired |  Wyzwolone wyzwalacze   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFailed |  Wyzwalacze zakończone niepowodzeniem   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersCompleted |  Ukończone wyzwalacze   | 
| Microsoft. Logic/integrationServiceEnvironments | RunThrottledEvents |  Uruchamianie zdarzeń z ograniczeniami  | 
| Microsoft. Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Uruchom zdarzenia uruchamiania z ograniczeniami  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsSucceeded |  Przebiegi zakończone powodzeniem  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsStarted |  Uruchomienia uruchomione  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsFailed |  Przebiegi zakończone niepowodzeniem  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCompleted |  Przebiegi zakończone  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCancelled |  Przebiegi anulowane  | 
| Microsoft. Logic/integrationServiceEnvironments | RunFailurePercentage |  Procent niepowodzeń przebiegu  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionThrottledEvents |  Zdarzenia ograniczające akcję  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSucceeded |  Akcje zakończone powodzeniem   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsStarted |  Akcje uruchomione   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSkipped |  Pominięte akcje   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsFailed |  Akcje zakończone niepowodzeniem   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsCompleted |  Wykonane akcje   | 
| Microsoft. Logic/przepływy pracy | TriggerThrottledEvents |  Wyzwalaj zdarzenia ograniczające  | 
| Microsoft. Logic/przepływy pracy | TriggersSucceeded |  Wyzwalacze zakończone powodzeniem   | 
| Microsoft. Logic/przepływy pracy | TriggersStarted |  Uruchomiono wyzwalacze   | 
| Microsoft. Logic/przepływy pracy | TriggersSkipped |  Pominięte wyzwalacze  | 
| Microsoft. Logic/przepływy pracy | TriggersFired |  Wyzwolone wyzwalacze   | 
| Microsoft. Logic/przepływy pracy | TriggersFailed |  Wyzwalacze zakończone niepowodzeniem   | 
| Microsoft. Logic/przepływy pracy | TriggersCompleted |  Ukończone wyzwalacze   | 
| Microsoft. Logic/przepływy pracy | TotalBillableExecutions |  Łączna Liczba wykonań rozliczanych  | 
| Microsoft. Logic/przepływy pracy | RunThrottledEvents |  Uruchamianie zdarzeń z ograniczeniami  | 
| Microsoft. Logic/przepływy pracy | RunStartThrottledEvents |  Uruchom zdarzenia uruchamiania z ograniczeniami  | 
| Microsoft. Logic/przepływy pracy | RunsSucceeded |  Przebiegi zakończone powodzeniem  | 
| Microsoft. Logic/przepływy pracy | RunsStarted |  Uruchomienia uruchomione  | 
| Microsoft. Logic/przepływy pracy | RunsFailed |  Przebiegi zakończone niepowodzeniem  | 
| Microsoft. Logic/przepływy pracy | RunsCompleted |  Przebiegi zakończone  | 
| Microsoft. Logic/przepływy pracy | RunsCancelled |  Przebiegi anulowane  | 
| Microsoft. Logic/przepływy pracy | RunFailurePercentage |  Procent niepowodzeń przebiegu  | 
| Microsoft. Logic/przepływy pracy | BillingUsageStorageConsumption |  Użycie rozliczeń dla wykonań zużycia magazynu  | 
| Microsoft. Logic/przepływy pracy | BillingUsageStorageConsumption |  Użycie rozliczeń dla wykonań zużycia magazynu  | 
| Microsoft. Logic/przepływy pracy | BillingUsageStandardConnector |  Użycie rozliczeń dla wykonywania łączników standardowych  | 
| Microsoft. Logic/przepływy pracy | BillingUsageStandardConnector |  Użycie rozliczeń dla wykonywania łączników standardowych  | 
| Microsoft. Logic/przepływy pracy | BillingUsageNativeOperation |  Użycie rozliczeń dla natywnych wykonań operacji  | 
| Microsoft. Logic/przepływy pracy | BillingUsageNativeOperation |  Użycie rozliczeń dla natywnych wykonań operacji  | 
| Microsoft. Logic/przepływy pracy | BillableTriggerExecutions |  Rozliczane wykonania wyzwalacza  | 
| Microsoft. Logic/przepływy pracy | BillableActionExecutions |  Rozliczane wykonania akcji  | 
| Microsoft. Logic/przepływy pracy | ActionThrottledEvents |  Zdarzenia ograniczające akcję  | 
| Microsoft. Logic/przepływy pracy | ActionsSucceeded |  Akcje zakończone powodzeniem   | 
| Microsoft. Logic/przepływy pracy | ActionsStarted |  Akcje uruchomione   | 
| Microsoft. Logic/przepływy pracy | ActionsSkipped |  Pominięte akcje   | 
| Microsoft. Logic/przepływy pracy | ActionsFailed |  Akcje zakończone niepowodzeniem   | 
| Microsoft. Logic/przepływy pracy | ActionsCompleted |  Wykonane akcje   | 
| Microsoft. Network/usługi frontdoor | WebApplicationFirewallRequestCount |  Liczba żądań zapory aplikacji sieci Web  | 
| Microsoft. Network/usługi frontdoor | TotalLatency |  Łączne opóźnienie  | 
| Microsoft. Network/usługi frontdoor | ResponseSize |  Rozmiar odpowiedzi  | 
| Microsoft. Network/usługi frontdoor | RequestSize |  Rozmiar żądania  | 
| Microsoft. Network/usługi frontdoor | RequestCount |  Liczba żądań  | 
| Microsoft. Network/usługi frontdoor | BillableResponseSize |  Rozmiar odpowiedzi do rozliczenia  | 
| Microsoft. Network/usługi frontdoor | BackendRequestLatency |  Opóźnienie żądania wewnętrznej bazy danych  | 
| Microsoft. Network/usługi frontdoor | BackendRequestCount |  Liczba żądań wewnętrznej bazy danych  | 
| Microsoft. Network/usługi frontdoor | BackendHealthPercentage |  Procent kondycji zaplecza  | 
| Microsoft. Network/trafficManagerProfiles | QpsByEndpoint |  Zwrócone zapytania według punktu końcowego  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | zaplanowane. oczekujące |  Oczekujące zaplanowane powiadomienia  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Rejestracja. Aktualizacja |  Operacje aktualizacji rejestracji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Rejestracja. Get |  Operacje odczytu rejestracji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Rejestracja. Usuwanie |  Operacje usuwania rejestracji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Rejestracja. Tworzenie |  Operacje tworzenia rejestracji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Rejestracja. wszystkie |  Operacje rejestracji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. wrongtoken |  WNS błędy autoryzacji (nieprawidłowy token)  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. tokenproviderunreachable |  Błędy autoryzacji WNS (nieosiągalne)  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. WNS. dławienia |  WNS powiadomienia z ograniczeniami  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. Success |  WNS pomyślne powiadomienia  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. pnserror |  Błędy WNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. invalidtoken |  Błędy autoryzacji WNS (nieprawidłowy token)  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. invalidnotificationsize |  Błąd nieprawidłowego rozmiaru powiadomienia WNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. invalidnotificationformat |  Nieprawidłowy format powiadomienia WNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. invalidcredentials |  WNS błędy autoryzacji (Nieprawidłowe poświadczenia)  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. expiredchannel |  Błąd WNS kanału wygasłego  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. WNS. upuszczone |  WNS usunięte powiadomienia  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. channelthrottled |  Ograniczenie kanału WNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. channeldisconnected |  Kanał WNS został odłączony  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. badchannel |  Błąd nieprawidłowego kanału WNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. WNS. authenticationerror |  Błędy uwierzytelniania WNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. usługi MPNS. dławienia |  USŁUGI MPNS powiadomienia z ograniczeniami  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. usługi MPNS. Success |  USŁUGI MPNS pomyślne powiadomienia  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. usługi MPNS. pnserror |  Błędy usługi MPNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. usługi MPNS. invalidnotificationformat |  Nieprawidłowy format powiadomienia usługi MPNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. usługi MPNS. invalidcredentials |  USŁUGI MPNS nieprawidłowe poświadczenia  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. usługi MPNS. upuszczone |  USŁUGI MPNS usunięte powiadomienia  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. usługi MPNS. channeldisconnected |  Kanał usługi MPNS został odłączony  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. usługi MPNS. badchannel |  Błąd nieprawidłowego kanału usługi MPNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. usługi MPNS. authenticationerror |  Błędy uwierzytelniania usługi MPNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. wrongchannel |  Błąd nieprawidłowego kanału GCM  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. GCM. dławienia |  GCM powiadomienia z ograniczeniami  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. Success |  GCM pomyślne powiadomienia  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. pnserror |  Błędy GCM  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. invalidnotificationsize |  Błąd nieprawidłowego rozmiaru powiadomienia GCM  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. invalidnotificationformat |  Nieprawidłowy format powiadomienia GCM  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. invalidcredentials |  GCM błędy autoryzacji (Nieprawidłowe poświadczenia)  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. expiredchannel |  Błąd GCM kanału wygasłego  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. badchannel |  Błąd nieprawidłowego kanału GCM  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. GCM. authenticationerror |  Błędy uwierzytelniania GCM  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. APN. sukces |  Pomyślne powiadomienia usługi APNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. APNs. pnserror |  Błędy usługi APNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. APNs. invalidnotificationsize |  Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. APNs. invalidcredentials |  Błędy autoryzacji usługi APNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. APNs. expiredchannel |  Błąd wygasłego kanału usługi APNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzące. APNs. badchannel |  Błąd nieprawidłowego kanału usługi APNS  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. allpns. Success |  Powiadomienia zakończone powodzeniem  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. allpns. pnserror |  Błędy zewnętrznego systemu powiadomień  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. allpns. invalidpayload |  Błędy ładunku  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | wychodzący. allpns. channelerror |  Błędy kanałów  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | notificationhub. pushs |  Wszystkie powiadomienia wychodzące  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Instalacja. upsert |  Tworzenie lub aktualizowanie operacji instalacji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Instalacja. poprawka |  Operacje instalacji poprawek  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Instalacja. Get |  Pobierz operacje instalacji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Instalacja. Delete |  Usuwanie operacji instalacji  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | Instalacja. wszystkie |  Operacje zarządzania instalacją  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | przychodzące. zaplanowane. Cancel |  Anulowano zaplanowane powiadomienia wypychane  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | przychodzące. zaplanowane |  Przesłane zaplanowane powiadomienia wypychane  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | przychodzące. ALL. Requests |  Wszystkie żądania przychodzące  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | przychodzące. ALL. failedrequests |  Wszystkie przychodzące żądania zakończone niepowodzeniem  | 
| Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs | przychodzące |  Komunikaty przychodzące  | 
| Microsoft. OperationalInsights/obszary robocze | Puls |  Puls  | 
| Microsoft. Relay/przestrzenie nazw | BytesTransferred |  BytesTransferred  | 
| Microsoft. ServiceBus/przestrzenie nazw | Komunikaty outgoingmessages |  Komunikaty wychodzące  | 
| Microsoft. ServiceBus/przestrzenie nazw | Żądania incomingrequests |  Żądania przychodzące  | 
| Microsoft. ServiceBus/przestrzenie nazw | Komunikaty incomingmessages |  Komunikaty przychodzące  | 
| Microsoft. SignalRService/sygnalizujący | Błędy usererrors |  Błędy użytkownika  | 
| Microsoft. SignalRService/sygnalizujący | SystemErrors |  Błędy systemu  | 
| Microsoft. SignalRService/sygnalizujący | OutboundTraffic |  Ruch wychodzący  | 
| Microsoft. SignalRService/sygnalizujący | MessageCount |  Liczba komunikatów  | 
| Microsoft. SignalRService/sygnalizujący | InboundTraffic |  Ruch przychodzący  | 
| Microsoft. SignalRService/sygnalizujący | ConnectionCount |  Liczba połączeń  | 
| Microsoft. SQL/managedInstances | avg_cpu_percent |  Średni procent procesora CPU  | 
| Microsoft. SQL/serwery | dtu_used |  Używane jednostki DTU  | 
| Microsoft. SQL/serwery | dtu_consumption_percent |  Procent użycia jednostek DTU  | 
| Microsoft. SQL/serwery/bazy danych | xtp_storage_percent |  Procent magazynu OLTP In-Memory  | 
| Microsoft. SQL/serwery/bazy danych | workers_percent |  Procent pracowników  | 
| Microsoft. SQL/serwery/bazy danych | sessions_percent |  Procent sesji  | 
| Microsoft. SQL/serwery/bazy danych | physical_data_read_percent |  Procent użycia operacji we/wy na danych  | 
| Microsoft. SQL/serwery/bazy danych | log_write_percent |  Procent operacji we/wy dziennika  | 
| Microsoft. SQL/serwery/bazy danych | dwu_used |  JEDNOSTEK dwu używane  | 
| Microsoft. SQL/serwery/bazy danych | dwu_consumption_percent |  JEDNOSTEK dwu procent  | 
| Microsoft. SQL/serwery/bazy danych | dtu_used |  Używane jednostki DTU  | 
| Microsoft. SQL/serwery/bazy danych | dtu_consumption_percent |  Procent użycia jednostek DTU  | 
| Microsoft. SQL/serwery/bazy danych | stanu |  Zakleszczenia  | 
| Microsoft. SQL/serwery/bazy danych | cpu_used |  Użycie procesora CPU  | 
| Microsoft. SQL/serwery/bazy danych | cpu_percent |  Procent użycia procesora CPU  | 
| Microsoft. SQL/serwery/bazy danych | connection_successful |  Udane połączenia  | 
| Microsoft. SQL/serwery/bazy danych | connection_failed |  Połączenia zakończone niepowodzeniem  | 
| Microsoft. SQL/serwery/bazy danych | cache_hit_percent |  Procent trafień w pamięci podręcznej  | 
| Microsoft. SQL/serwery/bazy danych | blocked_by_firewall |  Zablokowane przez zaporę  | 
| Microsoft. SQL/serwery/elasticPools | xtp_storage_percent |  Procent magazynu OLTP In-Memory  | 
| Microsoft. SQL/serwery/elasticPools | workers_percent |  Procent pracowników  | 
| Microsoft. SQL/serwery/elasticPools | sessions_percent |  Procent sesji  | 
| Microsoft. SQL/serwery/elasticPools | physical_data_read_percent |  Procent użycia operacji we/wy na danych  | 
| Microsoft. SQL/serwery/elasticPools | log_write_percent |  Procent operacji we/wy dziennika  | 
| Microsoft. SQL/serwery/elasticPools | eDTU_used |  użyta wartość eDTU  | 
| Microsoft. SQL/serwery/elasticPools | dtu_consumption_percent |  Procent użycia jednostek DTU  | 
| Microsoft. SQL/serwery/elasticPools | cpu_percent |  Procent użycia procesora CPU  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Łączne frontony  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Niewielka App Service planowanie procesów roboczych  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Żądania |  Żądania  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Procent pamięci  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Średni App Service pracowników planu  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Duże App Service planowanie procesów roboczych  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Długość kolejki http  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http5xx |  Błędy serwera http  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http4xx |  Http 4xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http404 |  HTTP 404  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http403 |  HTTP 403  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http401 |  HTTP 401  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http3xx |  Http 3xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http2xx |  Http 2xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Długość kolejki dysku  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | CpuPercentage |  Procent użycia procesora CPU  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | BytesSent |  Dane wychodzące  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | BytesReceived |  Dane w  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Średni czas odpowiedzi  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | ActiveRequests |  Aktywne żądania  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersUsed |  Używani pracownicy  | 
| Microsoft. Web/hostingEnvironments/workerPools | Łączna |  Łączna liczba procesów roboczych  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersAvailable |  Dostępni pracownicy  | 
| Microsoft. Web/hostingEnvironments/workerPools | MemoryPercentage |  Procent pamięci  | 
| Microsoft. Web/hostingEnvironments/workerPools | CpuPercentage |  Procent użycia procesora CPU  | 
| Microsoft. Web/dopuszczalna | TcpTimeWait |  Czas oczekiwania TCP  | 
| Microsoft. Web/dopuszczalna | TcpSynSent |  Wysłano pakiet TCP syn  | 
| Microsoft. Web/dopuszczalna | TcpSynReceived |  Odebrano pakiet TCP syn  | 
| Microsoft. Web/dopuszczalna | TcpLastAck |  Ostatnie potwierdzenie TCP  | 
| Microsoft. Web/dopuszczalna | TcpFinWait2 |  Oczekiwanie na TCP fin 2  | 
| Microsoft. Web/dopuszczalna | TcpFinWait1 |  Oczekiwanie na TCP FIN 1  | 
| Microsoft. Web/dopuszczalna | TcpEstablished |  Nawiązano ruch TCP  | 
| Microsoft. Web/dopuszczalna | TcpClosing |  Zamykanie protokołu TCP  | 
| Microsoft. Web/dopuszczalna | TcpCloseWait |  Oczekiwanie na zamknięcie protokołu TCP  | 
| Microsoft. Web/dopuszczalna | MemoryPercentage |  Procent pamięci  | 
| Microsoft. Web/dopuszczalna | HttpQueueLength |  Długość kolejki http  | 
| Microsoft. Web/dopuszczalna | DiskQueueLength |  Długość kolejki dysku  | 
| Microsoft. Web/dopuszczalna | CpuPercentage |  Procent użycia procesora CPU  | 
| Microsoft. Web/dopuszczalna | BytesSent |  Dane wychodzące  | 
| Microsoft. Web/dopuszczalna | BytesReceived |  Dane w  | 
| Microsoft. Web/witryny | TotalAppDomainsUnloaded |  Całkowita liczba zwolnionych domen aplikacji  | 
| Microsoft. Web/witryny | TotalAppDomains |  Łączna liczba domen aplikacji  | 
| Microsoft. Web/witryny | Wątki |  Liczba wątków  | 
| Microsoft. Web/witryny | RequestsInApplicationQueue |  Żądania w kolejce aplikacji  | 
| Microsoft. Web/witryny | Żądania |  Żądania  | 
| Microsoft. Web/witryny | PrivateBytes |  Bajty prywatne  | 
| Microsoft. Web/witryny | MemoryWorkingSet |  Zestaw roboczy pamięci  | 
| Microsoft. Web/witryny | IoWriteOperationsPerSecond |  Operacje zapisu we/wy na sekundę  | 
| Microsoft. Web/witryny | IoWriteBytesPerSecond |  Bajty zapisu we/wy na sekundę  | 
| Microsoft. Web/witryny | IoReadOperationsPerSecond |  Operacje odczytu we/wy na sekundę  | 
| Microsoft. Web/witryny | IoReadBytesPerSecond |  Bajty odczytu we/wy na sekundę  | 
| Microsoft. Web/witryny | IoOtherOperationsPerSecond |  Inne operacje we/wy na sekundę  | 
| Microsoft. Web/witryny | IoOtherBytesPerSecond |  Inne bajty we/wy na sekundę  | 
| Microsoft. Web/witryny | HttpResponseTime |  Czas odpowiedzi  | 
| Microsoft. Web/witryny | Http5xx |  Błędy serwera http  | 
| Microsoft. Web/witryny | Http4xx |  Http 4xx  | 
| Microsoft. Web/witryny | Http406 |  Http 406  | 
| Microsoft. Web/witryny | Http404 |  HTTP 404  | 
| Microsoft. Web/witryny | Http403 |  HTTP 403  | 
| Microsoft. Web/witryny | Http401 |  HTTP 401  | 
| Microsoft. Web/witryny | Http3xx |  Http 3xx  | 
| Microsoft. Web/witryny | Http2xx |  Http 2xx  | 
| Microsoft. Web/witryny | Http101 |  Http 101  | 
| Microsoft. Web/witryny | HealthCheckStatus |  Stan sprawdzania kondycji  | 
| Microsoft. Web/witryny | Handles |  Liczba dojść  | 
| Microsoft. Web/witryny | Gen2Collections |  Zbieranie elementów bezużytecznych generacji 2  | 
| Microsoft. Web/witryny | Gen1Collections |  Wyrzucanie elementów bezużytecznych generacji 1  | 
| Microsoft. Web/witryny | Gen0Collections |  Zbieranie elementów bezużytecznych generacji 0  | 
| Microsoft. Web/witryny | FunctionExecutionUnits |  Jednostki wykonywania funkcji  | 
| Microsoft. Web/witryny | FunctionExecutionCount |  Liczba wykonań funkcji  | 
| Microsoft. Web/witryny | CurrentAssemblies |  Bieżące zestawy  | 
| Microsoft. Web/witryny | CpuTime |  Czas procesora CPU  | 
| Microsoft. Web/witryny | BytesSent |  Dane wychodzące  | 
| Microsoft. Web/witryny | BytesReceived |  Dane w  | 
| Microsoft. Web/witryny | AverageResponseTime |  Średni czas odpowiedzi  | 
| Microsoft. Web/witryny | AverageMemoryWorkingSet |  Średni zestaw roboczy pamięci  | 
| Microsoft. Web/witryny | AppConnections |  Połączenia  | 
| Microsoft. Web/Sites/miejsca | TotalAppDomainsUnloaded |  Całkowita liczba zwolnionych domen aplikacji  | 
| Microsoft. Web/Sites/miejsca | TotalAppDomains |  Łączna liczba domen aplikacji  | 
| Microsoft. Web/Sites/miejsca | Wątki |  Liczba wątków  | 
| Microsoft. Web/Sites/miejsca | RequestsInApplicationQueue |  Żądania w kolejce aplikacji  | 
| Microsoft. Web/Sites/miejsca | Żądania |  Żądania  | 
| Microsoft. Web/Sites/miejsca | PrivateBytes |  Bajty prywatne  | 
| Microsoft. Web/Sites/miejsca | MemoryWorkingSet |  Zestaw roboczy pamięci  | 
| Microsoft. Web/Sites/miejsca | IoWriteOperationsPerSecond |  Operacje zapisu we/wy na sekundę  | 
| Microsoft. Web/Sites/miejsca | IoWriteBytesPerSecond |  Bajty zapisu we/wy na sekundę  | 
| Microsoft. Web/Sites/miejsca | IoReadOperationsPerSecond |  Operacje odczytu we/wy na sekundę  | 
| Microsoft. Web/Sites/miejsca | IoReadBytesPerSecond |  Bajty odczytu we/wy na sekundę  | 
| Microsoft. Web/Sites/miejsca | IoOtherOperationsPerSecond |  Inne operacje we/wy na sekundę  | 
| Microsoft. Web/Sites/miejsca | IoOtherBytesPerSecond |  Inne bajty we/wy na sekundę  | 
| Microsoft. Web/Sites/miejsca | HttpResponseTime |  Czas odpowiedzi  | 
| Microsoft. Web/Sites/miejsca | Http5xx |  Błędy serwera http  | 
| Microsoft. Web/Sites/miejsca | Http4xx |  Http 4xx  | 
| Microsoft. Web/Sites/miejsca | Http406 |  Http 406  | 
| Microsoft. Web/Sites/miejsca | Http404 |  HTTP 404  | 
| Microsoft. Web/Sites/miejsca | Http403 |  HTTP 403  | 
| Microsoft. Web/Sites/miejsca | Http401 |  HTTP 401  | 
| Microsoft. Web/Sites/miejsca | Http3xx |  Http 3xx  | 
| Microsoft. Web/Sites/miejsca | Http2xx |  Http 2xx  | 
| Microsoft. Web/Sites/miejsca | Http101 |  Http 101  | 
| Microsoft. Web/Sites/miejsca | HealthCheckStatus |  Stan sprawdzania kondycji  | 
| Microsoft. Web/Sites/miejsca | Handles |  Liczba dojść  | 
| Microsoft. Web/Sites/miejsca | Gen2Collections |  Zbieranie elementów bezużytecznych generacji 2  | 
| Microsoft. Web/Sites/miejsca | Gen1Collections |  Wyrzucanie elementów bezużytecznych generacji 1  | 
| Microsoft. Web/Sites/miejsca | Gen0Collections |  Zbieranie elementów bezużytecznych generacji 0  | 
| Microsoft. Web/Sites/miejsca | FunctionExecutionUnits |  Jednostki wykonywania funkcji  | 
| Microsoft. Web/Sites/miejsca | FunctionExecutionCount |  Liczba wykonań funkcji  | 
| Microsoft. Web/Sites/miejsca | CurrentAssemblies |  Bieżące zestawy  | 
| Microsoft. Web/Sites/miejsca | CpuTime |  Czas procesora CPU  | 
| Microsoft. Web/Sites/miejsca | BytesSent |  Dane wychodzące  | 
| Microsoft. Web/Sites/miejsca | BytesReceived |  Dane w  | 
| Microsoft. Web/Sites/miejsca | AverageResponseTime |  Średni czas odpowiedzi  | 
| Microsoft. Web/Sites/miejsca | AverageMemoryWorkingSet |  Średni zestaw roboczy pamięci  | 
| Microsoft. Web/Sites/miejsca | AppConnections |  Połączenia  | 
| Microsoft. SQL/serwery/bazy danych | cpu_percent | Procent użycia procesora CPU | 
| Microsoft. SQL/serwery/bazy danych | physical_data_read_percent | Procent użycia operacji we/wy na danych | 
| Microsoft. SQL/serwery/bazy danych | log_write_percent | Procent operacji we/wy dziennika | 
| Microsoft. SQL/serwery/bazy danych | dtu_consumption_percent | Procent użycia jednostek DTU | 
| Microsoft. SQL/serwery/bazy danych | connection_successful | Udane połączenia | 
| Microsoft. SQL/serwery/bazy danych | connection_failed | Połączenia zakończone niepowodzeniem | 
| Microsoft. SQL/serwery/bazy danych | blocked_by_firewall | Zablokowane przez zaporę | 
| Microsoft. SQL/serwery/bazy danych | stanu | Zakleszczenia | 
| Microsoft. SQL/serwery/bazy danych | xtp_storage_percent | Procent magazynu OLTP In-Memory | 
| Microsoft. SQL/serwery/bazy danych | workers_percent | Procent pracowników | 
| Microsoft. SQL/serwery/bazy danych | sessions_percent | Procent sesji | 
| Microsoft. SQL/serwery/bazy danych | dtu_used | Używane jednostki DTU | 
| Microsoft. SQL/serwery/bazy danych | cpu_used | Użycie procesora CPU | 
| Microsoft. SQL/serwery/bazy danych | dwu_consumption_percent | JEDNOSTEK dwu procent | 
| Microsoft. SQL/serwery/bazy danych | dwu_used | JEDNOSTEK dwu używane | 
| Microsoft. SQL/serwery/bazy danych | cache_hit_percent | Procent trafień w pamięci podręcznej | 
| Microsoft. SQL/serwery/bazy danych | wlg_allocation_relative_to_system_percent | Alokacja grupy obciążeń według wartości procentowej systemu | 
| Microsoft. SQL/serwery/bazy danych | wlg_allocation_relative_to_wlg_effective_cap_percent | Alokacja grupy obciążeń według maksymalnego procentu zasobów | 
| Microsoft. SQL/serwery/bazy danych | wlg_active_queries | Aktywne zapytania grupy obciążenia | 
| Microsoft. SQL/serwery/bazy danych | wlg_queued_queries | Zakolejkowane zapytania grupy obciążenia | 
| Microsoft. SQL/serwery/bazy danych | active_queries | Aktywne zapytania | 
| Microsoft. SQL/serwery/bazy danych | queued_queries | Zakolejkowane zapytania | 
| Microsoft. SQL/serwery/bazy danych | wlg_active_queries_timeouts | Limity czasu zapytania grupy obciążeń | 
| Microsoft. SQL/serwery/bazy danych | wlg_queued_queries_timeouts | Przekroczono limit czasu zapytania w kolejce dla grupy obciążeń | 
| Microsoft. SQL/serwery/bazy danych | wlg_effective_min_resource_percent | Efektywny minimalny procent zasobów | 
| Microsoft. SQL/serwery/bazy danych | wlg_effective_cap_resource_percent | Procent zasobów obowiązujących dla limitu | 
| Microsoft. SQL/serwery/elasticPools | cpu_percent | Procent użycia procesora CPU | 
| Microsoft. SQL/serwery/elasticPools | physical_data_read_percent | Procent użycia operacji we/wy na danych | 
| Microsoft. SQL/serwery/elasticPools | log_write_percent | Procent operacji we/wy dziennika | 
| Microsoft. SQL/serwery/elasticPools | dtu_consumption_percent | Procent użycia jednostek DTU | 
| Microsoft. SQL/serwery/elasticPools | workers_percent | Procent pracowników | 
| Microsoft. SQL/serwery/elasticPools | sessions_percent | Procent sesji | 
| Microsoft. SQL/serwery/elasticPools | eDTU_used | użyta wartość eDTU | 
| Microsoft. SQL/serwery/elasticPools | xtp_storage_percent | Procent magazynu OLTP In-Memory | 
| Microsoft. SQL/serwery | dtu_consumption_percent | Procent użycia jednostek DTU | 
| Microsoft. SQL/serwery | dtu_used | Używane jednostki DTU | 
| Microsoft. SQL/managedInstances | avg_cpu_percent | Średni procent procesora CPU | 

