---
title: Wyświetlanie dzienników aktywności platformy Azure w celu monitorowania zasobów
description: Użyj dzienników aktywności, aby przejrzeć akcje i błędy użytkownika. Pokazuje Azure Portal PowerShell, interfejs wiersza polecenia platformy Azure i interfejs REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 7612146a0f9407663631f87c57f30ea4c590c7a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773931"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Wyświetlanie dzienników aktywności w celu monitorowania akcji w zasobach

Za pomocą dzienników aktywności można określić:

* jakie operacje zostały wykonane na zasobach w ramach subskrypcji
* kto uruchomił operację
* czas, w którym wystąpiła operacja
* stan operacji
* wartości innych właściwości, które mogą ułatwić badanie operacji

Dziennik aktywności zawiera wpisy dla wszystkich operacji zapisu (PUT, POST, DELETE) dotyczących zasobów. Nie obejmuje on operacji odczytu (GET). Aby uzyskać listę akcji zasobów, zobacz [Operacje dostawcy zasobów platformy Azure.](../../role-based-access-control/resource-provider-operations.md) Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji.

Dzienniki aktywności są przechowywane przez 90 dni. Możesz wykonać zapytanie dotyczące dowolnego zakresu dat, jednak data początkowa nie może być wcześniejsza od daty bieżącej o więcej niż 90 dni.

Informacje z dzienników aktywności można pobrać za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST usługi Insights lub biblioteki [.NET Insights.](https://www.nuget.org/packages/Microsoft.Azure.Insights/)

## <a name="azure-portal"></a>Azure Portal

Aby wyświetlić dzienniki aktywności za pośrednictwem portalu, wykonaj następujące kroki:

1. W menu Azure Portal wybierz pozycję **Monitoruj** lub wyszukaj i wybierz **pozycję Monitoruj** na dowolnej stronie.

    ![Wybieranie monitora](./media/view-activity-logs/select-monitor-from-menu.png)

1. Wybierz **pozycję Dziennik aktywności.**

    ![Wybieranie dziennika aktywności](./media/view-activity-logs/select-activity-log.png)

1. Zostanie wyświetlony podsumowanie ostatnich operacji. Do operacji jest stosowany domyślny zestaw filtrów. Zwróć uwagę, że w podsumowaniu znajdują się informacje o tym, kto i kiedy uruchomił akcję.

    ![Wyświetlanie podsumowania ostatnich operacji](./media/view-activity-logs/audit-summary.png)

1. Aby szybko uruchomić wstępnie zdefiniowany zestaw filtrów, wybierz pozycję **Szybki wgląd w szczegółowe informacje**.

    ![Wybieranie szybkiego wglądu w szczegółowe dane](./media/view-activity-logs/select-quick-insights.png)

1. Wybierz jedną z opcji. Na przykład wybierz pozycję **Wdrożenia nieudane,** aby wyświetlić błędy z wdrożeń.

    ![Wybieranie wdrożeń, które zakończyły się niepowodzeniem](./media/view-activity-logs/select-failed-deployments.png)

1. Zwróć uwagę, że filtry zostały zmienione w celu skoncentrowania się na błędach wdrażania w ciągu ostatnich 24 godzin. Wyświetlane są tylko operacje zgodne z filtrami.

    ![Filtry widoku](./media/view-activity-logs/view-filters.png)

1. Aby skoncentrować się na określonych operacjach, zmień filtry lub zastosuj nowe. Na przykład na poniższej ilustracji przedstawiono nową wartość dla czasu **i** **typ** zasobu jest ustawiony na konta magazynu.

    ![Ustawianie opcji filtrowania](./media/view-activity-logs/set-filter.png)

1. Jeśli musisz uruchomić zapytanie ponownie później, wybierz pozycję **Przypnij bieżące filtry**.

    ![Przypinanie filtrów](./media/view-activity-logs/pin-filters.png)

1. Nadaj filtrowi nazwę.

    ![Filtry nazw](./media/view-activity-logs/name-filters.png)

1. Filtr jest dostępny na pulpicie nawigacyjnym. W menu witryny Azure Portal wybierz pozycję **Pulpit nawigacyjny**.

    ![Wyświetlanie filtru na pulpicie nawigacyjnym](./media/view-activity-logs/activity-log-on-dashboard.png)

1. W portalu można wyświetlić zmiany w zasobie. Wstecz widoku domyślnego w monitorze i wybierz operację, która dotyczyła zmiany zasobu.

    ![Wybieranie operacji](./media/view-activity-logs/select-operation.png)

1. Wybierz **pozycję Historia zmian (wersja zapoznawcza)** i wybierz jedną z dostępnych operacji.

    ![Wybieranie historii zmian](./media/view-activity-logs/select-change-history.png)

1. Zostaną wyświetlone zmiany w zasobie.

    ![Pokaż zmiany](./media/view-activity-logs/show-changes.png)

Aby dowiedzieć się więcej na temat historii zmian, zobacz [Get resource changes (Uzyskiwanie zmian zasobów).](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby pobrać wpisy dziennika, uruchom **polecenie Get-AzLog.** Aby filtrować listę wpisów, należy podać dodatkowe parametry. Jeśli nie określisz czasu rozpoczęcia i zakończenia, zostaną zwrócone wpisy z ostatnich siedmiu dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

W poniższym przykładzie pokazano, jak używać dziennika aktywności do badania operacji wykonywanych w określonym czasie. Daty rozpoczęcia i zakończenia są określane w formacie daty.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Możesz też użyć funkcji daty, aby określić zakres dat, na przykład ostatnie 14 dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Możesz sprawdzić akcje wykonane przez określonego użytkownika.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Można filtrować pod celu wyszukiwania operacji, które zakończyły się niepowodzeniem.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Możesz skupić się na jednym błędzie, patrząc na komunikat o stanie dla tego wpisu.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Możesz wybrać określone wartości, aby ograniczyć zwracane dane.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

W zależności od wybranego czasu rozpoczęcia poprzednie polecenia mogą zwracać długą listę operacji dla grupy zasobów. Wyniki można filtrować według tego, czego szukasz, podając kryteria wyszukiwania. Na przykład można filtrować według typu operacji.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Możesz użyć Resource Graph, aby wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [Get resource changes (Uzyskiwanie zmian zasobów).](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby pobrać wpisy dziennika, uruchom [polecenie az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) z przesunięciem, aby wskazać zakres czasu.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

W poniższym przykładzie pokazano, jak używać dziennika aktywności do badania operacji wykonywanych w określonym czasie. Daty rozpoczęcia i zakończenia są określane w formacie daty.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Możesz sprawdzić akcje wykonane przez określonego użytkownika, nawet w przypadku grupy zasobów, która już nie istnieje.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Można filtrować pod celu wyszukiwania operacji, które zakończyły się niepowodzeniem.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Możesz skupić się na jednym błędzie, patrząc na komunikat o stanie dla tego wpisu.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Możesz wybrać określone wartości, aby ograniczyć zwracane dane.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

W zależności od wybranego czasu rozpoczęcia poprzednie polecenia mogą zwracać długą listę operacji dla grupy zasobów. Wyniki można filtrować pod celu wyszukania, podając kryteria wyszukiwania. Można na przykład filtrować według typu operacji.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Możesz użyć Resource Graph, aby wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [Get resource changes (Uzyskiwanie zmian zasobów).](../../governance/resource-graph/how-to/get-resource-changes.md)

## <a name="rest-api"></a>Interfejs API REST

Operacje REST do pracy z dziennikiem aktywności są częścią [interfejsu API REST szczegółowych informacji](/rest/api/monitor/). Aby pobrać zdarzenia dziennika aktywności, zobacz [Wyświetlanie listy zdarzeń zarządzania w subskrypcji](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Następne kroki

* Dzienniki aktywności platformy Azure mogą być używane z Power BI, aby uzyskać lepszy wgląd w akcje w ramach subskrypcji. Zobacz [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w Power BI i nie tylko.](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)
* Aby dowiedzieć się więcej na temat ustawiania zasad zabezpieczeń, zobacz [Azure role-based access control (Azure RBAC) (Kontrola](../../role-based-access-control/role-assignments-portal.md)dostępu oparta na rolach na platformie Azure).
* Aby wyświetlić więcej szczegółów na temat zmian w aplikacjach od warstwy infrastruktury do wdrożenia aplikacji, zobacz Use [Analiza zmian aplikacji in Azure Monitor](../../azure-monitor/app/change-analysis.md)(
* Aby dowiedzieć się więcej o poleceniach wyświetlania operacji wdrażania, [zobacz Wyświetlanie operacji wdrażania](../templates/deployment-history.md).
* Aby dowiedzieć się, jak zapobiegać usuwaniu zasobów dla wszystkich użytkowników, zobacz Blokowanie zasobów za [pomocą Azure Resource Manager](lock-resources.md).
* Aby wyświetlić listę operacji dostępnych dla każdego dostawcy Microsoft Azure Resource Manager, zobacz [Operacje dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md)
