---
title: Azure Service Bus powiązania wyjściowe dla Azure Functions
description: Dowiedz się, jak wysyłać komunikaty Azure Service Bus z Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 12e57361b9e275fc441df27a3a1381989d48751c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788574"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus powiązanie danych wyjściowych dla Azure Functions

Użyj Azure Service Bus powiązania danych wyjściowych do wysyłania komunikatów kolejki lub tematu.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-service-bus.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która wysyła komunikat Service Bus kolejki:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia Service Bus powiązanie danych wyjściowych w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja używa wyzwalacza czasomierza do wysyłania komunikatu kolejki co 15 sekund.

Oto dane powiązania w *function.js* pliku:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Oto kod skryptu języka C#, który tworzy pojedynczy komunikat:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Oto kod skryptu języka C#, który tworzy wiele komunikatów:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję języka Java, która wysyła komunikat do kolejki Service Bus, `myqueue` gdy wyzwalane przez żądanie HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@QueueOutput` adnotacji w parametrach funkcji, których wartość zostałaby zapisywana w kolejce Service Bus.  Typem parametru powinien być `OutputBinding<T>` , gdzie T jest dowolnym natywnym typem języka Java Pojo.

Funkcje języka Java mogą również zapisywać w temacie Service Bus. Poniższy przykład używa `@ServiceBusTopicOutput` adnotacji do opisywania konfiguracji dla powiązania danych wyjściowych. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia Service Bus powiązanie danych wyjściowych w *function.js* pliku i [funkcji języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja używa wyzwalacza czasomierza do wysyłania komunikatu kolejki co 15 sekund.

Oto dane powiązania w *function.js* pliku:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Oto kod skryptu JavaScript, który tworzy pojedynczy komunikat:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Oto kod skryptu JavaScript, który tworzy wiele komunikatów:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład przedstawia Service Bus powiązanie danych wyjściowych w *function.js* pliku oraz [funkcję programu PowerShell](functions-reference-powershell.md) , która używa powiązania. 

Oto dane powiązania w *function.js* pliku:

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Oto środowisko programu PowerShell, które tworzy komunikat jako dane wyjściowe funkcji.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób zapisywania do kolejki Service Bus w języku Python.

Definicja powiązania Service Bus jest definiowana w *function.jsw* przypadku, gdy *Typ* jest ustawiony na `serviceBus` .

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atrybutu przyjmuje nazwę kolejki lub temat i subskrypcję. Możesz również określić prawa dostępu do połączenia. Sposób wybierania ustawienia praw dostępu jest objaśniony w sekcji [Konfiguracja wyjściowa](#configuration) . Oto przykład, który pokazuje atrybut zastosowany do wartości zwracanej przez funkcję:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Można ustawić `Connection` Właściwość, aby określić nazwę ustawienia aplikacji, która zawiera Service Bus parametry połączenia do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [Output-example](#example).

Możesz użyć `ServiceBusAccount` atrybutu, aby określić konto Service Bus do użycia na poziomie klasy, metody lub parametru.  Aby uzyskać więcej informacji, zobacz [wyzwalacz-atrybuty](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput` `ServiceBusTopicOutput` Adnotacje i są dostępne do pisania wiadomości jako dane wyjściowe funkcji. Parametr z tymi adnotacjami musi być zadeklarowany jako, `OutputBinding<T>` gdzie `T` jest typem odpowiadającym typowi komunikatu.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `ServiceBus` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiona na wartość "serviceBus". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiona na wartość "out". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje komunikat kolejki lub tematu w kodzie funkcji. Ustaw wartość "$return", aby odwołać się do zwracanej wartości funkcji. |
|**Zmienną QueueName**|**Zmienną QueueName**|Nazwa kolejki.  Ustawiaj tylko w przypadku wysyłania komunikatów w kolejce, a nie dla tematu.
|**temat**|**Temat**|Nazwa tematu. Ustawiaj tylko w przypadku wysyłania komunikatów tematu, a nie dla kolejki.|
|**połączenia**|**Połączenie**|Nazwa ustawienia aplikacji, która zawiera Service Bus parametry połączenia do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko resztę nazwy. Jeśli na przykład ustawisz wartość `connection` "MyServiceBus", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyServiceBus". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia Service Bus w ustawieniu aplikacji o nazwie "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj kroki opisane w sekcji [pobieranie poświadczeń zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą należeć do Service Bus przestrzeni nazw, a nie ograniczone do określonej kolejki lub tematu.|
|**accessRights** (tylko wersja 1)|**Dostęp**|Prawa dostępu do parametrów połączenia. Dostępne wartości to `manage` i `listen` . Wartość domyślna to `manage` , co oznacza, że `connection` ma uprawnienie do **zarządzania** . W przypadku używania parametrów połączenia, które nie mają uprawnienia do **zarządzania** , ustaw wartość `accessRights` "nasłuchuj". W przeciwnym razie środowisko uruchomieniowe funkcji może zakończyć się niepowodzeniem podczas próby wykonania operacji, które wymagają uprawnień do zarządzania. W Azure Functions w wersji 2. x lub nowszej ta właściwość nie jest dostępna, ponieważ Najnowsza wersja zestawu Service Bus SDK nie obsługuje operacji zarządzania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

W Azure Functions 1. x środowisko uruchomieniowe tworzy kolejkę, jeśli nie istnieje i ustawiono `accessRights` `manage` . W funkcjach w wersji 2. x i nowszych Kolejka lub temat muszą już istnieć; w przypadku określenia kolejki lub tematu, który nie istnieje, funkcja zakończy się niepowodzeniem. 

# <a name="c"></a>[C#](#tab/csharp)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `out T paramName` - `T` może być dowolnym typem możliwym do serializacji JSON. Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja tworzy komunikat z obiektem o wartości null.
* `out string` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out byte[]` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out BrokeredMessage` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 1. x)
* `out Message` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 2. x i wyższych)
* `ICollector<T>` lub `IAsyncCollector<T>` (dla metod asynchronicznych) — do tworzenia wielu komunikatów. Tworzony jest komunikat podczas wywoływania `Add` metody.

Podczas pracy z funkcjami języka C#:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

* Aby uzyskać dostęp do identyfikatora sesji, powiąż z [`Message`](/dotnet/api/microsoft.azure.servicebus.message) typem i Użyj `sessionId` właściwości.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `out T paramName` - `T` może być dowolnym typem możliwym do serializacji JSON. Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja tworzy komunikat z obiektem o wartości null.
* `out string` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out byte[]` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out BrokeredMessage` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 1. x)
* `out Message` -Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 2. x i wyższych)
* `ICollector<T>` lub `IAsyncCollector<T>` — w celu utworzenia wielu komunikatów. Tworzony jest komunikat podczas wywoływania `Add` metody.

Podczas pracy z funkcjami języka C#:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast `out` parametru.

* Aby uzyskać dostęp do identyfikatora sesji, powiąż z [`Message`](/dotnet/api/microsoft.azure.servicebus.message) typem i Użyj `sessionId` właściwości.

# <a name="java"></a>[Java](#tab/java)

Użyj [zestawu SDK Azure Service Bus](../service-bus-messaging/index.yml) , a nie wbudowanego powiązania danych wyjściowych.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do kolejki lub tematu za pomocą programu `context.bindings.<name from function.json>` . Do programu można przypisać ciąg, tablicę bajtową lub obiekt JavaScript (deserializowany w formacie JSON) `context.binding.<name>` .

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Dane wyjściowe do Service Bus są dostępne za pośrednictwem `Push-OutputBinding` polecenia cmdlet, w którym przekazywane są argumenty pasujące do nazwy wskazanej przez parametr Nazwa powiązania w *function.js* pliku.

# <a name="python"></a>[Python](#tab/python)

Użyj [zestawu SDK Azure Service Bus](../service-bus-messaging/index.yml) , a nie wbudowanego powiązania danych wyjściowych.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie | Dokumentacja |
|---|---|
| Service Bus | [Service Bus kody błędów](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Limity Service Bus](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsustawień

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Przykład host.jsw pliku poniżej zawiera tylko ustawienia dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej, zobacz [host.json Reference for Azure Functions Version](functions-host-json.md).

> [!NOTE]
> Aby uzyskać informacje na temat host.jsw funkcjach 1. x, zobacz [host.json Reference for Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

Jeśli `isSessionsEnabled` ustawiono `true` opcję, `sessionHandlerOptions` zostanie uznane.  Jeśli `isSessionsEnabled` ustawiono `false` opcję, `messageHandlerOptions` zostanie uznane.

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|prefetchCount|0|Pobiera lub ustawia liczbę komunikatów, które może jednocześnie wysłać odbiorca wiadomości.|
|maxAutoRenewDuration|00:05:00|Maksymalny czas, w którym Blokada wiadomości zostanie odnowiona automatycznie.|
|Wskazówk|true|Określa, czy wyzwalacz ma automatycznie wywoływać zakończenie po przetworzeniu, czy też kod funkcji zostanie wykonany ręcznie.<br><br>Ustawienie `false` jest obsługiwane tylko w języku C#.<br><br>Jeśli jest ustawiona na `true` , wyzwalacz kończy komunikat automatycznie, jeśli wykonanie funkcji zakończy się pomyślnie i porzuca komunikat w przeciwnym razie.<br><br>Po ustawieniu na `false` , użytkownik jest odpowiedzialny za wywoływanie metod [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet&preserve-view=true) w celu ukończenia, porzucenia lub utraconia wiadomości. Jeśli wyjątek jest zgłaszany (i żadna z `MessageReceiver` metod nie jest wywoływana), blokada pozostaje. Po wygaśnięciu blokady wiadomość zostanie ponownie umieszczona w kolejce z `DeliveryCount` przyrostem, a blokada zostanie automatycznie odnowiona.<br><br>W przypadku funkcji innych niż języka C wyjątki w funkcji powodują wywołania środowiska uruchomieniowego `abandonAsync` w tle. Jeśli żaden wyjątek nie wystąpi, wówczas `completeAsync` jest wywoływana w tle. |
|maxConcurrentCalls|16|Maksymalna liczba jednoczesnych wywołań wywołania zwrotnego, które pompa komunikatów powinna inicjować na wystąpienie skalowane. Domyślnie środowisko uruchomieniowe funkcji przetwarza wiele komunikatów jednocześnie.|
|maxConcurrentSessions|2000|Maksymalna liczba sesji, które mogą być obsłużone współbieżnie na wystąpienie skalowane.|

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję po utworzeniu kolejki Service Bus lub komunikatu tematu (Wyzwól)](./functions-bindings-service-bus-trigger.md)
