---
title: 'Samouczek: monitorowanie dzienników i metryk usługi Azure Firewall'
description: W tym samouczku dowiesz się, jak włączyć dzienniki i metryki usługi Azure Firewall i zarządzać nimi.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: da46cf826da40658883d22692e5038b09d222907
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75974529"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Samouczek: monitorowanie dzienników i metryk usługi Azure Firewall

Usługę Azure Firewall możesz monitorować przy użyciu dzienników zapory. Ponadto dzienniki aktywności umożliwiają inspekcję operacji wykonywanych względem zasobów usługi Azure Firewall. Za pomocą metryk możesz wyświetlać liczniki wydajności w portalu.

Niektóre z tych dzienników są dostępne za pośrednictwem portalu. Dzienniki mogą być wysyłane do [dzienników usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), usługi Storage i Event Hubs oraz analizowane za pomocą dzienników usługi Azure Monitor lub innych narzędzi, takich jak program Excel i usługa Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie rejestrowania za pośrednictwem witryny Azure Portal
> * Włączanie rejestrowania przy użyciu programu PowerShell
> * Wyświetlanie i analizowanie dziennika aktywności
> * Wyświetlanie i analizowanie dzienników reguł sieci i aplikacji
> * Wyświetlanie metryk


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy przeczytać sekcję dotyczącą [dzienników i metryk usługi Azure Firewall](logs-and-metrics.md), która zawiera omówienie dzienników i metryk dostępnych dla usługi Azure Firewall.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Włączanie rejestrowania diagnostycznego za pośrednictwem witryny Azure Portal

Od wykonania tej procedury w celu włączenia rejestrowania diagnostycznego może upłynąć kilka minut, zanim dane pojawią się w dziennikach. Jeśli na początku nie widzisz żadnych danych, sprawdź ponownie za kilka minut.

1. W witrynie Azure Portal otwórz grupę zasobów zapory, a następnie kliknij zaporę.
2. W obszarze **monitorowanie**kliknij pozycję **Ustawienia diagnostyczne**.

   W przypadku usługi Azure Firewall są dostępne dwa dzienniki specyficzne dla usługi:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Aby rozpocząć zbieranie danych, kliknij pozycję **Włącz diagnostykę**.
4. Strona **Ustawienia diagnostyczne** zawiera ustawienia dzienników diagnostycznych.
5. W tym przykładzie dzienniki są przechowywane w dziennikach usługi Azure Monitor, więc wpisz nazwę **Firewall log analytics**.
6. Kliknij pozycję **Wyślij do usługi Log Analytics** w celu skonfigurowania obszaru roboczego. Na potrzeby zapisywania dzienników diagnostycznych można także skorzystać z usługi Event Hubs i konta magazynu.
7. W obszarze **Log Analytics** kliknij pozycję **Konfiguruj**.
8. Na stronie Obszary robocze usługi Log Analytics kliknij pozycję **Utwórz nowy obszar roboczy**.
9. Na stronie **Obszar roboczy usługi Log Analytics** wpisz nazwę **firewall-oms** dla nowego obszaru w polu **Obszar roboczy OMS**.
10. Wybierz swoją subskrypcję, użyj istniejącej grupy zasobów zapory (**Test-FW-RG**), a następnie wybierz lokalizację **Wschodnie stany USA** i warstwę cenową **Bezpłatna**.
11. Kliknij przycisk **OK**.
   ![Uruchamianie procesu konfiguracji][1] Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.  
12. W obszarze **Dziennik** kliknij pozycję **AzureFirewallApplicationRule** i **AzureFirewallNetworkRule**, aby zbierać dzienniki dla reguł aplikacji i sieci.
   ![Zapisywanie ustawień diagnostycznych][2]
13. Kliknij przycisk **Zapisz**.

## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Rejestrowanie aktywności jest automatycznie włączone dla wszystkich zasobów usługi Resource Manager. Aby rozpocząć zbieranie danych dostępnych za pośrednictwem tych dzienników, należy włączyć rejestrowanie diagnostyczne.

Aby włączyć rejestrowanie diagnostyczne, wykonaj następujące kroki:

1. Zanotuj identyfikator zasobu konta magazynu, w ramach którego są przechowywane dane dzienników. Ta wartość ma postać: */subscriptions/\<identyfikatorSubskrypcji\>/resourceGroups/\<nazwa grupy zasobów\>/providers/Microsoft.Storage/storageAccounts/\<nazwa konta magazynu\>*.

   Użyć możesz dowolnego konta magazynu w ramach subskrypcji. Te informacje możesz znaleźć w witrynie Azure Portal. Znajdują się one na stronie **Właściwości** zasobu.

2. Zanotuj identyfikator zasobu zapory, dla której rejestrowanie jest włączane. Ta wartość ma postać: */subscriptions/\<identyfikatorSubskrypcji\>/resourceGroups/\<nazwa grupy zasobów\>/providers/Microsoft.Network/azureFirewalls/\<nazwa zapory\>*.

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
Przejdź do usługi Azure Firewall, a następnie w obszarze **Monitorowanie** kliknij pozycję **Metryki**. Aby wyświetlić dostępne wartości, wybierz listę rozwijaną **METRYKA**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy skonfigurowano zaporę na potrzeby zbierania dzienników, możesz eksplorować dzienniki usługi Azure Monitor, aby wyświetlać dane.

> [!div class="nextstepaction"]
> [Rozwiązania do monitorowania sieci w dziennikach usługi Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
