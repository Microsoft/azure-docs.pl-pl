---
title: Azure Files monitorowania | Microsoft Docs
description: Dowiedz się, jak monitorować wydajność i dostępność Azure Files. Monitoruj Azure Files dane, Dowiedz się więcej o konfiguracji i analizuj dane dotyczące metryk i dzienników.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 4b2f819edd875130c57d487536691b4588dcc71f
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772672"
---
# <a name="monitoring-azure-files"></a>Azure Files monitorowania

Jeśli masz krytyczne aplikacje i procesy biznesowe, które opierają się na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania, które są generowane przez Azure Files i jak można użyć funkcji Azure Monitor do analizowania alertów dotyczących tych danych.

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ta wersja zapoznawcza umożliwia korzystanie z dzienników dla obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Ta funkcja jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Zobacz [Omówienie konta magazynu](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Przegląd monitora

Strona **Przegląd** w Azure Portal dla każdego zasobu Azure Files zawiera krótki widok użycia zasobów, na przykład żądania i rozliczenia godzinowe. Te informacje są przydatne, ale dostępna jest tylko niewielka ilość danych monitorowania. Niektóre z tych danych są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu zasobu. Dodatkowe typy zbierania danych można włączyć za pomocą jakiejś konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Azure Files tworzy dane monitorowania przy użyciu [Azure monitor](../../azure-monitor/overview.md), który jest pełną usługą monitorowania stosu na platformie Azure. Azure Monitor oferuje pełny zestaw funkcji służących do monitorowania zasobów i zasobów platformy Azure w innych chmurach i lokalnych. 

Zacznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), który opisuje następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje zostały skompilowane w tym artykule, opisując szczegółowe dane zebrane z Azure Files. Przykłady pokazują, jak skonfigurować zbieranie danych i analizować je za pomocą narzędzi platformy Azure.

## <a name="monitoring-data"></a>Dane monitorowania

Azure Files gromadzi te same rodzaje danych monitorowania jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Aby uzyskać szczegółowe informacje na temat metryk i dzienników tworzonych przez Azure Files, zobacz [Dokumentacja usługi Azure File monitoring](storage-files-monitoring-reference.md) .

Metryki i dzienniki w Azure Monitor obsługują tylko Azure Resource Manager kont magazynu. Azure Monitor nie obsługuje klasycznych kont magazynu. Jeśli chcesz użyć metryk lub dzienników na klasycznym koncie magazynu, musisz przeprowadzić migrację do konta magazynu Azure Resource Manager. Zobacz [Migrowanie do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

## <a name="collection-and-routing"></a>Kolekcja i Routing

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawień diagnostycznych. Aby zbierać dzienniki zasobów, należy utworzyć ustawienie diagnostyczne. 

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont magazynu w warstwie Premium w celu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

Aby utworzyć ustawienie diagnostyczne przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub programu PowerShell, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Aby wyświetlić szablon Azure Resource Manager, który tworzy ustawienie diagnostyczne, zobacz [ustawienie diagnostyczne dla usługi Azure Storage](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

Podczas tworzenia ustawienia diagnostycznego wybierz typ magazynu, dla którego chcesz włączyć dzienniki, takie jak obiekt BLOB, kolejka, tabela lub plik. W obszarze Azure Files wybierz pozycję **plik**. 

W przypadku utworzenia ustawienia diagnostycznego w Azure Portal można wybrać zasób z listy. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, musisz użyć identyfikatora zasobu punktu końcowego Azure Files. Identyfikator zasobu można znaleźć w Azure Portal, otwierając stronę **Właściwości** konta magazynu.

Należy również określić jedną z następujących kategorii operacji, dla których mają być zbierane dzienniki. 

| Kategoria | Opis |
|:---|:---|
| StorageRead | Operacje odczytu na obiektach. |
| StorageWrite | Operacje zapisu w obiektach. |
| StorageDelete | Operacje usuwania obiektów. |

Aby uzyskać listę zarejestrowanych operacji SMB i REST, zobacz temat [zarejestrowane operacje magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz informacje o [danych monitorowania Azure Files](storage-files-monitoring-reference.md).

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki usługi Azure Storage można analizować za pomocą metryk z innych usług platformy Azure, korzystając z Eksplorator metryk. Otwórz Eksplorator metryk, wybierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md). 

W przypadku metryk, które obsługują wymiary, można filtrować metrykę przy użyciu żądanej wartości wymiaru.  Aby uzyskać pełną listę wymiarów obsługiwanych przez usługę Azure Storage, zobacz [Dimension Metrics](storage-files-monitoring-reference.md#metrics-dimensions). Metryki dla Azure Files należą do następujących przestrzeni nazw: 

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/fileServices

Aby uzyskać listę wszystkich Azure Monitor metryki pomocy technicznej, w tym Azure Files, zobacz temat [Azure monitor support Metrics](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Uzyskiwanie dostępu do metryk

> [!TIP]
> Aby wyświetlić przykłady interfejsu wiersza polecenia platformy Azure lub platformy .NET, wybierz odpowiednie karty wymienione w tym miejscu.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista definicji metryk

Można wyświetlić listę definicji metryk konta magazynu lub usługi Azure Files. Użyj polecenia cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition) .

W tym przykładzie Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu usługi Azure Files.  Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Odczytywanie wartości metryk

Można odczytywać wartości metryk na poziomie konta magazynu lub usługi Azure Files. Użyj polecenia cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Utwórz listę definicji metryk na poziomie konta

Można wyświetlić listę definicji metryk konta magazynu lub usługi Azure Files. Użyj polecenia [AZ monitor Metric list-](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) Definitions.
 
W tym przykładzie Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu usługi Azure Files. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Odczytaj wartości metryk na poziomie konta

Można odczytać wartości metryk konta magazynu lub usługi Azure Files. Użyj polecenia [AZ monitor Metric list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor udostępnia [zestaw .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) do odczytywania definicji metryk i wartości. [Przykładowy kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) pokazuje, jak używać zestawu SDK z innymi parametrami. Aby `0.18.0-preview` uzyskać metryki magazynu, należy użyć lub nowszej wersji.
 
W tych przykładach Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub usługi Azure Files. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

Zastąp `<subscription-ID>` ZMIENNĄ identyfikatorem subskrypcji. Aby uzyskać wskazówki dotyczące uzyskiwania wartości dla `<tenant-ID>` , `<application-ID>` i `<AccessKey>` , zobacz [Korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Utwórz listę definicji metryk na poziomie konta

Poniższy przykład pokazuje, jak wyświetlić definicję metryki na poziomie konta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
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

#### <a name="reading-account-level-metric-values"></a>Odczytywanie wartości metryk na poziomie konta

Poniższy przykład pokazuje, jak odczytywać `UsedCapacity` dane na poziomie konta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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
            // Enumrate metric value
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

#### <a name="reading-multidimensional-metric-values"></a>Odczytywanie wartości metryk wielowymiarowych

W przypadku metryk wielowymiarowych należy zdefiniować filtry metadanych, jeśli chcesz odczytywać dane metryk dla określonych wartości wymiarów.

Poniższy przykład pokazuje, jak odczytywać dane metryki z obsługą wielowymiarową metryki:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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

---

## <a name="analyzing-logs"></a>Analizowanie dzienników

Można uzyskać dostęp do dzienników zasobów jako obiektów BLOB na koncie magazynu, jako dane zdarzenia lub za pomocą zapytań analitycznych dzienników.

Aby uzyskać listę zarejestrowanych operacji SMB i REST, zobacz temat [zarejestrowane operacje magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz informacje o [danych monitorowania Azure Files](storage-files-monitoring-reference.md).

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont magazynu w warstwie Premium w celu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi. Na przykład jeśli konto magazynu ma aktywność w punkcie końcowym pliku, ale nie znajduje się w jego punktach końcowych tabeli lub kolejki, tworzone są tylko dzienniki odnoszące się do usługi plików. Dzienniki usługi Azure Storage zawierają szczegółowe informacje na temat żądań zakończonych powodzeniem i zakończonych niepowodzeniem w usłudze magazynu. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku.

### <a name="log-authenticated-requests"></a>Rejestruj uwierzytelnione żądania

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania używające sygnatury dostępu współdzielonego (SAS) lub OAuth, w tym żądania zakończone niepowodzeniem i zakończone powodzeniem
- Żądania danych analitycznych (klasyczne dane dziennika w **$Logs** kontenera i danych metryk klas w tabelach **$Metric** )

Żądania wykonywane przez samą usługę Azure Files, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych żądań SMB i REST, zobacz temat [zarejestrowane operacje magazynu i komunikaty o stanie](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz informacje o [danych monitorowania Azure Files](storage-files-monitoring-reference.md).

### <a name="log-anonymous-requests"></a>Rejestruj anonimowe żądania

 Rejestrowane są następujące typy żądań anonimowych:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy przekroczenia limitu czasu dla klienta i serwera
- Żądania GET zakończone niepowodzeniem z kodem błędu 304 (nie zmodyfikowano)

Wszystkie inne Nieudane żądania anonimowe nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych żądań SMB i REST, zobacz temat [zarejestrowane operacje magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz informacje o [danych monitorowania Azure Files](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Uzyskiwanie dostępu do dzienników na koncie magazynu

Dzienniki są wyświetlane jako obiekty blob przechowywane w kontenerze na docelowym koncie magazynu. Dane są zbierane i przechowywane w pojedynczym obiekcie BLOB jako ładunek JSON rozdzielany wierszami. Nazwa obiektu BLOB jest zgodna z tą konwencją nazewnictwa:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Oto przykład:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Uzyskiwanie dostępu do dzienników w centrum zdarzeń

Dzienniki wysyłane do centrum zdarzeń nie są przechowywane jako plik, ale można sprawdzić, czy centrum zdarzeń otrzymało informacje dziennika. W Azure Portal przejdź do centrum zdarzeń i sprawdź, czy liczba **przychodzących komunikatów** jest większa od zera. 

![Dzienniki inspekcji](media/storage-files-monitoring/event-hub-log.png)

Możesz uzyskiwać dostęp do danych dziennika, które są wysyłane do centrum zdarzeń, i je odczytywać, korzystając z informacji o zabezpieczeniach i narzędzi do monitorowania. Aby uzyskać więcej informacji, zobacz [co można zrobić przy użyciu danych monitorowania wysyłanych do centrum zdarzeń?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Uzyskiwanie dostępu do dzienników w obszarze roboczym Log Analytics

Można uzyskać dostęp do dzienników wysyłanych do obszaru roboczego Log Analytics przy użyciu zapytań dziennika Azure Monitor. Dane są przechowywane w tabeli **StorageFileLogs** . 

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

#### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Poniżej przedstawiono niektóre zapytania, które można wprowadzić na pasku **przeszukiwania dzienników** , aby ułatwić monitorowanie Azure Files. Te zapytania działają w [nowym języku](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** z menu Grupa zasobów konta magazynu zostanie otwarty log Analytics z zakresem zapytania ustawionym na bieżącą grupę zasobów. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tej grupy zasobów. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych zasobów lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

Użyj tych zapytań, aby ułatwić monitorowanie udziałów plików platformy Azure:

- Wyświetlanie błędów SMB w ciągu ostatniego tygodnia

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Utwórz wykres kołowy operacji SMB w ciągu ostatniego tygodnia

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Wyświetlanie błędów REST w ciągu ostatniego tygodnia

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Tworzenie wykresu kołowego operacji REST w ciągu ostatniego tygodnia

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Aby wyświetlić listę nazw kolumn i opisów Azure Files, zobacz [StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs).

Aby uzyskać więcej informacji na temat pisania zapytań, zobacz [Samouczek: Rozpoczynanie pracy z zapytaniami log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

## <a name="alerts"></a>Alerty

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów w systemie przed ich zapisaniem przez klientów. Można ustawić alerty dotyczące [metryk](/azure/azure-monitor/platform/alerts-metric-overview), [dzienników](/azure/azure-monitor/platform/alerts-unified-log)i [dziennika aktywności](/azure/azure-monitor/platform/activity-log-alerts). 

W poniższej tabeli przedstawiono niektóre przykładowe scenariusze monitorowania i właściwej metryki do użycia w ramach alertu:

| Scenariusz | Metryka do użycia dla alertu |
|-|-|
| Udział plików jest ograniczany. | Metryka: transakcje<br>Nazwa wymiaru: typ odpowiedzi <br>Nazwa wymiaru: plik udziału (tylko udział plików w warstwie Premium) |
| Rozmiar udziału plików to 80% pojemności. | Metryka: pojemność pliku<br>Nazwa wymiaru: plik udziału (tylko udział plików w warstwie Premium) |
| Wychodzący udział plików przekroczył 500 GiB w ciągu jednego dnia. | Metryka: ruch wychodzący<br>Nazwa wymiaru: plik udziału (tylko udział plików w warstwie Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Jak utworzyć alerty dla Azure Files

1. Przejdź do swojego **konta magazynu** w **Azure Portal**. 

2. Kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertów**.

3. Kliknij pozycję **Edytuj zasób**, wybierz **Typ zasobu plik** , a następnie kliknij przycisk **gotowe**. 

4. Kliknij pozycję **Wybierz warunek** i podaj następujące informacje dotyczące alertu: 

    - **Metryka**
    - **Nazwa wymiaru**
    - **Logika alertu**

5. Kliknij pozycję **Wybierz grupę akcji** i Dodaj grupę akcji (wiadomości e-mail, wiadomości SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

6. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis**i **ważność** **reguły alertu**.

7. Kliknij przycisk **Utwórz regułę alertu** , aby utworzyć alert.

> [!NOTE]  
> Jeśli tworzysz alert i jest zbyt dużo szumu, Dostosuj wartość progową i logikę alertu.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Jak utworzyć alert, jeśli udział plików jest ograniczany

1. Przejdź do swojego **konta magazynu** w **Azure Portal**.
2. W sekcji **monitorowanie** kliknij pozycję **alerty**, a następnie kliknij pozycję **+ Nowa reguła alertów**.
3. Kliknij pozycję **Edytuj zasób**, wybierz **Typ zasobu pliku** dla konta magazynu, a następnie kliknij pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to `contoso` , wybierz `contoso/file` zasób.
4. Kliknij pozycję **Wybierz warunek** , aby dodać warunek.
5. Zostanie wyświetlona lista sygnałów obsługiwanych przez konto magazynu, wybierz metrykę **transakcji** .
6. W bloku **Konfigurowanie logiki sygnału** kliknij listę rozwijaną **Nazwa wymiaru** i wybierz pozycję **Typ odpowiedzi**.
7. Kliknij listę rozwijaną **wartości wymiaru** i wybierz pozycję **SuccessWithThrottling** (dla protokołu SMB) lub **ClientThrottlingError** (dla opcji REST).

   > [!NOTE]
   > Jeśli wartość wymiaru SuccessWithThrottling lub ClientThrottlingError nie znajduje się na liście, oznacza to, że zasób nie został ograniczony. Aby dodać wartość wymiaru, kliknij pozycję **Dodaj wartość niestandardową** obok listy rozwijanej **wartości wymiaru** , wpisz **SuccessWithThrottling** lub **ClientThrottlingError**, kliknij przycisk **OK** , a następnie powtórz krok #7.

8. Kliknij listę rozwijaną **Nazwa wymiaru** i wybierz pozycję **udział plików**.
9. Kliknij listę rozwijaną **wartości wymiaru** i wybierz udziały plików, dla których chcesz utworzyć alert.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, zaznacz **wszystkie bieżące i przyszłe wartości**. Lista rozwijana wartości wymiarów nie będzie wyświetlać udziałów plików, ponieważ metryki dla udziałów nie są dostępne dla standardowych udziałów plików. Alerty dotyczące ograniczania przepustowości dla standardowych udziałów plików będą wyzwalane, jeśli jakikolwiek udział plików w ramach konta magazynu zostanie ograniczony, a alert nie określi, który udział plików został ograniczony. Ponieważ metryki dla poszczególnych udziałów nie są dostępne dla standardowych udziałów plików, zalecenie ma mieć jeden udział plików na konto magazynu.

10. Zdefiniuj **Parametry alertu** (wartość progowa, operator, stopień szczegółowości agregacji i częstotliwość oceny), a następnie kliknij pozycję **gotowe**.

    > [!TIP]
    > Jeśli jest używany próg statyczny, wykres metryk może pomóc w ustaleniu rozsądnej wartości progowej, jeśli udział plików jest obecnie ograniczany. Jeśli używasz progu dynamicznego, wykres metryki wyświetli obliczone progi na podstawie ostatnich danych.

11. Kliknij pozycję **Wybierz grupę akcji** , aby dodać do alertu **grupę akcji** (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
12. Wypełnij **szczegóły alertu** , takie jak **Nazwa reguły alertu**, * * opis i **ważność**.
13. Kliknij przycisk **Utwórz regułę alertu** , aby utworzyć alert.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Jak utworzyć alert, jeśli rozmiar udziału plików platformy Azure wynosi 80% pojemności

1. Przejdź do swojego **konta magazynu** w **Azure Portal**.
2. W sekcji **monitorowanie** kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertów**.
3. Kliknij pozycję **Edytuj zasób**, wybierz **Typ zasobu pliku** dla konta magazynu, a następnie kliknij pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to `contoso` , wybierz `contoso/file` zasób.
4. Kliknij pozycję **Wybierz warunek** , aby dodać warunek.
5. Zostanie wyświetlona lista sygnałów obsługiwanych przez konto magazynu, wybierz metrykę **pojemności pliku** .
6. W bloku **Konfigurowanie logiki sygnału** kliknij listę rozwijaną **Nazwa wymiaru** i wybierz pozycję **udział plików**.
7. Kliknij listę rozwijaną **wartości wymiaru** i wybierz udziały plików, dla których chcesz utworzyć alert.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, zaznacz **wszystkie bieżące i przyszłe wartości**. Lista rozwijana wartości wymiarów nie będzie wyświetlać udziałów plików, ponieważ metryki dla udziałów nie są dostępne dla standardowych udziałów plików. Alerty dla standardowych udziałów plików są oparte na wszystkich udziałach plików na koncie magazynu. Ponieważ metryki dla poszczególnych udziałów nie są dostępne dla standardowych udziałów plików, zalecenie ma mieć jeden udział plików na konto magazynu.

8. Wprowadź **wartość progową** w bajtach. Jeśli na przykład rozmiar udziału plików to 100 TiB i chcesz otrzymywać alert, gdy rozmiar udziału plików wynosi 80% pojemności, wartość progowa w bajtach to 87960930222080.
9. Zdefiniuj pozostałe **Parametry alertu** (stopień szczegółowości agregacji i częstotliwość oceny), a następnie kliknij przycisk **gotowe**.
10. Kliknij pozycję Wybierz grupę akcji, aby dodać do alertu grupę akcji (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
11. Wypełnij **szczegóły alertu** , takie jak **Nazwa reguły alertu**, * * opis i **ważność**.
12. Kliknij przycisk **Utwórz regułę alertu** , aby utworzyć alert.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Jak utworzyć alert, jeśli udział plików platformy Azure przekroczył 500 GiB w ciągu dnia

1. Przejdź do swojego **konta magazynu** w **Azure Portal**.
2. W sekcji monitorowanie kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertów**.
3. Kliknij pozycję **Edytuj zasób**, wybierz **Typ zasobu pliku** dla konta magazynu, a następnie kliknij pozycję **gotowe**. Jeśli na przykład nazwa konta magazynu to contoso, wybierz zasób contoso/File.
4. Kliknij pozycję **Wybierz warunek** , aby dodać warunek.
5. Zostanie wyświetlona lista sygnałów obsługiwanych przez konto magazynu, wybierz metrykę **ruchu** wychodzącego.
6. W bloku **Konfigurowanie logiki sygnału** kliknij listę rozwijaną **Nazwa wymiaru** i wybierz pozycję **udział plików**.
7. Kliknij listę rozwijaną **wartości wymiaru** i wybierz udziały plików, dla których chcesz utworzyć alert.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, zaznacz **wszystkie bieżące i przyszłe wartości**. Lista rozwijana wartości wymiarów nie będzie wyświetlać udziałów plików, ponieważ metryki dla udziałów nie są dostępne dla standardowych udziałów plików. Alerty dla standardowych udziałów plików są oparte na wszystkich udziałach plików na koncie magazynu. Ponieważ metryki dla poszczególnych udziałów nie są dostępne dla standardowych udziałów plików, zalecenie ma mieć jeden udział plików na konto magazynu.

8. Wprowadź **536870912000** bajtów dla wartości progowej. 
9. Kliknij listę rozwijaną **stopień szczegółowości agregacji** i wybierz pozycję **24 godziny**.
10. Wybierz **częstotliwość oceny** i **kliknij przycisk Gotowe**.
11. Kliknij pozycję **Wybierz grupę akcji** , aby dodać do alertu **grupę akcji** (wiadomości e-mail, wiadomości SMS itp.), wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.
12. Wypełnij **szczegóły alertu** , takie jak **Nazwa reguły alertu**, * * opis i **ważność**.
13. Kliknij przycisk **Utwórz regułę alertu** , aby utworzyć alert.

## <a name="next-steps"></a>Następne kroki

- [Informacje o danych monitorowania Azure Files](storage-files-monitoring.md)
- [Monitoruj zasoby platformy Azure za pomocą Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md)
- [Migracja metryk usługi Azure Storage](../common/storage-metrics-migration.md)
- [Planowanie wdrożenia usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Jak wdrożyć usługę Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Linux](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
