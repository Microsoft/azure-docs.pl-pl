---
title: RabbitMQ powiązania wyjściowe dla Azure Functions
description: Dowiedz się, jak wysyłać komunikaty RabbitMQ z Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 1664656f82492e664b7574339893cd688f0a061d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097317"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>RabbitMQ wyjściowe powiązania dla Azure Functions przegląd

> [!NOTE]
> Powiązania RabbitMQ są w pełni obsługiwane tylko w planach **Premium i dedykowanych** . Użycie nie jest obsługiwane.

Użyj powiązania danych wyjściowych RabbitMQ, aby wysyłać komunikaty do kolejki RabbitMQ.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która wysyła komunikat RabbitMQ, gdy jest wyzwalany przez TimerTrigger co 5 minut przy użyciu wartości zwracanej przez metodę jako dane wyjściowe:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
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
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

Poniższy przykład pokazuje, jak wysyłać komunikaty jako POCOs.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
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
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Oto kod skryptu w języku C#:

```C#
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
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
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
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

W *_\_ init_ \_ . PR*:

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

Następująca funkcja języka Java używa `@RabbitMQOutput` adnotacji z [typów RabbitMQ języka Java](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) , aby opisać konfigurację dla powiązania danych wyjściowych w kolejce RabbitMQ. Funkcja wysyła komunikat do kolejki RabbitMQ, gdy jest wyzwalany przez TimerTrigger co 5 minut.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
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

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `RabbitMQ` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiona na wartość "RabbitMQ".|
|**wskazywa** | n/d | Musi być ustawiona na wartość "out". |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. |
|**Zmienną QueueName**|**Zmienną QueueName**| Nazwa kolejki, do której mają być wysyłane wiadomości. |
|**Nazw**|**Nazw**|(ignorowane, jeśli jest używany ConnectStringSetting) <br>Nazwa hosta kolejki (np.: 10.26.45.210)|
|**Uż**|**Uż**|(ignorowane, jeśli jest używany ConnectionStringSetting) <br>Nazwa ustawienia aplikacji, która zawiera nazwę użytkownika, aby uzyskać dostęp do kolejki. Np. UserNameSetting: "< UserNameFromSettings >"|
|**hasło**|**Password** (Hasło)|(ignorowane, jeśli jest używany ConnectionStringSetting) <br>Nazwa ustawienia aplikacji, która zawiera hasło umożliwiające dostęp do kolejki. Np. UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|Nazwa ustawienia aplikacji, które zawiera parametry połączenia kolejki komunikatów RabbitMQ. Należy pamiętać, że w przypadku określenia parametrów połączenia bezpośrednio, a nie za pomocą ustawienia aplikacji w local.settings.jsna, wyzwalacz nie będzie działał. (Np.: w *function.jsna*: connectionStringSetting: "rabbitMQConnection" <br> W *local.settings.jsna*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**przewożąc**|**Port**|(ignorowane, jeśli jest używany ConnectionStringSetting) Pobiera lub ustawia używany port. Wartość domyślna to 0, która wskazuje domyślne ustawienie portu klienta RabbitMQ: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

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
* `POCO` -Jeśli wartość parametru nie jest sformatowana jako obiekt języka C#, zostanie wyświetlony błąd. Aby zapoznać się z kompletnym przykładem, zobacz [przykład](#example)skryptu C#.

Podczas pracy z funkcjami skryptu języka C#:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Komunikat kolejki jest dostępny za pośrednictwem kontekstu. powiązania.<NAME> gdzie <NAME> pasuje do nazwy zdefiniowanej w function.jsna. Jeśli ładunek jest w formacie JSON, wartość jest deserializowana do obiektu.

# <a name="python"></a>[Python](#tab/python)

Zapoznaj się z [przykładem](#example)w języku Python.

# <a name="java"></a>[Java](#tab/java)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `byte[]` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `string` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `POJO` — Jeśli wartość parametru nie jest sformatowana jako obiekt języka Java, zostanie wyświetlony błąd.

---

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję po utworzeniu komunikatu RabbitMQ (Wyzwól)](./functions-bindings-rabbitmq-trigger.md)
