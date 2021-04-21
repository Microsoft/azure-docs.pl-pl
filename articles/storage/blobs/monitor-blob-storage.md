---
title: Monitorowanie Azure Blob Storage | Microsoft Docs
description: Dowiedz się, jak monitorować wydajność i dostępność Azure Blob Storage. Monitoruj Azure Blob Storage, dowiedz się więcej o konfiguracji oraz analizuj metryki i dane dziennika.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: subject-monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 464b5a6fddb724500e27a4b7d5e35fd84549565b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771627"
---
# <a name="monitoring-azure-blob-storage"></a>Monitorowanie Azure Blob Storage

Jeśli masz krytyczne aplikacje i procesy biznesowe, które opierają się na zasobach platformy Azure, chcesz monitorować te zasoby pod względu na ich dostępność, wydajność i działanie. W tym artykule opisano dane monitorowania generowane przez Azure Blob Storage oraz sposób używania funkcji usługi Azure Monitor do analizowania alertów dotyczących tych danych.

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów blob (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Ta funkcja jest dostępna dla wszystkich kont magazynu utworzonych za pomocą Azure Resource Manager modelu wdrażania. Zobacz [Omówienie konta magazynu.](../common/storage-account-overview.md)

## <a name="monitor-overview"></a>Omówienie monitorowania

Strona **Przegląd** w Azure Portal dla każdego zasobu usługi Blob Storage zawiera krótki widok użycia zasobów, taki jak żądania i rozliczenia godzinowe. Te informacje są przydatne, ale dostępna jest tylko niewielka ilość danych monitorowania. Niektóre z tych danych są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu zasobu. Dodatkowe typy zbierania danych można włączyć przy użyciu konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Azure Blob Storage tworzy dane monitorowania przy [użyciu](../../azure-monitor/overview.md)Azure Monitor , która jest usługą monitorowania pełnego stosu na platformie Azure. Azure Monitor udostępnia kompletny zestaw funkcji do monitorowania zasobów i zasobów platformy Azure w innych chmurach i lokalnie. 

Zacznij od artykułu [Monitorowanie zasobów platformy Azure za pomocą Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md) który opisuje następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Dane monitorowania zebrane na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure do analizowania danych monitorowania i tworzenia alertów dotyczących tych danych

Poniższe sekcje są kompilowane w oparciu o ten artykuł, opisując konkretne dane zebrane z usługi Azure Storage. Przykłady pokazują, jak skonfigurować zbieranie danych i analizować te dane za pomocą narzędzi platformy Azure.

## <a name="monitoring-data"></a>Dane monitorowania

Azure Blob Storage zbiera te same rodzaje danych monitorowania co inne zasoby platformy Azure, które opisano w tece Monitorowanie danych [z zasobów platformy Azure.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Zobacz [Azure Blob Storage danych monitorowania,](monitor-blob-storage-reference.md) aby uzyskać szczegółowe informacje na temat metryk i dzienników utworzonych przez Azure Blob Storage.

Metryki i dzienniki w Azure Monitor obsługują tylko Azure Resource Manager magazynu. Azure Monitor nie obsługuje klasycznych kont magazynu. Jeśli chcesz używać metryk lub dzienników na klasycznym koncie magazynu, musisz przeprowadzić migrację do Azure Resource Manager magazynu. Zobacz [Migrowanie do Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Jeśli chcesz, możesz nadal używać klasycznych metryk i dzienników. W rzeczywistości klasyczne metryki i dzienniki są dostępne równolegle z metrykami i dziennikami w Azure Monitor. Obsługa pozostaje w miejscu do momentu zakończenia usługi Azure Storage dla starszych metryk i dzienników.

## <a name="collection-and-routing"></a>Zbieranie i routing

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawienia diagnostycznego. 

Aby zbierać dzienniki zasobów, należy utworzyć ustawienie diagnostyczne. Podczas tworzenia ustawienia wybierz obiekt **blob** jako typ magazynu, dla którego chcesz włączyć dzienniki. Następnie określ jedną z następujących kategorii operacji, dla których chcesz zbierać dzienniki. 

| Kategoria | Opis |
|:---|:---|
| StorageRead | Operacje odczytu na obiektach. |
| StorageWrite | Operacje zapisu na obiektach. |
| StorageDelete | Usuwanie operacji na obiektach. |

> [!NOTE]
> Data Lake Storage Gen2 nie jest wyświetlany jako typ magazynu. Wynika to z Data Lake Storage Gen2 to zestaw możliwości dostępnych dla usługi Blob Storage. 

## <a name="creating-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego

Ustawienie diagnostyczne można utworzyć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Resource Manager szablonu. 

Aby uzyskać ogólne wskazówki, zobacz Create diagnostic setting to collect platform logs and metrics in Azure (Tworzenie ustawienia diagnostycznego w celu zbierania dzienników i [metryk platformy na platformie Azure).](../../azure-monitor/essentials/diagnostic-settings.md)

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów blob (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Ta funkcja jest dostępna dla wszystkich kont magazynu utworzonych za pomocą Azure Resource Manager modelu wdrażania. Zobacz [Omówienie konta magazynu.](../common/storage-account-overview.md)

### <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. Zaloguj się w witrynie Azure Portal.

2. Przejdź do konta magazynu.

3. W sekcji **Monitorowanie** kliknij pozycję **Ustawienia diagnostyczne (wersja zapoznawcza).**

   > [!div class="mx-imgBorder"]
   > ![portal — dzienniki diagnostyczne](media/monitor-blob-storage/diagnostic-logs-settings-pane.png)   

4. Wybierz **obiekt blob** jako typ magazynu, dla którego chcesz włączyć dzienniki.

5. Kliknij **pozycję Dodaj ustawienie diagnostyczne.**

   > [!div class="mx-imgBorder"]
   > ![portal — dzienniki zasobów — dodawanie ustawienia diagnostycznego](media/monitor-blob-storage/diagnostic-logs-settings-pane-2.png)

   Zostanie **wyświetlona strona Ustawienia** diagnostyczne.

   > [!div class="mx-imgBorder"]
   > ![Strona dzienników zasobów](media/monitor-blob-storage/diagnostic-logs-page.png)

6. W **polu** Nazwa strony wprowadź nazwę dla tego ustawienia Dziennik zasobów. Następnie wybierz operacje, które mają być rejestrowane (operacje odczytu, zapisu i usuwania) oraz miejsce, do którego mają być wysyłane dzienniki.

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się zarchiwizować dzienniki na koncie magazynu, zapłacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Zaznacz pole **wyboru Zarchiwizuj na koncie** magazynu, a następnie kliknij **przycisk** Konfiguruj.

   > [!div class="mx-imgBorder"]   
   > ![Magazyn archiwum strony ustawień diagnostycznych](media/monitor-blob-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. Z **listy rozwijanej** Konto magazynu wybierz konto magazynu, na którym chcesz zarchiwizować dzienniki, kliknij przycisk **OK,** a następnie kliknij **przycisk** Zapisz.

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Przed wybraniem konta magazynu jako miejsca docelowego eksportu zobacz Archiwizowanie dzienników zasobów [platformy Azure,](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) aby poznać wymagania wstępne dotyczące konta magazynu.

#### <a name="stream-logs-to-azure-event-hubs"></a>Przesyłanie strumieniowe dzienników do Azure Event Hubs

Jeśli zdecydujesz się na przesyłanie strumieniowe dzienników do centrum zdarzeń, zapłacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Zaznacz pole **wyboru Przesyłaj strumieniowo do centrum** zdarzeń, a następnie kliknij **przycisk** Konfiguruj.

2. W **okienku Wybierz centrum zdarzeń** wybierz przestrzeń nazw, nazwę i nazwę zasad centrum zdarzeń, do którego chcesz przesyłać strumieniowo dzienniki. 

   > [!div class="mx-imgBorder"]
   > ![Centrum zdarzeń strony ustawień diagnostycznych](media/monitor-blob-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Kliknij przycisk **OK,** a następnie kliknij **przycisk** Zapisz.

#### <a name="send-logs-to-azure-log-analytics"></a>Wysyłanie dzienników do usługi Azure Log Analytics

1. Zaznacz pole **wyboru Wyślij do usługi Log Analytics,** wybierz obszar roboczy usługi Log Analytics, a następnie kliknij **przycisk** Zapisz.

   > [!div class="mx-imgBorder"]   
   > ![Analiza dzienników stron ustawień diagnostycznych](media/monitor-blob-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Otwórz Windows PowerShell polecenia i zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia . Następnie postępuj zgodnie z instrukcjami na ekranie.

   ```powershell
   Connect-AzAccount
   ```

2. Ustaw aktywną subskrypcję na subskrypcję konta magazynu, dla którego chcesz włączyć rejestrowanie.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się zarchiwizować dzienniki na koncie magazynu, zapłacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Włącz dzienniki przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell wraz z `StorageAccountId` parametrem .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log>
```

Zastąp symbol `<storage-service-resource--id>` zastępczy w tym fragmencie kodu identyfikatorem zasobu usługi blob. Identyfikator zasobu można znaleźć w Azure Portal, otwierając **stronę** Właściwości konta magazynu.

Jako wartości parametru Category można użyć wartości `StorageRead` `StorageWrite` , i `StorageDelete` . 

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Aby uzyskać opis każdego parametru, zobacz Archiwizowanie [dzienników zasobów platformy Azure za pośrednictwem Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

Jeśli zdecydujesz się przesłać strumieniowo dzienniki do centrum zdarzeń, zapłacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Włącz dzienniki za pomocą polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell z `EventHubAuthorizationRuleId` parametrem .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Aby uzyskać opis każdego parametru, zobacz [Stream Data to Event Hubs via PowerShell cmdlets (Przesyłanie](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)strumieniowe danych do usługi Event Hubs za pomocą poleceń cmdlet programu PowerShell).

#### <a name="send-logs-to-log-analytics"></a>Wysyłanie dzienników do usługi Log Analytics

Włącz dzienniki przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell z `WorkspaceId` parametrem .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Aby uzyskać więcej informacji, zobacz Stream Azure Resource Logs to Log Analytics workspace in Azure Monitor (Przesyłanie strumieniowe dzienników zasobów platformy Azure do obszaru roboczego usługi [Log Analytics Azure Monitor).](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Najpierw otwórz aplikację [Azure Cloud Shell](../../cloud-shell/overview.md), a jeśli [](/cli/azure/install-azure-cli) interfejs wiersza polecenia platformy Azure został zainstalowany lokalnie, otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Jeśli tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, dla którego chcesz włączyć dzienniki.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp wartość `<subscription-id>` symbolu zastępczego identyfikatorem subskrypcji.

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się zarchiwizować dzienniki na koncie magazynu, zapłacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Włącz dzienniki za pomocą [polecenia az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true }]'
```

Zastąp symbol `<storage-service-resource--id>` zastępczy w tym fragmencie kodu identyfikatorem zasobu Blob Storage. Identyfikator zasobu można znaleźć w Azure Portal, otwierając **stronę** Właściwości konta magazynu.

Jako wartości parametru kategorii można użyć `StorageRead` `StorageWrite` wartości , i `StorageDelete` . 

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite}]'`

Opis każdego parametru zawiera temat Archive [Resource logs via the Azure CLI (Archiwizowanie dzienników zasobów za pośrednictwem interfejsu wiersza polecenia platformy Azure).](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

Jeśli zdecydujesz się na przesyłanie strumieniowe dzienników do centrum zdarzeń, zapłacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Włącz dzienniki za pomocą [polecenia az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Opis każdego parametru można znaleźć w tesłudze Stream data to Event Hubs via Azure CLI (Przesyłanie strumieniowe danych [Event Hubs interfejsu wiersza polecenia platformy Azure).](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Wysyłanie dzienników do usługi Log Analytics

Włącz dzienniki za pomocą [polecenia az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/blobServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Aby uzyskać więcej informacji, zobacz Stream Azure Resource Logs to Log Analytics workspace in Azure Monitor (Przesyłanie strumieniowe dzienników zasobów platformy Azure do obszaru roboczego usługi [Log Analytics Azure Monitor).](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

### <a name="template"></a>[Szablon](#tab/template)

Aby wyświetlić szablon Azure Resource Manager, który tworzy ustawienie diagnostyczne, zobacz [Ustawienie diagnostyczne dla usługi Azure Storage.](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)

---

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki usługi Azure Storage można analizować za pomocą metryk z innych usług platformy Azure przy użyciu Eksplorator metryk. Otwórz Eksplorator metryk, wybierając **pozycję Metryki** z **Azure Monitor** menu. Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz Getting started with Azure Eksplorator metryk (Wprowadzenie [do usługi Azure Eksplorator metryk).](../../azure-monitor/essentials/metrics-getting-started.md) 

W tym przykładzie **pokazano, jak wyświetlić transakcje** na poziomie konta.

![Zrzut ekranu przedstawiający uzyskiwanie dostępu do metryk w Azure Portal](./media/monitor-blob-storage/access-metrics-portal.png)

W przypadku metryk, które obsługują wymiary, można filtrować metrykę przy użyciu żądanej wartości wymiaru. W tym przykładzie pokazano, jak wyświetlić **transakcje** na poziomie konta dla określonej operacji, wybierając wartości wymiaru **Nazwa interfejsu API.**

![Zrzut ekranu przedstawiający uzyskiwanie dostępu do metryk za pomocą wymiaru w Azure Portal](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Aby uzyskać pełną listę wymiarów, które obsługuje usługa Azure Storage, zobacz [Metrics dimensions (Wymiary metryk).](monitor-blob-storage-reference.md#metrics-dimensions)

Metryki dla Azure Blob Storage znajdują się w tych przestrzeniach nazw: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices

Aby uzyskać listę wszystkich metryk Azure Monitor, w tym metryki Azure Blob Storage, zobacz [Azure Monitor obsługiwanych metryk.](../../azure-monitor/essentials/metrics-supported.md)


### <a name="accessing-metrics"></a>Uzyskiwanie dostępu do metryk

> [!TIP]
> Aby wyświetlić przykłady interfejsu wiersza polecenia platformy Azure lub platformy .NET, wybierz odpowiednie karty wymienione tutaj.

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor udostępnia zestaw [SDK platformy .NET do](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) odczytywania definicji i wartości metryk. Przykładowy [kod pokazuje,](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) jak używać zestawu SDK z różnymi parametrami. Metryk magazynu należy `0.18.0-preview` użyć lub nowszej wersji.
 
W tych przykładach zastąp symbol zastępczy identyfikatorem zasobu całego konta magazynu lub `<resource-ID>` usługi Blob Storage. Te identyfikatory zasobów można znaleźć na **stronach** Właściwości konta magazynu w Azure Portal.

Zastąp `<subscription-ID>` zmienną identyfikatorem subskrypcji. Aby uzyskać wskazówki dotyczące sposobu uzyskiwania wartości dla , i , zobacz Use the portal to create an Azure AD application and service principal that can access resources (Używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi, które mogą uzyskiwać `<tenant-ID>` `<application-ID>` dostęp do `<AccessKey>` [zasobów).](../../active-directory/develop/howto-create-service-principal-portal.md) 

#### <a name="list-the-account-level-metric-definition"></a>Lista definicji metryki na poziomie konta

W poniższym przykładzie pokazano, jak wyświetlić definicję metryki na poziomie konta:

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

Poniższy przykład pokazuje, jak `UsedCapacity` odczytywać dane na poziomie konta:

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

#### <a name="reading-multidimensional-metric-values"></a>Odczytywanie wielowymiarowych wartości metryk

W przypadku metryk wielowymiarowych należy zdefiniować filtry metadanych, jeśli chcesz odczytywać dane metryk dla określonych wartości wymiarów.

W poniższym przykładzie pokazano, jak odczytywać dane metryki dotyczące metryki obsługi wielowymiarowej:

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

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista definicji metryki

Możesz wyświetlić definicję metryki konta magazynu lub usługi Blob Storage. Użyj polecenia cmdlet [Get-AzMetricDefinition.](/powershell/module/az.monitor/get-azmetricdefinition)

W tym przykładzie zastąp symbol zastępczy identyfikatorem zasobu całego konta magazynu lub `<resource-ID>` identyfikatorem zasobu usługi Blob Storage.  Te identyfikatory zasobów można znaleźć na **stronach Właściwości** konta magazynu w Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Odczytywanie wartości metryk

Możesz odczytywać wartości metryk na poziomie konta magazynu lub usługi Blob Storage. Użyj polecenia cmdlet [Get-AzMetric.](/powershell/module/Az.Monitor/Get-AzMetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Lista definicji metryki na poziomie konta

Możesz wyświetlić definicję metryki konta magazynu lub usługi Blob Storage. Użyj [polecenia az monitor metrics list-definitions.](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
 
W tym przykładzie zastąp symbol zastępczy identyfikatorem zasobu całego konta magazynu lub `<resource-ID>` identyfikatorem zasobu usługi Blob Storage. Te identyfikatory zasobów można znaleźć na **stronach Właściwości** konta magazynu w Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Odczytywanie wartości metryk na poziomie konta

Możesz odczytywać wartości metryk konta magazynu lub usługi Blob Storage. Użyj [polecenia az monitor metrics list.](/cli/azure/monitor/metrics#az_monitor_metrics_list)

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```
### <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy.

---

## <a name="analyzing-logs"></a>Analizowanie dzienników

Dostęp do dzienników zasobów można uzyskać jako obiekt blob na koncie magazynu, jako dane zdarzenia lub za pośrednictwem zapytań usługi Log Analytic.

Aby uzyskać szczegółowe informacje dotyczące pól wyświetlanych w tych dziennikach, zobacz [Azure Blob Storage danych monitorowania](monitor-blob-storage-reference.md).

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów blob (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont usługi Premium Storage w wersji 1 ogólnego przeznaczenia oraz kont magazynu ogólnego przeznaczenia w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

Wpisy dziennika są tworzone tylko w przypadku żądań względem punktu końcowego usługi. Jeśli na przykład konto magazynu ma działanie w punkcie końcowym obiektu blob, ale nie w jego punktach końcowych tabeli lub kolejki, tworzone są tylko dzienniki odnoszące się do usługi blob. Dzienniki usługi Azure Storage zawierają szczegółowe informacje o żądaniach do usługi magazynu, które zakończyły się powodzeniem i niepowodzeniem. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na zasadzie jak najostrowiej.

### <a name="log-authenticated-requests"></a>Rejestrowanie uwierzytelnionych żądań

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania, które używają sygnatury dostępu współdzielonego (SAS) lub protokołu OAuth, w tym żądania nieudane i pomyślne
- Żądania do danych analitycznych (klasyczne dane dziennika w **kontenerze $logs** dane metryk klasy w **tabelach** $metric danych)

Żądania wykonane przez samą usługę Blob Storage, takie jak tworzenie lub usuwanie dzienników, nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych danych, zobacz [Storage logged operations and status messages (Zarejestrowane](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) operacje i komunikaty o stanie magazynu) oraz [Storage log format (Format dziennika magazynu).](monitor-blob-storage-reference.md)

### <a name="log-anonymous-requests"></a>Rejestrowanie anonimowych żądań

 Rejestrowane są następujące typy anonimowych żądań:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy przechyłek czasu dla klienta i serwera
- Nieudane żądania GET z kodem błędu 304 (Nie zmodyfikowano)

Wszystkie inne żądania anonimowe, które zakończyły się niepowodzeniem, nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych danych, zobacz [Storage logged operations and status messages (Zarejestrowane](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) operacje i komunikaty o stanie magazynu) oraz [Storage log format (Format dziennika magazynu).](monitor-blob-storage-reference.md)

### <a name="accessing-logs-in-a-storage-account"></a>Uzyskiwanie dostępu do dzienników na koncie magazynu

Dzienniki są wyświetlane jako obiekty blob przechowywane w kontenerze na docelowym koncie magazynu. Dane są zbierane i przechowywane wewnątrz pojedynczego obiektu blob jako rozdzielany wierszami ładunek JSON. Nazwa obiektu blob jest zgodna z następującą konwencją nazewnictwa:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Oto przykład:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Uzyskiwanie dostępu do dzienników w centrum zdarzeń

Dzienniki wysyłane do centrum zdarzeń nie są przechowywane jako plik, ale można sprawdzić, czy centrum zdarzeń odebrało informacje dziennika. W Azure Portal przejdź do centrum zdarzeń i sprawdź, czy **liczba** komunikatów przychodzących jest większa niż zero. 

![Dzienniki inspekcji](media/monitor-blob-storage/event-hub-log.png)

Możesz uzyskać dostęp do danych dziennika wysyłanych do centrum zdarzeń i odczytywać je przy użyciu informacji o zabezpieczeniach oraz narzędzi do zarządzania zdarzeniami i monitorowania. Aby uzyskać więcej informacji, zobacz [Co mogę zrobić z danymi monitorowania wysyłanymi do centrum zdarzeń?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Uzyskiwanie dostępu do dzienników w obszarze roboczym usługi Log Analytics

Dostęp do dzienników wysyłanych do obszaru roboczego usługi Log Analytics można uzyskać przy użyciu Azure Monitor dziennika.

Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Log Analytics w Azure Monitor](../../azure-monitor/logs/log-analytics-tutorial.md).

Dane są przechowywane w **tabeli StorageBlobLog.** Dzienniki Data Lake Storage Gen2 nie są wyświetlane w dedykowanej tabeli. Wynika to z tego, Data Lake Storage Gen2 nie jest usługą. Jest to zestaw funkcji, które można włączyć na koncie magazynu. Jeśli włączono te możliwości, dzienniki będą nadal wyświetlane w tabeli StorageBlobLogs. 

#### <a name="sample-kusto-queries"></a>Przykładowe zapytania kusto

Oto kilka zapytań, które można  wprowadzić na pasku wyszukiwania dzienników, aby ułatwić monitorowanie magazynu obiektów blob. Te zapytania działają z [nowym językiem](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Po wybraniu pozycji **Dzienniki** z menu grupy zasobów konta magazynu usługa Log Analytics zostanie otwarta z zakresem zapytania ustawionym na bieżącą grupę zasobów. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tej grupy zasobów. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych zasobów  lub danych z innych usług platformy Azure, wybierz pozycję Dzienniki z Azure Monitor **menu.** Aby [uzyskać szczegółowe informacje, zobacz](../../azure-monitor/logs/scope.md) Zakres i zakres czasu zapytania Azure Monitor Log Analytics.

Użyj tych zapytań, aby ułatwić monitorowanie kont usługi Azure Storage:

* Aby wyświetlić listę 10 najczęściej występujących błędów z ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Aby wyświetlić 10 pierwszych operacji, które spowodowały najwięcej błędów w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Aby wyświetlić listę 10 pierwszych operacji z najdłuższym opóźnieniem end-to-end w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Aby wyświetlić listę wszystkich operacji, które spowodowały błędy ograniczania przepustowości po stronie serwera w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Aby wyświetlić listę wszystkich żądań z dostępem anonimowym w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Tworzenie wykresu kołowego operacji używanych w ciągu ostatnich trzech dni.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>Często zadawane pytania

**Czy usługa Azure Storage obsługuje metryki dla dysków Dyski zarządzane lub dysków nieza zarządzania?**

Nie. Azure Compute obsługuje metryki na dyskach. Aby uzyskać więcej informacji, zobacz [Metryki dotyczące 1 dysku dla dysków zarządzanych i nieza zarządzanych.](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać odwołanie do dzienników i metryk utworzonych przez program Azure Blob Storage, zobacz [Azure Blob Storage danych monitorowania](monitor-blob-storage-reference.md).
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, [zobacz Monitorowanie zasobów platformy Azure za pomocą Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
- Aby uzyskać więcej informacji na temat migracji metryk, zobacz [Azure Storage metrics migration (Migracja metryk usługi Azure Storage).](../common/storage-metrics-migration.md)
