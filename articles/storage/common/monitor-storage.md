---
title: Monitorowanie usługi Azure Storage | Microsoft Docs
description: Dowiedz się, jak monitorować wydajność i dostępność usługi Azure Storage.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722923"
---
# <a name="monitoring-azure-storage"></a>Monitorowanie usługi Azure Storage

Jeśli masz krytyczne aplikacje i procesy biznesowe, które opierają się na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania, które są generowane przez usługę Azure Storage, oraz sposób używania funkcji Azure Monitor do analizowania alertów dotyczących tych danych.

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont usługi Premium Storage w ramach kont magazynu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

## <a name="monitor-overview"></a>Przegląd monitora

Na stronie **Przegląd** w Azure Portal poszczególnych zasobów magazynu znajduje się krótki widok użycia zasobów, w tym jego żądanie i opłaty godzinowe. Jest to przydatne informacje, ale dostępna jest tylko niewielka ilość danych monitorowania. Niektóre z tych danych są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu zasobu magazynu. Dodatkowe typy zbierania danych można włączyć za pomocą jakiejś konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Usługa Azure Storage tworzy dane monitorowania za pomocą [Azure monitor](../../azure-monitor/overview.md) , który jest pełną usługą monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych. 

Rozpocznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) , w którym opisano następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje dotyczą tego artykułu, opisując określone dane zebrane z usługi Azure Storage i dostarczając przykłady służące do konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

## <a name="monitoring-data-from-azure-storage"></a>Monitorowanie danych z usługi Azure Storage

Usługa Azure Storage zbiera te same dane monitorowania, jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Szczegółowe informacje o dziennikach i metrykach utworzonych przez usługę Azure Storage można znaleźć w temacie [Informacje o danych monitorowania usługi Azure Storage](monitor-storage-reference.md) .

Metryki i dzienniki w Azure Monitor obsługują tylko Azure Resource Manager kont magazynu. Azure Monitor nie obsługuje klasycznych kont magazynu. Jeśli chcesz użyć metryk lub dzienników na klasycznym koncie magazynu, musisz przeprowadzić migrację do konta magazynu Azure Resource Manager. Zobacz [Migrowanie do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Jeśli chcesz, możesz nadal korzystać z klasycznych metryk i dzienników. W rzeczywistości klasyczne metryki i dzienniki są dostępne równolegle z metrykami i dziennikami w Azure Monitor. Obsługa będzie obowiązywać do momentu zakończenia usługi Azure Storage w ramach starszych metryk i dzienników. 

### <a name="logs-in-azure-monitor-preview"></a>Dzienniki w Azure Monitor (wersja zapoznawcza)

Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi. Na przykład jeśli konto magazynu ma aktywność w swoim punkcie końcowym obiektu BLOB, ale nie znajduje się w jego punktach końcowych tabeli lub kolejki, zostanie utworzony tylko dziennik dotyczący usługi BLOB. Dzienniki usługi Azure Storage zawierają szczegółowe informacje na temat żądań zakończonych powodzeniem i zakończonych niepowodzeniem w usłudze magazynu. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku.

#### <a name="logging-authenticated-requests"></a>Rejestrowanie żądań uwierzytelnionych

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania przy użyciu sygnatury dostępu współdzielonego (SAS) lub protokołu OAuth, w tym żądania zakończone niepowodzeniem i zakończone powodzeniem
- Żądania danych analitycznych (klasyczne dane dziennika w kontenerze **$Logs** i dane metryk klas w tabelach **$Metric** )

Żądania wykonywane przez samą usługę magazynu, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w temacie [zarejestrowane operacje magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz [format dziennika magazynu](monitor-storage-reference.md) .

#### <a name="logging-anonymous-requests"></a>Rejestrowanie żądań anonimowych

 Rejestrowane są następujące typy żądań anonimowych:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy przekroczenia limitu czasu dla klienta i serwera
- Żądania GET zakończone niepowodzeniem z kodem błędu 304 (Nie zmodyfikowano)

Wszystkie inne Nieudane żądania anonimowe nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w temacie [zarejestrowane operacje magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz [format dziennika magazynu](monitor-storage-reference.md) .

## <a name="configuration"></a>Konfigurowanie

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale należy utworzyć ustawienie diagnostyczne, aby zbierać dzienniki zasobów lub przesyłać je dalej poza Azure Monitor. Zobacz [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki platformy i metryki na platformie Azure](../../azure-monitor/platform/diagnostic-settings.md) w celu uzyskania szczegółowego procesu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell.

Podczas tworzenia ustawień diagnostycznych należy wybrać typ magazynu, dla którego chcesz włączyć dzienniki (obiekt BLOB, kolejka, tabela, plik). W przypadku utworzenia ustawienia diagnostycznego w Azure Portal, można wybrać zasób z listy. Jeśli używasz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, musisz użyć identyfikatora zasobu typu magazyn. Identyfikator zasobu można znaleźć w Azure Portal, otwierając stronę **Właściwości** konta magazynu.

Należy również określić kategorie operacji do zbierania dzienników. Kategorie usługi Azure Storage są wymienione w poniższej tabeli:

| Kategoria | Opis |
|:---|:---|
| StorageRead | Operacje odczytu w obiektach Blob.  |
| StorageWrite | Operacje zapisu w obiektach Blob. |
| StorageDelete | Operacje usuwania obiektów BLOB. |

## <a name="analyzing-metric-data"></a>Analizowanie danych metryki

Metryki usługi Azure Storage można analizować za pomocą metryk z innych usług platformy Azure za pomocą Eksploratora metryk. Otwórz Eksploratora metryk, wybierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md) . 

Poniższy przykład pokazuje, jak wyświetlić **transakcje** na poziomie konta.

![zrzut ekranu przedstawiający dostęp do metryk w Azure Portal](./media/monitor-storage/access-metrics-portal.png)

W przypadku metryk obsługujących Wymiary można filtrować metrykę z odpowiednią wartością wymiaru. Poniższy przykład pokazuje, jak wyświetlić **transakcje** na poziomie konta w ramach określonej operacji, wybierając wartości dla wymiaru **nazwa interfejsu API** .

![zrzut ekranu przedstawiający dostęp do metryk z wymiarem w Azure Portal](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Aby uzyskać pełną listę wymiarów obsługiwanych przez usługę Azure Storage, zobacz [Dimension Metrics](monitor-storage-reference.md#metrics-dimensions).

Wszystkie metryki usługi Azure Storage znajdują się w następujących obszarach nazw:

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/blobServices
- Microsoft. Storage/storageAccounts/fileServices
- Microsoft. Storage/storageAccounts/queueServices
- Microsoft. Storage/storageAccounts/tableServices

Aby uzyskać listę wszystkich Azure Monitor metryki pomocy technicznej (w tym Azure Storage), zobacz [Azure monitor obsługiwane metryki](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Metryki dostępu

> [!TIP]
> Aby wyświetlić przykłady interfejsu wiersza polecenia platformy Azure lub platformy .NET, wybierz odpowiednią kartę poniżej.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista definicji metryk

Można wyświetlić listę definicji metryk konta magazynu lub pojedynczej usługi magazynu, takiej jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia cmdlet [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

W tym przykładzie Zastąp `<resource-ID>` symbol ZASTĘPCZy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu pojedynczej usługi magazynu, takim jak obiekt BLOB, plik, tabela lub usługa kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu na Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Odczytaj wartości metryk

Można odczytywać wartości metryk na poziomie konta magazynu lub pojedynczej usługi magazynu, takiej jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia cmdlet [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>Lista definicji metryki na poziomie konta

Można wyświetlić listę definicji metryk konta magazynu lub pojedynczej usługi magazynu, takiej jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia [AZ monitor Metric list-](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) Definitions.
 
W tym przykładzie Zastąp `<resource-ID>` symbol ZASTĘPCZy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu pojedynczej usługi magazynu, takim jak obiekt BLOB, plik, tabela lub usługa kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu na Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Odczytaj wartości metryk na poziomie konta

Można odczytać wartości metryk konta magazynu lub pojedynczej usługi magazynu, takiej jak obiekt BLOB, plik, tabela lub usługa kolejki. Użyj polecenia [AZ monitor Metric list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor udostępnia [zestaw .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) do odczytywania definicji metryk i wartości. [Przykładowy kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) pokazuje, jak używać zestawu SDK z innymi parametrami. Aby uzyskać metryki `0.18.0-preview` magazynu, należy użyć wersji lub nowszej.
 
W tych przykładach Zastąp `<resource-ID>` symbol ZASTĘPCZy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu pojedynczej usługi magazynu, takim jak obiekt BLOB, plik, tabela lub usługa kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu na Azure Portal.

Zastąp `<subscription-ID>` zmienną identyfikatorem subskrypcji.  Aby uzyskać `<tenant-ID>`wskazówki dotyczące uzyskiwania wartości dla,, i `<application-ID>` `<AccessKey>`, zobacz [How to: Use the Portal, aby utworzyć aplikację usługi Azure AD i nazwę główną usługi, która może uzyskiwać dostęp do zasobów](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-account-level-metric-definition"></a>Lista definicji metryki na poziomie konta

W poniższych przykładach pokazano, jak wyświetlić listę metryk na poziomie konta:

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

#### <a name="read-account-level-metric-values"></a>Odczytaj wartości metryk na poziomie konta

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

#### <a name="read-multi-dimensional-metric-values"></a>Odczytaj wartości wielowymiarowych metryk

W przypadku metryk wielowymiarowych należy zdefiniować filtr metadanych, jeśli chcesz odczytać dane metryki dla określonej wartości wymiaru.

Poniższy przykład pokazuje, jak odczytać dane metryki z obsługą wielowymiarowego:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
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

## <a name="analyzing-log-data"></a>Analizowanie danych dziennika

Można uzyskać dostęp do dzienników zasobów jako obiektów BLOB na koncie magazynu, jako dane zdarzenia lub za pomocą zapytań analitycznych dzienników.

Szczegółowe informacje o polach, które pojawiają się w tych dziennikach, można znaleźć w temacie [Informacje o danych monitorowania usługi Azure Storage](monitor-storage-reference.md) .

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Aby zarejestrować się w wersji zapoznawczej, zobacz [Tę stronę](https://www.microsoft.com).  Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont usługi Premium Storage w ramach kont magazynu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

### <a name="access-logs-in-a-storage-account"></a>Dostęp do dzienników na koncie magazynu

Dzienniki są wyświetlane jako obiekty blob przechowywane w kontenerze na docelowym koncie magazynu. Dane są zbierane i przechowywane w pojedynczym obiekcie BLOB jako ładunek JSON rozdzielany wierszami. Nazwa obiektu BLOB jest zgodna z następującą konwencją nazewnictwa:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Przykład:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>Dostęp do dzienników w centrum zdarzeń

Dzienniki wysyłane do centrum zdarzeń nie są przechowywane jako plik, ale można sprawdzić, czy centrum zdarzeń otrzymało informacje dziennika. W Azure Portal przejdź do centrum zdarzeń, a następnie sprawdź, czy liczba **przychodzących komunikatów** jest większa od zera. 

![Dzienniki inspekcji](media/monitor-storage/event-hub-log.png)

Możesz uzyskać dostęp do danych dziennika, które są wysyłane do centrum zdarzeń, i je odczytywać, korzystając z informacji o zabezpieczeniach i narzędzi do monitorowania. Aby uzyskać więcej informacji, zobacz [co można zrobić przy użyciu danych monitorowania wysyłanych do centrum zdarzeń?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-log-analytics-workspace"></a>Dostęp do dzienników w obszarze roboczym Log Analytics

Dostęp do dzienników wysyłanych do obszaru roboczego Log Analytics można uzyskać przy użyciu zapytań dziennika Azure Monitor.

Zobacz Rozpoczynanie [pracy z log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Dane są przechowywane w poniższych tabelach.

| Tabela | Opis |
|:---|:---|
|StorageBlobLogs | Dzienniki opisujące działanie w usłudze BLOB Storage. |
|StorageFileLogs | Dzienniki opisujące działanie w udziałach plików. |
|StorageQueueLogs | Dzienniki opisujące działanie w kolejkach.|
|StorageTableLogs| Dzienniki opisujące działanie w tabelach.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Log Analytics zapytań usługi Azure Storage w Azure Monitor

Poniżej przedstawiono niektóre zapytania, które można wprowadzić na pasku wyszukiwania **przeszukiwania dzienników** , aby ułatwić monitorowanie kont usługi Azure Storage. Te zapytania działają w [nowym języku](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Poniżej znajdują się zapytania, których można użyć do monitorowania kont usługi Azure Storage.

* Lista 10 najczęstszych błędów w ciągu ostatnich 3 dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Aby wyświetlić 10 najważniejszych operacji, które powodują najwięcej błędów w ciągu ostatnich 3 dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Aby wyświetlić 10 najważniejszych operacji z najdłuższym opóźnieniem zakończonym w ciągu ostatnich 3 dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Aby wyświetlić listę wszystkich operacji powodujących błędy ograniczania po stronie serwera w ciągu ostatnich 3 dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Aby wyświetlić listę wszystkich żądań z dostępem anonimowym w ciągu ostatnich 3 dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Aby utworzyć wykres kołowy operacji użytych w ciągu ostatnich 3 dni.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Często zadawane pytania

**Czy usługa Azure Storage obsługuje metryki dla dysków Managed Disks lub niezarządzanych?**

Nie, usługa Azure COMPUTE obsługuje metryki na dyskach. Aby uzyskać więcej informacji, zobacz [artykuł](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) .

## <a name="next-steps"></a>Następne kroki

- [Informacje o danych monitorowania usługi Azure Storage](monitor-storage-reference.md) dotyczące dokumentacji dzienników i metryk utworzonych przez usługę Azure Storage.
- [Monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) , aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure.
- [Migracja metryk usługi Azure Storage](./storage-metrics-migration.md)

