---
title: wyrażenie Workspace () w kwerendzie dziennika Azure Monitor | Microsoft Docs
description: Wyrażenie obszaru roboczego jest używane w zapytaniu dziennika Azure Monitor do pobierania danych z określonego obszaru roboczego w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 34b1f850b262a0027d3a9cb2e926bcb2c8a49665
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710910"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>wyrażenie obszaru roboczego () w zapytaniu dziennika Azure Monitor

`workspace`Wyrażenie jest używane w zapytaniu Azure monitor, aby pobrać dane z określonego obszaru roboczego w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji. Jest to przydatne do uwzględniania danych dziennika w kwerendzie Application Insights i wykonywania zapytań dotyczących danych w wielu obszarach roboczych w zapytaniu dziennika.


## <a name="syntax"></a>Składnia

`workspace(`*Identyfikatora*`)`

## <a name="arguments"></a>Argumenty

- *Identyfikator*: określa obszar roboczy przy użyciu jednego z formatów w poniższej tabeli.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Nazwa zasobu | Czytelna dla człowieka nazwa obszaru roboczego ("Nazwa składnika") | obszar roboczy ("ContosoRetail") |
| Kwalifikowana nazwa | Pełna nazwa obszaru roboczego w postaci: "subscriptionname/resourceName/ComponentName" | obszar roboczy ("contoso/ContosoResource/ContosoWorkspace") |
| ID (Identyfikator) | Identyfikator GUID obszaru roboczego | obszar roboczy ("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Identyfikator zasobu platformy Azure | Identyfikator zasobu platformy Azure | obszar roboczy ("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Uwagi

* Musisz mieć dostęp do odczytu do obszaru roboczego.
* Wyrażenie pokrewne `app` umożliwia wykonywanie zapytań w aplikacjach Application Insights.

## <a name="examples"></a>Przykłady

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Następne kroki

- Zobacz [wyrażenie aplikacji](./app-expression.md) , aby odwołać się do aplikacji Application Insights.
- Przeczytaj o sposobie przechowywania [danych Azure monitor](./log-query-overview.md) .
- Uzyskaj dostęp do pełnej dokumentacji dotyczącej [języka zapytań Kusto](/azure/kusto/query/).