---
title: Migrowanie aplikacji do korzystania z Azure Cosmos DB .NET SDK 3,0 (com. Azure. Cosmos)
description: Dowiedz się, jak uaktualnić istniejącą aplikację .NET z zestawu v2 SDK do nowszego zestawu .NET SDK v3 (com. Azure. Cosmos Package) dla podstawowego interfejsu API (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 514a6c6daccfe63865ae0b2b9f5bf29c5cbedc29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334025"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Migrowanie aplikacji do korzystania z Azure Cosmos DB .NET SDK v3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Aby dowiedzieć się więcej na temat Azure Cosmos DB zestawu .NET SDK v3, zapoznaj się z [informacjami o wersji](sql-api-sdk-dotnet-standard.md), w [repozytorium usługi .NET GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3), [wskazówkami dotyczącymi wydajności](performance-tips-dotnet-sdk-v3-sql.md)zestawu .NET SDK v3 i [przewodniku rozwiązywania problemów](troubleshoot-dot-net-sdk.md).
>

W tym artykule omówiono niektóre zagadnienia związane z uaktualnianiem istniejącej aplikacji .NET do nowszej wersji interfejsu API programu Azure Cosmos DB .NET SDK v3 for Core (SQL). Azure Cosmos DB .NET SDK v3 odpowiada przestrzeni nazw Microsoft. Azure. Cosmos. Informacje zawarte w tym dokumencie można wykorzystać w przypadku migrowania aplikacji z dowolnego z następujących Azure Cosmos DB zestawów SDK platformy .NET:

* Azure Cosmos DB .NET Framework SDK V2 dla interfejsu API SQL
* Azure Cosmos DB zestaw .NET Core SDK V2 dla interfejsu API SQL

Instrukcje zawarte w tym artykule ułatwiają migrowanie następujących bibliotek zewnętrznych, które są teraz częścią interfejsu API Azure Cosmos DB .NET SDK v3 for Core (SQL):

* Biblioteka procesora kanału informacyjnego zmiany platformy .NET 2,0
* Biblioteka wykonawców zbiorczych platformy .NET 1,1 lub nowsza

## <a name="whats-new-in-the-net-v3-sdk"></a>Co nowego w zestawie SDK platformy .NET v3

Zestaw v3 SDK zawiera wiele ulepszeń użyteczności i wydajności, w tym:

* Intuicyjne nazewnictwo modeli programowania
* .NET Standard 2,0 * *
* Zwiększona wydajność dzięki obsłudze interfejsu API usługi Stream
* Hierarchia Fluent, która zastępuje potrzebę fabryki identyfikatorów URI
* Wbudowana obsługa biblioteki procesora źródła zmian
* Wbudowana obsługa operacji zbiorczych
* Mockabale interfejsy API w celu łatwiejszego testowania jednostek
* Obsługa partii i Blazor transakcyjnych
* Podłączane serializatory
* Skalowanie kontenerów bez partycjonowania i skalowania automatycznego

* * Zestaw SDK jest przeznaczony dla .NET Standard 2,0, które łączy istniejące Azure Cosmos DB .NET Framework i zestawy SDK platformy .NET Core do jednego zestawu SDK platformy .NET. Zestawu .NET SDK można używać na dowolnej platformie implementującej .NET Standard 2,0, w tym .NET Framework 4.6.1 + i .NET Core 2.0 i aplikacji.

Większość sieci, logika ponowień i niższy poziom zestawu SDK pozostają w znacznym stopniu bez zmian.

**Zestaw Azure Cosmos DB .NET SDK V3 jest teraz otwartym źródłem.** Powitamy wszystkie żądania ściągnięcia i będziemy rejestrować problemy oraz śledzić opinie w witrynie [GitHub.](https://github.com/Azure/azure-cosmos-dotnet-v3/) Będziemy korzystać z wszystkich funkcji, które pomogą ulepszyć środowisko klienta.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Dlaczego należy przeprowadzić migrację do zestawu SDK platformy .NET v3

Oprócz licznych ulepszeń użyteczności i wydajności nowe inwestycje w najnowszą wersję zestawu SDK nie zostaną przetworzone do starszych wersji.

Chociaż nie istnieją natychmiastowe plany [wycofywania pomocy technicznej dla zestawów sdk 2,0](sql-api-sdk-dotnet.md), zestawy SDK zostaną zastąpione nowszymi wersjami w przyszłości, a zestaw SDK przejdzie w tryb konserwacji. Aby uzyskać najlepsze środowisko programistyczne, zalecamy zawsze rozpoczęcie od najnowszej obsługiwanej wersji zestawu SDK.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Zmiany nazwy głównej z zestawu v2 SDK do V3 SDK

W ramach zestawu SDK .NET 3,0 zostały zastosowane następujące zmiany nazw do dopasowania z konwencjami nazewnictwa interfejsu API dla interfejsu API Core (SQL):

* `DocumentClient` Zmieniono nazwę na `CosmosClient`
* `Collection` Zmieniono nazwę na `Container`
* `Document` Zmieniono nazwę na `Item`

Wszystkie obiekty zasobów są zmieniane o dodatkowe właściwości, co obejmuje nazwę zasobu do przejrzystości.

Poniżej przedstawiono niektóre zmiany nazwy głównej klasy:

| Zestaw .NET V2 SDK | Zestaw SDK dla platformy .NET v3 |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Klasy zastąpione w zestawie SDK platformy .NET v3

Następujące klasy zostały zastąpione w zestawie SDK 3,0:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Microsoft.Azure.Documents. Klasa UriFactory została zastąpiona przez projekt Fluent. Projekt Fluent kompiluje adresy URL wewnętrznie i zezwala na `Container` przekazanie pojedynczego obiektu zamiast `DocumentClient` , `DatabaseName` i `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Zmiany generacji identyfikatora elementu

Identyfikator elementu nie jest już automatycznie wypełniany w zestawie SDK platformy .NET v3. W związku z tym identyfikator elementu musi obejmować wygenerowany identyfikator. Wyświetl Poniższy przykład:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Zmieniono domyślne zachowanie trybu połączenia

Zestaw SDK v3 teraz domyślnie umożliwia bezpośrednie + tryby połączeń TCP w porównaniu z poprzednimi wersjami zestawu SDK V2, które domyślnie są trybami połączeń bramy i HTTPS. Ta zmiana zapewnia lepszą wydajność i skalowalność.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Zmiany w FeedOptions (QueryRequestOptions w zestawie SDK v 3.0)

Nazwa `FeedOptions` klasy w zestawie SDK V2 została zmieniona na `QueryRequestOptions` w zestawie SDK v3 i w klasie, w kilku właściwościach wprowadzono zmiany w nazwie i/lub wartości domyślnej albo zostały one całkowicie usunięte.  

`FeedOptions.MaxDegreeOfParallelism` Zmieniono nazwę na `QueryRequestOptions.MaxConcurrency` i wartość domyślną, a powiązane zachowanie pozostaje takie samo, podczas równoległego wykonywania zapytań operacje wykonywane po stronie klienta będą wykonywane szeregowo z nierównoległą.

`FeedOptions.EnableCrossPartitionQuery` Program został usunięty i domyślne zachowanie w zestawie SDK 3,0 polega na tym, że zapytania między partycjami będą wykonywane bez konieczności włączania właściwości.

`FeedOptions.PopulateQueryMetrics` jest domyślnie włączona z wynikami obecnymi we właściwości Diagnostics odpowiedzi.

`FeedOptions.RequestContinuation` został teraz podwyższony do metod zapytania.

Następujące właściwości zostały usunięte:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Konstruowanie klienta

Zestaw .NET SDK v3 zawiera klasę Fluent `CosmosClientBuilder` , która zastępuje potrzebę fabryki identyfikatorów URI zestawu SDK V2.

Poniższy przykład tworzy nowy `CosmosClientBuilder` z silną ConsistencyLevelą i listą preferowanych lokalizacji:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Używanie interfejsów API procesora źródła zmian bezpośrednio z zestawu SDK v3

Zestaw v3 SDK ma wbudowaną obsługę interfejsów API procesora źródła zmian, umożliwiając używanie tego samego zestawu SDK do kompilowania aplikacji i implementacji procesora kanału zmian. Wcześniej należało użyć oddzielnej biblioteki procesora kanału informacyjnego zmian.

Aby uzyskać więcej informacji, zobacz [Jak przeprowadzić migrację z biblioteki procesora źródła zmian do zestawu SDK Azure Cosmos DB .NET v3](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Korzystanie z biblioteki wykonawców zbiorczych bezpośrednio z zestawu SDK v3

Zestaw v3 SDK ma wbudowaną obsługę biblioteki wykonawców zbiorczych, co pozwala na korzystanie z tego samego zestawu SDK do kompilowania aplikacji i wykonywania operacji zbiorczych. Wcześniej było wymagane użycie oddzielnej biblioteki wykonawców zbiorczych.

Aby uzyskać więcej informacji, zobacz [Jak przeprowadzić migrację z biblioteki programu do zbiorczego wykonawcy do obsługi zbiorczej w programie Azure Cosmos DB .NET v3 SDK](how-to-migrate-from-bulk-executor-library.md)

## <a name="code-snippet-comparisons"></a>Porównania fragmentów kodu

Poniższy fragment kodu przedstawia różnice w sposobie tworzenia zasobów między zestawami SDK platformy .NET V2 i V3:

## <a name="database-operations"></a>Operacje bazy danych

### <a name="create-a-database"></a>Tworzenie bazy danych

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Odczytywanie bazy danych na podstawie identyfikatora

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Usuwanie bazy danych

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Operacje kontenera

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Utwórz kontener (automatyczne skalowanie i czas wygaśnięcia z wygaśnięciem)

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Odczytaj właściwości kontenera

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Usuwanie kontenera

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Operacje elementów i zapytań

### <a name="create-an-item"></a>Tworzenie elementu

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Odczytaj wszystkie elementy w kontenerze

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Elementy zapytań

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Usuwanie elementu

# <a name="net-sdk-v3"></a>[Zestaw SDK .NET w wersji 3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[Zestaw .NET SDK w wersji 2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Następne kroki

* [Tworzenie aplikacji konsolowej](sql-api-get-started.md) do zarządzania Azure Cosmos DB danych interfejsu API SQL przy użyciu zestawu v3 SDK
* Dowiedz się więcej na temat tego, [co możesz zrobić z zestawem SDK v3](sql-api-dotnet-v3sdk-samples.md)
