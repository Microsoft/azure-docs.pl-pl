---
title: Azure Event Grid powiązanie danych wyjściowych dla Azure Functions
description: Dowiedz się, jak wysłać Zdarzenie Event Grid w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094680"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid powiązanie danych wyjściowych dla Azure Functions

Użyj powiązania danych wyjściowych Event Grid do zapisywania zdarzeń w temacie niestandardowym. Musisz mieć prawidłowy [klucz dostępu dla tematu niestandardowego](../event-grid/security-authenticate-publishing-clients.md).

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-event-grid.md).

> [!NOTE]
> Powiązanie danych wyjściowych Event Grid nie obsługuje sygnatur dostępu współdzielonego (tokeny SAS). Musisz użyć klucza dostępu tematu.

> [!IMPORTANT]
> Powiązanie danych wyjściowych Event Grid jest dostępne tylko dla funkcji 2. x i wyższych.

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która zapisuje komunikat do Event Grid niestandardowego tematu przy użyciu metody zwracanej wartości jako dane wyjściowe:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Poniższy przykład pokazuje, jak używać `IAsyncCollector` interfejsu do wysyłania partii komunikatów.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład pokazuje Event Grid dane wyjściowe powiązania w *function.js* pliku.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Oto kod skryptu języka C#, który tworzy jedno zdarzenie:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Oto kod skryptu języka C#, który tworzy wiele zdarzeń:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład pokazuje Event Grid dane wyjściowe powiązania w *function.js* pliku.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Oto kod JavaScript, który tworzy pojedyncze zdarzenie:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Oto kod JavaScript, który tworzy wiele zdarzeń:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

W poniższym przykładzie pokazano, jak skonfigurować funkcję do wyprowadzania komunikatu o zdarzeniu Event Grid. Sekcja gdzie `type` jest ustawiona do `eventGrid` konfigurowania wartości wymaganych do nawiązania Event Grid danych wyjściowych.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

W funkcji Użyj polecenia, `Push-OutputBinding` Aby wysłać zdarzenie do niestandardowego tematu za pomocą powiązania danych wyjściowych Event Grid.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład pokazuje powiązanie wyzwalacza w *function.jsw* pliku i [funkcji języka Python](functions-reference-python.md) , która używa powiązania. Następnie wysyła w zdarzeniu do tematu niestandardowego, zgodnie z opisem w `topicEndpointUri` .

Oto dane powiązania w *function.js* pliku:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Oto przykład języka Python do wysłania zdarzenia do tematu niestandardowego przez ustawienie `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W przypadku [bibliotek klas języka C#](functions-dotnet-class-library.md)należy użyć atrybutu [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę ustawienia aplikacji, która zawiera nazwę tematu niestandardowego i nazwę ustawienia aplikacji, która zawiera klucz tematu. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Output-Configuration](#configuration). Oto `EventGrid` przykład atrybutu:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Aby zapoznać się z pełnym przykładem, zobacz [przykład](#example).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Python.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `EventGrid` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiona na wartość "eventGrid". |
|**wskazywa** | n/d | Musi być ustawiona na wartość "out". Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje zdarzenie. |
|**topicEndpointUri** |**TopicEndpointUri** | Nazwa ustawienia aplikacji, która zawiera identyfikator URI tematu niestandardowego, na przykład `MyTopicEndpointUri` . |
|**topicKeySetting** |**TopicKeySetting** | Nazwa ustawienia aplikacji, która zawiera klucz dostępu dla tematu niestandardowego. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Upewnij się, że wartość `TopicEndpointUri` właściwości konfiguracja jest ustawiona na nazwę ustawienia aplikacji, która zawiera identyfikator URI tematu niestandardowego. Nie określaj identyfikatora URI tematu niestandardowego bezpośrednio w tej właściwości.

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out EventGridEvent paramName` . Aby napisać wiele komunikatów, można użyć `ICollector<EventGridEvent>` lub zamiast `IAsyncCollector<EventGridEvent>` `out EventGridEvent` .

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out EventGridEvent paramName` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. Aby napisać wiele komunikatów, można użyć `ICollector<EventGridEvent>` lub zamiast `IAsyncCollector<EventGridEvent>` `out EventGridEvent` .

# <a name="java"></a>[Java](#tab/java)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia wyjściowego przy użyciu metody `context.bindings.<name>` Where `<name>` jest wartością określoną we `name` właściwości *function.json*.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Dostęp do zdarzenia wyjściowego przy użyciu `Push-OutputBinding` polecenia cmdlet do wysyłania zdarzenia do powiązania danych wyjściowych Event Grid.

# <a name="python"></a>[Python](#tab/python)

Powiązanie danych wyjściowych Event Grid nie jest dostępne dla języka Python.

---

## <a name="next-steps"></a>Następne kroki

* [Wysyłanie zdarzenia Event Grid](./functions-bindings-event-grid-trigger.md)
