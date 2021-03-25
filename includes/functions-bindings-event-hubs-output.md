---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: cdd8b42f3ef7530809c50925c631b7aa90ee213c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105900"
---
Użyj powiązania danych wyjściowych Event Hubs do zapisywania zdarzeń w strumieniu zdarzeń. Musisz mieć uprawnienie do wysłania do centrum zdarzeń, aby zapisywać w nim zdarzenia.

Przed podjęciem próby wdrożenia powiązania wyjściowego upewnij się, że istnieją odwołania do wymaganych pakietów.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](../articles/azure-functions/functions-dotnet-class-library.md) , która zapisuje komunikat w centrum zdarzeń przy użyciu wartości zwracanej przez metodę jako dane wyjściowe:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Poniższy przykład pokazuje, jak używać `IAsyncCollector` interfejsu do wysyłania partii komunikatów. Ten scenariusz jest typowy podczas przetwarzania komunikatów pochodzących z jednego centrum zdarzeń i wysyłania wyników do innego centrum zdarzeń.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w *function.jsw* pliku i [funkcji skryptu języka C#](../articles/azure-functions/functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w *function.js* pliku. Pierwszy przykład dotyczy funkcji 2. x i wyższych, a drugi jest dla funkcji 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto kod skryptu w języku C#, który tworzy jeden komunikat:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Oto kod skryptu języka C#, który tworzy wiele komunikatów:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w *function.jsw* pliku oraz [funkcja języka JavaScript](../articles/azure-functions/functions-reference-node.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w *function.js* pliku. Pierwszy przykład dotyczy funkcji 2. x i wyższych, a drugi jest dla funkcji 1. x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Kod JavaScript, który wysyła pojedynczy komunikat:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Kod JavaScript, który wysyła wiele komunikatów:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w *function.jsw* pliku i [funkcji języka Python](../articles/azure-functions/functions-reference-python.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w *function.js* pliku.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto kod języka Python, który wysyła pojedynczy komunikat:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję języka Java, która zapisuje komunikat zawierający bieżący czas do centrum zdarzeń.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@EventHubOutput` adnotacji w parametrach, których wartość zostanie opublikowana w centrum zdarzeń.  Parametr powinien być typu `OutputBinding<T>` , gdzie T jest Pojo lub dowolnym natywnym typem języka Java.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W przypadku [bibliotek klas języka C#](../articles/azure-functions/functions-dotnet-class-library.md)należy użyć atrybutu [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę centrum zdarzeń i nazwę ustawienia aplikacji, które zawiera parametry połączenia. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Output-Configuration](#configuration). Oto `EventHub` przykład atrybutu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Aby zapoznać się z kompletnym przykładem, zobacz [dane wyjściowe — przykład w języku C#](#example).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) w przypadku parametrów, których wartość zostanie opublikowana w centrum zdarzeń. Parametr powinien być typu `OutputBinding<T>` , gdzie `T` jest Pojo lub dowolnym natywnym typem języka Java.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `EventHub` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Wartość musi być równa "eventHub". |
|**wskazywa** | n/d | Musi być ustawiona na wartość "out". Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje zdarzenie. |
|**ścieżka** |**EventHubName** | Tylko funkcje 1. x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**EventHubName** | Funkcje 2. x i nowsze. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia z przestrzenią nazw centrum zdarzeń. Skopiuj te parametry połączenia, klikając przycisk **Informacje o połączeniu** dla [obszaru nazw](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), a nie samego centrum zdarzeń. Te parametry połączenia muszą mieć uprawnienia do wysyłania, aby wysłać wiadomość do strumienia zdarzeń. <br><br>Jeśli używasz [wersji 5. x lub nowszej rozszerzenia](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)zamiast parametrów połączenia, możesz podać odwołanie do sekcji konfiguracji, która definiuje połączenie. Zobacz [połączenia](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Domyślne

Można użyć następujących typów parametrów dla powiązania danych wyjściowych centrum zdarzeń:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Domyślne właściwości EventData są podane w [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out string paramName` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. Aby napisać wiele komunikatów, można użyć `ICollector<string>` lub zamiast `IAsyncCollector<string>` `out string` .

### <a name="additional-types"></a>Dodatkowe typy 
Aplikacje korzystające z 5.0.0 lub nowszej wersji rozszerzenia centrum zdarzeń używają `EventData` typu na [platformie Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) zamiast w ramach [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Ta wersja porzuca obsługę starszego `Body` typu na korzyść następujących typów:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

### <a name="default"></a>Domyślne

Można użyć następujących typów parametrów dla powiązania danych wyjściowych centrum zdarzeń:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Domyślne właściwości EventData są podane w [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out string paramName` . W skrypcie języka C# `paramName` jest wartością określoną we `name` właściwości *function.jsna*. Aby napisać wiele komunikatów, można użyć `ICollector<string>` lub zamiast `IAsyncCollector<string>` `out string` .

### <a name="additional-types"></a>Dodatkowe typy 
Aplikacje korzystające z 5.0.0 lub nowszej wersji rozszerzenia centrum zdarzeń używają `EventData` typu na [platformie Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) zamiast w ramach [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Ta wersja porzuca obsługę starszego `Body` typu na korzyść następujących typów:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia wyjściowego przy użyciu metody `context.bindings.<name>` Where `<name>` jest wartością określoną we `name` właściwości *function.json*.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu centrum zdarzeń z funkcji:

- **Wartość zwracana**: Ustaw `name` Właściwość w *function.jsna* wartość `$return` . W przypadku tej konfiguracji wartość zwracana przez funkcję jest utrwalana jako komunikat centrum zdarzeń.

- Bezwzględnie **: Przekaż** wartość do metody [Set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) parametru zadeklarowanego jako typ [out](/python/api/azure-functions/azure.functions.out) . Wartość przeniesiona do `set` elementu jest utrwalana jako komunikat centrum zdarzeń.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania komunikatu centrum zdarzeń z funkcji przy użyciu adnotacji [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) :

- **Wartość zwracana**: przez zastosowanie adnotacji do samej funkcji, wartość zwracana funkcji jest utrwalana jako komunikat centrum zdarzeń.

- Bezwzględnie **: aby** jawnie ustawić wartość komunikatu, Zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) , gdzie `T` jest Pojo lub dowolny natywny typ Java. W przypadku tej konfiguracji przekazywanie wartości do `setValue` metody utrwala wartość jako komunikat centrum zdarzeń.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie | Odwołanie |
|---|---|
| Centrum zdarzeń | [Przewodnik obsługi](/rest/api/eventhub/publisher-policy-operations) |