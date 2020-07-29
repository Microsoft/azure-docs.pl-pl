---
title: Przejdź do nowego interfejsu API alertów platformy Azure
description: Przegląd starszej wersji zapisanego wyszukiwania opartej Log Analytics na interfejsie API alertów i proces przełączania reguł alertów na nowy interfejs API ScheduledQueryRules, ze szczegółowymi informacjami dotyczącymi typowych problemów klientów.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 7e1073817013d45558a9679a4f70db0c002cfaa9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324084"
---
# <a name="switch-api-preference-for-log-alerts"></a>Przełącz preferencję interfejsu API dla alertów dziennika

> [!NOTE]
> Zawartość ma zastosowanie tylko do użytkowników chmury publicznej platformy Azure, a **nie** dla Azure Government lub chmury z Chinami platformy Azure.  

> [!NOTE]
> Gdy użytkownik zdecyduje się na przełączenie preferencji do nowego [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) , nie jest możliwe przywrócenie starszej [wersji interfejsu API alertów programu log Analytics](api-alerts.md).

Do czasu ostatniego reguły alertów są zarządzane w portalu Microsoft Operations Management Suite. Nowe środowisko alertów zostało zintegrowane z różnymi usługami w Microsoft Azure, w tym Log Analytics, a my poprosimy o rozproszenie [reguł alertów z portalu pakietu OMS na platformę Azure](./alerts-unified-log.md). Jednak aby zapewnić klientom minimalne zakłócenia, proces nie zmienia interfejsu programistycznego dla [interfejsu API alertów log Analytics](api-alerts.md) użycia na podstawie zapisanego wyszukiwania.

Jednak teraz ogłaszamy, Log Analytics użytkownicy z alertami mają prawdziwą alternatywną platformę Azure, [Azure monitor-ScheduledQueryRules interfejs API](/rest/api/monitor/scheduledqueryrules), który jest również odzwierciedleniem w [rozliczeniach na platformie Azure — na potrzeby alertów dzienników](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Aby dowiedzieć się więcej na temat zarządzania alertami dzienników przy użyciu interfejsu API, zobacz [Zarządzanie alertami dzienników przy użyciu szablonu zasobów platformy Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) i [Zarządzanie alertami dzienników przy użyciu programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Zalety przełączania do nowego interfejsu API platformy Azure

Istnieje kilka korzyści związanych z tworzeniem alertów i zarządzaniem nimi za pomocą [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) przez [starszy interfejs api alertów log Analytics](api-alerts.md). Poniżej wymieniono niektóre z najważniejszych poniżej:

- Możliwość [przeszukiwania dzienników w obszarze roboczym](../log-query/cross-workspace-query.md) w regułach alertów i obejmujących zasoby zewnętrzne, takie jak log Analytics obszary robocze, a nawet aplikacje Application Insights
- Gdy wiele pól jest używanych do grupowania w zapytaniu, za pomocą [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) można określić, które pole ma być agregowane w Azure Portal
- Alerty dzienników utworzone za pomocą [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) mogą mieć określony okres do 48 godzin i pobierać dane przez dłuższy okres niż przed
- Tworzenie reguł alertów w jednym zrzucie jako pojedynczy zasób bez konieczności tworzenia trzech poziomów zasobów jako z [starszym interfejsem API alertów log Analytics](api-alerts.md)
- Pojedynczy interfejs programistyczny dla wszystkich wariantów alertów dzienników opartych na zapytaniach na platformie Azure — nowy [interfejs API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) może służyć do zarządzania regułami dla log Analytics, a także Application Insights
- Zarządzanie alertami dzienników przy użyciu [poleceń cmdlet programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Wszystkie nowe funkcje alertów dziennika i przyszłe programowanie będą dostępne tylko za pośrednictwem nowego [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces przełączania ze starszego interfejsu API alertów dziennika

Użytkownicy mogą korzystać z [starszego interfejsu API alertów log Analytics](api-alerts.md) lub nowego [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Reguły alertów utworzone za pomocą obu interfejsów API będą *możliwe do zarządzania tylko przez ten sam interfejs API* — a także z Azure Portal. Domyślnie Azure Monitor będzie nadal używać [starszej wersji interfejsu API log Analytics](api-alerts.md) do tworzenia nowej reguły alertu na podstawie Azure Portal dla istniejących obszarów roboczych log Analytics. Zgodnie z [ogłoszonym nowym obszarem roboczym dziennika utworzonym w dniu lub po 1 czerwca 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) — automatycznie będzie używać nowego [interfejsu API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) , w tym w Azure Portal.

Poniżej przedstawiono wpływ przełączania preferencji do interfejsu API scheduledQueryRules:

- Wszystkie interakcje wykonywane w celu zarządzania alertami dziennika za pośrednictwem interfejsów programistycznych muszą być teraz wykonywane przy użyciu [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) . Aby uzyskać więcej informacji, zobacz przykład [użycia przy użyciu szablonu zasobów platformy Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) i [przykładowego użycia za pośrednictwem programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell) .
- Każda Nowa reguła alertu dziennika utworzona w Azure Portal, zostanie utworzona tylko przy użyciu [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) i umożliwi użytkownikom korzystanie z [dodatkowych funkcji nowego interfejsu API](#benefits-of-switching-to-new-azure-api) za pośrednictwem Azure Portal
- Ważność reguł alertów dziennika zmienia się z: *krytyczne, ostrzegawcze & informacyjne*, do *wartości ważności 0, 1 & 2*. Wraz z opcją tworzenia/aktualizacji reguł alertów o ważności 3 i 4.

Proces przechodzenia reguł alertów ze [starszej wersji interfejsu API alertu log Analytics](api-alerts.md) nie obejmuje zmiany definicji alertu, zapytania ani konfiguracji. Reguły alertów i monitorowanie nie są modyfikowane, a alerty nie zatrzymają się ani nie zostaną wstrzymane podczas przełączania lub po nim. Jedyne zmiany:

- Zmiana preferencji interfejsu API i dostęp do reguł za pośrednictwem nowego interfejsu API.
- Zmodyfikowany identyfikator URI zasobu reguły alertu zawierający identyfikatory użyte w [starszym interfejsie API alertu log Analytics](api-alerts.md) zamiast nazwy reguły alertu w tej strukturze `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Nazwa wyświetlana reguły alertu pozostanie niezmieniona.

Wszyscy klienci, którzy chcą przełączać się dobrowolnie do nowego [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) i blokować użycie ze [starszego interfejsu API alertów log Analytics](api-alerts.md). można to zrobić przez wykonanie wywołania PUT na poniższym interfejsie API w celu przełączenia wszystkich reguł alertów skojarzonych z określonym obszarem roboczym Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Z treścią żądania zawierającego Poniższy kod JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Dostęp do interfejsu API można również uzyskać z wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzia wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API Azure Resource Manager. Jak pokazano poniżej, w przykładzie Umieść wywołanie za pomocą narzędzia ARMclient, aby przełączyć wszystkie reguły alertów skojarzone z określonym obszarem roboczym Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Jeśli przełączenie wszystkich reguł alertów w obszarze roboczym Log Analytics na użycie nowego [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) zakończy się pomyślnie, zostanie dostarczona następująca odpowiedź.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Użytkownicy mogą również sprawdzić bieżący stan obszaru roboczego Log Analytics i sprawdzić, czy jest on lub nie został przełączony do korzystania tylko z [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) . Aby to sprawdzić, użytkownicy mogą wykonać wywołanie GET w poniższym interfejsie API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Aby wykonać powyższe czynności przy użyciu wiersza polecenia programu PowerShell przy użyciu narzędzia [ARMClient](https://github.com/projectkudu/ARMClient) , zobacz Poniższy przykład.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Jeśli określony Log Analytics obszar roboczy został przełączony tak, aby używał tylko [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) ; następnie plik JSON odpowiedzi będzie wymieniony poniżej.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
W przeciwnym razie, jeśli określony obszar roboczy analityczny dziennika nie został jeszcze przełączony do korzystania tylko z [scheduledQueryRules](/rest/api/monitor/scheduledqueryrules) ; następnie plik JSON odpowiedzi będzie wymieniony poniżej.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [alertach dziennika Azure monitor](alerts-unified-log.md).
- Dowiedz się, jak tworzyć [alerty dzienników w usłudze Azure Alerts](alerts-log.md).
- Dowiedz się więcej o [funkcji alertów platformy Azure](./alerts-overview.md).

