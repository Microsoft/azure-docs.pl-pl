---
title: Obsługa błędów w Durable Functions — Azure
description: Dowiedz się, jak obsłużyć błędy w rozszerzeniu Durable Functions Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673651"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Obsługa błędów w Durable Functions (Azure Functions)

Trwałe aranżacje funkcji są implementowane w kodzie i mogą korzystać z wbudowanych funkcji obsługi błędów w języku programowania. Nie ma żadnych nowych koncepcji, które należy poznać, aby dowiedzieć się, jak dodać obsługę błędów i kompensację do swoich aranżacji. Istnieje jednak kilka zachowań, z którymi należy się zapoznać.

## <a name="errors-in-activity-functions"></a>Błędy w funkcjach działania

Każdy wyjątek, który jest generowany w funkcji działania, zostaje zorganizowany z powrotem do funkcji programu Orchestrator i zgłaszany jako `FunctionFailedException` . Można napisać obsługę błędów i kod kompensaty, który odpowiada Twoim potrzebom w funkcji programu Orchestrator.

Rozważmy na przykład następujące funkcje programu Orchestrator, które przesyłają fundusze z jednego konta do innego:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> Poprzednie przykłady w języku C# są przeznaczone dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

Jeśli pierwsze wywołanie funkcji **CreditAccount** zakończy się niepowodzeniem, funkcja programu Orchestrator będzie kompensować środki z powrotem do konta źródłowego.

## <a name="automatic-retry-on-failure"></a>Automatyczne ponawianie próby w przypadku niepowodzenia

Podczas wywoływania funkcji działania lub funkcji organizowania podrzędnego można określić zasady automatycznego ponawiania. Poniższy przykład próbuje wywołać funkcję maksymalnie trzy razy i czeka 5 sekund między kolejnymi próbami:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Poprzednie przykłady w języku C# są przeznaczone dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

Wywołanie funkcji działania w poprzednim przykładzie przyjmuje parametr służący do konfigurowania zasad automatycznego ponawiania. Istnieje kilka opcji dostosowywania zasad automatycznego ponawiania:

* **Maksymalna liczba prób**: Maksymalna liczba ponownych prób.
* **Interwał pierwszego ponowienia próby**: ilość czasu oczekiwania przed pierwszym ponowieniem próby.
* **Współczynnik wycofywania**: współczynnik używany do określenia współczynnika wzrostu wycofywania. Wartość domyślna to 1.
* **Maksymalna długość interwału ponawiania**: Maksymalna ilość czasu oczekiwania między ponownymi próbami.
* **Limit czasu ponawiania**: maksymalny czas poświęcany na wykonanie ponownych prób. Domyślne zachowanie polega na ponownym ponowieniu próby.
* **Dojście**: zdefiniowane przez użytkownika wywołanie zwrotne można określić, aby określić, czy należy ponowić próbę wykonania funkcji. 

> [!NOTE]
> Wywołania zwrotne zdefiniowane przez użytkownika nie są obecnie obsługiwane przez Durable Functions w języku JavaScript ( `context.df.RetryOptions` ).


## <a name="function-timeouts"></a>Limity czasu funkcji

Może zajść potrzeba porzucenia wywołania funkcji w ramach funkcji programu Orchestrator, jeśli trwa zbyt długo. Prawidłowym sposobem wykonania tej czynności jest utworzenie [trwałego czasomierza](durable-functions-timers.md) przy użyciu `context.CreateTimer` (.NET), `context.df.createTimer` (JavaScript) lub `context.create_timer` (Python) w połączeniu z `Task.WhenAny` (.NET), `context.df.Task.any` (JavaScript) lub `context.task_any` (Python), jak w poniższym przykładzie:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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
> Poprzednie przykłady w języku C# są przeznaczone dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
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
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Ten mechanizm nie kończy w rzeczywistości wykonywania funkcji działania w toku. Zamiast tego po prostu umożliwia funkcji programu Orchestrator ignorowanie wyniku i przejście. Aby uzyskać więcej informacji, zobacz dokumentację [czasomierzy](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Nieobsługiwane wyjątki

Jeśli funkcja programu Orchestrator zakończy się niepowodzeniem z powodu nieobsługiwanego wyjątku, szczegóły wyjątku są rejestrowane, a wystąpienie zostanie zakończone ze `Failed` stanem.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o aranżacjach Eternal](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak zdiagnozować problemy](durable-functions-diagnostics.md)
