---
title: Uaktualnij do bieżącego interfejsu API alertów dziennika Azure Monitor
description: Dowiedz się, jak przełączyć się do interfejsu API ScheduledQueryRules alertów dziennika
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294517"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Uaktualnij do bieżącego interfejsu API alertów dziennika ze starszej wersji interfejsu API alertu Log Analytics

> [!NOTE]
> Ten artykuł ma zastosowanie tylko do publicznej platformy Azure (**nie** do Azure Government lub chmury z Chinami platformy Azure).

> [!NOTE]
> Gdy użytkownik zdecyduje się na przełączenie preferencji do bieżącego [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) , nie jest możliwe przywrócenie starszego, starszego [interfejsu api alertów log Analytics](api-alerts.md).

W przeszłości użytkownicy korzystali z [starszego interfejsu API alertów log Analytics](api-alerts.md) , aby zarządzać regułami alertów dziennika. Bieżące obszary robocze używają [interfejsu API ScheduledQueryRules](/rest/api/monitor/scheduledqueryrules). W tym artykule opisano zalety i proces przełączania ze starszego interfejsu API do bieżącego interfejsu API.

## <a name="benefits"></a>Korzyści

- Pojedynczy szablon służący do tworzenia reguł alertów (wcześniej wymaganych trzech oddzielnych szablonów).
- Pojedynczy interfejs API dla obu Log Analytics obszarów roboczych lub zasobów Application Insights.
- [Obsługa poleceń cmdlet programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Wyrównanie wartości dla wszystkich typów alertów.
- Możliwość tworzenia [alertów dziennika obejmującego wiele obszarów roboczych](../log-query/cross-workspace-query.md) obejmujących kilka zasobów zewnętrznych, takich jak log Analytics obszarów roboczych lub zasobów Application Insights.
- Użytkownicy mogą określić wymiary, aby podzielić alerty za pomocą parametru "Aggregate on".
- Alerty dzienników mają dłuższy okres niż dwa dni (wcześniej ograniczone do jednego dnia).

## <a name="impact"></a>Wpływ

- Wszystkie nowe reguły muszą zostać utworzone/zmodyfikowane przy użyciu bieżącego interfejsu API. Zobacz [użycie przykładowe za pomocą szablonu zasobów platformy Azure](alerts-log-create-templates.md) i [przykładowego użycia za pośrednictwem programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Ponieważ reguły stają się Azure Resource Manager monitorowanymi zasobami w bieżącym interfejsie API i muszą być unikatowe, identyfikator zasobu reguł zmieni się na tę strukturę: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Nazwy wyświetlane reguły alertu pozostaną niezmienione.

## <a name="process"></a>Proces

Proces przełączania nie jest interaktywny i nie wymaga ręcznej czynności, w większości przypadków. Reguły alertów nie są zatrzymane lub wstrzymane, w trakcie lub po przełączniku.
Wykonaj to wywołanie, aby przełączyć wszystkie reguły alertów skojarzone z określonym obszarem roboczym Log Analytics:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Z treścią żądania zawierającego Poniższy kod JSON:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Oto przykład użycia [ARMClient](https://github.com/projectkudu/ARMClient), narzędzia wiersza polecenia typu open source, które upraszcza wywoływanie powyższego wywołania interfejsu API:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Jeśli przełącznik zakończy się pomyślnie, odpowiedź jest:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Sprawdź stan przełączania obszaru roboczego

Można również użyć tego wywołania interfejsu API, aby sprawdzić stan przełącznika:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Możesz również użyć narzędzia [ARMClient](https://github.com/projectkudu/ARMClient) :

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Jeśli obszar roboczy Log Analytics został przełączony do [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules), odpowiedź to:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Jeśli obszar roboczy Log Analytics nie został przełączony, odpowiedź:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach dziennika Azure monitor](alerts-unified-log.md).
- Dowiedz się [, jak zarządzać alertami dzienników przy użyciu interfejsu API](alerts-log-create-templates.md).
- Dowiedz się, jak [zarządzać alertami dziennika przy użyciu programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Dowiedz się więcej o [funkcji alertów platformy Azure](./alerts-overview.md).
