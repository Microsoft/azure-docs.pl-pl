---
title: Migrowanie aplikacji do korzystania z Azure Cosmos DB Java SDK v4 (com. Azure. Cosmos)
description: Dowiedz się, w jaki sposób uaktualnić istniejącą aplikację Java przy użyciu Azure Cosmos DB starszych zestawów SDK Java do nowszej wersji 4,0 (com. Azure. Cosmos Package) dla interfejsu API Core (SQL).
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 6bbf87689b577eda7de491744156e63eaa3b440c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546883"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migrowanie aplikacji do korzystania z Azure Cosmos DB Java SDK v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Aby uzyskać więcej informacji na temat tego zestawu SDK, Azure Cosmos DB Zobacz informacje o [wersji](sql-api-sdk-java-v4.md)zestawu Java SDK v4, [repozytorium Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB przewodniku [dotyczące wydajności](performance-tips-java-sdk-v4-sql.md)zestawu Java SDK 4 i Azure Cosmos DB [podręczniku rozwiązywania problemów](troubleshoot-java-sdk-v4-sql.md)z zestawem SDK w wersji 4.
>

W tym artykule wyjaśniono, jak uaktualnić istniejącą aplikację Java, która korzysta ze starszego zestawu SDK języka Java Azure Cosmos DB, do nowszego Azure Cosmos DB Java SDK 4,0 dla podstawowego (SQL) interfejsu API. Azure Cosmos DB Java SDK v4 odpowiada `com.azure.cosmos` pakietowi. W przypadku migrowania aplikacji z dowolnego z następujących Azure Cosmos DB zestawów SDK języka Java można użyć instrukcji zawartych w tym dokumencie: 

* Synchronizuj zestaw Java SDK 2. x. x
* Asynchroniczny zestaw Java SDK 2. x. x
* Zestaw Java SDK 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB mapowania zestawu Java SDK i pakietów

W poniższej tabeli wymieniono różne Azure Cosmos DB zestawy SDK Java, nazwę pakietu i informacje o wersji:

| Zestaw SDK Java| Data wydania | Powiązane interfejsy API   | Maven jar  | Nazwa pakietu Java  |Dokumentacja interfejsu API   | Informacje o wersji  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Czerwiec 2018 r.    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [Interfejs API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Uwagi do wersji](sql-api-sdk-async-java.md) |
| Synchronizuj 2. x. x     | Września 2018    | Synchronizuj   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [Interfejs API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Uwagi do wersji](sql-api-sdk-java.md)  |
| 3. x. x    | Lipiec 2019 r.    | Async (reaktor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [Interfejs API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Czerwiec 2020 r.   | Async (reaktor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [Interfejs API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Zmiany implementacji poziomu zestawu SDK

Poniżej przedstawiono podstawowe różnice implementacji między różnymi zestawami SDK:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava jest zastępowana przez reaktor w Azure Cosmos DB Java SDK w wersji 3. x. x i 4,0

Jeśli nie masz doświadczenia z programowaniem asynchronicznym lub nieaktywnym programowaniem, zobacz [Przewodnik po wzorcu reaktora](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) , aby zapoznać się z wprowadzeniem do programowania asynchronicznego i reaktora projektu. Ten przewodnik może być przydatny, jeśli używasz Azure Cosmos DB synchronizacji Java SDK 2. x. x lub Azure Cosmos DB Java SDK 3. x synchronizacji w przeszłości.

Jeśli używasz Azure Cosmos DB asynchronicznego zestawu Java SDK 2. x. x i planujesz migrację do zestawu 4,0 SDK, zapoznaj się z [przewodnikiem reaktora vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) , aby uzyskać wskazówki dotyczące konwertowania kodu RxJava na używanie reaktora.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 ma tryb łączności bezpośredniej w interfejsie API Async i Sync

Jeśli używasz Azure Cosmos DB synchronizacji zestawu Java SDK 2. x. x, należy zauważyć, że tryb połączenia bezpośredniego oparty na protokole TCP (w przeciwieństwie do protokołu HTTP) jest zaimplementowany w Azure Cosmos DB Java SDK 4,0 dla interfejsów API Async i Sync.

## <a name="api-level-changes"></a>Zmiany poziomu interfejsu API

Poniżej przedstawiono zmiany poziomu interfejsu API w Azure Cosmos DB Java SDK 4. x. x w porównaniu do poprzednich zestawów SDK (Java SDK 3. x. x, Async Java SDK 2. x. x i Sync Java SDK 2. x. x):

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Azure Cosmos DB konwencje nazewnictwa języka Java SDK":::

* Azure Cosmos DB Java SDK 3. x. x i 4,0 odnoszą się do zasobów klienta jako `Cosmos<resourceName>` . Na przykład, `CosmosClient` , `CosmosDatabase` `CosmosContainer` . W wersji 2. x. x Azure Cosmos DB zestawy SDK języka Java nie mają jednolitego schematu nazewnictwa.

* Azure Cosmos DB Java SDK 3. x. x i 4,0 oferują zarówno synchronizację, jak i asynchroniczne interfejsy API.

  * **Java SDK 4,0** : wszystkie klasy należą do interfejsu API synchronizacji, chyba że nazwa klasy jest dołączana `Async` po `Cosmos` .

  * **Java SDK 3. x. x**: wszystkie klasy należą do asynchronicznego interfejsu API, chyba że nazwa klasy jest dołączana `Async` po `Cosmos` .

  * **Asynchroniczny zestaw Java SDK 2. x. x**: nazwy klas są podobne do synchronizacji zestawu Java SDK 2. x. x, ale nazwa zaczyna się od *Async*.

### <a name="hierarchical-api-structure"></a>Hierarchiczna struktura interfejsu API

Azure Cosmos DB Java SDK 4,0 i 3. x. x wprowadź hierarchiczną strukturę interfejsu API, która organizuje klientów, bazy danych i kontenery w sposób zagnieżdżony, jak pokazano w następującym fragmencie kodu zestawu SDK 4,0:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

W wersji 2. x. x zestawu Azure Cosmos DB Java SDK wszystkie operacje na zasobach i dokumentach są wykonywane za pomocą wystąpienia klienta.

### <a name="representing-documents"></a>Reprezentuje dokumenty

W Azure Cosmos DB Java SDK 4,0, niestandardowe POJO i `JsonNodes` są dwiema opcjami odczytu i zapisu dokumentów z Azure Cosmos DB.

W Azure Cosmos DB Java SDK 3. x, `CosmosItemProperties` obiekt jest uwidaczniany przez publiczny interfejs API i obsługiwany jako reprezentacja dokumentu. Ta klasa nie jest już ujawniona publicznie w wersji 4,0.

### <a name="imports"></a>Importowania

* Pakiety Azure Cosmos DB Java SDK 4,0 zaczynają się od `com.azure.cosmos`
  * Azure Cosmos DB zestawu Java SDK 3. x. x pakiety rozpoczynają się od `com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4,0 umieszcza kilka klas w zagnieżdżonym pakiecie `com.azure.cosmos.models` . Niektóre z tych pakietów obejmują:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Asynchroniczne interfejs API Analogs dla wszystkich powyższych pakietów
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ... itd.

### <a name="accessors"></a>Metod dostępu

Azure Cosmos DB Java SDK 4,0 ujawnia `get` i `set` metody dostępu do elementów członkowskich wystąpienia. Na przykład `CosmosContainer` wystąpienie ma `container.getId()` `container.setId()` metodę i.

Różni się to od Azure Cosmos DB Java SDK 3. x. x, który uwidacznia interfejs Fluent. Na przykład `CosmosSyncContainer` wystąpienie ma `container.id()` przeciążone w celu pobrania lub ustawienia `id` wartości.

## <a name="code-snippet-comparisons"></a>Porównania fragmentów kodu

### <a name="create-resources"></a>Tworzenie zasobów

Poniższy fragment kodu przedstawia różnice w sposobie tworzenia zasobów między 4,0 i 3. x asynchronicznymi interfejsami API:

# <a name="java-sdk-40-async-api"></a>[Interfejs API Async SDK 4,0 języka Java](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>Operacje elementu

Poniższy fragment kodu przedstawia różnice w sposobie wykonywania operacji elementów między 4,0 i 3. x asynchronicznymi interfejsami API:

# <a name="java-sdk-40-async-api"></a>[Interfejs API Async SDK 4,0 języka Java](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>Indeksowanie

Poniższy fragment kodu przedstawia różnice w sposobie tworzenia indeksowania między 4,0 i 3. x asynchronicznymi interfejsami API:

# <a name="java-sdk-40-async-api"></a>[Interfejs API Async SDK 4,0 języka Java](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>Procedury składowane

Poniższy fragment kodu przedstawia różnice w sposobie tworzenia procedur przechowywanych między 4,0 i 3. x asynchronicznymi interfejsami API:

# <a name="java-sdk-40-async-api"></a>[Interfejs API Async SDK 4,0 języka Java](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>Źródło zmian

Poniższy fragment kodu przedstawia różnice w sposobie wykonywania operacji strumieniowego zmiany między 4,0 i 3. x asynchronicznymi interfejsami API:

# <a name="java-sdk-40-async-api"></a>[Interfejs API Async SDK 4,0 języka Java](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>Czas wygaśnięcia kontenera (TTL)

Poniższy fragment kodu przedstawia różnice w sposobie tworzenia czasu na żywo dla danych w kontenerze przy użyciu asynchronicznych interfejsów API 4,0 i 3. x:

# <a name="java-sdk-40-async-api"></a>[Interfejs API Async SDK 4,0 języka Java](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>Czas wygaśnięcia elementu na żywo (TTL)

Poniższy fragment kodu przedstawia różnice w sposobie tworzenia czasu wygaśnięcia dla elementu przy użyciu asynchronicznych interfejsów API 4,0 i 3. x:

# <a name="java-sdk-40-async-api"></a>[Interfejs API Async SDK 4,0 języka Java](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 3. x. x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>Następne kroki

* [Tworzenie aplikacji Java](create-sql-api-java.md) do zarządzania Azure Cosmos DB danych interfejsu API SQL przy użyciu zestawu SDK dla wersji 4
* Dowiedz się więcej na temat [zestawów SDK Java opartych na](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) replikach
* Więcej informacji na temat konwertowania kodu asynchronicznego RxJava na kod asynchroniczny reaktora przy użyciu programu [reaktora vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)
