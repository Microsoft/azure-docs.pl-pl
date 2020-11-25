---
title: Zarządzanie wystąpieniami w Durable Functions — Azure
description: Dowiedz się, jak zarządzać wystąpieniami w rozszerzeniu Durable Functions Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 2b99d032b953caecfca2b34d5eadafe94f45f307
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009538"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Zarządzanie wystąpieniami w Durable Functions na platformie Azure

Jeśli używasz rozszerzenia [Durable Functions](durable-functions-overview.md) Azure Functions lub chcesz rozpocząć wykonywanie tego działania, upewnij się, że najlepiej jest z niego korzystać. Aby zoptymalizować Durable Functions wystąpienia aranżacji, możesz dowiedzieć się więcej o sposobach zarządzania nimi. Ten artykuł zawiera szczegółowe informacje o każdej operacji zarządzania wystąpieniem.

Można na przykład uruchamiać i kończyć wystąpienia oraz wykonywać zapytania o wystąpienia, w tym możliwość wykonywania zapytań o wszystkie wystąpienia i wystąpienia zapytań za pomocą filtrów. Ponadto możesz wysyłać zdarzenia do wystąpień, poczekać na zakończenie aranżacji i pobrać adresy URL elementu webhook zarządzania HTTP. W tym artykule opisano również inne operacje zarządzania, w tym wystąpienia odwracania, przeczyszczania historii wystąpień i usuwania centrum zadań.

W Durable Functions są dostępne opcje implementacji każdej z tych operacji zarządzania. W tym artykule przedstawiono przykłady korzystania z [Azure Functions Core Tools](../functions-run-local.md) dla platformy .NET (C#), JavaScript i Python.

## <a name="start-instances"></a>Wystąpienia uruchamiania

Ważne jest, aby uruchomić wystąpienie aranżacji. Jest to często wykonywane, gdy używasz powiązania Durable Functions w wyzwalaczu innej funkcji.

`StartNewAsync`Metoda (.NET), `startNew` (JavaScript) lub `start_new` (Python) dla [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) uruchamia nowe wystąpienie. Wewnętrznie metoda ta enqueues komunikat do kolejki sterującej, która następnie wyzwala początek funkcji o określonej nazwie, która używa [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger).

Ta operacja asynchroniczna kończy się po pomyślnym zaplanowaniu procesu aranżacji.

Parametry uruchamiania nowego wystąpienia aranżacji są następujące:

* **Name**: nazwa funkcji programu Orchestrator do zaplanowania.
* **Wejście**: wszystkie dane, które można serializować w formacie JSON, które powinny być przesyłane jako dane wejściowe do funkcji programu Orchestrator.
* **InstanceId**: (opcjonalnie) unikatowy identyfikator wystąpienia. Jeśli ten parametr nie zostanie określony, metoda używa identyfikatora losowego.

> [!TIP]
> Użyj losowego identyfikatora wystąpienia. Losowe identyfikatory wystąpień pomagają zapewnić równoważną dystrybucję obciążenia podczas skalowania funkcji programu Orchestrator na wielu maszynach wirtualnych. Prawidłowy czas użycia nielosowych identyfikatorów wystąpień to, gdy identyfikator musi pochodzić ze źródła zewnętrznego lub gdy wdrażany jest wzorzec [pojedynczego programu Orchestrator](durable-functions-singletons.md) .

Poniższy kod jest przykładową funkcją, która uruchamia nowe wystąpienie aranżacji:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>O ile nie określono inaczej, przykłady na tej stronie używają wyzwalacza HTTP z następującym function.jsw systemie.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
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

> [!NOTE]
> Ten przykład dotyczy Durable Functions wersja 2. x. W wersji 1. x, użyj `orchestrationClient` zamiast `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

# <a name="python"></a>[Python](#tab/python)

<a name="javascript-function-json"></a>O ile nie określono inaczej, przykłady na tej stronie używają wyzwalacza HTTP z następującym function.jsw systemie.

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [    
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
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

> [!NOTE]
> Ten przykład dotyczy Durable Functions wersja 2. x. W wersji 1. x, użyj `orchestrationClient` zamiast `durableClient` .

**__init__. PR**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    
    instance_id = await client.start_new('HelloWorld', None, None)
    logging.log(f"Started orchestration with ID = ${instance_id}.")

```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Wystąpienie można również uruchomić bezpośrednio przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` . Przyjmuje następujące parametry:

* **`function-name` (wymagane)**: nazwa funkcji do uruchomienia.
* **`input` (opcjonalnie)**: dane wejściowe funkcji, wbudowane lub za pomocą pliku JSON. Dla plików Dodaj prefiks do ścieżki do pliku, na przykład `@` `@path/to/file.json` .
* **`id` (opcjonalnie)**: identyfikator wystąpienia aranżacji. Jeśli ten parametr nie jest określony, polecenie używa losowego identyfikatora GUID.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to AzureWebJobsStorage.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Wartość domyślna to DurableFunctionsHub. Możesz również ustawić tę wartość w [host.jsna](durable-functions-bindings.md#host-json) przy użyciu DurableTask: HubName.

> [!NOTE]
> Polecenia podstawowych narzędzi zakładają, że są one uruchamiane z katalogu głównego aplikacji funkcji. Jeśli jawnie podano `connection-string-setting` Parametry i `task-hub-name` , można uruchomić polecenia z dowolnego katalogu. Chociaż można uruchamiać te polecenia bez uruchomienia hosta aplikacji funkcji, może się okazać, że nie można obserwować niektórych efektów, chyba że host jest uruchomiony. Na przykład `start-new` polecenie enqueues komunikat startowy w docelowym centrum zadań, ale aranżacja nie działa, chyba że jest uruchomiony proces hosta aplikacji funkcji, który może przetwarzać komunikat.

Następujące polecenie uruchamia funkcję o nazwie HelloWorld i przekazuje `counter-data.json` do niej zawartość pliku:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Wystąpienia zapytań

W ramach wysiłków związanych z zarządzaniem aranżacjami najprawdopodobniej trzeba będzie zebrać informacje o stanie wystąpienia aranżacji (na przykład czy zostało zakończone normalnie lub zakończone niepowodzeniem).

`GetStatusAsync`Metoda (.NET), `getStatus` (JavaScript) lub `get_status` (Python) dla [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) wysyła zapytanie o stan wystąpienia aranżacji.

Przyjmuje (wymagane), (opcjonalnie), (opcjonalnie) `instanceId` `showHistory` `showHistoryOutput` i `showInput` (opcjonalnie) jako parametry.

* **`showHistory`**: Jeśli wartość jest ustawiona na `true` , odpowiedź zawiera historię wykonania.
* **`showHistoryOutput`**: Jeśli wartość jest ustawiona na `true` , historia wykonywania zawiera dane wyjściowe działania.
* **`showInput`**: Jeśli wartość jest ustawiona na `false` , odpowiedź nie będzie zawierać wejścia funkcji. Wartość domyślna to `true`.

Metoda zwraca obiekt o następujących właściwościach:

* **Name**: nazwa funkcji programu Orchestrator.
* **InstanceId**: identyfikator wystąpienia aranżacji (powinien być taki sam jak `instanceId` dane wejściowe).
* **CreatedTime**: godzina, o której uruchomiono funkcję programu Orchestrator.
* **LastUpdatedTime**: godzina ostatniego przetworzenia punktu kontrolnego aranżacji.
* **Wejście**: dane wejściowe funkcji jako wartość JSON. To pole nie jest wypełniane, jeśli `showInput` ma wartość false.
* **CustomStatus**: niestandardowy stan aranżacji w formacie JSON.
* **Dane wyjściowe**: dane wyjściowe funkcji jako wartości JSON (Jeśli funkcja została ukończona). Jeśli działanie programu Orchestrator nie powiodło się, ta właściwość zawiera szczegóły błędu. Jeśli funkcja programu Orchestrator została zakończona, ta właściwość zawiera przyczynę zakończenia (jeśli istnieje).
* **RuntimeStatus**: jedna z następujących wartości:
  * **Oczekiwanie**: wystąpienie zostało zaplanowane, ale jeszcze nie zostało uruchomione.
  * **Uruchomione**: wystąpienie zostało uruchomione.
  * **Ukończono**: wystąpienie zostało zakończone normalnie.
  * **ContinuedAsNew**: wystąpienie zostało uruchomione ponownie z nową historią. Ten stan jest stanem przejściowym.
  * **Niepowodzenie**: wystąpienie nie powiodło się z powodu błędu.
  * **Przerwano**: wystąpienie zostało zatrzymane w sposób nieoczekiwany.
* **Historia**: historia wykonywania aranżacji. To pole jest wypełniane tylko wtedy `showHistory` , gdy jest ustawione na `true` .

Ta metoda zwraca `null` (.NET), `undefined` (JavaScript) lub `None` (Python), jeśli wystąpienie nie istnieje.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    status = await client.get_status(instance_id)
    # do something based on the current status
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje również możliwość bezpośredniego pobrania stanu wystąpienia aranżacji przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` . Przyjmuje następujące parametry:

* **`id` (wymagane)**: identyfikator wystąpienia aranżacji.
* **`show-input` (opcjonalnie)**: w przypadku ustawienia wartości `true` odpowiedź zawiera dane wejściowe funkcji. Wartość domyślna to `false`.
* **`show-output` (opcjonalnie)**: Jeśli wartość jest ustawiona na `true` , odpowiedź zawiera dane wyjściowe funkcji. Wartość domyślna to `false`.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w [host.jsna](durable-functions-bindings.md#host-json), przy użyciu DurableTask: HubName.

Następujące polecenie pobiera stan (łącznie z danymi wejściowymi i wyjściowymi) wystąpienia z IDENTYFIKATORem wystąpienia aranżacji 0ab8c55a66644d68a3a8b220b12d209c. Przyjęto założenie, że uruchamiasz `func` polecenie z katalogu głównego aplikacji funkcji:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Możesz użyć polecenia, `durable get-history` Aby pobrać historię wystąpienia aranżacji. Przyjmuje następujące parametry:

* **`id` (wymagane)**: identyfikator wystąpienia aranżacji.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w host.jsna, przy użyciu durableTask: HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Badaj wszystkie wystąpienia

Zamiast wysyłać zapytania do jednego wystąpienia w ramach aranżacji, może się okazać, że jest bardziej wydajna kwerenda wszystkich jednocześnie.

Można użyć metody [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync?view=azure-dotnet#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET), [getStatusAll](/javascript/api/durable-functions/durableorchestrationclient?view=azure-node-latest#getstatusall--) (JavaScript) lub `get_status_all` (Python) do wykonywania zapytań dotyczących stanu wszystkich wystąpień aranżacji. W programie .NET można przekazać `CancellationToken` obiekt w przypadku, gdy chcesz go anulować. Metoda zwraca listę obiektów, które reprezentują wystąpienia aranżacji zgodne z parametrami zapytania.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var noFilter = new OrchestrationStatusQueryCondition();
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        noFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import json
import azure.functions as func
import azure.durable_functions as df


async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instances = await client.get_status_all()

    for instance in instances:
        logging.log(json.dumps(instance))
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje również możliwość bezpośredniego zapytania o wystąpienia przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` . Przyjmuje następujące parametry:

* **`top` (opcjonalnie)**: to polecenie obsługuje stronicowanie. Ten parametr odnosi się do liczby wystąpień pobranych dla żądania. Wartość domyślna to 10.
* **`continuation-token` (opcjonalnie)**: token wskazujący stronę lub sekcję wystąpień do pobrania. Każde `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w [host.jsna](durable-functions-bindings.md#host-json), przy użyciu DurableTask: HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Zapytania o wystąpienia z filtrami

Co zrobić, jeśli nie potrzebujesz wszystkich informacji, które mogą być używane przez zapytanie wystąpienia standardowego? Na przykład co zrobić, jeśli szukasz tylko czasu utworzenia aranżacji lub stanu środowiska uruchomieniowego aranżacji? Możesz zawęzić zapytanie, stosując filtry.

Użyj metody [ListInstancesAsync](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationclient.listinstancesasync?view=azure-dotnet#Microsoft_Azure_WebJobs_Extensions_DurableTask_IDurableOrchestrationClient_ListInstancesAsync_Microsoft_Azure_WebJobs_Extensions_DurableTask_OrchestrationStatusQueryCondition_System_Threading_CancellationToken_) (.NET) lub [getStatusBy](/javascript/api/durable-functions/durableorchestrationclient?view=azure-node-latest#getstatusby-date---undefined--date---undefined--orchestrationruntimestatus---) (JavaScript), aby uzyskać listę wystąpień aranżacji zgodnych z zestawem wstępnie zdefiniowanych filtrów.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    // Get the first 100 running or pending instances that were created between 7 and 1 day(s) ago
    var queryFilter = new OrchestrationStatusQueryCondition
    {
        RuntimeStatus = new[]
        {
            OrchestrationRuntimeStatus.Pending,
            OrchestrationRuntimeStatus.Running,
        },
        CreatedTimeFrom = DateTime.UtcNow.Subtract(TimeSpan.FromDays(7)),
        CreatedTimeTo = DateTime.UtcNow.Subtract(TimeSpan.FromDays(1)),
        PageSize = 100,
    };
    
    OrchestrationStatusQueryResult result = await client.ListInstancesAsync(
        queryFilter,
        CancellationToken.None);
    foreach (DurableOrchestrationStatus instance in result.DurableOrchestrationState)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    }
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
from datetime import datetime
import json
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.OrchestrationRuntimeStatus import OrchestrationRuntimeStatus

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    runtime_status = [OrchestrationRuntimeStatus.Completed, OrchestrationRuntimeStatus.Running]

    instances = await client.get_status_by(
        datetime(2018, 3, 10, 10, 1, 0),
        datetime(2018, 3, 10, 10, 23, 59),
        runtime_status
    )

    for instance in instances:
        logging.log(json.dumps(instance))
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

W Azure Functions Core Tools można również użyć `durable get-instances` polecenia z filtrami. Oprócz powyższych `top` `continuation-token` parametrów,, `connection-string-setting` i `task-hub-name` , można użyć trzech parametrów filtru ( `created-after` , `created-before` i `runtime-status` ).

* **`created-after` (opcjonalnie)**: Pobierz wystąpienia utworzone po tej dacie/godzinie (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* **`created-before` (opcjonalnie)**: Pobierz wystąpienia utworzone przed tą datą/godziną (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* **`runtime-status` (opcjonalnie)**: Pobierz wystąpienia z określonym stanem (na przykład uruchomioną lub ukończoną). Może zapewnić wiele stanów (rozdzielonych spacjami).
* **`top` (opcjonalnie)**: liczba wystąpień pobranych dla żądania. Wartość domyślna to 10.
* **`continuation-token` (opcjonalnie)**: token wskazujący stronę lub sekcję wystąpień do pobrania. Każde `get-instances` wykonanie zwraca token do następnego zestawu wystąpień.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w [host.jsna](durable-functions-bindings.md#host-json), przy użyciu DurableTask: HubName.

Jeśli nie podano żadnych filtrów ( `created-after` , `created-before` lub `runtime-status` ), polecenie po prostu pobiera `top` wystąpienia bez względu na stan środowiska uruchomieniowego lub czas utworzenia.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Przerwij wystąpienia

Jeśli masz wystąpienie aranżacji, które trwa zbyt długo lub musisz zatrzymać je przed zakończeniem z dowolnego powodu, będziesz mieć możliwość jej zakończenia.

`TerminateAsync`Aby przerwać wystąpienia, można użyć metody (.NET), `terminate` (JavaScript) lub `terminate` (Python) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) . Dwa parametry są `instanceId` i `reason` ciągiem, które są zapisywane w dziennikach i w stanie wystąpienia.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "It was time to be done."
    return client.terminate(instance_id, reason)
```

---

Przerwane wystąpienie będzie ostatecznie przejść do `Terminated` stanu. Jednak to przejście nie zostanie wykonane natychmiast. Zamiast tego operacja Terminate zostanie umieszczona w kolejce w centrum zadań wraz z innymi operacjami dla tego wystąpienia. Można użyć interfejsów API [zapytania wystąpienia](#query-instances) , aby wiedzieć, kiedy przerwane wystąpienie faktycznie osiągnęło `Terminated` stan.

> [!NOTE]
> Przerwanie wystąpienia nie jest obecnie propagowane. Funkcje działania i podzbiory są wykonywane do zakończenia, bez względu na to, czy zostało przerwane wystąpienie aranżacji, które je wywołało.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz również przerwać wystąpienie aranżacji bezpośrednio za pomocą polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` . Przyjmuje następujące parametry:

* **`id` (wymagane)**: identyfikator wystąpienia aranżacji do przerwania.
* **`reason` (opcjonalnie)**: Przyczyna zakończenia.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w [host.jsna](durable-functions-bindings.md#host-json), przy użyciu DurableTask: HubName.

Następujące polecenie kończy wystąpienie aranżacji o IDENTYFIKATORze 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Wysyłanie zdarzeń do wystąpień

W niektórych scenariuszach ważne jest, aby funkcja programu Orchestrator mogła czekać i słuchać zdarzeń zewnętrznych. Obejmuje to [funkcje monitorowania](durable-functions-overview.md#monitoring) i funkcje, które oczekują na [interakcję przez człowieka](durable-functions-overview.md#human).

Wysyłać powiadomienia o zdarzeniach do uruchomionych wystąpień przy użyciu `RaiseEventAsync` metody (.NET) lub `raiseEvent` metody (JavaScript) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client). Wystąpienia, które mogą obsługiwać te zdarzenia, są te, które oczekują na wywołanie `WaitForExternalEvent` (.NET) lub dają do `waitForExternalEvent` wywołania (JavaScript).

Parametry do `RaiseEventAsync` (.NET) i `raiseEvent` (JavaScript) są następujące:

* **InstanceId**: unikatowy identyfikator wystąpienia.
* **EventName**: Nazwa zdarzenia do wysłania.
* **EVENTDATA**: ładunek do serializacji JSON do wysłania do wystąpienia.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    event_data = [1, 2 ,3]
    return client.raise_event(instance_id, 'MyEvent', event_data)
```

---

> [!NOTE]
> Jeśli nie istnieje wystąpienie aranżacji o określonym IDENTYFIKATORze wystąpienia, komunikat o zdarzeniu zostanie odrzucony. Jeśli wystąpienie istnieje, ale jeszcze nie oczekuje na zdarzenie, zdarzenie będzie przechowywane w stanie wystąpienia do momentu, gdy będzie gotowe do odebrania i przetworzenia.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz również podnieść zdarzenie do wystąpienia aranżacji bezpośrednio za pomocą polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` . Przyjmuje następujące parametry:

* **`id` (wymagane)**: identyfikator wystąpienia aranżacji.
* **`event-name`**: Nazwa zdarzenia do podniesienia.
* **`event-data` (opcjonalnie)**: dane do wysłania do wystąpienia aranżacji. Może to być ścieżka do pliku JSON lub można dostarczyć dane bezpośrednio w wierszu polecenia.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Wartość domyślna to `DurableFunctionsHub`. Można ją również ustawić w [host.jsna](durable-functions-bindings.md#host-json), przy użyciu DurableTask: HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Poczekaj na zakończenie aranżacji

W długotrwałych aranżacjach warto oczekiwać i uzyskać wyniki aranżacji. W takich przypadkach warto również zdefiniować przedział czasu dla aranżacji. W przypadku przekroczenia limitu czasu, zamiast wyników należy zwrócić stan aranżacji.

`WaitForCompletionOrCreateCheckStatusResponseAsync`Metody (.NET) lub `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) można użyć do uzyskania danych wyjściowych z wystąpienia aranżacji synchronicznie. Domyślnie te metody używają wartości domyślnej 10 sekund dla `timeout` i 1 sekunda dla `retryInterval` .  

Oto przykład funkcji wyzwalacza HTTP, która pokazuje, jak używać tego interfejsu API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

timeout = "timeout"
retry_interval = "retryInterval"

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    instance_id = await client.start_new(req.route_params['functionName'], None, req.get_body())
    logging.log(f"Started orchestration with ID = '${instance_id}'.")

    timeout_in_milliseconds = get_time_in_seconds(req, timeout)
    timeout_in_milliseconds = timeout_in_milliseconds if timeout_in_milliseconds != None else 30000
    retry_interval_in_milliseconds = get_time_in_seconds(req, retry_interval)
    retry_interval_in_milliseconds = retry_interval_in_milliseconds if retry_interval_in_milliseconds != None else 1000

    return client.wait_for_completion_or_create_check_status_response(
        req,
        instance_id,
        timeout_in_milliseconds,
        retry_interval_in_milliseconds
    )

def get_time_in_seconds(req: func.HttpRequest, query_parameter_name: str):
    query_value = req.params.get(query_parameter_name)
    return query_value if query_value != None else 1000
```

---

Wywołaj funkcję z następującym wierszem. Użyj 2 sekund dla limitu czasu i 0,5 sekund dla interwału ponawiania próby:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

W zależności od czasu wymaganego do uzyskania odpowiedzi z wystąpienia aranżacji istnieją dwa przypadki:

* Wystąpienia aranżacji zakończone w określonym limicie czasu (w tym przypadku 2 sekund), a odpowiedź to rzeczywiste dane wyjściowe wystąpienia aranżacji, dostarczone synchronicznie:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* Wystąpienia aranżacji nie mogą zakończyć się w określonym limicie czasu, a odpowiedź jest domyślnie opisana w temacie [odnajdowanie adresów URL interfejsu API protokołu HTTP](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Format adresów URL elementu webhook może się różnić w zależności od używanej wersji hosta Azure Functions. Poprzedni przykład dotyczy hosta Azure Functions 2,0.

## <a name="retrieve-http-management-webhook-urls"></a>Pobierz adresy URL elementu webhook zarządzania HTTP

Możesz użyć systemu zewnętrznego do monitorowania lub zgłaszania zdarzeń do aranżacji. Systemy zewnętrzne mogą komunikować się z Durable Functions za pośrednictwem adresów URL elementu webhook, które są częścią domyślnej odpowiedzi opisanej w [odnajdowaniu adresów URL interfejsu API protokołu HTTP](durable-functions-http-features.md#http-api-url-discovery). Adresy URL elementu webhook mogą być alternatywnie dostępne programowo przy użyciu [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client). `CreateHttpManagementPayload`Metody (.NET) lub `createHttpManagementPayload` (JavaScript) mogą służyć do uzyskania obiektu możliwego do serializacji, który zawiera te adresy URL elementu webhook.

`CreateHttpManagementPayload`Metody (.NET) i `createHttpManagementPayload` (JavaScript) mają jeden parametr:

* **InstanceId**: unikatowy identyfikator wystąpienia.

Metody zwracają obiekt z następującymi właściwościami ciągu:

* **Identyfikator**: identyfikator wystąpienia aranżacji (powinien być taki sam jak `InstanceId` dane wejściowe).
* **StatusQueryGetUri**: adres URL stanu wystąpienia aranżacji.
* **SendEventPostUri**: adres URL "zgłoś zdarzenie" wystąpienia aranżacji.
* **TerminatePostUri**: adres URL "Terminate" wystąpienia aranżacji.
* **PurgeHistoryDeleteUri**: adres URL "Przeczyść historię" wystąpienia aranżacji.

Funkcje mogą wysyłać wystąpienia tych obiektów do systemów zewnętrznych w celu monitorowania lub wywoływania zdarzeń dotyczących odpowiednich aranżacji, jak pokazano w następujących przykładach:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. Dla Durable Functions 1. x, należy użyć `DurableActivityContext` zamiast, należy użyć `IDurableActivityContext` `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.cosmosdb.cdb.Document:
    client = df.DurableOrchestrationClient(starter)

    payload = client.create_check_status_response(req, instance_id).get_body().decode()

    return func.cosmosdb.CosmosDBConverter.encode({
        id: instance_id,
        payload: payload
    })
```
---

## <a name="rewind-instances-preview"></a>Przewiń do tyłu wystąpienia (wersja zapoznawcza)

Jeśli masz błąd aranżacji z nieoczekiwanej przyczyny, możesz *przewinąć* wystąpienie do poprzedniego stanu w dobrej kondycji, korzystając z interfejsu API skompilowanego do tego celu.

> [!NOTE]
> Ten interfejs API nie jest przeznaczony do zastępowania w celu zapewnienia prawidłowej obsługi błędów i zasad ponawiania. Zamiast tego jest przeznaczona do użycia tylko w przypadkach, w których wystąpienia aranżacji kończą się niepowodzeniem z nieoczekiwanych przyczyn. Aby uzyskać więcej informacji na temat obsługi błędów i zasad ponawiania, zobacz artykuł [Obsługa błędów](durable-functions-error-handling.md) .

Użyj `RewindAsync` metody (.NET) lub `rewind` (JavaScript) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) w celu przełączenia aranżacji do stanu *uruchomienia* . Ta metoda spowoduje również ponowne uruchomienie operacji lub niepowodzeń wykonania aranżacji podrzędnej, które spowodowały błąd aranżacji.

Załóżmy na przykład, że masz przepływ pracy obejmujący serię [zatwierdzeń ludzkich](durable-functions-overview.md#human). Załóżmy, że istnieje szereg funkcji działania, które powiadamiają kogoś o konieczności zatwierdzenia i oczekują odpowiedzi w czasie rzeczywistym. Po odebraniu odpowiedzi przez wszystkie działania zatwierdzenia lub przekroczeniu limitu czasu inne działanie nie powiedzie się z powodu błędnej konfiguracji aplikacji, na przykład parametrów połączenia z bazą danych. Wynikiem jest niepowodzenie aranżacji w przepływie pracy. Za pomocą `RewindAsync` interfejsu API (.NET) lub `rewind` (JavaScript), administrator aplikacji może naprawić błąd konfiguracji i przewinąć niepowodzenie aranżacji do stanu bezpośrednio przed awarią. Żadna z kroków interakcji między ludźmi nie musi zostać ponownie zatwierdzona, a organizacja może teraz pomyślnie zakończyć pracę.

> [!NOTE]
> Funkcja *przewijania do tyłu* nie obsługuje zawijania wystąpień aranżacji korzystających z trwałych czasomierzy.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Ta funkcja nie jest obecnie obsługiwana w języku Python.

<!-- ```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    reason = "Orchestrator failed and needs to be revived."
    return client.rewind(instance_id, reason)
``` -->

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Możesz również bezpośrednio przewinąć wystąpienie aranżacji przy użyciu polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` . Przyjmuje następujące parametry:

* **`id` (wymagane)**: identyfikator wystąpienia aranżacji.
* **`reason` (opcjonalnie)**: Przyczyna odwinięcia wystąpienia aranżacji.
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Domyślnie jest używana nazwa centrum zadań w [host.jsna](durable-functions-bindings.md#host-json) pliku.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Przeczyść historię wystąpień

Aby usunąć wszystkie dane skojarzone z aranżacją, można przeczyścić historię wystąpień. Na przykład możesz chcieć usunąć wszystkie wiersze tabeli platformy Azure oraz duże obiekty blob komunikatów powiązane z ukończonym wystąpieniem. W tym celu należy użyć `PurgeInstanceHistoryAsync` metody (.NET) lub `purgeInstanceHistory` (JavaScript) [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client).

Ta metoda ma dwa przeciążenia. Pierwsze Przeciążenie Przeczyszcza historię według identyfikatora wystąpienia aranżacji:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Zobacz [Uruchamianie wystąpień](#javascript-function-json) function.jsw konfiguracji.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)

    return client.purge_instance_history(instance_id)
```

---

W następnym przykładzie pokazano funkcję wyzwalaną przez czasomierz, która Przeczyszcza historię wszystkich wystąpień aranżacji, które zakończyły się po upływie określonego interwału czasu. W tym przypadku usuwa dane dla wszystkich wystąpień zakończonych 30 lub więcej dni temu. Zaplanowano uruchomienie raz dziennie, przy 12% AM:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> Poprzedni kod w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `OrchestrationClient` atrybutu zamiast `DurableClient` atrybutu i należy użyć `DurableOrchestrationClient` typu parametru zamiast `IDurableOrchestrationClient` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`purgeInstanceHistoryBy`Metoda może służyć do warunkowego przeczyszczania historii wystąpień dla wielu wystąpień.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
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

> [!NOTE]
> Ten przykład dotyczy Durable Functions wersja 2. x. W wersji 1. x, użyj `orchestrationClient` zamiast `durableClient` .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from azure.durable_functions.models.DurableOrchestrationStatus import OrchestrationRuntimeStatus
from datetime import datetime, timedelta

async def main(req: func.HttpRequest, starter: str, instance_id: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    created_time_from = datetime.datetime()
    created_time_to = datetime.datetime.today + timedelta(days = -30)
    runtime_statuses = [OrchestrationRuntimeStatus.Completed]

    return client.purge_instance_history_by(created_time_from, created_time_to, runtime_statuses)
```
---

> [!NOTE]
> Aby operacja przeczyszczania historii zakończyła się powodzeniem, stan czasu wykonywania wystąpienia docelowego musi **być zakończony**, **zakończony** lub **Niepowodzenie**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Historię wystąpienia aranżacji można przeczyścić, używając polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` . Podobnie jak w przypadku drugiego przykładu w języku C# w poprzedniej sekcji, Przeczyszcza historię wszystkich wystąpień aranżacji utworzonych w określonym przedziale czasu. Możliwe jest dalsze filtrowanie przeczyszczonych wystąpień według stanu środowiska uruchomieniowego. Polecenie ma kilka parametrów:

* **`created-after` (opcjonalnie)**: Przeczyść historię wystąpień utworzonych po tej dacie/godzinie (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* **`created-before` (opcjonalnie)**: Przeczyść historię wystąpień utworzonych przed tą datą/godziną (UTC). Zaakceptowane daty i godziny w formacie ISO 8601.
* **`runtime-status` (opcjonalnie)**: Przeczyść historię wystąpień z określonym stanem (na przykład uruchomioną lub ukończoną). Może zapewnić wiele stanów (rozdzielonych spacjami).
* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Domyślnie jest używana nazwa centrum zadań w [host.jsna](durable-functions-bindings.md#host-json) pliku.

Następujące polecenie usuwa historię wszystkich wystąpień zakończonych niepowodzeniem, które zostały utworzone przed 14 listopada 2018 o 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Usuwanie centrum zadań

Za pomocą polecenia [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` można usunąć wszystkie artefakty magazynu skojarzone z konkretnym centrum zadań, w tym tabele, kolejki i obiekty blob usługi Azure Storage. Polecenie ma dwa parametry:

* **`connection-string-setting` (opcjonalnie)**: Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia. Wartość domyślna to `AzureWebJobsStorage`.
* **`task-hub-name` (opcjonalnie)**: nazwa centrum zadań Durable Functions do użycia. Domyślnie jest używana nazwa centrum zadań w [host.jsna](durable-functions-bindings.md#host-json) pliku.

Następujące polecenie usuwa wszystkie dane usługi Azure Storage skojarzone z `UserTest` centrum zadań.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać przechowywanie wersji](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Wbudowana dokumentacja interfejsu API protokołu HTTP do zarządzania wystąpieniami](durable-functions-http-api.md)