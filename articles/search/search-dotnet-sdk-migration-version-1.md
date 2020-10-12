---
title: Uaktualnianie do Azure Search .NET SDK w wersji 1,1
titleSuffix: Azure Cognitive Search
description: Migruj kod do Azure Search .NET SDK wersja 1,1 ze starszych wersji interfejsu API. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4860918fc4f995ad267fc35b142d3fcb460ce177
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002831"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Uaktualnianie do Azure Search .NET SDK w wersji 1,1

Jeśli używasz wersji 1.0.2-Preview lub starszej części [zestawu SDK Azure Search .NET](/dotnet/api/overview/azure/search), ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 1,1.

Aby zapoznać się z bardziej ogólnym przewodnikiem dotyczącym zestawu SDK, łącznie z przykładami, zobacz [jak używać Azure Search z poziomu aplikacji .NET](search-howto-dotnet-sdk.md).

> [!NOTE]
> Po uaktualnieniu do wersji 1,1 lub jeśli korzystasz już z wersji z zakresu od 1,1 do 2,0 — wersja zapoznawcza, należy przeprowadzić uaktualnienie do wersji 3. Aby uzyskać instrukcje [, zobacz Uaktualnianie do Azure Search .NET SDK w wersji 3](search-dotnet-sdk-migration.md) .
>

Najpierw zaktualizuj odwołanie do programu NuGet `Microsoft.Azure.Search` przy użyciu konsoli Menedżera pakietów NuGet lub kliknij prawym przyciskiem myszy odwołania do projektu i wybierz polecenie "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt.

Jeśli wcześniej używasz wersji 1.0.0-Preview, 1.0.1-Preview lub 1.0.2-Preview, kompilacja powinna zakończyć się powodzeniem i wszystko gotowe do użycia.

Jeśli wcześniej używasz wersji 0.13.0-Preview lub starszej, powinny być widoczne błędy kompilacji podobne do następujących:

```output
Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)
```

Następnym krokiem jest poprawienie błędów kompilacji po jednym. Większość będzie wymagała zmiany nazw klas i metod, które zostały zmienione w zestawie SDK. [Lista istotnych zmian w wersji 1,1](#ListOfChangesV1) zawiera listę zmian nazw.

Jeśli używasz klas niestandardowych do modelowania dokumentów, a te klasy mają właściwości niedopuszczające wartości null typy pierwotne (na przykład `int` lub `bool` w języku C#), istnieje Poprawka błędu w wersji 1,1 zestawu SDK, którego należy wiedzieć. Aby uzyskać więcej informacji, zobacz [poprawki błędów w wersji 1,1](#BugFixesV1) .

Na koniec po usunięciu błędów kompilacji można wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista istotnych zmian w wersji 1,1
Poniższa lista jest uporządkowana według prawdopodobieństwa, że zmiana wpłynie na kod aplikacji.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch i IndexAction zmiany
`IndexBatch.Create` Zmieniono nazwę na `IndexBatch.New` i nie ma już `params` argumentu. Można użyć `IndexBatch.New` dla partii, które mieszają różne typy akcji (Scalanie, usuwanie itp.). Ponadto istnieją nowe metody statyczne do tworzenia partii, w których wszystkie akcje są takie same: `Delete` , `Merge` , `MergeOrUpload` , i `Upload` .

`IndexAction` nie ma już konstruktorów publicznych i jego właściwości są teraz niezmienne. Należy używać nowych metod statycznych do tworzenia akcji do różnych celów: `Delete` , `Merge` , `MergeOrUpload` , i `Upload` . `IndexAction.Create` został usunięty. Jeśli użyto przeciążenia, które pobiera tylko dokument, należy `Upload` zamiast tego użyć.

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
indexClient.Documents.Index(batch);
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
indexClient.Documents.Index(batch);
```

Jeśli chcesz, możesz uprościć to:

```csharp
var batch = IndexBatch.Upload(documents);
indexClient.Documents.Index(batch);
```

### <a name="indexbatchexception-changes"></a>IndexBatchException zmiany
`IndexBatchException.IndexResponse`Nazwa właściwości została zmieniona na `IndexingResults` , a jej typ to teraz `IList<IndexingResult>` .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
catch (IndexBatchException e)
{
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Zmiany metody operacji
Każda operacja w Azure Search .NET SDK jest uwidaczniana jako zestaw przeciążeń metody dla wywołań synchronicznych i asynchronicznych. Sygnatury i ich fabryki zostały zmienione w wersji 1,1.

Na przykład operacja "Pobierz statystyki indeksu" we wcześniejszych wersjach zestawu SDK uwidacznia następujące sygnatury:

W pliku `IIndexOperations`:

```csharp
// Asynchronous operation with all parameters
Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    string indexName,
    CancellationToken cancellationToken);
```

W pliku `IndexOperationsExtensions`:

```csharp
// Asynchronous operation with only required parameters
public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
    this IIndexOperations operations,
    string indexName);

// Synchronous operation with only required parameters
public static IndexGetStatisticsResponse GetStatistics(
    this IIndexOperations operations,
    string indexName);
```

Sygnatury metod dla tej samej operacji w wersji 1,1 wyglądać następująco:

W pliku `IIndexesOperations`:

```csharp
// Asynchronous operation with lower-level HTTP features exposed
Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
    Dictionary<string, List<string>> customHeaders = null,
    CancellationToken cancellationToken = default(CancellationToken));
```

W pliku `IndexesOperationsExtensions`:

```csharp
// Simplified asynchronous operation
public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
    this IIndexesOperations operations,
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
    CancellationToken cancellationToken = default(CancellationToken));

// Simplified synchronous operation
public static IndexGetStatisticsResult GetStatistics(
    this IIndexesOperations operations,
    string indexName,
    SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));
```

Począwszy od wersji 1,1, zestaw SDK Azure Search .NET organizuje różne metody operacji:

* Parametry opcjonalne są teraz modelowane jako parametry domyślne, a nie dodatkowe przeciążenia metod. Zmniejsza to liczbę przeciążeń metod, czasami znacząco.
* Metody rozszerzające teraz ukrywają wiele nadmiarowych szczegółów protokołu HTTP z obiektu wywołującego. Na przykład starsze wersje zestawu SDK zwróciły obiekt odpowiedzi z kodem stanu HTTP, którego często nie trzeba było sprawdzić, ponieważ metody operacji zgłaszają `CloudException` kod stanu, który wskazuje na błąd. Nowe metody rozszerzające zwracają obiekty modelu, co pozwala na ich odwinięcie w kodzie.
* Z drugiej strony interfejsy podstawowe teraz uwidaczniają metody, które zapewniają większą kontrolę na poziomie protokołu HTTP, jeśli jest to potrzebne. Teraz można przekazać niestandardowe nagłówki HTTP, aby uwzględnić je w żądaniach, a nowy `AzureOperationResponse<T>` zwracany typ daje bezpośredni dostęp do `HttpRequestMessage` i `HttpResponseMessage` dla operacji. `AzureOperationResponse` jest zdefiniowany w `Microsoft.Rest.Azure` przestrzeni nazw i zastępuje `Hyak.Common.OperationResponse` .

### <a name="scoringparameters-changes"></a>ScoringParameters zmiany
Nowa klasa o nazwie `ScoringParameter` została dodana w najnowszym zestawie SDK, aby ułatwić dostarczenie parametrów do profilów oceniania w zapytaniu wyszukiwania. Wcześniej `ScoringProfiles` Właściwość `SearchParameters` klasy została wpisana jako `IList<string>` ; Teraz jest wpisana jako `IList<ScoringParameter>` .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };
```

Można to zmienić w celu usunięcia błędów kompilacji: 

```csharp
var sp = new SearchParameters();
sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
sp.ScoringParameters =
    new[]
    {
        new ScoringParameter("featuredParam", new[] { "featured" }),
        new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
    };
```

### <a name="model-class-changes"></a>Zmiany klasy modelu
Ze względu na zmiany sygnatury opisane w [metodzie operacji zmiany](#OperationMethodChanges)nazwy wielu klas w `Microsoft.Azure.Search.Models` przestrzeni nazw zostały zmienione lub zostały usunięte. Na przykład:

* `IndexDefinitionResponse` został zastąpiony przez `AzureOperationResponse<Index>`
* Zmieniono nazwę polecenia `DocumentSearchResponse` na `DocumentSearchResult`
* Zmieniono nazwę polecenia `IndexResult` na `IndexingResult`
* `Documents.Count()` Funkcja zwraca teraz `long` liczbę dokumentów zamiast `DocumentCountResponse`
* Zmieniono nazwę polecenia `IndexGetStatisticsResponse` na `IndexGetStatisticsResult`
* Zmieniono nazwę polecenia `IndexListResponse` na `IndexListResult`

Aby podsumować `OperationResponse` klasy pochodne, które istniały tylko w przypadku, gdy obiekt modelu został usunięty. Sufiks pozostałych klas został zmieniony z `Response` na `Result` .

#### <a name="example"></a>Przykład
Jeśli kod wygląda następująco:

```csharp
IndexerGetStatusResponse statusResponse = null;

try
{
    statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
}
catch (Exception ex)
{
    Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
    return;
}

IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
IndexerExecutionInfo status = null;

try
{
    status = _searchClient.Indexers.GetStatus(indexer.Name);
}
catch (Exception ex)
{
    Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
    return;
}

IndexerExecutionResult lastResult = status.LastResult;
```

#### <a name="response-classes-and-ienumerable"></a>Klasy odpowiedzi i IEnumerable
Dodatkowa zmiana, która może mieć wpływ na Twój kod, to klasy odpowiedzi, które nie implementują kolekcji `IEnumerable<T>` . Zamiast tego można uzyskać bezpośredni dostęp do właściwości kolekcji. Na przykład, jeśli kod wygląda następująco:

```csharp
DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response)
{
    Console.WriteLine(result.Document);
}
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
foreach (SearchResult<Hotel> result in response.Results)
{
    Console.WriteLine(result.Document);
}
```

#### <a name="special-case-for-web-applications"></a>Specjalny przypadek dla aplikacji sieci Web
Jeśli masz aplikację sieci Web, która deserializacji `DocumentSearchResponse` bezpośrednio w celu wysyłania wyników wyszukiwania do przeglądarki, musisz zmienić kod lub wyniki nie będą poprawnie serializowane. Na przykład, jeśli kod wygląda następująco:

```csharp
public ActionResult Search(string q = "")
{
    // If blank search, assume they want to search everything
    if (string.IsNullOrWhiteSpace(q))
        q = "*";

    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = _featuresSearch.Search(q)
    };
}
```

Można to zmienić, pobierając `.Results` Właściwość odpowiedzi wyszukiwania w celu naprawienia renderowania wyników wyszukiwania:

```csharp
public ActionResult Search(string q = "")
{
    // If blank search, assume they want to search everything
    if (string.IsNullOrWhiteSpace(q))
        q = "*";

    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = _featuresSearch.Search(q).Results
    };
}
```

Należy poszukać takich przypadków w kodzie, **Kompilator nie wyświetli ostrzeżenia,** ponieważ `JsonResult.Data` jest typem `object` .

### <a name="cloudexception-changes"></a>Zmiany w chmurzeexception
`CloudException`Klasa została przeniesiona z `Hyak.Common` przestrzeni nazw do `Microsoft.Rest.Azure` przestrzeni nazw. Ponadto nazwa jego `Error` właściwości została zmieniona na `Body` .

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient i SearchIndexClient zmiany
Typ `Credentials` Właściwości został zmieniony z `SearchCredentials` na jego klasę bazową `ServiceClientCredentials` . Jeśli potrzebujesz dostępu do `SearchCredentials` `SearchIndexClient` lub `SearchServiceClient` , użyj nowej `SearchCredentials` właściwości.

W starszych wersjach zestawu SDK `SearchServiceClient` i `SearchIndexClient` posiadały konstruktory, które przyjęły `HttpClient` parametr. Zostały one zastąpione konstruktorami, które pobierają `HttpClientHandler` i tablicę `DelegatingHandler` obiektów. Ułatwia to instalowanie niestandardowych obsługi żądań HTTP w razie potrzeby.

Na koniec konstruktory, które miały `Uri` i `SearchCredentials` zostały zmienione. Na przykład, jeśli masz kod, który wygląda następująco:

```csharp
var client =
    new SearchServiceClient(
        new SearchCredentials("abc123"),
        new Uri("http://myservice.search.windows.net"));
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
var client =
    new SearchServiceClient(
        new Uri("http://myservice.search.windows.net"),
        new SearchCredentials("abc123"));
```

Należy również zauważyć, że typ parametru poświadczenia został zmieniony na `ServiceClientCredentials` . Prawdopodobnie nie wpłynie to na kod, ponieważ `SearchCredentials` pochodzi od `ServiceClientCredentials` .

### <a name="passing-a-request-id"></a>Przekazywanie identyfikatora żądania
W starszych wersjach zestawu SDK można ustawić identyfikator żądania na `SearchServiceClient` lub `SearchIndexClient` i zostanie on uwzględniony w każdym żądaniu do interfejsu API REST. Jest to przydatne w przypadku rozwiązywania problemów z usługą wyszukiwania, jeśli musisz skontaktować się z pomocą techniczną. Jednak bardziej przydatne jest ustawienie unikatowego identyfikatora żądania dla każdej operacji, a nie użycie tego samego identyfikatora dla wszystkich operacji. Z tego powodu `SetClientRequestId` metody `SearchServiceClient` i `SearchIndexClient` zostały usunięte. Zamiast tego można przekazać identyfikator żądania do każdej metody operacji za pośrednictwem opcjonalnego `SearchRequestOptions` parametru.

> [!NOTE]
> W przyszłej wersji zestawu SDK dodamy nowy mechanizm konfigurowania identyfikatora żądania globalnie dla obiektów klienta, które są spójne z podejściem używanym przez inne zestawy SDK platformy Azure.
> 
> 

### <a name="example"></a>Przykład
Jeśli masz kod, który wygląda następująco:

```csharp
client.SetClientRequestId(Guid.NewGuid());
...
long count = client.Documents.Count();
```

Można to zmienić w celu usunięcia błędów kompilacji:

```csharp
long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));
```

### <a name="interface-name-changes"></a>Zmiany nazw interfejsów
Wszystkie nazwy interfejsów grupy operacji zostały zmienione tak, aby były spójne z odpowiadającymi im nazwami właściwości:

* `ISearchServiceClient.Indexes`Zmieniono nazwę typu z `IIndexOperations` na `IIndexesOperations` .
* `ISearchServiceClient.Indexers`Zmieniono nazwę typu z `IIndexerOperations` na `IIndexersOperations` .
* `ISearchServiceClient.DataSources`Zmieniono nazwę typu z `IDataSourceOperations` na `IDataSourcesOperations` .
* `ISearchIndexClient.Documents`Zmieniono nazwę typu z `IDocumentOperations` na `IDocumentsOperations` .

Ta zmiana nie wpłynie na kod, chyba że nie zostały utworzone żadne imitacje tych interfejsów do celów testowych.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Poprawki błędów w wersji 1,1
Wystąpił błąd we wcześniejszych wersjach Azure Search .NET SDK odnoszących się do serializacji niestandardowych klas modelu. Usterka może wystąpić, jeśli utworzono niestandardową klasę modelu z właściwością typu wartości niedopuszczające wartości null.

### <a name="steps-to-reproduce"></a>Kroki do odtworzenia
Utwórz niestandardową klasę modelu z właściwością typu wartości, która nie dopuszcza wartości null. Na przykład Dodaj publiczną `UnitCount` Właściwość Type `int` zamiast `int?` .

Jeśli indeksowanie dokumentu ma wartość domyślną tego typu (na przykład 0 dla `int` ), pole będzie miało wartość null w Azure Search. Jeśli następnie wyszukasz ten dokument, `Search` wywołanie zgłosi `JsonSerializationException` skargę, że nie można przeprowadzić konwersji `null` do `int` .

Ponadto filtry mogą nie zadziałały zgodnie z oczekiwaniami, ponieważ w indeksie zamiast zamierzonej wartości Zapisano wartość null.

### <a name="fix-details"></a>Popraw szczegóły
Ten problem został rozwiązany w wersji 1,1 zestawu SDK. Teraz, jeśli masz klasę modelu podobną do tej:

```csharp
public class Model
{
    public string Key { get; set; }

    public int IntValue { get; set; }
}
```

i ustawiona `IntValue` na 0, ta wartość jest teraz prawidłowo serializowana jako 0 w locie i przechowywana jako 0 w indeksie. Rounding działa również zgodnie z oczekiwaniami.

Istnieje jeden potencjalny problem, który należy wiedzieć o tym podejściu: Jeśli używasz typu modelu z właściwością, która nie dopuszcza wartości null, musisz **zagwarantować** , że żadne dokumenty w indeksie nie zawierają wartości null dla odpowiedniego pola. Nie można wymusić tego zestawu SDK ani interfejsu API REST Azure Search.

Nie jest to czysto hipotetyczny problem: wyobraź sobie scenariusz, w którym dodajesz nowe pole do istniejącego indeksu typu `Edm.Int32`. Po zaktualizowaniu definicji indeksu wszystkie dokumenty będą miały wartość null dla tego nowego pola (ponieważ wszystkie typy w usłudze Azure Search dopuszczają wartość null). Jeśli następnie dla tego pola użyjesz klasy modelu z właściwością `int` niedopuszczającą wartości null, podczas próby pobrania dokumentów otrzymasz wyjątek `JsonSerializationException` podobny do poniższego:

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

Z tego powodu nadal zalecamy używanie typów dopuszczających wartości null w klasach modelu jako najlepsze rozwiązanie.

Aby uzyskać więcej informacji na temat tego błędu i poprawki, zobacz [ten problem w witrynie GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).