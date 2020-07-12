---
title: Monitorowanie dzienników i metryk usługi Azure Firewall
description: W tym artykule dowiesz się, jak włączyć dzienniki i metryki zapory platformy Azure oraz zarządzać nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 07/10/2020
ms.author: victorh
ms.openlocfilehash: e55f92c624ec338005cd23ba5e6063a9ca7f82c4
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262050"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Monitorowanie dzienników i metryk usługi Azure Firewall

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall. Za pomocą metryk możesz wyświetlać liczniki wydajności w portalu.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do [dzienników usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), usługi Storage i Event Hubs oraz analizowane za pomocą dzienników usługi Azure Monitor lub innych narzędzi, takich jak program Excel i usługa Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy zapoznać się z tematem [dzienniki i metryki zapory platformy Azure](logs-and-metrics.md) , aby zapoznać się z omówieniem dzienników diagnostycznych i metryk dostępnych dla zapory platformy Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Włączanie rejestrowania diagnostycznego za pośrednictwem witryny Azure Portal

Od wykonania tej procedury w celu włączenia rejestrowania diagnostycznego może upłynąć kilka minut, zanim dane pojawią się w dziennikach. Jeśli na początku nie widzisz żadnych danych, sprawdź ponownie za kilka minut.

1. W Azure Portal Otwórz grupę zasobów zapory i wybierz zaporę.
2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**.

   W przypadku usługi Azure Firewall są dostępne dwa dzienniki specyficzne dla usługi:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Wybierz pozycję **Dodaj ustawienie diagnostyczne**. Strona **Ustawienia diagnostyczne** zawiera ustawienia dzienników diagnostycznych.
5. W tym przykładzie dzienniki są przechowywane w dziennikach usługi Azure Monitor, więc wpisz nazwę **Firewall log analytics**.
6. W obszarze **Dziennik**wybierz pozycję **AzureFirewallApplicationRule** i **AzureFirewallNetworkRule** , aby zebrać dzienniki dla reguł aplikacji i sieci.
7. Wybierz pozycję **Wyślij do log Analytics** , aby skonfigurować obszar roboczy.
8. Wybierz subskrypcję.
9. Wybierz pozycję **Zapisz**.

## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Aby rozpocząć zbieranie danych dostępnych za pośrednictwem tych dzienników, należy włączyć rejestrowanie diagnostyczne.

Aby włączyć rejestrowanie diagnostyczne, wykonaj następujące kroki:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać: */subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> *.

   Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal. Znajdują się one na stronie **Właściwości** zasobu.

2. Zanotuj identyfikator zasobu zapory, dla której rejestrowanie jest włączane. Ta wartość ma postać: */subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\> *.

   Te informacje możesz znaleźć w portalu.

3. Włącz rejestrowanie diagnostyczne przy użyciu następującego polecenia cmdlet programu PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Dzienniki diagnostyczne nie wymagają oddzielnego konta magazynu. Użycie magazynu na potrzeby rejestrowania danych o dostępie i wydajności powoduje naliczenie opłat za usługę.

## <a name="view-and-analyze-the-activity-log"></a>Wyświetlanie i analizowanie dziennika aktywności

Dane dziennika aktywności można wyświetlać i analizować przy użyciu dowolnej z następujących metod:

* **Narzędzia platformy Azure**: pobierz informacje z dziennika aktywności przy użyciu programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST platformy Azure lub witryny Azure Portal. Instrukcje krok po kroku dla każdej metody są szczegółowo opisane w artykule [Activity operations with Resource Manager (Operacje działań przy użyciu usługi Resource Manager)](../azure-resource-manager/management/view-activity-logs.md).
* **Usługa Power BI**: jeśli nie masz jeszcze konta usługi [Power BI](https://powerbi.microsoft.com/pricing), możesz ją wypróbować bezpłatnie. Korzystając z [pakietu zawartości dzienników aktywności platformy Azure dla usługi Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), możesz analizować dane przy użyciu wstępnie skonfigurowanych pulpitów nawigacyjnych, których możesz używać bez zmian lub po dostosowaniu.

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

> [!div class="nextstepaction"]
> [Rozwiązania do monitorowania sieci w dziennikach usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)
