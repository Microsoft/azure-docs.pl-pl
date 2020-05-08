---
title: Powiązanie danych wyjściowych usługi Azure queue storage dla Azure Functions
description: Informacje na temat tworzenia komunikatów usługi Azure queue storage w Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: dd8442c00e2b7685b0dc1a7bd5150c87f2c27b7c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891450"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Powiązania wyjściowe usługi Azure queue storage dla Azure Functions

Azure Functions można utworzyć nowe komunikaty usługi Azure queue storage przez skonfigurowanie powiązania danych wyjściowych.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-queue.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[S #](#tab/csharp)

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

Poniższy przykład przedstawia powiązanie wyzwalacza HTTP w pliku *Function. JSON* i kodzie [skryptu C# (. CSX)](functions-reference-csharp.md) , który używa powiązania. Funkcja tworzy element kolejki z ładunkiem obiektu **CustomQueueMessage** dla każdego odebranego żądania HTTP.

Oto plik *Function. JSON* :

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

Jednocześnie można wysłać wiele komunikatów przy użyciu parametru `ICollector` lub. `IAsyncCollector` Oto kod skryptu w języku C#, który wysyła wiele komunikatów, jeden z danymi żądania HTTP i jedną z ustalonymi wartościami:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza HTTP w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja tworzy element kolejki dla każdego odebranego żądania HTTP.

Oto plik *Function. JSON* :

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

Można wysłać wiele komunikatów jednocześnie, definiując tablicę komunikatów dla powiązania `myQueueItem` danych wyjściowych. Poniższy kod JavaScript wysyła dwie komunikaty kolejki z ustalonymi wartościami dla każdego odebranego żądania HTTP.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób wyprowadzania pojedynczych i wielu wartości do kolejek magazynu. Konfiguracja wymagana dla *funkcji Function. JSON* jest taka sama jak w obu kierunkach.

Powiązanie kolejki magazynu jest zdefiniowane w *funkcji Function. JSON* , w której *Typ* jest `queue`ustawiony na.

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

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@QueueOutput` adnotacji w parametrach, których wartość zostanie zapisywana w usłudze queue storage.  Typ parametru powinien mieć `OutputBinding<T>`wartość, gdzie `T` jest dowolnym natywnym typem języka Java Pojo.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[S #](#tab/csharp)

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

Możesz ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

`QueueOutput` Adnotacja pozwala pisać komunikat jako dane wyjściowe funkcji. Poniższy przykład pokazuje funkcję wyzwalaną przez protokół HTTP, która tworzy komunikat w kolejce.

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

Parametr skojarzony z `QueueOutput` adnotacją jest wpisywany jako wystąpienie elementu [wyjściowegobinding\> \<T](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) .

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i w `Queue` atrybucie.

|Function. JSON — Właściwość | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `queue`. Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiony na `out`. Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. Ustaw, `$return` aby odwoływać się do zwracanej wartości funkcji.|
|**Zmienną QueueName** |**Zmienną QueueName** | Nazwa kolejki. |
|**połączenia** | **Połączenia** |Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz `connection` opcję "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "WebStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[S #](#tab/csharp)

Napisz pojedynczy komunikat w kolejce przy użyciu parametru metody, takiego jak `out T paramName`. Można użyć typu zwracanego metody zamiast `out` parametru i `T` może być dowolny z następujących typów:

* Obiekt możliwy do serializacji jako plik JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

W skrypcie C# i C# Napisz wiele komunikatów w kolejce przy użyciu jednego z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Napisz pojedynczy komunikat w kolejce przy użyciu parametru metody, takiego jak `out T paramName`. `paramName` Jest wartością określoną we `name` właściwości *Function. JSON*. Można użyć typu zwracanego metody zamiast `out` parametru i `T` może być dowolny z następujących typów:

* Obiekt możliwy do serializacji jako plik JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

W skrypcie C# i C# Napisz wiele komunikatów w kolejce przy użyciu jednego z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Element kolejki wyjściowej jest dostępny za `context.bindings.<NAME>` pośrednictwem WHERE, gdzie `<NAME>` pasuje do nazwy zdefiniowanej w *funkcji Function. JSON*. Dla ładunku elementu kolejki można użyć ciągu lub obiektu możliwego do serializacji w formacie JSON.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu centrum zdarzeń z funkcji:

- **Wartość zwracana**: Ustaw `name` właściwość w *funkcji Function. JSON* na `$return`. W przypadku tej konfiguracji wartość zwracana przez funkcję jest utrwalana jako komunikat magazynu kolejki.

- Bezwzględnie **: Przekaż**wartość do metody [Set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru zadeklarowanego jako typ [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Wartość przeniesiona do `set` jest utrwalana jako komunikat magazynu kolejki.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania komunikatu centrum zdarzeń z funkcji przy użyciu adnotacji [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Wartość zwracana**: przez zastosowanie adnotacji do samej funkcji, wartość zwracana funkcji jest utrwalana jako komunikat centrum zdarzeń.

- Bezwzględnie **: aby**jawnie ustawić wartość komunikatu, Zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), gdzie `T` jest Pojo lub dowolny natywny typ Java. W przypadku tej konfiguracji przekazywanie wartości do `setValue` metody utrwala wartość jako komunikat centrum zdarzeń.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie |  Dokumentacja |
|---|---|
| Kolejka | [Kody błędów kolejki](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia pliku host. JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Poniższy przykładowy plik host. JSON zawiera tylko ustawienia wersji 2. x dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersjach 2. x i więcej, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host. JSON w funkcjach 1. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions 1. x](functions-host-json-v1.md).

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

|Właściwość  |Domyślny | Opis |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maksymalny interwał między sondami kolejki. Wartość minimalna to 00:00:00.100 (100 ms) i zwiększa się do 00:01:00 (1 min).  W 1. x typ danych to milisekundy, a w 2. x i wyższych jest to TimeSpan.|
|visibilityTimeout|00:00:00|Przedział czasu między ponownymi próbami, gdy przetwarzanie komunikatu kończy się niepowodzeniem. |
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe funkcji jednocześnie pobiera i przetwarza równolegle. Gdy przetwarzana liczba jest w dół `newBatchThreshold`, środowisko uruchomieniowe pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. Dlatego Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję to `batchSize` Plus. `newBatchThreshold` Ten limit dotyczy osobno każdej funkcji wyzwalanej przez kolejkę. <br><br>Aby zapobiec równoległemu wykonywaniu komunikatów odebranych w jednej kolejce, można ustawić wartość `batchSize` 1. To ustawienie eliminuje jednak współbieżność tylko wtedy, gdy aplikacja funkcji działa na jednej maszynie wirtualnej. Jeśli aplikacja funkcji jest skalowana do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Wartość maksymalna `batchSize` to 32. |
|maxDequeueCount|5|Liczba prób przetworzenia komunikatu przed przeniesieniem go do kolejki trującej.|
|newBatchThreshold|batchSize/2|Gdy liczba przetwarzanych komunikatów współbieżnie przyjdzie do tego numeru, środowisko uruchomieniowe pobiera kolejną partię.|

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji jako kolejki zmian danych magazynu (wyzwalacz)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
