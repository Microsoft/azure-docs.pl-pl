---
title: Wyzwalacz magazynu obiektów Blob platformy Azure dla usług Azure Functions
description: Dowiedz się, jak uruchomić funkcję platformy Azure w miarę wprowadzania zmian danych magazynu obiektów blob platformy Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 61fbaf37577efdab0b147d437ae78fc4df0764cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084961"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Wyzwalacz magazynu obiektów Blob platformy Azure dla usług Azure Functions

Wyzwalacz magazynu obiektów Blob uruchamia funkcję po wykryciu nowego lub zaktualizowanego obiektu blob. Zawartość obiektu blob są dostarczane jako [dane wejściowe do funkcji](./functions-bindings-storage-blob-input.md).

Wyzwalacz magazynu obiektów Blob platformy Azure wymaga konta magazynu ogólnego przeznaczenia. Aby użyć konta tylko do obiektów blob lub jeśli aplikacja ma specjalistyczne potrzeby, przejrzyj alternatywy dla korzystania z tego wyzwalacza.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-storage-blob.md).

## <a name="alternatives"></a>Alternatywy

### <a name="event-grid-trigger"></a>Wyzwalacz siatki zdarzeń

[Wyzwalacz siatki zdarzeń](functions-bindings-event-grid.md) ma również wbudowaną obsługę [zdarzeń obiektów blob.](../storage/blobs/storage-blob-event-overview.md) Użyj siatki zdarzeń zamiast wyzwalacza magazynu obiektów blob dla następujących scenariuszy:

- **Konta magazynu tylko dla obiektów blob:** konta magazynu tylko dla obiektów [blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) są obsługiwane dla powiązań wejściowych i wyjściowych obiektów blob, ale nie dla wyzwalaczy obiektów blob.

- **Duża skala:** Skala może być luźno zdefiniowana jako kontenery, które mają więcej niż 100 000 obiektów blob w nich lub kont magazynu, które mają więcej niż 100 aktualizacji obiektów blob na sekundę.

- **Minimalizowanie opóźnień:** Jeśli aplikacja funkcji znajduje się w planie zużycia, może wystąpić maksymalnie 10-minutowe opóźnienie w przetwarzaniu nowych obiektów blob, jeśli aplikacja funkcji została bezczynna. Aby uniknąć tego opóźnienia, można przełączyć się do planu usługi aplikacji z włączoną funkcją Zawsze włączone. Można również użyć [wyzwalacza siatki zdarzeń](functions-bindings-event-grid.md) z kontem magazynu obiektów Blob. Na przykład zobacz [samouczek siatki zdarzeń](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Zobacz [obraz zmienić rozmiar za pomocą siatki zdarzeń](../event-grid/resize-images-on-storage-blob-upload-event.md) samouczka siatki zdarzeń przykład siatki zdarzeń.

### <a name="queue-storage-trigger"></a>Wyzwalacz magazynu kolejek

Innym podejściem do przetwarzania obiektów blob jest pisanie komunikatów kolejki, które odpowiadają obiektom blob tworzone lub modyfikowane, a następnie użyć [wyzwalacza magazynu kolejki,](./functions-bindings-storage-queue.md) aby rozpocząć przetwarzanie.

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która zapisuje `samples-workitems` dziennik, gdy obiekt blob jest dodawany lub aktualizowany w kontenerze.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu blob tworzy wyrażenie [wiązania,](./functions-bindings-expressions-patterns.md) którego można użyć w kodzie funkcji, aby uzyskać dostęp do nazwy pliku wyzwalającego obiektu blob. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów blob](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej `BlobTrigger` informacji na temat atrybutu, zobacz [atrybuty i adnotacje](#attributes-and-annotations).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza obiektu blob w pliku *function.json* i kod, który używa powiązania. Funkcja zapisuje dziennik, gdy obiekt blob jest `samples-workitems` dodawany lub aktualizowany w [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Oto dane powiązania w pliku *function.json:*

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

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu blob tworzy wyrażenie [wiązania,](./functions-bindings-expressions-patterns.md) którego można użyć w kodzie funkcji, aby uzyskać dostęp do nazwy pliku wyzwalającego obiektu blob. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów blob](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *function.json,* zobacz [configuration](#configuration) sekcji opisano te właściwości.

Oto kod skryptu C#, który `Stream`wiąże się z:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Oto kod skryptu C#, który `CloudBlockBlob`wiąże się z:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza obiektu blob w pliku *function.json* i [kod JavaScript,](functions-reference-node.md) który używa powiązania. Funkcja zapisuje dziennik, gdy obiekt blob jest `samples-workitems` dodawany lub aktualizowany w kontenerze.

Oto plik *function.json:*

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

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu blob tworzy wyrażenie [wiązania,](./functions-bindings-expressions-patterns.md) którego można użyć w kodzie funkcji, aby uzyskać dostęp do nazwy pliku wyzwalającego obiektu blob. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów blob](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *function.json,* zobacz [configuration](#configuration) sekcji opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza obiektu blob w pliku *function.json* i [kod python,](functions-reference-python.md) który używa powiązania. Funkcja zapisuje dziennik, gdy obiekt blob jest `samples-workitems` dodawany lub aktualizowany w [kontenerze](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Oto plik *function.json:*

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

Ciąg `{name}` w ścieżce `samples-workitems/{name}` wyzwalacza obiektu blob tworzy wyrażenie [wiązania,](./functions-bindings-expressions-patterns.md) którego można użyć w kodzie funkcji, aby uzyskać dostęp do nazwy pliku wyzwalającego obiektu blob. Aby uzyskać więcej informacji, zobacz [wzorce nazw obiektów blob](#blob-name-patterns) w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat właściwości pliku *function.json,* zobacz [configuration](#configuration) sekcji opisano te właściwości.

Oto kod Pythona:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Ta funkcja zapisuje dziennik, gdy obiekt blob `myblob` jest dodawany lub aktualizowany w kontenerze.

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

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj następujących atrybutów, aby skonfigurować wyzwalacz obiektu blob:

* [Atrybut BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje ciąg ścieżki, który wskazuje kontener do obejrzenia i opcjonalnie [wzorzec nazwy obiektu blob](#blob-name-patterns). Oto przykład:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Można ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w poniższym przykładzie:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Pełny przykład można znaleźć w [przykładzie wyzwalania](#example).

* [Atrybut StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Umożliwia inny sposób określania konta magazynu, którego chcesz użyć. Konstruktor przyjmuje nazwę ustawienia aplikacji, który zawiera ciąg połączenia magazynu. Atrybut można zastosować na poziomie parametru, metody lub klasy. Poniższy przykład przedstawia poziom klasy i poziom metody:

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

* Właściwość `BlobTrigger` atrybutu. `Connection`
* Atrybut `StorageAccount` zastosowany do tego samego `BlobTrigger` parametru co atrybut.
* Atrybut `StorageAccount` zastosowany do funkcji.
* Atrybut `StorageAccount` stosowany do klasy.
* Domyślne konto magazynu dla aplikacji funkcji (ustawienie aplikacji AzureWebJobsStorage").

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Atrybut `@BlobTrigger` jest używany do udzielenia dostępu do obiektu blob, który wyzwolił funkcję. Szczegółowe informacje można znaleźć w [przykładzie wyzwalacza.](#example)

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `BlobTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `blobTrigger`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być `in`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. Wyjątki są odnotowane w sekcji [użycia.](#usage) |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej obiekt blob w kodzie funkcji. |
|**Ścieżka** | **Ścieżka obiektu BlobPath** |[Kontener](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) do monitorowania.  Może to być [wzorzec nazwy obiektu blob](#blob-name-patterns). |
|**Połączenia** | **Połączenia** | Nazwa ustawienia aplikacji, która zawiera ciąg połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy tutaj. Na przykład jeśli `connection` ustawisz "MyStorage", funkcja środowiska wykonawczego szuka ustawienia aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko wykonawcze Functions używa domyślnego ciągu połączenia `AzureWebJobsStorage`Magazyn w ustawieniu aplikacji o nazwie .<br><br>Parametry połączenia muszą być dla konta magazynu ogólnego przeznaczenia, a nie [konta magazynu obiektów Blob](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do danych `context.bindings.<NAME>` `<NAME>` obiektów blob przy użyciu miejsca, w którym jest zgodny z wartością zdefiniowaną w *pliku function.json*.

# <a name="python"></a>[Python](#tab/python)

Dostęp do danych obiektów blob za pośrednictwem parametru wpisanego jako [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python). Szczegółowe informacje można znaleźć w [przykładzie wyzwalacza.](#example)

# <a name="java"></a>[Java](#tab/java)

Atrybut `@BlobTrigger` jest używany do udzielenia dostępu do obiektu blob, który wyzwolił funkcję. Szczegółowe informacje można znaleźć w [przykładzie wyzwalacza.](#example)

---

## <a name="blob-name-patterns"></a>Wzorce nazw obiektów blob

Można określić wzorzec nazwy `path` obiektu blob we właściwości `BlobTrigger` w *function.json* lub w konstruktorze atrybutów. Wzorzec nazwy może być [wyrażeniem filtru lub powiązania](./functions-bindings-expressions-patterns.md). W poniższych sekcjach podano przykłady.

### <a name="get-file-name-and-extension"></a>Pobierz nazwę pliku i rozszerzenie

W poniższym przykładzie pokazano, jak powiązanie z nazwą pliku obiektu blob i rozszerzenie oddzielnie:

```json
"path": "input/{blobname}.{blobextension}",
```

Jeśli obiekt blob nosi nazwę *original-Blob1.txt,* wartości `blobname` zmiennych `blobextension` i zmiennych w kodzie funkcji są *oryginalne-Blob1* i *txt*.

### <a name="filter-on-blob-name"></a>Filtrowanie nazwy obiektu blob

Poniższy przykład wyzwala tylko na `input` obiektach blob w kontenerze, które zaczynają się od ciągu "original-":

```json
"path": "input/original-{name}",
```

Jeśli nazwa obiektu blob jest *oryginalna-Blob1.txt,* wartość `name` `Blob1`zmiennej w kodzie funkcji jest .

### <a name="filter-on-file-type"></a>Filtr na typie pliku

Poniższy przykład wyzwala tylko pliki *png:*

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtrowanie nawiasów klamrowych w nazwach plików

Aby wyszukać nawiasy klamrowe w nazwach plików, należy uciec od nawiasów klamrowych za pomocą dwóch nawiasów klamrowych. Następujące przykładowe filtry dla obiektów blob, które mają nawiasy klamrowe w nazwie:

```json
"path": "images/{{20140101}}-{name}",
```

Jeśli obiekt blob nosi nazwę `name` * {20140101}-soundfile.mp3,* wartością zmiennej w kodzie funkcji jest *soundfile.mp3*.

## <a name="metadata"></a>Metadane

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

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

Metadane są niedostępne w języku Java.

---

## <a name="blob-receipts"></a>Przyjęcia obiektów blob

Środowisko uruchomieniowe usługi Azure Functions zapewnia, że żadna funkcja wyzwalacza obiektu blob nie zostanie wywołana więcej niż jeden raz dla tego samego nowego lub zaktualizowanego obiektu blob. Aby ustalić, czy dana wersja obiektu blob została przetworzona, zachowuje *przyjęcia obiektów blob*.

Usługa Azure Functions przechowuje potwierdzenia obiektów blob w kontenerze o nazwie *azure-webjobs-hosts* `AzureWebJobsStorage`na koncie magazynu platformy Azure dla aplikacji funkcji (zdefiniowanej przez ustawienie aplikacji). Paragon obiektu blob ma następujące informacje:

* Wyzwalana funkcja ("*&lt;nazwa aplikacji funkcji>*. Funkcje. nazwa funkcji>", na przykład: "MyFunctionApp.Functions.CopyBlob") * &lt; *
* Nazwa kontenera
* Typ obiektu blob ("BlockBlob" lub "PageBlob")
* Nazwa obiektu blob
* ETag (identyfikator wersji obiektu blob, na przykład: "0x8D1DC6E70A277EF")

Aby wymusić ponowne przetwarzanie obiektu blob, należy ręcznie usunąć przyjęcie obiektu blob dla tego obiektu blob z kontenera *azure-webjobs-hosts.* Podczas ponownego przetwarzania może nie wystąpić natychmiast, jest gwarantowane występuje w późniejszym momencie w czasie. Aby ponownie przetworzyć natychmiast, obiekt blob *scaninfo* w *azure-webjobs-hosts/blobscaninfo* mogą być aktualizowane. Wszystkie obiekty BLOB z ostatnio zmodyfikowaną `LatestScan` sygnaturą czasową po właściwości zostaną ponownie zeskanowane.

## <a name="poison-blobs"></a>Trujące plamy

Gdy funkcja wyzwalacza obiektu blob nie powiedzie się dla danego obiektu blob, usługi Azure Functions ponawia ponawia te funkcje łącznie 5 razy domyślnie.

Jeśli wszystkie 5 prób zakończy się niepowodzeniem, usługa Azure Functions dodaje wiadomość do kolejki magazynu o nazwie *webjobs-blobtrigger-poison*. Maksymalna liczba ponownych prób jest konfigurowalna. To samo ustawienie MaxDequeueCount jest używane do obsługi trujących obiektów blob i obsługi wiadomości kolejki trującej. Komunikat kolejki dla obiektów blob poison jest obiektem JSON, który zawiera następujące właściwości:

* FunctionId (w>* &lt;nazwa aplikacji funkcji *formatu . Funkcje. nazwa funkcji>) * &lt; *
* Typ obiektu BlobType ("BlockBlob" lub "PageBlob")
* NazwaKontenera
* Nazwa obiektu Blob
* ETag (identyfikator wersji obiektu blob, na przykład: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Współbieżność i użycie pamięci

Wyzwalacz obiektu blob używa kolejki wewnętrznie, więc maksymalna liczba wywołań funkcji równoczesnych jest kontrolowana przez [konfigurację kolejek w pliku host.json](functions-host-json.md#queues). Ustawienia domyślne ograniczają współbieżność do 24 wywołań. Ten limit stosuje się oddzielnie do każdej funkcji, która używa wyzwalacza obiektu blob.

[Plan zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work) ogranicza aplikację funkcji na jednej maszynie wirtualnej (VM) do 1,5 GB pamięci. Pamięć jest używana przez każdego jednocześnie wykonującego wystąpienie funkcji i przez sam środowisko wykonawcze Functions. Jeśli funkcja wyzwalana obiektami blob ładuje cały obiekt blob do pamięci, maksymalna pamięć używana przez tę funkcję tylko dla obiektów blob wynosi 24 * maksymalny rozmiar obiektu blob. Na przykład aplikacja funkcji z trzema funkcjami wyzwalanych przez obiekt blob i ustawieniami domyślnymi będzie miała maksymalną współbieżność na maszynę wirtualną 3 *24 = 72 wywołania funkcji.

Funkcje JavaScript i Java ładują cały obiekt blob do `string`pamięci, a funkcje języka C# robią to w przypadku powiązania z programem , `Byte[]`lub POCO.

## <a name="polling"></a>Sondowania

Sondowanie działa jako hybryda między sprawdzanie dzienników i uruchamianie okresowych skanowania kontenerów. Obiekty BLOB są skanowane w grupach 10 000 naraz z tokenem kontynuacji używanym między interwałami.

> [!WARNING]
> Ponadto dzienniki magazynu są tworzone na zasadzie ["najlepszego wysiłku".](/rest/api/storageservices/About-Storage-Analytics-Logging) Nie ma żadnej gwarancji, że wszystkie zdarzenia są przechwytywane. W niektórych warunkach dzienniki mogą zostać pominięte.
> 
> Jeśli potrzebujesz szybszego lub bardziej niezawodnego przetwarzania obiektów blob, należy rozważyć utworzenie [komunikatu kolejki](../storage/queues/storage-dotnet-how-to-use-queues.md) podczas tworzenia obiektu blob. Następnie użyj [wyzwalacza kolejki](functions-bindings-storage-queue.md) zamiast wyzwalacza obiektu blob, aby przetworzyć obiekt blob. Inną opcją jest użycie siatki zdarzeń; zobacz samouczek [Automatyzacja zmiany rozmiaru przesłanych obrazów przy użyciu siatki zdarzeń](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>Następne kroki

- [Odczytywanie danych magazynu obiektów blob po uruchomieniu funkcji](./functions-bindings-storage-blob-input.md)
- [Zapisywanie danych magazynu obiektów blob z funkcji](./functions-bindings-storage-blob-output.md)
