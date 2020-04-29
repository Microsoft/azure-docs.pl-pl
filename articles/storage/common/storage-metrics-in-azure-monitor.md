---
title: Metryki usługi Azure Storage w Azure Monitor | Microsoft Docs
description: Dowiedz się więcej o nowych metrykach oferowanych przez Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247099"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metryki usługi Azure Storage w usłudze Azure Monitor

Dzięki metrykom w usłudze Azure Storage można analizować trendy użycia, żądania śledzenia i diagnozować problemy z kontem magazynu.

Azure Monitor zapewnia ujednolicone interfejsy użytkownika do monitorowania różnych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Usługa Azure Storage integruje Azure Monitor, wysyłając dane metryk do platformy Azure Monitor.

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor zapewnia wiele sposobów uzyskiwania dostępu do metryk. Możesz uzyskać do nich dostęp z [Azure Portal](https://portal.azure.com), interfejsów API Azure monitor (REST i .NET) oraz rozwiązań do analizy, takich jak Event Hubs. Aby uzyskać więcej informacji, zobacz [Azure monitor metryki](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Metryki są domyślnie włączone i można uzyskać dostęp do ostatnich 93 dni danych. Jeśli chcesz zachować dane przez dłuższy czas, możesz zarchiwizować dane metryk na koncie usługi Azure Storage. Ta konfiguracja jest skonfigurowana w [ustawieniach diagnostycznych](../../azure-monitor/platform/platform-logs-overview.md) w Azure monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Dostęp do metryk w Azure Portal

Można monitorować metryki w czasie w Azure Portal. Poniższy przykład pokazuje, jak wyświetlić **transakcje** na poziomie konta.

![zrzut ekranu przedstawiający dostęp do metryk w Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

W przypadku metryk obsługujących Wymiary można filtrować metrykę z odpowiednią wartością wymiaru. Poniższy przykład pokazuje, jak wyświetlić **transakcje** na poziomie konta w ramach określonej operacji, wybierając wartości dla wymiaru **nazwa interfejsu API** .

![zrzut ekranu przedstawiający dostęp do metryk z wymiarem w Azure Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Dostęp do metryk przy użyciu interfejsu API REST

Azure Monitor udostępnia [interfejsy API REST](/rest/api/monitor/) do odczytywania definicji metryk i wartości. W tej sekcji pokazano, jak odczytać metryki magazynu. Identyfikator zasobu jest używany we wszystkich interfejsach API REST. Aby uzyskać więcej informacji, zapoznaj się z tematem identyfikator zasobu dla usług w magazynie.

Poniższy przykład pokazuje, jak używać [ArmClient](https://github.com/projectkudu/ARMClient) w wierszu polecenia, aby uprościć testowanie przy użyciu interfejsu API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Wyświetlanie definicji metryki na poziomie konta za pomocą interfejsu API REST

Poniższy przykład pokazuje, jak wyświetlić definicję metryki na poziomie konta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Jeśli chcesz wyświetlić listę definicji metryk dla obiektów blob, Table, File lub Queue, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

Odpowiedź zawiera definicję metryki w formacie JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Odczytywanie wartości metryk na poziomie konta za pomocą interfejsu API REST

Poniższy przykład pokazuje, jak odczytywać dane metryk na poziomie konta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

W powyższym przykładzie, jeśli chcesz odczytać wartości metryk dla obiektów blob, Table, File lub Queue, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

Następująca odpowiedź zawiera wartości metryk w formacie JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Uzyskiwanie dostępu do metryk przy użyciu zestawu .NET SDK

Azure Monitor udostępnia [zestaw .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) do odczytywania definicji metryk i wartości. [Przykładowy kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) pokazuje, jak używać zestawu SDK z innymi parametrami. Aby uzyskać metryki `0.18.0-preview` magazynu, należy użyć wersji lub nowszej. Identyfikator zasobu jest używany w zestawie .NET SDK. Aby uzyskać więcej informacji, zapoznaj się z tematem identyfikator zasobu dla usług w magazynie.

W poniższym przykładzie pokazano, jak Azure Monitor za pomocą zestawu SDK platformy .NET odczytać metryki magazynu.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Wyświetlanie definicji metryki na poziomie konta przy użyciu zestawu .NET SDK

Poniższy przykład pokazuje, jak wyświetlić definicję metryki na poziomie konta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Jeśli chcesz wyświetlić listę definicji metryk dla obiektów blob, Table, File lub Queue, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Odczytywanie wartości metryk przy użyciu zestawu .NET SDK

Poniższy przykład pokazuje, jak odczytywać `UsedCapacity` dane na poziomie konta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

W powyższym przykładzie, jeśli chcesz odczytać wartości metryk dla obiektów blob, Table, File lub Queue, należy określić różne identyfikatory zasobów dla każdej usługi z interfejsem API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Odczytywanie wielowymiarowych wartości metryk przy użyciu zestawu .NET SDK

W przypadku metryk wielowymiarowych należy zdefiniować filtr metadanych, jeśli chcesz odczytać dane metryki dla określonej wartości wymiaru.

Poniższy przykład pokazuje, jak odczytać dane metryki z obsługą wielowymiarowego:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Informacje o IDENTYFIKATORze zasobu dla usług w usłudze Azure Storage

Identyfikator zasobu jest unikatowym identyfikatorem zasobu na platformie Azure. W przypadku używania interfejsu API REST Azure Monitor do odczytywania definicji lub wartości metryk, należy użyć identyfikatora zasobu dla zasobu, na którym zamierzasz pracować. Szablon identyfikatora zasobu jest następujący:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Magazyn udostępnia metryki zarówno na poziomie konta magazynu, jak i na poziomie usługi z Azure Monitor. Na przykład można pobrać metryki tylko dla magazynu obiektów BLOB. Każdy poziom ma własny identyfikator zasobu, który jest używany do pobierania metryk tylko dla tego poziomu.

### <a name="resource-id-for-a-storage-account"></a>Identyfikator zasobu dla konta magazynu

Poniżej przedstawiono format służący do określania identyfikatora zasobu dla konta magazynu.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Identyfikator zasobu dla usług magazynu

Poniżej przedstawiono format służący do określania identyfikatora zasobu dla każdej usługi magazynu.

* Identyfikator zasobu Blob service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Identyfikator zasobu Table service
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Identyfikator zasobu usługa kolejki
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Identyfikator zasobu usługi plików
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Identyfikator zasobu w interfejsie API REST Azure Monitor

Poniżej przedstawiono wzorzec używany podczas wywoływania interfejsu API REST Azure Monitor.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metryki pojemności
Wartości metryk pojemności są wysyłane do Azure Monitor co godzinę. Wartości są odświeżane codziennie. Ziarno czasu określa przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk pojemności wynosi godzinę (PT1H).

Usługa Azure Storage udostępnia następujące metryki wydajności w Azure Monitor.

### <a name="account-level"></a>Poziom konta

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| UsedCapacity | Ilość miejsca do magazynowania używanego przez konto magazynu. W przypadku kont magazynu w warstwie Standardowa jest to suma pojemności używanej przez obiekt blob, tabelę, plik i kolejkę. W przypadku kont w warstwie Premium i kont usługi Blob Storage wartość jest taka sama jak BlobCapacity. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| BlobCapacity | Łączna liczba magazynów obiektów BLOB używanych na koncie magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Dimensions: **blobtype**i **BlobTier** ([Definicja](#metrics-dimensions)) |
| BlobCount    | Liczba obiektów BLOB przechowywanych na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 <br/> Dimensions: **blobtype**i **BlobTier** ([Definicja](#metrics-dimensions)) |
| ContainerCount    | Liczba kontenerów na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| IndexCapacity     | Ilość miejsca do magazynowania używanego przez ADLS Gen2 indeks hierarchiczny <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="table-storage"></a>Table Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| TableCapacity | Ilość magazynu tabel używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| TableCount   | Liczba tabel na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| TableEntityCount | Liczba jednostek tabeli na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| QueueCapacity | Ilość magazynu kolejki używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueCount   | Liczba kolejek na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| QueueMessageCount | Liczba niewygasłych komunikatów w kolejce na koncie magazynu. <br/><br/>Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

### <a name="file-storage"></a>File Storage

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| FileCapacity | Ilość miejsca w magazynie plików używanego przez konto magazynu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileCount   | Liczba plików na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |
| FileShareCount | Liczba udziałów plików na koncie magazynu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Przykład wartości: 1024 |

## <a name="transaction-metrics"></a>Metryki transakcji

Metryki transakcji są emitowane dla każdego żądania do konta magazynu z usługi Azure Storage do Azure Monitor. W przypadku braku aktywności na koncie magazynu w danym okresie nie będą dostępne żadne dane dotyczące metryk transakcji. Wszystkie metryki transakcji są dostępne na poziomie konta i usługi (BLOB Storage, Table Storage, Azure Files i Queue Storage). Ziarno czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwane ziarno czasu dla wszystkich metryk transakcji to PT1H i PT1M.

Usługa Azure Storage udostępnia następujące metryki transakcji w Azure Monitor.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Transakcje | Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: responsetype, geotype, ApiName i Authentication ([Definicja](#metrics-dimensions))<br/> Przykład wartości: 1024 |
| Ruch przychodzący | Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Ruch wychodzący | Ilość danych wychodzących. Ta liczba obejmuje ruch wychodzący do zewnętrznego klienta z usługi Azure Storage, a także dane wychodzące na platformie Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. <br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessServerLatency | Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. <br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| SuccessE2ELatency | Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi. <br/><br/> Jednostka: milisekundy <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 1024 |
| Dostępność | Procent dostępności usługi magazynu lub określonej operacji interfejsu API. Dostępność jest obliczana przez podzielenie wartości wszystkich żądań do rozliczenia przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API. <br/><br/> Jednostka: procent <br/> Typ agregacji: Średnia <br/> Odpowiednie wymiary: geotype, ApiName i Authentication ([Definicja](#metrics-dimensions)) <br/> Przykład wartości: 99,99 |

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Storage obsługuje następujące wymiary dla metryk w Azure Monitor.

| Nazwa wymiaru | Opis |
| ------------------- | ----------------- |
| **Obiekt blobtype** | Typ obiektu BLOB tylko dla metryk obiektów BLOB. Obsługiwane wartości to **BlockBlob**, **PageBlob**i **Azure Data Lake Storage**. Dołącz obiekt BLOB jest zawarty w BlockBlob. |
| **BlobTier** | Usługa Azure Storage oferuje różne warstwy dostępu, dzięki którym można przechowywać dane obiektów BLOB w najbardziej opłacalny sposób. Zobacz więcej w [warstwie obiektów BLOB usługi Azure Storage](../blobs/storage-blob-storage-tiers.md). Obsługiwane są następujące wartości: <br/> <li>**Gorąca**: warstwa gorąca</li> <li>**Chłodna**: warstwa chłodna</li> <li>**Archiwum**: warstwa Archiwum</li> <li>**Premium**: warstwa Premium dla blokowego obiektu BLOB</li> <li>**P4/P6/P10/P15/P20**/P30/P40/P50/P60: typy warstw dla obiektów BLOB na stronie Premium</li> <li>**Standardowa**: typ warstwy dla standardowego obiektu BLOB strony</li> <li>**Niewarstwowy**: typ warstwy dla konta magazynu ogólnego przeznaczenia w wersji 1</li> |
| **Typ geotype** | Transakcja z klastra podstawowego lub pomocniczego. Dostępne wartości to **podstawowy** i **pomocniczy**. Dotyczy on dostępu do odczytu geograficznie nadmiarowego magazynu (RA-GRS) podczas odczytywania obiektów z pomocniczej dzierżawy. |
| **Responsetype** | Typ odpowiedzi transakcji. Dostępne wartości obejmują: <br/><br/> <li>**ServerOtherError**: wszystkie inne błędy po stronie serwera, z wyjątkiem opisanych </li> <li>**ServerBusyError**: uwierzytelnione żądanie, które zwróciło kod stanu HTTP 503. </li> <li>**ServerTimeoutError**: upłynął limit czasu uwierzytelnionego żądania, które zwróciło kod stanu HTTP 500. Przekroczenie limitu czasu wystąpiło z powodu błędu serwera. </li> <li>**AuthorizationError**: uwierzytelnione żądanie, które nie powiodło się z powodu nieautoryzowanego dostępu do danych lub niepowodzenia autoryzacji. </li> <li>**NetworkError**: uwierzytelnione żądanie, które nie powiodło się z powodu błędów sieci. Najczęściej występuje, gdy klient przedwcześnie zamyka połączenie przed wygaśnięciem limitu czasu. </li>  <li>**ClientAccountBandwidthThrottlingError**: żądanie jest ograniczane na przepustowość, aby przekroczyć [limity skalowalności konta magazynu](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: żądanie jest ograniczone przy częstotliwości żądania w przypadku przekroczenia [limitów skalowalności konta magazynu](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: inne błędy ograniczania po stronie klienta. ClientAccountBandwidthThrottlingError i ClientAccountRequestThrottlingError są wykluczone.</li> <li>**ClientTimeoutError**: upłynął limit czasu uwierzytelnionego żądania, które zwróciło kod stanu HTTP 500. Jeśli limit czasu sieci klienta lub limit czasu żądania jest ustawiony na mniejszą wartość niż oczekiwana przez usługę magazynu, jest to oczekiwany limit czasu. W przeciwnym razie zostanie zgłoszone jako ServerTimeoutError.</li> </li> <li>**ClientOtherError**: wszystkie inne błędy po stronie klienta, z wyjątkiem opisanych. </li> <li>**Sukces**: pomyślne żądanie</li> <li> **SuccessWithThrottling**: pomyślne żądanie, gdy klient SMB zostanie w pierwszej kolejności określony, ale po ponownych próbach zakończy się pomyślnie.</li> |
| **ApiName** | Nazwa operacji. Przykład: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> W przypadku wszystkich nazw operacji zobacz [dokument](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Uwierzytelnianie** | Typ uwierzytelniania używany w transakcjach. Dostępne wartości obejmują: <br/> <li>**AccountKey**: transakcja jest uwierzytelniana przy użyciu klucza konta magazynu.</li> <li>**SAS**: transakcja jest uwierzytelniana za pomocą sygnatur dostępu współdzielonego.</li> <li>**OAuth**: transakcja jest uwierzytelniana przy użyciu tokenów dostępu OAuth.</li> <li>**Anonimowe**: transakcja jest wymagana anonimowo. Nie obejmuje to żądań inspekcji wstępnej.</li> <li>**AnonymousPreflight**: transakcja jest żądaniem wstępnym.</li> |

W przypadku wymiarów pomocniczych metryk należy określić wartość wymiaru, aby wyświetlić odpowiednie wartości metryk. Jeśli na przykład przeszukiwane są wartości **transakcji** dla udanych odpowiedzi, należy filtrować wymiar **responsetype** z **sukcesem**. Lub Jeśli zobaczysz wartość **BlobCount** dla blokowych obiektów blob, musisz filtrować wymiar **blobtype** z **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Ciągłość usługi ze starszymi metrykami

Starsze metryki są dostępne równolegle z Azure Monitor metrykami zarządzanymi. Obsługa zachowuje się tak samo, dopóki usługa Azure Storage nie zakończy działania usługi na starszych metrykach.

## <a name="faq"></a>Najczęściej zadawane pytania

**Czy nowe metryki obsługują klasyczne konto magazynu?**

Nie, nowe metryki w Azure Monitor obsługują tylko Azure Resource Manager kont magazynu. Jeśli chcesz używać metryk na kontach magazynu, musisz przeprowadzić migrację do konta magazynu Azure Resource Manager. Zobacz [Migrowanie do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Czy usługa Azure Storage obsługuje metryki dla dysków Managed Disks lub niezarządzanych?**

Nie, usługa Azure COMPUTE obsługuje metryki na dyskach. Aby uzyskać więcej informacji, zobacz [artykuł](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) .

**Jak mapować i migrować metryki klasyczne przy użyciu nowych metryk?**

Można znaleźć szczegółowe mapowanie między metrykami klasycznymi i nowymi metrykami w ramach [migracji metryk usługi Azure Storage](./storage-metrics-migration.md).

## <a name="next-steps"></a>Następne kroki

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
