---
title: Monitorowanie dzienników i metryk usługi Azure Firewall
description: W tym artykule dowiesz się, jak włączyć dzienniki i metryki zapory platformy Azure oraz zarządzać nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 2899121db4b6a3f202be4860e2e4f43027cdef7c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348771"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Monitorowanie dzienników i metryk usługi Azure Firewall

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall. Za pomocą metryk możesz wyświetlać liczniki wydajności w portalu.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do [Azure monitor dzienników](../azure-monitor/insights/azure-networking-analytics.md), magazynu i Event Hubs i analizowane w dziennikach Azure monitor lub przy użyciu różnych narzędzi, takich jak Excel i Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy zapoznać się z tematem [dzienniki i metryki zapory platformy Azure](logs-and-metrics.md) , aby zapoznać się z omówieniem dzienników diagnostycznych i metryk dostępnych dla zapory platformy Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Włączanie rejestrowania diagnostycznego za pośrednictwem witryny Azure Portal

Od wykonania tej procedury w celu włączenia rejestrowania diagnostycznego może upłynąć kilka minut, zanim dane pojawią się w dziennikach. Jeśli na początku nie widzisz żadnych danych, sprawdź ponownie za kilka minut.

1. W Azure Portal Otwórz grupę zasobów zapory i wybierz zaporę.
2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.

   W przypadku zapory platformy Azure dostępne są cztery dzienniki specyficzne dla usługi:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Wybierz pozycję **Dodaj ustawienie diagnostyczne**. Strona **Ustawienia diagnostyczne** zawiera ustawienia dzienników diagnostycznych.
5. W tym przykładzie dzienniki są przechowywane w dziennikach usługi Azure Monitor, więc wpisz nazwę **Firewall log analytics**.
6. W **obszarze Dziennik** wybierz pozycję **AzureFirewallApplicationRule** , **AzureFirewallNetworkRule** , **AzureFirewallThreatIntelLog** i **AzureFirewallDnsProxy** , aby zebrać dzienniki.
7. Wybierz pozycję **Wyślij do log Analytics** , aby skonfigurować obszar roboczy.
8. Wybierz subskrypcję.
9. Wybierz pozycję **Zapisz**.

## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Aby rozpocząć zbieranie danych dostępnych za pośrednictwem tych dzienników, należy włączyć rejestrowanie diagnostyczne.

Aby włączyć rejestrowanie diagnostyczne, wykonaj następujące kroki:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać: */subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\>*.

   Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal. Znajdują się one na stronie **Właściwości** zasobu.

2. Zanotuj identyfikator zasobu zapory, dla której rejestrowanie jest włączane. Ta wartość ma postać: */subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\>*.

   Te informacje możesz znaleźć w portalu.

3. Włącz rejestrowanie diagnostyczne przy użyciu następującego polecenia cmdlet programu PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Dzienniki diagnostyczne nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

## <a name="enable-diagnostic-logging-by-using-azure-cli"></a>Włączanie rejestrowania diagnostycznego przy użyciu interfejsu wiersza polecenia platformy Azure

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Aby rozpocząć zbieranie danych dostępnych za pośrednictwem tych dzienników, należy włączyć rejestrowanie diagnostyczne.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="enable-diagnostic-logging"></a>Włączanie rejestrowania diagnostycznego

Użyj następujących poleceń, aby włączyć rejestrowanie diagnostyczne.

1. Uruchom polecenie [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) , aby włączyć rejestrowanie diagnostyczne:

   ```azurecli
   az monitor diagnostic-settings create –name AzureFirewallApplicationRule \
     --resource Firewall07 --storage-account MyStorageAccount
   ```

   Uruchom polecenie [AZ monitor Diagnostic-Settings List](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_list) , aby wyświetlić ustawienia diagnostyczne dla zasobu:

   ```azurecli
   az monitor diagnostic-settings list --resource Firewall07
   ```

   Użyj [AZ monitor Diagnostic-Settings show](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_show) , aby wyświetlić aktywne ustawienia diagnostyczne dla zasobu:

   ```azurecli
   az monitor diagnostic-settings show --name AzureFirewallApplicationRule --resource Firewall07
   ```

1. Uruchom polecenie [AZ monitor Diagnostic-Settings Update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) , aby zaktualizować ustawienia.

   ```azurecli
   az monitor diagnostic-settings update --name AzureFirewallApplicationRule --resource Firewall07 --set retentionPolicy.days=365
   ```

   Użyj polecenia [AZ monitor Diagnostic-Settings Delete](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_delete) , aby usunąć ustawienie diagnostyki.

   ```azurecli
   az monitor diagnostic-settings delete --name AzureFirewallApplicationRule --resource Firewall07
   ```

> [!TIP]
>Dzienniki diagnostyczne nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

## <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można wyświetlać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure** : pobierz informacje z dziennika aktywności przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub witryny Azure Portal. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w artykule [Activity operations with Resource Manager (Operacje działań przy użyciu usługi Resource Manager)](../azure-resource-manager/management/view-activity-logs.md).
* **Usługa Power BI** : jeśli nie masz jeszcze konta usługi [Power BI](https://powerbi.microsoft.com/pricing), możesz ją wypróbować bezpłatnie. Korzystając z [pakietu zawartości dzienników aktywności platformy Azure dla usługi Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), możesz analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych, których możesz używać bez zmian lub po dostosowaniu.
* **Azure — wskaźnik** : możesz połączyć Dzienniki zapory platformy Azure z badaniem wskaźnikowym platformy Azure, umożliwiając wyświetlanie danych dziennika w skoroszytach, korzystanie z nich do tworzenia alertów niestandardowych i uwzględnianie go w celu usprawnienia badania. Łącznik danych zapory platformy Azure w usłudze Azure wskaźnikowej jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [łączenie danych z zapory platformy Azure](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Wyświetlanie i analizowanie dzienników reguł sieci i aplikacji

[Dzienniki usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) zbierają pliki dzienników liczników i zdarzeń. Obejmuje ona wizualizacje oraz zaawansowane możliwości wyszukiwania na potrzeby analizowania dzienników.

Przykładowe zapytania analizy dzienników w usłudze Azure Firewall można znaleźć w temacie [Przykłady analizy dzienników w usłudze Azure Firewall](log-analytics-samples.md).

Ponadto możesz połączyć się z kontem magazynu i pobrać wpisy dziennika JSON dotyczące dostępu i wydajności. Po pobraniu plików JSON możesz je przekonwertować do formatu CSV i wyświetlać w programie Excel, usłudze Power BI lub innym narzędziu do wizualizacji danych.

> [!TIP]
> Jeśli znasz program Visual Studio oraz podstawowe pojęcia dotyczące zmiany wartości stałych i zmiennych w języku C#, możesz skorzystać z [konwerterów dzienników](https://github.com/Azure-Samples/networking-dotnet-log-converter) dostępnych w witrynie GitHub.

## <a name="view-metrics"></a>Wyświetlanie metryk
Przejdź do zapory platformy Azure, w obszarze **monitorowanie** wybierz pozycję **metryki**. Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy skonfigurowano zaporę na potrzeby zbierania dzienników, możesz eksplorować dzienniki usługi Azure Monitor, aby wyświetlać dane.

[Rozwiązania do monitorowania sieci w dziennikach usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)
