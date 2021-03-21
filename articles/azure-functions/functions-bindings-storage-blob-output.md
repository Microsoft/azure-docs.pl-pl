---
title: Powiązanie danych wyjściowych usługi Azure Blob Storage dla Azure Functions
description: Dowiedz się, jak udostępniać dane wyjściowe powiązań usługi Azure Blob Storage do funkcji platformy Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: eaa8a4c600864f636d49813d415621d46130fff7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381663"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych usługi Azure Blob Storage dla Azure Functions

Powiązanie danych wyjściowych pozwala modyfikować i usuwać dane usługi BLOB Storage w funkcji platformy Azure.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-blob.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład to [funkcja języka C#](functions-dotnet-class-library.md) , która używa wyzwalacza obiektu BLOB i dwóch powiązań wyjściowych obiektów BLOB. Funkcja jest wyzwalana przez utworzenie obiektu BLOB obrazu w kontenerze *przykładowych obrazów* . Tworzy on małe i średnie kopie obiektu BLOB obrazu.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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

* [Wyzwalacz http, przy użyciubinding](#http-trigger-using-outputbinding-java)
* [Wyzwalacz kolejki, używanie wartości zwracanej funkcji](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Wyzwalacz http przy użyciubinding (Java)

 W poniższym przykładzie pokazano funkcję języka Java, która używa `HttpTrigger` adnotacji do otrzymywania parametru zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. `BlobInput`Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]` . `BlobOutput`Adnotacja jest powiązana z `OutputBinding outputItem` , który jest następnie używany przez funkcję do zapisywania zawartości wejściowego obiektu BLOB w skonfigurowanym kontenerze magazynu.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Wyzwalacz kolejki, używanie zwracanej wartości funkcji (Java)

 Poniższy przykład pokazuje funkcję języka Java, która używa `QueueTrigger` adnotacji do odbierania komunikatu zawierającego nazwę pliku w kontenerze magazynu obiektów BLOB. `BlobInput`Adnotacja następnie odczytuje plik i przekazuje jego zawartość do funkcji jako `byte[]` . `BlobOutput`Adnotacja jest powiązana z wartością zwracaną funkcji, która następnie jest używana przez środowisko uruchomieniowe do zapisywania zawartości wejściowego obiektu BLOB w skonfigurowanym kontenerze magazynu.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@BlobOutput` adnotacji w parametrach funkcji, których wartość zostałaby zapisywana w obiekcie w magazynie obiektów BLOB.  Typem parametru powinien być `OutputBinding<T>` , gdzie T jest dowolnym natywnym typem Java lub Pojo.

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

W poniższym przykładzie pokazano, jak utworzyć kopię przychodzącego obiektu BLOB jako dane wyjściowe z [funkcji programu PowerShell](functions-reference-powershell.md).

W pliku konfiguracji funkcji (*function.json*) `trigger` Właściwość metadanych służy do określania wyjściowej nazwy obiektu BLOB we `path` właściwościach.

> [!NOTE]
> Aby uniknąć nieskończonych pętli, upewnij się, że ścieżki wejściowe i wyjściowe są różne.

```json
{
  "bindings": [
    {
      "name": "myInputBlob",
      "path": "data/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in",
      "type": "blobTrigger"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "data/copy/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Oto kod programu PowerShell:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $myInputBlob, $TriggerMetadata)
Write-Host "PowerShell Blob trigger function Processed blob Name: $($TriggerMetadata.Name)"
Push-OutputBinding -Name myOutputBlob -Value $myInputBlob
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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj [obiektu blobattribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Konstruktor atrybutu przyjmuje ścieżkę do obiektu BLOB i `FileAccess` parametr wskazujący odczyt lub zapis, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Możesz ustawić `Connection` Właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="java"></a>[Java](#tab/java)

Ten `@BlobOutput` atrybut daje dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw wartość `dataType` `binary` . Aby uzyskać szczegółowe informacje, zobacz [przykład danych wyjściowych](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Atrybuty nie są obsługiwane przez program PowerShell.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

---

Aby uzyskać pełny przykład, zobacz [przykład danych wyjściowych](#example).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacz-atrybuty](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Blob` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `blob` . |
|**wskazywa** | n/d | Musi być ustawiona na `out` dla powiązania danych wyjściowych. Wyjątki są zanotowane w sekcji [użycie](#usage) . |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji.  Ustaw, aby `$return` odwoływać się do zwracanej wartości funkcji.|
|**ścieżka** |**Blobpath ścieżką** | Ścieżka do kontenera obiektów BLOB. |
|**połączenia** |**Połączenie**| Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz opcję `connection` "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem magazynu obsługującym tylko obiekty blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Jeśli używasz [wersji 5. x lub nowszej rozszerzenia](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)zamiast parametrów połączenia, możesz podać odwołanie do sekcji konfiguracji, która definiuje połączenie. Zobacz [połączenia](./functions-reference.md#connections).|
|n/d | **Dostęp** | Wskazuje, czy będą odczytywane i zapisywane. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="java"></a>[Java](#tab/java)

Ten `@BlobOutput` atrybut daje dostęp do obiektu BLOB, który wyzwolił funkcję. Jeśli używasz tablicy bajtowej z atrybutem, ustaw wartość `dataType` `binary` . Aby uzyskać szczegółowe informacje, zobacz [przykład danych wyjściowych](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Uzyskaj dostęp do danych obiektu BLOB przy użyciu `context.bindings.<BINDING_NAME>` , gdzie nazwa powiązania jest zdefiniowana w _function.jsna_ pliku.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Dostęp do danych obiektu BLOB za pośrednictwem parametru, który odpowiada nazwie wskazanej przez parametr Nazwa powiązania w _function.jsw_ pliku.

# <a name="python"></a>[Python](#tab/python)

Parametry funkcji można zadeklarować jako następujące typy do zapisu w magazynie obiektów blob:

* Ciągi jako `func.Out(str)`
* Strumienie jako `func.Out(func.InputStream)`

Aby uzyskać szczegółowe informacje, zobacz [przykład danych wyjściowych](#example) .

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotu

| Wiązanie |  Dokumentacja |
|---|---|
| Obiekt blob | [Kody błędów obiektów BLOB](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Kody błędów magazynu](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Rozwiązywanie problemów](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję, gdy zmieniają się dane magazynu obiektów BLOB](./functions-bindings-storage-blob-trigger.md)
- [Odczytaj dane magazynu obiektów blob, gdy działa funkcja](./functions-bindings-storage-blob-input.md)
