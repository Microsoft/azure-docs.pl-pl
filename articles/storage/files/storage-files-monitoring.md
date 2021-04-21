---
title: Monitorowanie Azure Files | Microsoft Docs
description: Dowiedz się, jak monitorować wydajność i dostępność Azure Files. Monitoruj Azure Files, dowiedz się więcej o konfiguracji oraz analizuj metryki i dane dzienników.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 3/02/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 620ee3bc5978da4b274aed9a412679ae0835f0b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759831"
---
# <a name="monitoring-azure-files"></a>Monitorowanie Azure Files

Jeśli masz krytyczne aplikacje i procesy biznesowe, które opierają się na zasobach platformy Azure, chcesz monitorować te zasoby pod względu na ich dostępność, wydajność i działanie. W tym artykule opisano dane monitorowania generowane przez program Azure Files oraz sposób użycia funkcji usługi Azure Monitor do analizowania alertów dotyczących tych danych.

## <a name="monitor-overview"></a>Omówienie monitorowania

Strona **Przegląd** w Azure Portal dla każdego zasobu Azure Files zawiera krótki widok użycia zasobów, taki jak żądania i rozliczenia godzinowe. Te informacje są przydatne, ale dostępna jest tylko niewielka ilość danych monitorowania. Niektóre z tych danych są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu zasobu. Dodatkowe typy zbierania danych można włączyć przy użyciu konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Azure Files tworzy dane monitorowania przy [użyciu](../../azure-monitor/overview.md)Azure Monitor , która jest usługą monitorowania pełnego stosu na platformie Azure. Azure Monitor udostępnia kompletny zestaw funkcji do monitorowania zasobów i zasobów platformy Azure w innych chmurach i lokalnie. 

Zacznij od artykułu [Monitorowanie zasobów platformy Azure za pomocą Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md), który opisuje następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure do analizowania danych monitorowania i tworzenia alertów

Poniższe sekcje są kompilowane w oparciu o ten artykuł, opisując konkretne dane zebrane z Azure Files. Przykłady pokazują, jak skonfigurować zbieranie danych i analizować te dane za pomocą narzędzi platformy Azure.

## <a name="monitoring-data"></a>Dane monitorowania

Azure Files zbiera te same rodzaje danych monitorowania co inne zasoby platformy Azure, które opisano w tesłudze [monitorowania danych z zasobów platformy Azure.](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Zobacz [Azure File monitoring data reference (Informacje](storage-files-monitoring-reference.md) o danych monitorowania plików platformy Azure), aby uzyskać szczegółowe informacje na temat metryk i dzienników utworzonych przez Azure Files.

Metryki i dzienniki w Azure Monitor obsługują tylko Azure Resource Manager magazynu. Azure Monitor nie obsługuje klasycznych kont magazynu. Jeśli chcesz używać metryk lub dzienników na klasycznym koncie magazynu, musisz przeprowadzić migrację do Azure Resource Manager magazynu. Zobacz [Migrowanie do Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

## <a name="collection-and-routing"></a>Zbieranie i routing

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawienia diagnostycznego. 

Aby zbierać dzienniki zasobów, należy utworzyć ustawienie diagnostyczne. Podczas tworzenia ustawienia wybierz **plik** jako typ magazynu, dla którego chcesz włączyć dzienniki. Następnie określ jedną z następujących kategorii operacji, dla których chcesz zbierać dzienniki. 

| Kategoria | Opis |
|:---|:---|
| StorageRead | Operacje odczytu na obiektach. |
| StorageWrite | Operacje zapisu na obiektach. |
| StorageDelete | Usuwanie operacji na obiektach. |

Aby uzyskać listę rejestrowanych operacji SMB i REST, zobacz Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Zarejestrowane operacje i komunikaty o stanie magazynu) oraz Azure Files danych [monitorowania](storage-files-monitoring-reference.md).

## <a name="creating-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego

Ustawienie diagnostyczne można utworzyć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Resource Manager szablonu.

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów blob (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Ta funkcja jest dostępna dla wszystkich kont magazynu utworzonych za pomocą Azure Resource Manager modelu wdrażania. Zobacz [Omówienie konta magazynu.](../common/storage-account-overview.md)

Aby uzyskać ogólne wskazówki, zobacz Create diagnostic setting to collect platform logs and metrics in Azure (Tworzenie ustawienia diagnostycznego w celu zbierania dzienników i [metryk platformy na platformie Azure).](../../azure-monitor/essentials/diagnostic-settings.md)

### <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. Zaloguj się w witrynie Azure Portal.

2. Przejdź do konta magazynu.

3. W sekcji **Monitorowanie** kliknij pozycję **Ustawienia diagnostyczne (wersja zapoznawcza).**

   > [!div class="mx-imgBorder"]
   > ![portal — dzienniki diagnostyczne](media/storage-files-monitoring/diagnostic-logs-settings-pane.png)   

4. Wybierz **plik** jako typ magazynu, dla którego chcesz włączyć dzienniki.

5. Kliknij **pozycję Dodaj ustawienie diagnostyczne.**

   > [!div class="mx-imgBorder"]
   > ![portal — dzienniki zasobów — dodawanie ustawienia diagnostycznego](media/storage-files-monitoring/diagnostic-logs-settings-pane-2.png)

   Zostanie **wyświetlona strona Ustawienia** diagnostyczne.

   > [!div class="mx-imgBorder"]
   > ![Strona dzienników zasobów](media/storage-files-monitoring/diagnostic-logs-page.png)

6. W **polu** Nazwa strony wprowadź nazwę dla tego ustawienia Dziennik zasobów. Następnie wybierz operacje, które mają być rejestrowane (operacje odczytu, zapisu i usuwania) oraz miejsce, do którego mają być wysyłane dzienniki.

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się zarchiwizować dzienniki na koncie magazynu, zapłacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki platformy** na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Zaznacz pole **wyboru Zarchiwizuj na koncie** magazynu, a następnie kliknij **przycisk** Konfiguruj.

   > [!div class="mx-imgBorder"]   
   > ![Magazyn archiwum strony ustawień diagnostycznych](media/storage-files-monitoring/diagnostic-logs-settings-pane-archive-storage.png)

2. Z **listy rozwijanej** Konto magazynu wybierz konto magazynu, na którym chcesz zarchiwizować dzienniki, kliknij przycisk **OK,** a następnie kliknij przycisk **Zapisz.**

   [!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

   > [!NOTE]
   > Przed wybraniem konta magazynu jako miejsca docelowego eksportu zobacz Archiwizowanie dzienników zasobów [platformy Azure,](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) aby poznać wymagania wstępne dotyczące konta magazynu.

#### <a name="stream-logs-to-azure-event-hubs"></a>Przesyłanie strumieniowe dzienników do Azure Event Hubs

Jeśli zdecydujesz się na przesyłanie strumieniowe dzienników do centrum zdarzeń, zapłacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

1. Zaznacz pole **wyboru Przesyłaj strumieniowo do centrum** zdarzeń, a następnie kliknij **przycisk** Konfiguruj.

2. W **okienku Wybierz centrum zdarzeń** wybierz przestrzeń nazw, nazwę i nazwę zasad centrum zdarzeń, do którego chcesz przesyłać strumieniowo dzienniki. 

   > [!div class="mx-imgBorder"]
   > ![Centrum zdarzeń strony ustawień diagnostycznych](media/storage-files-monitoring/diagnostic-logs-settings-pane-event-hub.png)

3. Kliknij przycisk **OK,** a następnie kliknij **przycisk** Zapisz.

#### <a name="send-logs-to-azure-log-analytics"></a>Wysyłanie dzienników do usługi Azure Log Analytics

1. Zaznacz pole **wyboru Wyślij do usługi Log Analytics,** wybierz obszar roboczy usługi Log Analytics, a następnie kliknij przycisk **Zapisz.**

   > [!div class="mx-imgBorder"]   
   > ![Analiza dzienników stron ustawień diagnostycznych](media/storage-files-monitoring/diagnostic-logs-settings-pane-log-analytics.png)

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

Włącz dzienniki przy użyciu polecenia cmdlet programu PowerShell [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) wraz z `StorageAccountId` parametrem .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> 
```

Zastąp symbol `<storage-service-resource--id>` zastępczy w tym fragmencie kodu identyfikatorem zasobu usługi Azure File. Identyfikator zasobu można znaleźć w Azure Portal, otwierając **stronę** Właściwości konta magazynu.

Jako wartości parametru Category można użyć wartości `StorageRead` `StorageWrite` , i `StorageDelete` . 

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Aby uzyskać opis każdego parametru, zobacz Archiwizowanie [dzienników zasobów platformy Azure za pośrednictwem Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

Jeśli zdecydujesz się przesłać strumieniowo dzienniki do centrum zdarzeń, zapłacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki platformy** na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Włącz dzienniki przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell z `EventHubAuthorizationRuleId` parametrem .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Aby uzyskać opis każdego parametru, zobacz [Stream Data to Event Hubs via PowerShell cmdlets (Przesyłanie](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)strumieniowe danych do usługi Event Hubs za pomocą poleceń cmdlet programu PowerShell).

#### <a name="send-logs-to-log-analytics"></a>Wysyłanie dzienników do usługi Log Analytics

Włącz dzienniki przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell z `WorkspaceId` parametrem .

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Aby uzyskać więcej informacji, zobacz Stream Azure Resource Logs to Log Analytics workspace in Azure Monitor (Przesyłanie strumieniowe dzienników zasobów platformy Azure do obszaru roboczego usługi [Log Analytics Azure Monitor).](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Najpierw otwórz aplikację [Azure Cloud Shell](../../cloud-shell/overview.md)lub jeśli interfejs [](/cli/azure/install-azure-cli) wiersza polecenia platformy Azure został zainstalowany lokalnie, otwórz aplikację konsoli poleceń, taką jak Windows PowerShell.

2. Jeśli tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, dla którego chcesz włączyć dzienniki.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp wartość `<subscription-id>` symbolu zastępczego identyfikatorem subskrypcji.

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się zarchiwizować dzienniki na koncie magazynu, zapłacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki platformy** na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Włącz dzienniki za pomocą [polecenia az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true}]'
```

Zastąp symbol `<storage-service-resource--id>` zastępczy w tym fragmencie kodu usługą magazynu obiektów blob o identyfikatorze zasobu. Identyfikator zasobu można znaleźć w Azure Portal, otwierając **stronę** Właściwości konta magazynu.

Jako wartości parametru kategorii można użyć `StorageRead` `StorageWrite` wartości , i `StorageDelete` . 

[!INCLUDE [no retention policy](../../../includes/azure-storage-logs-retention-policy.md)]

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true}]'`

Opis każdego parametru zawiera temat [Archive Resource logs via the Azure CLI (Archiwizowanie dzienników zasobów za pośrednictwem interfejsu wiersza polecenia platformy Azure).](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

#### <a name="stream-logs-to-an-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

Jeśli zdecydujesz się na przesyłanie strumieniowe dzienników do centrum zdarzeń, zapłacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz **sekcję Dzienniki** platformy na [Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/#platform-logs)

Włącz dzienniki za pomocą [polecenia az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Opis każdego parametru można znaleźć w tesłudze Stream data to Event Hubs via Azure CLI (Przesyłanie strumieniowe danych [Event Hubs interfejsu wiersza polecenia platformy Azure).](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

#### <a name="send-logs-to-log-analytics"></a>Wysyłanie dzienników do usługi Log Analytics

Włącz dzienniki za pomocą [polecenia az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create)

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/fileServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Aby uzyskać więcej informacji, zobacz Stream Azure Resource Logs to Log Analytics workspace in Azure Monitor (Przesyłanie strumieniowe dzienników zasobów platformy Azure do obszaru roboczego usługi [Log Analytics Azure Monitor).](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

### <a name="template"></a>[Szablon](#tab/template)

Aby wyświetlić szablon Azure Resource Manager, który tworzy ustawienie diagnostyczne, zobacz [Ustawienie diagnostyczne dla usługi Azure Storage.](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage)

---

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki usługi Azure Storage można analizować za pomocą metryk z innych usług platformy Azure przy użyciu Eksplorator metryk. Otwórz Eksplorator metryk, wybierając **pozycję Metryki** z **Azure Monitor** menu. Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz Getting started with Azure Eksplorator metryk (Wprowadzenie [do usługi Azure Eksplorator metryk).](../../azure-monitor/essentials/metrics-getting-started.md) 

W przypadku metryk, które obsługują wymiary, możesz filtrować metrykę przy użyciu żądanej wartości wymiaru.  Aby uzyskać pełną listę wymiarów, które obsługuje usługa Azure Storage, zobacz [Metrics dimensions (Wymiary metryk).](storage-files-monitoring-reference.md#metrics-dimensions) Metryki dla Azure Files znajdują się w tych przestrzeniach nazw: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

Aby uzyskać listę wszystkich metryk Azure Monitor, w tym metryki Azure Files, [zobacz Azure Monitor obsługiwanych metryk.](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices)

### <a name="accessing-metrics"></a>Uzyskiwanie dostępu do metryk

> [!TIP]
> Aby wyświetlić przykłady interfejsu wiersza polecenia platformy Azure lub platformy .NET, wybierz odpowiednie karty wymienione tutaj.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista definicji metryki

Możesz wyświetlić definicję metryki konta magazynu lub Azure Files magazynu. Użyj polecenia cmdlet [Get-AzMetricDefinition.](/powershell/module/az.monitor/get-azmetricdefinition)

W tym przykładzie zastąp symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu usługi `<resource-ID>` Azure Files magazynu.  Te identyfikatory zasobów można znaleźć na **stronach** Właściwości konta magazynu w Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Odczytywanie wartości metryk

Możesz odczytywać wartości metryk na poziomie konta magazynu lub Azure Files magazynu. Użyj polecenia cmdlet [Get-AzMetric.](/powershell/module/Az.Monitor/Get-AzMetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Lista definicji metryki na poziomie konta

Możesz wyświetlić definicję metryki konta magazynu lub Azure Files magazynu. Użyj [polecenia az monitor metrics list-definitions.](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
 
W tym przykładzie zastąp symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu Azure Files `<resource-ID>` magazynu. Te identyfikatory zasobów można znaleźć na **stronach** Właściwości konta magazynu w Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Odczytywanie wartości metryk na poziomie konta

Możesz odczytać wartości metryk konta magazynu lub usługi Azure Files magazynu. Użyj [polecenia az monitor metrics list.](/cli/azure/monitor/metrics#az_monitor_metrics_list)

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor udostępnia zestaw [SDK platformy .NET do](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) odczytywania definicji i wartości metryk. Przykładowy [kod pokazuje,](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) jak używać zestawu SDK z różnymi parametrami. W przypadku metryk magazynu należy użyć lub `0.18.0-preview` nowszej wersji.
 
W tych przykładach zastąp symbol zastępczy identyfikatorem zasobu całego konta magazynu lub Azure Files `<resource-ID>` magazynu. Te identyfikatory zasobów można znaleźć na **stronach** Właściwości konta magazynu w Azure Portal.

Zastąp `<subscription-ID>` zmienną identyfikatorem subskrypcji. Aby uzyskać wskazówki dotyczące uzyskiwania wartości dla , i , zobacz Use the portal to create an Azure AD application and service principal that can access resources (Używanie portalu do tworzenia aplikacji usługi Azure AD i jednostki usługi, które mogą `<tenant-ID>` `<application-ID>` `<AccessKey>` [uzyskiwać dostęp do zasobów).](../../active-directory/develop/howto-create-service-principal-portal.md) 

#### <a name="list-the-account-level-metric-definition"></a>Lista definicji metryki na poziomie konta

W poniższym przykładzie pokazano, jak wyświetlić listę definicji metryki na poziomie konta:

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

# <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy.

---

## <a name="analyzing-logs"></a>Analizowanie dzienników

Dostęp do dzienników zasobów można uzyskać jako obiekt blob na koncie magazynu, jako dane zdarzenia lub za pośrednictwem zapytań usługi Log Analytic.

Aby uzyskać listę rejestrowanych operacji SMB i REST, zobacz Storage [logged operations and status messages](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) (Zarejestrowane operacje i komunikaty o stanie magazynu) oraz Azure Files danych [monitorowania](storage-files-monitoring-reference.md).

> [!NOTE]
> Dzienniki usługi Azure Storage w usłudze Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania w wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów blob (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont magazynu w chmurze Premium w przypadku kont ogólnego przeznaczenia w wersji 1 oraz kont magazynu ogólnego przeznaczenia w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania dotyczące punktu końcowego usługi. Jeśli na przykład konto magazynu ma działanie w punkcie końcowym pliku, ale nie w punktach końcowych tabeli lub kolejki, tworzone są tylko dzienniki dotyczące usługi Azure File. Dzienniki usługi Azure Storage zawierają szczegółowe informacje o żądaniach do usługi magazynu, które zakończyły się powodzeniem i niepowodzeniem. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie wszelkich wysiłków.

### <a name="log-authenticated-requests"></a>Rejestrowanie uwierzytelnionych żądań

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania, które używają protokołu Kerberos, NTLM lub sygnatury dostępu współdzielonego (SAS), w tym żądania, które zakończyły się niepowodzeniem i zakończyły się powodzeniem
- Żądania do danych analitycznych (klasyczne dane dziennika w **kontenerze $logs** i klasyczne dane metryk w **tabelach $metric** danych)

Żądania Azure Files, takie jak tworzenie lub usuwanie dzienników, nie są rejestrowane. Aby uzyskać pełną listę rejestrowanych żądań SMB i REST, zobacz Storage [logged operations](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) and status messages [(Zarejestrowane](storage-files-monitoring-reference.md)operacje i komunikaty o stanie magazynu) oraz Azure Files danych monitorowania .

### <a name="accessing-logs-in-a-storage-account"></a>Uzyskiwanie dostępu do dzienników na koncie magazynu

Dzienniki są wyświetlane jako obiekty blob przechowywane w kontenerze na docelowym koncie magazynu. Dane są zbierane i przechowywane wewnątrz pojedynczego obiektu blob jako rozdzielany wierszami ładunek JSON. Nazwa obiektu blob jest zgodna z następującą konwencją nazewnictwa:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Oto przykład:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Uzyskiwanie dostępu do dzienników w centrum zdarzeń

Dzienniki wysyłane do centrum zdarzeń nie są przechowywane jako plik, ale można sprawdzić, czy centrum zdarzeń odebrało informacje dziennika. W Azure Portal przejdź do centrum zdarzeń i sprawdź, czy **liczba** komunikatów przychodzących jest większa niż zero. 

![Dzienniki inspekcji](media/storage-files-monitoring/event-hub-log.png)

Możesz uzyskać dostęp do danych dziennika wysyłanych do centrum zdarzeń i odczytywać je przy użyciu informacji o zabezpieczeniach oraz narzędzi do zarządzania zdarzeniami i monitorowania. Aby uzyskać więcej informacji, zobacz [Co mogę zrobić z danymi monitorowania wysyłanymi do centrum zdarzeń?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Uzyskiwanie dostępu do dzienników w obszarze roboczym usługi Log Analytics

Dostęp do dzienników wysyłanych do obszaru roboczego usługi Log Analytics można uzyskać przy użyciu Azure Monitor dziennika. Dane są przechowywane w **tabeli StorageFileLogs.** 

Aby uzyskać więcej informacji, zobacz [Samouczek usługi Log Analytics.](../../azure-monitor/logs/log-analytics-tutorial.md)

#### <a name="sample-kusto-queries"></a>Przykładowe zapytania kusto

Oto kilka zapytań, które można  wprowadzić na pasku wyszukiwania w dzienniku, aby ułatwić monitorowanie Azure Files. Te zapytania działają z nowym [językiem](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Po wybraniu pozycji **Dzienniki** z menu grupy zasobów konta magazynu usługa Log Analytics zostanie otwarta z zakresem zapytania ustawionym na bieżącą grupę zasobów. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tej grupy zasobów. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych zasobów  lub danych z innych usług platformy Azure, wybierz pozycję Dzienniki z Azure Monitor **menu.** Aby [uzyskać szczegółowe informacje, zobacz](../../azure-monitor/logs/scope.md) Zakres i zakres czasu zapytania Azure Monitor Log Analytics.

Użyj tych zapytań, aby ułatwić monitorowanie udziałów plików platformy Azure:

- Wyświetlanie błędów SMB w ostatnim tygodniu

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Tworzenie wykresu kołowego operacji SMB w ostatnim tygodniu

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Wyświetlanie błędów REST w ostatnim tygodniu

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Tworzenie wykresu kołowego operacji REST w ostatnim tygodniu

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Aby wyświetlić listę nazw kolumn i opisów dla Azure Files, zobacz [StorageFileLogs](/azure/azure-monitor/reference/tables/storagefilelogs).

Aby uzyskać więcej informacji na temat pisania zapytań, zobacz [Samouczek usługi Log Analytics.](../../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="alerts"></a>Alerty

Azure Monitor proaktywnie powiadamiają użytkownika, gdy w danych monitorowania zostaną znalezione ważne warunki. Pozwalają one identyfikować i rozsyłać problemy w systemie, zanim klienci ich zauważą. Alerty można ustawić dla [metryk,](../../azure-monitor/alerts/alerts-metric-overview.md) [dzienników](../../azure-monitor/alerts/alerts-unified-log.md)i [dziennika aktywności.](../../azure-monitor/alerts/activity-log-alerts.md) 

W poniższej tabeli wymieniono kilka przykładowych scenariuszy monitorowania oraz odpowiednią metrykę do użycia dla alertu:

| Scenariusz | Metryka do użycia dla alertu |
|-|-|
| Udział plików jest ograniczany. | Metryka: Transakcje<br>Nazwa wymiaru: Typ odpowiedzi <br>Nazwa wymiaru: Udział plików (tylko udział plików Premium) |
| Rozmiar udziału plików to 80% pojemności. | Metryka: Pojemność pliku<br>Nazwa wymiaru: Udział plików (tylko udział plików Premium) |
| Ruch wychodzący udziału plików przekroczył 500 GiB w ciągu jednego dnia. | Metryka: Ruch wychodzący<br>Nazwa wymiaru: Udział plików (tylko udział plików Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Jak tworzyć alerty dla Azure Files

1. Przejdź do **konta magazynu w** Azure Portal .  

2. Kliknij **pozycję Alerty,** a następnie **kliknij pozycję + Nowa reguła alertu.**

3. Kliknij **pozycję Edytuj zasób,** wybierz typ **zasobu Plik,** a następnie kliknij pozycję **Gotowe.** 

4. Kliknij **pozycję Dodaj warunek** i podaj następujące informacje dotyczące alertu: 

    - **Metryka**
    - **Nazwa wymiaru**
    - **Logika alertów**

5. Kliknij **pozycję Dodaj grupy akcji** i dodaj grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

6. Wypełnij szczegóły **alertu, takie** jak **Nazwa reguły alertu,** **Opis** i **Ważność.**

7. Kliknij **pozycję Utwórz regułę alertu,** aby utworzyć alert.

> [!NOTE]  
> Jeśli tworzysz alert i jest on zbyt zaszumiiony, dostosuj wartość progową i logikę alertu.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Jak utworzyć alert, jeśli udział plików jest ograniczany

1. Przejdź do konta **magazynu w** **Azure Portal**.
2. W sekcji **Monitorowanie** kliknij pozycję **Alerty,** a następnie **kliknij pozycję + Nowa reguła alertu.**
3. Kliknij **pozycję Edytuj zasób,** wybierz typ **zasobu Plik** dla konta magazynu, a następnie kliknij pozycję **Gotowe.** Jeśli na przykład nazwa konta magazynu to `contoso` , wybierz `contoso/file` zasób.
4. Kliknij **pozycję Dodaj warunek,** aby dodać warunek.
5. Zostanie wyświetlona lista sygnałów obsługiwanych dla konta magazynu. Wybierz **metrykę Transakcje.**
6. W bloku **Konfigurowanie logiki sygnału** kliknij menu **rozwijane Nazwa** wymiaru i wybierz pozycję **Typ odpowiedzi.**
7. Kliknij menu **rozwijane Wartości** wymiarów i wybierz odpowiednie typy odpowiedzi dla udziału plików.

    W przypadku standardowych udziałów plików wybierz następujące typy odpowiedzi:

    - SuccessWithShareIopsThrottling
    - SuccessWithThrottling
    - ClientShareIopsThrottlingError

    W przypadku udziałów plików Premium wybierz następujące typy odpowiedzi:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Jeśli typy odpowiedzi nie są wymienione na liście **rozwijanej** Wartości wymiaru, oznacza to, że zasób nie został ograniczany. Aby dodać wartości wymiarów,  obok listy rozwijanej Wartości wymiaru wybierz pozycję Dodaj wartość niestandardową, wprowadź typ ponawiania (na przykład **SuccessWithThrottling),** wybierz przycisk **OK,** a następnie powtórz te kroki, aby dodać wszystkie odpowiednie typy odpowiedzi dla udziału plików.

8. W **przypadku udziałów plików Premium** kliknij menu rozwijane **Nazwa** wymiaru i wybierz pozycję **Udział plików**. W **przypadku standardowych udziałów plików** przejdź do kroku **#10**.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, wymiar Udział plików nie będzie zawierał listy udziałów plików, ponieważ metryki dla udziału nie są dostępne dla standardowych udziałów plików.  Alerty ograniczania dla standardowych udziałów plików zostaną wyzwolone, jeśli dowolny udział plików w ramach konta magazynu jest ograniczany, a alert nie zidentyfikuje, który udział plików został ograniczany. Ponieważ metryki na udział nie są dostępne dla standardowych udziałów plików, zaleca się, aby mieć jeden udział plików na konto magazynu.

9. Kliknij menu **rozwijane Wartości** wymiarów i wybierz udziały plików, dla których chcesz ostrzegania.
10. **Zdefiniuj parametry alertu** (wartość progowa, operator, poziom szczegółowości agregacji i częstotliwość oceny) i kliknij przycisk **Gotowe.**

    > [!TIP]
    > Jeśli używasz progu statycznego, wykres metryki może pomóc określić rozsądną wartość progową, jeśli udział plików jest obecnie ograniczany. Jeśli używasz progu dynamicznego, wykres metryki wyświetli obliczone progi na podstawie ostatnich danych.

11. Kliknij **pozycję Dodaj grupy akcji,** aby dodać grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji. 
12. Wypełnij szczegóły **alertu, takie** jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
13. Kliknij **pozycję Utwórz regułę alertu,** aby utworzyć alert.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Jak utworzyć alert, jeśli rozmiar udziału plików platformy Azure wynosi 80% pojemności

1. Przejdź do konta **magazynu w** **Azure Portal**.
2. W sekcji **Monitorowanie** kliknij pozycję **Alerty,** a następnie **kliknij pozycję + Nowa reguła alertu.**
3. Kliknij **pozycję Edytuj zasób,** wybierz typ zasobu **Plik** dla konta magazynu, a następnie kliknij przycisk **Gotowe.** Jeśli na przykład nazwa konta magazynu to `contoso` , wybierz `contoso/file` zasób.
4. Kliknij **pozycję Dodaj warunek,** aby dodać warunek.
5. Zostanie wyświetlona lista sygnałów obsługiwanych dla konta magazynu. Wybierz **metrykę Pojemność** pliku.
6. W **przypadku udziałów plików Premium** kliknij menu rozwijane **Nazwa** wymiaru i wybierz pozycję **Udział plików**. W **przypadku standardowych udziałów plików** przejdź do **#8**.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, wymiar Udziału plików nie będzie zawierał listy udziałów plików, ponieważ metryki dla każdego udziału nie są dostępne dla standardowych udziałów plików.  Alerty dotyczące standardowych udziałów plików są oparte na wszystkich udziałach plików na koncie magazynu. Ponieważ metryki na udział nie są dostępne dla standardowych udziałów plików, zaleca się, aby mieć jeden udział plików na konto magazynu.

7. Kliknij menu **rozwijane Wartości** wymiarów i wybierz udziały plików, dla których chcesz ostrzegania.
8. Wprowadź wartość **progową w** bajtach. Jeśli na przykład rozmiar udziału plików wynosi 100 TiB i chcesz otrzymać alert, gdy rozmiar udziału plików wynosi 80% pojemności, wartość progowa w bajtach to 87960930222080.
9. Zdefiniuj pozostałe parametry **alertu** (poziom szczegółowości agregacji i częstotliwość oceny), a następnie kliknij pozycję **Gotowe.**
10. Kliknij **pozycję Dodaj grupy akcji,** aby dodać grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji. 
11. Wypełnij szczegóły **alertu, takie** jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
12. Kliknij **pozycję Utwórz regułę alertu,** aby utworzyć alert.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Jak utworzyć alert, jeśli ruch wychodzący udziału plików platformy Azure przekroczył 500 GiB w ciągu dnia

1. Przejdź do konta **magazynu w** **Azure Portal**.
2. W sekcji Monitorowanie kliknij pozycję **Alerty,** a następnie **kliknij pozycję + Nowa reguła alertu.**
3. Kliknij **pozycję Edytuj zasób,** wybierz typ **zasobu Plik** dla konta magazynu, a następnie kliknij pozycję **Gotowe.** Jeśli na przykład nazwa konta magazynu to contoso, wybierz zasób contoso/file.
4. Kliknij **pozycję Dodaj warunek,** aby dodać warunek.
5. Zobaczysz listę sygnałów obsługiwanych dla konta magazynu i wybierz metrykę **Ruch wychodzący.**
6. W **przypadku udziałów plików Premium** kliknij menu rozwijane **Nazwa** wymiaru i wybierz pozycję **Udział plików.** W **przypadku standardowych udziałów plików** przejdź do kroku **#8**.

   > [!NOTE]
   > Jeśli udział plików jest standardowym udziałem plików, wymiar Udział plików nie będzie zawierał listy udziałów plików, ponieważ metryki dla udziału nie są dostępne dla standardowych udziałów plików.  Alerty dotyczące standardowych udziałów plików są oparte na wszystkich udziałach plików na koncie magazynu. Ponieważ metryki na udział nie są dostępne dla standardowych udziałów plików, zaleca się, aby mieć jeden udział plików na konto magazynu.

7. Kliknij menu **rozwijane Wartości** wymiarów i wybierz udziały plików, dla których chcesz ostrzegania.
8. Wprowadź **536870912000** bajtów w wartości progowej. 
9. Kliknij menu **rozwijane Poziom szczegółowości agregacji** i wybierz **pozycję 24 godziny.**
10. Wybierz częstotliwość **oceny i** kliknij **przycisk Gotowe.**
11. Kliknij **pozycję Dodaj grupy akcji,** aby dodać grupę akcji (wiadomość e-mail, wiadomość SMS itp.) do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji. 
12. Wypełnij szczegóły **alertu, takie** jak **Nazwa reguły alertu,** **Opis** i **Ważność.**
13. Kliknij **pozycję Utwórz regułę alertu,** aby utworzyć alert.

## <a name="next-steps"></a>Następne kroki

- [Azure Files danych monitorowania](storage-files-monitoring-reference.md)
- [Monitorowanie zasobów platformy Azure za pomocą Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md)
- [Migracja metryk usługi Azure Storage](../common/storage-metrics-migration.md)
- [Planowanie wdrożenia usługi Azure Files](./storage-files-planning.md)
- [Jak wdrożyć usługę Azure Files](./storage-how-to-create-file-share.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](./storage-troubleshoot-windows-file-connection-problems.md)
- [Rozwiązywanie problemów z usługą Azure Files w systemie Linux](./storage-troubleshoot-linux-file-connection-problems.md)
