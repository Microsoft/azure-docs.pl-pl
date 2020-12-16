---
title: Informacje dotyczące monitorowania Queue Storage platformy Azure
description: Informacje o dziennikach i metrykach dotyczące monitorowania danych z platformy Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 10/02/2020
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: ba8a82ed1113bfb3e71560ca9a6c713602df21f2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590651"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Informacje dotyczące monitorowania Queue Storage platformy Azure

Zobacz [monitorowanie usługi Azure Storage](monitor-queue-storage.md) , aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla usługi Azure Storage.

## <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure Storage.

### <a name="capacity-metrics"></a>Metryki pojemności

Wartości metryk pojemności są odświeżane codziennie (do 24 godzin). Ziarno czasu określa przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi godzinę (PT1H).

Usługa Azure Storage udostępnia następujące metryki wydajności w Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Metryki wydajności na poziomie konta

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Metryki Queue Storage

W tej tabeli przedstawiono [queue storage metryki](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Metryka | Opis |
| ------------------- | ----------------- |
| **QueueCapacity** | Ilość Queue Storage używana przez konto magazynu. <br><br> Jednostka `Bytes` <br> Typ agregacji: `Average` <br> Przykład wartości: `1024` |
| **QueueCount** | Liczba kolejek na koncie magazynu. <br><br> Jednostka `Count` <br> Typ agregacji: `Average` <br> Przykład wartości: `1024` |
| **QueueMessageCount** | Przybliżona liczba komunikatów w kolejce na koncie magazynu. <br><br> Jednostka `Count` <br> Typ agregacji: `Average` <br> Przykład wartości: `1024` |

### <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane dla każdego żądania do konta magazynu z usługi Azure Storage do Azure Monitor. W przypadku braku aktywności na koncie magazynu w danym okresie nie będą dostępne żadne dane dotyczące metryk transakcji. Wszystkie metryki transakcji są dostępne zarówno na koncie, jak i na poziomie usługi Queue Storage. Ziarno czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk transakcji to PT1H i PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Storage obsługuje następujące wymiary dla metryk w Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Dzienniki zasobów (wersja zapoznawcza)

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont usługi Premium Storage w ramach kont magazynu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

W poniższej tabeli wymieniono właściwości dzienników zasobów usługi Azure Storage, które są zbierane w dziennikach Azure Monitor lub Azure Storage. Właściwości opisują operację, usługę i typ autoryzacji, która została użyta do wykonania operacji.

### <a name="fields-that-describe-the-operation"></a>Pola opisujące operację

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Pola opisujące sposób uwierzytelniania operacji

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Pola opisujące usługę

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Zobacz także

- Aby uzyskać opis monitorowania Queue Storage platformy Azure, zobacz temat [monitorowanie queue storage platformy Azure](monitor-queue-storage.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) .
