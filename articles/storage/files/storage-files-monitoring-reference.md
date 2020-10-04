---
title: Informacje o danych monitorowania Azure Files | Microsoft Docs
description: Informacje o dziennikach i metrykach dotyczące monitorowania danych z Azure Files.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 11f89a0a42a42fd84d1dc0c8606128f5af9e751e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711565"
---
# <a name="azure-files-monitoring-data-reference"></a>Informacje o danych monitorowania Azure Files

Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla Azure Files, zobacz [Azure Files monitorowania](storage-files-monitoring.md) .

## <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla Azure Files. 

### <a name="capacity-metrics"></a>Metryki pojemności

Wartości metryk pojemności są wysyłane do Azure Monitor co godzinę. Wartości są odświeżane codziennie. Ziarno czasu określa przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi godzinę (PT1H).

Azure Files oferuje następujące metryki pojemności w Azure Monitor.

#### <a name="account-level"></a>Poziom konta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure Files

W tej tabeli przedstawiono [Azure Files metryki](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

| Metryka | Opis |
| ------------------- | ----------------- |
| FileCapacity | Ilość miejsca w magazynie plików używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileCount   | Liczba plików na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileShareCapacityQuota | Górny limit ilości miejsca do magazynowania, który może być używany przez usługę Azure Files w bajtach. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024|
| FileShareCount | Liczba udziałów plików na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileShareProvisionedIOPS | Liczba operacji we/wy zainicjowanych w udziale plików. Ta Metryka dotyczy tylko magazynu plików w warstwie Premium. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia |
| FileShareSnapshotCount | Liczba migawek znajdujących się w udziale w usłudze Azure Files konta magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia | 
|FileShareSnapshotSize|Ilość miejsca do magazynowania używanego przez migawki w usłudze Azure Files konta magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia|

### <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane dla każdego żądania do konta magazynu z usługi Azure Storage do Azure Monitor. W przypadku braku aktywności na koncie magazynu w danym okresie nie będą dostępne żadne dane dotyczące metryk transakcji. Wszystkie metryki transakcji są dostępne zarówno na koncie, jak i na poziomie usługi Azure Files. Ziarno czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk transakcji to PT1H i PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Wymiary metryk

Azure Files obsługuje następujące wymiary dla metryk w Azure Monitor.

> [!NOTE] 
> Wymiar udziału plików nie jest dostępny dla standardowych udziałów plików (tylko udziały plików w warstwie Premium). W przypadku korzystania z standardowych udziałów plików metryki dla wszystkich plików są udostępniane na koncie magazynu. Aby uzyskać metryki dla udziałów plików w warstwie Standardowa, Utwórz jeden udział plików na konto magazynu.

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

- Aby uzyskać opis monitorowania usługi Azure Storage, zobacz [Azure Files monitorowania](storage-files-monitoring-reference.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) .