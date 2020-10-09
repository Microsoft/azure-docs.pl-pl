---
title: Samouczek języka C# — indeksowanie wielu źródeł danych platformy Azure
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak importować dane z wielu źródeł danych do jednego indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu indeksatorów. Ten samouczek i przykładowy kod są w języku C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 06b80b5fe14a7a913d8ad8454c6568b04fe01c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819791"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Samouczek: indeksowanie z wielu źródeł danych przy użyciu zestawu .NET SDK

Usługa Azure Wyszukiwanie poznawcze umożliwia importowanie, analizowanie i indeksowanie danych z wielu źródeł danych w jednym indeksie wyszukiwania skonsolidowanego. Obsługuje to sytuacje, w których dane strukturalne są agregowane przy użyciu mniej strukturalnych lub nawet danych zwykłego tekstu z innych źródeł, takich jak tekst, HTML lub dokumenty JSON.

W tym samouczku opisano sposób indeksowania danych hotelu ze źródła danych Azure Cosmos DB i scalania z informacjami o pokoju hotelowym pobranymi z dokumentów Blob Storage platformy Azure. Wynik będzie połączonym indeksem wyszukiwania hotelowego zawierającym złożone typy danych.

Ten samouczek używa języka C# i [zestawu .NET SDK](/dotnet/api/overview/azure/search). W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Przekazywanie przykładowych danych i tworzenie źródeł danych
> * Identyfikowanie klucza dokumentu
> * Definiowanie i tworzenie indeksu
> * Indeksuj dane hotelu z Azure Cosmos DB
> * Scalanie danych pokoju hotelowego z magazynu obiektów BLOB

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Utwórz](search-create-service-portal.md) lub [Znajdź istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby akceptować nowe zasoby.

## <a name="download-files"></a>Pobieranie plików

Kod źródłowy dla tego samouczka znajduje się w repozytorium GitHub [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) w folderze z [wieloma źródłami danych](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) .

## <a name="1---create-services"></a>1 — Tworzenie usług

Ten samouczek używa platformy Azure Wyszukiwanie poznawcze do indeksowania i zapytań, Azure Cosmos DB dla jednego zestawu danych i magazynu obiektów blob platformy Azure dla drugiego zestawu danych. 

Jeśli to możliwe, Utwórz wszystkie usługi w tym samym regionie i grupie zasobów, aby umożliwić bliskość i łatwość zarządzania. W tym przypadku usługi mogą znajdować się w dowolnym regionie.

Ten przykład używa dwóch małych zestawów danych, które opisują siedem fikcyjnych hoteli. Jeden zestaw opisuje same hotele i zostanie załadowany do bazy danych Azure Cosmos DB. Drugi zestaw zawiera szczegółowe informacje o pokoju hotelowym i jest dostarczany jako siedem oddzielnych plików JSON do przekazania do Blob Storage platformy Azure.

### <a name="start-with-cosmos-db"></a>Rozpocznij od Cosmos DB

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie przejdź do strony przegląd konta Azure Cosmos DB.

1. Wybierz pozycję **Eksplorator danych** a następnie wybierz pozycję **Nowa baza danych**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Tworzenie nowej bazy danych" border="false":::

1. Wprowadź nazwę **"pokoje hotelowe — baza danych"**. Zaakceptuj wartości domyślne pozostałych ustawień.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Tworzenie nowej bazy danych" border="false":::

1. Utwórz nowy kontener. Użyj właśnie utworzonej bazy danych. Wprowadź **Hotele** dla nazwy kontenera, a następnie użyj **/HotelId** dla klucza partycji.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Tworzenie nowej bazy danych" border="false":::

1. Wybierz pozycję **elementy** w **hotelach**, a następnie kliknij pozycję **Przekaż element** na pasku poleceń. Przejdź do, a następnie wybierz plik **cosmosdb/HotelsDataSubset_CosmosDb.js** w folderze projektu.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Tworzenie nowej bazy danych" border="false":::

1. Użyj przycisku Odśwież, aby odświeżyć widok elementów w kolekcji hoteli. Powinien być widoczny na liście siedem nowych dokumentów bazy danych.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Zaloguj się do [Azure Portal](https://portal.azure.com), przejdź do konta usługi Azure Storage, kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener**.

1. [Utwórz kontener obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md) o nazwie **pokoje hotelowe** , aby zapisać przykładowe pliki JSON pokoju hotelowego. Można ustawić poziom dostępu publicznego na dowolną z jego prawidłowych wartości.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Tworzenie nowej bazy danych" border="false":::

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń. Przejdź do folderu zawierającego pliki przykładowe. Zaznacz wszystkie z nich, a następnie kliknij przycisk **Przekaż**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Tworzenie nowej bazy danych" border="false":::

Po zakończeniu przekazywania pliki powinny pojawić się na liście kontenera danych.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Trzeci składnik to Wyszukiwanie poznawcze platformy Azure, który można [utworzyć w portalu](search-create-service-portal.md). Aby ukończyć ten przewodnik, możesz skorzystać z warstwy Bezpłatna. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Pobierz klucz API i adres URL administratora dla usługi Azure Wyszukiwanie poznawcze

Aby można było korzystać z usługi Azure Wyszukiwanie poznawcze, wymagany jest adres URL usługi i klucz dostępu. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   Pobierz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

   :::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Tworzenie nowej bazy danych" border="false":::

Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="2---set-up-your-environment"></a>2 — Konfigurowanie środowiska

1. Uruchom program Visual Studio 2019, a następnie w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet** , a następnie **Zarządzaj pakietami NuGet dla rozwiązania.**... 

1. Na karcie **Przeglądaj** Znajdź i zainstaluj **program Microsoft. Azure. Search** (w wersji 9.0.1 lub nowszej). Aby ukończyć instalację, trzeba będzie kliknąć dodatkowe okna dialogowe.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Tworzenie nowej bazy danych" border="false":::

1. Wyszukaj **Microsoft.Extensions.Configuration.Jsw** pakiecie NuGet i zainstaluj go również.

1. Otwórz plik rozwiązania **AzureSearchMultipleDataSources. sln**.

1. W Eksplorator rozwiązań Edytuj **appsettings.js** pliku, aby dodać informacje o połączeniu.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Pierwsze dwa wpisy używają adresu URL i kluczy administratora usługi Azure Wyszukiwanie poznawcze. Załóżmy na przykład, `https://mydemo.search.windows.net` że nazwa usługi jest określona `mydemo` .

Następne wpisy określają nazwy kont i informacje o parametrach połączenia dla Blob Storage platformy Azure i Azure Cosmos DB źródeł danych.

## <a name="3---map-key-fields"></a>3 — Mapuj pola kluczy

Scalanie zawartości wymaga, aby oba strumienie danych były zgodne z tymi samymi dokumentami w indeksie wyszukiwania. 

W usłudze Azure Wyszukiwanie poznawcze pole klucza jednoznacznie identyfikuje każdy dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu `Edm.String` . Pole klucza musi być obecne dla każdego dokumentu w źródle danych, które jest dodawane do indeksu. (W rzeczywistości jest to jedyne pole wymagane).

Podczas indeksowania danych z wielu źródeł danych upewnij się, że każdy przychodzący wiersz lub dokument zawiera wspólny klucz dokumentu do scalania danych z dwóch fizycznie oddzielnych dokumentów źródłowych do nowego dokumentu wyszukiwania w połączonym indeksie. 

Często wymaga to pewnej planowania z góry, aby zidentyfikować istotny klucz dokumentu dla indeksu, i upewnić się, że istnieje w obu źródłach danych. W tym pokazie `HotelId` klucz dla każdego hotelu w Cosmos DB jest również obecny w obiektach Blob w formacie JSON w usłudze BLOB Storage.

Indeksatory usługi Azure Wyszukiwanie poznawcze mogą używać mapowań pól do zmiany nazw i nawet ponownego formatowania pól danych w procesie indeksowania, dzięki czemu dane źródłowe mogą być kierowane do poprawnego pola indeksu. Na przykład, w Cosmos DB, identyfikator hotelu jest wywoływany **`HotelId`** . Jednak w plikach obiektów BLOB JSON dla pokojów hotelowych identyfikator hotelu ma nazwę **`Id`** . Program obsługuje to przez mapowanie **`Id`** pola z obiektów BLOB do **`HotelId`** pola klucza w indeksie.

> [!NOTE]
> W większości przypadków automatycznie generowane klucze dokumentów, takie jak te utworzone domyślnie przez niektórych indeksatorów, nie sprawiają, że są to dobre klucze dokumentów dla połączonych indeksów. Ogólnie rzecz biorąc, należy użyć znaczącej, unikatowej wartości klucza, która już istnieje w programie, lub można ją łatwo dodać do źródła danych.

## <a name="4---explore-the-code"></a>4 — Eksplorowanie kodu

Po wprowadzeniu ustawień danych i konfiguracji, przykładowy program w **AzureSearchMultipleDataSources. sln** powinien być gotowy do kompilowania i uruchamiania.

Ta prosta aplikacja konsolowa języka C#/.NET wykonuje następujące zadania:

* Tworzy nowy indeks oparty na strukturze danych klasy hotelu języka C# (która odwołuje się również do klas adresów i pokojów).
* Tworzy nowe źródło danych i indeksator, który mapuje Azure Cosmos DB dane na pola indeksu. Są to obiekty na platformie Azure Wyszukiwanie poznawcze.
* Uruchamia indeksator w celu załadowania danych hotelu z Cosmos DB.
* Tworzy drugie źródło danych i indeksator, który mapuje dane obiektów BLOB JSON do pól indeksu.
* Uruchamia drugi indeksator, aby załadować dane pokojów z magazynu obiektów BLOB.

 Przed uruchomieniem programu Poświęć minutę na przeanalizowanie kodu i definicji indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

  + **Hotel.cs** zawiera schemat definiujący indeks
  + **Program.cs** zawiera funkcje, które tworzą indeks wyszukiwanie poznawcze platformy Azure, źródła danych i indeksatory, a następnie Ładuj połączone wyniki do indeksu.

### <a name="create-an-index"></a>Tworzenie indeksu

Ten przykładowy program używa zestawu .NET SDK do definiowania i tworzenia indeksu Wyszukiwanie poznawcze platformy Azure. Wykorzystuje klasę [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder) , aby wygenerować strukturę indeksu z klasy modelu danych języka C#.

Model danych jest definiowany przez klasę hotelu, która również zawiera odwołania do klas Address i Room. FieldBuilder przechodzi przez wiele definicji klas w celu wygenerowania złożonej struktury danych dla indeksu. Tagi metadanych są używane do definiowania atrybutów poszczególnych pól, na przykład czy można je przeszukiwać czy sortować.

Poniższe fragmenty kodu z pliku **Hotel.cs** pokazują, jak można określić jedno pole i odwołanie do innej klasy modelu danych.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

W pliku **program.cs** indeks jest zdefiniowany przy użyciu nazwy i kolekcji pól wygenerowanej przez `FieldBuilder.BuildForType<Hotel>()` metodę, a następnie utworzony w następujący sposób:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Tworzenie Azure Cosmos DB źródła danych i indeksatora

Następny program główny zawiera logikę, aby utworzyć Azure Cosmos DB źródło danych dla danych hoteli.

Najpierw łączy nazwę bazy danych Azure Cosmos DB z parametrami połączenia. Następnie definiuje obiekt źródła danych, w tym ustawienia specyficzne dla źródeł Azure Cosmos DB, takich jak Właściwość [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Po utworzeniu źródła danych program skonfiguruje Azure Cosmos DB indeksatora o nazwie **pokoje hotelowe-Cosmos-Indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Program usunie wszystkie istniejące indeksatory o tej samej nazwie przed utworzeniem nowego, w przypadku, gdy chcesz uruchomić ten przykład więcej niż raz.

Ten przykład definiuje harmonogram dla indeksatora, tak aby był uruchamiany raz dziennie. Możesz usunąć Właściwość Schedule z tego wywołania, jeśli nie chcesz, aby indeksator został automatycznie uruchomiony ponownie w przyszłości.

### <a name="index-azure-cosmos-db-data"></a>Indeksuj Azure Cosmos DB dane

Po utworzeniu źródła danych i indeksatora kod, który uruchamia indeksator, jest krótki:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Ten przykład zawiera prosty blok try-catch do zgłaszania błędów, które mogą wystąpić podczas wykonywania.

Po uruchomieniu indeksatora Azure Cosmos DB indeks wyszukiwania będzie zawierał pełny zestaw przykładowych dokumentów hotelowych. Jednak pole pokojów dla każdego hotelu będzie pustą tablicę, ponieważ źródło danych Azure Cosmos DB nie zawierało szczegółów pokoju. Następnie program pobierze z usługi BLOB Storage w celu załadowania i scalenia danych pokoju.

### <a name="create-blob-storage-data-source-and-indexer"></a>Tworzenie źródła danych i indeksatora magazynu obiektów BLOB

Aby uzyskać szczegółowe informacje o pokoju, program najpierw konfiguruje źródło danych magazynu obiektów blob, aby odwołać się do zestawu poszczególnych plików obiektów BLOB JSON.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Po utworzeniu źródła danych program skonfiguruje obiekt BLOB indeksatora o nazwie **sale-Indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Obiekty blob JSON zawierają pole klucza o nazwie **`Id`** zamiast **`HotelId`** . Kod używa klasy, `FieldMapping` Aby określić indeksator do skierowania **`Id`** wartości pola do **`HotelId`** klucza dokumentu w indeksie.

Indeksatory magazynu obiektów BLOB mogą używać parametrów, które identyfikują tryb analizowania, który ma być używany. Tryb analizy różni się dla obiektów BLOB reprezentujących pojedynczy dokument lub wiele dokumentów w ramach tego samego obiektu BLOB. W tym przykładzie każdy obiekt BLOB reprezentuje pojedynczy dokument indeksu, więc kod używa `IndexingParameters.ParseJson()` parametru.

Aby uzyskać więcej informacji na temat parametrów analizy indeksatora dla obiektów BLOB JSON, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md). Aby uzyskać więcej informacji na temat określania tych parametrów przy użyciu zestawu .NET SDK, zobacz Klasa [IndexerParametersExtension](/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) .

Program usunie wszystkie istniejące indeksatory o tej samej nazwie przed utworzeniem nowego, w przypadku, gdy chcesz uruchomić ten przykład więcej niż raz.

Ten przykład definiuje harmonogram dla indeksatora, tak aby był uruchamiany raz dziennie. Możesz usunąć Właściwość Schedule z tego wywołania, jeśli nie chcesz, aby indeksator został automatycznie uruchomiony ponownie w przyszłości.

### <a name="index-blob-data"></a>Indeksuj dane obiektów BLOB

Po utworzeniu źródła danych i indeksatora magazynu obiektów BLOB kod, który uruchamia indeksator, jest prosty:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Ponieważ indeks został już wypełniony danymi hotelu z bazy danych Azure Cosmos DB, indeksator obiektu BLOB aktualizuje istniejące dokumenty w indeksie i dodaje szczegóły pokoju.

> [!NOTE]
> Jeśli masz te same pola, które nie są kluczami w obu źródłach danych, a dane w tych polach nie są zgodne, indeks będzie zawierał wartości z dowolnego indeksatora. W naszym przykładzie oba źródła danych zawierają pole **hotelname** . Jeśli z jakiegoś powodu dane w tym polu różnią się w przypadku dokumentów o tej samej wartości klucza, dane **hoteluname** ze źródła danych, które było ostatnio indeksowane, będą wartościami przechowywanymi w indeksie.

## <a name="5---search"></a>5 — wyszukiwanie

Można eksplorować wypełniony indeks wyszukiwania po uruchomieniu programu przy użyciu [**Eksploratora wyszukiwania**](search-explorer.md) w portalu.

W Azure Portal Otwórz stronę **Przegląd** usługi wyszukiwania i Znajdź na liście **indeksów** pozycję **Pokój z przemieszczeń hotelowych** .

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Tworzenie nowej bazy danych" border="false":::

Na liście kliknij indeks pokojów hotelowych — przykład. Zostanie wyświetlony interfejs Eksploratora wyszukiwania dla indeksu. Wprowadź zapytanie dla terminu takiego jak "możliwość zaprojektowania". W wynikach powinien być widoczny co najmniej jeden dokument, a ten dokument powinien zawierać listę obiektów Room w tablicy pokojów.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

W przypadku wczesnych eksperymentalnych etapów tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod dla tego samouczka sprawdza istniejące obiekty i usuwa je, aby można było ponownie uruchomić kod.

Możesz również użyć portalu, aby usunąć indeksy, indeksatory i źródła danych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku wszystkie zasoby lub grupy zasobów w okienku nawigacji po lewej stronie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz koncepcję pozyskiwania danych z wielu źródeł, przyjrzyjmy się bliżej konfiguracji indeksatora, rozpoczynając od Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurowanie indeksatora Azure Cosmos DB](search-howto-index-cosmosdb.md)