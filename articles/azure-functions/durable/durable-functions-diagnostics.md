---
title: Diagnostyka w Durable Functions — Azure
description: Dowiedz się, jak zdiagnozować problemy przy użyciu rozszerzenia Durable Functions Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 62cc5e1762a2a54b26cbebae5aa7cfbf64204ba5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584614"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostyka w usłudze Durable Functions na platformie Azure

Istnieje kilka opcji diagnozowania problemów z [Durable Functions](durable-functions-overview.md). Niektóre opcje są takie same jak w przypadku funkcji regularnych, a niektóre z nich są unikatowe dla usługi Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) jest zalecanym sposobem przeprowadzania diagnostyki i monitorowania w programie Azure Functions. To samo dotyczy Durable Functions. Aby dowiedzieć się, jak korzystać z Application Insights w aplikacji funkcji, zobacz [Azure Functions monitorowania](../functions-monitoring.md).

Azure Functions trwałego rozszerzenia emituje także *zdarzenia śledzenia* , które umożliwiają śledzenie kompleksowego wykonywania aranżacji. Te zdarzenia śledzenia można znaleźć i zbadać przy użyciu narzędzia [Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) w Azure Portal.

### <a name="tracking-data"></a>Śledzenie danych

Każde zdarzenie cyklu życia wystąpienia aranżacji powoduje zapisanie zdarzenia śledzenia w kolekcji **TRACES** w Application Insights. To zdarzenie zawiera ładunek **customDimensions** z kilkoma polami.  Wszystkie nazwy pól są poprzedzone `prop__` .

* **hubName**: nazwa centrum zadań, w którym działają aranżacje.
* **nazwa_aplikacji**: Nazwa aplikacji funkcji. To pole jest przydatne, gdy masz wiele aplikacji funkcji, które współużytkują to samo wystąpienie Application Insights.
* **gniazdoname**: [miejsce wdrożenia](../functions-deployment-slots.md) , w którym jest uruchomiona bieżąca aplikacja funkcji. To pole jest przydatne w przypadku korzystania z miejsc wdrożenia w celu uzyskania wersji swoich aranżacji.
* **FunctionName**: nazwa funkcji programu Orchestrator lub Activity.
* **FunctionType**: typ funkcji, na przykład **Orchestrator** lub **Activity**.
* **InstanceId**: unikatowy identyfikator wystąpienia aranżacji.
* **State**: stan wykonywania cyklu życia wystąpienia. Prawidłowe wartości to:
  * **Zaplanowano**: funkcja została zaplanowana do wykonania, ale jeszcze jej nie uruchomiono.
  * **Rozpoczęto**: funkcja zaczęła działać, ale nie została jeszcze oczekiwana lub ukończona.
  * **Oczekiwane**: program Orchestrator zaplanował pewną pracę i oczekuje na jego zakończenie.
  * **Nasłuchiwanie**: usługa Orchestrator nasłuchuje dla powiadomienia o zdarzeniu zewnętrznym.
  * **Ukończono**: funkcja została ukończona pomyślnie.
  * **Niepowodzenie**: funkcja nie powiodła się z powodu błędu.
* **Przyczyna**: dodatkowe dane skojarzone ze zdarzeniem śledzenia. Jeśli na przykład wystąpienie oczekuje na powiadomienie o zdarzeniu zewnętrznym, to pole wskazuje nazwę zdarzenia, dla którego oczekuje. Jeśli funkcja zakończyła się niepowodzeniem, to pole będzie zawierać szczegóły błędu.
* **isreplay**: wartość logiczna wskazująca, czy zdarzenie śledzenia jest przeznaczone do ponownego uruchomienia.
* **extensionVersion**: wersja rozszerzenia zadania trwałego. Informacje o wersji są szczególnie ważne w przypadku zgłaszania możliwych usterek w rozszerzeniu. Długotrwałe wystąpienia mogą zgłaszać wiele wersji w przypadku, gdy jest ona uruchomiona.
* **sequenceNumber**: numer sekwencji wykonywania dla zdarzenia. W połączeniu z sygnaturą czasową można zamówić zdarzenia według czasu wykonania. *Należy pamiętać, że ta liczba zostanie zresetowana do wartości zero, Jeśli host zostanie uruchomiony ponownie w trakcie działania wystąpienia, dlatego ważne jest, aby zawsze sortować według sygnatur czasowych, a następnie sequenceNumber.*

Poziom szczegółowości śledzenia danych emitowanych do Application Insights można skonfigurować w `logger` sekcji (Functions 1. x) lub `logging` (functions 2,0) `host.json` pliku.

#### <a name="functions-10"></a>Funkcje 1,0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Funkcje 2,0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Domyślnie są emitowane wszystkie zdarzenia śledzenia inne niż powtarzające się. Ilość danych można zmniejszyć przez ustawienie wartości `Host.Triggers.DurableTask` `"Warning"` lub `"Error"` , w przypadku których zdarzenia śledzenia przypadków będą emitowane tylko w sytuacji wyjątkowej.

Aby włączyć emitowanie pełnych zdarzeń powtarzania aranżacji, `LogReplayEvents` można ustawić `true` w `host.json` pliku w `durableTask` sposób pokazany:

#### <a name="functions-10"></a>Funkcje 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funkcje 2,0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Domyślnie dane telemetryczne Application Insights są próbkowane przez środowisko uruchomieniowe Azure Functions, aby uniknąć zbyt częstego emitowania danych. Może to spowodować utratę informacji śledzenia, gdy w krótkim czasie wystąpi wiele zdarzeń cyklu życia. W [artykule monitorowanie Azure Functions](../configure-monitoring.md#configure-sampling) wyjaśniono, jak skonfigurować to zachowanie.

### <a name="single-instance-query"></a>Zapytanie o pojedynczym wystąpieniu

Następujące zapytanie pokazuje historyczne dane śledzenia dla pojedynczego wystąpienia aranżacji funkcji w [sekwencji Hello](durable-functions-sequence.md) . Jest ona zapisywana przy użyciu [języka zapytań Kusto](/azure/data-explorer/kusto/query/). Filtruje wykonywanie powtarzania, tak aby była wyświetlana tylko ścieżka wykonywania *logicznego* . Zdarzenia mogą być uporządkowane według sortowania według `timestamp` i `sequenceNumber` jak pokazano w poniższym zapytaniu:

```kusto
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Wynik jest listą zdarzeń śledzenia, które pokazują ścieżkę wykonywania aranżacji, łącznie z wszystkimi funkcjami działania uporządkowanymi według czasu wykonywania w kolejności rosnącej.

![Zapytanie o zamówione pojedyncze wystąpienie Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Zapytanie podsumowania wystąpienia

Następujące zapytanie wyświetla stan wszystkich wystąpień aranżacji, które zostały uruchomione w określonym przedziale czasu.

```kusto
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Wynikiem jest lista identyfikatorów wystąpień i ich bieżący stan środowiska uruchomieniowego.

![Zapytanie Application Insights pojedynczego wystąpienia](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Trwałe rejestrowanie struktury zadań

Dzienniki rozszerzeń trwałych są przydatne do poznania zachowań logiki aranżacji. Jednak te dzienniki nie zawierają zawsze wystarczającej ilości informacji do debugowania problemów z wydajnością i niezawodnością na poziomie platformy. Począwszy od **2.3.0** o trwałej rozszerzeniu, dzienniki emitowane przez podstawową strukturę zadań trwałych (DTFx) są również dostępne dla kolekcji.

Podczas przeglądania dzienników emitowanych przez DTFx należy zrozumieć, że aparat DTFx składa się z dwóch składników: podstawowego aparatu wysyłania ( `DurableTask.Core` ) i jednego z wielu obsługiwanych dostawców magazynu (Durable Functions `DurableTask.AzureStorage` domyślnie używane).

* **DurableTask. Core**: zawiera informacje o wykonywaniu aranżacji i planowaniu niskiego poziomu.
* **DurableTask. AzureStorage**: zawiera informacje dotyczące interakcji z artefaktami usługi Azure Storage, w tym wewnętrzne kolejki, obiekty blob i tabele magazynu używane do przechowywania i pobierania wewnętrznego stanu aranżacji.

Te dzienniki można włączyć, aktualizując `logging/logLevel` sekcjęhost.jsaplikacji funkcji **w** pliku. Poniższy przykład pokazuje, jak włączyć dzienniki ostrzeżeń i błędów zarówno z poziomu programu `DurableTask.Core` , jak i `DurableTask.AzureStorage` :

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Jeśli włączono Application Insights, te dzienniki będą automatycznie dodawane do `trace` kolekcji. Można wyszukiwać je w taki sam sposób, jak wyszukiwanie innych `trace` dzienników przy użyciu zapytań Kusto.

> [!NOTE]
> W przypadku aplikacji produkcyjnych zaleca się włączenie `DurableTask.Core` i wykonanie `DurableTask.AzureStorage` dzienników przy użyciu `"Warning"` filtru. Wyższe filtry szczegółowości, takie jak `"Information"` są bardzo przydatne do debugowania problemów z wydajnością. Jednak te zdarzenia dziennika są duże i mogą znacząco zwiększyć koszty magazynowania danych Application Insights.

Poniższe zapytanie Kusto pokazuje, jak wykonywać zapytania dotyczące dzienników DTFx. Najważniejszym elementem zapytania jest `where customerDimensions.Category startswith "DurableTask"` ponieważ filtrowanie wyników do dzienników w `DurableTask.Core` `DurableTask.AzureStorage` kategorii i.

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
W wyniku tego powstaje zestaw dzienników zapisany przez dostawców dzienników trwałego struktury zadań.

![Application Insights wyniki zapytania DTFx](./media/durable-functions-diagnostics/app-insights-dtfx.png)

Aby uzyskać więcej informacji o tym, jakie zdarzenia dzienników są dostępne, zobacz [dokumentację dotyczącą rejestrowania strukturalnego struktury zadań w witrynie GitHub](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging).

## <a name="app-logging"></a>Rejestrowanie aplikacji

Ważne jest, aby zachować zachowanie odtwarzania programu Orchestrator podczas pisania dzienników bezpośrednio z poziomu funkcji programu Orchestrator. Rozważmy na przykład następującą funkcję programu Orchestrator:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Wynikowe dane dziennika są podobne do następujących przykładowych danych wyjściowych:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Należy pamiętać, że podczas gdy usługa Logs wywołuje wywoływanie F1, F2 i F3, te funkcje są tylko *faktycznie* wywoływane przy pierwszym napotkaniu. Kolejne wywołania, które nastąpiły podczas powtarzania są pomijane, a dane wyjściowe są odtwarzane do logiki programu Orchestrator.

Jeśli chcesz tylko zapisywać dzienniki dla wykonań, które nie są powtarzane, możesz napisać wyrażenie warunkowe do rejestrowania tylko wtedy, gdy flaga "jest odtwarzana" `false` . Rozważmy przykład powyżej, ale tym razem z testami powtarzania.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

Począwszy od Durable Functions 2,0, funkcje programu .NET Orchestrator mają również opcję tworzenia `ILogger` , która automatycznie filtruje instrukcje dziennika podczas odtwarzania. To automatyczne filtrowanie odbywa się przy użyciu interfejsu API [IDurableOrchestrationContext. CreateReplaySafeLogger (ILogger)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) .

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> Poprzednie przykłady w języku C# są przeznaczone dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

W przypadku powyższych zmian dane wyjściowe dziennika są następujące:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Stan niestandardowy

Stan aranżacji niestandardowej pozwala ustawić niestandardową wartość stanu dla funkcji programu Orchestrator. Ten stan niestandardowy jest następnie widoczny dla klientów zewnętrznych za pośrednictwem [interfejsu API zapytania o stan http](durable-functions-http-api.md#get-instance-status) lub wywołań interfejsu API specyficznych dla języka. Niestandardowy stan aranżacji umożliwia zaawansowane monitorowanie funkcji programu Orchestrator. Na przykład kod funkcji programu Orchestrator może wywołać interfejs API "Ustawianie stanu niestandardowego", aby zaktualizować postęp długotrwałej operacji. Klient, taki jak strona sieci Web lub inny system zewnętrzny, może następnie okresowo badać interfejsy API zapytań o stan HTTP w celu uzyskania bardziej szczegółowych informacji o postępie. Przykładowy kod służący do ustawiania niestandardowej wartości stanu w funkcji programu Orchestrator jest podany poniżej:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Poprzedni przykład w języku C# jest przeznaczony dla Durable Functions 2. x. W przypadku Durable Functions 1. x należy użyć `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext` . Aby uzyskać więcej informacji o różnicach między wersjami, zobacz artykuł dotyczący [wersji Durable Functions](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Po uruchomieniu aranżacji klienci zewnętrzni mogą pobrać ten stan niestandardowy:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

Klienci otrzymają następujące odpowiedzi:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Niestandardowy ładunek stanu jest ograniczony do 16 KB tekstu JSON w formacie UTF-16, ponieważ musi być w stanie zmieścić się w kolumnie Table Storage platformy Azure. Jeśli potrzebujesz większego ładunku, możesz użyć magazynu zewnętrznego.

## <a name="debugging"></a>Debugowanie

Azure Functions obsługuje kod funkcji debugowania bezpośrednio, a ta sama obsługa jest przenoszone do Durable Functions, niezależnie od tego, czy działa na platformie Azure, czy lokalnie. Istnieje jednak kilka zachowań, z którymi należy wiedzieć podczas debugowania:

* **Powtarzanie**: funkcje programu Orchestrator są regularnie [powtarzane](durable-functions-orchestrations.md#reliability) , gdy odbierane są nowe dane wejściowe. To zachowanie oznacza, że pojedyncze *logiczne* wykonywanie funkcji programu Orchestrator może skutkować tym samym punktem przerwania, szczególnie, jeśli jest ustawiony wczesny w kodzie funkcji.
* **Await**: za każdym razem `await` , gdy zostanie napotkany w funkcji programu Orchestrator, daje ona kontrolę z powrotem do dyspozytora trwałej struktury zadań. Jeśli jest to pierwszy raz `await` , skojarzone zadanie *nigdy nie* zostanie wznowione. Ponieważ zadanie nigdy nie zostanie wznowione, *Przechodzenie do* oczekiwania (F10 w programie Visual Studio) nie jest możliwe. Przechodzenie do kolejnych kroków działa tylko wtedy, gdy zadanie jest odtwarzane.
* **Limity czasu obsługi komunikatów**: Durable Functions wewnętrznie używa komunikatów w kolejce do wykonywania zadań programu Orchestrator, Activity i Entity. W środowisku z obsługą wiele maszyn wirtualnych przerwanie debugowania przez dłuższy czas może spowodować, że inna maszyna wirtualna będzie mogła pobrać komunikat, co spowodowało zduplikowane wykonanie. To zachowanie istnieje dla zwykłych funkcji wyzwalacza kolejki, ale jest ważne, aby wskazać w tym kontekście, ponieważ kolejki są szczegółami implementacji.
* **Zatrzymywanie i uruchamianie**: komunikaty w trwałych funkcjach utrzymują się między sesjami debugowania. Jeśli zatrzymasz debugowanie i zakończy proces hosta lokalnego podczas wykonywania trwałej funkcji, ta funkcja może zostać ponownie wykonana automatycznie w przyszłej sesji debugowania. Takie zachowanie może być mylące, gdy nie jest oczekiwane. Czyszczenie wszystkich komunikatów z [wewnętrznych kolejek magazynu](durable-functions-perf-and-scale.md#internal-queue-triggers) między sesjami debugowania jest jedną techniką, aby uniknąć tego zachowania.

> [!TIP]
> Jeśli ustawienia punktów przerwania w funkcjach programu Orchestrator mają być przerywane tylko przy wykonywaniu bez powtarzania, można ustawić warunkowy punkt przerwania, który jest dzielony tylko wtedy, gdy wartość "jest odtwarzana" `false` .

## <a name="storage"></a>Storage

Domyślnie magazyny Durable Functions są przechowywane w usłudze Azure Storage. To zachowanie oznacza, że można sprawdzić stan swoich aranżacji przy użyciu narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

![Zrzut ekranu Eksplorator usługi Azure Storage](./media/durable-functions-diagnostics/storage-explorer.png)

Jest to przydatne w przypadku debugowania, ponieważ widzisz dokładnie, z jakim stanem może być aranżacja. Komunikaty w kolejkach można również sprawdzić, aby dowiedzieć się, jaka jest oczekująca (lub w niektórych przypadkach zablokowana).

> [!WARNING]
> Mimo że wygodnie jest wyświetlić historię wykonywania w usłudze Table Storage, unikaj podejmowania jakichkolwiek zależności od tej tabeli. Może ulec zmianie w miarę rozwoju rozszerzenia Durable Functions.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o monitorowaniu w Azure Functions](../functions-monitoring.md)
