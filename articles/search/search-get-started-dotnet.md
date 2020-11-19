---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w programie .NET'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start w języku C# dowiesz się, jak utworzyć indeks, załadować dane i uruchamiać zapytania przy użyciu biblioteki klienta Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f82254915ffedf97f945be79be0de827a956af45
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916614"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Szybki Start: Tworzenie indeksu wyszukiwania przy użyciu biblioteki klienta Azure.Search.Documents

Użyj nowej [ biblioteki klientaAzure.Search.Documents (wersja 11)](/dotnet/api/overview/azure/search.documents-readme) do utworzenia aplikacji konsolowej .NET Core w języku C#, która tworzy, ładuje i bada indeks wyszukiwania.

[Pobierz kod źródłowy](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) , aby rozpocząć pracę z gotowym projektem, lub wykonaj kroki opisane w tym artykule, aby utworzyć własne.

> [!NOTE]
> Szukasz wcześniejszej wersji? Zobacz [Tworzenie indeksu wyszukiwania przy użyciu programu Microsoft. Azure. Search v10](search-get-started-dotnet-v10.md) zamiast tego.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy dysponować następującymi narzędziami i usługami:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), dowolna wersja. Przykładowy kod został przetestowany w bezpłatnej wersji Community programu Visual Studio 2019.

+ [ Pakiet NuGetAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)

Zestaw Azure SDK dla platformy .NET jest zgodny z [.NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), co oznacza, że .NET Framework 4.6.1 i .net Core 2,0 jako wymagania minimalne.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Załącz informacje o połączeniu z usługą, a następnie uruchom program Visual Studio, aby utworzyć nowy projekt aplikacji konsoli, który można uruchomić w środowisku .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kopiowanie klucza i punktu końcowego

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu dla każdego żądania. Najpierw znajdź klucz interfejsu API i adres URL, które mają zostać dodane do projektu. Podczas tworzenia klienta w późniejszym kroku należy określić obie wartości.

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi, wymagany w przypadku tworzenia lub usuwania obiektów. Istnieją dwa wymienne klucze podstawowe i pomocnicze. Możesz użyć jednej z nich.

   ![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-rest/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

### <a name="install-the-nuget-package"></a>Zainstaluj pakiet NuGet

1. W programie Visual Studio Utwórz nowy projekt przy użyciu szablonu Aplikacja konsolowa (.NET Core) dla języka C#.

1. Wyszukaj `Azure.Search.Documents` i wybierz wersję 11,0 lub nowszą.

### <a name="create-a-search-client"></a>Tworzenie klienta wyszukiwania

1. W **program.cs** Zmień przestrzeń nazw na, `AzureSearch.SDK.Quickstart.v11` a następnie Dodaj następujące `using` dyrektywy.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Tworzenie dwóch klientów: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) tworzy indeks, a [SearchClient](/dotnet/api/azure.search.documents.searchclient) ładuje i wysyła zapytanie do istniejącego indeksu. Oba muszą mieć punkt końcowy usługi i klucz interfejsu API administratora na potrzeby uwierzytelniania przy użyciu praw do tworzenia i usuwania.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Ten przewodnik Szybki Start tworzy indeks hoteli, który zostanie załadowany z danymi hotelu i wykonuje zapytania względem. W tym kroku Zdefiniuj pola w indeksie. Każda definicja pola zawiera nazwę, typ danych i atrybuty, które określają sposób używania pola.

W tym przykładzie metody synchroniczne Azure.Search.Docbiblioteki uments są używane do uproszczenia i czytelności. Jednak w przypadku scenariuszy produkcyjnych należy używać metod asynchronicznych, aby zachować skalowalność i szybkość reakcji aplikacji. Na przykład można użyć [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) zamiast [indeksu](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Dodaj pustą definicję klasy do projektu: **Hotel.cs**

1. W **Hotel.cs**, Zdefiniuj strukturę dokumentu hotelowego.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. W **program.cs** Utwórz obiekt [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex)  [, wywołując metodę](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) `SearchIndexClient` .

    ```csharp
    private static void CreateIndex(string indexName, SearchIndexClient indexClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));
        var definition = new SearchIndex(indexName, searchFields);

        indexClient.CreateOrUpdateIndex(definition);
    }
    ```

   <!-- ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ``` -->

Atrybuty w polu określają, w jaki sposób jest używany w aplikacji. Na przykład, `IsFilterable` atrybut musi być przypisany do każdego pola, które obsługuje wyrażenie filtru.

W bibliotece klienta Azure.Search.Documents można użyć [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) i [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) , aby usprawnić definicje pól. Oba są pochodnymi elementu [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) i mogą uprościć swój kod:

+ `SimpleField` może być dowolnego typu danych, jest zawsze niemożliwy do przeszukania (jest ignorowany w przypadku kwerend wyszukiwania pełnotekstowego) i można je pobrać (nie jest ukryte). Inne atrybuty są domyślnie wyłączone, ale można je włączyć. Możesz użyć `SimpleField` dla identyfikatorów dokumentu lub pól używanych tylko w filtrach, aspektach lub profilach oceniania. Jeśli tak, pamiętaj, aby zastosować wszelkie atrybuty, które są niezbędne dla tego scenariusza, `IsKey = true` na przykład identyfikator dokumentu. Aby uzyskać więcej informacji, zobacz [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) w kodzie źródłowym.

+ `SearchableField` musi być ciągiem i jest zawsze możliwy do przeszukiwania i pobierania. Inne atrybuty są domyślnie wyłączone, ale można je włączyć. Ponieważ ten typ pola jest możliwy do przeszukiwania, obsługuje synonimy i pełen uzupełnianie właściwości analizatora. Aby uzyskać więcej informacji, zobacz [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) w kodzie źródłowym.

Bez względu na to, czy używasz podstawowego `SearchField` interfejsu API, czy jednego z modeli pomocników, musisz jawnie włączyć atrybuty Filter, facet i Sort. Na przykład, [IsFiltered](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [issorting](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)i [iskrojuing](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) musi być jawnie przypisany, jak pokazano w powyższym przykładzie. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Usługa Azure Wyszukiwanie poznawcze przeszukuje zawartość przechowywaną w usłudze. W tym kroku zostaną załadowane dokumenty JSON, które są zgodne z właśnie utworzonym indeksem hotelu.

W przypadku usługi Azure Wyszukiwanie poznawcze dokumenty wyszukiwania są strukturami danych, które są danymi wejściowymi do indeksowania i wyjść z zapytań. Jak uzyskano z zewnętrznego źródła danych, dane wejściowe dokumentu mogą być wierszami w bazie danych, obiektami BLOB w magazynie obiektów blob lub dokumentami JSON na dysku. W tym przykładzie przygotowujemy skrót i osadzamy dokumenty JSON dla pięciu hoteli w samym kodzie. 

Podczas przekazywania dokumentów należy użyć obiektu [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) . `IndexDocumentsBatch`Obiekt zawiera kolekcję [akcji](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), z których każdy zawiera dokument i Właściwość informującą platformę Azure wyszukiwanie poznawcze czynności do wykonania ([przekazywanie, scalanie, usuwanie i mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. W **program.cs** Utwórz tablicę dokumentów i akcji indeksu, a następnie Przekaż tablicę do `IndexDocumentsBatch` . Poniższe dokumenty są zgodne ze indeksem hoteli-szybkiego startu V11, zdefiniowanym przez klasę hotelu.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    srchclient.IndexDocuments(batch, idxoptions);
    ```

    Po zainicjowaniu obiektu [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) można wysłać go do indeksu, wywołując [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) na obiekcie [SearchClient](/dotnet/api/azure.search.documents.searchclient) .

1. Ponieważ jest to Aplikacja konsolowa, która uruchamia wszystkie polecenia sekwencyjnie, należy dodać 2-sekundowe opóźnienie.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    2-sekundowe opóźnienie kompensuje indeksowanie, które jest asynchroniczne, tak aby wszystkie dokumenty mogły być indeksowane przed wykonaniem zapytań. Kodowanie w opóźnieniu jest zwykle wymagane tylko w pokazach, testach i przykładowych aplikacjach.

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Wyniki zapytania można uzyskać zaraz po indeksowaniu pierwszego dokumentu, ale w celu poprawnego testowania poczekaj, aż wszystkie dokumenty są indeksowane.

W tej sekcji dodano dwie elementy funkcjonalności: Logika zapytań i wyniki. W przypadku zapytań Użyj metody [Search](/dotnet/api/azure.search.documents.searchclient.search) . Ta metoda pobiera tekst przeszukiwany (ciąg zapytania), a także inne [Opcje](/dotnet/api/azure.search.documents.searchoptions).

Klasa [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) reprezentuje wyniki.

1. W **program.cs** Utwórz metodę WriteDocuments, która drukuje wyniki wyszukiwania w konsoli programu.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Utwórz metodę RunQueries, aby wykonać zapytania i zwrócić wyniki. Wyniki to obiekty hotelowe.

    ```csharp
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = srchclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

1. Dodaj RunQueries do `Main()` .

    ```csharp
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);
    ```

W tym przykładzie przedstawiono dwa [sposoby dopasowywania terminów w kwerendzie](search-query-overview.md#types-of-queries): wyszukiwanie pełnotekstowe i filtry:

+ Zapytania wyszukiwania pełnotekstowego dla co najmniej jednego warunku w polach z możliwością wyszukiwania w indeksie. Pierwsze zapytanie jest wyszukiwaniem pełnotekstowym. Wyszukiwanie pełnotekstowe generuje oceny przydatności używane do klasyfikowania wyników.

+ Filtr jest wyrażeniem logicznym, które jest oceniane dla pól [IsFiltered](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) w indeksie. Kwerendy filtru obejmują wartości dołączania lub wykluczania. W związku z tym nie istnieje wynik istotny związany z kwerendą filtru. Ostatnie dwa zapytania demonstrują wyszukiwanie filtru.

Możesz użyć wyszukiwania pełnotekstowego i filtrów razem lub oddzielnie.

Wyszukiwanie i filtry są wykonywane przy użyciu metody [SearchClient. Search](/dotnet/api/azure.search.documents.searchclient.search) . Zapytanie wyszukiwania można przesłać w `searchText` ciągu, podczas gdy wyrażenie filtru może być przesyłane we właściwości [filtru](/dotnet/api/azure.search.documents.searchoptions.filter) klasy [SearchOptions zawierają](/dotnet/api/azure.search.documents.searchoptions) . Aby filtrować bez wyszukiwania, należy tylko przekazać `"*"` `searchText` parametr metody [Search](/dotnet/api/azure.search.documents.searchclient.search) . Aby wyszukiwać bez filtrowania, pozostaw `Filter` Właściwość nieustawioną lub nie przekazuj wystąpienia w `SearchOptions` ogóle.

## <a name="run-the-program"></a>Uruchamianie programu

Naciśnij klawisz F5, aby ponownie skompilować aplikację i uruchomić program w całości. 

Dane wyjściowe zawierają komunikaty z [konsoli. WriteLine](/dotnet/api/system.console.writeline)z dodaniem informacji o zapytaniu i wyników.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start w języku C# pracujesz nad zestawem zadań, aby utworzyć indeks, załadować go wraz z dokumentami i uruchamiać zapytania. Na różnych etapach wprowadziliśmy skróty upraszczające kod umożliwiający odczytywanie i zrozumienie. Jeśli masz doświadczenie z podstawowymi pojęciami, zalecamy użycie następnego artykułu w celu eksplorowania alternatywnych metod i koncepcji, które pomogą Ci pogłębić swoją wiedzę. 

> [!div class="nextstepaction"]
> [Jak opracowywać w programie .NET](search-howto-dotnet-sdk.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
