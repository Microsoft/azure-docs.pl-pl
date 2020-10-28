---
title: Starsza wersja języka C# — Szybki Start
titleSuffix: Azure Cognitive Search
description: Ten przewodnik Szybki Start w języku C# używa biblioteki klienta w wersji 10 (Microsoft. Azure. Search) do tworzenia, ładowania i wykonywania zapytań względem indeksu wyszukiwania.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c6dd64ae8b7b7307d7dcd510d1fdb877365c6f36
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675949"
---
# <a name="quickstart-create-a-search-index-using-the-legacy-microsoftazuresearch-v10-client-library"></a>Szybki Start: Tworzenie indeksu wyszukiwania przy użyciu starszej biblioteki klienta Microsoft. Azure. Search v10

Ten artykuł jest przewodnikiem Szybki Start dla starszej wersji biblioteki klienta [**Microsoft. Azure. Search**](/dotnet/api/overview/azure/search/client10) (wersja 10), która została zastąpiona przez bibliotekę kliencką [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) (wersja 11).

> [!NOTE]
> Jeśli masz istniejące lub numerów porządkowych określających projekty deweloperskie, możesz nadal korzystać z wersji 10. Ale w przypadku nowych projektów lub do korzystania z nowych funkcji należy przejść do [nowej biblioteki](/dotnet/api/overview/azure/search.documents-readme).

## <a name="about-this-quickstart"></a>Informacje o tym przewodniku Szybki Start

Tworzenie aplikacji konsolowej platformy .NET Core w języku C#, która tworzy, ładuje i bada indeks Wyszukiwanie poznawcze platformy Azure przy użyciu programu Visual Studio i [bibliotek klienckich Microsoft. Azure. Search](/dotnet/api/overview/azure/search/client10). 

W tym artykule wyjaśniono, jak utworzyć aplikację. Możesz również [pobrać i uruchomić kompletną aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v10).

> [!NOTE]
> Kod demonstracyjny w tym artykule używa metod synchronicznych zestawu .NET SDK systemu Azure Wyszukiwanie poznawcze w wersji 10 dla uproszczenia. Jednak w przypadku scenariuszy produkcyjnych zalecamy użycie metod asynchronicznych we własnych aplikacjach, aby zapewnić ich skalowalność i elastyczność. Można na przykład użyć `CreateAsync` `DeleteAsync` elementów i zamiast `Create` i `Delete` .

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

+ Usługa Wyszukiwanie poznawcze platformy Azure. [Utwórz usługę](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), dowolna wersja. Przykładowy kod i instrukcje zostały przetestowane w wersji bezpłatnej społeczności.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   Pobierz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

![Pobieranie punktu końcowego HTTP i klucza dostępu](media/search-get-started-postman/get-url-key.png "Pobieranie punktu końcowego HTTP i klucza dostępu")

Wszystkie żądania wymagają klucza API dla każdego żądania wysyłanego do usługi. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Zacznij od otwierania programu Visual Studio i tworzenia nowego projektu aplikacji konsolowego, który można uruchomić w środowisku .NET Core.

### <a name="install-nuget-packages"></a>Instalowanie pakietów NuGet

[Pakiet Microsoft. Azure. Search](https://www.nuget.org/packages/Microsoft.Azure.Search/) składa się z kilku bibliotek klienckich dystrybuowanych jako pakiety NuGet.

W przypadku tego projektu należy użyć wersji 10 `Microsoft.Azure.Search` pakietu NuGet i najnowszego `Microsoft.Extensions.Configuration.Json` pakietu NuGet.

1. W obszarze **Narzędzia**  >  **Menedżer pakietów NuGet** wybierz pozycję **Zarządzaj pakietami NuGet dla rozwiązania.** .. 

1. Kliknij pozycję **Browse (Przeglądaj)** .

1. Wyszukaj `Microsoft.Azure.Search` i wybierz wersję 10.

1. Kliknij przycisk **Instaluj** po prawej stronie, aby dodać zestaw do projektu i rozwiązania.

1. Powtórz dla `Microsoft.Extensions.Configuration.Json` , wybierając wersję 2.2.0 lub nowszą.


### <a name="add-azure-cognitive-search-service-information"></a>Dodawanie informacji o usłudze Wyszukiwanie poznawcze platformy Azure

1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Dodaj**  >  **nowy element.** ... 

1. W obszarze Dodaj nowy element Wyszukaj ciąg "JSON", aby zwrócić listę typów elementów związanych ze standardem JSON.

1. Wybierz **plik JSON** , Nazwij plik "appsettings.json", a następnie kliknij przycisk **Dodaj** . 

1. Dodaj plik do katalogu wyjściowego. Kliknij prawym przyciskiem myszy appsettings.jsna i wybierz pozycję **Właściwości** . W obszarze **Kopiuj do katalogu wyjściowego** wybierz opcję **Kopiuj, jeśli nowszy** .

1. Skopiuj poniższy kod JSON do nowego pliku JSON. 

    ```json
    {
      "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
      "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
      "SearchIndexName": "hotels-quickstart"
    }
    ```

1. Zastąp wartość w polu Nazwa usługi wyszukiwania (nazwa usługi-SEARCH-SERVICE-NAME) i klucz interfejsu API administratora (administrator-administrator-klucz) z prawidłowymi wartościami. Jeśli punkt końcowy usługi to `https://mydemo.search.windows.net` , nazwa usługi to " `mydemo` ".

### <a name="add-class-method-files-to-your-project"></a>Dodaj klasę ". Metoda "pliki do projektu

Ten krok jest wymagany w celu utworzenia znaczących danych wyjściowych w konsoli programu. Podczas drukowania wyników do okna konsoli, poszczególne pola z obiektu hotelu muszą zostać zwrócone jako ciągi. Ten krok implementuje program [ToString ()](/dotnet/api/system.object.tostring) w celu wykonania tego zadania, które można skopiować do dwóch nowych plików.

1. Dodaj dwie puste definicje klas do projektu: Address.Methods.cs, Hotel.Methods.cs

1. W Address.Methods.cs Zastąp domyślną zawartość następującym kodem: [wierszami 1-25](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Address.Methods.cs#L1-L25).

1. W Hotel.Methods.cs Skopiuj [wiersze 1-68](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/quickstart/v10/AzureSearchQuickstart/Hotel.Methods.cs#L1-L68).

## <a name="1---create-index"></a>1 — Tworzenie indeksu

Indeks hoteli zawiera proste i złożone pola, w których proste pole to "Hotelname" lub "Description", a złożone pola są adresami z podpolami lub kolekcją pokojów. Gdy indeks zawiera typy złożone, Izoluj złożone definicje pól w oddzielnych klasach.

1. Dodaj dwie puste definicje klas do projektu: Address.cs, Hotel.cs

1. W Address.cs Zastąp domyślną zawartość następującym kodem:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

1. W Hotel.cs, Klasa definiuje ogólną strukturę indeksu, łącznie z odwołaniami do klasy Address.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrLucene)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Category { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public bool? ParkingIncluded { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Atrybuty w polu określają, w jaki sposób jest używany w aplikacji. Na przykład, `IsSearchable` atrybut musi być przypisany do każdego pola, które powinny być uwzględnione w wyszukiwaniu pełnotekstowym. 
    
    > [!NOTE]
    > W zestawie .NET SDK pola muszą być jawnie przypisane do,, [`IsSearchable`](/dotnet/api/microsoft.azure.search.models.field.issearchable) [`IsFilterable`](/dotnet/api/microsoft.azure.search.models.field.isfilterable) [`IsSortable`](/dotnet/api/microsoft.azure.search.models.field.issortable) , i [`IsFacetable`](/dotnet/api/microsoft.azure.search.models.field.isfacetable) . To zachowanie jest w przeciwieństwie do interfejsu API REST, który niejawnie włącza przypisanie na podstawie typu danych (na przykład proste pola ciągów są automatycznie przeszukiwane).

    Dokładnie jedno pole w indeksie typu `string` musi być polem *klucza* , jednoznacznie identyfikując każdy dokument. W tym schemacie klucz ma wartość `HotelId` .

    W tym indeksie pola Description używają [`analyzer`](/dotnet/api/microsoft.azure.search.models.field.analyzer) Właściwości opcjonalne, określonej podczas przesłonięcia domyślnego standardowego analizatora Lucene. W `description_fr` polu jest używany francuski Analizator Luces ([FrLucene](/dotnet/api/microsoft.azure.search.models.analyzername.frlucene)), ponieważ zawiera on tekst w języku francuskim. `description`Używa opcjonalnego narzędzia Microsoft Language Analyzer ([EnMicrosoft](/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft)).

1. W Program.cs Utwórz wystąpienie [`SearchServiceClient`](/dotnet/api/microsoft.azure.search.searchserviceclient) klasy w celu nawiązania połączenia z usługą przy użyciu wartości, które są przechowywane w pliku konfiguracyjnym aplikacji (appsettings.json). 

   `SearchServiceClient` ma [`Indexes`](/dotnet/api/microsoft.azure.search.searchserviceclient.indexes) Właściwość, dostarczając wszystkie metody, które są potrzebne do tworzenia, wyświetlania, aktualizowania lub usuwania indeksów wyszukiwanie poznawcze platformy Azure. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
            static void Main(string[] args)
            {
                IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
                IConfigurationRoot configuration = builder.Build();

                SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

                string indexName = configuration["SearchIndexName"];

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteIndexIfExists(indexName, serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateIndex(indexName, serviceClient);

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Microsoft.Azure.Search.Models.Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Jeśli to możliwe, należy udostępnić pojedyncze wystąpienie `SearchServiceClient` w aplikacji, aby uniknąć otwierania zbyt wielu połączeń. Metody klasy są bezpieczne wątkowo, aby umożliwić takie udostępnianie.

   Klasa ma kilka konstruktorów. Konstruktor odpowiedni w tym przypadku przyjmuje jako parametry nazwę usługi wyszukiwania i obiekt `SearchCredentials`. `SearchCredentials` opakowuje klucz interfejsu API.

    W definicji indeksu Najprostszym sposobem tworzenia `Field` obiektów jest wywołanie `FieldBuilder.BuildForType` metody, przekazanie klasy modelu dla parametru typu. Właściwości klasy modelu są mapowane na pola indeksu. To mapowanie pozwala powiązać dokumenty z indeksu wyszukiwania z wystąpieniami klasy modelu.

    > [!NOTE]
    > Jeśli nie zamierzasz używać klasy modelu, nadal możesz zdefiniować indeks poprzez bezpośrednie utworzenie obiektów `Field`. Możesz podać nazwę pola konstruktorowi wraz z typem danych (lub analizatorem w przypadku pól ciągów). Możesz również ustawić inne właściwości, takie jak `IsSearchable` , `IsFilterable` , aby nazwać kilka.
    >

1. Naciśnij klawisz F5, aby skompilować aplikację i utworzyć indeks. 

    W przypadku pomyślnego skompilowania projektu zostanie otwarte okno konsoli, które zapisuje komunikaty o stanie na ekranie w celu usunięcia i utworzenia indeksu. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 — ładowanie dokumentów

Na platformie Azure Wyszukiwanie poznawcze dokumenty są strukturami danych, które są danymi wejściowymi do indeksowania i wyjść z zapytań. Jak uzyskano z zewnętrznego źródła danych, dane wejściowe dokumentu mogą być wierszami w bazie danych, obiektami BLOB w magazynie obiektów blob lub dokumentami JSON na dysku. W tym przykładzie przygotowujemy skrót i osadzamy dokumenty JSON dla czterech hoteli w samym kodzie. 

Podczas przekazywania dokumentów należy użyć [`IndexBatch`](/dotnet/api/microsoft.azure.search.models.indexbatch) obiektu. `IndexBatch`Zawiera kolekcję [`IndexAction`](/dotnet/api/microsoft.azure.search.models.indexaction) obiektów, z których każdy zawiera dokument i Właściwość informującą platformę Azure wyszukiwanie poznawcze czynności do wykonania ([przekazywanie, scalanie, usuwanie i mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. W Program.cs Utwórz tablicę dokumentów i akcji indeksu, a następnie Przekaż tablicę do `IndexBatch` . Poniższe dokumenty są zgodne z indeksem hotelu — Szybki Start, zdefiniowanym przez klasy hotelowe i adresowe.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Po zainicjowaniu `IndexBatch` obiektu można wysłać go do indeksu, wywołując [`Documents.Index`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index) [`SearchIndexClient`](/dotnet/api/microsoft.azure.search.searchindexclient) obiekt. `Documents` jest właściwością `SearchIndexClient` , która zapewnia metody dodawania, modyfikowania, usuwania lub wykonywania zapytań o dokumenty w indeksie.

    `try` / `catch` Otaczające wywołanie `Index` metody przechwytuje błędy indeksowania, co może się zdarzyć, jeśli usługa jest mocno obciążona. W kodzie produkcyjnym można opóźnić, a następnie ponowić próbę indeksowania dokumentów, które zakończyły się niepowodzeniem, lub rejestrować i kontynuować, podobnie jak w przypadku przykładu, lub obsłużyć je w inny sposób, który spełnia wymagania dotyczące spójności danych aplikacji.

    2-sekundowe opóźnienie kompensuje indeksowanie, które jest asynchroniczne, tak aby wszystkie dokumenty mogły być indeksowane przed wykonaniem zapytań. Kodowanie w opóźnieniu jest zwykle wymagane tylko w pokazach, testach i przykładowych aplikacjach.

1. W Program.cs, w obszarze głównym, Usuń komentarz z wierszy dla "2-Ładuj dokumenty". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Naciśnij klawisz F5, aby ponownie skompilować aplikację. 

    W przypadku pomyślnego skompilowania projektu zostanie otwarte okno konsoli, w którym są zapisywane komunikaty o stanie, tym razem z komunikatem dotyczącym przekazywania dokumentów. W Azure Portal na stronie **Przegląd** usługi wyszukiwania Hotele-szybki indeks powinien teraz zawierać 4 dokumenty.

Aby uzyskać więcej informacji na temat przetwarzania dokumentów, zobacz ["jak zestaw SDK .NET obsługuje dokumenty"](search-howto-dotnet-sdk-v10.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Wyniki zapytania można uzyskać zaraz po indeksowaniu pierwszego dokumentu, ale rzeczywiste testy indeksu powinny poczekać do momentu indeksowania wszystkich dokumentów. 

W tej sekcji dodano dwie elementy funkcjonalności: Logika zapytań i wyniki. W przypadku zapytań Użyj [`Search`](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search) metody. Ta metoda pobiera tekst wyszukiwania oraz inne [Parametry](/dotnet/api/microsoft.azure.search.models.searchparameters). 

[`DocumentsSearchResult`](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1)Klasa reprezentuje wyniki.


1. W Program.cs Utwórz metodę WriteDocuments, która drukuje wyniki wyszukiwania w konsoli programu.

    ```csharp
    private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.Results)
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Utwórz metodę RunQueries, aby wykonać zapytania i zwrócić wyniki. Wyniki to obiekty hotelowe. Możesz użyć parametru SELECT, aby wyrównać poszczególne pola. Jeśli pole nie jest dołączone do parametru SELECT, odpowiadająca mu Właściwość hotelu będzie równa null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Istnieją dwa [sposoby dopasowywania terminów do zapytania](search-query-overview.md#types-of-queries): wyszukiwanie pełnotekstowe i filtry. Zapytanie wyszukiwania pełnotekstowego wyszukuje co najmniej jeden termin w `IsSearchable` polach w indeksie. Filtr jest wyrażeniem logicznym, które jest oceniane względem `IsFilterable` pól w indeksie. Możesz użyć wyszukiwania pełnotekstowego i filtrów razem lub oddzielnie.

    Zarówno operacja wyszukiwania, jak i filtrowania są wykonywane przy użyciu metody `Documents.Search`. Zapytanie wyszukiwania może zostać przekazane za pośrednictwem parametru `searchText`, natomiast wyrażenie filtrowania może zostać przekazane za pośrednictwem właściwości `Filter` klasy `SearchParameters`. Aby filtrować bez wyszukiwania, po prostu przekaż wartość `"*"` jako parametr `searchText`. Aby wyszukiwać bez filtrowania, pozostaw nieustawioną właściwość `Filter` lub nie przekazuj jej w wystąpieniu obiektu `SearchParameters`.

1. W Program.cs, w obszarze głównym, Usuń komentarz z wierszy dla "3-Search". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Rozwiązanie zostało zakończone. Naciśnij klawisz F5, aby ponownie skompilować aplikację i uruchomić program w całości. 

    Dane wyjściowe zawierają te same wiadomości jak wcześniej, z dodaniem informacji o zapytaniu i wyników.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W przypadku pracy w ramach własnej subskrypcji warto sprawdzić po zakończeniu projektu, czy dalej potrzebuje się utworzonych zasobów. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start w języku C# przepracowałeś szereg zadań, aby utworzyć indeks, załadować go wraz z dokumentami i uruchamiać zapytania. Na różnych etapach wprowadziliśmy skróty upraszczające kod umożliwiający odczytywanie i zrozumienie. Jeśli masz doświadczenie z podstawowymi pojęciami, zalecamy użycie następnego artykułu w celu eksplorowania alternatywnych metod i koncepcji, które pomogą Ci pogłębić swoją wiedzę. 

Przykładowy kod i indeks są rozwiniętymi wersjami tego elementu. Następny przykład dodaje kolekcję pokojów, używa różnych klas i akcji i ma bliższy wpływ na działanie przetwarzania.

> [!div class="nextstepaction"]
> [Jak opracowywać w programie .NET](search-howto-dotnet-sdk.md)

Chcesz zoptymalizować i zapisać wydatki na chmurę?

> [!div class="nextstepaction"]
> [Rozpocznij analizowanie kosztów za pomocą Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)