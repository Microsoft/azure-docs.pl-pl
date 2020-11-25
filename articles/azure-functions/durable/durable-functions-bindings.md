---
title: Powiązania dla Durable Functions — Azure
description: Jak używać wyzwalaczy i powiązań dla rozszerzenia Durable Functions Azure Functions.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 899bc3fdc94b8232acd3edf3e0cbab3c481ff8f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013733"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Powiązania dla Durable Functions (Azure Functions)

Rozszerzenie [Durable Functions](durable-functions-overview.md) wprowadza dwa nowe powiązania wyzwalacza kontrolujące wykonywanie funkcji Orchestrator i Functions. Wprowadza również powiązanie danych wyjściowych, które działa jako klient dla środowiska uruchomieniowego Durable Functions.

## <a name="orchestration-trigger"></a>Wyzwalacz aranżacji

Wyzwalacz aranżacji umożliwia tworzenie [trwałych funkcji programu Orchestrator](durable-functions-types-features-overview.md#orchestrator-functions). Ten wyzwalacz obsługuje uruchamianie nowych wystąpień funkcji programu Orchestrator i wznawianie istniejących wystąpień funkcji programu Orchestrator, które oczekują na zadanie.

W przypadku korzystania z narzędzi Visual Studio Tools for Azure Functions wyzwalacz aranżacji jest konfigurowany przy użyciu atrybutu platformy .NET [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute?view=azure-dotnet) .

W przypadku pisania funkcji programu Orchestrator w językach skryptów (na przykład skryptów JavaScript lub C#) wyzwalacz aranżacji jest definiowany przez następujący obiekt JSON w `bindings` tablicy *function.jsw* pliku:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` jest nazwą aranżacji, której klienci muszą używać, gdy chcą uruchamiać nowe wystąpienia tej funkcji programu Orchestrator. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, zostanie użyta nazwa funkcji.

Wewnętrznie to powiązanie wyzwalacza sonduje serię kolejek w domyślnym koncie magazynu dla aplikacji funkcji. Te kolejki są wewnętrznymi szczegółami implementacji rozszerzenia, dlatego nie są one jawnie skonfigurowane we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Poniżej znajdują się pewne uwagi dotyczące wyzwalacza aranżacji:

* **Jednowątkowy** — pojedynczy wątek dyspozytora jest używany dla wszystkich wykonań funkcji programu Orchestrator na jednym wystąpieniu hosta. Z tego powodu ważne jest, aby upewnić się, że kod funkcji programu Orchestrator jest wydajny i nie wykonuje żadnych operacji we/wy. Ważne jest również, aby upewnić się, że ten wątek nie wykonuje żadnych działań asynchronicznych, z wyjątkiem przypadków, gdy oczekuje na Durable Functions typów zadań.
* **Obsługa komunikatów trujących** — nie istnieje Trująca obsługa komunikatów w wyzwalaczach aranżacji.
* **Widoczność komunikatów** — komunikaty wyzwalacza aranżacji są dekolejkowane i niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie, o ile aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Zwracane wartości** — zwracane wartości są serializowane do formatu JSON i utrwalane w tabeli historii aranżacji w usłudze Azure Table Storage. Te zwracane wartości mogą być badane przez powiązanie klienta aranżacji opisane w dalszej części.

> [!WARNING]
> Funkcje programu Orchestrator nigdy nie powinny używać żadnych powiązań wejściowych ani wyjściowych innych niż powiązanie wyzwalacza aranżacji. Takie działanie może spowodować problemy z rozszerzeniem zadania trwałego, ponieważ te powiązania mogą nie przestrzegać reguł jednowątkowych i operacji we/wy. Jeśli chcesz użyć innych powiązań, Dodaj je do funkcji działania wywołanej z funkcji programu Orchestrator.

> [!WARNING]
> Funkcje programu Orchestrator w języku JavaScript nigdy nie powinny być deklarowane `async` .

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Powiązanie wyzwalacza aranżacji obsługuje dane wejściowe i wyjściowe. Oto kilka rzeczy, które należy wiedzieć na temat obsługi danych wejściowych i wyjściowych:

* **wejścia** — funkcje aranżacji platformy .NET obsługują tylko `DurableOrchestrationContext` jako typ parametru. Deserializacja danych wejściowych bezpośrednio w sygnaturze funkcji nie jest obsługiwana. `GetInput<T>` `getInput` Aby można było pobrać dane wejściowe funkcji programu Orchestrator, kod musi używać metody (.NET) lub (JavaScript). Te dane wejściowe muszą być typami możliwymi do serializacji JSON.
* Generowanie **danych wyjściowych —** wyzwalacze aranżacji obsługują wartości wyjściowe oraz dane wejściowe. Wartość zwracana funkcji służy do przypisywania wartości wyjściowej i musi być możliwa do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void` , `null` wartość zostanie zapisana jako dane wyjściowe.

### <a name="trigger-sample"></a>Przykład wyzwalacza

Poniższy przykładowy kod pokazuje, jak najprostsza funkcja programu Orchestrator "Hello world" może wyglądać następująco:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Poprzedni kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> `context`Obiekt w języku JavaScript nie reprezentuje DurableOrchestrationContext, ale [kontekst funkcji jako całości](../functions-reference-node.md#context-object). Dostęp do metod aranżacji można uzyskać za pomocą `context` `df` właściwości obiektu.

> [!NOTE]
> Koordynator JavaScript powinien używać `return` . `durable-functions`Biblioteka zajmuje się wywołaniem `context.done` metody.

Większość funkcji działania wywoływanych przez program Orchestrator ma w tym miejscu następujący przykład "Hello world", który pokazuje, jak wywołać funkcję działania:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Poprzedni kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

#### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Wyzwalacz działania

Wyzwalacz działania umożliwia tworzenie funkcji, które są wywoływane przez funkcje programu Orchestrator, znane jako [funkcje działania](durable-functions-types-features-overview.md#activity-functions).

Jeśli używasz programu Visual Studio, wyzwalacz aktywności jest konfigurowany przy użyciu `ActivityTriggerAttribute` atrybutu .NET.

Jeśli używasz VS Code lub Azure Portal do programowania, wyzwalacz aktywności jest definiowany przez następujący obiekt JSON w `bindings` tablicy *function.jsw*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` to nazwa działania. Ta wartość jest nazwą, której funkcje programu Orchestrator używają do wywołania tej funkcji działania. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, zostanie użyta nazwa funkcji.

Wewnętrznie to powiązanie wyzwalacza sonduje kolejkę w domyślnym koncie magazynu dla aplikacji funkcji. Ta kolejka jest wewnętrznymi szczegółami implementacji rozszerzenia, dlatego nie jest on jawnie skonfigurowany we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Poniżej przedstawiono niektóre uwagi dotyczące wyzwalacza działania:

* **Wątkowość** — w przeciwieństwie do wyzwalacza aranżacji, wyzwalacze aktywności nie mają żadnych ograniczeń dotyczących wątków lub operacji we/wy. Mogą być traktowane jak regularne funkcje.
* **Obsługa komunikatów trujących** — w wyzwalaczach aktywności nie ma obsługi skażonych komunikatów.
* **Widoczność komunikatów** — komunikaty wyzwalacza aktywności są dekolejkowane i niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie, o ile aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Zwracane wartości** — zwracane wartości są serializowane do formatu JSON i utrwalane w tabeli historii aranżacji w usłudze Azure Table Storage.

> [!WARNING]
> Zaplecze magazynu dla funkcji działania to szczegóły implementacji, a kod użytkownika nie powinien bezpośrednio korzystać z tych jednostek magazynu.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Powiązanie wyzwalacza działania obsługuje dane wejściowe i wyjściowe, podobnie jak wyzwalacz aranżacji. Oto kilka rzeczy, które należy wiedzieć na temat obsługi danych wejściowych i wyjściowych:

* **wejścia** — funkcje działania .NET natywnie wykorzystują `DurableActivityContext` jako typ parametru. Alternatywnie można zadeklarować funkcję działania z dowolnym typem parametru, który jest możliwy do serializacji notacji JSON. Gdy używasz `DurableActivityContext` , możesz wywołać, `GetInput<T>` Aby pobrać i zdeserializować dane wejściowe funkcji działania.
* funkcje **danych wyjściowych** obsługują wartości wyjściowe oraz dane wejściowe. Wartość zwracana funkcji służy do przypisywania wartości wyjściowej i musi być możliwa do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void` , `null` wartość zostanie zapisana jako dane wyjściowe.
* **metadane** — funkcje działania .NET można powiązać z `string instanceId` parametrem w celu uzyskania identyfikatora wystąpienia aranżacji nadrzędnej.

### <a name="trigger-sample"></a>Przykład wyzwalacza

Poniższy przykładowy kod pokazuje, jak prosta funkcja działania "Hello world" może wyglądać następująco:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Poprzedni kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableActivityContext` zamiast `IDurableActivityContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

Domyślny typ parametru dla `ActivityTriggerAttribute` powiązania .NET to `IDurableActivityContext` . Jednak wyzwalacze aktywności .NET obsługują również powiązanie bezpośrednio z typami serializowanych JSON (w tym typami pierwotnymi), dzięki czemu ta sama funkcja może być uproszczona w następujący sposób:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2,0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Powiązania języka JavaScript mogą być również przesyłane jako dodatkowe parametry, dlatego ta sama funkcja może być uproszczona w następujący sposób:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Używanie powiązań wejściowych i wyjściowych

Oprócz powiązania wyzwalacza aktywności można użyć zwykłych powiązań danych wejściowych i wyjściowych. Na przykład możesz przełączyć dane wejściowe do powiązania działania i wysłać komunikat do centrum EventHub przy użyciu powiązania danych wyjściowych EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Klient aranżacji

Powiązanie klienta aranżacji pozwala pisać funkcje, które współdziałają z funkcjami programu Orchestrator. Te funkcje są czasami określane jako [funkcje klienta](durable-functions-types-features-overview.md#client-functions). Na przykład można działać na wystąpieniach aranżacji w następujący sposób:

* Je uruchomić.
* Zbadaj ich stan.
* Zakończyć te działania.
* Wysyłaj do nich zdarzenia, gdy są one uruchomione.
* Przeczyść historię wystąpień.

Jeśli używasz programu Visual Studio, możesz powiązać się z klientem aranżacji przy użyciu `OrchestrationClientAttribute` atrybutu .NET dla Durable Functions 1,0. Począwszy od Durable Functions 2,0, można powiązać z klientem aranżacji przy użyciu `DurableClientAttribute` atrybutu platformy .NET.

Jeśli używasz języków skryptów (na przykład plików *. CSX* lub *. js* ) do programowania, wyzwalacz aranżacji jest DEFINIOWANY przez następujący obiekt JSON w `bindings` tablicy *function.jsw*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` — Używane w scenariuszach, w których wiele aplikacji funkcji współużytkuje to samo konto magazynu, ale musi być od siebie odizolowane. Jeśli nie zostanie określony, `host.json` zostanie użyta wartość domyślna od. Ta wartość musi być zgodna z wartością używaną przez docelowe funkcje programu Orchestrator.
* `connectionName` — Nazwa ustawienia aplikacji, które zawiera parametry połączenia konta magazynu. Konto magazynu reprezentowane przez te parametry połączenia musi być takie samo, jak używane przez docelowe funkcje programu Orchestrator. Jeśli nie zostanie określony, używane są domyślne parametry połączenia konta magazynu dla aplikacji funkcji.

> [!NOTE]
> W większości przypadków zaleca się pominięcie tych właściwości i zagwarantowanie zachowania domyślnego.

### <a name="client-usage"></a>Użycie klienta

W programie .NET Functions zwykle wiąże się z `IDurableOrchestrationClient` , który zapewnia pełny dostęp do wszystkich interfejsów API klienta aranżacji obsługiwanych przez Durable Functions. W starszych wersjach Durable Functions 2. x, zamiast tego należy powiązać z `DurableOrchestrationClient` klasą. W języku JavaScript te same interfejsy API są uwidaczniane przez obiekt zwrócony z `getClient` . Interfejsy API w obiekcie klienta obejmują:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatywnie funkcje programu .NET mogą tworzyć powiązania z `IAsyncCollector<T>` miejscem, gdzie `T` jest `StartOrchestrationArgs` lub `JObject` .

Więcej informacji na temat tych operacji można znaleźć w `IDurableOrchestrationClient` dokumentacji interfejsu API.

### <a name="client-sample-visual-studio-development"></a>Przykład klienta (Programowanie Visual Studio)

Oto przykład funkcji wyzwalanej przez kolejkę, która uruchamia organizację "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

### <a name="client-sample-not-visual-studio"></a>Przykład klienta (nie Visual Studio)

Jeśli nie używasz programu Visual Studio do tworzenia aplikacji, możesz utworzyć następujące *function.jsw* pliku. Ten przykład przedstawia sposób konfigurowania funkcji wyzwalanej przez kolejkę, która używa powiązania klienta trwałej aranżacji:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Poprzedni kod JSON jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `orchestrationClient` zamiast `durableClient` typu wyzwalacza. Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

Poniżej przedstawiono przykłady charakterystyczne dla języka, które uruchamiają nowe wystąpienia funkcji programu Orchestrator.

#### <a name="c-script-sample"></a>Przykładowy skrypt w języku C#

Poniższy przykład pokazuje, jak używać trwałego powiązania klienta aranżacji, aby uruchomić nowe wystąpienie funkcji z funkcji języka C# wyzwalanej przez kolejkę:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Poprzedni kod jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

#### <a name="javascript-sample"></a>Przykładowy kod JavaScript

Poniższy przykład pokazuje, jak używać trwałego powiązania klienta aranżacji do uruchomienia nowego wystąpienia funkcji z funkcji JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Więcej informacji o wystąpieniach początkowych można znaleźć w temacie [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Wyzwalacz jednostki

Wyzwalacze jednostek umożliwiają tworzenie [funkcji jednostek](durable-functions-entities.md). Ten wyzwalacz obsługuje przetwarzanie zdarzeń dla określonego wystąpienia jednostki.

W przypadku korzystania z narzędzi Visual Studio Tools for Azure Functions wyzwalacz jednostki jest konfigurowany przy użyciu `EntityTriggerAttribute` atrybutu .NET.

> [!NOTE]
> Wyzwalacze jednostki są dostępne, zaczynając od Durable Functions 2. x.

Wewnętrznie to powiązanie wyzwalacza sonduje serię kolejek w domyślnym koncie magazynu dla aplikacji funkcji. Te kolejki są wewnętrznymi szczegółami implementacji rozszerzenia, dlatego nie są one jawnie skonfigurowane we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Poniżej przedstawiono niektóre uwagi dotyczące wyzwalacza jednostki:

* **Jednowątkowo**: pojedynczy wątek dyspozytora jest używany do przetwarzania operacji dla określonej jednostki. Jeśli wiele komunikatów jest wysyłanych jednocześnie do pojedynczej jednostki, operacje zostaną przetworzone jeden w czasie.
* **Obsługa komunikatów trujących** — nie istnieje Trująca obsługa komunikatów w wyzwalaczach jednostek.
* **Widoczność komunikatów** — komunikaty wyzwalacza jednostki są dekolejkowane i niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie, o ile aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Wartości zwracane** — funkcje jednostek nie obsługują zwracanych wartości. Istnieją określone interfejsy API, których można użyć w celu zapisania stanu lub przekazania wartości do aranżacji.

Wszystkie zmiany stanu wprowadzone w jednostce podczas jej wykonywania zostaną automatycznie utrwalone po zakończeniu wykonywania.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Każda funkcja jednostki ma typ parametru `IDurableEntityContext` , który ma następujące elementy członkowskie:

* **EntityName**: nazwa aktualnie wykonywanej jednostki.
* **EntityKey**: klucz aktualnie wykonywanej jednostki.
* **EntityId**: Identyfikator aktualnie wykonywanej jednostki.
* **OperationName**: nazwa bieżącej operacji.
* **HasState**: wskazuje, czy jednostka istnieje, czy ma jakiś stan. 
* **GetState \<TState> ()**: Pobiera bieżący stan jednostki. Jeśli jeszcze nie istnieje, zostanie utworzony i zainicjowany do `default<TState>` . `TState`Parametr musi być typem pierwotnym lub obiektem serializacji JSON. 
* **GetState \<TState> (initfunction)**: Pobiera bieżący stan jednostki. Jeśli jeszcze nie istnieje, jest tworzony przez wywołanie dostarczonego `initfunction` parametru. `TState`Parametr musi być typem pierwotnym lub obiektem serializacji JSON. 
* **Setstate (arg)**: tworzy lub aktualizuje stan jednostki. `arg`Parametr musi być obiektem, który można serializować lub elementem pierwotnym JSON.
* **DeleteState ()**: usuwa stan jednostki. 
* **Getinput \<TInput> ()**: Pobiera dane wejściowe dla bieżącej operacji. `TInput`Parametr type musi być typem pierwotnym lub, który można serializować.
* **Return (arg)**: zwraca wartość aranżacji, która wywołała operację. `arg`Parametr musi być obiektem pierwotnym lub serializowanym w formacie JSON.
* **SignalEntity (EntityId, scheduledTimeUtc, Operation, input)**: wysyła komunikat jednokierunkowy do jednostki. `operation`Parametr musi być ciągiem o wartości innej niż null, opcjonalnie `scheduledTimeUtc` musi być czasem UTC, przy którym ma zostać wywołana operacja, a `input` parametr musi być obiektem pierwotnym lub SERIALIZOWANYM przez kod JSON.
* **CreateNewOrchestration (orchestratorFunctionName, input)**: uruchamia nową aranżację. `input`Parametr musi być obiektem pierwotnym lub serializowanym w formacie JSON.

Do `IDurableEntityContext` obiektu przesłanego do funkcji Entity można uzyskać dostęp za pomocą `Entity.Current` Właściwości Async-Local. Takie podejście jest wygodne w przypadku korzystania z modelu programowania opartego na klasie.

### <a name="trigger-sample-c-function-based-syntax"></a>Przykład wyzwalacza (składnia oparta na funkcji C#)

Poniższy kod jest przykładem prostej jednostki *licznika* wdrożonej jako funkcja trwała. Ta funkcja definiuje trzy operacje, `add` , `reset` , i `get` , z których każda działa na stanie liczby całkowitej.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Aby uzyskać więcej informacji na temat składni opartej na funkcjach i korzystania z niej, zobacz [składnia oparta na funkcjach](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Przykład wyzwalacza (składnia oparta na klasie C#)

Poniższy przykład jest równoważną implementacją `Counter` jednostki przy użyciu klas i metod.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Stan tej jednostki jest obiektem typu `Counter` , który zawiera pole przechowujące bieżącą wartość licznika. Aby zachować ten obiekt w magazynie, jest on serializowany i deserializowany przez bibliotekę [JSON.NET](https://www.newtonsoft.com/json) . 

Aby uzyskać więcej informacji na temat składni opartej na klasie i korzystania z niej, zobacz [Definiowanie klas jednostek](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Metoda punktu wejścia funkcji z `[FunctionName]` atrybutem *musi* być zadeklarowana w `static` przypadku używania klas jednostek. Niestatyczne metody punktu wejścia mogą spowodować inicjalizację wielu obiektów oraz inne niezdefiniowane zachowania.

Klasy jednostek mają specjalne mechanizmy współpracy z powiązaniami i iniekcją zależności platformy .NET. Aby uzyskać więcej informacji, zobacz [konstruowanie jednostki](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Przykład wyzwalacza (JavaScript)

Poniższy kod jest przykładem prostej jednostki *licznika* zaimplementowane jako funkcja trwała zapisywana w języku JavaScript. Ta funkcja definiuje trzy operacje, `add` , `reset` , i `get` , z których każda działa na stanie liczby całkowitej.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Trwałe jednostki są dostępne w języku JavaScript, począwszy **1.3.0** od wersji 1.3.0 `durable-functions` pakietu npm.

## <a name="entity-client"></a>Klient jednostki

Powiązanie klienta jednostki umożliwia asynchroniczne wyzwalanie [funkcji jednostek](#entity-trigger). Te funkcje są czasami określane jako [funkcje klienta](durable-functions-types-features-overview.md#client-functions).

Jeśli używasz programu Visual Studio, możesz powiązać z klientem jednostki przy użyciu `DurableClientAttribute` atrybutu .NET.

> [!NOTE]
> `[DurableClientAttribute]`Może również służyć do powiązania z [klientem aranżacji](#orchestration-client).

Jeśli używasz języków skryptów (na przykład plików *. CSX* lub *. js* ) do programowania, wyzwalacz jednostki jest zdefiniowany przez następujący obiekt JSON w `bindings` tablicy *function.jsw*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` — Używane w scenariuszach, w których wiele aplikacji funkcji współużytkuje to samo konto magazynu, ale musi być od siebie odizolowane. Jeśli nie zostanie określony, `host.json` zostanie użyta wartość domyślna od. Ta wartość musi być zgodna z wartością używaną przez funkcje jednostki docelowej.
* `connectionName` — Nazwa ustawienia aplikacji, które zawiera parametry połączenia konta magazynu. Konto magazynu reprezentowane przez te parametry połączenia musi być takie samo, jak używane przez funkcje jednostki docelowej. Jeśli nie zostanie określony, używane są domyślne parametry połączenia konta magazynu dla aplikacji funkcji.

> [!NOTE]
> W większości przypadków zaleca się pominięcie opcjonalnych właściwości i zagwarantowanie zachowania domyślnego.

### <a name="entity-client-usage"></a>Użycie klienta jednostki

W programie .NET Functions zwykle wiąże się z `IDurableEntityClient` , który zapewnia pełny dostęp do wszystkich interfejsów API klienta obsługiwanych przez trwałe jednostki. Można również powiązać z `IDurableOrchestrationClient` interfejsem, który zapewnia dostęp do interfejsów API klienta zarówno dla jednostek, jak i aranżacji. Interfejsy API w obiekcie klienta obejmują:

* **ReadEntityStateAsync \<T>**: odczytuje stan jednostki. Zwraca odpowiedź wskazującą, czy jednostka docelowa istnieje, a jeśli tak, to jaki jest jej stan.
* **SignalEntityAsync**: wysyła komunikat jednokierunkowy do jednostki i czeka na jego przekolejkowanie.
* **ListEntitiesAsync**: wykonuje zapytania dotyczące stanu wielu jednostek. Jednostki mogą być zapytania według *nazwy* i *czasu ostatniej operacji*.

Nie ma potrzeby tworzenia jednostki docelowej przed wysłaniem sygnału — stan jednostki można utworzyć z poziomu funkcji jednostki, która obsługuje sygnał.

> [!NOTE]
> Ważne jest, aby zrozumieć, że "sygnały" wysyłane z klienta są po prostu w kolejce, aby były przetwarzane asynchronicznie w późniejszym czasie. W szczególności, `SignalEntityAsync` zwykle zwraca przed jednostką, nawet rozpoczyna operację, i nie jest możliwe przywrócenie wartości zwracanej lub obserwowanie wyjątków. Jeśli wymagane są silniejsze gwarancje (np. dla przepływów pracy), powinny być używane *funkcje programu Orchestrator* , które mogą poczekać na zakończenie operacji jednostki i mogą przetwarzać wartości zwracane i przestrzegać wyjątków.

### <a name="example-client-signals-entity-directly---c"></a>Przykład: klient sygnalizuje jednostkę bezpośrednio-C #

Oto przykład funkcji wyzwalanej przez kolejkę, która wywołuje jednostkę "Counter".

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Przykład: klient sygnalizuje jednostkę za pośrednictwem interfejsu-C #

Tam, gdzie to możliwe, zalecamy [dostęp do jednostek za poorednictwem interfejsów](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , ponieważ zapewnia ona większą kontrolę typów. Załóżmy na przykład, że `Counter` Jednostka wymieniona wcześniej implementuje `ICounter` interfejs, zdefiniowane w następujący sposób:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Kod klienta może następnie użyć `SignalEntityAsync<ICounter>` do wygenerowania bezpiecznego typu serwera proxy:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

`proxy`Parametr jest dynamicznie generowanym wystąpieniem `ICounter` , które wewnętrznie tłumaczy wywołanie na `Add` równoważne (bez typu) wywołania do `SignalEntityAsync` .

> [!NOTE]
> `SignalEntityAsync`Interfejsy API reprezentują operacje jednokierunkowe. Jeśli interfejsy jednostki zwracają `Task<T>` , wartość `T` parametru zawsze będzie równa null lub `default` .

W szczególności nie ma sensu sygnalizowanie `Get` operacji, ponieważ nie jest zwracana żadna wartość. Zamiast tego klienci mogą `ReadStateAsync` uzyskać dostęp do stanu licznika bezpośrednio lub uruchomić funkcję programu Orchestrator, która wywołuje `Get` operację.

### <a name="example-client-signals-entity---javascript"></a>Przykład: klient sygnalizuje jednostkę — JavaScript

Oto przykład funkcji wyzwalanej przez kolejkę, która sygnalizuje jednostkę "Counter" w języku JavaScript.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Trwałe jednostki są dostępne w języku JavaScript, począwszy **1.3.0** od wersji 1.3.0 `durable-functions` pakietu npm.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.jsustawień

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wbudowana dokumentacja interfejsu API protokołu HTTP do zarządzania wystąpieniami](durable-functions-http-api.md)
