---
title: Azure Queue Storage danych monitorowania
description: Informacje o dziennikach i metrykach dotyczące monitorowania danych z Azure Queue Storage.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 04/20/2021
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 506f5a46688f597b8ac5db341c5bbe5eb5fb67c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763149"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Azure Queue Storage danych monitorowania

Aby [uzyskać szczegółowe informacje na](monitor-queue-storage.md) temat zbierania i analizowania danych monitorowania dla usługi Azure Storage, zobacz Monitorowanie usługi Azure Storage.

## <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure Storage.

### <a name="capacity-metrics"></a>Metryki pojemności

Wartości metryk pojemności są odświeżane codziennie (do 24 godzin). Ziarno czasu definiuje przedział czasu, dla którego prezentowane są wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi jedną godzinę (PT1H).

Usługa Azure Storage udostępnia następujące metryki pojemności w Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Metryki pojemności na poziomie konta

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Queue Storage metryki

W tej tabeli [Queue Storage metryki.](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)

| Metric | Opis |
| ------------------- | ----------------- |
| **QueueCapacity** | Liczba Queue Storage przez konto magazynu. <br><br> Jednostki: `Bytes` <br> Typ agregacji: `Average` <br> Przykład wartości: `1024` |
| **QueueCount** | Liczba kolejek na koncie magazynu. <br><br> Jednostki: `Count` <br> Typ agregacji: `Average` <br> Przykład wartości: `1024` |
| **QueueMessageCount** | Liczba niewydajnych komunikatów w kolejce na koncie magazynu. <br><br> Jednostki: `Count` <br> Typ agregacji: `Average` <br> Przykład wartości: `1024` |

### <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane przy każdym żądaniu do konta magazynu z usługi Azure Storage do Azure Monitor. W przypadku, gdy na koncie magazynu nie będzie żadnych działań, w tym okresie nie będą dostępne żadne dane dotyczące metryk transakcji. Wszystkie metryki transakcji są dostępne zarówno na koncie, Queue Storage poziomie usługi. Ziarno czasu definiuje przedział czasu, w których prezentowane są wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk transakcji to PT1H i PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Storage obsługuje następujące wymiary metryk w Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Dzienniki zasobów (wersja zapoznawcza)

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników dla obiektów blob (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont magazynu w chmurze Premium na kontach magazynu ogólnego przeznaczenia w wersji 1 i kontach magazynu ogólnego przeznaczenia w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

W poniższej tabeli wymieniono właściwości dzienników zasobów usługi Azure Storage, gdy są zbierane w dziennikach Azure Monitor Azure Storage. Właściwości opisują operację, usługę i typ autoryzacji, który został użyty do wykonania operacji.

### <a name="fields-that-describe-the-operation"></a>Pola opisujące operację

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Pola opisujące sposób uwierzytelniania operacji

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Pola opisujące usługę

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Zobacz też

- Opis [Azure Queue Storage](monitor-queue-storage.md) monitorowania można znaleźć w te Azure Queue Storage.
- Aby uzyskać szczegółowe informacje na temat [monitorowania zasobów platformy Azure, Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) monitorowanie zasobów platformy Azure za pomocą usługi Azure Resources.
