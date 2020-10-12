---
title: Alerty z Azure Monitor dla maszyn wirtualnych
description: Opisuje sposób tworzenia reguł alertów na podstawie danych wydajności zbieranych przez Azure Monitor dla maszyn wirtualnych.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: be469ab3b05c54ebc5afa6bd6d129efd8d4ba692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254809"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Jak utworzyć alerty z Azure Monitor dla maszyn wirtualnych
[Alerty w usłudze Azure monitor](../platform/alerts-overview.md) aktywnie powiadamiać o interesujących danych i wzorcach danych monitorowania. Azure Monitor dla maszyn wirtualnych nie obejmuje wstępnie skonfigurowanych reguł alertów, ale można je utworzyć na podstawie zbieranych danych. Ten artykuł zawiera wskazówki dotyczące tworzenia reguł alertów, w tym zestawu przykładowych zapytań.


## <a name="alert-rule-types"></a>Typy reguł alertów
Azure Monitor ma [różne typy reguł alertów](../platform/alerts-overview.md#what-you-can-alert-on) na podstawie danych użytych do utworzenia alertu. Wszystkie dane zbierane przez Azure Monitor dla maszyn wirtualnych są przechowywane w dziennikach Azure Monitor, które obsługują [alerty dzienników](../platform/alerts-log.md). Nie można obecnie używać [alertów metryk](../platform/alerts-log.md) z danymi wydajności zbieranymi z Azure monitor dla maszyn wirtualnych, ponieważ dane nie są zbierane do Azure monitor metryk. Aby zbierać dane dotyczące alertów metryk, należy zainstalować [rozszerzenie diagnostyki](../platform/diagnostics-extension-overview.md) dla maszyn wirtualnych z systemem Windows lub [telegraf agenta](../platform/collect-custom-metrics-linux-telegraf.md) dla maszyn wirtualnych z systemem Linux w celu zbierania danych wydajności w metrykach.

Istnieją dwa typy alertów dziennika w Azure Monitor:

- [Liczba alertów z wynikami](../platform/alerts-unified-log.md#count-of-the-results-table-rows) tworzenia pojedynczego alertu, gdy zapytanie zwróci co najmniej określoną liczbę rekordów. Są one idealne dla danych nieliczbowych, takich jak zdarzenia systemu Windows i dziennika systemowego zbierane przez [agenta log Analytics](../platform/log-analytics-agent.md) lub do analizowania trendów wydajności na wielu komputerach.
- [Alerty pomiarów metryk](../platform/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) tworzą osobny alert dla każdego rekordu w zapytaniu, który ma wartość, która przekracza próg zdefiniowany w regule alertu. Te reguły alertów są idealne dla danych wydajności zbieranych przez Azure Monitor dla maszyn wirtualnych, ponieważ mogą tworzyć indywidualne alerty dla każdego komputera.


## <a name="alert-rule-walkthrough"></a>Przewodnik po regule alertu
Ta sekcja zawiera szczegółowe instrukcje dotyczące tworzenia reguły alertu pomiaru metryk przy użyciu danych wydajności z Azure Monitor dla maszyn wirtualnych. Możesz użyć tego podstawowego procesu z wieloma kwerendami dzienników, aby otrzymywać alerty dotyczące różnych liczników wydajności.

Zacznij od utworzenia nowej reguły alertu zgodnie z procedurą w temacie [Tworzenie, wyświetlanie i zarządzanie alertami dzienników przy użyciu Azure monitor](../platform/alerts-log.md). Dla **zasobu**wybierz obszar roboczy log Analytics używany przez Azure monitor maszyny wirtualne w ramach subskrypcji. Ponieważ zasób docelowy dla reguł alertów dziennika jest zawsze obszarem roboczym Log Analytics, zapytanie dziennika musi zawierać dowolny filtr dla określonych maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych. 

W przypadku **warunku** reguły alertu Użyj jednego z zapytań w [poniższej sekcji](#sample-alert-queries) jako **zapytanie wyszukiwania**. Zapytanie musi zwracać Właściwość liczbową o nazwie *AggregatedValue*. Powinno ono podsumowywać dane według komputera, aby można było utworzyć oddzielny alert dla każdej maszyny wirtualnej, która przekracza wartość progową.

W **logice alertu**wybierz pozycję **pomiar metryki** , a następnie podaj **wartość progową**. W obszarze **wyzwalanie alertów opartych na**Określ, ile razy należy przekroczyć wartość progową przed utworzeniem alertu. Na przykład prawdopodobnie nie zachodzi, że procesor przekroczy próg raz, a następnie powraca do normalnego, ale należy zachować ostrożność, jeśli przekroczy próg z wielu kolejnych pomiarów.

**Obliczona na podstawie** sekcji określa, jak często jest wykonywane zapytanie i przedział czasu dla zapytania. W przykładzie przedstawionym poniżej zapytanie zostanie uruchomione co 15 minut i zostanie obliczone wartości wydajności zebrane w ciągu ostatnich 15 minut.


![Reguła alertu pomiaru metryki](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Przykładowe zapytania dotyczące alertu
Następujące zapytania mogą być używane z regułą alertu pomiaru metryki przy użyciu danych wydajności zbieranych przez Azure Monitor dla maszyn wirtualnych. Każda z nich podsumowuje dane według komputera, aby alert został utworzony dla każdego komputera o wartości przekraczającej wartość progową.

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

- Dowiedz się więcej o [alertach w Azure monitor](../platform/alerts-overview.md).
- Dowiedz się więcej [na temat zapytań dzienników przy użyciu danych z Azure monitor dla maszyn wirtualnych](vminsights-log-search.md).
