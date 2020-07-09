---
title: Informacje o monitorowaniu usługi Azure Storage | Microsoft Docs
description: Informacje o dziennikach i metrykach dotyczące monitorowania danych z usługi Azure Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 12df9566dd3ddfedd1f4553ad8877258d840858c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960218"
---
# <a name="azure-storage-monitoring-data-reference"></a>Informacje o monitorowaniu usługi Azure Storage

Zobacz [monitorowanie usługi Azure Storage](monitor-storage.md) , aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania dla usługi Azure Storage.

## <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure Storage. 

### <a name="capacity-metrics"></a>Metryki pojemności

Wartości metryk pojemności są wysyłane do Azure Monitor co godzinę. Wartości są odświeżane codziennie. Ziarno czasu określa przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi godzinę (PT1H).

Usługa Azure Storage udostępnia następujące metryki wydajności w Azure Monitor.

#### <a name="account-level"></a>Poziom konta

W tej tabeli przedstawiono [metryki na poziomie konta](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccounts).

| Metric | Opis |
| ------------------- | ----------------- |
| UsedCapacity | Ilość miejsca do magazynowania używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont w warstwie Premium i kont usługi Blob Storage wartość jest taka sama jak BlobCapacity. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

#### <a name="blob-storage"></a>Blob Storage

W tej tabeli przedstawiono [metryki magazynu obiektów BLOB](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsblobservices).

| Metric | Opis |
| ------------------- | ----------------- |
| BlobCapacity | Łączna liczba magazynów obiektów BLOB używanych na koncie magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Dimensions: **blobtype**i **BlobTier** ([Definicja](#metrics-dimensions)) |
| BlobCount    | Liczba obiektów BLOB przechowywanych na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Dimensions: **blobtype**i **BlobTier** ([Definicja](#metrics-dimensions)) |
| ContainerCount    | Liczba kontenerów na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| IndexCapacity     | Ilość miejsca do magazynowania używanego przez ADLS Gen2 indeks hierarchiczny <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

#### <a name="table-storage"></a>Magazyn tabel

W tej tabeli przedstawiono [metryki magazynu tabel](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices).

| Metric | Opis |
| ------------------- | ----------------- |
| TableCapacity | Ilość magazynu tabel używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| TableCount   | Liczba tabel na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| TableEntityCount | Liczba jednostek tabeli na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

#### <a name="queue-storage"></a>Queue Storage

W tej tabeli przedstawiono [metryki magazynu kolejki](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

| Metric | Opis |
| ------------------- | ----------------- |
| QueueCapacity | Ilość magazynu kolejki używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueCount   | Liczba kolejek na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueMessageCount | Przybliżona liczba komunikatów w kolejce w usługa kolejki konta magazynu. <br/><br/>Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

#### <a name="file-storage"></a>File Storage

W tej tabeli przedstawiono [metryki magazynu plików](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices).

| Metric | Opis |
| ------------------- | ----------------- |
| FileCapacity | Ilość miejsca w magazynie plików używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileCount   | Liczba plików na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileShareCount | Liczba udziałów plików na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane dla każdego żądania do konta magazynu z usługi Azure Storage do Azure Monitor. W przypadku braku aktywności na koncie magazynu w danym okresie nie będą dostępne żadne dane dotyczące metryk transakcji. Wszystkie metryki transakcji są dostępne na poziomie konta i usługi (BLOB Storage, Table Storage, Azure Files i Queue Storage). Ziarno czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk transakcji to PT1H i PT1M.

Usługa Azure Storage udostępnia następujące metryki transakcji w Azure Monitor.

| Metric | Opis |
| ------------------- | ----------------- |
| Transakcje | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: responsetype, geotype, ApiName i Authentication ([Definicja](#metrics-dimensions))<br/> Przykład wartości: 1024 |
| Ruch przychodzący | Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Ruch wychodzący | Ilość danych wychodzących. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessServerLatency | Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. <br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessE2ELatency | Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. <br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Dostępność | Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości wszystkich żądań do rozliczenia przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API. <br/><br/> Jednostka: procent <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 99,99 |

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Storage obsługuje następujące wymiary dla metryk w Azure Monitor.

| Nazwa wymiaru | Opis |
| ------------------- | ----------------- |
| **Obiekt blobtype** | Typ obiektu BLOB tylko dla metryk obiektów BLOB. Obsługiwane wartości to **BlockBlob**, **PageBlob**i **Azure Data Lake Storage**. Dołącz obiekt BLOB jest zawarty w BlockBlob. |
| **BlobTier** | Usługa Azure Storage oferuje różne warstwy dostępu, dzięki którym można przechowywać dane obiektów BLOB w najbardziej opłacalny sposób. Zobacz więcej w [warstwie obiektów BLOB usługi Azure Storage](../blobs/storage-blob-storage-tiers.md). Obsługiwane są następujące wartości: <br/> <li>**Gorąca**: warstwa gorąca</li> <li>**Chłodna**: warstwa chłodna</li> <li>**Archiwum**: warstwa Archiwum</li> <li>**Premium**: warstwa Premium dla blokowego obiektu BLOB</li> <li>**P4/P6/P10/P15/P20**/P30/P40/P50/P60: typy warstw dla obiektów BLOB na stronie Premium</li> <li>**Standardowa**: typ warstwy dla standardowego obiektu BLOB strony</li> <li>**Niewarstwowy**: typ warstwy dla konta magazynu ogólnego przeznaczenia w wersji 1</li> |
| **Typ geotype** | Transakcja z klastra podstawowego lub pomocniczego. Dostępne wartości to **podstawowy** i **pomocniczy**. Dotyczy on dostępu do odczytu geograficznie nadmiarowego magazynu (RA-GRS) podczas odczytywania obiektów z pomocniczej dzierżawy. |
| **Responsetype** | Typ odpowiedzi transakcji. Dostępne wartości obejmują: <br/><br/> <li>**ServerOtherError**: wszystkie inne błędy po stronie serwera, z wyjątkiem opisanych </li> <li>**ServerBusyError**: uwierzytelnione żądanie, które zwróciło kod stanu HTTP 503. </li> <li>**ServerTimeoutError**: upłynął limit czasu uwierzytelnionego żądania, które zwróciło kod stanu HTTP 500. Przekroczenie limitu czasu wystąpiło z powodu błędu serwera. </li> <li>**AuthorizationError**: uwierzytelnione żądanie, które nie powiodło się z powodu nieautoryzowanego dostępu do danych lub niepowodzenia autoryzacji. </li> <li>**NetworkError**: uwierzytelnione żądanie, które nie powiodło się z powodu błędów sieci. Najczęściej występuje, gdy klient przedwcześnie zamyka połączenie przed wygaśnięciem limitu czasu. </li><li>**ClientAccountBandwidthThrottlingError**: żądanie jest ograniczane na przepustowość, aby przekroczyć [limity skalowalności konta magazynu](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: żądanie jest ograniczone przy częstotliwości żądania w przypadku przekroczenia [limitów skalowalności konta magazynu](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: inne błędy ograniczania po stronie klienta. ClientAccountBandwidthThrottlingError i ClientAccountRequestThrottlingError są wykluczone.</li> <li>**ClientTimeoutError**: upłynął limit czasu uwierzytelnionego żądania, które zwróciło kod stanu HTTP 500. Jeśli limit czasu sieci klienta lub limit czasu żądania jest ustawiony na mniejszą wartość niż oczekiwana przez usługę magazynu, jest to oczekiwany limit czasu. W przeciwnym razie zostanie zgłoszone jako ServerTimeoutError. </li> <li>**ClientOtherError**: wszystkie inne błędy po stronie klienta, z wyjątkiem opisanych. </li> <li>**Sukces**: pomyślne żądanie</li> <li> **SuccessWithThrottling**: pomyślne żądanie, gdy klient SMB zostanie w pierwszej kolejności określony, ale po ponownych próbach zakończy się pomyślnie.</li> |
| **ApiName** | Nazwa operacji. Przykład: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> W przypadku wszystkich nazw operacji zobacz [dokument](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Authentication** | Typ uwierzytelniania używany w transakcjach. Dostępne wartości obejmują: <br/> <li>**AccountKey**: transakcja jest uwierzytelniana przy użyciu klucza konta magazynu.</li> <li>**SAS**: transakcja jest uwierzytelniana za pomocą sygnatur dostępu współdzielonego.</li> <li>**OAuth**: transakcja jest uwierzytelniana przy użyciu tokenów dostępu OAuth.</li> <li>**Anonimowe**: transakcja jest wymagana anonimowo. Nie obejmuje to żądań inspekcji wstępnej.</li> <li>**AnonymousPreflight**: transakcja jest żądaniem wstępnym.</li> |

W przypadku wymiarów pomocniczych metryk należy określić wartość wymiaru, aby wyświetlić odpowiednie wartości metryk. Jeśli na przykład przeszukiwane są wartości **transakcji** dla udanych odpowiedzi, należy filtrować wymiar **responsetype** z **sukcesem**. Lub Jeśli zobaczysz wartość **BlobCount** dla blokowych obiektów blob, musisz filtrować wymiar **blobtype** z **BlockBlob**.

## <a name="resource-logs-preview"></a>Dzienniki zasobów (wersja zapoznawcza)

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont usługi Premium Storage w ramach kont magazynu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

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

| Właściwość | Opis |
|:--- |:---|
|**pierwszym** | Czas uniwersalnego czasu koordynowanego (UTC), gdy żądanie zostało odebrane przez magazyn. Na przykład: `2018/11/08 21:09:36.6900118`.|
|**Identyfikator** | Identyfikator zasobu konta magazynu. Na przykład: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**kategorii** | Kategoria żądanej operacji. Na przykład: `StorageRead` , `StorageWrite` , lub `StorageDelete` .|
|**operationName** | Typ operacji REST, która została wykonana. <br> Aby uzyskać pełną listę operacji, zobacz [temat analityka magazynu zarejestrowane operacje i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Wersja usługi magazynu określona podczas żądania. Jest to odpowiednik wartości nagłówka **x-MS-Version** . Na przykład: `2017-04-17`.|
|**schemaVersion** | Wersja schematu dziennika. Na przykład: `1.0`.|
|**Stanu** | Kod stanu HTTP dla żądania. Jeśli żądanie zostanie przerwane, ta wartość może być ustawiona na `Unknown` . <br> Na przykład: `206` |
|**statusText** | Stan wymaganej operacji.  Pełna lista komunikatów o stanie znajduje się w [temacie analityka magazynu zarejestrowane operacje i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). W wersji 2017-04-17 i nowszych komunikat o stanie `ClientOtherError` nie jest używany. Zamiast tego pole to zawiera kod błędu. Na przykład: `SASSuccess`  |
|**durationMs** | Łączny czas, wyrażony w milisekundach, w celu wykonania żądanej operacji. Obejmuje to czas odczytywania żądania przychodzącego oraz wysłanie odpowiedzi do osoby żądającej. Na przykład: `12`.|
|**callerIpAddress** | Adres IP osoby żądającej, w tym numer portu. Na przykład: `192.100.0.102:4362`. |
|**korelacj** | Identyfikator, który jest używany do skorelowania dzienników między zasobami. Na przykład: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**przeniesienie** | Lokalizacja konta magazynu. Na przykład: `North Europe`. |
|**protokol**|Protokół, który jest używany w operacji. Na przykład: `HTTP` , `HTTPS` , `SMB` lub`NFS`|
| **adresu** | Zażądano identyfikatora Uniform Resource Identifier. Na przykład: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

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

| Właściwość | Opis |
|:--- |:---|
|**tożsamość/typ** | Typ uwierzytelniania, który został użyty do wysłania żądania. Na przykład: `OAuth` , `SAS Key` , `Account Key` lub`Anonymous` |
|**tożsamość/tokenHash**|To pole jest zarezerwowane wyłącznie do użytku wewnętrznego. |
|**Autoryzacja/akcja** | Akcja, która jest przypisana do żądania. Na przykład: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**Autoryzacja/roleAssignmentId** | Identyfikator przypisania roli. Na przykład: `4e2521b7-13be-4363-aeda-111111111111`.|
|**Autoryzacja/zduplikowanych** | Identyfikator definicji roli. Na przykład: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**nazwy główne/identyfikator** | Identyfikator podmiotu zabezpieczeń. Na przykład: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**podmioty zabezpieczeń/typ** | Typ podmiotu zabezpieczeń. Na przykład: `ServicePrincipal`. |
|**żądający/appID** | Identyfikator aplikacji Open Authorization (OAuth) używany jako obiekt żądający. <br> Na przykład: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**Obiekt żądający/odbiorcy** | Odbiorcy protokołu OAuth żądania. Na przykład: `https://storage.azure.com`. |
|**Obiekt żądający/objectId** | Identyfikator obiektu OAuth osoby żądającej. W przypadku uwierzytelniania przy użyciu protokołu Kerberos reprezentuje identyfikator obiektu uwierzytelnionego użytkownika protokołu Kerberos. Na przykład: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**Obiekt żądający/tenantId** | Identyfikator dzierżawy OAuth tożsamości. Na przykład: `72f988bf-86f1-41af-91ab-222222222222`.|
|**Obiekt żądający/tokenIssuer** | Wystawca tokenu OAuth. Na przykład: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**zleceniodawca/nazwa UPN** | Główna nazwa użytkownika (UPN) obiektu żądającego. Na przykład: `someone@contoso.com`. |
|**Obiekt żądający/nazwa użytkownika** | To pole jest zarezerwowane wyłącznie do użytku wewnętrznego.|

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

| Właściwość | Opis |
|:--- |:---|
|**accountName** | Nazwa konta magazynu. Na przykład: `mystorageaccount`.  |
|**requestUrl** | Żądany adres URL. Na przykład: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | Wartość **nagłówka User-Agent** w cudzysłowie. Na przykład: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Wartość nagłówka **odwołującego** się. Na przykład: `http://contoso.com/about.html`.|
|**Identyfikatorem żądania klienta** | Wartość nagłówka **x-MS-Client-Request-ID** żądania. Na przykład: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**element ETag** | Identyfikator ETag dla zwracanego obiektu, w cudzysłowie. Na przykład: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Łączny czas, wyrażony w milisekundach, aby wykonać żądaną operację. Ta wartość nie obejmuje opóźnienia sieci (czasu odczytu żądania przychodzącego i wysłania odpowiedzi do zleceniodawcy). Na przykład: `22`. |
|**Service** | Usługa skojarzona z tym żądaniem. Na przykład: `blob` , `table` , `files` lub `queue` . |
|**operationCount** | Liczba każdej zarejestrowanej operacji, która jest uwzględniona w żądaniu. Ta liczba zaczyna się od indeksu `0` . Niektóre żądania wymagają więcej niż jednej operacji, na przykład żądanie skopiowania obiektu BLOB. Większość żądań wykonuje tylko jedną operację. Na przykład: `1`. |
|**requestHeaderSize** | Rozmiar nagłówka żądania wyrażony w bajtach. Na przykład: `578`. <br>Jeśli żądanie nie powiedzie się, ta wartość może być pusta. |
|**requestBodySize** | Rozmiar pakietów żądań wyrażony w bajtach, które są odczytywane przez usługę magazynu. <br> Na przykład: `0`. <br>Jeśli żądanie nie powiedzie się, ta wartość może być pusta.  |
|**responseHeaderSize** | Rozmiar nagłówka odpowiedzi wyrażony w bajtach. Na przykład: `216`. <br>Jeśli żądanie nie powiedzie się, ta wartość może być pusta.  |
|**responseBodySize** | Rozmiar pakietów odpowiedzi zapisanych przez usługę magazynu w bajtach. Jeśli żądanie nie powiedzie się, ta wartość może być pusta. Na przykład: `216`.  |
|**requestMd5** | Wartość nagłówka **Content-MD5** lub nagłówka **x-MS-Content-MD5** w żądaniu. Wartość skrótu MD5 określona w tym polu reprezentuje zawartość żądania. Na przykład: `788815fd0198be0d275ad329cafd1830`. <br>To pole może być puste.  |
|**serverMd5** | Wartość skrótu MD5 obliczonego przez usługę magazynu. Na przykład: `3228b3cf1069a5489b298446321f8521`. <br>To pole może być puste.  |
|**lastModifiedTime** | Czas ostatniej modyfikacji (LMT) dla zwracanego obiektu.  Na przykład: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>To pole jest puste dla operacji, które mogą zwracać wiele obiektów. |
|**conditionsUsed** | Rozdzielana średnikami lista par klucz-wartość reprezentujących warunek. Mogą to być następujące warunki: <li> If-Modified — od <li> If-unmodifiedd — od <li> If-Match <li> If-None-Match  <br> Na przykład: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Wartość nagłówka Content-Length dla żądania wysłanego do usługi Storage. Jeśli żądanie zakończyło się pomyślnie, ta wartość jest równa requestBodySize. Jeśli żądanie nie powiedzie się, ta wartość nie może być równa requestBodySize lub może być pusta. |
|**tlsVersion** | Wersja protokołu TLS używana w połączeniu żądania. Na przykład: `TLS 1.2`. |
|**smbTreeConnectID** | Blok komunikatów serwera (SMB) **treeConnectId** ustanowiony w czasie łączenia drzewa. Na przykład: `0x3` |
|**smbPersistentHandleID** | Identyfikator dojścia trwałego z żądania SMB2 CREATE, które przeżyje połączenia sieciowe.  Przywoływany w [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Trwałe**. Na przykład: `0x6003f` |
|**smbVolatileHandleID** | Identyfikator dojścia nietrwałego z żądania SMB2 CREATE, które jest odtwarzane w sieci ponownie nawiązuje połączenie.  Przywoływany w [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Nietrwały**. Na przykład: `0xFFFFFFFF00000065` |
|**smbMessageID** | Identyfikator **MessageID**względny połączenia. Na przykład: `0x3b165` |
|**smbCreditsConsumed** | Dane przychodzące lub wychodzące używane przez żądanie w jednostkach 64 KB. Na przykład: `0x3` |
|**smbCommandDetail** | Więcej informacji o tym konkretnym żądaniu, a nie o ogólnym typie żądania. Na przykład: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | Identyfikator **fileid** skojarzony z plikiem lub katalogiem.  W przybliżeniu analogicznie do systemu plików NTFS. Na przykład: `0x9223442405598953` |
|**smbSessionID** | **Identyfikator sesji** SMB2 został utworzony podczas instalacji sesji. Na przykład: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Wartość w **SMB2_HEADER. Command**. Obecnie jest to liczba z przedziału od 0 do 18 włącznie. Na przykład: `0x6` |
|**smbCommandMinor** | Podklasa elementu **SmbCommandMajor**, gdzie to konieczne. Na przykład: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Zobacz także

- Zobacz [monitorowanie usługi Azure Storage](monitor-storage.md) , aby zapoznać się z opisem monitorowania usługi Azure Storage.
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) .