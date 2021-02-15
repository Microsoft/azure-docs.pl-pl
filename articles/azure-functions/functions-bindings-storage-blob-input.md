---
title: Powiązanie danych wejściowych magazynu obiektów blob platformy Azure dla Azure Functions
description: Dowiedz się, jak dostarczyć dane wejściowe powiązania usługi Azure Blob Storage do funkcji platformy Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 678b6f02245e463870fc5b2c4bfc8b5ffa50de60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381697"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Powiązanie danych wejściowych magazynu obiektów blob platformy Azure dla Azure Functions

Powiązanie danych wejściowych umożliwia odczytywanie danych usługi BLOB Storage jako danych wejściowych do funkcji platformy Azure.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-blob.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład to [funkcja języka C#](functions-dotnet-class-library.md) , która używa wyzwalacza kolejki i wejściowego powiązania obiektu BLOB. Komunikat kolejki zawiera nazwę obiektu BLOB, a funkcja rejestruje rozmiar obiektu BLOB.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w *function.jsw* kodzie pliku i [skrypcie C# (CSX)](functions-reference-csharp.md) , który używa powiązań. Funkcja tworzy kopię tekstu obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W *function.jsw* pliku `queueTrigger` Właściwość metadanych służy do określania nazwy obiektu BLOB we `path` właściwościach:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Ta sekcja zawiera następujące przykłady:

* [Wyzwalacz HTTP, wyszukiwanie nazwy obiektu BLOB na podstawie ciągu zapytania](#http-trigger-look-up-blob-name-from-query-string)
* [Wyzwalacz kolejki, Odbierz nazwę obiektu BLOB z komunikatu kolejki](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>Wyzwalacz HTTP, wyszukiwanie nazwy obiektu BLOB na podstawie ciągu zapytania

 W poniższym przykładzie pokazano funkcję języka Java, która używa `HttpTrigger` adnotacji do otrzymywania parametru zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. `BlobInput`Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]` .

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Wyzwalacz kolejki, Odbierz nazwę obiektu BLOB z komunikatu kolejki

 Poniższy przykład pokazuje funkcję języka Java, która używa `QueueTrigger` adnotacji do odbierania komunikatu zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. `BlobInput`Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]` .

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@BlobInput` adnotacji w parametrach, których wartość pochodzi z obiektu BLOB.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w *function.jsw* kodzie pliku i [JavaScript](functions-reference-node.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W *function.jsw* pliku `queueTrigger` Właściwość metadanych służy do określania nazwy obiektu BLOB we `path` właściwościach:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Poniższy przykład przedstawia powiązanie danych wejściowych obiektu BLOB zdefiniowane w _function.jsw_ pliku, co sprawia, że przychodzące dane obiektów BLOB są dostępne dla funkcji [programu PowerShell](functions-reference-powershell.md) .

Oto konfiguracja JSON:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Oto kod funkcji:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Poniższy przykład przedstawia powiązania danych wejściowych i wyjściowych obiektów BLOB w *function.jsw* kodzie pliku i języku [Python](functions-reference-python.md) , który używa powiązań. Funkcja tworzy kopię obiektu BLOB. Funkcja jest wyzwalana przez komunikat kolejki, który zawiera nazwę obiektu BLOB do skopiowania. Nowy obiekt BLOB ma nazwę *{originalblobname}-Copy*.

W *function.jsw* pliku `queueTrigger` Właściwość metadanych służy do określania nazwy obiektu BLOB we `path` właściwościach:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

`dataType`Właściwość określa, które powiązanie jest używane. Dostępne są następujące wartości, które obsługują różne strategie powiązań:

| Wartość powiązania | Domyślne | Opis | Przykład |
| --- | --- | --- | --- |
| `undefined` | Y | Używa rozbudowanego powiązania | `def main(input: func.InputStream)` |
| `string` | N | Używa powiązania ogólnego i rzutuje typ danych wejściowych na `string` | `def main(input: str)` |
| `binary` | N | Używa powiązania ogólnego i rzutuje wejściowego obiektu BLOB jako `bytes` obiekt Python | `def main(input: bytes)` |

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj [obiektu blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu BLOB i `FileAccess` parametr wskazujący odczyt lub zapis, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Możesz ustawić `Connection` Właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacz-atrybuty i adnotacje](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

Ten `@BlobInput` atrybut daje dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw wartość `dataType` `binary` . Aby uzyskać szczegółowe informacje, zobacz [przykład danych wejściowych](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Blob` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `blob` . |
|**wskazywa** | n/d | Musi być ustawiony na `in` . Wyjątki są zanotowane w sekcji [użycie](#usage) . |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji.|
|**ścieżka** |**Blobpath ścieżką** | Ścieżka do obiektu BLOB. |
|**połączenia** |**Połączenie**| Nazwa ustawienia aplikacji, które zawiera [Parametry połączenia magazynu](../storage/common/storage-configure-connection-string.md) , które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz opcję `connection` "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem magazynu obsługującym tylko obiekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Jeśli używasz [wersji 5. x lub nowszej rozszerzenia](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)zamiast parametrów połączenia, możesz podać odwołanie do sekcji konfiguracji, która definiuje połączenie. Zobacz [połączenia](./functions-reference.md#connections).|
|**dataType**| n/d | W przypadku języków z typem dynamicznym określa typ danych bazowych. Możliwe wartości to `string` , `binary` , lub `stream` . Aby uzyskać bardziej szczegółowe informacje, zobacz [pojęcia wyzwalacze i powiązania](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions). |
|n/d | **Dostęp** | Wskazuje, czy będą odczytywane i zapisywane. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

Ten `@BlobInput` atrybut daje dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw wartość `dataType` `binary` . Aby uzyskać szczegółowe informacje, zobacz [przykład danych wejściowych](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do danych obiektów BLOB przy użyciu `context.bindings.<NAME>` `<NAME>` wartości WHERE, która jest zgodna z wartością zdefiniowaną w *function.jsna*.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Dostęp do danych obiektu BLOB za pośrednictwem parametru, który odpowiada nazwie wskazanej przez parametr Nazwa powiązania w _function.jsw_ pliku.

# <a name="python"></a>[Python](#tab/python)

Dostęp do danych obiektów BLOB za pomocą parametru, który został określony jako [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python&preserve-view=true). Aby uzyskać szczegółowe informacje, zobacz [przykład danych wejściowych](#example) .

---

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję, gdy zmieniają się dane magazynu obiektów BLOB](./functions-bindings-storage-blob-trigger.md)
- [Zapisywanie danych usługi BLOB Storage z funkcji](./functions-bindings-storage-blob-output.md)
