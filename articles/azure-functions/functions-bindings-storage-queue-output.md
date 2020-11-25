---
title: Powiązanie danych wyjściowych usługi Azure queue storage dla Azure Functions
description: Informacje na temat tworzenia komunikatów usługi Azure queue storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 087073437fe9d6159422799c04ce095c0aae5eca
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96001256"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Powiązania wyjściowe usługi Azure queue storage dla Azure Functions

Azure Functions można utworzyć nowe komunikaty usługi Azure queue storage przez skonfigurowanie powiązania danych wyjściowych.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-queue.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która tworzy komunikat w kolejce dla każdego odebranego żądania HTTP.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza HTTP w *function.jsw* kodzie pliku i [skrypcie C# (CSX)](functions-reference-csharp.md) , który używa powiązania. Funkcja tworzy element kolejki z ładunkiem obiektu **CustomQueueMessage** dla każdego odebranego żądania HTTP.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#, który tworzy komunikat o pojedynczej kolejce:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Jednocześnie można wysłać wiele komunikatów przy użyciu `ICollector` `IAsyncCollector` parametru lub. Oto kod skryptu w języku C#, który wysyła wiele komunikatów, jeden z danymi żądania HTTP i jedną z ustalonymi wartościami:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 Poniższy przykład pokazuje funkcję języka Java, która tworzy komunikat w kolejce dla wyzwalane przez żądanie HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@QueueOutput` adnotacji w parametrach, których wartość zostanie zapisywana w usłudze queue storage.  Typ parametru powinien mieć wartość `OutputBinding<T>` , gdzie `T` jest dowolnym natywnym typem języka Java Pojo.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład pokazuje powiązanie wyzwalacza HTTP w *function.jsw* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja tworzy element kolejki dla każdego odebranego żądania HTTP.

Oto *function.js* pliku:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Można wysłać wiele komunikatów jednocześnie, definiując tablicę komunikatów dla `myQueueItem` powiązania danych wyjściowych. Poniższy kod JavaScript wysyła dwie komunikaty kolejki z ustalonymi wartościami dla każdego odebranego żądania HTTP.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład kodu przedstawia sposób wyprowadzania komunikatu kolejki z funkcji wyzwalanej przez protokół HTTP. Sekcja konfiguracji z programem `type` `queue` definiuje powiązanie danych wyjściowych.

```json
{
  "bindings": [
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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Przy użyciu tej konfiguracji powiązań funkcja programu PowerShell może utworzyć komunikat w kolejce przy użyciu `Push-OutputBinding` . W tym przykładzie komunikat jest tworzony na podstawie ciągu zapytania lub parametru Body.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Aby wysłać wiele komunikatów jednocześnie, zdefiniuj tablicę komunikatów i Użyj `Push-OutputBinding` do wysyłania komunikatów do powiązania danych wyjściowych kolejki.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób wyprowadzania pojedynczych i wielu wartości do kolejek magazynu. Konfiguracja wymagana dla *function.js* jest taka sama w obu przypadkach.

Powiązanie kolejki magazynu jest zdefiniowane w *function.jsna* miejscu, w którym ustawiono *Typ* `queue` .

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Aby ustawić pojedynczy komunikat w kolejce, należy przekazać jedną wartość do `set` metody.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Aby utworzyć wiele komunikatów w kolejce, zadeklaruj parametr jako odpowiedni typ listy i przekaż tablicę wartości (które pasują do typu listy) do `set` metody.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), użyj klasy [queueattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Ten atrybut ma zastosowanie do `out` parametru lub wartości zwracanej funkcji. Konstruktor atrybutu przyjmuje nazwę kolejki, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Możesz ustawić `Connection` Właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [przykład danych wyjściowych](#example).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz wyzwalacz-atrybuty.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

`QueueOutput`Adnotacja pozwala pisać komunikat jako dane wyjściowe funkcji. Poniższy przykład pokazuje funkcję wyzwalaną przez protokół HTTP, która tworzy komunikat w kolejce.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Właściwość    | Opis |
|-------------|-----------------------------|
|`name`       | Deklaruje nazwę parametru w podpisie funkcji. Gdy funkcja jest wyzwalana, wartość tego parametru ma zawartość komunikatu w kolejce. |
|`queueName`  | Deklaruje nazwę kolejki na koncie magazynu. |
|`connection` | Wskazuje na parametry połączenia konta magazynu. |

Parametr skojarzony z `QueueOutput` adnotacją jest typem wystąpienia elementu [wyjściowegobinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Queue` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `queue` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiony na `out` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. Ustaw, aby `$return` odwoływać się do zwracanej wartości funkcji.|
|**Zmienną QueueName** |**Zmienną QueueName** | Nazwa kolejki. |
|**połączenia** | **Połączenie** |Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz opcję `connection` "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "WebStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Napisz pojedynczy komunikat w kolejce przy użyciu parametru metody, takiego jak `out T paramName` . Można użyć typu zwracanego metody zamiast `out` parametru i `T` może być dowolny z następujących typów:

* Obiekt możliwy do serializacji jako plik JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

W skrypcie C# i C# Napisz wiele komunikatów w kolejce przy użyciu jednego z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Napisz pojedynczy komunikat w kolejce przy użyciu parametru metody, takiego jak `out T paramName` . `paramName`Jest wartością określoną we `name` właściwości *function.jsna*. Można użyć typu zwracanego metody zamiast `out` parametru i `T` może być dowolny z następujących typów:

* Obiekt możliwy do serializacji jako plik JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

W skrypcie C# i C# Napisz wiele komunikatów w kolejce przy użyciu jednego z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania komunikatu kolejki z funkcji przy użyciu adnotacji [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Wartość zwracana**: przez zastosowanie adnotacji do samej funkcji, wartość zwracana funkcji jest utrwalana jako komunikat w kolejce.

- Bezwzględnie **: aby** jawnie ustawić wartość komunikatu, Zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , gdzie `T` jest Pojo lub dowolny natywny typ Java. W przypadku tej konfiguracji przekazywanie wartości do `setValue` metody utrwala wartość jako komunikat w kolejce.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Element kolejki wyjściowej jest dostępny za pośrednictwem `context.bindings.<NAME>` Where `<NAME>` , gdzie pasuje do nazwy zdefiniowanej w *function.jsna*. Dla ładunku elementu kolejki można użyć ciągu lub obiektu możliwego do serializacji w formacie JSON.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Dane wyjściowe do komunikatu kolejki są dostępne za pośrednictwem `Push-OutputBinding` miejsca, w którym przekazywane są argumenty, które pasują do nazwy wskazanej przez `name` parametr powiązania w *function.jsna* pliku.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu kolejki z funkcji:

- **Wartość zwracana**: Ustaw `name` Właściwość w *function.jsna* wartość `$return` . W przypadku tej konfiguracji wartość zwracana przez funkcję jest utrwalana jako komunikat magazynu kolejki.

- Bezwzględnie **: Przekaż** wartość do metody [Set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) parametru zadeklarowanego jako typ [out](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) . Wartość przeniesiona do `set` jest utrwalana jako komunikat magazynu kolejki.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie |  Odwołanie |
|---|---|
| Kolejka | [Kody błędów kolejki](/rest/api/storageservices/queue-service-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Kody błędów magazynu](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Rozwiązywanie problemów](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsustawień

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Przykład host.jsna poniższym pliku zawiera tylko ustawienia wersji 2. x dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersjach 2. x i więcej, zobacz [host.json Reference for Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać informacje na temat host.jsw funkcjach 1. x, zobacz [host.json Reference for Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maksymalny interwał między sondami kolejki. Wartość minimalna to 00:00:00.100 (100 ms) i zwiększa się do 00:01:00 (1 min).  W 1. x typ danych to milisekundy, a w 2. x i wyższych jest to TimeSpan.|
|visibilityTimeout|00:00:00|Przedział czasu między ponownymi próbami, gdy przetwarzanie komunikatu kończy się niepowodzeniem. |
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe funkcji jednocześnie pobiera i przetwarza równolegle. Gdy przetwarzana liczba jest w dół `newBatchThreshold` , środowisko uruchomieniowe pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. Dlatego Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję to `batchSize` Plus `newBatchThreshold` . Ten limit dotyczy osobno każdej funkcji wyzwalanej przez kolejkę. <br><br>Aby zapobiec równoległemu wykonywaniu komunikatów odebranych w jednej kolejce, można ustawić wartość `batchSize` 1. To ustawienie eliminuje jednak współbieżność tylko wtedy, gdy aplikacja funkcji działa na jednej maszynie wirtualnej. Jeśli aplikacja funkcji jest skalowana do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Wartość maksymalna `batchSize` to 32. |
|maxDequeueCount|5|Liczba prób przetworzenia komunikatu przed przeniesieniem go do kolejki trującej.|
|newBatchThreshold|batchSize/2|Gdy liczba przetwarzanych komunikatów współbieżnie przyjdzie do tego numeru, środowisko uruchomieniowe pobiera kolejną partię.|

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji jako kolejki zmian danych magazynu (wyzwalacz)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
