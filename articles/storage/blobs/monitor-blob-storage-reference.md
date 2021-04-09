---
title: Informacje o danych monitorowania magazynu obiektów blob platformy Azure | Microsoft Docs
description: Informacje o dziennikach i metrykach dotyczące monitorowania danych z usługi Azure Blob Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: b37586f66106e33b2a8dad034a6e7c131484be73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571632"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Dokumentacja monitorowania magazynu obiektów blob platformy Azure

Zobacz [monitorowanie usługi Azure Storage](monitor-blob-storage.md) , aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla usługi Azure Storage.

## <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure Storage. 

### <a name="capacity-metrics"></a>Metryki pojemności

Wartości metryk pojemności są odświeżane codziennie (do 24 godzin). Ziarno czasu określa przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi godzinę (PT1H).

Usługa Azure Storage udostępnia następujące metryki wydajności w Azure Monitor.

#### <a name="account-level"></a>Poziom konta

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob Storage

W tej tabeli przedstawiono [metryki magazynu obiektów BLOB](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices).

| Metric | Opis |
| ------------------- | ----------------- |
| BlobCapacity | Łączna liczba magazynów obiektów BLOB używanych na koncie magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Dimensions: **blobtype** i **BlobTier** ([Definicja](#metrics-dimensions)) |
| BlobCount    | Liczba obiektów BLOB przechowywanych na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Dimensions: **blobtype** i **BlobTier** ([Definicja](#metrics-dimensions)) |
| BlobProvisionedSize | Ilość miejsca do magazynowania obsługiwana na koncie magazynu. Ta Metryka ma zastosowanie tylko do kont magazynu w warstwie Premium. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia |
| ContainerCount    | Liczba kontenerów na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| IndexCapacity     | Ilość miejsca do magazynowania używanego przez ADLS Gen2 indeks hierarchiczny <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane dla każdego żądania do konta magazynu z usługi Azure Storage do Azure Monitor. W przypadku braku aktywności na koncie magazynu w danym okresie nie będą dostępne żadne dane dotyczące metryk transakcji. Wszystkie metryki transakcji są dostępne zarówno na koncie, jak i na poziomie usługi BLOB Storage. Ziarno czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk transakcji to PT1H i PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Storage obsługuje następujące wymiary dla metryk w Azure Monitor.

### <a name="dimensions-available-to-all-storage-services"></a>Dostępne wymiary dla wszystkich usług magazynu

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Wymiary specyficzne dla magazynu obiektów BLOB

| Nazwa wymiaru | Opis |
| ------------------- | ----------------- |
| **Obiekt blobtype** | Typ obiektu BLOB tylko dla metryk obiektów BLOB. Obsługiwane wartości to **BlockBlob**, **PageBlob** i **Azure Data Lake Storage**. Dołączane obiekty blob są zawarte w **BlockBlob**. |
| **BlobTier** | Usługa Azure Storage oferuje różne warstwy dostępu, dzięki którym można przechowywać dane obiektów BLOB w najbardziej opłacalny sposób. Zobacz więcej w [warstwie obiektów BLOB usługi Azure Storage](../blobs/storage-blob-storage-tiers.md). Obsługiwane są następujące wartości: <br/> <li>**Gorąca**: warstwa gorąca</li> <li>**Chłodna**: warstwa chłodna</li> <li>**Archiwum**: warstwa Archiwum</li> <li>**Premium**: warstwa Premium dla blokowego obiektu BLOB</li> <li>**P4/P6/P10/P15/P20**/P30/P40/P50/P60: typy warstw dla obiektów BLOB na stronie Premium</li> <li>**Standardowa**: typ warstwy dla standardowego obiektu BLOB strony</li> <li>**Niewarstwowy**: typ warstwy dla konta magazynu ogólnego przeznaczenia w wersji 1</li> |

W przypadku wymiarów pomocniczych metryk należy określić wartość wymiaru, aby wyświetlić odpowiednie wartości metryk. Jeśli na przykład przeszukiwane są wartości  **transakcji** dla udanych odpowiedzi, należy filtrować wymiar **responsetype** z **sukcesem**. Jeśli zobaczysz wartość **BlobCount** dla blokowych obiektów blob, musisz filtrować wymiar **blobtype** z **BlockBlob**.

## <a name="resource-logs-preview"></a>Dzienniki zasobów (wersja zapoznawcza)

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont usługi Premium Storage w ramach kont magazynu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

W poniższej tabeli wymieniono właściwości dzienników zasobów usługi Azure Storage, które są zbierane w dziennikach Azure Monitor lub Azure Storage. Właściwości opisują operację, usługę i typ autoryzacji, która została użyta do wykonania operacji.

### <a name="fields-that-describe-the-operation"></a>Pola opisujące operację

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Pola opisujące sposób uwierzytelniania operacji

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Pola opisujące usługę

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Zobacz też

- Zobacz [monitorowanie usługi Azure Storage](monitor-blob-storage.md) , aby zapoznać się z opisem monitorowania usługi Azure Storage.
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) .
