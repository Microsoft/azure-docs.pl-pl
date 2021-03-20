---
title: Czasomierze w Durable Functions — Azure
description: Dowiedz się, jak zaimplementować trwałe czasomierze w rozszerzeniu Durable Functions dla Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: bb91f205a9b83b0b4b410644ef6c0fcbbf60876a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91876451"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Czasomierze w Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) zapewnia *trwałe czasomierze* do użycia w funkcjach programu Orchestrator do implementowania opóźnień lub skonfigurowania limitów czasu dla akcji asynchronicznych. Trwałe czasomierze powinny być używane w funkcjach programu Orchestrator zamiast `Thread.Sleep` i `Task.Delay` (C#), lub `setTimeout()` `setInterval()` (JavaScript) lub `time.sleep()` (Python).

Aby utworzyć trwały czasomierz, należy wywołać `CreateTimer` metodę (.NET) lub `createTimer` metodę (JavaScript) [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger). Metoda zwraca zadanie, które kończy się w określonym dniu i o określonej godzinie.

## <a name="timer-limitations"></a>Ograniczenia czasomierza

Podczas tworzenia czasomierza, który wygaśnie o 4:30 PM, podstawowa infrastruktura zadań trwałych enqueues komunikat, który zostanie widoczny tylko na 4:30 PM. Po uruchomieniu w planie zużycia Azure Functions, nowo widoczny komunikat czasomierza zapewni, że aplikacja funkcji zostanie aktywowana na odpowiedniej maszynie wirtualnej.

> [!NOTE]
> * Począwszy od [wersji 2.3.0](https://github.com/Azure/azure-functions-durable-extension/releases/tag/v2.3.0) trwałego rozszerzenia, trwałe czasomierze są nieograniczone. We wcześniejszych wersjach rozszerzenia trwałe czasomierze są ograniczone do siedmiu dni. Jeśli używasz starszej wersji i potrzebujesz opóźnienia dłuższego niż siedem dni, Użyj interfejsów API czasomierzy w pętli, `while` Aby symulować to opóźnienie.
> * Zawsze używaj `CurrentUtcDateTime` zamiast programu `DateTime.UtcNow` .NET lub `currentUtcDateTime` zamiast `Date.now` lub `Date.UTC` w języku JavaScript podczas obliczania czasu ognia dla trwałych czasomierzy. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [ograniczeń kodu funkcji programu Orchestrator](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Użycie opóźnienia

Poniższy przykład ilustruje sposób użycia trwałych czasomierzy do opóźnienia wykonywania. Przykład wysyła powiadomienie o rozliczeniach codziennie przez 10 dni.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Poprzedni przykład w języku C# jest celem Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> Unikaj nieskończonych pętli w funkcjach programu Orchestrator. Aby dowiedzieć się, jak bezpiecznie i efektywnie zaimplementować nieskończone scenariusze pętli, zobacz [Eternal aranżacji](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Użycie limitu czasu

Ten przykład ilustruje sposób używania trwałych czasomierzy do implementowania limitów czasu.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Poprzedni przykład w języku C# jest celem Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> Użyj `CancellationTokenSource` (.NET) lub wywołaj `cancel()` zwrócone `TimerTask` (JavaScript), aby anulować trwały czasomierz, jeśli kod nie będzie oczekiwał na jego zakończenie. W przypadku trwałej struktury zadań nie zostanie zmieniony stan aranżacji na "ukończone", dopóki wszystkie zaległe zadania nie zostaną ukończone lub anulowane.

Ten mechanizm anulowania nie przerywa wykonywania funkcji działania ani podaranżacji podrzędnych. Zamiast tego po prostu umożliwia funkcji programu Orchestrator ignorowanie wyniku i przejście. Jeśli aplikacja funkcji korzysta z planu zużycia, opłaty są naliczane za dowolny czas i użycie pamięci przez zaniechaną funkcję działania. Domyślnie funkcje działające w ramach planu zużycia mają limit czasu równy pięć minut. W przypadku przekroczenia tego limitu Host Azure Functions zostanie odtworzony w celu zatrzymania całego wykonywania i uniemożliwienia rozliczenia. [Limit czasu funkcji można skonfigurować](../functions-host-json.md#functiontimeout).

Aby zapoznać się z bardziej szczegółowym przykładem sposobu implementowania limitów czasu w funkcjach programu Orchestrator, zobacz temat [interakcja ludzka & limity czasu — artykuł weryfikacyjny telefonu](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak podnieść i obsłużyć zdarzenia zewnętrzne](durable-functions-external-events.md)
