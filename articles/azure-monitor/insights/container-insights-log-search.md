---
title: Jak wykonywać zapytania dotyczące dzienników z Azure Monitor kontenerów | Microsoft Docs
description: Azure Monitor dla kontenerów zbiera dane dotyczące metryk i dzienników, a w tym artykule opisano rekordy i zawiera przykładowe zapytania.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 9bfa63a49da33289b8c811007f210e6546579d9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033565"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Jak wykonywać zapytania dotyczące dzienników z Azure Monitor dla kontenerów

Azure Monitor dla kontenerów zbiera metryki wydajności, dane spisu i informacje o stanie kondycji z hostów kontenerów i kontenerów. Dane są zbierane co trzy minuty i przekazywane do obszaru roboczego Log Analytics w Azure Monitor. Te dane są dostępne dla [zapytań](../log-query/log-query-overview.md) w Azure monitor. Te dane można zastosować do scenariuszy, które obejmują Planowanie migracji, analizę pojemności, odnajdywanie i rozwiązywanie problemów z wydajnością na żądanie.

## <a name="container-records"></a>Rekordy kontenerów

W poniższej tabeli przedstawiono szczegółowe informacje o rekordach zbieranych przez Azure Monitor kontenerów. Aby zapoznać się z listą opisów kolumn, zobacz temat informacje o tabelach [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) i [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) .

| Dane | Źródło danych | Typ danych | Pola |
|------|-------------|-----------|--------|
| Spis kontenerów | Kubelet | `ContainerInventory` | TimeGenerated, Computer, Name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenie, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Dziennik kontenera | Docker | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, nazwa, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Spis węzłów kontenera | Interfejs API polecenia | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Spis magazynów w klastrze Kubernetes | Interfejs API polecenia | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Spis węzłów części klastra Kubernetes | Interfejs API polecenia | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Zdarzenia Kubernetes | Interfejs API polecenia | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, komunikat, SourceSystem | 
| Usługi w klastrze Kubernetes | Interfejs API polecenia | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metryki wydajności dla węzłów części klastra Kubernetes | Metryki użycia są uzyskiwane z cAdvisor i limitów z interfejsu API polecenia | &#124; wydajności, gdzie ObjectName = = "K8SNode" | Computer, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki wydajności dotyczące kontenerów części klastra Kubernetes | Metryki użycia są uzyskiwane z cAdvisor i limitów z interfejsu API polecenia | &#124; wydajności, gdzie ObjectName = = "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metryki niestandardowe ||`InsightsMetrics` | Komputer, nazwa, przestrzeń nazw, pochodzenie, SourceSystem, znaczniki<sup>1</sup>, TimeGenerated, typ, Va, _ResourceId | 

<sup>1</sup> Właściwość *Tags* reprezentuje [wiele wymiarów](../platform/data-platform-metrics.md#multi-dimensional-metrics) dla odpowiedniej metryki. Aby uzyskać więcej informacji na temat metryk zbieranych i przechowywanych w `InsightsMetrics` tabeli oraz opis właściwości rekordu, zobacz [InsightsMetrics Overview (omówienie](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)).

## <a name="search-logs-to-analyze-data"></a>Wyszukaj dzienniki, aby analizować dane

Dzienniki Azure Monitor mogą ułatwić wyszukiwanie trendów, diagnozowanie wąskich gardeł, prognozowanie lub skorelowanie danych, które mogą pomóc w ustaleniu, czy bieżąca Konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane przeszukiwania dzienników są udostępniane, aby od razu zacząć korzystać z programu lub, aby dostosować je do swoich potrzeb.

Możesz wykonać interaktywną analizę danych w obszarze roboczym, wybierając opcję **Wyświetl dzienniki zdarzeń Kubernetes** lub **Wyświetl dzienniki kontenerów** w okienku podglądu z listy rozwijanej **Widok w analizie** . Strona **przeszukiwanie dzienników** pojawia się po prawej stronie Azure Portal.

![Analiza danych w usłudze Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Dane wyjściowe dzienników kontenerów, które są przekazywane do obszaru roboczego, to STDOUT i STDERR. Ponieważ Azure Monitor jest monitorowany przez usługę Azure Managed Kubernetes (AKS), system polecenia nie jest zbierany dzisiaj ze względu na dużą ilość wygenerowanych danych. 

### <a name="example-log-search-queries"></a>Przykładowe zapytania dotyczące przeszukiwania dzienników

Często warto tworzyć zapytania, które zaczynają się od przykładu lub dwa, a następnie modyfikują je w celu dopasowania do własnych wymagań. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, można eksperymentować z następującymi przykładowymi zapytaniami:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124; komputera projektu, nazwy, obrazu, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> Tabela renderowania &#124; | Wyświetl listę wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;, gdzie nie (IsEmpty (Namespace_s))<br> &#124; Sortuj według opisu TimeGenerated<br> Tabela renderowania &#124; | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124; Sumuj AggregatedValue = Count () według obrazu, ImageTag, uruchomione | Spis obrazów | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Wyd.<br> &#124; gdzie ObjectName = = "K8SContainer" i CounterName = = "cpuUsageNanoCores" &#124; podsumowuje AvgCPUUsageNanoCores = AVG (CounterValue) według bin (TimeGenerated, – min), InstanceName | Procesor CPU kontenera | 
| **Wybierz opcję wyświetlania wykresu liniowego**:<br> Wyd.<br> &#124; gdzie ObjectName = = "K8SContainer" i CounterName = = "memoryRssBytes" &#124; podsumowuje AvgUsedRssMemoryBytes = AVG (CounterValue) według bin (TimeGenerated, – min), InstanceName | Pamięć kontenera |
| InsightsMetrics<br> &#124; WHERE name = "requests_count"<br> &#124; Sumuj wartość Val = any (Val) przez TimeGenerated = bin (TimeGenerated, 1M)<br> &#124; Sortuj według TimeGenerated ASC<br> &#124; Project RequestsPerMinute = Val-poprzedni (Val), TimeGenerated <br> &#124; renderowania BarChart  | Liczba żądań na minutę z metrykami niestandardowymi |

## <a name="query-prometheus-metrics-data"></a>Zapytanie danych metryk Prometheus

Poniższy przykład to zapytanie metryki Prometheus przedstawiające Odczyty dysku na sekundę na dysk na węzeł.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Aby wyświetlić metryki Prometheus oddzielone przez Azure Monitor filtrowane według przestrzeni nazw, określ wartość "Prometheus". Oto przykładowe zapytanie, aby wyświetlić metryki Prometheus z `default` przestrzeni nazw Kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Dane Prometheus można także zbadać bezpośrednio według nazwy.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Błędy podczas konfigurowania lub wypadków

Aby zbadać wszelkie błędy związane z konfiguracją lub brakiem, następujące przykładowe zapytanie zwraca informacje o zdarzeniach z `KubeMonAgentEvents` tabeli.

```
KubeMonAgentEvents | where Level != "Info" 
```

Dane wyjściowe pokazują wyniki podobne do następującego przykładu:

![Rejestruj wyniki zapytania dotyczącego zdarzeń informacyjnych z agenta](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Następne kroki

Azure Monitor kontenerów nie zawiera wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dotyczących wydajności za pomocą Azure monitor dla kontenerów](./container-insights-log-alerts.md) , aby dowiedzieć się, jak utworzyć zalecane alerty dotyczące wysokiego użycia procesora i pamięci, aby zapewnić obsługę procesów i procedur operacyjnych DevOps.
