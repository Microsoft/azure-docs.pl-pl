---
title: Metryki platformy Azure Monitor eksportowane za pośrednictwem ustawień diagnostycznych
description: Lista metryk dostępnych dla każdego typu zasobu z Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 91fc2c4525ee622064520b0098087d54158bbe9e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680690"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Metryki platformy Azure Monitor eksportowane za pośrednictwem ustawień diagnostycznych

Azure Monitor domyślnie udostępnia [metryki platformy](data-platform-metrics.md) bez konfiguracji. Zapewnia kilka sposobów współpracy z metrykami platformy, w tym wykresów w portalu, uzyskiwania dostępu do nich za pośrednictwem interfejsu API REST lub wykonywania zapytań przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Zobacz [metryki — obsługa](metrics-supported.md) pełnej listy metryk platformy dostępnych obecnie w ramach skonsolidowanego potoku metryki Azure monitor. Aby wykonać zapytanie o i uzyskać dostęp do tych metryk, użyj [wersji interfejsu api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Inne metryki mogą być dostępne w portalu lub przy użyciu starszych interfejsów API.

Metryki platformy z potoku usługi Azure monitor można wyeksportować do innych lokalizacji na jeden z dwóch sposobów.
1. Wysyłanie do Log Analytics, Event Hubs lub Azure Storage przy użyciu [ustawień diagnostycznych](diagnostic-settings.md) .
2. Korzystanie z [interfejsu API REST metryk](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Ze względu na złożonego w zapleczu Azure Monitor nie wszystkie metryki są eksportowane przy użyciu ustawień diagnostycznych. W poniższej tabeli wymieniono i nie można eksportować za pomocą ustawień diagnostycznych.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Zmiana zachowania dla wartości NULL i zero 
 
W przypadku metryk platformy, które można wyeksportować za pomocą ustawień diagnostycznych, istnieje kilka metryk, dla których Azure Monitor interpretuje wartość "0s" jako "null". Spowodowało to kilka pomyłek między rzeczywistym "0s" (emitowanym przez zasób) i interpretowaniem "0s" (wartości null). Wkrótce zostanie nadana zmiana, a metryki platformy wyeksportowane za pośrednictwem ustawień diagnostycznych nie będą już eksportować elementu "0s", chyba że zostanie on rzeczywiście wyemitowany przez bazowego zasobu. 

> [!CAUTION]
> Zmiana w zachowaniu opisanym powyżej została zaplanowana na 1 czerwca 2020.

Uwaga:

1.  W przypadku usunięcia grupy zasobów lub określonego zasobu dane metryk z zasobów, których to dotyczy, nie będą już wysyłane do ustawień diagnostycznych. Oznacza to, że nie będzie już wyświetlany w Event Hubs, konta magazynu i Log Analytics obszary robocze.
2.  To ulepszenie będzie dostępne we wszystkich chmurach publicznych i prywatnych.
3.  Ta zmiana nie wpłynie na zachowanie żadnego z następujących środowisk: 
   - Dzienniki zasobów platformy wyeksportowane za pomocą ustawień diagnostycznych
   - Wykresy metryk w Eksplorator metryk
   - Alerty dotyczące metryk platformy
 
## <a name="metrics-exportable-table"></a>Tabela z możliwością eksportowania metryk 

Tabela zawiera następujące kolumny. 
- Eksportować za pomocą ustawień diagnostycznych? 
- Wpływ na wartość NULL/0 
- ResourceType 
- Metric 
- MetricDisplayName
- Jednostka 
- Agregacja


> [!NOTE]
> Poniższa tabela może mieć poziomy pasek przewijania u dołu. Jeśli uważasz, że brakuje informacji, sprawdź, czy pasek przewijania jest widoczny w lewo.  


| Eksportować za pomocą ustawień diagnostycznych?  | Już emituje wartości NULL |  ResourceType  |  Metric  |  MetricDisplayName  |  Jednostka  |  Agregacja | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  CleanerCurrentPrice  |  Pamięć: bieżąca cena oczyszczarki  |  Liczba  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  CleanerMemoryNonshrinkable  |  Pamięć: pamięć oczyszczarki niemożliwa do zmniejszenia  |  Bajty  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  CleanerMemoryShrinkable  |  Pamięć: zmniejszanie ilości pamięci czyszczącej  |  Bajty  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  CommandPoolBusyThreads  |  Wątki: zajęte wątki w puli poleceń  |  Liczba  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  CommandPoolIdleThreads  |  Wątki: bezczynne wątki w puli poleceń  |  Liczba  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  CommandPoolJobQueueLength  |  Długość kolejki zadań puli poleceń  |  Liczba  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  Wartości CurrentConnections  |  Połączenie: bieżące połączenia  |  Liczba  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  CurrentUserSessions  |  Bieżące sesje użytkowników  |  Liczba  |  Średnia | 
| Tak * * * *  | Nie |  Microsoft. AnalysisServices/serwery  |  LongParsingBusyThreads  |  Wątki: zajęte wątki o długotrwałej analizie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  LongParsingIdleThreads  |  Wątki: długotrwałe wątki bezczynne analizy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  LongParsingJobQueueLength  |  Wątki: Długość kolejki zadań o długotrwałej analizie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  mashup_engine_memory_metric  |  Pamięć aparatu M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  mashup_engine_private_bytes_metric  |  Liczba prywatnych bajtów aparatu M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  mashup_engine_qpu_metric  |  M QPU aparatu  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  mashup_engine_virtual_bytes_metric  |  Liczba bajtów wirtualnych aparatu M  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  memory_metric  |  Memory (Pamięć)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  memory_thrashing_metric  |  Przeładowywanie pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  MemoryLimitHard  |  Pamięć: sztywny limit pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  MemoryLimitHigh  |  Pamięć: limit pamięci jest wysoki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  MemoryLimitLow  |  Pamięć: limit pamięci — niski  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  MemoryLimitVertiPaq  |  Pamięć: limit pamięci — tryb VertiPaq  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  MemoryUsage  |  Pamięć: użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  private_bytes_metric  |  Bajty prywatne  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ProcessingPoolBusyIOJobThreads  |  Wątki: wątki zadań we/wy puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ProcessingPoolBusyNonIOThreads  |  Wątki: zajęte wątki innych niż we/wy puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ProcessingPoolIdleIOJobThreads  |  Wątki: wątki zadań we/wy puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ProcessingPoolIdleNonIOThreads  |  Wątki: bezczynne wątki inne niż we/wy puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ProcessingPoolIOJobQueueLength  |  Wątki: Długość kolejki zadań we/wy puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ProcessingPoolJobQueueLength  |  Długość kolejki zadań puli przetwarzania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  qpu_metric  |  QPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  QueryPoolBusyThreads  |  Wątki zajęte w puli zapytań  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  QueryPoolIdleThreads  |  Wątki: bezczynne wątki puli zapytań  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  QueryPoolJobQueueLength  |  Wątki: Długość kolejki zadań puli zapytań  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  Limit przydziału  |  Pamięć: limit przydziału  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  QuotaBlocked  |  Pamięć: zablokowany limit przydziału  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  RowsConvertedPerSec  |  Przetwarzanie: przekonwertowane wiersze na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  RowsReadPerSec  |  Przetwarzanie: odczytane wiersze na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  RowsWrittenPerSec  |  Przetwarzanie: liczba wierszy na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ShortParsingBusyThreads  |  Wątki: zajęte wątki z krótkim analizowaniem  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ShortParsingIdleThreads  |  Wątki: bezczynne wątki z krótkim analizowaniem  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  ShortParsingJobQueueLength  |  Wątki: Długość kolejki zadań o krótkiej analizie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  SuccessfullConnectionsPerSec  |  Udane połączenia na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  TotalConnectionFailures  |  Łączna liczba błędów połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  TotalConnectionRequests  |  Łączna liczba żądań połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  VertiPaqNonpaged  |  Pamięć: VertiPaq niestronicowana  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  VertiPaqPaged  |  Pamięć: stronicowana na stronie VertiPaq  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AnalysisServices/serwery  |  virtual_bytes_metric  |  Bajty wirtualne  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Czas trwania żądań wewnętrznej bazy danych  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft.ApiManagement/service  |  Pojemność  |  Pojemność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Czas trwania  |  Całkowity czas trwania żądań bramy  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Opuszczone zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Odrzucone zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Pomyślne zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Ograniczone zdarzenia EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Przekroczono limit czasu zdarzeń EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Rozmiar zdarzeń EventHub  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Łączna liczba zdarzeń EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Niepowodzenie zdarzeń EventHub  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Nieudane żądania bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Inne żądania bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  Żądania successfulrequests  |  Pomyślne żądania bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Łączna liczba żądań bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Nieautoryzowane żądania bramy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppCpuUsagePercentage  |  Procent użycia procesora aplikacji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppMemoryCommitted  |  Przypisana pamięć aplikacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppMemoryMax  |  Maksymalna ilość pamięci aplikacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  AppMemoryUsed  |  Użyta pamięć aplikacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  GCPauseTotalCount  |  Liczba wstrzymań GC  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  GCPauseTotalTime  |  Całkowity czas wstrzymania odzyskiwania pamięci  |  )  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  MaxOldGenMemoryPoolBytes  |  Maksymalna dostępna wartość starego rozmiaru danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  OldGenMemoryPoolBytes  |  Stary rozmiar danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  OldGenPromotedBytes  |  Podwyższ poziom do starego rozmiaru danych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  SystemCpuUsagePercentage  |  Procent użycia procesora systemu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatErrorCount  |  Błąd globalny Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatReceivedBytes  |  Całkowita liczba odebranych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatRequestMaxTime  |  Maksymalny czas żądania Tomcat  |  )  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatRequestTotalCount  |  Łączna liczba żądań Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatRequestTotalTime  |  Łączny czas żądania Tomcat  |  )  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatResponseAvgTime  |  Średni czas żądania Tomcat  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSentBytes  |  Całkowita liczba wysłanych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionActiveCurrentCount  |  Liczba aktywności sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionActiveMaxCount  |  Maksymalna liczba aktywnych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionAliveMaxTime  |  Maksymalny czas aktywności sesji Tomcat  |  )  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionCreatedCount  |  Liczba utworzonych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionExpiredCount  |  Liczba wygasłych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  TomcatSessionRejectedCount  |  Liczba odrzuconych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. AppPlatform/Sprężyna  |  YoungGenPromotedBytes  |  Podwyższ poziom do rozmiaru danych dla małych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Automation/automationAccounts  |  TotalJob  |  Łączna liczba zadań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Łączna liczba przebiegów wdrażania aktualizacji maszyny  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Łączna liczba przebiegów wdrażania aktualizacji  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  CoreCount  |  Liczba dedykowanych rdzeni  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  CreatingNodeCount  |  Tworzenie liczby węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  IdleNodeCount  |  Liczba węzłów bezczynności  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Zdarzenia ukończenia usuwania zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  JobDeleteStartEvent  |  Zdarzenia rozpoczęcia usuwania zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  JobDisableCompleteEvent  |  Zdarzenia ukończenia wyłączania zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  JobDisableStartEvent  |  Wyłącz zdarzenia uruchamiania dla zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  JobStartEvent  |  Zdarzenia uruchamiania zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Zdarzenia ukończenia kończenia zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  JobTerminateStartEvent  |  Zdarzenia rozpoczęcia zakończenia zadania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  LeavingPoolNodeCount  |  Opuszczanie liczby węzłów puli  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  LowPriorityCoreCount  |  LowPriority rdzeń  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  OfflineNodeCount  |  Liczba węzłów w trybie offline  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  PoolCreateEvent  |  Zdarzenia tworzenia puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Zdarzenia ukończenia usuwania puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  PoolDeleteStartEvent  |  Zdarzenia uruchamiania usuwania puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Zdarzenia ukończenia zmiany rozmiaru puli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  PoolResizeStartEvent  |  Zdarzenia rozpoczęcia zmiany rozmiaru puli  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  PreemptedNodeCount  |  Liczba przeniesiona węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  RebootingNodeCount  |  Ponowny rozruch liczby węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  ReimagingNodeCount  |  Liczba węzłów regraficznych  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  RunningNodeCount  |  Liczba uruchomionych węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  StartingNodeCount  |  Początkowa liczba węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Uruchamianie zadania nie powiodło się liczba węzłów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  TaskCompleteEvent  |  Zdarzenia ukończenia zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  TaskFailEvent  |  Zdarzenia błędów zadań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batch/batchAccounts  |  TaskStartEvent  |  Zdarzenia uruchamiania zadania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Liczba węzłów o niskim priorytecie  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  TotalNodeCount  |  Liczba dedykowanych węzłów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  UnusableNodeCount  |  Liczba węzłów, których nie można użyć  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Oczekiwanie na liczbę węzłów zadania uruchamiania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Aktywne rdzenie  |  Aktywne rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Aktywne węzły  |  Aktywne węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Rdzenie bezczynne  |  Rdzenie bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Węzły bezczynne  |  Węzły bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Ukończono zadanie  |  Ukończono zadanie  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Przesłane zadanie  |  Przesłane zadanie  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Opuszczanie rdzeni  |  Opuszczanie rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Opuszczanie węzłów  |  Opuszczanie węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Występujące rdzenie  |  Występujące rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Zastępujące węzły  |  Zastępujące węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Procent wykorzystania przydziałów  |  Procent wykorzystania przydziałów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Łączna liczba rdzeni  |  Łączna liczba rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Łączna liczba węzłów  |  Łączna liczba węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Rdzenie, których nie można używać  |  Rdzenie, których nie można używać  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Batchai Job/obszary robocze  |  Węzły niezdatne do użytku  |  Węzły niezdatne do użytku  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ConnectionAccepted  |  Zaakceptowane połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ConnectionActive  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ConnectionHandled  |  Obsłużone połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  CpuUsagePercentageInDouble  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  IOReadBytes  |  Bajty odczytu we/wy  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  IOWriteBytes  |  Bajty zapisu we/wy  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  MemoryLimit  |  Limit pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  MemoryUsage  |  Użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Procent użycia pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  PendingTransactions  |  Oczekujące transakcje  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ProcessedBlocks  |  Przetworzone bloki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  ProcessedTransactions  |  Przetworzone transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  QueuedTransactions  |  Transakcje w kolejce  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  RequestHandled  |  Obsłużone żądania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. łańcucha bloków/blockchainMembers  |  StorageUsage  |  Użycie magazynu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits  |  Trafienia w pamięci podręcznej  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits0  |  Trafienia pamięci podręcznej (fragmentu 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits1  |  Trafienia pamięci podręcznej (fragmentu 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits2  |  Trafienia pamięci podręcznej (fragmentu 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits3  |  Trafienia pamięci podręcznej (fragmentu 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits4  |  Trafienia pamięci podręcznej (fragmentu 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits5  |  Trafienia pamięci podręcznej (fragmentu 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits6  |  Trafienia pamięci podręcznej (fragmentu 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits7  |  Trafienia pamięci podręcznej (fragmentu 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits8  |  Trafienia pamięci podręcznej (fragmentu 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachehits9  |  Trafienia pamięci podręcznej (fragmentu 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheLatency  |  Mikrosekundy opóźnienia pamięci podręcznej (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses  |  Chybienia w pamięci podręcznej  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses0  |  Chybienia w pamięci podręcznej (fragmentu 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses1  |  Chybienia w pamięci podręcznej (fragmentu 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses2  |  Chybienia w pamięci podręcznej (fragmentu 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses3  |  Chybienia w pamięci podręcznej (fragmentu 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses4  |  Chybienia w pamięci podręcznej (fragmentu 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses5  |  Chybienia w pamięci podręcznej (fragmentu 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses6  |  Chybienia w pamięci podręcznej (fragmentu 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses7  |  Chybienia w pamięci podręcznej (fragmentu 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses8  |  Chybienia w pamięci podręcznej (fragmentu 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cachemisses9  |  Chybienia w pamięci podręcznej (fragmentu 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead  |  Odczyt pamięci podręcznej  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead0  |  Odczyt pamięci podręcznej (fragmentu 0)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead1  |  Odczyt pamięci podręcznej (fragmentu 1)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead2  |  Odczyt pamięci podręcznej (fragmentu 2)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead3  |  Odczyt pamięci podręcznej (fragmentu 3)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead4  |  Odczyt pamięci podręcznej (fragmentu 4)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead5  |  Odczyt pamięci podręcznej (fragmentu 5)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead6  |  Odczyt pamięci podręcznej (fragmentu 6)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead7  |  Odczyt pamięci podręcznej (fragmentu 7)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead8  |  Odczyt pamięci podręcznej (fragmentu 8)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheRead9  |  Odczyt pamięci podręcznej (fragmentu 9)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite  |  Zapis w pamięci podręcznej  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite0  |  Zapis pamięci podręcznej (fragmentu 0)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite1  |  Zapisywanie pamięci podręcznej (fragmentu 1)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite2  |  Zapisywanie pamięci podręcznej (fragmentu 2)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite3  |  Zapis w pamięci podręcznej (fragmentu 3)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite4  |  Zapis pamięci podręcznej (fragmentu 4)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite5  |  Zapis w pamięci podręcznej (fragmentu 5)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite6  |  Zapis w pamięci podręcznej (fragmentu 6)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite7  |  Zapis w pamięci podręcznej (fragmentu 7)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite8  |  Zapis w pamięci podręcznej (fragmentu 8)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  cacheWrite9  |  Zapis w pamięci podręcznej (fragmentu 9)  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients  |  Połączeni klienci  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients0  |  Połączeni klienci (fragmentu 0)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients1  |  Połączeni klienci (fragmentu 1)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients2  |  Połączeni klienci (fragmentu 2)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients3  |  Połączeni klienci (fragmentu 3)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients4  |  Połączeni klienci (fragmentu 4)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients5  |  Połączeni klienci (fragmentu 5)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients6  |  Połączeni klienci (fragmentu 6)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients7  |  Połączeni klienci (fragmentu 7)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients8  |  Połączeni klienci (fragmentu 8)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  connectedclients9  |  Połączeni klienci (fragmentu 9)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  błędy  |  Errors  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys  |  Wykluczone klucze  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys0  |  Wykluczone klucze (fragmentu 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys1  |  Wykluczone klucze (fragmentu 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys2  |  Wykluczone klucze (fragmentu 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys3  |  Wykluczone klucze (fragmentu 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys4  |  Wykluczone klucze (fragmentu 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys5  |  Wykluczone klucze (fragmentu 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys6  |  Wykluczone klucze (fragmentu 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys7  |  Wykluczone klucze (fragmentu 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys8  |  Wykluczone klucze (fragmentu 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  evictedkeys9  |  Wykluczone klucze (fragmentu 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys  |  Wygasłe klucze  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys0  |  Wygasłe klucze (fragmentu 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys1  |  Wygasłe klucze (fragmentu 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys2  |  Wygasłe klucze (fragmentu 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys3  |  Wygasłe klucze (fragmentu 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys4  |  Wygasłe klucze (fragmentu 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys5  |  Wygasłe klucze (fragmentu 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys6  |  Wygasłe klucze (fragmentu 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys7  |  Wygasłe klucze (fragmentu 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys8  |  Wygasłe klucze (fragmentu 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  expiredkeys9  |  Wygasłe klucze (fragmentu 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  GetCommands  |  Pobrania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands0  |  Pobiera (fragmentu 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands1  |  Pobiera (fragmentu 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands2  |  Pobiera (fragmentu 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands3  |  Pobiera (fragmentu 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands4  |  Pobiera (fragmentu 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands5  |  Pobiera (fragmentu 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands6  |  Pobiera (fragmentu 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands7  |  Pobiera (fragmentu 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands8  |  Pobiera (fragmentu 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  getcommands9  |  Pobiera (fragmentu 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond  |  Liczba operacji na sekundę  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond0  |  Operacje na sekundę (fragmentu 0)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond1  |  Operacje na sekundę (fragmentu 1)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond2  |  Operacje na sekundę (fragmentu 2)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond3  |  Operacje na sekundę (fragmentu 3)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond4  |  Operacje na sekundę (fragmentu 4)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond5  |  Operacje na sekundę (fragmentu 5)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond6  |  Operacje na sekundę (fragmentu 6)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond7  |  Operacje na sekundę (fragmentu 7)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond8  |  Operacje na sekundę (fragmentu 8)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  operationsPerSecond9  |  Operacje na sekundę (fragmentu 9)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime  |  Procesor CPU  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime0  |  Procesor CPU (fragmentu 0)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime1  |  Procesor CPU (fragmentu 1)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime2  |  Procesor CPU (fragmentu 2)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime3  |  Procesor CPU (fragmentu 3)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime4  |  Procesor CPU (fragmentu 4)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime5  |  Procesor CPU (fragmentu 5)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime6  |  Procesor CPU (fragmentu 6)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime7  |  Procesor CPU (fragmentu 7)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime8  |  Procesor CPU (fragmentu 8)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  percentProcessorTime9  |  Procesor CPU (fragmentu 9)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad  |  Obciążenie serwera  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad0  |  Ładowanie serwera (fragmentu 0)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad1  |  Ładowanie serwera (fragmentu 1)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad2  |  Ładowanie serwera (fragmentu 2)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad3  |  Ładowanie serwera (fragmentu 3)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad4  |  Ładowanie serwera (fragmentu 4)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad5  |  Ładowanie serwera (fragmentu 5)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad6  |  Ładowanie serwera (fragmentu 6)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad7  |  Ładowanie serwera (fragmentu 7)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad8  |  Ładowanie serwera (fragmentu 8)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  serverLoad9  |  Ładowanie serwera (fragmentu 9)  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  SetCommands  |  Zestawy  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands0  |  Zestawy (fragmentu 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands1  |  Zestawy (fragmentu 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands2  |  Zestawy (fragmentu 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands3  |  Zestawy (fragmentu 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands4  |  Zestawy (fragmentu 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands5  |  Zestawy (fragmentu 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands6  |  Zestawy (fragmentu 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands7  |  Zestawy (fragmentu 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands8  |  Zestawy (fragmentu 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  setcommands9  |  Zestawy (fragmentu 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed  |  Łączna liczba operacji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed0  |  Łączna liczba operacji (fragmentu 0)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed1  |  Łączna liczba operacji (fragmentu 1)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed2  |  Łączna liczba operacji (fragmentu 2)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed3  |  Łączna liczba operacji (fragmentu 3)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed4  |  Łączna liczba operacji (fragmentu 4)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed5  |  Łączna liczba operacji (fragmentu 5)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed6  |  Łączna liczba operacji (fragmentu 6)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed7  |  Łączna liczba operacji (fragmentu 7)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed8  |  Łączna liczba operacji (fragmentu 8)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalcommandsprocessed9  |  Łączna liczba operacji (fragmentu 9)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys  |  Łączna liczba kluczy  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys0  |  Łączna liczba kluczy (fragmentu 0)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys1  |  Łączna liczba kluczy (fragmentu 1)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys2  |  Łączna liczba kluczy (fragmentu 2)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys3  |  Łączna liczba kluczy (fragmentu 3)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys4  |  Łączna liczba kluczy (fragmentu 4)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys5  |  Łączna liczba kluczy (fragmentu 5)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys6  |  Łączna liczba kluczy (fragmentu 6)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys7  |  Łączna liczba kluczy (fragmentu 7)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys8  |  Łączna liczba kluczy (fragmentu 8)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  totalkeys9  |  Łączna liczba kluczy (fragmentu 9)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory  |  Użyta pamięć  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory0  |  Używana pamięć (fragmentu 0)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory1  |  Używana pamięć (fragmentu 1)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory2  |  Używana pamięć (fragmentu 2)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory3  |  Używana pamięć (fragmentu 3)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory4  |  Używana pamięć (fragmentu 4)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory5  |  Używana pamięć (fragmentu 5)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory6  |  Używana pamięć (fragmentu 6)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory7  |  Używana pamięć (fragmentu 7)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory8  |  Używana pamięć (fragmentu 8)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemory9  |  Używana pamięć (fragmentu 9)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemorypercentage  |  Procentowe użycie pamięci  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss  |  Używana pamięć RSS  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss0  |  Używana pamięć RSS (fragmentu 0)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss1  |  Używana pamięć RSS (fragmentu 1)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss2  |  Używana pamięć RSS (fragmentu 2)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss3  |  Używana pamięć RSS (fragmentu 3)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss4  |  Używana pamięć RSS (fragmentu 4)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss5  |  Używana pamięć RSS (fragmentu 5)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss6  |  Używana pamięć RSS (fragmentu 6)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss7  |  Używana pamięć RSS (fragmentu 7)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss8  |  Używana pamięć RSS (fragmentu 8)  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. cache/Redis  |  usedmemoryRss9  |  Używana pamięć RSS (fragmentu 9)  |  Bajty  |  Maksimum | 
| Nie  | Nie |  Microsoft. ClassicCompute/domainNames/gniazda/role  |  Bajty odczytu dysku/s  |  Odczyt dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/domainNames/gniazda/role  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicCompute/domainNames/gniazda/role  |  Bajty zapisu dysku/s  |  Zapis na dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/domainNames/gniazda/role  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/domainNames/gniazda/role  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/domainNames/gniazda/role  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/domainNames/gniazda/role  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicCompute/virtualMachines  |  Bajty odczytu dysku/s  |  Odczyt dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicCompute/virtualMachines  |  Bajty zapisu dysku/s  |  Zapis na dysku  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/virtualMachines  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/virtualMachines  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicCompute/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts  |  UsedCapacity  |  Używana pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Pojemność obiektu BLOB  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Liczba obiektów BLOB  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Liczba kontenerów obiektów BLOB  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Pojemność indeksu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/blobServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Pojemność plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Liczba plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Liczba udziałów plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Rozmiar przydziału udziału plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Liczba migawek udziału plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Rozmiar migawki udziału plików  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/fileServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Pojemność kolejki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Liczba kolejek  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Liczba komunikatów w kolejce  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/queueServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Pojemność tabeli  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Liczba tabel  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Liczba jednostek tabeli  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ClassicStorage/storageAccounts/tableServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  BlockedCalls  |  Zablokowane wywołania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  CharactersTrained  |  Znaki przeszkolone  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  CharactersTranslated  |  Znaki tłumaczone  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  ClientErrors  |  Błędy klienta  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  Dane  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  DataOut  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  Opóźnienie  |  Opóźnienie  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  Błędy servererrors  |  Błędy serwera  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  SpeechSessionDuration  |  Czas trwania sesji mowy  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  SuccessfulCalls  |  Pomyślne wywołania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  TotalCalls  |  Łączna liczba wywołań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  TotalErrors  |  Całkowita liczba błędów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  TotalTokenCalls  |  Łączna liczba wywołań tokenów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. CognitiveServices/konta  |  TotalTransactions  |  Łączna liczba transakcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Wykorzystane środki CPU  |  Wykorzystane środki CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Pozostałe kredyty procesora CPU  |  Pozostałe kredyty procesora CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysku danych (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu z dysku danych/s  |  Operacje odczytu z dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu na dysku danych/s  |  Bajty zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów przychodzących  |  Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć — wejście  |  Sieć jest rozliczana (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć łącznie  |  Sieć łącznie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Sieć — wyjście  |  Sieć — do rozliczania (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Całkowita liczba sieci  |  Całkowita liczba sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu z dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Głębokość kolejki systemu operacyjnego na dysku  |  Głębokość kolejki dysku systemu operacyjnego (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu na dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu na dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów wychodzących  |  Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Na dysk głębokość kolejki  |  Głębokość kolejki dysku danych (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty odczytu na dysk/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje odczytu na dysk/s  |  Operacje odczytu z dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Bajty zapisu na dysku/s  |  Bajty zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachines  |  Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium  |  Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Wykorzystane środki CPU  |  Wykorzystane środki CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Pozostałe kredyty procesora CPU  |  Pozostałe kredyty procesora CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysku danych (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu z dysku danych/s  |  Operacje odczytu z dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu na dysku danych/s  |  Bajty zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Maksymalny współczynnik tworzenia przepływów przychodzących  |  Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć — wejście  |  Sieć jest rozliczana (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć łącznie  |  Sieć łącznie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Sieć — wyjście  |  Sieć — do rozliczania (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Całkowita liczba sieci  |  Całkowita liczba sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu z dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Głębokość kolejki systemu operacyjnego na dysku  |  Głębokość kolejki dysku systemu operacyjnego (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu na dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu na dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Maksymalny współczynnik tworzenia przepływów wychodzących  |  Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Na dysk głębokość kolejki  |  Głębokość kolejki dysku danych (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty odczytu na dysk/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje odczytu na dysk/s  |  Operacje odczytu z dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Bajty zapisu na dysku/s  |  Bajty zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft.Compute/virtualMachineScaleSets  |  Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium  |  Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Wykorzystane środki CPU  |  Wykorzystane środki CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Pozostałe kredyty procesora CPU  |  Pozostałe kredyty procesora CPU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki dysku danych  |  Głębokość kolejki dysku danych (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku danych/s  |  Bajty odczytu dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu z dysku danych/s  |  Operacje odczytu z dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu na dysku danych/s  |  Bajty zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku danych/s  |  Operacje zapisu na dysku danych/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Przepływy przychodzące  |  Przepływy przychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów przychodzących  |  Maksymalny współczynnik tworzenia przepływów przychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Sieć — wejście  |  Sieć jest rozliczana (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Sieć łącznie  |  Sieć łącznie  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Sieć — wyjście  |  Sieć — do rozliczania (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Całkowita liczba sieci  |  Całkowita liczba sieci  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki dysku systemu operacyjnego  |  Głębokość kolejki dysku systemu operacyjnego (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu z dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Głębokość kolejki systemu operacyjnego na dysku  |  Głębokość kolejki dysku systemu operacyjnego (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu dysku systemu operacyjnego/s  |  Bajty odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu na dysku systemu operacyjnego/s  |  Operacje odczytu z dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu na dysku systemu operacyjnego/s  |  Bajty zapisu dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku systemu operacyjnego/s  |  Operacje zapisu na dysku systemu operacyjnego/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Przepływy wychodzące  |  Przepływy wychodzące  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Maksymalny współczynnik tworzenia przepływów wychodzących  |  Maksymalny współczynnik tworzenia przepływów wychodzących (wersja zapoznawcza)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Na dysk głębokość kolejki  |  Głębokość kolejki dysku danych (przestarzałe)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty odczytu na dysk/s  |  Bajty odczytu dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje odczytu na dysk/s  |  Operacje odczytu z dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Bajty zapisu na dysku/s  |  Bajty zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku danych/s (przestarzałe)  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Trafienie odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Trafienie odczytu pamięci podręcznej dysku danych Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Chybienia odczytu pamięci podręcznej dysku danych w warstwie Premium  |  Chybienia odczytu pamięci podręcznej dysku z danymi Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium  |  Trafienie odczytu pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. COMPUTE/virtualMachineScaleSets/virtualMachines  |  Brak odczytu pamięci podręcznej dysku systemu operacyjnego w warstwie Premium  |  Brak chybień w pamięci podręcznej systemu operacyjnego Premium (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerInstance/containerGroups  |  CpuUsage  |  Użycie procesora  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerInstance/containerGroups  |  MemoryUsage  |  Użycie pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Bajty odebrane przez sieć na sekundę  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Bajty przesyłane przez sieć na sekundę  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerRegistry/rejestry  |  RunDuration  |  Czas trwania przebiegu  |  )  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. ContainerRegistry/rejestry  |  SuccessfulPullCount  |  Pomyślna liczba ściągania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerRegistry/rejestry  |  SuccessfulPushCount  |  Liczba wypychanych zakończonych powodzeniem  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerRegistry/rejestry  |  TotalPullCount  |  Łączna liczba ściągania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. ContainerRegistry/rejestry  |  TotalPushCount  |  Łączna liczba wypychanych  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Łączna liczba dostępnych rdzeni procesora CPU w zarządzanym klastrze  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Łączna ilość dostępnej pamięci w zarządzanym klastrze  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ContainerService/managedClusters  |  kube_node_status_condition  |  Stany różnych warunków węzła  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ContainerService/managedClusters  |  kube_pod_status_phase  |  Liczba etapów według fazy  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ContainerService/managedClusters  |  kube_pod_status_ready  |  Liczba zasobników w stanie gotowe  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  AvailableCapacity  |  Dostępna pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Przekazane bajty w chmurze (urządzenie)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Przekazane bajty w chmurze (udział)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Przepływność pobierania w chmurze  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Przepływność pobierania w chmurze (udział)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Przepływność przekazywania w chmurze  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Przepływność przekazywania w chmurze (udział)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Obliczenia brzegowe — użycie pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Obliczenia brzegowe — procentowy procesor CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Przepływność odczytu (Sieć)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Przepływność zapisu (Sieć)  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataBoxEdge/dataBoxEdgeDevices  |  Łączna pojemność  |  Całkowita pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataFactory/datafactors  |  FailedRuns  |  Nieudane uruchomienia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/datafactors  |  SuccessfulRuns  |  Pomyślne uruchomienia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  ActivityCancelledRuns  |  Metryki uruchomień działań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  ActivityFailedRuns  |  Metryki uruchamiania działań zakończonych niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  ActivitySucceededRuns  |  Metryki uruchamiania działań zakończonych powodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  FactorySizeInGbUnits  |  Łączny rozmiar fabryki (jednostka GB)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  IntegrationRuntimeAvailableMemory  |  Dostępna pamięć środowiska Integration Runtime  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  IntegrationRuntimeAverageTaskPickupDelay  |  Czas trwania kolejki Integration Runtime  |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  IntegrationRuntimeCpuPercentage  |  Użycie procesora Integration Runtime  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  IntegrationRuntimeQueueLength  |  Długość kolejki środowiska Integration Runtime  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  MaxAllowedFactorySizeInGbUnits  |  Maksymalny dozwolony rozmiar fabryki (jednostka GB)  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  MaxAllowedResourceCount  |  Maksymalna dozwolona liczba jednostek  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  PipelineCancelledRuns  |  Metryki uruchomień potoku  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  PipelineFailedRuns  |  Metryki uruchomionych potoków zakończonych niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  PipelineSucceededRuns  |  Metryki uruchamiania potoków zakończonych powodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  ResourceCount  |  Łączna liczba jednostek  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  TriggerCancelledRuns  |  Anulowane metryki uruchamiania wyzwalacza  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  TriggerFailedRuns  |  Metryki uruchomienia wyzwalacza zakończonego niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataFactory/fabryki  |  TriggerSucceededRuns  |  Wyzwalacze uruchomienia wyzwalają metryki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataLakeAnalytics/konta  |  JobAUEndedCancelled  |  Anulowano czas aktualizacji  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataLakeAnalytics/konta  |  JobAUEndedFailure  |  Czas niepowodzenia  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataLakeAnalytics/konta  |  JobAUEndedSuccess  |  Czas pomyślnej aktualizacji  |  Sekundy  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataLakeAnalytics/konta  |  JobEndedCancelled  |  Anulowane zadania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataLakeAnalytics/konta  |  JobEndedFailure  |  Zadania zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DataLakeAnalytics/konta  |  JobEndedSuccess  |  Zadania zakończone powodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. kontach datalakestore/konta  |  Odczyt DataReady  |  Odczytane dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. kontach datalakestore/konta  |  Zapisywana  |  Zapisywane dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. kontach datalakestore/konta  |  ReadRequests  |  Żądania odczytu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. kontach datalakestore/konta  |  TotalStorage  |  Łączny rozmiar magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. kontach datalakestore/konta  |  WriteRequests  |  Żądania zapisu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  io_consumption_percent  |  Procent operacji we/wy  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  seconds_behind_master  |  Opóźnienie replikacji w sekundach  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  serverlog_storage_limit  |  Limit magazynowania dziennika serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  serverlog_storage_percent  |  Procent magazynu dzienników serwera  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  storage_limit  |  Limit magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  storage_percent  |  Procent magazynu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMariaDB/serwery  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  io_consumption_percent  |  Procent operacji we/wy  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  seconds_behind_master  |  Opóźnienie replikacji w sekundach  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  serverlog_storage_limit  |  Limit magazynowania dziennika serwera  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  serverlog_storage_percent  |  Procent magazynu dzienników serwera  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  storage_limit  |  Limit magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  storage_percent  |  Procent magazynu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DBforMySQL/serwery  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  backup_storage_used  |  Używany magazyn kopii zapasowych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  connections_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  io_consumption_percent  |  Procent operacji we/wy  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  pg_replica_log_delay_in_bytes  |  Maksymalne opóźnienie między replikami  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  pg_replica_log_delay_in_seconds  |  Zwłoka repliki  |  Sekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  serverlog_storage_limit  |  Limit magazynowania dziennika serwera  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  serverlog_storage_percent  |  Procent magazynu dzienników serwera  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  serverlog_storage_usage  |  Używany magazyn dzienników serwera  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  storage_limit  |  Limit magazynu  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  storage_percent  |  Procent magazynu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serwery  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  active_connections  |  Aktywne połączenia  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  Wejścia  |  Liczba operacji we/wy na sekundę  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  memory_percent  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  storage_percent  |  Procent magazynu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. DBforPostgreSQL/serversv2  |  storage_used  |  Używany magazyn  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/konto  |  digitaltwins. Telemetry. nodes  |  Symbol zastępczy telemetrii cyfrowego węzła bliźniaczych reprezentacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. Commands. wyjście. Abandon. Success  |  Porzucone komunikaty C2D  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. Commands. wyjście. Complete. Success  |  Zakończono dostarczanie komunikatów C2D  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. Commands. wyjście. Odrzuć. sukces  |  Odrzucone komunikaty C2D  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. Methods. Failure  |  Nieudane wywołania metody bezpośredniej  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. Methods. requestSize  |  Rozmiar żądania wywołań metody bezpośredniej  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. Methods. responseSize  |  Rozmiar odpowiedzi wywołań metody bezpośredniej  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. Methods. Success  |  Pomyślne wywołania metody bezpośredniej  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. splot. Read. Failure  |  Nieudane odczyty sznurów z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. splot. Read. size  |  Rozmiar odpowiedzi na odwrocie od zaplecza  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. splot. Read. Success  |  Pomyślne odczyty sznurów z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. splot. Update. Failure  |  Niepowodzenie aktualizacji bliźniaczych z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. splot. Update. size  |  Rozmiar aktualizacji przędzy od zaplecza  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2D. splot. Update. Success  |  Pomyślne aktualizacje bliźniaczych z zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  C2DMessagesExpired  |  Komunikaty C2D wygasły (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  komputerów  |  Metryki konfiguracji  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Devices/IotHubs  |  connectedDeviceCount  |  Podłączone urządzenia (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. wychodzące. wbudowane. Events  |  Routing: komunikaty dostarczane do komunikatów/zdarzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. wychodzące. eventHubs  |  Routing: komunikaty dostarczane do centrum zdarzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. wychodzące. serviceBusQueues  |  Routing: komunikaty dostarczone do kolejki Service Bus  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. wychodzące. serviceBusTopics  |  Routing: komunikaty dostarczane do Service Bus tematu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. ruch wychodzący. Storage  |  Routing: komunikaty dostarczane do magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. dane wyjściowe. Storage. blob  |  Routing: obiekty blob dostarczane do magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. ruch wychodzący. Storage. Bytes  |  Routing: dane dostarczane do magazynu  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. opóźnienie. wbudowane. Events  |  Routing: opóźnienie komunikatów dla komunikatów/zdarzeń  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. opóźnienie. eventHubs  |  Routing: opóźnienie komunikatu dla centrum zdarzeń  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. opóźnienie. serviceBusQueues  |  Routing: opóźnienie komunikatu dla kolejki Service Bus  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. opóźnienie. serviceBusTopics  |  Routing: opóźnienie komunikatu dla Service Bus tematu  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. endpoints. opóźnienie. Storage  |  Routing: opóźnienie komunikatu dla magazynu  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. dane telemetryczne. wychodzące. upuszczone  |  Routing: porzucone komunikaty telemetryczne   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. telemetrię. ruch wychodzący. Fallback  |  Routing: komunikaty dostarczane do powrotu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. Telemetria. ruch wychodzący. nieprawidłowe  |  Routing: komunikaty telemetryczne są niezgodne  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. dane telemetryczne. wychodzące. oddzielone  |  Routing: oddzielone komunikaty telemetryczne   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. telemetrię. ruch wychodzący. sukces  |  Routing: dostarczono komunikaty telemetryczne  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. telemetrię. allProtocol  |  Próby wysłania komunikatów telemetrycznych  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. telemetrię. sendThrottle  |  Liczba błędów ograniczania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. telemetrię. dane wejściowe. sukces  |  Wysłane komunikaty telemetryczne  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. splot. Read. Failure  |  Nieudane odczyty sznurów z urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. splot. Read. size  |  Rozmiar odpowiedzi dla sznurów odczytanych z urządzeń  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. splot. Read. Success  |  Pomyślne odczyty sznurów z urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. splot. Update. Failure  |  Niepowodzenie aktualizacji bliźniaczych z urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. splot. Update. size  |  Rozmiar aktualizacji bliźniaczych z urządzeń  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  D2C. splot. Update. Success  |  Pomyślne aktualizacje bliźniaczych urządzeń  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  dailyMessageQuotaUsed  |  Całkowita liczba użytych komunikatów  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  deviceDataUsage  |  Całkowite użycie danych urządzenia  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  deviceDataUsageV2  |  Całkowite użycie danych urządzenia (wersja zapoznawcza)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Devices. connectedDevices. allProtocol  |  Podłączone urządzenia (przestarzałe)   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Devices. totalDevices  |  Łączna liczba urządzeń (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  EventGridDeliveries  |  Dostawy Event Grid (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  EventGridLatency  |  Opóźnienie Event Grid (wersja zapoznawcza)  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. cancelJob. Failure  |  Nieudane anulowania zadań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. cancelJob. Success  |  Pomyślne anulowania zadań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  zadania. ukończone  |  Ukończone zadania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. createDirectMethodJob. Failure  |  Nie można utworzyć zadań wywołania metody  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. createDirectMethodJob. Success  |  Pomyślne utworzenie zadań wywołania metody  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. createTwinUpdateJob. Failure  |  Nie można utworzyć dwuosiowych zadań aktualizacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. createTwinUpdateJob. Success  |  Pomyślne utworzenie dwuosiowych zadań aktualizacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  zadania. Niepowodzenie  |  Zadania zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. listJobs. Failure  |  Wywołania zakończone niepowodzeniem do listy zadań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. listJobs. Success  |  Pomyślne wywołania do zadań na liście  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. queryJobs. Failure  |  Nieudane kwerendy zadań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  Jobs. queryJobs. Success  |  Pomyślne zapytania dotyczące zadań  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Devices/IotHubs  |  totalDeviceCount  |  Łączna liczba urządzeń (wersja zapoznawcza)  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  twinQueries. Failure  |  Niepowodzenie zapytań bliźniaczych  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  twinQueries.resultSize  |  Rozmiar wyniku zapytań bliźniaczych  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Devices/IotHubs  |  twinQueries. Success  |  Pomyślne zapytania bliźniaczy  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/provisioningServices  |  AttestationAttempts  |  Próby zaświadczania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/provisioningServices  |  DeviceAssignments  |  Przypisane urządzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Devices/provisioningServices  |  RegistrationAttempts  |  Próby rejestracji  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  AvailableStorage  |  Dostępny magazyn  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Zamknięcia połączeń Cassandra  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Opłaty za żądania Cassandra  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  CassandraRequests  |  Żądania Cassandra  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  Datausage  |  Użycie danych  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  DocumentCount  |  Liczba dokumentów  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  DocumentQuota  |  Przydział dokumentu  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  IndexUsage  |  Użycie indeksu  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  MetadataRequests  |  Żądania metadanych  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Opłata żądania Mongo  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequests  |  Żądania Mongo  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Częstotliwość żądania Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Mongo — wskaźnik żądania usunięcia  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Liczba żądań wstawienia Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Częstotliwość żądań zapytań Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Częstotliwość żądań aktualizacji Mongo  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Aprowizowana przepływność  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  ReplicationLatency  |  Opóźnienie replikacji poziomie P99  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. DocumentDB/databaseAccounts  |  Dostępność  |  Dostępność usługi  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. DocumentDB/databaseAccounts  |  TotalRequests  |  Łączna liczba żądań  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Łączna liczba jednostek żądania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  FailureCount  |  Liczba niepowodzeń  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  SuccessCount  |  Liczba powodzeń  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  SuccessLatency  |  Opóźnienie sukcesu  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. EnterpriseKnowledgeGraph/usługi  |  TransactionCount  |  Liczba transakcji  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  DeadLetteredCount  |  Zdarzenia utraconych wiadomości  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventGrid/domeny  |  DeliveryAttemptFailCount  |  Zdarzenia zakończonych niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  DeliverySuccessCount  |  Dostarczone zdarzenia  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventGrid/domeny  |  DestinationProcessingDurationInMs  |  Czas przetwarzania docelowego  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  DroppedEventCount  |  Opuszczone zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  MatchedEventCount  |  Dopasowane zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  PublishFailCount  |  Publikowanie zdarzeń zakończonych niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  PublishSuccessCount  |  Zdarzenia opublikowane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/domeny  |  PublishSuccessLatencyInMs  |  Czas oczekiwania na pomyślne publikowanie  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Zdarzenia utraconych wiadomości  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Zdarzenia zakończonych niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Dostarczone zdarzenia  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Czas przetwarzania docelowego  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/eventSubscriptions  |  DroppedEventCount  |  Opuszczone zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/eventSubscriptions  |  MatchedEventCount  |  Dopasowane zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  PublishFailCount  |  Publikowanie zdarzeń zakończonych niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessCount  |  Zdarzenia opublikowane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Czas oczekiwania na pomyślne publikowanie  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/extensionTopics  |  UnmatchedEventCount  |  Niedopasowane zdarzenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  PublishFailCount  |  Publikowanie zdarzeń zakończonych niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  PublishSuccessCount  |  Zdarzenia opublikowane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  PublishSuccessLatencyInMs  |  Czas oczekiwania na pomyślne publikowanie  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventGrid/tematy  |  UnmatchedEventCount  |  Niedopasowane zdarzenia  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  AvailableMemory  |  Dostępna pamięć  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  CaptureBacklog  |  Zaległości przechwytywania.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  CapturedBytes  |  Przechwycone bajty.  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  CapturedMessages  |  Przechwycone komunikaty.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Połączenia connectionsclosed  |  Połączenia zamknięte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Połączenia connectionsopened  |  Otwarte połączenia.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Procesor CPU  |  Procesor CPU  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Bajty incomingbytes  |  Bajty przychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Komunikaty incomingmessages  |  Komunikaty przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Żądania incomingrequests  |  Żądania przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Bajty outgoingbytes  |  Bajty wychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/klastry  |  Komunikaty outgoingmessages  |  Komunikaty wychodzące  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Błędy quotaexceedederrors  |  Błędy przekroczenia limitu przydziału.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Błędy servererrors  |  Błędy serwera.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Żądania successfulrequests  |  Żądania zakończone powodzeniem  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  ThrottledRequests  |  Żądania z ograniczeniami.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/klastry  |  Błędy usererrors  |  Błędy użytkownika.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  CaptureBacklog  |  Zaległości przechwytywania.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  CapturedBytes  |  Przechwycone bajty.  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  CapturedMessages  |  Przechwycone komunikaty.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Połączenia connectionsclosed  |  Połączenia zamknięte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Połączenia connectionsopened  |  Otwarte połączenia.  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHABL  |  Archiwum komunikatów zaległości (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHAMBS  |  Przepływność komunikatów archiwalnych (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHAMSGS  |  Komunikaty archiwalne (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHINBYTES  |  Przychodzące bajty (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHINMBS  |  Przychodzące bajty (przestarzałe) (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHINMSGS  |  Komunikaty przychodzące (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHOUTBYTES  |  Bajty wychodzące (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHOUTMBS  |  Bajty wychodzące (przestarzałe) (przestarzałe)  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  EHOUTMSGS  |  Wiadomości wychodzące (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  FAILREQ  |  Nieudane żądania (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  Bajty incomingbytes  |  Bajty przychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  Komunikaty incomingmessages  |  Komunikaty przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  Żądania incomingrequests  |  Żądania przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  INMSGS  |  Komunikaty przychodzące (przestarzałe) (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  INREQS  |  Żądania przychodzące (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  MIĘDZY  |  Wewnętrzne błędy serwera (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  MISCERR  |  Inne błędy (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  Bajty outgoingbytes  |  Bajty wychodzące.  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  Komunikaty outgoingmessages  |  Komunikaty wychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  OUTMSGS  |  Wiadomości wychodzące (przestarzałe) (przestarzałe)  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Błędy quotaexceedederrors  |  Błędy przekroczenia limitu przydziału.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Błędy servererrors  |  Błędy serwera.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Rozmiar  |  Rozmiar  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Żądania successfulrequests  |  Żądania zakończone powodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  SUCCREQ  |  Żądania zakończone powodzeniem (przestarzałe)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. EventHub/przestrzenie nazw  |  SVRBSY  |  Błędy zajęte przez serwer (przestarzałe)  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  ThrottledRequests  |  Żądania z ograniczeniami.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. EventHub/przestrzenie nazw  |  Błędy usererrors  |  Błędy użytkownika.  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. HDInsight/Klastry  |  CategorizedGatewayRequests  |  Skategoryzowane żądania bramy  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. HDInsight/Klastry  |  GatewayRequests  |  Żądania bramy  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. HDInsight/Klastry  |  NumActiveWorkers  |  Liczba aktywnych procesów roboczych  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. HDInsight/Klastry  |  ScalingRequests  |  Skalowanie żądań  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Insights/AutoscaleSettings  |  MetricThreshold  |  Próg metryki  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/AutoscaleSettings  |  ObservedCapacity  |  Zaobserwowana pojemność  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/AutoscaleSettings  |  ObservedMetricValue  |  Obserwowana wartość metryki  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Zainicjowane akcje skalowania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  availabilityResults/availabilityPercentage  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  availabilityResults/liczba  |  Testy dostępności  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  availabilityResults/czas trwania  |  Czas trwania testu dostępności  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/networkDuration  |  Czas połączenia sieciowego ładowania strony  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/processingDuration  |  Czas przetwarzania klienta  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/receiveDuration  |  Czas odpowiedzi na odebranie  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/sendDuration  |  Czas żądania wysłania  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  browserTimings/totalDuration  |  Czas ładowania strony w przeglądarce  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  zależności/liczba  |  Wywołania zależności  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  zależności/czas trwania  |  Czas trwania zależności  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  zależności/niepowodzenie  |  Błędy wywołań zależności  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  wyjątki/przeglądarka  |  Wyjątki przeglądarki  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  wyjątki/liczba  |  Wyjątki  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  wyjątki/serwer  |  Wyjątki serwera  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  pageViews/liczba  |  Wyświetlenia stron  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  pageViews/czas trwania  |  Czas ładowania widoku strony  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  Liczniki wydajności/exceptionsPerSecond  |  Częstotliwość wyjątków  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/memoryAvailableBytes  |  Dostępna pamięć  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processCpuPercentage  |  Procesor CPU procesu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processIOBytesPerSecond  |  Szybkość operacji we/wy procesu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processorCpuPercentage  |  Czas procesora  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/processPrivateBytes  |  Prywatne bajty procesu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  Liczniki wydajności/requestExecutionTime  |  Czas wykonywania żądania HTTP  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  Liczniki wydajności/requestsInQueue  |  Żądania HTTP w kolejce aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  Liczniki wydajności/requestsPerSecond  |  Częstotliwość żądań HTTP  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  Liczba żądań na sekundę  |  Żądania serwera  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft. Insights/składniki  |  żądania/czas trwania  |  Czas odpowiedzi serwera  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  żądania/niepowodzenie  |  Żądania zakończone niepowodzeniem  |  Liczba  |  Liczba | 
| Nie  | Nie |  Microsoft. Insights/składniki  |  żądania/częstotliwość  |  Liczba żądań serwera  |  CountPerSecond  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Insights/składniki  |  ślady/liczba  |  Ślady  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft./magazyny kluczy  |  ServiceApiHit  |  Całkowita liczba trafień interfejsu API usługi  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft./magazyny kluczy  |  ServiceApiLatency  |  Ogólne opóźnienie interfejsu API usługi  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft./magazyny kluczy  |  ServiceApiResult  |  Łączna liczba wyników interfejsu API usługi  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  CacheUtilization  |  Użycie pamięci podręcznej  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  ContinuousExportMaxLatenessMinutes  |  Maksymalna liczba minut opóźnienia eksportu  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  ContinuousExportNumOfRecordsExported  |  Eksport ciągły — liczba eksportowanych rekordów  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  ContinuousExportPendingCount  |  Liczba oczekujących eksportu ciągłego  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  ContinuousExportResult  |  Wynik eksportu ciągłego  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  Procesor CPU  |  Procesor CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  EventsProcessedForEventHubs  |  Zdarzenia przetwarzane (dla centrów zdarzeń/IoT)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  ExportUtilization  |  Użycie eksportu  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  IngestionLatencyInSeconds  |  Opóźnienie pozyskiwania (w sekundach)  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  IngestionResult  |  Wynik pozyskiwania  |  Liczba  |  Liczba | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  IngestionUtilization  |  Wykorzystanie pozyskiwania  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  IngestionVolumeInMB  |  Wolumin pozyskiwania (w MB)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  Utrzymywani  |  Utrzymywanie aktywności  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  QueryDuration  |  Czas trwania zapytania  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  SteamingIngestRequestRate  |  Szybkość żądania pozyskiwania strumieniowego  |  Liczba  |  RateRequestsPerSecond | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  StreamingIngestDataRate  |  Szybkość danych pozyskiwania przesyłania strumieniowego  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  StreamingIngestDuration  |  Czas trwania pozyskiwania strumieniowego  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Kusto/klastry  |  StreamingIngestResults  |  Wynik pozyskiwania strumieniowego  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  ActionLatency  |  Opóźnienie akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Wykonane akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsFailed  |  Akcje zakończone niepowodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Pominięte akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsStarted  |  Akcje uruchomione   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Akcje zakończone powodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Opóźnienie sukcesu akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Zdarzenia ograniczające akcję  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Użycie pamięci przez łącznik dla środowisko usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Użycie procesora przez łącznik dla środowisko usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Użycie pamięci przez przepływ pracy dla środowisko usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Użycie procesora przepływu pracy dla środowisko usługi integracji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Procent niepowodzeń przebiegu  |  Wartość procentowa  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  RunLatency  |  Opóźnienie uruchamiania  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsCancelled  |  Przebiegi anulowane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsCompleted  |  Przebiegi zakończone  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsFailed  |  Przebiegi zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsStarted  |  Uruchomienia uruchomione  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Przebiegi zakończone powodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Uruchom zdarzenia uruchamiania z ograniczeniami  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Opóźnienie sukcesu przebiegu  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Uruchamianie zdarzeń z ograniczeniami  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Opóźnienie wyzwalania wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerLatency  |  Opóźnienie wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Ukończone wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersFailed  |  Wyzwalacze zakończone niepowodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersFired  |  Wyzwolone wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Pominięte wyzwalacze  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersStarted  |  Uruchomiono wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Wyzwalacze zakończone powodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Opóźnienie sukcesu wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Wyzwalaj zdarzenia ograniczające  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/przepływy pracy  |  ActionLatency  |  Opóźnienie akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  ActionsCompleted  |  Wykonane akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  ActionsFailed  |  Akcje zakończone niepowodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  ActionsSkipped  |  Pominięte akcje   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  ActionsStarted  |  Akcje uruchomione   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  ActionsSucceeded  |  Akcje zakończone powodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/przepływy pracy  |  ActionSuccessLatency  |  Opóźnienie sukcesu akcji   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  ActionThrottledEvents  |  Zdarzenia ograniczające akcję  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillableActionExecutions  |  Rozliczane wykonania akcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillableTriggerExecutions  |  Rozliczane wykonania wyzwalacza  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillingUsageNativeOperation  |  Użycie rozliczeń dla natywnych wykonań operacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillingUsageNativeOperation  |  Użycie rozliczeń dla natywnych wykonań operacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillingUsageStandardConnector  |  Użycie rozliczeń dla wykonywania łączników standardowych  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillingUsageStandardConnector  |  Użycie rozliczeń dla wykonywania łączników standardowych  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillingUsageStorageConsumption  |  Użycie rozliczeń dla wykonań zużycia magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  BillingUsageStorageConsumption  |  Użycie rozliczeń dla wykonań zużycia magazynu  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunFailurePercentage  |  Procent niepowodzeń przebiegu  |  Wartość procentowa  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/przepływy pracy  |  RunLatency  |  Opóźnienie uruchamiania  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunsCancelled  |  Przebiegi anulowane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunsCompleted  |  Przebiegi zakończone  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunsFailed  |  Przebiegi zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunsStarted  |  Uruchomienia uruchomione  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunsSucceeded  |  Przebiegi zakończone powodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunStartThrottledEvents  |  Uruchom zdarzenia uruchamiania z ograniczeniami  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/przepływy pracy  |  RunSuccessLatency  |  Opóźnienie sukcesu przebiegu  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  RunThrottledEvents  |  Uruchamianie zdarzeń z ograniczeniami  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TotalBillableExecutions  |  Łączna Liczba wykonań rozliczanych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/przepływy pracy  |  TriggerFireLatency  |  Opóźnienie wyzwalania wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Logic/przepływy pracy  |  TriggerLatency  |  Opóźnienie wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TriggersCompleted  |  Ukończone wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TriggersFailed  |  Wyzwalacze zakończone niepowodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TriggersFired  |  Wyzwolone wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TriggersSkipped  |  Pominięte wyzwalacze  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TriggersStarted  |  Uruchomiono wyzwalacze   |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TriggersSucceeded  |  Wyzwalacze zakończone powodzeniem   |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Logic/przepływy pracy  |  TriggerSuccessLatency  |  Opóźnienie sukcesu wyzwalacza   |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Logic/przepływy pracy  |  TriggerThrottledEvents  |  Wyzwalaj zdarzenia ograniczające  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Aktywne rdzenie  |  Aktywne rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Aktywne węzły  |  Aktywne węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Ukończone uruchomienia  |  Ukończone uruchomienia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Nieudane uruchomienia  |  Nieudane uruchomienia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Rdzenie bezczynne  |  Rdzenie bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Węzły bezczynne  |  Węzły bezczynne  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Opuszczanie rdzeni  |  Opuszczanie rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Opuszczanie węzłów  |  Opuszczanie węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Wdrażanie modelu nie powiodło się  |  Wdrażanie modelu nie powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Uruchomiono Wdrażanie modelu  |  Uruchomiono Wdrażanie modelu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Wdrażanie modelu powiodło się  |  Wdrażanie modelu powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Nie można zarejestrować modelu  |  Nie można zarejestrować modelu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Rejestrowanie modelu powiodło się  |  Rejestrowanie modelu powiodło się  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Występujące rdzenie  |  Występujące rdzenie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Zastępujące węzły  |  Zastępujące węzły  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Procent wykorzystania przydziałów  |  Procent wykorzystania przydziałów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Uruchomione uruchomienia  |  Uruchomione uruchomienia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Łączna liczba rdzeni  |  Łączna liczba rdzeni  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Łączna liczba węzłów  |  Łączna liczba węzłów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Rdzenie, których nie można używać  |  Rdzenie, których nie można używać  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. MachineLearningServices/obszary robocze  |  Węzły niezdatne do użytku  |  Węzły niezdatne do użytku  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Maps/konta  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. Maps/konta  |  Użycie  |  Użycie  |  Liczba  |  Liczba | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  AssetCount  |  Liczba zasobów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  AssetQuota  |  Przydział zasobów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  AssetQuotaUsedPercentage  |  Procent użycia przydziału zasobów  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  ContentKeyPolicyCount  |  Liczba zasad dotyczących kluczy zawartości  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  ContentKeyPolicyQuota  |  Przydział zasad dotyczących kluczy zawartości  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  ContentKeyPolicyQuotaUsedPercentage  |  Procent użycia przydziału zasad klucza zawartości  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  StreamingPolicyCount  |  Liczba zasad przesyłania strumieniowego  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  StreamingPolicyQuota  |  Limit przydziału zasad przesyłania strumieniowego  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices  |  StreamingPolicyQuotaUsedPercentage  |  Procent użycia limitu przydziału zasad przesyłania strumieniowego  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices/streamingEndpoints  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices/streamingEndpoints  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Media/MediaServices/streamingEndpoints  |  SuccessE2ELatency  |  Pomyślne zakończenie oczekiwania  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Liczba wstrzymań GC  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Całkowity czas wstrzymania odzyskiwania pamięci  |  )  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Maksymalna dostępna wartość starego rozmiaru danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Stary rozmiar danych generacji  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Podwyższ poziom do starego rozmiaru danych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Procent użycia procesora usługi  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Przypisano pamięć usługi  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Maksymalna ilość pamięci usługi  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Użyta pamięć usługi  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Procent użycia procesora systemu  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatErrorCount  |  Błąd globalny Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Całkowita liczba odebranych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Maksymalny czas żądania Tomcat  |  )  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Łączna liczba żądań Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Łączny czas żądania Tomcat  |  )  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Średni czas żądania Tomcat  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSentBytes  |  Całkowita liczba wysłanych bajtów Tomcat  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Liczba aktywności sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Maksymalna liczba aktywnych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Maksymalny czas aktywności sesji Tomcat  |  )  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Liczba utworzonych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Liczba wygasłych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Liczba odrzuconych sesji Tomcat  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Podwyższ poziom do rozmiaru danych dla małych generacji  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Użyte przydzieloną pulę woluminów  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Łączny rozmiar logiczny puli woluminów  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  AverageReadLatency  |  Średnie opóźnienie odczytu  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  AverageWriteLatency  |  Średnie opóźnienie zapisu  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  ReadIops  |  Odczyt operacji we/wy  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  VolumeLogicalSize  |  Rozmiar logiczny woluminu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  VolumeSnapshotSize  |  Rozmiar migawki woluminu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. NetApp/netAppAccounts/capacityPools/woluminy  |  WriteIops  |  Zapisz operacje we/wy  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Łączny czas Application Gateway  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Liczba żądań na minutę na hosta w dobrej kondycji  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  BackendConnectTime  |  Czas połączenia z zapleczem  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  BackendFirstByteResponseTime  |  Czas odpowiedzi na pierwszy bajt zaplecza  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  BackendLastByteResponseTime  |  Czas odpowiedzi ostatniego bajtu wewnętrznej bazy danych  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  BackendResponseStatus  |  Stan odpowiedzi zaplecza  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  BlockedCount  |  Dystrybucja reguły zablokowanych żądań zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  BlockedReqCount  |  Liczba zablokowanych żądań zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  BytesReceived  |  Bajty odebrane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  BytesSent  |  Bajty wysłane  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  CapacityUnits  |  Bieżące jednostki wydajności  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  ClientRtt  |  Czas RTT klienta  |  )  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  ComputeUnits  |  Bieżące jednostki obliczeniowe  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  Wartości CurrentConnections  |  Bieżące połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  FailedRequests  |  Żądania zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  HealthyHostCount  |  Liczba hostów w dobrej kondycji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  MatchedCount  |  Dystrybucja reguł dla zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  ResponseStatus  |  Stan odpowiedzi  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Network/applicationGateways  |  Przepływność  |  Przepływność  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  TlsProtocol  |  Protokół TLS klienta  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  TotalRequests  |  Łączna liczba żądań  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/applicationGateways  |  UnhealthyHostCount  |  Liczba hostów w złej kondycji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/azurefirewalls  |  ApplicationRuleHit  |  Liczba trafień reguł aplikacji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/azurefirewalls  |  Przetwarzanie dataprocessed  |  Przetworzone dane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/azurefirewalls  |  FirewallHealth  |  Stan kondycji zapory  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/azurefirewalls  |  NetworkRuleHit  |  Liczba trafień reguł sieci  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/azurefirewalls  |  SNATPortUtilization  |  Wykorzystanie portów przez przytranslatora adresów sieciowych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/Connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/Connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/dnszones  |  QueryVolume  |  Wolumin zapytania  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Network/dnszones  |  RecordSetCapacityUtilization  |  Wykorzystanie pojemności zestawu rekordów  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/dnszones  |  RecordSetCount  |  Liczba zestawów rekordów  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/expressRouteCircuits  |  ArpAvailability  |  Dostępność protokołu ARP  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRouteCircuits  |  BgpAvailability  |  Dostępność protokołu BGP  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| Nie  | Nie |  Microsoft. Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  AdminState  |  AdminState  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BackendHealthPercentage  |  Procent kondycji zaplecza  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BackendRequestCount  |  Liczba żądań wewnętrznej bazy danych  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BackendRequestLatency  |  Opóźnienie żądania wewnętrznej bazy danych  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  BillableResponseSize  |  Rozmiar odpowiedzi do rozliczenia  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  RequestCount  |  Liczba żądań  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  RequestSize  |  Rozmiar żądania  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  ResponseSize  |  Rozmiar odpowiedzi  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  TotalLatency  |  Łączne opóźnienie  |  )  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Network/usługi frontdoor  |  WebApplicationFirewallRequestCount  |  Liczba żądań zapory aplikacji sieci Web  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Network/loadBalancers  |  AllocatedSnatPorts  |  Przydzielono porty przydziałów (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/loadBalancers  |  ByteCount  |  Liczba bajtów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/loadBalancers  |  DipAvailability  |  Stan sondy kondycji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/loadBalancers  |  PacketCount  |  Liczba pakietów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/loadBalancers  |  SnatConnectionCount  |  Liczba połączeń z translatorem adresów sieciowych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/loadBalancers  |  SYNCount  |  Liczba SYN  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Network/loadBalancers  |  UsedSnatPorts  |  Używane porty (wersja zapoznawcza)  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/loadBalancers  |  VipAvailability  |  Dostępność ścieżki danych  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/networkInterfaces  |  BytesReceivedRate  |  Bajty odebrane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/networkInterfaces  |  BytesSentRate  |  Bajty wysłane  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/networkInterfaces  |  PacketsReceivedRate  |  Odebrane pakiety  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/networkInterfaces  |  PacketsSentRate  |  Wysłane pakiety  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Średni czas błądzenia (MS)  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Sprawdza procent niepowodzenia (wersja zapoznawcza)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % Sond nie powiodło się  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Czas błądzenia (MS) (wersja zapoznawcza)  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  ByteCount  |  Liczba bajtów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  BytesDroppedDDoS  |  Bajty przychodzące opuszczone DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  BytesForwardedDDoS  |  Przekazane bajty przychodzące DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  BytesInDDoS  |  Bajty przychodzące DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  DDoSTriggerSYNPackets  |  Pakiety przychodzących SYN wyzwalające łagodzenie DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  DDoSTriggerTCPPackets  |  Przychodzące pakiety TCP do wyzwalania łagodzenia DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  DDoSTriggerUDPPackets  |  Przychodzące pakiety UDP do wyzwalania łagodzenia DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  IfUnderDDoSAttack  |  W obszarze atak DDoS  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  PacketCount  |  Liczba pakietów  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  PacketsDroppedDDoS  |  Odrzucone pakiety przychodzące DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  PacketsForwardedDDoS  |  Przekazane pakiety przychodzące DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  PacketsInDDoS  |  Pakiety przychodzące DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  SynCount  |  Liczba SYN  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  TCPBytesDroppedDDoS  |  Liczba porzuconych bajtów przychodzących protokołu TCP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  TCPBytesForwardedDDoS  |  Przekazane DDoS przychodzące bajty TCP  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  TCPBytesInDDoS  |  Przychodzące bajty TCP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  TCPPacketsDroppedDDoS  |  Liczba porzuconych pakietów TCP przychodzących DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  TCPPacketsForwardedDDoS  |  Przychodzące pakiety TCP przesłane dalej DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  TCPPacketsInDDoS  |  Przychodzące pakiety TCP DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  UDPBytesDroppedDDoS  |  Liczba porzuconych bajtów przychodzących UDP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  UDPBytesForwardedDDoS  |  Przekazane przychodzące bajty UDP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  UDPBytesInDDoS  |  Przychodzące bajty UDP DDoS  |  BytesPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  UDPPacketsDroppedDDoS  |  Liczba porzuconych pakietów przychodzących UDP DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  UDPPacketsForwardedDDoS  |  Przychodzące pakiety UDP DDoS przesłane dalej  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  UDPPacketsInDDoS  |  Przychodzące pakiety UDP DDoS  |  CountPerSecond  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/adresów publicipaddress  |  VipAvailability  |  Dostępność ścieżki danych  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Stan punktu końcowego według punktu końcowego  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. Network/trafficManagerProfiles  |  QpsByEndpoint  |  Zwrócone zapytania według punktu końcowego  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  AverageBandwidth  |  Przepustowość S2S bramy  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  P2SBandwidth  |  Przepustowość P2S bramy  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  P2SConnectionCount  |  Liczba połączeń P2S  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Przepustowość tunelu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Bajty wychodzące tunelu  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Porzucanie pakietów wychodzących przez tunelowanie  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Pakiety wychodzące tuneli  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Bajty transferu danych wejściowych tunelu  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Wyrzucanie niezgodności pakietów przez tunelowanie  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Pakiety przychodzące tunelu  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Czas błądzenia dla poleceń ping dla maszyny wirtualnej  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Nieudane polecenia ping do maszyny wirtualnej  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  przychodzące  |  Komunikaty przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  przychodzące. ALL. failedrequests  |  Wszystkie przychodzące żądania zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  przychodzące. ALL. Requests  |  Wszystkie żądania przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  przychodzące. zaplanowane  |  Przesłane zaplanowane powiadomienia wypychane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  przychodzące. zaplanowane. Cancel  |  Anulowano zaplanowane powiadomienia wypychane  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Instalacja. wszystkie  |  Operacje zarządzania instalacją  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Instalacja. Delete  |  Usuwanie operacji instalacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Instalacja. Get  |  Pobierz operacje instalacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Instalacja. poprawka  |  Operacje instalacji poprawek  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Instalacja. upsert  |  Tworzenie lub aktualizowanie operacji instalacji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  notificationhub. pushs  |  Wszystkie powiadomienia wychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. allpns. badorexpiredchannel  |  Błędy nieprawidłowych lub wygasłych kanałów  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. allpns. channelerror  |  Błędy kanałów  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. allpns. invalidpayload  |  Błędy ładunku  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. allpns. pnserror  |  Błędy zewnętrznego systemu powiadomień  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. allpns. Success  |  Powiadomienia zakończone powodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. APNs. badchannel  |  Błąd nieprawidłowego kanału usługi APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. APNs. expiredchannel  |  Błąd wygasłego kanału usługi APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. APNs. invalidcredentials  |  Błędy autoryzacji usługi APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. APNs. invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia usługi APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. APNs. pnserror  |  Błędy usługi APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. APN. sukces  |  Pomyślne powiadomienia usługi APNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. authenticationerror  |  Błędy uwierzytelniania GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. badchannel  |  Błąd nieprawidłowego kanału GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. expiredchannel  |  Błąd GCM kanału wygasłego  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. invalidcredentials  |  GCM błędy autoryzacji (Nieprawidłowe poświadczenia)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. invalidnotificationformat  |  Nieprawidłowy format powiadomienia GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. pnserror  |  Błędy GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. Success  |  GCM pomyślne powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. GCM. dławienia  |  GCM powiadomienia z ograniczeniami  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. GCM. wrongchannel  |  Błąd nieprawidłowego kanału GCM  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. usługi MPNS. authenticationerror  |  Błędy uwierzytelniania usługi MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. usługi MPNS. badchannel  |  Błąd nieprawidłowego kanału usługi MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. usługi MPNS. channeldisconnected  |  Kanał usługi MPNS został odłączony  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. usługi MPNS. upuszczone  |  USŁUGI MPNS usunięte powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. usługi MPNS. invalidcredentials  |  USŁUGI MPNS nieprawidłowe poświadczenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. usługi MPNS. invalidnotificationformat  |  Nieprawidłowy format powiadomienia usługi MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. usługi MPNS. pnserror  |  Błędy usługi MPNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. usługi MPNS. Success  |  USŁUGI MPNS pomyślne powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. usługi MPNS. dławienia  |  USŁUGI MPNS powiadomienia z ograniczeniami  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. authenticationerror  |  Błędy uwierzytelniania WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. badchannel  |  Błąd nieprawidłowego kanału WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. channeldisconnected  |  Kanał WNS został odłączony  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. channelthrottled  |  Ograniczenie kanału WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. WNS. upuszczone  |  WNS usunięte powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. expiredchannel  |  Błąd WNS kanału wygasłego  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. invalidcredentials  |  WNS błędy autoryzacji (Nieprawidłowe poświadczenia)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. invalidnotificationformat  |  Nieprawidłowy format powiadomienia WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. invalidnotificationsize  |  Błąd nieprawidłowego rozmiaru powiadomienia WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. invalidtoken  |  Błędy autoryzacji WNS (nieprawidłowy token)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. pnserror  |  Błędy WNS  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. Success  |  WNS pomyślne powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzące. WNS. dławienia  |  WNS powiadomienia z ograniczeniami  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. tokenproviderunreachable  |  Błędy autoryzacji WNS (nieosiągalne)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  wychodzący. WNS. wrongtoken  |  WNS błędy autoryzacji (nieprawidłowy token)  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Rejestracja. wszystkie  |  Operacje rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Rejestracja. Tworzenie  |  Operacje tworzenia rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Rejestracja. Usuwanie  |  Operacje usuwania rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Rejestracja. Get  |  Operacje odczytu rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  Rejestracja. Aktualizacja  |  Operacje aktualizacji rejestracji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. NotificationHubs/przestrzenie nazw/NotificationHubs  |  zaplanowane. oczekujące  |  Oczekujące zaplanowane powiadomienia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Dostępna pamięć Average_%  |  Dostępna pamięć (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Dostępny obszar wymiany Average_%  |  Dostępny obszar wymiany (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_% zadeklarowanych bajtów w użyciu  |  % Zadeklarowanych bajtów w użyciu  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Czas DPC Average_%  |  Czas DPC (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_% bezpłatne węzłów i  |  % Wolnego węzłów i  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Wolne miejsce w Average_%  |  Wolne miejsce (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Wolne miejsce w Average_%  |  Wolne miejsce (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Czas bezczynności Average_%  |  Czas bezczynności (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_ czas przerwań (%)  |  Czas przerwań (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Czas oczekiwania operacji we/wy Average_%  |  Czas oczekiwania operacji we/wy (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_% czasu całkiem  |  % Całkiem czasu  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_% czasu uprzywilejowanego  |  Czas uprzywilejowany (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_ czas procesora (%)  |  Czas procesora (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_ czas procesora (%)  |  Czas procesora (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_ użyto węzłów i  |  % Użytych węzłów i  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Użyta pamięć Average_%  |  Używana pamięć (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Używane miejsce Average_%  |  Zajęte miejsce (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Używany obszar wymiany Average_%  |  Używany obszar wymiany (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Czas użytkownika Average_%  |  Czas użytkownika (%)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Available MB  |  Dostępna pamięć (MB)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Pamięć Average_Available MB  |  Dostępna pamięć (MB)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Zamiana Average_Available MB  |  Dostępny obszar wymiany (MB)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Avg. wartość operacji dysku na sek./odczyt  |  Średni czas dysku w s/odczyt  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Avg. wartość operacji dysku na sek./odczyt  |  Średni czas dysku w s/odczyt  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Avg. Transfery dysku/s  |  Średni czas dysku w s/transfer  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Avg. dysku na sek./zapis  |  Średni czas dysku w s/zapis  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Avg. dysku na sek./zapis  |  Średni czas dysku w s/zapis  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Odebrane Average_Bytes/s  |  Bajty odebrane/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Wysłane Average_Bytes/s  |  Bajty wysłane/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Bytes całkowita/s  |  Całkowita liczba bajtów/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Current długość kolejki dysku  |  Bieżąca długość kolejki dysku  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Bajty odczytu Average_Disk/s  |  Bajty odczytu dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Odczyty Average_Disk/s  |  Odczyty dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Odczyty Average_Disk/s  |  Odczyty dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Transfery Average_Disk/s  |  Transfery dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Transfery Average_Disk/s  |  Transfery dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Bajty zapisu Average_Disk/s  |  Bajty zapisu dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Zapisy Average_Disk/s  |  Zapisy dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Zapisy Average_Disk/s  |  Zapisy dysku/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Free megabajtów  |  Wolne megabajty  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Free megabajtów  |  Wolne megabajty  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Free pamięci fizycznej  |  Wolna pamięć fizyczna  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Free miejsce w plikach stronicowania  |  Wolne miejsce w plikach stronicowania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Free pamięć wirtualną  |  Wolna pamięć wirtualna  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Bajty dysku Average_Logical/s  |  Bajty dysku logicznego/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Odczyty Average_Page/s  |  Odczyty stron/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Zapisy Average_Page/s  |  Zapisy stron/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Pages/s  |  Stron/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Pct uprzywilejowany czas  |  Czas uprzywilejowany PCT  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Czas użytkownika Average_Pct  |  Czas użytkownika PCT  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Bajty dysku Average_Physical/s  |  Bajty dysku fizycznego/s  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Processes  |  Procesy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Długość kolejki Average_Processor  |  Długość kolejki procesora  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Size przechowywane w plikach stronicowania  |  Rozmiar zapisany w plikach stronicowania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Total bajty  |  Łączna liczba bajtów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Bajty odebrane Average_Total  |  Całkowita liczba odebranych bajtów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Przesyłane Average_Total bajty  |  Całkowita liczba przesłanych bajtów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Total kolizje  |  Łączna liczba kolizji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Odebrane pakiety Average_Total  |  Całkowita liczba odebranych pakietów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Total przesyłane pakiety  |  Całkowita liczba przesłanych pakietów  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Total błędy odbierania  |  Całkowita liczba błędów odbierania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Błędy wysyłania Average_Total  |  Całkowita liczba błędów transmisji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Uptime  |  Czas  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Przestrzeń wymiany Average_Used MB  |  Używany obszar wymiany (MB)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Pamięć Average_Used w kilobajtach  |  Używana pamięć (w kilobajtach)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Pamięć Average_Used pamięci (MB)  |  Używana pamięć (MB)  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Users  |  Użytkownicy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Average_Virtual pamięci współdzielonej  |  Wirtualna pamięć udostępniona  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Zdarzenie  |  Zdarzenie  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. OperationalInsights/obszary robocze  |  Puls  |  Puls  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. OperationalInsights/obszary robocze  |  Aktualizowanie  |  Aktualizowanie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. PowerBIDedicated/pojemności  |  memory_metric  |  Memory (Pamięć)  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. PowerBIDedicated/pojemności  |  memory_thrashing_metric  |  Migotanie pamięci (zbiory danych)  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. PowerBIDedicated/pojemności  |  qpu_high_utilization_metric  |  Wysokie wykorzystanie jednostek QPU  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. PowerBIDedicated/pojemności  |  QueryDuration  |  Czas trwania zapytania (zestawy danych)  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. PowerBIDedicated/pojemności  |  QueryPoolJobQueueLength  |  Długość kolejki zadań puli zapytań (zbiory danych)  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ActiveListeners  |  ActiveListeners  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Relay/przestrzenie nazw  |  BytesTransferred  |  BytesTransferred  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections — błąd clienterror  |  ListenerConnections — błąd clienterror  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections — błąd servererror  |  ListenerConnections — błąd servererror  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections — sukces  |  ListenerConnections — sukces  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  ListenerDisconnects  |  ListenerDisconnects  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections — błąd clienterror  |  SenderConnections — błąd clienterror  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections — błąd servererror  |  SenderConnections — błąd servererror  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections — sukces  |  SenderConnections — sukces  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderConnections — TotalRequests  |  SenderConnections — TotalRequests  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Relay/przestrzenie nazw  |  SenderDisconnects  |  SenderDisconnects  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Search/searchServices  |  SearchLatency  |  Opóźnienie wyszukiwania  |  Sekundy  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Search/searchServices  |  SearchQueriesPerSecond  |  Zapytania wyszukiwania na sekundę  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Procent zapytań wyszukiwania z ograniczeniami  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Połączeń ActiveConnections  |  Połączeń ActiveConnections  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  ActiveMessages  |  Liczba aktywnych komunikatów w kolejce/temacie.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Połączenia connectionsclosed  |  Połączenia zamknięte.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Połączenia connectionsopened  |  Otwarte połączenia.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  CPUXNS  |  PROCESOR (przestarzałe)  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  DeadletteredMessages  |  Liczba utraconych wiadomości w kolejce/temacie.  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. ServiceBus/przestrzenie nazw  |  Komunikaty incomingmessages  |  Komunikaty przychodzące  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. ServiceBus/przestrzenie nazw  |  Żądania incomingrequests  |  Żądania przychodzące  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Komunikaty  |  Liczba komunikatów w kolejce/temacie.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  NamespaceCpuUsage  |  Procesor CPU  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  NamespaceMemoryUsage  |  Użycie pamięci  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. ServiceBus/przestrzenie nazw  |  Komunikaty outgoingmessages  |  Komunikaty wychodzące  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  ScheduledMessages  |  Liczba zaplanowanych komunikatów w kolejce/temacie.  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Błędy servererrors  |  Błędy serwera.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Rozmiar  |  Rozmiar  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Żądania successfulrequests  |  Żądania zakończone powodzeniem  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  ThrottledRequests  |  Żądania z ograniczeniami.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  Błędy usererrors  |  Błędy użytkownika.  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. ServiceBus/przestrzenie nazw  |  WSXNS  |  Użycie pamięci (przestarzałe)  |  Wartość procentowa  |  Maksimum | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  ActualCpu  |  ActualCpu  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  ActualMemory  |  ActualMemory  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  AllocatedCpu  |  AllocatedCpu  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  AllocatedMemory  |  AllocatedMemory  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  ApplicationStatus  |  ApplicationStatus  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  ContainerStatus  |  ContainerStatus  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  CpuUtilization  |  CpuUtilization  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  MemoryUtilization  |  MemoryUtilization  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  RestartCount  |  RestartCount  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. ServiceFabricMesh/aplikacje  |  Stan servicestatus  |  Stan servicestatus  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SignalRService/sygnalizujący  |  ConnectionCount  |  Liczba połączeń  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SignalRService/sygnalizujący  |  InboundTraffic  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. SignalRService/sygnalizujący  |  MessageCount  |  Liczba komunikatów  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. SignalRService/sygnalizujący  |  OutboundTraffic  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. SignalRService/sygnalizujący  |  SystemErrors  |  Błędy systemu  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SignalRService/sygnalizujący  |  Błędy usererrors  |  Błędy użytkownika  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SQL/managedInstances  |  avg_cpu_percent  |  Średni procent procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/managedInstances  |  io_bytes_read  |  Odczytane bajty we/wy  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/managedInstances  |  io_bytes_written  |  Bajty we/wy zapisywane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/managedInstances  |  io_requests  |  Liczba żądań we/wy  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/managedInstances  |  reserved_storage_mb  |  Zarezerwowane miejsce w magazynie  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/managedInstances  |  storage_space_used_mb  |  Używane miejsce do magazynowania  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/managedInstances  |  virtual_core_count  |  Liczba rdzeni wirtualnych  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery  |  database_dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery  |  database_storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery  |  dtu_used  |  Używane jednostki DTU  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery  |  storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  allocated_data_storage  |  Przydzielono miejsce na danych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  app_cpu_billed  |  Rozliczane użycie procesora przez aplikację  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  app_cpu_percent  |  Procent użycia procesora CPU aplikacji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  app_memory_percent  |  Procent pamięci aplikacji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  blocked_by_firewall  |  Zablokowane przez zaporę  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  cache_hit_percent  |  Procent trafień w pamięci podręcznej  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  cache_used_percent  |  Procent użycia pamięci podręcznej  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  connection_failed  |  Połączenia zakończone niepowodzeniem  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  connection_successful  |  Udane połączenia  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  cpu_limit  |  Limit CPU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  cpu_used  |  Użycie procesora CPU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  stanu  |  Zakleszczenia  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  dtu_limit  |  Limit jednostek DTU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  dtu_used  |  Używane jednostki DTU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  dwu_consumption_percent  |  JEDNOSTEK dwu procent  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  dwu_limit  |  Limit jednostek dwu  |  Liczba  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  dwu_used  |  JEDNOSTEK dwu używane  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  local_tempdb_usage_percent  |  Procent lokalnej bazy danych tempdb  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  log_write_percent  |  Procent operacji we/wy dziennika  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  memory_usage_percent  |  Procent pamięci  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  sessions_percent  |  Procent sesji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  magazyn  |  Używane miejsce na dane  |  Bajty  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  storage_percent  |  Procent użytego miejsca na danych  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  tempdb_data_size  |  Rozmiar pliku danych tempdb kilobajtów  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  tempdb_log_size  |  Rozmiar pliku dziennika bazy danych tempdb kilobajtów  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/bazy danych  |  tempdb_log_used_percent  |  Użyto dziennika% tempdb  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  workers_percent  |  Procent pracowników  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/bazy danych  |  xtp_storage_percent  |  Procent magazynu OLTP w pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  allocated_data_storage  |  Przydzielono miejsce na danych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  allocated_data_storage_percent  |  Procent przydzielonych przestrzeni danych  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  cpu_limit  |  Limit CPU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  cpu_used  |  Użycie procesora CPU  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_allocated_data_storage  |  Przydzielono miejsce na danych  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_cpu_limit  |  Limit CPU  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_cpu_percent  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_cpu_used  |  Użycie procesora CPU  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_eDTU_used  |  użyta wartość eDTU  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_log_write_percent  |  Procent operacji we/wy dziennika  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_sessions_percent  |  Procent sesji  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. SQL/serwery/elasticPools  |  database_workers_percent  |  Procent pracowników  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  dtu_consumption_percent  |  Procent użycia jednostek DTU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  eDTU_limit  |  limit liczby jednostek eDTU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  eDTU_used  |  użyta wartość eDTU  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  log_write_percent  |  Procent operacji we/wy dziennika  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  physical_data_read_percent  |  Procent użycia operacji we/wy na danych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  sessions_percent  |  Procent sesji  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  storage_limit  |  Maksymalny rozmiar danych  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  storage_percent  |  Procent użytego miejsca na danych  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  storage_used  |  Używane miejsce na dane  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  tempdb_data_size  |  Rozmiar pliku danych tempdb kilobajtów  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  tempdb_log_size  |  Rozmiar pliku dziennika bazy danych tempdb kilobajtów  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. SQL/serwery/elasticPools  |  tempdb_log_used_percent  |  Użyto dziennika% tempdb  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  workers_percent  |  Procent pracowników  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. SQL/serwery/elasticPools  |  xtp_storage_percent  |  Procent magazynu OLTP w pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts  |  UsedCapacity  |  Używana pojemność  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  BlobCapacity  |  Pojemność obiektu BLOB  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  BlobCount  |  Liczba obiektów BLOB  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  ContainerCount  |  Liczba kontenerów obiektów BLOB  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  IndexCapacity  |  Pojemność indeksu  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/blobServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  FileCapacity  |  Pojemność plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  FileCount  |  Liczba plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareCount  |  Liczba udziałów plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareQuota  |  Rozmiar przydziału udziału plików  |  Bajty  |  Średnia | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Liczba migawek udziału plików  |  Liczba  |  Średnia | 
| Nie  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Rozmiar migawki udziału plików  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/fileServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  QueueCapacity  |  Pojemność kolejki  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  QueueCount  |  Liczba kolejek  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Liczba komunikatów w kolejce  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/queueServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  Dostępność  |  Dostępność  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  Ruch wychodzący  |  Ruch wychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  Ruch przychodzący  |  Ruch przychodzący  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Opóźnienie E2E dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Opóźnienie serwera dla powodzenia  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  TableCapacity  |  Pojemność tabeli  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  TableCount  |  Liczba tabel  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  TableEntityCount  |  Liczba jednostek tabeli  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. Storage/storageAccounts/tableServices  |  Transakcje  |  Transakcje  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientIOPS  |  Łączna liczba operacji we/wy klienta  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientLatency  |  Średnie opóźnienie klienta  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientLockIOPS  |  Liczba operacji we/wy blokady klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientMetadataReadIOPS  |  Liczba operacji we/wy odczytu metadanych klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientMetadataWriteIOPS  |  Liczba IOPS zapisu metadanych klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientReadIOPS  |  Liczba operacji we/wy odczytu klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientReadThroughput  |  Średnia przepływność odczytu pamięci podręcznej  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientWriteIOPS  |  Liczba operacji we/wy zapisu klienta  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  ClientWriteThroughput  |  Średnia przepływność zapisu w pamięci podręcznej  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetAsyncWriteThroughput  |  Przepływność zapisu asynchronicznego StorageTarget  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetFillThroughput  |  Przepływność wypełnienia StorageTarget  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetHealth  |  Kondycja docelowa magazynu  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetIOPS  |  Łączna liczba operacji we/wy StorageTarget  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetLatency  |  Opóźnienie StorageTarget  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetMetadataReadIOPS  |  Liczba operacji we/wy odczytu metadanych StorageTarget  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetMetadataWriteIOPS  |  Liczba IOPS zapisu w metadanych StorageTarget  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetReadAheadThroughput  |  StorageTarget z wyprzedzeniem  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetReadIOPS  |  StorageTarget operacji we/wy odczytu  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetSyncWriteThroughput  |  Przepływność zapisu synchronicznego StorageTarget  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetTotalReadThroughput  |  StorageTarget całkowita przepływność odczytu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetTotalWriteThroughput  |  StorageTarget całkowita przepływność zapisu  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  StorageTargetWriteIOPS  |  StorageTarget zapisu IOPS  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. StorageCache/pamięci podręczne  |  Czas  |  Czas  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Wynik synchronizacji sesji  |  Liczba  |  Średnia | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Zsynchronizowane bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Rozmiar odwołania do warstw w chmurze według aplikacji  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Rozmiar odwołania do warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Odwołanie do warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Przepływność odwołań do warstw w chmurze  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Stan online serwera  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Synchronizowane pliki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Pliki, których nie należy synchronizować  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  Stan online serwera  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Odwołanie do warstw w chmurze  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Zsynchronizowane bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Synchronizowane pliki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Pliki, których nie należy synchronizować  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Zsynchronizowane bajty  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Synchronizowane pliki  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Pliki, których nie należy synchronizować  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Nieudane żądania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Zdarzenia funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Żądania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  ConversionErrors  |  Błędy konwersji danych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  DeserializationError  |  Błędy deserializacji danych wejściowych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Zdarzenia poza kolejnością  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Wczesne zdarzenia wejściowe  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  Errors  |  Błędy środowiska uruchomieniowego  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventBytes  |  Bajty zdarzeń wejściowych  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  InputEvents  |  Zdarzenia wejściowe  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Zaległe zdarzenia wejściowe  |  Liczba  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Odebrane źródła danych wejściowych  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  LateInputEvents  |  Opóźnione zdarzenia wejściowe  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  OutputEvents  |  Zdarzenia wyjściowe  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Opóźnienie znaku wodnego  |  Sekundy  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Użycie SU%  |  Wartość procentowa  |  Maksimum | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Bajty odczytu dysku  |  Bajty odczytu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Operacje odczytu z dysku/s  |  Operacje odczytu z dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Bajty zapisu dysku  |  Bajty zapisu dysku  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Operacje zapisu na dysku/s  |  Operacje zapisu na dysku/s  |  CountPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Bajty odczytu dysku/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Opóźnienie odczytu dysku  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Operacje odczytu z dysku  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Bajty zapisu dysku/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Opóźnienie zapisu na dysku  |  )  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Operacje zapisu na dysku  |  Liczba  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Pamięć aktywna  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Przydzielone pamięci  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Używana pamięć  |  Bajty  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Sieć — wejście  |  Sieć — wejście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Sieć — wyjście  |  Sieć — wyjście  |  Bajty  |  Łącznie | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Sieć w bajtach/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Bajty wychodzące z sieci/s  |  BytesPerSecond  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  Procentowe użycie procesora CPU  |  Procentowe użycie procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | Nie |  Microsoft. VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Procent gotowych do użycia procesora  |  )  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Aktywne żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Średni czas odpowiedzi  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Długość kolejki dysku  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http404  |  HTTP 404  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Błędy serwera http  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Długość kolejki http  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Duże App Service planowanie procesów roboczych  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Średni App Service pracowników planu  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Niewielka App Service planowanie procesów roboczych  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Łączne frontony  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Dostępni pracownicy  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  Łączna  |  Łączna liczba procesów roboczych  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Używani pracownicy  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  CpuPercentage  |  Procent użycia procesora CPU  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  DiskQueueLength  |  Długość kolejki dysku  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  HttpQueueLength  |  Długość kolejki http  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  MemoryPercentage  |  Procent pamięci  |  Wartość procentowa  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpCloseWait  |  Oczekiwanie na zamknięcie protokołu TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpClosing  |  Zamykanie protokołu TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpEstablished  |  Nawiązano ruch TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpFinWait1  |  Oczekiwanie na TCP FIN 1  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpFinWait2  |  Oczekiwanie na TCP fin 2  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpLastAck  |  Ostatnie potwierdzenie TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpSynReceived  |  Odebrano pakiet TCP syn  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpSynSent  |  Wysłano pakiet TCP syn  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/dopuszczalna  |  TcpTimeWait  |  Czas oczekiwania TCP  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  AppConnections  |  Połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  AverageMemoryWorkingSet  |  Średni zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  AverageResponseTime  |  Średni czas odpowiedzi  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  CpuTime  |  Czas procesora CPU  |  Sekundy  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  CurrentAssemblies  |  Bieżące zestawy  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  FunctionExecutionCount  |  Liczba wykonań funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  FunctionExecutionUnits  |  Jednostki wykonywania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Gen0Collections  |  Zbieranie elementów bezużytecznych generacji 0  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Gen1Collections  |  Wyrzucanie elementów bezużytecznych generacji 1  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Gen2Collections  |  Zbieranie elementów bezużytecznych generacji 2  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Handles  |  Liczba dojść  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  HealthCheckStatus  |  Stan sprawdzania kondycji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http101  |  Http 101  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http2xx  |  Http 2xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http3xx  |  Http 3xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http401  |  HTTP 401  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http403  |  HTTP 403  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http404  |  HTTP 404  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http406  |  Http 406  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http4xx  |  Http 4xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Http5xx  |  Błędy serwera http  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  HttpResponseTime  |  Czas odpowiedzi  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  IoOtherBytesPerSecond  |  Inne bajty we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  IoOtherOperationsPerSecond  |  Inne operacje we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  IoReadBytesPerSecond  |  Bajty odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  IoReadOperationsPerSecond  |  Operacje odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  IoWriteBytesPerSecond  |  Bajty zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  IoWriteOperationsPerSecond  |  Operacje zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  MemoryWorkingSet  |  Zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  PrivateBytes  |  Bajty prywatne  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  RequestsInApplicationQueue  |  Żądania w kolejce aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  Wątki  |  Liczba wątków  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  TotalAppDomains  |  Łączna liczba domen aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/witryny  |  TotalAppDomainsUnloaded  |  Całkowita liczba zwolnionych domen aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  AppConnections  |  Połączenia  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  AverageMemoryWorkingSet  |  Średni zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  AverageResponseTime  |  Średni czas odpowiedzi  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  BytesReceived  |  Dane w  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  BytesSent  |  Dane wychodzące  |  Bajty  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  CpuTime  |  Czas procesora CPU  |  Sekundy  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  CurrentAssemblies  |  Bieżące zestawy  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  FunctionExecutionCount  |  Liczba wykonań funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  FunctionExecutionUnits  |  Jednostki wykonywania funkcji  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Gen0Collections  |  Zbieranie elementów bezużytecznych generacji 0  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Gen1Collections  |  Wyrzucanie elementów bezużytecznych generacji 1  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Gen2Collections  |  Zbieranie elementów bezużytecznych generacji 2  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Handles  |  Liczba dojść  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  HealthCheckStatus  |  Stan sprawdzania kondycji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http101  |  Http 101  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http2xx  |  Http 2xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http3xx  |  Http 3xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http401  |  HTTP 401  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http403  |  HTTP 403  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http404  |  HTTP 404  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http406  |  Http 406  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http4xx  |  Http 4xx  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Http5xx  |  Błędy serwera http  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  HttpResponseTime  |  Czas odpowiedzi  |  Sekundy  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  IoOtherBytesPerSecond  |  Inne bajty we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  IoOtherOperationsPerSecond  |  Inne operacje we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  IoReadBytesPerSecond  |  Bajty odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  IoReadOperationsPerSecond  |  Operacje odczytu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  IoWriteBytesPerSecond  |  Bajty zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  IoWriteOperationsPerSecond  |  Operacje zapisu we/wy na sekundę  |  BytesPerSecond  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  MemoryWorkingSet  |  Zestaw roboczy pamięci  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  PrivateBytes  |  Bajty prywatne  |  Bajty  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Żądania  |  Żądania  |  Liczba  |  Łącznie | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  RequestsInApplicationQueue  |  Żądania w kolejce aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  Wątki  |  Liczba wątków  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  TotalAppDomains  |  Łączna liczba domen aplikacji  |  Liczba  |  Średnia | 
| **Tak**  | **Tak** |  Microsoft. Web/Sites/miejsca  |  TotalAppDomainsUnloaded  |  Całkowita liczba zwolnionych domen aplikacji  |  Liczba  |  Średnia | 
