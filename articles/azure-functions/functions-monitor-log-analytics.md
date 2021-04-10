---
title: Azure Functions monitorowania z dziennikami Azure Monitor
description: Dowiedz się, jak używać dzienników Azure Monitor z Azure Functions do monitorowania wykonań funkcji.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b6ce9e77421df0563810fd7f8255720c1fd2d0c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591078"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions monitorowania z dziennikami Azure Monitor

Azure Functions oferuje integrację z [dziennikami Azure monitor](../azure-monitor/logs/data-platform-logs.md) do monitorowania funkcji. W tym artykule opisano sposób konfigurowania Azure Functions wysyłania dzienników generowanych przez system i generowanych przez użytkownika w celu Azure Monitor dzienników.

Dzienniki Azure Monitor umożliwiają konsolidowanie dzienników z różnych zasobów w tym samym obszarze roboczym, dzięki czemu można je analizować przy użyciu [zapytań](../azure-monitor/logs/log-query-overview.md) , aby szybko pobierać, konsolidować i analizować zebrane dane.  Możesz tworzyć i testować zapytania przy użyciu [log Analytics](../azure-monitor/logs/log-query-overview.md) w Azure Portal, a następnie bezpośrednio analizować dane przy użyciu tych narzędzi lub zapisywać zapytania do użycia z [wizualizacjami](../azure-monitor/visualizations.md) lub [regułami alertów](../azure-monitor/alerts/alerts-overview.md).

Azure Monitor używa wersji [języka zapytań Kusto](/azure/kusto/query/) używanej przez platformę Azure Eksplorator danych, która jest odpowiednia dla prostych zapytań dzienników, ale również zawiera zaawansowane funkcje, takie jak agregacje, sprzężenia i Inteligentna analiza. Możesz szybko poznać język zapytań przy użyciu [wielu lekcji](../azure-monitor/logs/get-started-queries.md).

> [!NOTE]
> Integracja z dziennikami Azure Monitor jest obecnie dostępna w publicznej wersji zapoznawczej dla aplikacji funkcji w wersji 2 i V3 działających w ramach użycia systemu Windows, Premium i dedykowanych planów hostingu.

## <a name="setting-up"></a>Konfigurowanie

1. Z sekcji **monitorowanie** aplikacji funkcji w [Azure Portal](https://portal.azure.com)wybierz pozycję **Ustawienia diagnostyczne**, a następnie wybierz pozycję **Dodaj ustawienie diagnostyczne**.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Wybieranie ustawień diagnostycznych":::

1. Na stronie **Ustawienia diagnostyki** w obszarze **szczegóły kategorii** i **Dziennik** wybierz pozycję **FunctionAppLogs**.

   Tabela **FunctionAppLogs** zawiera żądane dzienniki.

1. W obszarze **szczegóły miejsca docelowego** wybierz pozycję **Wyślij do log Analytics**. a następnie wybierz **obszar roboczy log Analytics**. 

1. Wprowadź **nazwę ustawień diagnostycznych**, a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Dodaj ustawienie diagnostyczne":::

## <a name="user-generated-logs"></a>Dzienniki generowane przez użytkownika

Aby generować dzienniki niestandardowe, użyj instrukcji rejestrowania właściwej dla danego języka. Oto przykładowe fragmenty kodu:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Wykonywanie zapytania dotyczącego dzienników

Aby wykonać zapytanie dotyczące wygenerowanych dzienników:
 
1. W aplikacji funkcji wybierz pozycję **Ustawienia diagnostyczne**. 

1. Z listy **ustawień diagnostycznych** wybierz obszar roboczy log Analytics, który został skonfigurowany do wysyłania dzienników funkcji. 

1. Na stronie **obszar roboczy log Analytics** wybierz pozycję **dzienniki**.

   Azure Functions zapisuje wszystkie dzienniki w tabeli **FunctionAppLogs** w obszarze **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Okno zapytania w obszarze roboczym Log Analytics":::

Oto kilka przykładowych zapytań:

### <a name="all-logs"></a>Wszystkie dzienniki

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Określone dzienniki funkcji

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Wyjątki

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [omówieniem Azure Functions](functions-overview.md).
- Dowiedz się więcej o [dziennikach Azure monitor](../azure-monitor/logs/data-platform-logs.md).
- Dowiedz się więcej o [języku zapytań](../azure-monitor/logs/get-started-queries.md).
