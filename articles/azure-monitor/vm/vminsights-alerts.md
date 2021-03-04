---
title: Alerty z usługi VM Insights
description: Opisuje sposób tworzenia reguł alertów na podstawie danych wydajności zebranych przez usługi VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 06c58b7081ed68724a3c907f8fe76dcf5f7b8057
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046809"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>Jak tworzyć alerty na podstawie szczegółowych informacji o maszynie wirtualnej
[Alerty w usłudze Azure monitor](../alerts/alerts-overview.md) aktywnie powiadamiać o interesujących danych i wzorcach danych monitorowania. Usługa VM Insights nie obejmuje wstępnie skonfigurowanych reguł alertów, ale można utworzyć własne na podstawie zbieranych danych. Ten artykuł zawiera wskazówki dotyczące tworzenia reguł alertów, w tym zestawu przykładowych zapytań.

> [!IMPORTANT]
> Alerty opisane w tym artykule opierają się na zapytaniach dziennika zebranych danych dotyczących maszyn wirtualnych. Różni się to od alertów utworzonych przez [Azure monitor na potrzeby kondycji gościa maszyny wirtualnej](vminsights-health-overview.md) , która jest obecnie dostępna w publicznej wersji zapoznawczej. Ponieważ ta funkcja jest niemal ogólnie dostępna, wskazówki dotyczące alertów zostaną skonsolidowane.


## <a name="alert-rule-types"></a>Typy reguł alertów
Azure Monitor ma [różne typy reguł alertów](../alerts/alerts-overview.md#what-you-can-alert-on) na podstawie danych użytych do utworzenia alertu. Wszystkie dane zbierane przez program VM Insights są przechowywane w dziennikach Azure Monitor, które obsługują [alerty dzienników](../alerts/alerts-log.md). Nie można obecnie używać [alertów metryk](../alerts/alerts-log.md) z danymi wydajności zebranymi z usługi VM Insights, ponieważ dane nie są zbierane do metryk Azure monitor. Aby zbierać dane dotyczące alertów metryk, należy zainstalować [rozszerzenie diagnostyki](../agents/diagnostics-extension-overview.md) dla maszyn wirtualnych z systemem Windows lub [telegraf agenta](../essentials/collect-custom-metrics-linux-telegraf.md) dla maszyn wirtualnych z systemem Linux w celu zbierania danych wydajności w metrykach.

Istnieją dwa typy alertów dziennika w Azure Monitor:

- [Liczba alertów z wynikami](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) tworzenia pojedynczego alertu, gdy zapytanie zwróci co najmniej określoną liczbę rekordów. Są one idealne dla danych nieliczbowych, takich jak zdarzenia systemu Windows i dziennika systemowego zbierane przez [agenta log Analytics](../agents/log-analytics-agent.md) lub do analizowania trendów wydajności na wielu komputerach.
- [Alerty pomiarów metryk](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) tworzą osobny alert dla każdego rekordu w zapytaniu, który ma wartość, która przekracza próg zdefiniowany w regule alertu. Te reguły alertów są idealne dla danych wydajności zbieranych przez program VM Insights, ponieważ mogą tworzyć poszczególne alerty dla każdego komputera.


## <a name="alert-rule-walkthrough"></a>Przewodnik po regule alertu
Ta sekcja zawiera szczegółowe informacje na temat tworzenia reguły alertu pomiaru metryk przy użyciu danych wydajności z usługi VM Insights. Możesz użyć tego podstawowego procesu z wieloma kwerendami dzienników, aby otrzymywać alerty dotyczące różnych liczników wydajności.

Zacznij od utworzenia nowej reguły alertu zgodnie z procedurą w temacie [Tworzenie, wyświetlanie i zarządzanie alertami dzienników przy użyciu Azure monitor](../alerts/alerts-log.md). Dla **zasobu** wybierz obszar roboczy log Analytics używany przez Azure monitor maszyny wirtualne w ramach subskrypcji. Ponieważ zasób docelowy dla reguł alertów dziennika jest zawsze obszarem roboczym Log Analytics, zapytanie dziennika musi zawierać dowolny filtr dla określonych maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych. 

W przypadku **warunku** reguły alertu Użyj jednego z zapytań w [poniższej sekcji](#sample-alert-queries) jako **zapytanie wyszukiwania**. Zapytanie musi zwracać Właściwość liczbową o nazwie *AggregatedValue*. Powinno ono podsumowywać dane według komputera, aby można było utworzyć oddzielny alert dla każdej maszyny wirtualnej, która przekracza wartość progową.

W **logice alertu** wybierz pozycję **pomiar metryki** , a następnie podaj **wartość progową**. W obszarze **wyzwalanie alertów opartych na** Określ, ile razy należy przekroczyć wartość progową przed utworzeniem alertu. Na przykład prawdopodobnie nie zachodzi, że procesor przekroczy próg raz, a następnie powraca do normalnego, ale należy zachować ostrożność, jeśli przekroczy próg z wielu kolejnych pomiarów.

**Obliczona na podstawie** sekcji określa, jak często jest wykonywane zapytanie i przedział czasu dla zapytania. W przykładzie przedstawionym poniżej zapytanie zostanie uruchomione co 15 minut i zostanie obliczone wartości wydajności zebrane w ciągu ostatnich 15 minut.


![Reguła alertu pomiaru metryki](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Przykładowe zapytania dotyczące alertu
Następujące zapytania mogą być używane z regułą alertu pomiaru metryki przy użyciu danych wydajności zbieranych przez program VM Insights. Każda z nich podsumowuje dane według komputera, aby alert został utworzony dla każdego komputera o wartości przekraczającej wartość progową.

### <a name="cpu-utilization"></a>Wykorzystanie procesora

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Dostępna pamięć (MB)

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Dostępna pamięć (w procentach)

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Użyto dysku logicznego — wszystkie dyski na każdym komputerze

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Użyto dysku logicznego — poszczególne dyski

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Liczba operacji we/wy dysku logicznego

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Szybkość danych dysku logicznego

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Odebrane bajty interfejsów sieciowych — wszystkie interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Odebrane bajty interfejsów sieciowych — poszczególne interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Wysłane bajty interfejsów sieciowych — wszystkie interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Wysłane bajty interfejsów sieciowych — poszczególne interfejsy

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Zestaw skalowania maszyn wirtualnych
Zmodyfikuj swój identyfikator subskrypcji, grupę zasobów i nazwę zestawu skalowania maszyn wirtualnych.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Określona maszyna wirtualna
Zmodyfikuj swój identyfikator subskrypcji, grupę zasobów i nazwę maszyny wirtualnej.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Użycie procesora CPU dla wszystkich zasobów obliczeniowych w ramach subskrypcji
Zmodyfikuj swój identyfikator subskrypcji.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Użycie procesora CPU dla wszystkich zasobów obliczeniowych w grupie zasobów
Zmodyfikuj swój identyfikator subskrypcji i grupę zasobów.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach w Azure monitor](../alerts/alerts-overview.md).
- Dowiedz się więcej [na temat zapytań dzienników przy użyciu danych z usługi VM Insights](vminsights-log-search.md).
