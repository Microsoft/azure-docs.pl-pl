---
title: Informacje o danych monitorowania usługi Azure queue storage | Microsoft Docs
description: Informacje o dziennikach i metrykach dotyczące monitorowania danych z usługi Azure queue storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 52ee08e0cce23aebbac1564a5c3ed894d0e487d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711414"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Informacje o monitorowaniu usługi Azure queue storage

Zobacz [monitorowanie usługi Azure Storage](monitor-queue-storage.md) , aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla usługi Azure Storage.

## <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure Storage. 

### <a name="capacity-metrics"></a>Metryki pojemności

Wartości metryk pojemności są wysyłane do Azure Monitor co godzinę. Wartości są odświeżane codziennie. Ziarno czasu określa przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi godzinę (PT1H).

Usługa Azure Storage udostępnia następujące metryki wydajności w Azure Monitor.

#### <a name="account-level"></a>Poziom konta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage"></a>Queue Storage

W tej tabeli przedstawiono [metryki magazynu kolejki](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices).

| Metryka | Opis |
| ------------------- | ----------------- |
| QueueCapacity | Ilość magazynu kolejki używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueCount   | Liczba kolejek na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueMessageCount | Przybliżona liczba komunikatów w kolejce w usługa kolejki konta magazynu. <br/><br/>Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane dla każdego żądania do konta magazynu z usługi Azure Storage do Azure Monitor. W przypadku braku aktywności na koncie magazynu w danym okresie nie będą dostępne żadne dane dotyczące metryk transakcji. Wszystkie metryki transakcji są dostępne zarówno na poziomie usługi konta, jak i kolejki. Ziarno czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk transakcji to PT1H i PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Storage obsługuje następujące wymiary dla metryk w Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Dzienniki zasobów (wersja zapoznawcza)

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont usługi Premium Storage w ramach kont magazynu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

W poniższej tabeli wymieniono właściwości dzienników zasobów usługi Azure Storage, które są zbierane w dziennikach Azure Monitor lub Azure Storage. Właściwości opisują operację, usługę i typ autoryzacji, która została użyta do wykonania operacji.

### <a name="fields-that-describe-the-operation"></a>Pola opisujące operację

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Pola opisujące sposób uwierzytelniania operacji

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Pola opisujące usługę

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Zobacz też

- Zobacz [monitorowanie usługi Azure queue storage](monitor-queue-storage.md) , aby uzyskać opis monitorowania usługi Azure Storage.
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) .