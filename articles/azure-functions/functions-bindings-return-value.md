---
title: Używanie wartości zwracanej z funkcji platformy Azure
description: Dowiedz się, jak zarządzać zwracanymi wartościami dla Azure Functions
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 58ee9b682bc97dc4044d811392cf4ff5b51a69fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491362"
---
# <a name="using-the-azure-function-return-value"></a>Korzystanie z wartości zwracanej przez funkcję platformy Azure

W tym artykule opisano sposób działania zwracanych wartości wewnątrz funkcji.

W językach, które mają wartość zwracaną, można powiązać [powiązanie danych wyjściowych](./functions-triggers-bindings.md#binding-direction) funkcji z wartością zwracaną:

* W bibliotece klas języka C# zastosuj atrybut wynik powiązania do wartości zwracanej przez metodę.
* W języku Java Zastosuj adnotację powiązania danych wyjściowych do metody Function.
* W innych językach Ustaw `name` Właściwość w *function.jsna* `$return` .

Jeśli istnieje wiele powiązań wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

W skrypcie języka C# i C# alternatywne sposoby wysyłania danych do powiązania wyjściowego to `out` Parametry i [obiekty modułów zbierających](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C#](#tab/csharp)

Oto kod języka C#, który używa wartości zwracanej dla powiązania danych wyjściowych, po którym następuje asynchroniczny przykład:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Oto powiązanie danych wyjściowych w *function.js* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto kod skryptu C#, po którym następuje asynchroniczny przykład:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F#](#tab/fsharp)

Oto powiązanie danych wyjściowych w *function.js* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto kod F #:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Oto powiązanie danych wyjściowych w *function.js* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

W języku JavaScript wartość zwracana jest w drugim parametrze dla `context.done` :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```
# <a name="powershell"></a>[Program PowerShell](#tab/PowerShell)

Oto powiązanie danych wyjściowych w *function.js* pliku:

```json
{
    "name": "Response",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{blobname}"
}
```

Oto kod programu PowerShell, który używa wartości zwracanej dla powiązania wyjściowego http:

```powershell
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $blobname
    })
```

# <a name="python"></a>[Python](#tab/python)

Oto powiązanie danych wyjściowych w *function.js* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Oto kod języka Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Tutaj znajduje się kod Java, który używa wartości zwracanej dla powiązania danych wyjściowych:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługa błędów powiązań Azure Functions](./functions-bindings-errors.md)
