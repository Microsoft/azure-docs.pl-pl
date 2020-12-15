---
title: RabbitMQ powiązania wyjściowe dla Azure Functions
description: Dowiedz się, jak wysyłać komunikaty RabbitMQ z Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/13/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 212bfcee09cd63b6ff09faaba4d99e4b4c583fe8
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505776"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>RabbitMQ wyjściowe powiązania dla Azure Functions przegląd

> [!NOTE]
> Powiązania RabbitMQ są w pełni obsługiwane tylko w planach **systemu Windows Premium** . Użycie i system Linux nie są obecnie obsługiwane.

Użyj powiązania danych wyjściowych RabbitMQ, aby wysyłać komunikaty do kolejki RabbitMQ.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która wysyła komunikat RabbitMQ, gdy jest wyzwalany przez TimerTrigger co 5 minut przy użyciu wartości zwracanej przez metodę jako dane wyjściowe:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ("outputQueue", ConnectionStringSetting = "ConnectionStringSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Poniższy przykład pokazuje, jak używać interfejsu IAsyncCollector do wysyłania komunikatów.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    // processing:
    var myProcessedEvent = DoSomething(rabbitMQEvent);
    
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
}
```

Poniższy przykład pokazuje, jak wysyłać komunikaty jako POCOs.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "TriggerConnectionString")] TestClass rabbitMQEvent,
[RabbitMQ("destinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<TestClass> outputPocObj,
ILogger log)
{
    // send the message
    await outputPocObj.Add(rabbitMQEvent);
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie danych wyjściowych RabbitMQ w *function.jsna* pliku i [funkcję skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja odczytuje komunikat z wyzwalacza HTTP i wyprowadza go do kolejki RabbitMQ.

Oto dane powiązania w *function.js* pliku:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

Oto kod skryptu w języku C#:

```csx
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie danych wyjściowych RabbitMQ w *function.jsw* pliku i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja odczytuje komunikat z wyzwalacza HTTP i wyprowadza go do kolejki RabbitMQ.

Oto dane powiązania w *function.js* pliku:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

Kod JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie danych wyjściowych RabbitMQ w *function.jsw* pliku i funkcję języka Python, która używa powiązania. Funkcja odczytuje komunikat z wyzwalacza HTTP i wyprowadza go do kolejki RabbitMQ.

Oto dane powiązania w *function.js* pliku:

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "http",
            "direction": "out",
            "name": "$return"
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "connectionStringAppSetting",
            "direction": "out"
        }
    ]
}
```

W *_\_ init_ \_ . PR* można napisać komunikat do kolejki, przekazując wartość do `set` metody.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    msg.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję języka Java, która wysyła komunikat do kolejki RabbitMQ po wyzwoleniu przez TimerTrigger co 5 minut.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQ", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

Oto `RabbitMQAttribute` atrybut w sygnaturze metody:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Aby zapoznać się z kompletnym przykładem, zobacz [przykład](#example)w języku C#.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

`RabbitMQOutput`Adnotacja pozwala utworzyć funkcję, która jest uruchamiana podczas wysyłania komunikatu RabbitMQ. Dostępne opcje konfiguracji obejmują nazwę kolejki i nazwę parametrów połączenia. Aby uzyskać dodatkowe informacje o parametrach, odwiedź [Adnotacje języka Java RabbitMQOutput](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

Zobacz [przykład](#example) powiązania danych wyjściowych, aby uzyskać więcej szczegółów.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `RabbitMQ` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiona na wartość "RabbitMQ".|
|**wskazywa** | n/d | Musi być ustawiona na wartość "out". |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. |
|**Zmienną QueueName**|**Zmienną QueueName**| Nazwa kolejki, do której mają być wysyłane wiadomości. |
|**Nazw**|**Nazw**|(opcjonalnie, jeśli jest używany ConnectStringSetting) <br>Nazwa hosta kolejki (np.: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(opcjonalnie, jeśli jest używany ConnectionStringSetting) <br>Nazwa dostępu do kolejki |
|**passwordSetting**|**PasswordSetting**|(opcjonalnie, jeśli jest używany ConnectionStringSetting) <br>Hasło dostępu do kolejki|
|**connectionStringSetting**|**ConnectionStringSetting**|Nazwa ustawienia aplikacji, które zawiera parametry połączenia kolejki komunikatów RabbitMQ. Należy pamiętać, że w przypadku określenia parametrów połączenia bezpośrednio, a nie za pomocą ustawienia aplikacji w local.settings.jsna, wyzwalacz nie będzie działał. (Np.: w *function.jsna*: connectionStringSetting: "rabbitMQConnection" <br> W *local.settings.jsna*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**przewożąc**|**Port**|Pobiera lub ustawia używany port. Wartość domyślna to 0.|

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `byte[]` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `string` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `POCO` -Jeśli wartość parametru nie jest sformatowana jako obiekt języka C#, zostanie wyświetlony błąd. Aby zapoznać się z kompletnym przykładem, zobacz [przykład](#example)w języku C#.

Podczas pracy z funkcjami języka C#:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `byte[]` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `string` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `POCO` -Jeśli wartość parametru nie jest sformatowana jako obiekt języka C#, zostanie wyświetlony błąd.

Podczas pracy z funkcjami skryptu języka C#:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Komunikat RabbitMQ jest wysyłany przez ciąg.

# <a name="python"></a>[Python](#tab/python)

Komunikat RabbitMQ jest wysyłany przez ciąg.

# <a name="java"></a>[Java](#tab/java)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `byte[]` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `string` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `POJO` — Jeśli wartość parametru nie jest sformatowana jako obiekt języka Java, zostanie wyświetlony błąd.

---

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję po utworzeniu komunikatu RabbitMQ (Wyzwól)](./functions-bindings-rabbitmq-trigger.md)
