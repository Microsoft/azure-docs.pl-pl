---
title: Samouczek języka C# — indeksowanie wielu źródeł danych platformy Azure
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak importować dane z wielu źródeł danych do jednego indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu indeksatorów. Ten samouczek i przykładowy kod są w języku C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: df7dcf9859b6942662ae447af836f59985e2d11a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509506"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Samouczek: indeksowanie z wielu źródeł danych przy użyciu zestawu .NET SDK

Usługa Azure Wyszukiwanie poznawcze umożliwia importowanie, analizowanie i indeksowanie danych z wielu źródeł danych w jednym skonsolidowanym indeksie wyszukiwania. 

W tym samouczku w ramach zestawu Azure SDK dla platformy .NET jest używane [Azure.Search.Doc](/dotnet/api/overview/azure/search) Biblioteka kliencka, która umożliwia indeksowanie przykładowych danych hotelu z Azure Cosmos DB i scalanie z informacjami o pokoju hotelowym narysowanymi z dokumentów BLOB Storage platformy Azure. Wynik będzie połączonym indeksem wyszukiwania hotelowego zawierającym dokumenty hotelowe, z pomieszczeniami w postaci złożonych typów danych.

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
> * Przekazywanie przykładowych danych i tworzenie źródeł danych
> * Identyfikowanie klucza dokumentu
> * Definiowanie i tworzenie indeksu
> * Indeksuj dane hotelu z Azure Cosmos DB
> * Scalanie danych pokoju hotelowego z magazynu obiektów BLOB

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="overview"></a>Omówienie

W tym samouczku zostanie użyta Nowa Biblioteka kliencka [Azure.Search.Documents](/dotnet/api/overview/azure/search), wersja 11. x, aby utworzyć i uruchomić wiele indeksatorów. W tym samouczku zostaną skonfigurowane dwa źródła danych platformy Azure, dzięki czemu można skonfigurować indeksator, który pobiera z obu, aby wypełnić pojedynczy indeks wyszukiwania. Dwa zestawy danych muszą mieć wspólną wartość, aby można było obsługiwać scalanie. W tym przykładzie to pole jest IDENTYFIKATORem. Tak długo, jak w przypadku wspólnego pola do obsługi mapowania, indeksator może scalać dane z różnych zasobów: dane strukturalne z platformy Azure SQL, dane bez struktury z magazynu obiektów blob lub dowolna kombinacja [obsługiwanych źródeł danych](search-indexer-overview.md#supported-data-sources) na platformie Azure.

Gotowa wersja kodu w tym samouczku można znaleźć w następującym projekcie:

* [wiele źródeł danych/v11 (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v11)

Ten samouczek został zaktualizowany do korzystania z pakietu Azure.Search.Documents (wersja 11). Aby uzyskać wcześniejszą wersję zestawu .NET SDK, zobacz [przykład kodu Microsoft. Azure. Search (wersja 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources/v10) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Visual Studio](https://visualstudio.microsoft.com/)
+ [Pakiet NuGet Wyszukiwanie poznawcze platformy Azure (wersja 11. x)](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Utwórz](search-create-service-portal.md) lub [Znajdź istniejącą usługę wyszukiwania](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Możesz skorzystać z bezpłatnej usługi dla tego samouczka. Bezpłatna usługa wyszukiwania ogranicza do trzech indeksów, trzech indeksatorów i trzech źródeł danych. W ramach tego samouczka tworzony jest jeden element każdego z tych typów. Przed rozpoczęciem upewnij się, że masz miejsce w usłudze, aby akceptować nowe zasoby.

## <a name="1---create-services"></a>1 — Tworzenie usług

Ten samouczek używa platformy Azure Wyszukiwanie poznawcze do indeksowania i zapytań, Azure Cosmos DB dla jednego zestawu danych i magazynu obiektów blob platformy Azure dla drugiego zestawu danych. 

Jeśli to możliwe, Utwórz wszystkie usługi w tym samym regionie i grupie zasobów, aby umożliwić bliskość i łatwość zarządzania. W tym przypadku usługi mogą znajdować się w dowolnym regionie.

Ten przykład używa dwóch małych zestawów danych, które opisują siedem fikcyjnych hoteli. Jeden zestaw opisuje same hotele i zostanie załadowany do bazy danych Azure Cosmos DB. Drugi zestaw zawiera szczegółowe informacje o pokoju hotelowym i jest dostarczany jako siedem oddzielnych plików JSON do przekazania do Blob Storage platformy Azure.

### <a name="start-with-cosmos-db"></a>Rozpocznij od Cosmos DB

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie przejdź do strony przegląd konta Azure Cosmos DB.

1. Wybierz pozycję **Eksplorator danych** a następnie wybierz pozycję **Nowa baza danych**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Tworzenie nowej bazy danych" border="false":::

1. Wprowadź nazwę **"pokoje hotelowe — baza danych"**. Zaakceptuj wartości domyślne pozostałych ustawień.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Konfiguruj bazę danych" border="false":::

1. Utwórz nowy kontener. Użyj właśnie utworzonej bazy danych. Wprowadź **Hotele** dla nazwy kontenera, a następnie użyj **/HotelId** dla klucza partycji.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Dodawanie kontenera" border="false":::

1. Wybierz pozycję **elementy** w **hotelach**, a następnie kliknij pozycję **Przekaż element** na pasku poleceń. Przejdź do, a następnie wybierz plik **cosmosdb/HotelsDataSubset_CosmosDb.js** w folderze projektu.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Przekaż do kolekcji Azure Cosmos DB" border="false":::

1. Użyj przycisku Odśwież, aby odświeżyć widok elementów w kolekcji hoteli. Powinien być widoczny na liście siedem nowych dokumentów bazy danych.

1. Skopiuj parametry połączenia ze strony **klucze** do Notatnika. Będzie ona potrzebna do **appsettings.js** w późniejszym kroku. Jeśli nie używasz sugerowanej nazwy bazy danych "Hotel-sale-DB", skopiuj również nazwę bazy danych.

### <a name="azure-blob-storage"></a>Azure Blob Storage

1. Zaloguj się do [Azure Portal](https://portal.azure.com), przejdź do konta usługi Azure Storage, kliknij pozycję **obiekty blob**, a następnie kliknij pozycję **+ kontener**.

1. [Utwórz kontener obiektów BLOB](../storage/blobs/storage-quickstart-blobs-portal.md) o nazwie **pokoje hotelowe** , aby zapisać przykładowe pliki JSON pokoju hotelowego. Można ustawić poziom dostępu publicznego na dowolną z jego prawidłowych wartości.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Tworzenie kontenera obiektów blob" border="false":::

1. Po utworzeniu kontenera Otwórz go i wybierz pozycję **Przekaż** na pasku poleceń. Przejdź do folderu zawierającego pliki przykładowe. Zaznacz wszystkie z nich, a następnie kliknij przycisk **Przekaż**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Przekazywanie plików" border="false":::

1. Skopiuj nazwę konta magazynu i parametry połączenia ze strony **klucze dostępu** do Notatnika. W późniejszym kroku będą potrzebne obie wartości **appsettings.js** .

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Trzeci składnik to Wyszukiwanie poznawcze platformy Azure, który można [utworzyć w portalu](search-create-service-portal.md). 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopiowanie klucza API administratora — klucz i adres URL dla usługi Azure Wyszukiwanie poznawcze

Do uwierzytelnienia w usłudze wyszukiwania potrzebny jest adres URL usługi i klucz dostępu.

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

1. W obszarze **Ustawienia**  >  **klucze** Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="Pobieranie nazwy usługi i administratora oraz kluczy zapytań" border="false":::

Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="2---set-up-your-environment"></a>2 — Konfigurowanie środowiska

1. Uruchom program Visual Studio, a następnie w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet** , a następnie **Zarządzaj pakietami NuGet dla rozwiązania.**.. 

1. Na karcie **Przeglądaj** Znajdź i zainstaluj **Azure.Search.Documents** (wersja 11,0 lub nowsza). Aby ukończyć instalację, trzeba będzie kliknąć dodatkowe okna dialogowe.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Dodawanie bibliotek platformy Azure przy użyciu narzędzia NuGet" border="false":::

1. Wyszukaj **Microsoft.Extensions.Configwersja** i **Microsoft.Extensions.Configuration.Jsw** pakietach NuGet i zainstaluj je.

1. Otwórz plik rozwiązania **/v11/AzureSearchMultipleDataSources.sln**.

1. W Eksplorator rozwiązań Edytuj **appsettings.js** pliku, aby dodać informacje o połączeniu.  

    ```json
    {
      "SearchServiceUri": "<YourSearchServiceURL>",
      "SearchServiceAdminApiKey": "<YourSearchServiceAdminApiKey>",
      "BlobStorageAccountName": "<YourBlobStorageAccountName>",
      "BlobStorageConnectionString": "<YourBlobStorageConnectionString>",
      "CosmosDBConnectionString": "<YourCosmosDBConnectionString>",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Pierwsze dwa wpisy to adres URL i klucze administratora usługi wyszukiwania. Użyj pełnego punktu końcowego, na przykład: `https://mydemo.search.windows.net` .

Następne wpisy określają nazwy kont i informacje o parametrach połączenia dla Blob Storage platformy Azure i Azure Cosmos DB źródeł danych.

## <a name="3---map-key-fields"></a>3 — Mapuj pola kluczy

Scalanie zawartości wymaga, aby oba strumienie danych były zgodne z tymi samymi dokumentami w indeksie wyszukiwania. 

W usłudze Azure Wyszukiwanie poznawcze pole klucza jednoznacznie identyfikuje każdy dokument. Każdy indeks wyszukiwania musi mieć dokładnie jedno pole klucza typu `Edm.String` . Pole klucza musi być obecne dla każdego dokumentu w źródle danych, które jest dodawane do indeksu. (W rzeczywistości jest to jedyne pole wymagane).

Podczas indeksowania danych z wielu źródeł danych upewnij się, że każdy przychodzący wiersz lub dokument zawiera wspólny klucz dokumentu do scalania danych z dwóch fizycznie oddzielnych dokumentów źródłowych do nowego dokumentu wyszukiwania w połączonym indeksie. 

Często wymaga to pewnej planowania z góry, aby zidentyfikować istotny klucz dokumentu dla indeksu, i upewnić się, że istnieje w obu źródłach danych. W tym pokazie `HotelId` klucz dla każdego hotelu w Cosmos DB jest również obecny w obiektach Blob w formacie JSON w usłudze BLOB Storage.

Indeksatory usługi Azure Wyszukiwanie poznawcze mogą używać mapowań pól do zmiany nazw i nawet ponownego formatowania pól danych w procesie indeksowania, dzięki czemu dane źródłowe mogą być kierowane do poprawnego pola indeksu. Na przykład, w Cosmos DB, identyfikator hotelu jest wywoływany **`HotelId`** . Jednak w plikach obiektów BLOB JSON dla pokojów hotelowych identyfikator hotelu ma nazwę **`Id`** . Program obsługuje to przez mapowanie **`Id`** pola z obiektów BLOB do **`HotelId`** pola klucza w indeksatorze.

> [!NOTE]
> W większości przypadków automatycznie generowane klucze dokumentów, takie jak te utworzone domyślnie przez niektórych indeksatorów, nie sprawiają, że są to dobre klucze dokumentów dla połączonych indeksów. Ogólnie rzecz biorąc, należy użyć znaczącej, unikatowej wartości klucza, która już istnieje w programie, lub można ją łatwo dodać do źródła danych.

## <a name="4---explore-the-code"></a>4 — Eksplorowanie kodu

Po zakończeniu ustawień danych i konfiguracji, przykładowy program w **/v11/AzureSearchMultipleDataSources.sln** powinien być gotowy do kompilowania i uruchamiania.

Ta prosta aplikacja konsolowa języka C#/.NET wykonuje następujące zadania:

* Tworzy nowy indeks oparty na strukturze danych klasy hotelu języka C# (która odwołuje się również do klas adresów i pokojów).
* Tworzy nowe źródło danych i indeksator, który mapuje Azure Cosmos DB dane na pola indeksu. Są to obiekty na platformie Azure Wyszukiwanie poznawcze.
* Uruchamia indeksator w celu załadowania danych hotelu z Cosmos DB.
* Tworzy drugie źródło danych i indeksator, który mapuje dane obiektów BLOB JSON do pól indeksu.
* Uruchamia drugi indeksator, aby załadować dane pokojów z magazynu obiektów BLOB.

 Przed uruchomieniem programu Poświęć minutę na przeanalizowanie kodu i definicji indeksu i indeksatora dla tego przykładu. Odpowiedni kod znajduje się w dwóch plikach:

  + **Hotel. cs** zawiera schemat definiujący indeks
  + **Program. cs** zawiera funkcje, które tworzą indeks wyszukiwanie poznawcze platformy Azure, źródła danych i indeksatory, a następnie ładuje połączone wyniki do indeksu.

### <a name="create-an-index"></a>Tworzenie indeksu

Ten przykładowy program używa [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) do definiowania i tworzenia indeksu wyszukiwanie poznawcze platformy Azure. Wykorzystuje klasę [FieldBuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) , aby wygenerować strukturę indeksu z klasy modelu danych języka C#.

Model danych jest definiowany przez klasę hotelu, która również zawiera odwołania do klas Address i Room. FieldBuilder przechodzi przez wiele definicji klas w celu wygenerowania złożonej struktury danych dla indeksu. Tagi metadanych są używane do definiowania atrybutów poszczególnych pól, na przykład czy można je przeszukiwać czy sortować.

Program usunie istniejący indeks o tej samej nazwie przed utworzeniem nowego, w przypadku, gdy chcesz uruchomić ten przykład więcej niż raz.

Poniższe fragmenty kodu z pliku **hotelu. cs** przedstawiają pojedyncze pola, po których następuje odwołanie do innej klasy modelu danych, pomieszczenie [], które z kolei jest zdefiniowane w pliku **pokoju. cs** (nie pokazano).

```csharp
. . .
[SimpleField(IsFilterable = true, IsKey = true)]
public string HotelId { get; set; }

[SearchableField(IsFilterable = true, IsSortable = true)]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

W pliku **program. cs** [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) jest zdefiniowany przy użyciu nazwy i kolekcji pól wygenerowanej przez `FieldBuilder.Build` metodę, a następnie utworzony w następujący sposób:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Tworzenie Azure Cosmos DB źródła danych i indeksatora

Następny program główny zawiera logikę, aby utworzyć Azure Cosmos DB źródło danych dla danych hoteli.

Najpierw łączy nazwę bazy danych Azure Cosmos DB z parametrami połączenia. Następnie definiuje obiekt [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) .

```csharp
private static async Task CreateAndRunCosmosDbIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    // Append the database name to the connection string
    string cosmosConnectString =
        configuration["CosmosDBConnectionString"]
        + ";Database="
        + configuration["CosmosDBDatabaseName"];

    SearchIndexerDataSourceConnection cosmosDbDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["CosmosDBDatabaseName"],
        type: SearchIndexerDataSourceType.CosmosDb,
        connectionString: cosmosConnectString,
        container: new SearchIndexerDataContainer("hotels"));

    // The Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(cosmosDbDataSource);
```

Po utworzeniu źródła danych program skonfiguruje Azure Cosmos DB indeksatora o nazwie **pokoje hotelowe-Cosmos-Indexer**.

Program zaktualizuje wszystkie istniejące indeksatory o tej samej nazwie, zastępując istniejący indeksator zawartością powyższego kodu. Obejmuje także akcje resetowania i uruchamiania, jeśli chcesz uruchomić ten przykład więcej niż raz.

W poniższym przykładzie zdefiniowano harmonogram dla indeksatora, tak aby był uruchamiany raz dziennie. Możesz usunąć Właściwość Schedule z tego wywołania, jeśli nie chcesz, aby indeksator został automatycznie uruchomiony ponownie w przyszłości.

```csharp
SearchIndexer cosmosDbIndexer = new SearchIndexer(
    name: "hotel-rooms-cosmos-indexer",
    dataSourceName: cosmosDbDataSource.Name,
    targetIndexName: indexName)
{
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

// Indexers keep metadata about how much they have already indexed.
// If we already ran the indexer, it "remembers" and does not run again.
// To avoid this, reset the indexer if it exists.
try
{
    await indexerClient.GetIndexerAsync(cosmosDbIndexer.Name);
    // Reset the indexer if it exists.
    await indexerClient.ResetIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404)
{
    // If the indexer does not exist, 404 will be thrown.
}

await indexerClient.CreateOrUpdateIndexerAsync(cosmosDbIndexer);

Console.WriteLine("Running Cosmos DB indexer...\n");

try
{
    // Run the indexer.
    await indexerClient.RunIndexerAsync(cosmosDbIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 429)
{
    Console.WriteLine("Failed to run indexer: {0}", ex.Message);
}
```

Ten przykład zawiera prosty blok try-catch do zgłaszania błędów, które mogą wystąpić podczas wykonywania.

Po uruchomieniu indeksatora Azure Cosmos DB indeks wyszukiwania będzie zawierał pełny zestaw przykładowych dokumentów hotelowych. Jednak pole pokojów dla każdego hotelu będzie pustą tablicę, ponieważ źródło danych Azure Cosmos DB pomija szczegóły pokoju. Następnie program pobierze z usługi BLOB Storage w celu załadowania i scalenia danych pokoju.

### <a name="create-blob-storage-data-source-and-indexer"></a>Tworzenie źródła danych i indeksatora magazynu obiektów BLOB

Aby uzyskać szczegółowe informacje o pokoju, program najpierw konfiguruje źródło danych magazynu obiektów BLOB w celu odwoływania się do zestawu poszczególnych plików obiektów BLOB JSON.

```csharp
private static async Task CreateAndRunBlobIndexerAsync(string indexName, SearchIndexerClient indexerClient)
{
    SearchIndexerDataSourceConnection blobDataSource = new SearchIndexerDataSourceConnection(
        name: configuration["BlobStorageAccountName"],
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["BlobStorageConnectionString"],
        container: new SearchIndexerDataContainer("hotel-rooms"));

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await indexerClient.CreateOrUpdateDataSourceConnectionAsync(blobDataSource);
```

Po utworzeniu źródła danych program skonfiguruje obiekt BLOB indeksatora o nazwie **sale-Indexer**, jak pokazano poniżej.

Obiekty blob JSON zawierają pole klucza o nazwie **`Id`** zamiast **`HotelId`** . Kod używa klasy, `FieldMapping` Aby określić indeksator do skierowania **`Id`** wartości pola do **`HotelId`** klucza dokumentu w indeksie.

Indeksatory magazynu obiektów BLOB mogą używać [IndexingParameters](/dotnet/api/azure.search.documents.indexes.models.indexingparameters) do określania trybu analizy. Należy ustawić różne tryby analizy w zależności od tego, czy obiekty blob reprezentują pojedynczy dokument, czy wiele dokumentów w ramach tego samego obiektu BLOB. W tym przykładzie każdy obiekt BLOB reprezentuje pojedynczy dokument JSON, więc kod używa `json` trybu analizowania. Aby uzyskać więcej informacji na temat parametrów analizy indeksatora dla obiektów BLOB JSON, zobacz [indeksowanie obiektów BLOB JSON](search-howto-index-json-blobs.md).

Ten przykład definiuje harmonogram dla indeksatora, tak aby był uruchamiany raz dziennie. Możesz usunąć Właściwość Schedule z tego wywołania, jeśli nie chcesz, aby indeksator został automatycznie uruchomiony ponownie w przyszłości.

```csharp
// Map the Id field in the Room documents to the HotelId key field in the index
List<FieldMapping> map = new List<FieldMapping> {
    new FieldMapping("Id")
    {
        TargetFieldName =  "HotelId"
    }
};

IndexingParameters parameters = new IndexingParameters();
parameters.Configuration.Add("parsingMode", "json");

SearchIndexer blobIndexer = new SearchIndexer(
    name: "hotel-rooms-blob-indexer",
    dataSourceName: blobDataSource.Name,
    targetIndexName: indexName)
{
    Parameters = parameters,
    Schedule = new IndexingSchedule(TimeSpan.FromDays(1))
};

blobIndexer.FieldMappings.Add(new FieldMapping("Id") { TargetFieldName = "HotelId" });

// Reset the indexer if it already exists
try
{
    await indexerClient.GetIndexerAsync(blobIndexer.Name);
    await indexerClient.ResetIndexerAsync(blobIndexer.Name);
}
catch (RequestFailedException ex) when (ex.Status == 404) { }

await indexerClient.CreateOrUpdateIndexerAsync(blobIndexer);

try
{
    // Run the indexer.
    await searchService.Indexers.RunAsync(blobIndexer.Name);
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

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Lista indeksów usługi Azure Wyszukiwanie poznawcze" border="false":::

Na liście kliknij indeks pokojów hotelowych — przykład. Zostanie wyświetlony interfejs Eksploratora wyszukiwania dla indeksu. Wprowadź zapytanie dla terminu takiego jak "możliwość zaprojektowania". W wynikach powinien być widoczny co najmniej jeden dokument, a ten dokument powinien zawierać listę obiektów Room w tablicy pokojów.

## <a name="reset-and-rerun"></a>Resetowanie i ponowne uruchamianie

W przypadku wczesnych eksperymentalnych etapów tworzenia najlepszym podejściem do iteracji projektu jest usunięcie obiektów z platformy Azure Wyszukiwanie poznawcze i umożliwienie kodowi odbudowania. Nazwy zasobów są unikatowe. Usunięcie obiektu umożliwia jego ponowne utworzenie przy użyciu tej samej nazwy.

Przykładowy kod sprawdza istniejące obiekty i usuwa lub aktualizuje je, aby można było ponownie uruchomić program.

Możesz również użyć portalu, aby usunąć indeksy, indeksatory i źródła danych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku wszystkie zasoby lub grupy zasobów w okienku nawigacji po lewej stronie.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz koncepcję pozyskiwania danych z wielu źródeł, przyjrzyjmy się bliżej konfiguracji indeksatora, rozpoczynając od Cosmos DB.

> [!div class="nextstepaction"]
> [Konfigurowanie indeksatora Azure Cosmos DB](search-howto-index-cosmosdb.md)
