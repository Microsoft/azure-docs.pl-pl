---
title: Azure Functions monitorowania z dziennikami Azure Monitor
description: Dowiedz się, jak używać dzienników Azure Monitor z Azure Functions do monitorowania wykonań funkcji.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77649878"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Azure Functions monitorowania z dziennikami Azure Monitor

Azure Functions oferuje integrację z [dziennikami Azure monitor](../azure-monitor/platform/data-platform-logs.md) do monitorowania funkcji. W tym artykule opisano sposób konfigurowania Azure Functions wysyłania dzienników generowanych przez system i generowanych przez użytkownika w celu Azure Monitor dzienników.

Dzienniki Azure Monitor umożliwiają konsolidowanie dzienników z różnych zasobów w tym samym obszarze roboczym, dzięki czemu można je analizować przy użyciu [zapytań](../azure-monitor/log-query/log-query-overview.md) , aby szybko pobierać, konsolidować i analizować zebrane dane.  Możesz tworzyć i testować zapytania przy użyciu [log Analytics](../azure-monitor/log-query/portals.md) w Azure Portal, a następnie bezpośrednio analizować dane przy użyciu tych narzędzi lub zapisywać zapytania do użycia z [wizualizacjami](../azure-monitor/visualizations.md) lub [regułami alertów](../azure-monitor/platform/alerts-overview.md).

Azure Monitor używa wersji [języka zapytań Kusto](/azure/kusto/query/) używanej przez platformę Azure Eksplorator danych, która jest odpowiednia dla prostych zapytań dzienników, ale również zawiera zaawansowane funkcje, takie jak agregacje, sprzężenia i Inteligentna analiza. Możesz szybko poznać język zapytań przy użyciu [wielu lekcji](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integracja z dziennikami Azure Monitor jest obecnie dostępna w publicznej wersji zapoznawczej dla aplikacji funkcji działających w ramach użycia, Premium i dedykowanych planów hostingu systemu Windows.

## <a name="setting-up"></a>Konfigurowanie

W sekcji **monitorowanie** wybierz pozycję **Ustawienia diagnostyczne** , a następnie kliknij pozycję **Dodaj ustawienie diagnostyczne**.

![Dodaj ustawienie diagnostyczne](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Na stronie **Ustawienia diagnostyki** wybierz pozycję **Wyślij do log Analytics**a następnie wybierz obszar roboczy log Analytics. W obszarze **Dziennik** wybierz **FunctionAppLogs**, ta tabela zawiera odpowiednie dzienniki.

![Dodaj ustawienie diagnostyczne](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Dzienniki generowane przez użytkownika

W celu wygenerowania dzienników niestandardowych można użyć określonej instrukcji rejestrowania w zależności od języka, poniżej przedstawiono przykładowe fragmenty kodu:


# <a name="c"></a>[S #](#tab/csharp)

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

# <a name="powershell"></a>[Narzędzia](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Wykonywanie zapytania dotyczącego dzienników

Aby wykonać zapytanie dotyczące wygenerowanych dzienników, przejdź do obszaru roboczego Log Analytics skonfigurowanego do wysyłania dzienników funkcji do i kliknij pozycję **dzienniki**.

![Okno zapytania w obszarze roboczym LA](media/functions-monitor-log-analytics/querying.png)

Azure Functions zapisuje wszystkie dzienniki w tabeli **FunctionAppLogs** , poniżej przedstawiono kilka przykładowych zapytań.

### <a name="all-logs"></a>Wszystkie dzienniki

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Określone dzienniki funkcji

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

- Zapoznaj się z [omówieniem Azure Functions](functions-overview.md)
- Dowiedz się więcej o [dziennikach Azure monitor](../azure-monitor/platform/data-platform-logs.md)
- Dowiedz się więcej o [języku zapytań](../azure-monitor/log-query/get-started-queries.md).
