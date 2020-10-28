---
title: Używanie Azure.Search.Documents (v11) w środowisku .NET
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak tworzyć obiekty wyszukiwania i zarządzać nimi w aplikacji .NET przy użyciu języka C# i biblioteki klienta Azure.Search.Documents (v11). Fragmenty kodu przedstawiają łączenie się z usługą, Tworzenie indeksów i zapytań.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 47cface4efbf38f2cec630745d498db674c936d3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791957"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Jak używać uments Azure.Search.Docw aplikacji .NET w języku C#

W tym artykule wyjaśniono, jak tworzyć obiekty wyszukiwania i zarządzać nimi za pomocą języka C# i biblioteki klienta [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (wersja 11).

## <a name="about-version-11"></a>Informacje o wersji 11

Zestaw Azure SDK dla platformy .NET dodaje nową bibliotekę kliencką [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) z zespołu zestawu Azure SDK, która jest funkcjonalnie równoważna z [Microsoft. Azure. Search](/dotnet/api/overview/azure/search/client10) , ale wykorzystuje typowe podejścia i konwencje, jeśli ma to zastosowanie. Niektóre przykłady obejmują [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) uwierzytelnianie klucza i [System.Text.Jsna. Serializacja](/dotnet/api/system.text.json.serialization) serializacji JSON.

Podobnie jak w przypadku poprzednich wersji, można użyć tej biblioteki do:

+ Tworzenie i zarządzanie indeksami wyszukiwania, źródłami danych, indeksatorami, umiejętności i mapami synonimów
+ Ładowanie i zarządzanie dokumentami wyszukiwania w indeksie
+ Wykonaj zapytania, bez konieczności rozpatrywania szczegółów protokołu HTTP i JSON

Biblioteka jest dystrybuowana jako pojedynczy [ pakiet NuGetAzure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents/), który obejmuje wszystkie interfejsy API używane do programistycznego dostępu do usługi wyszukiwania.

Biblioteka klienta definiuje klasy takie jak `SearchIndex` , `SearchField` , i `SearchDocument` , jak również operacje takie jak `SearchIndexClient.CreateIndex` i `SearchClient.Search` `SearchIndexClient` `SearchClient` klasy i. Te klasy są zorganizowane w następujące przestrzenie nazw:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (wersja 11) wskazuje wersję [ `2020-06-30` interfejsu API REST platformy Azure wyszukiwanie poznawcze](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30). 

Biblioteka kliencka nie zapewnia [operacji zarządzania usługami](/rest/api/searchmanagement/), takich jak tworzenie i skalowanie usług wyszukiwania oraz zarządzanie kluczami interfejsu API. Jeśli potrzebujesz zarządzać zasobami wyszukiwania z poziomu aplikacji .NET, użyj biblioteki [Microsoft. Azure. Management. Search](/dotnet/api/overview/azure/search/management) w zestawie Azure SDK dla platformy .NET.

## <a name="upgrade-to-v11"></a>Uaktualnij do v11

Jeśli używasz poprzedniej wersji zestawu .NET SDK i chcesz przeprowadzić uaktualnienie do bieżącej ogólnie dostępnej wersji, zobacz [uaktualnianie do platformy Azure wyszukiwanie poznawcze .NET SDK wersja 11](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>Wymagania dotyczące zestawu SDK

+ Program Visual Studio 2019 lub nowszy.

+ Twoja usługa Wyszukiwanie poznawcze platformy Azure. Aby można było korzystać z zestawu SDK, potrzebna jest nazwa usługi i jeden lub więcej kluczy interfejsu API. [Utwórz usługę w portalu,](search-create-service-portal.md) Jeśli jej nie masz.

+ Pobierz [pakietAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents) przy użyciu **narzędzi**  >  **Menedżer pakietów NuGet** zarządzanie pakietami  >  **NuGet dla rozwiązania** w programie Visual Studio. Wyszukaj nazwę pakietu `Azure.Search.Documents` .

Zestaw Azure SDK dla platformy .NET jest zgodny z [.NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), co oznacza, że .NET Framework 4.6.1 i .net Core 2,0 jako wymagania minimalne.

## <a name="example-application"></a>Przykładowa aplikacja

Ten artykuł "Naucz się na przykład", opierając się na przykładowym przykładzie kodu [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo/v11) w witrynie GitHub, aby zilustrować podstawowe koncepcje na platformie Azure wyszukiwanie poznawcze — w tym informacje o sposobie tworzenia, ładowania i wykonywania zapytań względem indeksu wyszukiwania.

W pozostałej części tego artykułu założono, że nowy indeks o nazwie "Hotele" został wypełniony kilkoma dokumentami z kilkoma zapytaniami, które pasują do wyników.

Poniżej znajduje się główny program przedstawiający ogólny przepływ:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Następnie jest częściowym zrzutem ekranu danych wyjściowych, przy założeniu, że ta aplikacja jest uruchamiana z prawidłową nazwą usługi i kluczami interfejsu API:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Konsola. WriteLine dane wyjściowe z programu przykładowego":::

### <a name="client-types"></a>Typy klientów

Biblioteka klienta używa trzech typów klienta dla różnych operacji: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) do tworzenia, aktualizowania lub usuwania indeksów, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) ładowania lub wykonywania zapytań względem indeksu oraz [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) do pracy z indeksatorami i umiejętności. Ten artykuł koncentruje się na dwóch pierwszych. 

Co najmniej wszyscy klienci wymagają nazwy usługi lub punktu końcowego oraz klucza interfejsu API. Często te informacje są dostępne w pliku konfiguracji, podobnie jak w `appsettings.json` pliku [przykładowej aplikacji DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Aby odczytywać plik konfiguracji, Dodaj `using Microsoft.Extensions.Configuration;` go do programu.

Poniższa instrukcja tworzy klient indeksu służący do tworzenia, aktualizowania lub usuwania indeksów. Pobiera punkt końcowy wyszukiwania i klucz interfejsu API administratora.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

W następnej instrukcji jest tworzony klient wyszukiwania używany do ładowania dokumentów lub uruchamiania zapytań. `SearchClient` wymaga indeksu. Do załadowania dokumentów potrzebny będzie klucz interfejsu API administratora, ale do uruchamiania zapytań można użyć klucza interfejsu API zapytania. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Jeśli podano nieprawidłowy klucz dla operacji importowania (na przykład klucz zapytania, w którym jest wymagany klucz administratora), program `SearchClient` zgłosi `CloudException` komunikat o błędzie "zabronione" przy pierwszym wywołaniu metody operacji. W takim przypadku należy dokładnie sprawdzić klucz interfejsu API.
>

### <a name="deleting-the-index"></a>Usuwanie indeksu

We wczesnych etapach tworzenia warto dodać [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) instrukcję usuwania indeksu w toku, aby można było go odtworzyć ze zaktualizowaną definicją. Przykładowy kod dla platformy Azure Wyszukiwanie poznawcze często zawiera krok usuwania, aby można było ponownie uruchomić przykład.

Następujące wywołania wiersza `DeleteIndexIfExists` :

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Ta metoda używa danego `SearchIndexClient` do sprawdzenia, czy indeks istnieje, a jeśli tak, usuwa:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> Przykładowy kod w tym artykule używa metod synchronicznych dla uproszczenia, ale należy użyć metod asynchronicznych we własnych aplikacjach, aby zapewnić ich skalowalność i szybkość reakcji. Na przykład w powyższej metodzie można użyć [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) zamiast [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Tworzenie indeksu

Za pomocą [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) programu można utworzyć indeks. 

Poniższa metoda tworzy nowy [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) obiekt z listą [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) obiektów, które definiują schemat nowego indeksu. Każde pole ma nazwę, typ danych i kilka atrybutów definiujących zachowanie wyszukiwania. 

Pola można definiować z klasy modelu przy użyciu [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . `FieldBuilder`Klasa używa odbicia, aby utworzyć listę `SearchField` obiektów dla indeksu, sprawdzając właściwości publiczne i atrybuty danej `Hotel` klasy modelu. Przejdziemy bliżej `Hotel` klasy później.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Oprócz pól można także dodać profile oceniania, sugerują lub opcje CORS do indeksu (te parametry są pomijane z przykładu dla zwięzłości). Więcej informacji na temat obiektu SearchIndex i jego części składowych można znaleźć na [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) liście właściwości, a także w [dokumentacji interfejsu API REST](/rest/api/searchservice/).

> [!NOTE]
> Możesz zawsze utworzyć listę `Field` obiektów bezpośrednio zamiast używać w `FieldBuilder` razie konieczności. Na przykład nie trzeba używać klasy modelu lub może być konieczne użycie istniejącej klasy modelu, która nie ma być modyfikowana przez dodanie atrybutów.
>

### <a name="call-createindex-in-main"></a>Wywołaj metodę "noindex" w Main ()

`Main` Tworzy nowy indeks "Hotele" przez wywołanie powyższej metody:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Używanie klasy modelu do reprezentowania danych

Przykład DotNetHowTo używa klas modelu dla struktur danych [hotelu](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [Address](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)i [Room](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) . `Hotel` odwołania `Address` , typ złożony pojedynczego poziomu (pole wieloczęściowe) i `Room` (Kolekcja pól wieloczęściowych).

Możesz użyć tych typów do utworzenia i załadowania indeksu oraz do struktury odpowiedzi z zapytania:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="field-definitions"></a>Definicje pól

Model danych w programie .NET i odpowiedni schemat indeksu powinny obsługiwać środowisko wyszukiwania, które chcesz przekazać użytkownikowi końcowemu. Każdy obiekt najwyższego poziomu w programie .NET, taki jak dokument wyszukiwania w indeksie wyszukiwania, odpowiada wynikowi wyszukiwania, który jest obecny w interfejsie użytkownika. Na przykład w aplikacji wyszukiwania w hotelu użytkownicy końcowi mogą chcieć przeszukiwać według nazwy hotelu, funkcji hotelu lub cech charakterystycznych w konkretnym pokoju. 

W ramach każdej klasy pole jest zdefiniowane z typem danych i atrybutami, które określają, w jaki sposób jest używany. Nazwy każdej właściwości publicznej w każdej klasie są mapowane do pola o tej samej nazwie w definicji indeksu. 

Zapoznaj się z poniższym fragmentem kodu, który pobiera kilka definicji pól z klasy hotelu. Zwróć uwagę, że adres i pokoje są typami C# z własnymi definicjami klas (zapoznaj się z przykładowym kodem, jeśli chcesz je wyświetlić). Oba są typami złożonymi. Aby uzyskać więcej informacji, zobacz [jak modelować typy złożone](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Wybieranie klasy pól

Podczas definiowania pól można użyć [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) klasy bazowej lub użyć pochodnych modeli pomocników, które służą jako "Szablony" z wstępnie skonfigurowanymi właściwościami.

Dokładnie jedno pole w indeksie musi być kluczem dokumentu ( `IsKey = true` ). Musi być ciągiem i musi jednoznacznie identyfikować każdy dokument. Jest to również wymagane `IsHidden = true` , co oznacza, że nie może być widoczne w wynikach wyszukiwania.

| Typ pola | Opis i użycie |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Klasa bazowa, z większością właściwości ustawioną na wartość null, z wyjątkiem tego, `Name` który jest wymagany, i `AnalyzerName` które domyślnie są standardowymi Lucene. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Model pomocnika. Może być dowolnego typu danych, jest zawsze niemożliwy do przeszukania (jest ignorowany w przypadku kwerend wyszukiwania pełnotekstowego) i można je pobrać (nie jest ukryte). Inne atrybuty są domyślnie wyłączone, ale można je włączyć. Możesz użyć `SimpleField` dla identyfikatorów dokumentu lub pól używanych tylko w filtrach, aspektach lub profilach oceniania. Jeśli tak, pamiętaj, aby zastosować wszelkie atrybuty, które są niezbędne dla tego scenariusza, `IsKey = true` na przykład identyfikator dokumentu. Aby uzyskać więcej informacji, zobacz [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) w kodzie źródłowym. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Model pomocnika. Musi być ciągiem i jest zawsze możliwy do przeszukiwania i pobierania. Inne atrybuty są domyślnie wyłączone, ale można je włączyć. Ponieważ ten typ pola jest możliwy do przeszukiwania, obsługuje synonimy i pełen uzupełnianie właściwości analizatora. Aby uzyskać więcej informacji, zobacz [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) w kodzie źródłowym. |

Bez względu na to, czy używasz podstawowego `SearchField` interfejsu API, czy jednego z modeli pomocników, musisz jawnie włączyć atrybuty Filter, facet i Sort. Na przykład, [IsFiltered](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [issorting](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)i [iskrojuing](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) musi być jawnie przypisany, jak pokazano w powyższym przykładzie.

#### <a name="adding-field-attributes"></a>Dodawanie atrybutów pola

Zwróć uwagę, jak każde pole jest dekoracyjne z atrybutami, takimi jak `IsFilterable` ,, `IsSortable` `IsKey` i `AnalyzerName` . Te atrybuty są mapowane bezpośrednio do [odpowiednich atrybutów pól w indeksie wyszukiwanie poznawcze platformy Azure](/rest/api/searchservice/create-index). `FieldBuilder`Klasa używa tych właściwości do konstruowania definicji pól dla indeksu.

#### <a name="field-type-mapping"></a>Mapowanie typu pola

Typy .NET właściwości są mapowane na równoważne typy pól w definicji indeksu. Na przykład właściwość ciągu `Category` jest mapowana na pole `category` mające typ `Edm.String`. Istnieją podobne mapowania typu między `bool?` , `Edm.Boolean` ,, `DateTimeOffset?` i `Edm.DateTimeOffset` tak dalej. 

Czy wiesz, że właściwość została zadana `SmokingAllowed` ?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

`JsonIgnore`Atrybut dla tej właściwości informuje, `FieldBuilder` aby nie serializować go do indeksu jako pole.  Jest to świetny sposób tworzenia właściwości obliczeniowych po stronie klienta, których można użyć jako pomocników w aplikacji.  W tym przypadku `SmokingAllowed` właściwość odzwierciedla, czy którykolwiek `Room` z `Rooms` kolekcji pozwala na palenie. Jeśli wszystkie mają wartość false, oznacza to, że cały hotel nie zezwala na palenie.

## <a name="load-an-index"></a>Ładowanie indeksu

Następny krok w `Main` wypełnia nowo utworzony indeks "hoteli". Ta populacja indeksu jest wykonywana w następującej metodzie: (część kodu została zastąpiona "..." na potrzeby ilustracji. Zobacz pełne przykładowe rozwiązanie dla kodu populacji pełnej danych.)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                HotelName = "Secret Point Motel",
                ...
                Address = new Address()
                {
                    StreetAddress = "677 5th Ave",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Budget Room, 1 Queen Bed (Cityside)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (City View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                HotelName = "Twin Dome Motel",
                ...
                {
                    StreetAddress = "140 University Town Center Dr",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Suite, 2 Double Beds (Mountain View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Standard Room, 1 Queen Bed (City View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Budget Room, 1 King Bed (Waterfront View)",
                        ...
                    }
                }
            }),
        IndexDocumentsAction.Upload(
            new Hotel()
            {
                HotelId = "3",
                HotelName = "Triple Landscape Hotel",
                ...
                Address = new Address()
                {
                    StreetAddress = "3393 Peachtree Rd",
                    ...
                },
                Rooms = new Room[]
                {
                    new Room()
                    {
                        Description = "Standard Room, 2 Queen Beds (Amenities)",
                        ...
                    },
                    new Room ()
                    {
                        Description = "Standard Room, 2 Double Beds (Waterfront View)",
                        ...
                    },
                    new Room()
                    {
                        Description = "Deluxe Room, 2 Double Beds (Cityside)",
                        ...
                    }
                }
            }
        };

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Ta metoda ma cztery części. Pierwszy tworzy tablicę trzech `Hotel` obiektów z 3 `Room` obiektami, które będą traktować jako dane wejściowe do przekazania do indeksu. Te dane są trwale kodowane dla uproszczenia. W rzeczywistej aplikacji dane prawdopodobnie będą pochodzić z zewnętrznego źródła danych, takiego jak baza danych SQL.

Druga część tworzy [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) dokument zawierający dokumenty. Należy określić operację, która ma zostać zastosowana do partii w momencie utworzenia, w tym przypadku przez wywołanie [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . Partia jest następnie przekazywana do indeksu Wyszukiwanie poznawcze platformy Azure przez [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) metodę.

> [!NOTE]
> W tym przykładzie właśnie przekazująmy dokumenty. Jeśli chcesz scalić zmiany w istniejących dokumentach lub usunąć dokumenty, możesz utworzyć partie, wywołując `IndexDocumentsAction.Merge` , `IndexDocumentsAction.MergeOrUpload` lub `IndexDocumentsAction.Delete` zamiast. Możesz również mieszać różne operacje w pojedynczej partii, wywołując `IndexBatch.New` , który pobiera kolekcję `IndexDocumentsAction` obiektów, z których każdy mówi, że platforma Azure wyszukiwanie poznawcze wykonać określoną operację na dokumencie. Każdą z nich można utworzyć `IndexDocumentsAction` przy użyciu własnej operacji poprzez wywołanie odpowiedniej metody, takiej jak `IndexDocumentsAction.Merge` , `IndexAction.Upload` , i tak dalej.
> 

Trzecią częścią tej metody jest blok catch, który obsługuje ważny przypadek błędu indeksowania. Jeśli usługa wyszukiwania nie może indeksować niektórych dokumentów w partii, `IndexBatchException` jest zgłaszany przez `IndexDocuments` . Ten wyjątek może wystąpić, jeśli indeksowane są dokumenty, gdy usługa jest mocno obciążona. **Zdecydowanie zalecamy jawną obsługę tego przypadku w kodzie.** Możesz opóźnić, a następnie ponowić indeksowanie dokumentów, których przetwarzanie zakończyło się niepowodzeniem, lub możesz zarejestrować błąd i kontynuować, tak jak w prezentowanym przykładzie. Możesz też wykonać inną akcję w zależności od wymagań spójności danych aplikacji.

Na koniec `UploadDocuments` Metoda opóźnia się przez dwie sekundy. Indeksowanie odbywa się asynchronicznie w usłudze wyszukiwania, więc przykładowa aplikacja musi czekać krótko, aby upewnić się, że dokumenty są dostępne do przeszukania. Tego typu opóźnienia są zazwyczaj konieczne tylko w przypadku pokazów, testów i przykładowych aplikacji.

### <a name="call-uploaddocuments-in-main"></a>Wywołaj UploadDocuments w Main ()

Poniższy fragment kodu konfiguruje wystąpienie [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) przy użyciu [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) metody indexClient. IndexClient używa klucza interfejsu API administratora na żądanie, które jest wymagane do ładowania lub odświeżania dokumentów.

Alternatywnym rozwiązaniem jest bezpośrednie wywoływanie `SearchClient` w kluczu interfejsu API administratora `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Uruchamianie zapytań

Najpierw należy skonfigurować, `SearchClient` który odczytuje punkt końcowy wyszukiwania i klucz interfejsu API zapytania z **appsettings.jsna** :

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

Następnie zdefiniuj metodę, która wysyła żądanie zapytania. 

Za każdym razem, gdy metoda wykonuje zapytanie, tworzy nowy [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) obiekt. Ten obiekt służy do określania dodatkowych opcji zapytania, takich jak sortowanie, filtrowanie, stronicowanie i aspektowanie. W tej metodzie ustawiamy `Filter` `Select` Właściwość,, i `OrderBy` dla różnych zapytań. Aby uzyskać więcej informacji na temat składni wyrażenia zapytania wyszukiwania, [prosta Składnia zapytania](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).

Następnym krokiem jest faktyczne wykonanie zapytania wyszukiwania. Uruchamianie wyszukiwania odbywa się przy użyciu `SearchClient.Search` metody. Dla każdego zapytania Przekaż tekst wyszukiwania, który ma być używany jako ciąg (lub `"*"` Jeśli nie ma tekstu wyszukiwania) oraz opcje wyszukiwania utworzone wcześniej. Określamy również `Hotel` jako parametr typu dla `SearchClient.Search` , który informuje zestaw SDK, aby deserializować dokumenty w wynikach wyszukiwania do obiektów typu `Hotel` .

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

Po trzecie Zdefiniuj metodę, która zapisuje odpowiedź, drukując każdy dokument do konsoli programu:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Wywołaj RunQueries w Main ()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Eksplorowanie konstrukcji zapytań

Przyjrzyjmy się bliżej każdej z tych zapytań. Oto kod do wykonania pierwszego zapytania:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

W tym przypadku przeszukujemy cały indeks słowa "Motel" w dowolnym polu z możliwością wyszukiwania i chcemy tylko pobrać nazwy hotelu, jak określono przez `Select` opcję. Oto wyniki:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

W drugim zapytaniu Użyj filtru w celu wybrania pokoju z nocną częstotliwością mniejszą niż $100. Zwróć tylko identyfikator hotelu i opis w wynikach:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

Powyższe zapytanie używa `$filter` wyrażenia OData, `Rooms/any(r: r/BaseRate lt 100)` Aby odfiltrować dokumenty w indeksie. Używa [dowolnego operatora](./search-query-odata-collection-operators.md) , aby zastosować "BaseRate lt 100" do każdego elementu w kolekcji pokojów. Aby uzyskać więcej informacji, zobacz [składnia filtru OData](./query-odata-filter-orderby-syntax.md).

W trzecim zapytaniu Znajdź dwie pierwsze Hotele, które zostały ostatnio renovatedne, i Pokaż nazwę hotelu i datę ostatniej odtworzenia. Oto kod: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

W ostatniej kwerendzie Znajdź wszystkie nazwy hoteli, które pasują do wyrazu "Hotel":

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

Ta sekcja zawiera wprowadzenie do zestawu SDK platformy .NET, ale nie można jej zatrzymać. W następnej sekcji zaproponowano dodatkowe zasoby, aby dowiedzieć się więcej o programowaniu przy użyciu usługi Azure Wyszukiwanie poznawcze.

## <a name="next-steps"></a>Następne kroki

+ Przejrzyj dokumentację dotyczącą interfejsu API [Azure.Search.Documents](/dotnet/api/azure.search.documents) i [interfejs API REST](/rest/api/searchservice/)

+ Przeglądaj inne przykłady kodu w oparciu o Azure.Search.Documents na [platformie Azure — wyszukiwanie-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) i [Search-Start-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ Zapoznaj się z [konwencjami nazewnictwa](/rest/api/searchservice/Naming-rules) , aby poznać reguły nazewnictwa różnych obiektów

+ Przejrzyj [obsługiwane typy danych](/rest/api/searchservice/Supported-data-types)