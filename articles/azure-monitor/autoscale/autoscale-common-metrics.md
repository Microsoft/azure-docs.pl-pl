---
title: Często skalowane metryki
description: Dowiedz się, które metryki są często używane do automatycznego skalowania Cloud Services, Virtual Machines i Web Apps.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 4b763f39d3b88a7884e89dddbc2c483c1bb84d31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717778"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure Monitor typowe metryki automatycznego skalowania

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor Skalowanie automatyczne pozwala skalować liczbę uruchomionych wystąpień w górę lub w dół na podstawie danych telemetrycznych (metryk). W tym dokumencie opisano typowe metryki, których warto użyć. W Azure Portal można wybrać metrykę zasobu do skalowania przez. Można jednak wybrać dowolną metrykę z innego zasobu do skalowania.

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)i [usług API Management](../../api-management/api-management-key-concepts.md). Inne usługi platformy Azure używają różnych metod skalowania.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Metryki obliczeniowe dla maszyn wirtualnych opartych na Menedżer zasobów
Domyślnie Virtual Machines Menedżer zasobów i Virtual Machine Scale Sets emitują podstawowe (na poziomie hosta) metryki. Ponadto po skonfigurowaniu zbierania danych diagnostycznych dla maszyny wirtualnej platformy Azure i VMSS rozszerzenie diagnostyki platformy Azure emituje także liczniki wydajności gościa (nazywane "metrykami systemu operacyjnego gościa").  Wszystkie te metryki są używane w regułach skalowania automatycznego.

Możesz użyć `Get MetricDefinitions` interfejsu API/PoSH/interfejsu wiersza polecenia, aby wyświetlić metryki dostępne dla zasobu VMSS.

Jeśli korzystasz z zestawów skalowania maszyn wirtualnych i nie widzisz określonej metryki na liście, prawdopodobnie jest ona *wyłączona* w rozszerzeniu diagnostyki.

Jeśli określona Metryka nie jest próbkowana lub transferowana z częstotliwością, którą chcesz, możesz zaktualizować konfigurację diagnostyki.

Jeśli poprzednia sprawa ma wartość true, należy sprawdzić [użycie programu PowerShell, aby włączyć Diagnostyka Azure w maszynie wirtualnej z systemem Windows](../../virtual-machines/extensions/diagnostics-windows.md) informacje o programie PowerShell w celu skonfigurowania i zaktualizowania rozszerzenia diagnostyki maszyn wirtualnych platformy Azure w celu włączenia metryki. Ten artykuł zawiera również przykładowy plik konfiguracji diagnostyki.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metryki hosta dla maszyn wirtualnych z systemem Windows i Linux opartych na Menedżer zasobów
Następujące metryki na poziomie hosta są domyślnie emitowane dla maszyny wirtualnej platformy Azure i VMSS w wystąpieniach systemów Windows i Linux. Te metryki opisują maszynę wirtualną platformy Azure, ale są zbierane z hosta maszyny wirtualnej platformy Azure, a nie za pośrednictwem agenta zainstalowanego na maszynie wirtualnej gościa. Te metryki mogą być używane w regułach skalowania automatycznego.

- [Metryki hosta dla maszyn wirtualnych z systemem Windows i Linux opartych na Menedżer zasobów](../essentials/metrics-supported.md#microsoftcomputevirtualmachines)
- [Metryki hostów dla systemów Windows i Linux opartych na Menedżer zasobówach VM Scale Sets](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Metryki systemu operacyjnego gościa dla maszyn wirtualnych z systemem Windows opartych na Menedżer zasobów
Podczas tworzenia maszyny wirtualnej na platformie Azure Diagnostyka jest włączana za pomocą rozszerzenia diagnostyki. Rozszerzenie Diagnostics emituje zestaw metryk wykonanych z wewnątrz maszyny wirtualnej. Oznacza to, że można automatycznie skalować metryki, które nie są emitowane domyślnie.

Listę metryk można wygenerować za pomocą następującego polecenia w programie PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Można utworzyć alert dla następujących metryk:

| Nazwa metryki | Jednostka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Procent |
| \Processor (_Total) \% uprzywilejowany czas |Procent |
| \Processor (_Total) — \% czas użytkownika |Procent |
| \Processor Information (_Total) \Processor częstotliwość |Liczba |
| \System\Processes |Liczba |
| \Process (_Total) \Thread liczba wystąpień |Liczba |
| \Process (_Total) \Handle liczba wystąpień |Liczba |
| \Memory \% zadeklarowane bajty w użyciu |Procent |
| \Memory\Available Bytes |Bajty |
| \Memory\Committed bajty |Bajty |
| Limit \Memory\Commit |Bajty |
| \Memory\Pool stronicowane bajty |Bajty |
| \Memory\Pool niestronicowane bajty |Bajty |
| \PhysicalDisk (_Total) \% czas dysku |Procent |
| \PhysicalDisk (_Total) \% czas odczytu dysku |Procent |
| \PhysicalDisk (_Total) \% godzina zapisu dysku |Procent |
| \PhysicalDisk (_Total) \Bajty transfery/s |CountPerSecond |
| \PhysicalDisk (_Total) \Bajty odczyty/s |CountPerSecond |
| \PhysicalDisk (_Total) \Bajty zapisy/s |CountPerSecond |
| \PhysicalDisk (_Total) \Bajty bajty/s |BytesPerSecond |
| \PhysicalDisk (_Total) \Bajty bajty odczytu/s |BytesPerSecond |
| \PhysicalDisk (_Total) \Bajty Bajty zapisu/s |BytesPerSecond |
| \PhysicalDisk (_Total) \Avg. Długość kolejki dysku |Liczba |
| \PhysicalDisk (_Total) \Avg. Długość kolejki odczytu dysku |Liczba |
| \PhysicalDisk (_Total) \Avg. Długość kolejki zapisu dysku |Liczba |
| \Dysk logiczny (_Total) \% wolne miejsce |Procent |
| \Dysk logiczny (_Total) \Wolne megabajty |Liczba |

### <a name="guest-os-metrics-linux-vms"></a>Pomiar systemu operacyjnego gościa maszyny wirtualne z systemem Linux
Podczas tworzenia maszyny wirtualnej na platformie Azure Diagnostyka jest domyślnie włączona przy użyciu rozszerzenia diagnostyki.

Listę metryk można wygenerować za pomocą następującego polecenia w programie PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Można utworzyć alert dla następujących metryk:

| Nazwa metryki | Jednostka |
| --- | --- |
| \Memory\AvailableMemory |Bajty |
| \Memory\PercentAvailableMemory |Procent |
| \Memory\UsedMemory |Bajty |
| \Memory\PercentUsedMemory |Procent |
| \Memory\PercentUsedByCache |Procent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bajty |
| \Memory\PercentAvailableSwap |Procent |
| \Memory\UsedSwap |Bajty |
| \Memory\PercentUsedSwap |Procent |
| \Processor\PercentIdleTime |Procent |
| \Processor\PercentUserTime |Procent |
| \Processor\PercentNiceTime |Procent |
| \Processor\PercentPrivilegedTime |Procent |
| \Processor\PercentInterruptTime |Procent |
| \Processor\PercentDPCTime |Procent |
| \Processor\PercentProcessorTime |Procent |
| \Processor\PercentIOWaitTime |Procent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Sekundy |
| \PhysicalDisk\AverageWriteTime |Sekundy |
| \PhysicalDisk\AverageTransferTime |Sekundy |
| \PhysicalDisk\AverageDiskQueueLength |Liczba |
| \NetworkInterface\BytesTransmitted |Bajty |
| \NetworkInterface\BytesReceived |Bajty |
| \NetworkInterface\PacketsTransmitted |Liczba |
| \NetworkInterface\PacketsReceived |Liczba |
| \NetworkInterface\BytesTotal |Bajty |
| \NetworkInterface\TotalRxErrors |Liczba |
| \NetworkInterface\TotalTxErrors |Liczba |
| \NetworkInterface\TotalCollisions |Liczba |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Metryki często używane App Service (farma serwerów)
Możesz również wykonywać automatyczne skalowanie na podstawie wspólnych metryk serwera sieci Web, takich jak długość kolejki http. Nazwa metryki to **HttpQueueLength**.  W poniższej sekcji przedstawiono dostępne metryki farmy serwerów (App Service).

### <a name="web-apps-metrics"></a>Metryki Web Apps
Listę metryk Web Apps można wygenerować za pomocą następującego polecenia w programie PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Możesz otrzymywać alerty dotyczące tych metryk lub skalować je.

| Nazwa metryki | Jednostka |
| --- | --- |
| CpuPercentage |Procent |
| MemoryPercentage |Procent |
| DiskQueueLength |Liczba |
| HttpQueueLength |Liczba |
| BytesReceived |Bajty |
| BytesSent |Bajty |

## <a name="commonly-used-storage-metrics"></a>Często używane metryki magazynu
Możesz skalować według długości kolejki magazynu, która jest liczbą komunikatów w kolejce magazynu. Długość kolejki magazynu jest specjalną metryką, a wartość progowa to liczba komunikatów na wystąpienie. Na przykład jeśli istnieją dwa wystąpienia i jeśli próg jest ustawiony na 100, skalowanie występuje, gdy całkowita liczba komunikatów w kolejce wynosi 200. Mogą to być 100 komunikatów na wystąpienie, 120 i 80, lub dowolną inną kombinację, która dodaje do 200 lub więcej.

Skonfiguruj to ustawienie w Azure Portal w bloku **Ustawienia** . W przypadku zestawów skalowania maszyn wirtualnych można zaktualizować ustawienie skalowania automatycznego w szablonie Menedżer zasobów, aby użyć wartości *metricname* jako *APPROXIMATEMESSAGECOUNT* i przekazać identyfikator kolejki magazynu jako *metricResourceUri*.

Na przykład przy użyciu klasycznego konta magazynu ustawienie skalowania automatycznego metricTrigger będzie obejmowało:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

W przypadku konta magazynu (nieklasycznego) metricTrigger może obejmować:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Często używane metryki Service Bus
Możesz skalować Service Bus długość kolejki, czyli liczbę komunikatów w kolejce Service Bus. Długość kolejki Service Bus jest specjalną metryką, a wartość progowa to liczba komunikatów na wystąpienie. Na przykład jeśli istnieją dwa wystąpienia i jeśli próg jest ustawiony na 100, skalowanie występuje, gdy całkowita liczba komunikatów w kolejce wynosi 200. Mogą to być 100 komunikatów na wystąpienie, 120 i 80, lub dowolną inną kombinację, która dodaje do 200 lub więcej.

W przypadku zestawów skalowania maszyn wirtualnych można zaktualizować ustawienie skalowania automatycznego w szablonie Menedżer zasobów, aby użyć wartości *metricname* jako *APPROXIMATEMESSAGECOUNT* i przekazać identyfikator kolejki magazynu jako *metricResourceUri*.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> W przypadku Service Bus koncepcji grupy zasobów nie istnieje, ale Azure Resource Manager tworzy domyślną grupę zasobów na region. Grupa zasobów jest zwykle w formacie "default-ServiceBus-[region]". Na przykład "default-ServiceBus-Wschodnie", "default-ServiceBus-zachodnie", "default-ServiceBus-AustraliaEast" itd.
>
>