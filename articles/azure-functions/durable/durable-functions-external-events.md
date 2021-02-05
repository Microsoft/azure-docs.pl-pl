---
title: Obsługa zdarzeń zewnętrznych w Durable Functions — Azure
description: Dowiedz się, jak obsługiwać zdarzenia zewnętrzne w rozszerzeniu Durable Functions Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: c08306edcea02a9207ab5a15eb62b7fffc2ecb44
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576333"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Obsługa zdarzeń zewnętrznych w Durable Functions (Azure Functions)

Funkcje programu Orchestrator mają możliwość oczekiwania i nasłuchiwania zdarzeń zewnętrznych. Ta funkcja [Durable Functions](durable-functions-overview.md) jest często przydatna do obsługi interakcji człowieka lub innych zewnętrznych wyzwalaczy.

> [!NOTE]
> Zdarzenia zewnętrzne to jednokierunkowe operacje asynchroniczne. Nie są odpowiednie do sytuacji, w których klient wysyłający zdarzenie potrzebuje synchronicznej odpowiedzi z funkcji programu Orchestrator.

## <a name="wait-for-events"></a>Zaczekaj na zdarzenia

Metody [WaitForExternalEvent](/dotnet/api/microsoft.azure.webjobs.durableorchestrationcontextbase.waitforexternalevent?view=azure-dotnet-legacy) (.NET), `waitForExternalEvent` (JavaScript) i `wait_for_external_event` (Python) [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger) pozwalają funkcji programu Orchestrator na asynchroniczne oczekiwanie i nasłuchiwanie zewnętrznego zdarzenia. Funkcja programu Orchestrator nasłuchiwanie deklaruje *nazwę* zdarzenia i *kształt danych* , które oczekuje na odebranie.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    approved = context.wait_for_external_event('Approval')
    if approved:
        # approval granted - do the approved action
    else:
        # approval denied - send a notification

main = df.Orchestrator.create(orchestrator_function)
```

---

Poprzedni przykład nasłuchuje określonego pojedynczego zdarzenia i podejmuje akcję po odebraniu.

Można nasłuchiwać wielu zdarzeń współbieżnie, takich jak w poniższym przykładzie, które czeka na jedno z trzech możliwych powiadomień o zdarzeniach.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    event1 = context.wait_for_external_event('Event1')
    event2 = context.wait_for_external_event('Event2')
    event3 = context.wait_for_external_event('Event3')

    winner = context.task_any([event1, event2, event3])
    if winner == event1:
        # ...
    elif winner == event2:
        # ...
    elif winner == event3:
        # ...

main = df.Orchestrator.create(orchestrator_function)
```

---

Poprzedni przykład nasłuchuje dla *dowolnego* z wielu zdarzeń. Istnieje również możliwość poczekania na *wszystkie* zdarzenia.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> Poprzedni kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

W programie .NET, jeśli nie można przekonwertować ładunku zdarzenia na oczekiwany typ `T` , zostanie zgłoszony wyjątek.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    application_id = context.get_input()
    
    gate1 = context.wait_for_external_event('CityPlanningApproval')
    gate2 = context.wait_for_external_event('FireDeptApproval')
    gate3 = context.wait_for_external_event('BuildingDeptApproval')

    yield context.task_all([gate1, gate2, gate3])
    yield context.call_activity('IssueBuildingPermit', application_id)

main = df.Orchestrator.create(orchestrator_function)
```

---

`WaitForExternalEvent` czeka na nieokreślony czas w przypadku niektórych danych wejściowych.  Aplikacja funkcji może zostać bezpiecznie zwolniona z ładowania podczas oczekiwania. Jeśli i po nadejściu zdarzenia dla tego wystąpienia aranżacji zostanie ono automatycznie wznowione i natychmiast przetworzy zdarzenie.

> [!NOTE]
> Jeśli aplikacja funkcji korzysta z planu zużycia, nie są naliczane opłaty za rozliczanie, gdy funkcja programu Orchestrator oczekuje na zadanie z `WaitForExternalEvent` (.NET), `waitForExternalEvent` (JavaScript) lub `wait_for_external_event` (Python), niezależnie od tego, jak długo czeka.

## <a name="send-events"></a>Wysyłanie zdarzeń

Możesz użyć metod [RaiseEventAsync](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclientbase.raiseeventasync?view=azure-dotnet-legacy) (.NET) lub `raiseEventAsync` (JavaScript) do wysyłania zdarzenia zewnętrznego do aranżacji. Te metody są udostępniane przez powiązanie [klienta aranżacji](durable-functions-bindings.md#orchestration-client) . Możesz również użyć wbudowanego [interfejsu API protokołu HTTP zdarzenia](durable-functions-http-api.md#raise-event) do wysyłania zdarzenia zewnętrznego do aranżacji.

Zdarzenie wywoływane zawiera *Identyfikator wystąpienia*, *EventName* i *eventData* jako parametry. Funkcje programu Orchestrator obsługują te zdarzenia przy użyciu `WaitForExternalEvent` interfejsów API (.NET) lub `waitForExternalEvent` (JavaScript). Nazwa *zdarzenia* musi być zgodna zarówno po stronie wysyłającej, jak i odbiorczej, aby można było przetworzyć zdarzenie. Dane zdarzenia muszą być również serializowane w formacie JSON.

Wewnętrznie, mechanizmy "zgłoś zdarzenie" w kolejce komunikatu, który jest pobierany przez oczekującą funkcję programu Orchestrator. Jeśli wystąpienie nie oczekuje na określoną *nazwę zdarzenia,* komunikat o zdarzeniu zostanie dodany do kolejki w pamięci. Jeśli wystąpienie aranżacji rozpocznie nasłuchiwanie dla tej *nazwy zdarzenia,* sprawdza kolejkę komunikatów o zdarzeniach.

> [!NOTE]
> Jeśli nie istnieje wystąpienie aranżacji o określonym *identyfikatorze wystąpienia*, komunikat o zdarzeniu zostanie odrzucony.

Poniżej znajduje się przykładowa funkcja wyzwalana przez kolejki, która wysyła zdarzenie "zatwierdzenie" do wystąpienia funkcji programu Orchestrator. Identyfikator wystąpienia aranżacji pochodzi z treści komunikatu w kolejce.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(instance_id:str, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    await client.raise_event(instance_id, 'Approval', True)
```

---

Wewnętrznie, `RaiseEventAsync` (.NET), `raiseEvent` (JavaScript) lub `raise_event` (Python) enqueues komunikat, który jest pobierany przez oczekiwaną funkcję programu Orchestrator. Jeśli wystąpienie nie oczekuje na określoną *nazwę zdarzenia,* komunikat o zdarzeniu zostanie dodany do kolejki w pamięci. Jeśli wystąpienie aranżacji rozpocznie nasłuchiwanie dla tej *nazwy zdarzenia,* sprawdza kolejkę komunikatów o zdarzeniach.

> [!NOTE]
> Jeśli nie istnieje wystąpienie aranżacji o określonym *identyfikatorze wystąpienia*, komunikat o zdarzeniu zostanie odrzucony.

### <a name="http"></a>HTTP

Poniżej znajduje się przykład żądania HTTP, które wywołuje zdarzenie "zatwierdzenie" do wystąpienia aranżacji. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

W takim przypadku identyfikator wystąpienia jest stałe jako obiekt *InstanceId*.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaimplementować obsługę błędów](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Uruchamianie przykładu, który czeka na interakcję człowieka](durable-functions-phone-verification.md)