---
title: Rozwiązania do monitorowania w Azure Monitor | Microsoft Docs
description: Rozwiązania monitorowania w Azure Monitor to zbiór reguł logiki, wizualizacji i pozyskiwania danych, które zapewniają metryki przenoszone wokół określonego obszaru problemu.  Ten artykuł zawiera informacje na temat instalowania i używania rozwiązań do monitorowania.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 6a98571f513e25d801acd4f4a9c2901dcd56fabc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869225"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Rozwiązania do monitorowania w Azure Monitor

Rozwiązania do monitorowania Azure Monitor zapewniają analizę działania określonej aplikacji lub usługi platformy Azure. Ten artykuł zawiera krótkie omówienie monitorowania rozwiązań na platformie Azure oraz szczegółowe informacje na temat ich używania i instalowania. Rozwiązania do monitorowania można dodać do Azure Monitor dla dowolnych aplikacji i usług, których używasz. Są one zwykle dostępne bez poniesień kosztów, ale zbierają dane, które mogą wywoływać opłaty za użycie.

## <a name="use-monitoring-solutions"></a>Używanie rozwiązań do monitorowania

Na stronie **Przegląd rozwiązań** w Azure Monitor jest wyświetlany kafelek dla każdego rozwiązania zainstalowanego w obszarze roboczym usługi Log Analytics. Aby otworzyć tę stronę, przejdź do **Azure Monitor** w [Azure Portal](https://ms.portal.azure.com). W menu **Szczegółowe informacje** wybierz pozycję **Więcej,** aby otworzyć centrum **szczegółowych** informacji, a następnie kliknij pozycję Obszary robocze usługi **Log Analytics.**

[![Centrum szczegółowych informacji](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Użyj pól rozwijanych w górnej części ekranu, aby zmienić obszar roboczy lub zakres czasu używany dla kafelków. Kliknij kafelek rozwiązania, aby otworzyć jego widok, który zawiera bardziej szczegółową analizę zebranych danych.

[![Zrzut ekranu przedstawia Azure Portal z wybranymi rozwiązaniami i rozwiązaniami wyświetlanymi w okienku Rozwiązania.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Rozwiązania do monitorowania mogą zawierać wiele typów zasobów platformy Azure i można wyświetlać wszystkie zasoby dołączone do rozwiązania, tak jak każdy inny zasób. Na przykład wszystkie zapytania dziennika zawarte w rozwiązaniu są wyświetlane w obszarze **Zapytania rozwiązań w** [Eksploratorze zapytań](../logs/log-analytics-tutorial.md). Tych zapytań można używać podczas przeprowadzania analizy ad hoc za pomocą [zapytań dziennika](../logs/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Lista zainstalowanych rozwiązań do monitorowania

### <a name="portal"></a>[Portal](#tab/portal)

Użyj poniższej procedury, aby wyświetlić listę rozwiązań do monitorowania zainstalowanych w ramach subskrypcji.

1. Przejdź do witryny [Azure Portal](https://ms.portal.azure.com). Wyszukaj i wybierz **pozycję Rozwiązania.**
1. Zostaną wyświetlone rozwiązania zainstalowane we wszystkich twoich obszarach roboczych. Po nazwie rozwiązania następuje nazwa obszaru roboczego, w którym jest zainstalowane.
1. Użyj pól rozwijanych w górnej części ekranu, aby filtrować według subskrypcji lub grupy zasobów.

![Lista wszystkich rozwiązań](media/solutions/list-solutions-all.png)

Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania. Na tej stronie są wyświetlane wszystkie widoki zawarte w rozwiązaniu oraz dostępne są różne opcje dla samego rozwiązania i jego obszaru roboczego. Wyświetl stronę podsumowania rozwiązania, korzystając z jednej z powyższych procedur, aby wyświetlić listę rozwiązań, a następnie kliknij nazwę rozwiązania.

![Właściwości rozwiązania](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [az monitor log-analytics solution list,](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) aby wyświetlić listę rozwiązań do monitorowania zainstalowanych w ramach subskrypcji.   Przed uruchomieniem polecenia należy spełnić wymagania wstępne z `list` [tematu Instalowanie rozwiązania do monitorowania.](#install-a-monitoring-solution)

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Użyj polecenia cmdlet [Get-AzMonitorLogAnalyticsSolution,](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) aby wyświetlić listę rozwiązań do monitorowania zainstalowanych w ramach subskrypcji. Przed uruchomieniem tych poleceń należy spełnić wymagania wstępne z [tematu Instalowanie rozwiązania do monitorowania.](#install-a-monitoring-solution)

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Instalowanie rozwiązania do monitorowania

### <a name="portal"></a>[Portal](#tab/portal)

Rozwiązania do monitorowania firmy Microsoft i partnerów są dostępne w [Azure Marketplace](https://azuremarketplace.microsoft.com). Dostępne rozwiązania można wyszukiwać i instalować przy użyciu poniższej procedury. Podczas instalowania rozwiązania należy wybrać obszar roboczy usługi [Log Analytics,](../logs/manage-access.md) w którym rozwiązanie zostanie zainstalowane i gdzie będą zbierane jego dane.

1. Na liście [rozwiązań dla twojej subskrypcji kliknij](#list-installed-monitoring-solutions)pozycję **Dodaj**.
1. Przeglądaj lub wyszukaj rozwiązanie. Rozwiązania można również przeglądać za pomocą [tego linku wyszukiwania.](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)
1. Znajdź rozwiązanie do monitorowania i zapoznaj się z jego opisem.
1. Kliknij **przycisk Utwórz,** aby rozpocząć proces instalacji.
1. Po zakończeniu procesu instalacji zostanie wyświetlony monit o określenie obszaru roboczego usługi Log Analytics i podanie dowolnej konfiguracji wymaganej dla rozwiązania.

![Instalowanie rozwiązania](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalowanie rozwiązania ze społeczności

Członkowie społeczności mogą przesyłać rozwiązania do zarządzania do szablonów szybkiego startu platformy Azure. Te rozwiązania można zainstalować bezpośrednio lub pobrać szablony do późniejszej instalacji.

1. Postępuj zgodnie z procesem opisanym w te [tematach Log Analytics workspace and Automation account (Obszar roboczy](#log-analytics-workspace-and-automation-account) usługi Log Analytics i konto usługi Automation), aby połączyć obszar roboczy i konto.
2. Przejdź do szablonów [szybkiego startu platformy Azure.](https://azure.microsoft.com/documentation/templates/)
3. Wyszukaj rozwiązanie, które Cię interesuje.
4. Wybierz rozwiązanie z wyników, aby wyświetlić jego szczegóły.
5. Kliknij przycisk **Deploy to Azure (Wd wdrażaj na platformie Azure).**
6. Zostanie wyświetlony monit o podanie informacji, takich jak grupa zasobów i lokalizacja, oprócz wartości dla dowolnych parametrów w rozwiązaniu.
7. Kliknij **pozycję Kup,** aby zainstalować rozwiązanie.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Instalowanie interfejsu wiersza polecenia platformy Azure

   Przed uruchomieniem [poleceń referencyjnych interfejsu wiersza](/cli/azure/install-azure-cli) polecenia należy zainstalować interfejs wiersza polecenia platformy Azure.  Jeśli wolisz, możesz również użyć Azure Cloud Shell, aby wykonać kroki opisane w tym artykule.  Azure Cloud Shell to interakcyjne środowisko powłoki, które można używać za pośrednictwem przeglądarki.  Uruchom Cloud Shell przy użyciu jednej z tych metod:

   - Otwórz Cloud Shell, przechodząc do [https://shell.azure.com](https://shell.azure.com)

   - Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu okna [Azure Portal](https://portal.azure.com)

1. Zaloguj się.

   Jeśli używasz lokalnej instalacji interfejsu wiersza polecenia, zaloguj się przy użyciu [polecenia az login.](/cli/azure/reference-index#az_login)  Wykonaj kroki wyświetlane w terminalu, aby ukończyć proces uwierzytelniania.

    ```azurecli
    az login
    ```

1. Instalowanie `log-analytics-solution` rozszerzenia

   To `log-analytics-solution` polecenie jest eksperymentalnym rozszerzeniem podstawowego interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się więcej na temat odwołań do rozszerzeń, [zobacz Używanie rozszerzenia z interfejsem wiersza polecenia platformy Azure.](/cli/azure/azure-cli-extensions-overview?)

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   Oczekiwane jest następujące ostrzeżenie.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Instalowanie rozwiązania przy użyciu interfejsu wiersza polecenia platformy Azure

Podczas instalowania rozwiązania należy wybrać obszar roboczy usługi [Log Analytics,](../logs/manage-access.md) w którym rozwiązanie zostanie zainstalowane i gdzie będą zbierane jego dane.  Za pomocą interfejsu wiersza polecenia platformy Azure możesz zarządzać obszarami roboczymi za pomocą [poleceń az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace) reference.  Postępuj zgodnie z procesem opisanym w te [tematach Log Analytics workspace and Automation account](#log-analytics-workspace-and-automation-account) (Obszar roboczy usługi Log Analytics i konto usługi Automation), aby połączyć obszar roboczy i konto.

Użyj narzędzia [az monitor log-analytics solution create,](/cli/azure/monitor/log-analytics/solution) aby zainstalować rozwiązanie do monitorowania.  Parametry w nawiasach kwadratowych są opcjonalne.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Oto przykładowy kod, który tworzy rozwiązanie do analizy dzienników dla produktu planu OMSGallery/Containers.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Instalowanie programu Azure PowerShell

   Przed [uruchomieniem polecenia Azure PowerShell](/powershell/azure/install-az-ps) polecenia referencyjnego Azure PowerShell należy Azure PowerShell zainstalować program . Jeśli wolisz, możesz również użyć Azure Cloud Shell, aby wykonać kroki opisane w tym artykule. Azure Cloud Shell to interaktywne środowisko powłoki, z których korzystasz w przeglądarce. Uruchom Cloud Shell przy użyciu jednej z tych metod:

   - Otwórz Cloud Shell, przechodząc do [https://shell.azure.com](https://shell.azure.com)

   - Wybierz przycisk **Cloud Shell** na pasku menu w prawym górnym rogu okna [Azure Portal](https://portal.azure.com)

   > [!IMPORTANT]
   > Gdy moduł **Az.MonitoringSolutions** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Zaloguj się.

   Jeśli używasz lokalnej instalacji programu PowerShell, zaloguj się przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Wykonaj kroki wyświetlane w programie PowerShell, aby ukończyć proces uwierzytelniania.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Instalowanie rozwiązania za pomocą Azure PowerShell

Podczas instalowania rozwiązania należy wybrać obszar roboczy usługi [Log Analytics,](../logs/manage-access.md) w którym rozwiązanie zostanie zainstalowane i gdzie będą zbierane jego dane. Za Azure PowerShell obszarami roboczymi można zarządzać przy użyciu poleceń cmdlet w module [Az.MonitoringSolutions programu](/powershell/module/az.monitoringsolutions) PowerShell. Postępuj zgodnie z procesem opisanym w te [tematach Log Analytics workspace and Automation account (Obszar roboczy](#log-analytics-workspace-and-automation-account) usługi Log Analytics i konto usługi Automation), aby połączyć obszar roboczy i konto.

Użyj polecenia cmdlet [New-AzMonitorLogAnalyticsSolution,](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) aby zainstalować rozwiązanie do monitorowania. Parametry w nawiasach kwadratowych są opcjonalne.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

Poniższy przykład tworzy rozwiązanie do monitorowania analizy dzienników dla obszaru roboczego usługi Log Analytics.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy usługi Log Analytics i konto usługi Automation

Wszystkie rozwiązania do monitorowania wymagają obszaru [roboczego usługi Log Analytics](../logs/manage-access.md) do przechowywania danych zebranych przez rozwiązanie oraz hostowania jego przeszukiwania dzienników i widoków. Niektóre rozwiązania wymagają również konta [usługi Automation, aby](../../automation/automation-security-overview.md) zawierało elementy Runbook i powiązane zasoby. Obszar roboczy i konto muszą spełniać następujące wymagania.

* Każda instalacja rozwiązania może używać tylko jednego obszaru roboczego usługi Log Analytics i jednego konta usługi Automation. Rozwiązanie można zainstalować oddzielnie w wielu obszarach roboczych.
* Jeśli rozwiązanie wymaga konta usługi Automation, obszar roboczy usługi Log Analytics i konto usługi Automation muszą być ze sobą połączone. Obszar roboczy usługi Log Analytics może być połączony tylko z jednym kontem usługi Automation, a konto usługi Automation może być połączone tylko z jednym obszarem roboczym usługi Log Analytics.

Podczas instalowania rozwiązania za Azure Marketplace zostanie wyświetlony monit o obszar roboczy i konto usługi Automation. Połączenie między nimi jest tworzone, jeśli nie zostało jeszcze połączone.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Weryfikowanie połączenia między obszarem roboczym usługi Log Analytics i kontem usługi Automation

Aby sprawdzić połączenie między obszarem roboczym usługi Log Analytics i kontem usługi Automation, należy użyć poniższej procedury.

1. Wybierz konto usługi Automation w Azure Portal.
1. Przewiń menu **do sekcji Powiązane** zasoby i wybierz pozycję Połączony obszar **roboczy.**
1. Jeśli obszar **roboczy jest** połączony z kontem usługi Automation, na tej stronie znajduje się obszar roboczy, z który jest połączony. Jeśli wybierzesz nazwę wymienionego obszaru roboczego, nastąpi przekierowanie do strony przeglądu dla tego obszaru roboczego.

## <a name="remove-a-monitoring-solution"></a>Usuwanie rozwiązania do monitorowania

### <a name="portal"></a>[Portal](#tab/portal)

Aby usunąć zainstalowane rozwiązanie przy użyciu portalu, znajdź je na [liście zainstalowanych rozwiązań.](#list-installed-monitoring-solutions) Kliknij nazwę rozwiązania, aby otworzyć jego stronę podsumowania, a następnie kliknij pozycję **Usuń.**

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć zainstalowane rozwiązanie przy użyciu interfejsu wiersza polecenia platformy Azure, użyj [polecenia az monitor log-analytics solution delete.](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete)

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Aby usunąć zainstalowane rozwiązanie przy użyciu Azure PowerShell, użyj polecenia cmdlet [Remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Następne kroki

* Pobierz listę [rozwiązań do monitorowania od firmy Microsoft.](../monitor-reference.md)
* Dowiedz się, jak [tworzyć zapytania w](../logs/log-query-overview.md) celu analizowania danych zbieranych przez rozwiązania do monitorowania.
* Zobacz wszystkie polecenia [interfejsu wiersza polecenia platformy Azure dla Azure Monitor](/cli/azure/azure-cli-reference-for-monitor).