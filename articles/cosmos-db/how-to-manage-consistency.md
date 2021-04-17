---
title: Zarządzanie spójnością w Azure Cosmos DB
description: Dowiedz się, jak konfigurować poziomy spójności i zarządzać nimi w programie Azure Cosmos DB przy użyciu Azure Portal, zestawu SDK platformy .NET, zestawu JAVA SDK i różnych innych zestawów SDK
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b7cab67b49196a3d50ce5483282971bbb7b9ece1
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483269"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Zarządzanie poziomami spójności w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule wyjaśniono, jak zarządzać poziomami spójności w usłudze Azure Cosmos DB. Dowiesz się, jak skonfigurować domyślny poziom spójności, zastąpić spójność domyślną, ręcznie zarządzać tokenami sesji oraz jak rozumieć metrykę PBS (Probabilistically Bounded Staleness).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

Domyślny [poziom spójności to](consistency-levels.md) poziom spójności, z których domyślnie korzystają klienci.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby wyświetlić lub zmodyfikować domyślny poziom spójności, zaloguj się do witryny Azure Portal. Znajdź konto usługi Azure Cosmos i otwórz **okienko Domyślna spójność.** Wybierz odpowiedni poziom spójności jako nowe ustawienie domyślne, a następnie wybierz pozycję **Zapisz**. Ponadto Azure Portal wizualizację różnych poziomów spójności z notatkami do muzyki. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Menu spójności w witrynie Azure Portal":::

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Utwórz konto usługi Cosmos ze spójnością sesji, a następnie zaktualizuj spójność domyślną.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Utwórz konto usługi Cosmos ze spójnością sesji, a następnie zaktualizuj spójność domyślną.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Zastępowanie domyślnego poziomu spójności

Klienci mogą zastąpić domyślny poziom spójności, który jest ustawiony przez usługę. Poziom spójności można ustawić dla każdego żądania, co zastępuje domyślny poziom spójności ustawiony na poziomie konta.

> [!TIP]
> Spójność można **złagonić** tylko na poziomie żądania. Aby przejść ze słabszej do silniejszej spójności, zaktualizuj domyślną spójność dla konta usługi Cosmos.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>Zestaw SDK platformy .NET

# <a name="net-sdk-v2"></a>[Zestaw SDK platformy .NET w wersji 2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[Zestaw SDK platformy .NET w wersji 3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Zestaw SDK java w wersji 4

# <a name="async"></a>[Async](#tab/api-async)

   Zestaw Java SDK w wersji 4 (Maven com.azure::azure-cosmos) — asynchroniczny interfejs API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   Interfejs API synchronizacji zestawu Java SDK w wersji 4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Zestawy SDK języka Java w wersji 2

# <a name="async"></a>[Async](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Korzystanie z tokenów sesji

Jednym z poziomów spójności w Azure Cosmos DB jest *spójność* sesji. Jest to domyślny poziom stosowany domyślnie do kont usługi Cosmos. Podczas pracy *ze spójnością* sesji klient będzie używać tokenu sesji wewnętrznie z każdym żądaniem odczytu/zapytania, aby zapewnić zachowanie ustawionego poziomu spójności.

Aby ręcznie zarządzać tokenami sesji, pobieraj token sesji z odpowiedzi i ustawiaj go dla poszczególnych żądań. Jeśli nie chcesz ręcznie zarządzać tokenami sesji, nie musisz korzystać z tych przykładów. Zestaw SDK automatycznie śledzi tokeny sesji. Jeśli nie ustawisz tokenu sesji ręcznie, zestaw SDK domyślnie użyje najnowszego tokenu sesji.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>Zestaw SDK platformy .NET

# <a name="net-sdk-v2"></a>[Zestaw SDK platformy .NET w wersji 2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[Zestaw SDK platformy .NET w wersji 3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Zestaw SDK java w wersji 4

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK 4 (Maven com.azure::azure-cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   Interfejs API synchronizacji zestawu Java SDK 4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Zestawy SDK języka Java w wersji 2

# <a name="async"></a>[Async](#tab/api-async)

AsyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

SyncÂ JavaÂ V2Â SDKÂ (MavenÂ com.microsoft.azure::azure-documentdb)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Monitorowanie metryki PBS (Probabilistically Bounded Staleness)

Jak ostateczna jest spójność ostateczna? W przypadku średniej możemy zaoferować granice nieaktualności w odniesieniu do historii i czasu wersji. Metryka [**PBS (Probabilistically Bounded Staleness)**](https://pbs.cs.berkeley.edu/) próbuje określić prawdopodobieństwo nieaktualności i pokazuje ją jako metrykę. Aby wyświetlić metrykę pbs, przejdź do swojego konta usługi Azure Cosmos w Azure Portal. Otwórz **okienko Metryki** i wybierz **kartę Spójność.** Spójrz na wykres o nazwie Prawdopodobieństwo silnie **spójnych odczytów opartych na obciążeniu (zobacz PBS).**

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Wykres PBS w witrynie Azure Portal":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu konfliktami danych lub przejdź do następnej kluczowej koncepcji w usłudze Azure Cosmos DB. Zobacz następujące artykuły:

* [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
* [Partycjonowanie i dystrybucja danych](./partitioning-overview.md)
* [Zarządzanie konfliktami między regionami](how-to-manage-conflicts.md)
* [Partycjonowanie i dystrybucja danych](partitioning-overview.md)
* [Kompromisy w zakresie spójności w nowoczesnym projekcie rozproszonych systemów baz danych](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Wysoka dostępność](high-availability.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
