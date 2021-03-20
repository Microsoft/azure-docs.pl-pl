---
title: Wyrażenia Azure Functions i wzorce powiązań
description: Dowiedz się, jak tworzyć różne wyrażenia powiązań Azure Functions na podstawie wspólnych wzorców.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 7245b0c0fb1e96959ef5dca4992cf52a38accb58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920294"
---
# <a name="azure-functions-binding-expression-patterns"></a>Wzorce wyrażeń powiązań Azure Functions

Jedną z najbardziej zaawansowanych funkcji [wyzwalaczy i powiązań](./functions-triggers-bindings.md) jest *wyrażenie wiążące*. W *function.jsna* pliku i w parametrach i kodzie funkcji można używać wyrażeń, które rozwiązują wartości z różnych źródeł.

Większość wyrażeń identyfikuje się przez umieszczenie ich w nawiasach klamrowych. Na przykład, w funkcji wyzwalacza kolejki, jest `{queueTrigger}` rozpoznawana jako tekst komunikatu w kolejce. Jeśli `path` Właściwość dla powiązania danych wyjściowych obiektu BLOB ma wartość, `container/{queueTrigger}` a funkcja jest wyzwalana przez komunikat w kolejce `HelloWorld` , tworzony jest obiekt BLOB o nazwie `HelloWorld` .

Typy wyrażeń powiązania

* [Ustawienia aplikacji](#binding-expressions---app-settings)
* [Nazwa pliku wyzwalacza](#trigger-file-name)
* [Metadane wyzwalacza](#trigger-metadata)
* [Ładunki JSON](#json-payloads)
* [Nowy identyfikator GUID](#create-guids)
* [Bieżąca data i godzina](#current-time)

## <a name="binding-expressions---app-settings"></a>Wyrażenia powiązań — ustawienia aplikacji

Najlepszym rozwiązaniem jest zarządzanie kluczami tajnymi i ciągami połączeń przy użyciu ustawień aplikacji, a nie plików konfiguracji. Pozwala to ograniczyć dostęp do tych kluczy tajnych i umożliwia bezpieczne przechowywanie plików, takich jak *function.jsna* publicznych repozytoriach kontroli źródła.

Ustawienia aplikacji są również przydatne, gdy chcesz zmienić konfigurację w oparciu o środowisko. Na przykład w środowisku testowym może być konieczne monitorowanie innej kolejki lub kontenera magazynu obiektów BLOB.

Wyrażenia powiązań ustawień aplikacji są identyfikowane inaczej od innych wyrażeń powiązania: są one opakowane w znaki procentowe, a nie nawiasy klamrowe. Na przykład jeśli ścieżka powiązania danych wyjściowych obiektu BLOB jest `%Environment%/newblob.txt` i `Environment` wartość ustawienia aplikacji to `Development` , obiekt BLOB zostanie utworzony w `Development` kontenerze.

Gdy funkcja działa lokalnie, wartości ustawień aplikacji pochodzą z *local.settings.js* pliku.

> [!NOTE]
> `connection`Właściwość wyzwalaczy i powiązań jest szczególnym przypadkiem i automatycznie rozpoznaje wartości jako ustawienia aplikacji bez znaków procentu. 

Poniższy przykład to wyzwalacz usługi Azure Queue Storage, który używa ustawienia aplikacji `%input_queue_name%` do zdefiniowania kolejki do wyzwalania.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input_queue_name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Możesz użyć tego samego podejścia w bibliotekach klas:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input_queue_name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Nazwa pliku wyzwalacza

`path`Wyzwalacz dla obiektu BLOB może być wzorcem, który umożliwia odwoływanie się do nazwy obiektu BLOB wyzwalającego w innych powiązaniach i kodzie funkcji. Wzorzec może również obejmować kryteria filtrowania, które określają, które obiekty blob mogą wyzwalać wywołanie funkcji.

Na przykład w poniższym powiązaniu wyzwalacza obiektu BLOB `path` wzorzec jest `sample-images/{filename}` , który tworzy wyrażenie powiązania o nazwie `filename` :

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

Wyrażenia `filename` można następnie użyć w powiązaniu danych wyjściowych, aby określić nazwę tworzonego obiektu BLOB:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Kod funkcji ma dostęp do tej samej wartości przy użyciu `filename` jako nazwa parametru:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Ta sama możliwość używania wyrażeń i wzorców powiązań ma zastosowanie do atrybutów w bibliotekach klas. W poniższym przykładzie parametry konstruktora atrybutów są te same `path` wartości jak w poprzednim *function.jsna* przykład: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Można również tworzyć wyrażenia dla części nazwy pliku. W poniższym przykładzie funkcja jest wyzwalana tylko w nazwach plików, które pasują do wzorca: `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Aby uzyskać więcej informacji na temat używania wyrażeń i wzorców w ciągu ścieżki obiektu BLOB, zobacz [odwołanie do powiązania obiektu blob magazynu](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadane wyzwalacza

Oprócz ładunku danych dostarczonego przez wyzwalacz (na przykład zawartości komunikatu kolejki, który wyzwolił funkcję), wiele wyzwalaczy zapewnia dodatkowe wartości metadanych. Te wartości mogą być używane jako parametry wejściowe w języku C# i F # lub we właściwościach `context.bindings` obiektu w języku JavaScript. 

Na przykład wyzwalacz usługi Azure queue storage obsługuje następujące właściwości:

* QueueTrigger — wyzwalanie zawartości komunikatu, jeśli prawidłowy ciąg
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Te wartości metadanych są dostępne w *function.jsna* właściwościach pliku. Załóżmy na przykład, że używasz wyzwalacza kolejki, a komunikat kolejki zawiera nazwę obiektu BLOB, który ma zostać odczytany. W *function.js* pliku można użyć `queueTrigger` Właściwości Metadata we właściwości obiektu BLOB `path` , jak pokazano w następującym przykładzie:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
}
```

Szczegóły właściwości metadanych każdego wyzwalacza są opisane w odpowiednim artykule referencyjnym. Aby zapoznać się z przykładem, zobacz [metadane wyzwalacza kolejki](functions-bindings-storage-queue-trigger.md#message-metadata). Dokumentacja jest również dostępna na karcie **integracja** w portalu, w sekcji **Dokumentacja** pod obszarem Konfiguracja powiązania.  

## <a name="json-payloads"></a>Ładunki JSON

Gdy ładunek wyzwalacza to JSON, można odwołać się do jego właściwości w konfiguracji dla innych powiązań w tej samej funkcji i w kodzie funkcji.

Poniższy przykład pokazuje *function.jsw* pliku dla funkcji webhook, która otrzymuje nazwę obiektu BLOB w formacie JSON: `{"BlobName":"HelloWorld.txt"}` . Powiązanie danych wejściowych obiektu BLOB odczytuje obiekt BLOB, a powiązanie danych wyjściowych HTTP zwraca zawartość obiektu BLOB w odpowiedzi HTTP. Zwróć uwagę, że powiązanie danych wejściowych obiektu BLOB Pobiera nazwę obiektu BLOB, odwołując się bezpośrednio do `BlobName` właściwości ( `"path": "strings/{BlobName}"` )

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Aby można było korzystać z języka C# i F #, potrzebna jest Klasa, która definiuje pola do deserializacji, jak w poniższym przykładzie:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

W języku JavaScript deserializacja JSON jest wykonywana automatycznie.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Notacja kropka

Jeśli niektóre właściwości w ładunku JSON są obiektami z właściwościami, można odwoływać się do nich bezpośrednio przy użyciu notacji kropkowej. Załóżmy na przykład, że kod JSON wygląda następująco:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Można odwołać się bezpośrednio do programu `FileName` `BlobName.FileName` . W tym formacie JSON `path` Właściwość w poprzednim przykładzie będzie wyglądać następująco:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

W języku C# potrzebne są dwie klasy:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Tworzenie identyfikatorów GUID

`{rand-guid}`Wyrażenie powiązania tworzy identyfikator GUID. Następująca ścieżka obiektu BLOB w `function.json` pliku tworzy obiekt BLOB o nazwie takiej jak *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Bieżący czas

Wyrażenie powiązania jest `DateTime` rozpoznawane jako `DateTime.UtcNow` . Następująca ścieżka obiektu BLOB w `function.json` pliku tworzy obiekt BLOB o nazwie takiej jak *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W języku C# i innych językach .NET można użyć wzorca powiązania bezwzględnego, w przeciwieństwie do deklaratywnych powiązań w *function.jsna* i atrybutów. Bezwzględne powiązanie jest przydatne, gdy parametry powiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Aby dowiedzieć się więcej, zobacz [informacje dotyczące deweloperów w języku c#](functions-dotnet-class-library.md#binding-at-runtime) lub [informacje dotyczące deweloperów skryptów języka c#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Korzystanie z wartości zwracanej przez funkcję platformy Azure](./functions-bindings-return-value.md)
