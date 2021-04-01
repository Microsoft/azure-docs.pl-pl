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
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98180102"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Szybki Start: Tworzenie indeksu wyszukiwania przy użyciu biblioteki klienta Azure.Search.Documents

Użyj nowej [ biblioteki klientaAzure.Search.Documents (wersja 11)](/dotnet/api/overview/azure/search.documents-readme) do utworzenia aplikacji konsolowej .NET Core w języku C#, która tworzy, ładuje i bada indeks wyszukiwania.

Możesz [pobrać kod źródłowy](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) , aby rozpocząć od gotowego projektu lub wykonać kroki opisane w tym artykule, aby utworzyć własne.

> [!NOTE]
> Szukasz wcześniejszej wersji? Zobacz [Tworzenie indeksu wyszukiwania przy użyciu programu Microsoft. Azure. Search v10](search-get-started-dotnet-v10.md) zamiast tego.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy dysponować następującymi narzędziami i usługami:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), dowolna wersja. Przykładowy kod został przetestowany w bezpłatnej wersji Community programu Visual Studio 2019.

Podczas konfigurowania projektu zostanie pobrany [ pakiet NuGetAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/).

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

Po utworzeniu projektu dodaj bibliotekę kliencką. [PakietAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) składa się z jednej biblioteki klienckiej, która zapewnia wszystkie interfejsy API używane do pracy z usługą wyszukiwania w programie .NET.

1. Uruchom program Visual Studio i Utwórz aplikację konsolową .NET Core.

1. W obszarze **Narzędzia**  >  **Menedżer pakietów NuGet** wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania.**.. 

1. Kliknij pozycję **Browse (Przeglądaj)**.

1. Wyszukaj `Azure.Search.Documents` i wybierz wersję 11,0 lub nowszą.

1. Kliknij przycisk **Instaluj** po prawej stronie, aby dodać zestaw do projektu i rozwiązania.

### <a name="create-a-search-client"></a>Tworzenie klienta wyszukiwania

1. W programie **program. cs** Zmień przestrzeń nazw na, `AzureSearch.SDK.Quickstart.v11` a następnie Dodaj następujące `using` dyrektywy.

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
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 — Tworzenie indeksu

Ten przewodnik Szybki Start tworzy indeks hoteli, który zostanie załadowany z danymi hotelu i wykonuje zapytania względem. W tym kroku Zdefiniuj pola w indeksie. Każda definicja pola zawiera nazwę, typ danych i atrybuty, które określają sposób używania pola.

W tym przykładzie metody synchroniczne Azure.Search.Docbiblioteki uments są używane do uproszczenia i czytelności. Jednak w przypadku scenariuszy produkcyjnych należy używać metod asynchronicznych, aby zachować skalowalność i szybkość reakcji aplikacji. Na przykład można użyć [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) zamiast [indeksu](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Dodaj pustą definicję klasy do projektu: **Hotel. cs**

1. Skopiuj następujący kod do **hotelu. cs** , aby zdefiniować strukturę dokumentu hotelowego. Atrybuty w polu określają, w jaki sposób jest używany w aplikacji. Na przykład, `IsFilterable` atrybut musi być przypisany do każdego pola, które obsługuje wyrażenie filtru.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   W bibliotece klienta Azure.Search.Documents można użyć [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) i [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) , aby usprawnić definicje pól. Oba są pochodnymi elementu [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) i mogą uprościć swój kod:

   + `SimpleField` może być dowolnego typu danych, jest zawsze niemożliwy do przeszukania (jest ignorowany w przypadku kwerend wyszukiwania pełnotekstowego) i można je pobrać (nie jest ukryte). Inne atrybuty są domyślnie wyłączone, ale można je włączyć. Możesz użyć `SimpleField` dla identyfikatorów dokumentu lub pól używanych tylko w filtrach, aspektach lub profilach oceniania. Jeśli tak, pamiętaj, aby zastosować wszelkie atrybuty, które są niezbędne dla tego scenariusza, `IsKey = true` na przykład identyfikator dokumentu. Aby uzyskać więcej informacji, zobacz [SimpleFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) w kodzie źródłowym.

   + `SearchableField` musi być ciągiem i jest zawsze możliwy do przeszukiwania i pobierania. Inne atrybuty są domyślnie wyłączone, ale można je włączyć. Ponieważ ten typ pola jest możliwy do przeszukiwania, obsługuje synonimy i pełen uzupełnianie właściwości analizatora. Aby uzyskać więcej informacji, zobacz [SearchableFieldAttribute. cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) w kodzie źródłowym.

   Bez względu na to, czy używasz podstawowego `SearchField` interfejsu API, czy jednego z modeli pomocników, musisz jawnie włączyć atrybuty Filter, facet i Sort. Na przykład, [IsFiltered](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [issorting](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)i [iskrojuing](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) musi być jawnie przypisany, jak pokazano w powyższym przykładzie. 

1. Dodaj drugą pustą definicję klasy do projektu: **Address. cs**.  Skopiuj poniższy kod do klasy.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Utwórz dwie więcej klas: **Hotel. Methods. cs** i **Address. Methods. cs** for ToString (). Te klasy są używane do renderowania wyników wyszukiwania w danych wyjściowych konsoli.  Zawartość tych klas nie znajduje się w tym artykule, ale można skopiować kod z [plików w usłudze GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11).

1. W programie **program. cs** Utwórz obiekt [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) , a następnie Wywołaj metodę Create [index](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) , aby wyrazić indeks w usłudze wyszukiwania. Indeks zawiera również [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) , aby włączyć Autouzupełnianie dla określonych pól.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Usługa Azure Wyszukiwanie poznawcze przeszukuje zawartość przechowywaną w usłudze. W tym kroku zostaną załadowane dokumenty JSON, które są zgodne z właśnie utworzonym indeksem hotelu.

W przypadku usługi Azure Wyszukiwanie poznawcze dokumenty wyszukiwania są strukturami danych, które są danymi wejściowymi do indeksowania i wyjść z zapytań. Jak uzyskano z zewnętrznego źródła danych, dane wejściowe dokumentu mogą być wierszami w bazie danych, obiektami BLOB w magazynie obiektów blob lub dokumentami JSON na dysku. W tym przykładzie przygotowujemy skrót i osadzamy dokumenty JSON dla czterech hoteli w samym kodzie. 

Podczas przekazywania dokumentów należy użyć obiektu [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) . `IndexDocumentsBatch`Obiekt zawiera kolekcję [akcji](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), z których każdy zawiera dokument i Właściwość informującą platformę Azure wyszukiwanie poznawcze czynności do wykonania ([przekazywanie, scalanie, usuwanie i mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. W programie **program. cs** Utwórz tablicę dokumentów i akcji indeksu, a następnie Przekaż tablicę do `IndexDocumentsBatch` . Poniższe dokumenty są zgodne ze indeksem hoteli — Szybki Start, zdefiniowanym przez klasę hotelu.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Po zainicjowaniu obiektu [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) można wysłać go do indeksu, wywołując [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) na obiekcie [SearchClient](/dotnet/api/azure.search.documents.searchclient) .

1. Dodaj następujące wiersze do Main (). Ładowanie dokumentów odbywa się przy użyciu SearchClient, ale operacja wymaga również uprawnień administratora usługi, która jest zwykle skojarzona z SearchIndexClient. Jednym ze sposobów skonfigurowania tej operacji jest uzyskanie SearchClient do SearchIndexClient (adminClient w tym przykładzie).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Ponieważ jest to Aplikacja konsolowa, która uruchamia wszystkie polecenia sekwencyjnie, należy dodać 2-sekundowy czas oczekiwania między indeksowaniem a zapytaniami.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    2-sekundowe opóźnienie kompensuje indeksowanie, które jest asynchroniczne, tak aby wszystkie dokumenty mogły być indeksowane przed wykonaniem zapytań. Kodowanie w opóźnieniu jest zwykle wymagane tylko w pokazach, testach i przykładowych aplikacjach.

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Wyniki zapytania można uzyskać zaraz po indeksowaniu pierwszego dokumentu, ale rzeczywiste testy indeksu powinny poczekać do momentu indeksowania wszystkich dokumentów.

W tej sekcji dodano dwie elementy funkcjonalności: Logika zapytań i wyniki. W przypadku zapytań Użyj metody [Search](/dotnet/api/azure.search.documents.searchclient.search) . Ta metoda pobiera tekst przeszukiwany (ciąg zapytania), a także inne [Opcje](/dotnet/api/azure.search.documents.searchoptions).

Klasa [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) reprezentuje wyniki.

1. W programie **program. cs** Utwórz metodę **WriteDocuments** , która drukuje wyniki wyszukiwania w konsoli.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Utwórz metodę **RunQueries** , aby wykonać zapytania i zwrócić wyniki. Wyniki to obiekty hotelowe. Ten przykład pokazuje sygnaturę metody i pierwsze zapytanie. To zapytanie pokazuje parametr SELECT, który umożliwia redagowanie wyniku przy użyciu wybranych pól z dokumentu.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. W drugim zapytaniu wyszukaj termin, Dodaj filtr, który wybiera dokumenty, gdzie Rating jest większy niż 4, a następnie Sortuj według klasyfikacji w kolejności malejącej. Filtr jest wyrażeniem logicznym, które jest oceniane dla pól [IsFiltered](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) w indeksie. Kwerendy filtru obejmują wartości dołączania lub wykluczania. W związku z tym nie istnieje wynik istotny związany z kwerendą filtru. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. Trzecia kwerenda pokazuje searchFields, używany do określania zakresu operacji wyszukiwania pełnotekstowego do określonych pól.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. Czwarte zapytanie ilustruje aspekty, które mogą służyć do tworzenia struktury strukturalnej struktury nawigacyjnej. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. W piątej kwerendzie zwraca określony dokument. Wyszukiwanie dokumentów to typowa odpowiedź na zdarzenie onkliknięcia w zestawie wyników.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. Ostatnie zapytanie pokazuje składnię funkcji Autouzupełnianie, symulując częściowe dane wejściowe użytkownika "sa", które są rozpoznawane jako dwie możliwe dopasowania w sourceFields skojarzone z sugestią zdefiniowaną w indeksie.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Dodaj **RunQueries** do Main ().

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

Poprzednie zapytania pokazują wiele [sposobów dopasowywania terminów w zapytaniu](search-query-overview.md#types-of-queries): wyszukiwanie pełnotekstowe, filtry i Autouzupełnianie.

Wyszukiwanie pełnotekstowe i filtry są wykonywane przy użyciu metody [SearchClient. Search](/dotnet/api/azure.search.documents.searchclient.search) . Zapytanie wyszukiwania można przesłać w `searchText` ciągu, podczas gdy wyrażenie filtru może być przesyłane we właściwości [filtru](/dotnet/api/azure.search.documents.searchoptions.filter) klasy [SearchOptions zawierają](/dotnet/api/azure.search.documents.searchoptions) . Aby filtrować bez wyszukiwania, należy tylko przekazać `"*"` `searchText` parametr metody [Search](/dotnet/api/azure.search.documents.searchclient.search) . Aby wyszukiwać bez filtrowania, pozostaw `Filter` Właściwość nieustawioną lub nie przekazuj wystąpienia w `SearchOptions` ogóle.

## <a name="run-the-program"></a>Uruchamianie programu

Naciśnij klawisz F5, aby ponownie skompilować aplikację i uruchomić program w całości. 

Dane wyjściowe zawierają komunikaty z [konsoli. WriteLine](/dotnet/api/system.console.writeline)z dodaniem informacji o zapytaniu i wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start w języku C# pracujesz nad zestawem zadań, aby utworzyć indeks, załadować go wraz z dokumentami i uruchamiać zapytania. Na różnych etapach wprowadziliśmy skróty upraszczające kod umożliwiający odczytywanie i zrozumienie. Jeśli masz doświadczenie z podstawowymi pojęciami, zalecamy użycie następnego artykułu w celu eksplorowania alternatywnych metod i koncepcji, które pomogą Ci pogłębić swoją wiedzę. 

> [!div class="nextstepaction"]
> [Jak opracowywać w programie .NET](search-howto-dotnet-sdk.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
