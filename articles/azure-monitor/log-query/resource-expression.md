---
title: wyrażenie Resource () w kwerendzie dziennika Azure Monitor | Microsoft Docs
description: Wyrażenie zasobu jest używane w zasobie zorientowanym na zasoby Azure Monitor zapytania dziennika do pobierania danych z wielu zasobów.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77665703"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>wyrażenie Resource () w zapytaniu dziennika Azure Monitor

`resource`Wyrażenie jest używane w kwerendzie Azure monitor [należącej do zakresu](scope.md#query-scope) , aby można było pobrać dane z innych zasobów. 


## <a name="syntax"></a>Składnia

`resource(`*Identyfikatora*`)`

## <a name="arguments"></a>Argumenty

- *Identyfikator*: Identyfikator zasobu zasobu.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Zasób | Zawiera dane dla zasobu. | zasób ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.COMPUTE/virtualmachines/MyVM") |
| Grupa zasobów lub subskrypcja | Zawiera dane dotyczące zasobu i wszystkich zasobów, które zawiera.  | zasób ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup") |


## <a name="notes"></a>Uwagi

* Musisz mieć dostęp do odczytu do zasobu.


## <a name="examples"></a>Przykłady

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat zakresu zapytania [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](scope.md) .
- Uzyskaj dostęp do pełnej dokumentacji dotyczącej [języka zapytań Kusto](/azure/kusto/query/).
