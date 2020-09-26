---
title: Wyświetlanie dzienników aktywności platformy Azure w celu monitorowania zasobów
description: Użyj dzienników aktywności, aby przejrzeć akcje i błędy użytkownika. Pokazuje Azure Portal PowerShell, interfejs wiersza polecenia platformy Azure i REST.
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 14015e9b2792515e6818af551b8bd9f54c686bee
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371596"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>Wyświetlanie dzienników aktywności w celu monitorowania akcji dotyczących zasobów

Za pomocą dzienników aktywności można określić:

* jakie operacje zostały wykonane względem zasobów w ramach subskrypcji
* kto uruchomił operację
* gdy wystąpiła operacja
* stan operacji
* wartości innych właściwości, które mogą pomóc w zbadaniu operacji

Dziennik aktywności zawiera wpisy dla wszystkich operacji zapisu (PUT, POST, DELETE) dotyczących zasobów. Nie obejmuje on operacji odczytu (GET). Listę akcji zasobów można znaleźć w temacie [operacje dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md). Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji.

Dzienniki aktywności są przechowywane przez 90 dni. Możesz wykonać zapytanie dotyczące dowolnego zakresu dat, jednak data początkowa nie może być wcześniejsza od daty bieżącej o więcej niż 90 dni.

Możesz pobrać informacje z dzienników aktywności za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure, API REST lub [biblioteki usługi Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="azure-portal"></a>Azure Portal

Aby wyświetlić dzienniki aktywności w portalu, wykonaj następujące kroki:

1. W menu Azure Portal wybierz pozycję **Monitoruj**lub Wyszukaj i wybierz pozycję **Monitoruj** z dowolnej strony.

    ![Wybierz monitor](./media/view-activity-logs/select-monitor-from-menu.png)

1. Wybierz pozycję **Dziennik aktywności**.

    ![Wybierz Dziennik aktywności](./media/view-activity-logs/select-activity-log.png)

1. Zobaczysz podsumowanie ostatnich operacji. Do operacji jest stosowany domyślny zestaw filtrów. Zwróć uwagę na informacje na temat tego, kto uruchomił akcję i kiedy wystąpił.

    ![Wyświetl podsumowanie ostatnich operacji](./media/view-activity-logs/audit-summary.png)

1. Aby szybko uruchomić wstępnie zdefiniowany zestaw filtrów, wybierz pozycję **szybki wgląd w szczegółowe informacje**.

    ![Wybieranie szybkiego wglądu w szczegółowe dane](./media/view-activity-logs/select-quick-insights.png)

1. Wybierz jedną z opcji. Na przykład wybierz pozycję **wdrożenia zakończone niepowodzeniem** , aby zobaczyć błędy wdrożeń.

    ![Wybieranie wdrożeń zakończonych niepowodzeniem](./media/view-activity-logs/select-failed-deployments.png)

1. Zauważ, że filtry zostały zmienione, aby skoncentrować się na błędach wdrożenia w ciągu ostatnich 24 godzin. Wyświetlane są tylko operacje pasujące do filtrów.

    ![Filtry widoku](./media/view-activity-logs/view-filters.png)

1. Aby skoncentrować się na określonych operacjach, zmień filtry lub Zastosuj nowe. Na przykład na poniższej ilustracji przedstawiono nową wartość ustawienia **TimeSpan** , a **Typ zasobu** to konta magazynu.

    ![Ustawianie opcji filtru](./media/view-activity-logs/set-filter.png)

1. Jeśli musisz ponownie uruchomić zapytanie później, wybierz pozycję **Przypnij bieżące filtry**.

    ![Przypnij filtry](./media/view-activity-logs/pin-filters.png)

1. Nadaj nazwę filtrowi.

    ![Filtry nazw](./media/view-activity-logs/name-filters.png)

1. Filtr jest dostępny na pulpicie nawigacyjnym. W menu witryny Azure Portal wybierz pozycję **Pulpit nawigacyjny**.

    ![Pokaż filtr na pulpicie nawigacyjnym](./media/view-activity-logs/activity-log-on-dashboard.png)

1. W portalu można wyświetlić zmiany w zasobie. Wróć do widoku domyślnego w monitorze i wybierz operację, która obejmuje zmianę zasobu.

    ![Wybierz operację](./media/view-activity-logs/select-operation.png)

1. Wybierz pozycję **historia zmian (wersja zapoznawcza)** i wybierz jedną z dostępnych operacji.

    ![Wybierz historię zmian](./media/view-activity-logs/select-change-history.png)

1. Zostaną wyświetlone zmiany w zasobie.

    ![Pokaż zmiany](./media/view-activity-logs/show-changes.png)

Aby dowiedzieć się więcej na temat historii zmian, zobacz [pobieranie zmian zasobów](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby pobrać wpisy dziennika, uruchom polecenie **Get-AzLog** . Podajesz dodatkowe parametry filtrowania listy wpisów. Jeśli nie określisz godziny rozpoczęcia i zakończenia, zwracane są wpisy z ostatnich siedmiu dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

Poniższy przykład pokazuje, jak za pomocą dziennika aktywności zbadać operacje wykonywane w określonym czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

Lub można użyć funkcji daty, aby określić zakres dat, taki jak ostatnie 14 dni.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

Można wyszukiwać akcje wykonywane przez określonego użytkownika.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

Można filtrować dla operacji zakończonych niepowodzeniem.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

Możesz skupić się na jednym błędzie, przeglądając komunikat o stanie dla tego wpisu.

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

Możesz wybrać określone wartości, aby ograniczyć zwracane dane.

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

W zależności od określonego czasu rozpoczęcia poprzednie polecenia mogą zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki dla tego, czego szukasz, dostarczając kryteria wyszukiwania. Na przykład można filtrować według typu operacji.

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Możesz użyć grafu zasobów, aby wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [pobieranie zmian zasobów](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby pobrać wpisy dziennika, uruchom polecenie [AZ monitor Activity-Log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) z przesunięciem w celu wskazania przedziału czasu.

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

Poniższy przykład pokazuje, jak za pomocą dziennika aktywności zbadać operacje wykonywane w określonym czasie. Daty rozpoczęcia i zakończenia są określone w formacie daty.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

Można wyszukiwać akcje wykonywane przez określonego użytkownika, nawet w przypadku grupy zasobów, która już nie istnieje.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

Można filtrować dla operacji zakończonych niepowodzeniem.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

Możesz skupić się na jednym błędzie, przeglądając komunikat o stanie dla tego wpisu.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

Możesz wybrać określone wartości, aby ograniczyć zwracane dane.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

W zależności od określonego czasu rozpoczęcia poprzednie polecenia mogą zwracać długą listę operacji dla grupy zasobów. Możesz filtrować wyniki dla tego, czego szukasz, dostarczając kryteria wyszukiwania. Na przykład można filtrować według typu operacji.

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Możesz użyć grafu zasobów, aby wyświetlić historię zmian dla zasobu. Aby uzyskać więcej informacji, zobacz [pobieranie zmian zasobów](../../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="rest-api"></a>Interfejs API REST

Operacje REST do pracy z dziennikiem aktywności są częścią [interfejsu API REST szczegółowych informacji](/rest/api/monitor/). Aby pobrać zdarzenia dziennika aktywności, zobacz [Wyświetlanie listy zdarzeń zarządzania w subskrypcji](/rest/api/monitor/activitylogs).

## <a name="next-steps"></a>Następne kroki

* Dzienników aktywności platformy Azure można używać w połączeniu z Power BI, aby uzyskać więcej szczegółowych informacji o akcjach w ramach subskrypcji. Zobacz [Wyświetlanie i analizowanie dzienników aktywności platformy Azure w Power BI i nie tylko](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Aby dowiedzieć się więcej o ustawianiu zasad zabezpieczeń, zobacz [Kontrola dostępu oparta na rolach na platformie Azure (RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Aby wyświetlić więcej szczegółów o zmianach w aplikacjach z warstwy infrastruktury we wszystkich sposobach wdrażania aplikacji, zobacz [Korzystanie z analizy zmian aplikacji w Azure monitor](../../azure-monitor/app/change-analysis.md).
* Aby dowiedzieć się więcej na temat poleceń do wyświetlania operacji wdrażania, zobacz [Wyświetlanie operacji wdrażania](../templates/deployment-history.md).
* Aby dowiedzieć się, jak uniemożliwić usuwanie zasobów dla wszystkich użytkowników, zobacz temat [blokowanie zasobów przy użyciu Azure Resource Manager](lock-resources.md).
* Aby wyświetlić listę operacji dostępnych dla każdego Microsoft Azure dostawcy Menedżer zasobów, zobacz [operacje dostawcy zasobów platformy Azure](../../role-based-access-control/resource-provider-operations.md)
