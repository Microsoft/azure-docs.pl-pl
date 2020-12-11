---
title: Wyzwalacz usługi Azure Blob Storage dla Azure Functions
description: Dowiedz się, jak uruchamiać funkcję platformy Azure jako zmiany danych usługi Azure Blob Storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: fd33ca4c5d637e31230d8c124fdb9ec7c71d2ba7
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094849"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure Blob Storage dla Azure Functions

Wyzwalacz magazynu obiektów BLOB uruchamia funkcję po wykryciu nowego lub zaktualizowanego obiektu BLOB. Zawartość obiektu BLOB jest dostarczana jako [dane wejściowe do funkcji](./functions-bindings-storage-blob-input.md).

Wyzwalacz usługi Azure Blob Storage wymaga konta magazynu ogólnego przeznaczenia. Obsługiwane są również konta magazynu w wersji 2 z [przestrzeniami nazw hierarchiczna](../storage/blobs/data-lake-storage-namespace.md) . Aby użyć konta typu obiekt BLOB lub jeśli aplikacja ma specjalne potrzeby, przejrzyj alternatywy do użycia tego wyzwalacza.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](./functions-bindings-storage-blob.md).

## <a name="polling"></a>Sondowanie

Sondowanie działa jako hybrydowe między inspekcjami dzienników i uruchamiania okresowych skanowania kontenerów. Obiekty blob są skanowane w grupach 10 000 na raz z tokenem kontynuacji używanym między interwałami.

> [!WARNING]
> Ponadto [dzienniki magazynu są tworzone na podstawie najlepszego nakładu pracy](/rest/api/storageservices/About-Storage-Analytics-Logging) . Nie ma gwarancji, że wszystkie zdarzenia są przechwytywane. W pewnych warunkach dzienniki mogą być pominięte.
> 
> Jeśli potrzebujesz szybszego lub bardziej niezawodnego przetwarzania obiektów blob, rozważ utworzenie [komunikatu w kolejce](../storage/queues/storage-dotnet-how-to-use-queues.md) podczas tworzenia obiektu BLOB. Następnie przetworzyć obiekt BLOB przy użyciu [wyzwalacza kolejki](functions-bindings-storage-queue.md) zamiast wyzwalacza obiektu BLOB. Innym rozwiązaniem jest użycie Event Grid; Zapoznaj się z samouczkiem [Automatyzowanie zmiany rozmiarów przekazanych obrazów przy użyciu Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="alternatives"></a>Alternatywy

### <a name="event-grid-trigger"></a>Wyzwalacz Event Grid

[Wyzwalacz Event Grid](functions-bindings-event-grid.md) również ma wbudowaną obsługę [zdarzeń obiektów BLOB](../storage/blobs/storage-blob-event-overview.md). Użyj Event Grid zamiast wyzwalacza usługi BLOB Storage dla następujących scenariuszy:

- **Konta magazynu tylko dla obiektów BLOB**: [konta magazynu](../storage/common/storage-account-overview.md#types-of-storage-accounts) obsługujące obiekty blob są obsługiwane przez powiązania danych wejściowych i wyjściowych obiektów blob, ale nie dla wyzwalaczy obiektów BLOB.

- **Wysoka Skala**: wysoka Skala można luźno definiować jako kontenery, które mają więcej niż 100 000 obiektów BLOB w ramach konta magazynu, które mają więcej niż 100 aktualizacji obiektów BLOB na sekundę.

- **Minimalizacja opóźnień**: Jeśli aplikacja funkcji jest w planie zużycia, może wystąpić maksymalnie 10 minut opóźnienia w przetwarzaniu nowych obiektów blob, jeśli aplikacja funkcji przestała być w stanie bezczynności. Aby uniknąć tego opóźnienia, możesz przejść do planu App Service z opcją zawsze włączone. Możesz również użyć [wyzwalacza Event Grid](functions-bindings-event-grid.md) z kontem magazynu obiektów BLOB. Aby zapoznać się z przykładem, zobacz [samouczek Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Zapoznaj się z przykładem [zmiany rozmiaru obrazu za pomocą Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) samouczka Event Grid.

### <a name="queue-storage-trigger"></a>Wyzwalacz magazynu kolejek

Innym podejściem do przetwarzania obiektów BLOB jest zapisanie komunikatów w kolejce, które odpowiadają tworzonym lub modyfikowanym obiektom BLOB, a następnie do rozpoczęcia przetwarzania przy użyciu [wyzwalacza magazynu kolejki](./functions-bindings-storage-queue.md) .

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `samples-workitems` kontenerze.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat `BlobTrigger` atrybutu, zobacz [atrybuty i adnotacje](#attributes-and-annotations).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza obiektu BLOB w *function.jsw* pliku i kodzie, który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `samples-workitems` [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Oto dane powiązania w *function.js* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji *na tematfunction.js* właściwości pliku, zobacz sekcję [Konfiguracja](#configuration) objaśnia te właściwości.

Oto kod skryptu w języku C#, który jest powiązany z `Stream` :

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Oto kod skryptu w języku C#, który jest powiązany z `CloudBlockBlob` :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie wyzwalacza obiektu BLOB w *function.jsw* kodzie pliku i [JavaScript](functions-reference-node.md) , który używa powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `samples-workitems` kontenerze.

Oto *function.js* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji *na tematfunction.js* właściwości pliku, zobacz sekcję [Konfiguracja](#configuration) objaśnia te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie wyzwalacza obiektu BLOB w *function.jsw* kodzie pliku i języku [Python](functions-reference-python.md) , który używa tego powiązania. Funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `samples-workitems` [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Oto *function.js* pliku:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Ciąg `{name}` w ścieżce wyzwalacza obiektu BLOB `samples-workitems/{name}` tworzy [wyrażenie powiązania](./functions-bindings-expressions-patterns.md) , którego można użyć w kodzie funkcji w celu uzyskania dostępu do nazwy pliku wyzwalanego obiektu BLOB. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów BLOB](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji *na tematfunction.js* właściwości pliku, zobacz sekcję [Konfiguracja](#configuration) objaśnia te właściwości.

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Ta funkcja zapisuje dziennik w przypadku dodania lub zaktualizowania obiektu BLOB w `myblob` kontenerze.

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować wyzwalacz obiektu BLOB:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje ciąg ścieżki wskazujący, że kontener ma być obserwowany i opcjonalnie [wzorzec nazwy obiektu BLOB](#blob-name-patterns). Oto przykład:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Możesz ustawić `Connection` Właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Aby zapoznać się z kompletnym przykładem, zobacz [przykład wyzwalacza](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Zapewnia inny sposób określania konta magazynu, które ma być używane. Konstruktor przyjmuje nazwę ustawienia aplikacji, które zawiera parametry połączenia magazynu. Ten atrybut może być stosowany na poziomie parametru, metody lub klasy. W poniższym przykładzie przedstawiono poziom klasy i poziom metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Konto magazynu do użycia jest określane w następującej kolejności:

* `BlobTrigger` `Connection` Właściwość atrybutu.
* `StorageAccount`Atrybut zastosowany do tego samego parametru, który jest `BlobTrigger` atrybutem.
* `StorageAccount`Atrybut zastosowany do funkcji.
* `StorageAccount`Atrybut zastosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji "AzureWebJobsStorage").

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

Ten `@BlobTrigger` atrybut służy do zapewnienia dostępu do obiektu BLOB, który wyzwolił funkcję. Zapoznaj się z [przykładem wyzwalacza](#example) , aby uzyskać szczegółowe informacje.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `BlobTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `blobTrigger` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiony na `in` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. Wyjątki są zanotowane w sekcji [użycie](#usage) . |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje obiekt BLOB w kodzie funkcji. |
|**ścieżka** | **Blobpath ścieżką** |[Kontener](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) do monitorowania.  Może być [wzorcem nazw obiektów BLOB](#blob-name-patterns). |
|**połączenia** | **Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz opcję `connection` "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .<br><br>Parametry połączenia muszą być kontem magazynu ogólnego przeznaczenia, a nie [kontem usługi BLOB Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do danych obiektów BLOB przy użyciu `context.bindings.<NAME>` `<NAME>` wartości WHERE, która jest zgodna z wartością zdefiniowaną w *function.jsna*.

# <a name="python"></a>[Python](#tab/python)

Dostęp do danych obiektów BLOB za pomocą parametru, który został określony jako [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Zapoznaj się z [przykładem wyzwalacza](#example) , aby uzyskać szczegółowe informacje.

# <a name="java"></a>[Java](#tab/java)

Ten `@BlobTrigger` atrybut służy do zapewnienia dostępu do obiektu BLOB, który wyzwolił funkcję. Zapoznaj się z [przykładem wyzwalacza](#example) , aby uzyskać szczegółowe informacje.

---

## <a name="blob-name-patterns"></a>Wzorce nazw obiektów BLOB

Można określić wzorzec nazwy obiektu BLOB we `path` właściwości w *function.jsna* lub w `BlobTrigger` konstruktorze atrybutu. Wzorzec nazwy może być [wyrażeniem filtru lub powiązania](./functions-bindings-expressions-patterns.md). W poniższych sekcjach przedstawiono przykłady.

> [!TIP]
> Nazwa kontenera nie może zawierać mechanizmu rozwiązywania konfliktów we wzorcu nazwy.

### <a name="get-file-name-and-extension"></a>Pobierz nazwę i rozszerzenie pliku

Poniższy przykład pokazuje, jak powiązać z nazwą pliku i rozszerzeniem obiektu BLOB osobno:

```json
"path": "input/{blobname}.{blobextension}",
```

Jeśli obiekt BLOB ma nazwę *original-Blob1.txt*, wartości `blobname` `blobextension` zmiennych i w kodzie funkcji są *oryginalne-Blob1* i *txt*.

### <a name="filter-on-blob-name"></a>Filtruj według nazwy obiektu BLOB

Poniższy przykład wyzwala wyzwalacze tylko w obiektach Blob w `input` kontenerze, które zaczynają się od ciągu "Original-":

```json
"path": "input/original-{name}",
```

Jeśli nazwa obiektu BLOB jest *original-Blob1.txt*, wartość `name` zmiennej w kodzie funkcji to `Blob1.txt` .

### <a name="filter-on-file-type"></a>Filtruj według typu pliku

Poniższy przykład wyzwala tylko na plikach *PNG* :

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtruj dla nawiasów klamrowych w nazwach plików

Aby wyszukać nawiasy klamrowe w nazwach plików, należy wprowadzić nawiasy klamrowe przy użyciu dwóch nawiasów klamrowych. Poniższe przykładowe filtry dla obiektów blob, które mają nawiasy klamrowe w nazwie:

```json
"path": "images/{{20140101}}-{name}",
```

Jeśli obiekt BLOB ma nazwę *{20140101}-soundfile.mp3*, `name` wartość zmiennej w kodzie funkcji jest *soundfile.mp3*.

## <a name="metadata"></a>Metadane

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Metadane nie są dostępne w języku Python.

# <a name="java"></a>[Java](#tab/java)

Metadane nie są dostępne w języku Java.

---

## <a name="blob-receipts"></a>Potwierdzenia obiektów BLOB

Środowisko uruchomieniowe Azure Functions zapewnia, że żadna funkcja wyzwalacza obiektu BLOB nie zostanie wywołana więcej niż raz dla tego samego nowego lub zaktualizowanego obiektu BLOB. Aby określić, czy dana wersja obiektu BLOB została przetworzona, przechowuje *potwierdzenia obiektów BLOB*.

Azure Functions przechowuje potwierdzenia obiektów BLOB w kontenerze o nazwie *Azure-WebJobs-hosty* na koncie usługi Azure Storage dla aplikacji funkcji (zdefiniowanej przez ustawienie aplikacji `AzureWebJobsStorage` ). Potwierdzenie obiektu BLOB zawiera następujące informacje:

* Funkcja wyzwalana ("*&lt; Nazwa aplikacji funkcji>*. Obowiązki. *&lt; nazwa funkcji>*", na przykład:" MyFunctionApp. Functions. CopyBlob ")
* Nazwa kontenera
* Typ obiektu BLOB ("BlockBlob" lub "PageBlob")
* Nazwa obiektu BLOB
* Element ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

Aby wymusić ponowne przetwarzanie obiektu BLOB, Usuń potwierdzenie obiektu BLOB dla tego obiektu BLOB z kontenera *usługi Azure-WebJobs-hosts* ręcznie. Proces ponownego przetwarzania może nie nastąpić natychmiast, dlatego ma miejsce w późniejszym czasie. Aby natychmiastowo przetworzyć obiekt BLOB *scaninfo* na *platformie Azure — można zaktualizować hosty/blobscaninfo* . Wszystkie obiekty blob z ostatnio modyfikowaną sygnaturą czasową po `LatestScan` właściwości zostaną ponownie przeskanowane.

## <a name="poison-blobs"></a>Trujące obiekty blob

Gdy funkcja wyzwalacza obiektu BLOB kończy się niepowodzeniem dla danego obiektu BLOB, Azure Functions ponownych prób, które domyślnie działają łącznie 5 razy.

W przypadku niepowodzenia wszystkich 5 prób Azure Functions dodaje komunikat do kolejki magazynu o nazwie *WebJobs-blobtrigger-trujące*. Maksymalna liczba ponownych prób można skonfigurować. To samo ustawienie MaxDequeueCount jest używane na potrzeby obsługi skażonych obiektów blob i komunikatów trującej kolejki. Komunikat w kolejce dla trujących obiektów BLOB jest obiektem JSON, który zawiera następujące właściwości:

* FunctionId (w formacie *&lt; Nazwa aplikacji funkcji>*. Obowiązki. *&lt; nazwa funkcji>*)
* Blobtype ("BlockBlob" lub "PageBlob")
* NazwaKontenera
* BlobName
* ETag (identyfikator wersji obiektu BLOB, na przykład: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Użycie współbieżności i pamięci

Wyzwalacz obiektów BLOB używa kolejki wewnętrznie, więc Maksymalna liczba współbieżnych wywołań funkcji jest kontrolowana przez [konfigurację kolejek w host.jsna](functions-host-json.md#queues). Ustawienia domyślne ograniczają współbieżność do 24 wywołań. Ten limit dotyczy osobno każdej funkcji, która używa wyzwalacza obiektu BLOB.

[Plan zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work) ogranicza aplikację funkcji na jednej maszynie wirtualnej (VM) do 1,5 GB pamięci. Pamięć jest używana przez wszystkie jednocześnie wykonywane wystąpienia funkcji i przez sam czas wykonywania funkcji. Jeśli funkcja wyzwalana przez obiekt BLOB ładuje cały obiekt BLOB do pamięci, maksymalna ilość pamięci używana przez tę funkcję tylko dla obiektów BLOB to 24 * maksymalny rozmiar obiektu BLOB. Na przykład aplikacja funkcji mająca trzy funkcje wyzwalane przez obiekt BLOB i ustawienia domyślne byłyby w maksymalnym zakresie współbieżności maszyny wirtualnej równym 3 * 24 = 72 wywołań funkcji.

Funkcje języka JavaScript i języka Java ładują cały obiekt BLOB do pamięci, a funkcje języka C# to w przypadku powiązania z `string` , lub `Byte[]` .

## <a name="next-steps"></a>Następne kroki

- [Odczytaj dane magazynu obiektów blob, gdy działa funkcja](./functions-bindings-storage-blob-input.md)
- [Zapisywanie danych usługi BLOB Storage z funkcji](./functions-bindings-storage-blob-output.md)
