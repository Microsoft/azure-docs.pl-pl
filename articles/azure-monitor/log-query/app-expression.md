---
title: wyrażenie App () w kwerendach dziennika Azure Monitor | Microsoft Docs
description: Wyrażenie aplikacji jest używane w zapytaniu dziennika Azure Monitor, aby pobrać dane z konkretnej aplikacji Application Insights w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 7cb8073d048118e704dafb0c70fe7cdb6774344a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324543"
---
# <a name="app-expression-in-azure-monitor-query"></a>wyrażenie App () w kwerendzie Azure Monitor

`app`Wyrażenie jest używane w zapytaniu Azure monitor, aby pobrać dane z określonej Application Insights aplikacji w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji. Jest to przydatne do dołączania danych aplikacji do zapytania dziennika Azure Monitor i do wykonywania zapytań dotyczących danych w wielu aplikacjach w kwerendzie Application Insightsowej.

> [!IMPORTANT]
> Wyrażenie App () nie jest używane, jeśli używasz [zasobu Application Insights opartego na obszarze roboczym](../app/create-workspace-resource.md) , ponieważ dane dziennika są przechowywane w log Analytics obszarze roboczym. Użyj wyrażenia log (), aby napisać zapytanie zawierające aplikację w wielu obszarach roboczych. W przypadku wielu aplikacji w tym samym obszarze roboczym nie jest wymagane zapytanie między obszarem roboczym.

## <a name="syntax"></a>Składnia

`app(`*Identyfikatora*`)`


## <a name="arguments"></a>Argumenty

- *Identyfikator*: identyfikuje aplikację przy użyciu jednego z formatów w poniższej tabeli.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Nazwa zasobu | Czytelna dla człowieka nazwa aplikacji (znana również jako "Nazwa składnika") | Aplikacja ("fabrikamapp") |
| Kwalifikowana nazwa | Pełna nazwa aplikacji w postaci: "subscriptionname/resourceName/ComponentName" | Aplikacja ("AI-Prototype/Fabrikam/fabrikamapp") |
| ID (Identyfikator) | Identyfikator GUID aplikacji | Aplikacja ("988ba129-363e-4415-8fe7-8cbab5447518") |
| Identyfikator zasobu platformy Azure | Identyfikator zasobu platformy Azure |Aplikacja ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Uwagi

* Musisz mieć dostęp do odczytu do aplikacji.
* Określenie aplikacji według nazwy zakłada, że jest ona unikatowa dla wszystkich dostępnych subskrypcji. Jeśli masz wiele aplikacji o określonej nazwie, zapytanie nie powiedzie się z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.
* Za pomocą pokrewnego [obszaru roboczego](workspace-expression.md) wyrażeń można wykonywać zapytania na log Analytics obszarach roboczych.
* Wyrażenie App () nie jest obecnie obsługiwane w zapytaniach wyszukiwania, gdy przy użyciu Azure Portal można utworzyć [niestandardową regułę alertu wyszukiwania w dzienniku](../platform/alerts-log.md), chyba że aplikacja Application Insights jest używana jako zasób dla reguły alertu.

## <a name="examples"></a>Przykłady

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Zobacz [wyrażenie obszaru roboczego](workspace-expression.md) , aby odwołać się do obszaru roboczego log Analytics.
- Przeczytaj o sposobie przechowywania [danych Azure monitor](./log-query-overview.md) .
- Uzyskaj dostęp do pełnej dokumentacji dotyczącej [języka zapytań Kusto](/azure/kusto/query/).

