---
title: Monitorowanie Queue Storage platformy Azure
description: Dowiedz się, jak monitorować wydajność i dostępność Queue Storage platformy Azure. Monitoruj dane Queue Storage platformy Azure, Dowiedz się więcej o konfiguracji i analizuj dane dotyczące metryk i dzienników.
author: normesta
services: storage
ms.author: normesta
ms.reviewer: fryu
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: monitoring, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: bb9eb2e91fb29849f27e7aba9bc4794698bf0b0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570044"
---
# <a name="monitoring-azure-queue-storage"></a>Monitorowanie Queue Storage platformy Azure

Jeśli masz krytyczne aplikacje i procesy biznesowe, które opierają się na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania, które są generowane przez usługę Azure Queue Storage i jak można użyć funkcji Azure Monitor do analizowania alertów dotyczących tych danych.

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników dla obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Ta funkcja jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Zobacz [Omówienie konta magazynu](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Przegląd monitora

Strona **Przegląd** w Azure Portal dla każdego zasobu queue storage zawiera krótki widok użycia zasobów, na przykład żądania i rozliczenia godzinowe. Te informacje są przydatne, ale dostępna jest tylko niewielka ilość danych monitorowania. Niektóre z tych danych są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu zasobu. Dodatkowe typy zbierania danych można włączyć za pomocą jakiejś konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Usługa Azure Queue Storage tworzy dane monitorowania przy użyciu [Azure monitor](../../azure-monitor/overview.md), który jest pełną usługą monitorowania stosu na platformie Azure. Azure Monitor oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych.

Rozpocznij od [monitorowania zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) , w którym opisano następujące kwestie:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje dotyczą tego artykułu, opisując szczegółowe dane zebrane z usługi Azure Storage. Przykłady pokazują, jak skonfigurować zbieranie danych i analizować je za pomocą narzędzi platformy Azure.

## <a name="monitoring-data"></a>Dane monitorowania

Usługa Azure Queue Storage gromadzi te same rodzaje danych monitorowania jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Aby uzyskać szczegółowe informacje na temat metryk i dzienników tworzonych przez usługę Azure Queue Storage, zobacz informacje o [danych monitorowania queue storage platformy Azure](monitor-queue-storage-reference.md) .

Metryki i dzienniki w Azure Monitor obsługują tylko Azure Resource Manager kont magazynu. Azure Monitor nie obsługuje klasycznych kont magazynu. Jeśli chcesz użyć metryk lub dzienników na klasycznym koncie magazynu, musisz przeprowadzić migrację do konta magazynu Azure Resource Manager. Zobacz [Migrowanie do Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md).

Jeśli chcesz, możesz nadal korzystać z klasycznych metryk i dzienników. W rzeczywistości klasyczne metryki i dzienniki są dostępne równolegle z metrykami i dziennikami w Azure Monitor. Obsługa będzie obowiązywać do momentu zakończenia usługi Azure Storage w ramach starszych metryk i dzienników.

## <a name="collection-and-routing"></a>Kolekcja i Routing

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawień diagnostycznych.

Aby zbierać dzienniki zasobów, należy utworzyć ustawienie diagnostyczne. Podczas tworzenia ustawienia wybierz **kolejkę** jako typ magazynu, dla którego chcesz włączyć dzienniki. Następnie należy określić jedną z następujących kategorii operacji, dla których mają być zbierane dzienniki.

| Kategoria | Opis |
|:---|:---|
| **StorageRead** | Operacje odczytu na obiektach. |
| **StorageWrite** | Operacje zapisu w obiektach. |
| **StorageDelete** | Operacje usuwania obiektów. |

## <a name="creating-a-diagnostic-setting"></a>Tworzenie ustawień diagnostycznych

Ustawienie diagnostyczne można utworzyć przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonu Azure Resource Manager.

Aby uzyskać ogólne wskazówki, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](../../azure-monitor/essentials/diagnostic-settings.md).

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników dla obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Ta funkcja jest dostępna dla wszystkich kont magazynu utworzonych za pomocą modelu wdrażania Azure Resource Manager. Zobacz [Omówienie konta magazynu](../common/storage-account-overview.md).

### <a name="azure-portal"></a>[Witryna Azure Portal](#tab/azure-portal)

1. Zaloguj się do witryny Azure Portal.

2. Przejdź do konta magazynu.

3. W sekcji **monitorowanie** kliknij pozycję **Ustawienia diagnostyczne (wersja zapoznawcza)**.

   > [!div class="mx-imgBorder"]
   > ![Portal — dzienniki diagnostyki](media/monitor-queue-storage/diagnostic-logs-settings-pane.png)

4. Wybierz **kolejkę** jako typ magazynu, dla którego chcesz włączyć dzienniki.

5. Kliknij pozycję **Dodaj ustawienie diagnostyczne**.

   > [!div class="mx-imgBorder"]
   > ![Portal — dzienniki zasobów — Dodaj ustawienie diagnostyczne](media/monitor-queue-storage/diagnostic-logs-settings-pane-2.png)

   Zostanie wyświetlona strona **Ustawienia diagnostyczne** .

   > [!div class="mx-imgBorder"]
   > ![Strona dzienniki zasobów](media/monitor-queue-storage/diagnostic-logs-page.png)

6. W polu **Nazwa** strony wprowadź nazwę tego ustawienia dziennika zasobów. Następnie wybierz operacje, które mają być rejestrowane (operacje odczytu, zapisu i usuwania) i miejsce, w którym mają być wysyłane dzienniki.

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się na archiwizowanie dzienników na koncie magazynu, płacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz sekcję **dzienniki platformy** na stronie [cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaznacz pole wyboru **Archiwizuj do konta magazynu** , a następnie wybierz przycisk **Konfiguruj** .

   > [!div class="mx-imgBorder"]
   > ![Magazyn archiwum stron ustawień diagnostycznych](media/monitor-queue-storage/diagnostic-logs-settings-pane-archive-storage.png)

2. Z listy rozwijanej **konto magazynu** wybierz konto magazynu, w którym mają być archiwizowane dzienniki, kliknij przycisk **OK** , a następnie wybierz przycisk **Zapisz** .

   > [!NOTE]
   > Przed wybraniem konta magazynu jako miejsca docelowego eksportu Zobacz sekcję [Archiwizowanie dzienników zasobów platformy Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) , aby poznać wymagania wstępne na koncie magazynu.

#### <a name="stream-logs-to-azure-event-hubs"></a>Przesyłanie strumieniowe dzienników do usługi Azure Event Hubs

W przypadku wybrania przesyłania strumieniowego dzienników do centrum zdarzeń płacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz sekcję **dzienniki platformy** na stronie [cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

1. Zaznacz pole wyboru **strumień do centrum zdarzeń** , a następnie wybierz przycisk **Konfiguruj** .

2. W okienku **Wybierz centrum zdarzeń** wybierz przestrzeń nazw, nazwę i nazwę zasad centrum zdarzeń, do którego chcesz przesyłać strumieniowo dzienniki.

   > [!div class="mx-imgBorder"]
   > ![Centrum zdarzeń strony ustawień diagnostycznych](media/monitor-queue-storage/diagnostic-logs-settings-pane-event-hub.png)

3. Kliknij przycisk **OK** , a następnie wybierz przycisk **Zapisz** .

#### <a name="send-logs-to-azure-log-analytics"></a>Wyślij dzienniki do usługi Azure Log Analytics

1. Zaznacz pole wyboru **Wyślij do log Analytics** wybierz log Analytics obszar roboczy, a następnie wybierz przycisk **Zapisz** .

   > [!div class="mx-imgBorder"]
   > ![Analiza dzienników strony ustawień diagnostycznych](media/monitor-queue-storage/diagnostic-logs-settings-pane-log-analytics.png)

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Otwórz okno polecenia programu Windows PowerShell i zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia. Następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

2. Ustaw aktywną subskrypcję na konto magazynu, dla którego chcesz włączyć rejestrowanie.

   ```powershell
   Set-AzContext -SubscriptionId <subscription-id>
   ```

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się na archiwizowanie dzienników na koncie magazynu, płacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz sekcję **dzienniki platformy** na stronie [cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Włącz dzienniki przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell wraz z `StorageAccountId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -StorageAccountId <storage-account-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Zastąp `<storage-service-resource--id>` symbol zastępczy w tym fragmencie kodu identyfikatorem zasobu kolejki. Identyfikator zasobu można znaleźć w Azure Portal, otwierając stronę **Właściwości** konta magazynu.

Można użyć `StorageRead` , `StorageWrite` i `StorageDelete` dla wartości parametru **kategorii** .

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -StorageAccountId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount -Enabled $true -Category StorageWrite,StorageDelete`

Aby zapoznać się z opisem każdego parametru, zobacz [Archiwizowanie dzienników zasobów platformy Azure za pośrednictwem Azure PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

W przypadku wybrania przesyłania strumieniowego dzienników do centrum zdarzeń płacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz sekcję **dzienniki platformy** na stronie [cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Włącz dzienniki przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell z `EventHubAuthorizationRuleId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -EventHubAuthorizationRuleId <event-hub-namespace-and-key-name> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -EventHubAuthorizationRuleId /subscriptions/20884142-a14v3-4234-5450-08b10c09f4/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey -Enabled $true -Category StorageDelete`

Aby uzyskać opis każdego z parametrów, zobacz [przesyłanie strumieniowe danych do Event Hubs za pomocą poleceń cmdlet programu PowerShell](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Wysyłanie dzienników do usługi Log Analytics

Włącz dzienniki przy użyciu polecenia cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) programu PowerShell z `WorkspaceId` parametrem.

```powershell
Set-AzDiagnosticSetting -ResourceId <storage-service-resource-id> -WorkspaceId <log-analytics-workspace-resource-id> -Enabled $true -Category <operations-to-log> -RetentionEnabled <retention-bool> -RetentionInDays <number-of-days>
```

Oto przykład:

`Set-AzDiagnosticSetting -ResourceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default -WorkspaceId /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace -Enabled $true -Category StorageDelete`

Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe dzienników zasobów platformy Azure do obszaru roboczego log Analytics w Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Najpierw Otwórz [Azure Cloud Shell](../../cloud-shell/overview.md)lub jeśli [interfejs wiersza polecenia platformy Azure został zainstalowany](/cli/azure/install-azure-cli) lokalnie, Otwórz aplikację konsoli poleceń, taką jak PowerShell.

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, dla którego chcesz włączyć dzienniki.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

#### <a name="archive-logs-to-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Jeśli zdecydujesz się na archiwizowanie dzienników na koncie magazynu, płacisz za ilość dzienników wysyłanych do konta magazynu. Aby uzyskać szczegółowe informacje o cenach, zobacz sekcję **dzienniki platformy** na stronie [cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Włącz dzienniki przy użyciu [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --storage-account <storage-account-name> --resource <storage-service-resource-id> --resource-group <resource-group> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Zastąp `<storage-service-resource--id>` symbol zastępczy w tym fragmencie kodu identyfikatorem zasobu kolejki. Identyfikator zasobu można znaleźć w Azure Portal, otwierając stronę **Właściwości** konta magazynu.

Można użyć `StorageRead` `StorageWrite` wartości parametru, i `StorageDelete` `category` .

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --storage-account mystorageaccount --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --resource-group myresourcegroup --logs '[{"category": StorageWrite, "enabled": true, "retentionPolicy": {"days": 90, "enabled": true}}]'`

Aby zapoznać się z opisem każdego parametru, zobacz [Archiwizowanie dzienników zasobów za pośrednictwem interfejsu wiersza polecenia platformy Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

#### <a name="stream-logs-to-an-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń

W przypadku wybrania przesyłania strumieniowego dzienników do centrum zdarzeń płacisz za ilość dzienników wysyłanych do centrum zdarzeń. Aby uzyskać szczegółowe informacje o cenach, zobacz sekcję **dzienniki platformy** na stronie [cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/#platform-logs) .

Włącz dzienniki przy użyciu [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --event-hub <event-hub-name> --event-hub-rule <event-hub-namespace-and-key-name> --resource <storage-account-resource-id> --logs '[{"category": <operations>, "enabled": true "retentionPolicy": {"days": <number-days>, "enabled": <retention-bool}}]'
```

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --event-hub myeventhub --event-hub-rule /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhubnamespace/authorizationrules/RootManageSharedAccessKey --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true }]'`

Aby uzyskać opis każdego z parametrów, zobacz [przesyłanie strumieniowe danych do Event Hubs za pomocą interfejsu wiersza polecenia platformy Azure](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs).

#### <a name="send-logs-to-log-analytics"></a>Wysyłanie dzienników do usługi Log Analytics

Włącz dzienniki przy użyciu [`az monitor diagnostic-settings create`](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

```azurecli-interactive
az monitor diagnostic-settings create --name <setting-name> --workspace <log-analytics-workspace-resource-id> --resource <storage-account-resource-id> --logs '[{"category": <category name>, "enabled": true "retentionPolicy": {"days": <days>, "enabled": <retention-bool}}]'
```

Oto przykład:

`az monitor diagnostic-settings create --name setting1 --workspace /subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.OperationalInsights/workspaces/my-analytic-workspace --resource /subscriptions/938841be-a40c-4bf4-9210-08bcf06c09f9/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/myloggingstorageaccount/queueServices/default --logs '[{"category": StorageDelete, "enabled": true ]'`

 Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe dzienników zasobów platformy Azure do obszaru roboczego log Analytics w Azure monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

# <a name="template"></a>[Szablon](#tab/template)

Aby wyświetlić szablon Azure Resource Manager, który tworzy ustawienie diagnostyczne, zobacz [ustawienie diagnostyczne dla usługi Azure Storage](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md#diagnostic-setting-for-azure-storage).

---

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki usługi Azure Storage można analizować za pomocą metryk z innych usług platformy Azure za pomocą usługi Azure Eksplorator metryk. Otwórz Eksplorator metryk, wybierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../../azure-monitor/essentials/metrics-getting-started.md).

Ten przykład pokazuje sposób wyświetlania **transakcji** na poziomie konta.

![Zrzut ekranu przedstawiający dostęp do metryk w Azure Portal](./media/monitor-queue-storage/access-metrics-portal.png)

W przypadku metryk, które obsługują wymiary, można filtrować metrykę przy użyciu żądanej wartości wymiaru. Ten przykład pokazuje, jak wyświetlić **transakcje** na poziomie konta w określonej operacji, wybierając wartości dla wymiaru **nazwa interfejsu API** .

![Zrzut ekranu przedstawiający dostęp do metryk z wymiarem w Azure Portal](./media/monitor-queue-storage/access-metrics-portal-with-dimension.png)

Aby uzyskać pełną listę wymiarów obsługiwanych przez usługę Azure Storage, zobacz [Dimension Metrics](monitor-queue-storage-reference.md#metrics-dimensions).

Metryki dla Queue Storage platformy Azure znajdują się w następujących przestrzeniach nazw:

- Microsoft. Storage/storageAccounts
- Microsoft. Storage/storageAccounts/queueServices

Aby uzyskać listę wszystkich Azure Monitor metryki pomocy technicznej, które obejmują usługę Azure Queue Storage, zobacz temat [Azure monitor support Metrics](../../azure-monitor/essentials/metrics-supported.md).

### <a name="accessing-metrics"></a>Uzyskiwanie dostępu do metryk

> [!TIP]
> Aby wyświetlić przykłady interfejsu wiersza polecenia platformy Azure lub platformy .NET, wybierz odpowiednie karty wymienione w tym miejscu.

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Lista definicji metryk

Można wyświetlić listę definicji metryk konta magazynu lub usługi Queue Storage. Użyj polecenia cmdlet [Get-AzMetricDefinition](/powershell/module/az.monitor/get-azmetricdefinition) .

W tym przykładzie Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Odczytywanie wartości metryk

Można odczytywać wartości metryk na poziomie konta magazynu lub usługi Queue Storage. Użyj polecenia cmdlet [Get-AzMetric](/powershell/module/az.monitor/get-azmetric) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Utwórz listę definicji metryk na poziomie konta

Można wyświetlić listę definicji metryk konta magazynu lub usługi Queue Storage. Użyj [`az monitor metrics list-definitions`](/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions) polecenia.

W tym przykładzie Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub identyfikatorem zasobu kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Odczytaj wartości metryk na poziomie konta

Można odczytać wartości metryk konta magazynu lub usługi Queue Storage. Użyj [`az monitor metrics list`](/cli/azure/monitor/metrics#az-monitor-metrics-list) polecenia.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/azure-portal)

Azure Monitor udostępnia [zestaw .NET SDK](https://www.nuget.org/packages/microsoft.azure.management.monitor/) do odczytywania definicji metryk i wartości. [Przykładowy kod](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) pokazuje, jak używać zestawu SDK z innymi parametrami. Aby `0.18.0-preview` uzyskać metryki magazynu, należy użyć lub nowszej wersji.

W tych przykładach Zastąp `<resource-ID>` symbol zastępczy identyfikatorem zasobu całego konta magazynu lub kolejki. Te identyfikatory zasobów można znaleźć na stronach **Właściwości** konta magazynu w Azure Portal.

Zastąp `<subscription-ID>` ZMIENNĄ identyfikatorem subskrypcji. Aby uzyskać wskazówki dotyczące uzyskiwania wartości dla `<tenant-ID>` , `<application-ID>` i `<AccessKey>` , zobacz [Korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md).

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
        // Resource ID for queue storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default";
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

### <a name="template"></a>[Szablon](#tab/template)

Nie dotyczy.

---

## <a name="analyzing-logs"></a>Analizowanie dzienników

Można uzyskać dostęp do dzienników zasobów jako kolejka na koncie magazynu, jako dane zdarzenia lub za pomocą zapytań Log Analytics.

Aby uzyskać szczegółowe informacje na temat pól, które pojawiają się w tych dziennikach, zobacz [informacje dotyczące monitorowania queue storage platformy Azure](monitor-queue-storage-reference.md).

> [!NOTE]
> Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek, tabel, kont magazynu w warstwie Premium w celu ogólnego przeznaczenia w wersji 1 i w wersji 2. Klasyczne konta magazynu nie są obsługiwane.

Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi. Na przykład jeśli konto magazynu ma działanie w punkcie końcowym kolejki, ale nie znajduje się w jego obszarze końcowym tabeli lub obiektu BLOB, tworzone są tylko dzienniki, które odnoszą się do Queue Storage. Dzienniki usługi Azure Storage zawierają szczegółowe informacje na temat żądań zakończonych powodzeniem i zakończonych niepowodzeniem w usłudze magazynu. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku.

### <a name="log-authenticated-requests"></a>Rejestruj uwierzytelnione żądania

Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania używające sygnatury dostępu współdzielonego (SAS) lub OAuth, w tym żądania zakończone niepowodzeniem i zakończone powodzeniem
- Żądania danych analitycznych (klasyczne dane dziennika w **$Logs** kontenera i danych metryk klas w tabelach **$Metric** )

Żądania wykonywane przez samą usługę Queue Storage, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych danych, zobacz [zarejestrowane operacje magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz [format dziennika magazynu](monitor-queue-storage-reference.md).

### <a name="log-anonymous-requests"></a>Rejestruj anonimowe żądania

Rejestrowane są następujące typy żądań anonimowych:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy limitu czasu dla klienta i serwera
- Żądania zakończone niepowodzeniem `GET` z kodem błędu 304 ( `Not Modified` )

Wszystkie inne Nieudane żądania anonimowe nie są rejestrowane. Aby uzyskać pełną listę zarejestrowanych danych, zobacz [zarejestrowane operacje magazynu i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz [format dziennika magazynu](monitor-queue-storage-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Uzyskiwanie dostępu do dzienników na koncie magazynu

Dzienniki są wyświetlane jako obiekty blob przechowywane w kontenerze na docelowym koncie magazynu. Dane są zbierane i przechowywane w pojedynczym obiekcie BLOB jako ładunek JSON rozdzielany wierszami. Nazwa obiektu BLOB jest zgodna z tą konwencją nazewnictwa:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/queueServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Oto przykład:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/queueServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Uzyskiwanie dostępu do dzienników w centrum zdarzeń

Dzienniki wysyłane do centrum zdarzeń nie są przechowywane jako plik, ale można sprawdzić, czy centrum zdarzeń otrzymało informacje dziennika. W Azure Portal przejdź do centrum zdarzeń i sprawdź, czy `incoming requests` liczba jest większa od zera.

![Dzienniki inspekcji](media/monitor-queue-storage/event-hub-log.png)

Możesz uzyskiwać dostęp do danych dziennika, które są wysyłane do centrum zdarzeń, i je odczytywać, korzystając z informacji o zabezpieczeniach i narzędzi do monitorowania. Aby uzyskać więcej informacji, zobacz [co można zrobić przy użyciu danych monitorowania wysyłanych do centrum zdarzeń?](../../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Uzyskiwanie dostępu do dzienników w obszarze roboczym Log Analytics

Można uzyskać dostęp do dzienników wysyłanych do obszaru roboczego Log Analytics przy użyciu zapytań dziennika Azure Monitor.

Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z log Analytics w Azure monitor](../../azure-monitor/logs/log-analytics-tutorial.md).

Dane są przechowywane w `StorageQueueLogs` tabeli.

#### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Poniżej przedstawiono niektóre zapytania, które można wprowadzić na pasku **przeszukiwania dzienników** , aby ułatwić monitorowanie kolejek. Te zapytania działają w [nowym języku](../../azure-monitor/logs/log-query-overview.md).

> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** z menu Grupa zasobów konta magazynu zostanie otwarty log Analytics z zakresem zapytania ustawionym na bieżącą grupę zasobów. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tej grupy zasobów. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych zasobów lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](../../azure-monitor/logs/scope.md) .

Użyj tych zapytań, aby ułatwić monitorowanie kont usługi Azure Storage:

- Aby wyświetlić listę 10 najczęstszych błędów w ciągu ostatnich trzech dni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```

- Aby wyświetlić 10 najważniejszych operacji, które spowodowały najwięcej błędów w ciągu ostatnich trzech dni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```

- Aby wyświetlić 10 najważniejszych operacji z najdłuższym opóźnieniem zakończonym w ciągu ostatnich trzech dni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```

- Aby wyświetlić listę wszystkich operacji, które spowodowały błędy ograniczania wydajności po stronie serwera w ciągu ostatnich trzech dni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```

- Aby wyświetlić listę wszystkich żądań z dostępem anonimowym w ciągu ostatnich trzech dni.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```

- Aby utworzyć wykres kołowy operacji używanych w ciągu ostatnich trzech dni.

    ```Kusto
    StorageQueueLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc
    | render piechart
    ```

## <a name="faq"></a>Często zadawane pytania

**Czy usługa Azure Storage obsługuje metryki dla dysków zarządzanych lub dysków niezarządzanych?**

Nie. Wystąpienia obliczeniowe obsługują metryki na dyskach. Aby uzyskać więcej informacji, zobacz [metryki na dysku dla dysków zarządzanych i niezarządzanych](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Następne kroki

- Informacje o dziennikach i metrykach utworzonych przez usługę Azure Queue Storage można znaleźć w temacie [azure queue storage monitoring Data Reference](monitor-queue-storage-reference.md).
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
- Aby uzyskać więcej informacji na temat migracji metryk, zobacz [migracja metryk usługi Azure Storage](../common/storage-metrics-migration.md).
