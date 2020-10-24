---
title: Zarządzanie spójnością w Azure Cosmos DB
description: Informacje o konfigurowaniu poziomów spójności i zarządzaniu nimi w Azure Cosmos DB przy użyciu Azure Portal, zestawu .NET SDK, zestawu Java SDK i różnych zestawów SDK
author: anfeldma-ms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 2b3433d969611fabe1b12a8dcabfe6e50066a8c1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491193"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Zarządzanie poziomami spójności w usłudze Azure Cosmos DB

W tym artykule wyjaśniono, jak zarządzać poziomami spójności w usłudze Azure Cosmos DB. Dowiesz się, jak skonfigurować domyślny poziom spójności, zastąpić spójność domyślną, ręcznie zarządzać tokenami sesji oraz jak rozumieć metrykę PBS (Probabilistically Bounded Staleness).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurowanie domyślnego poziomu spójności

[Domyślny poziom spójności](consistency-levels.md) jest poziomem spójności używanym domyślnie przez klientów.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby wyświetlić lub zmodyfikować domyślny poziom spójności, zaloguj się do witryny Azure Portal. Znajdź konto usługi Azure Cosmos i Otwórz domyślne okienko **spójności** . Wybierz odpowiedni poziom spójności jako nowe ustawienie domyślne, a następnie wybierz pozycję **Zapisz**. Azure Portal udostępnia również wizualizację różnych poziomów spójności przy użyciu notatek muzycznych. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Menu spójności w witrynie Azure Portal":::

# <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli)

Utwórz konto Cosmos z spójnością sesji, a następnie zaktualizuj domyślną spójność.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Utwórz konto Cosmos z spójnością sesji, a następnie zaktualizuj domyślną spójność.

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

Klienci mogą zastąpić domyślny poziom spójności, który jest ustawiony przez usługę. Poziom spójności można ustawić dla każdego żądania, który zastępuje domyślny poziom spójności ustawiony na poziomie konta.

> [!TIP]
> Spójność **może być możliwa tylko na poziomie żądania** . Aby przejść ze słabszego do silniejszego spójności, zaktualizuj domyślną spójność dla konta Cosmos.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>Zestaw SDK .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

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

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Zestaw SDK Java v4

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Zestawy SDK Java v2

# <a name="async"></a>[Async](#tab/api-async)

Async Java v2 SDK (Maven com. Microsoft. Azure:: Azure-cosmosdb)

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

Synchronizuj zestaw Java v2 SDK (Maven com. Microsoft. Azure:: Azure-DocumentDB)

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

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Zestaw SDK dla języka Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Korzystanie z tokenów sesji

Jeden z poziomów spójności w Azure Cosmos DB jest spójność *sesji* . Jest to domyślny poziom stosowany domyślnie do kont Cosmos. Podczas pracy ze spójnością *sesji* klient będzie używać tokenu sesji wewnętrznie w przypadku każdego żądania odczytu/zapytania, aby zapewnić zachowanie ustawienia poziomu spójności.

Aby ręcznie zarządzać tokenami sesji, pobieraj token sesji z odpowiedzi i ustawiaj go dla poszczególnych żądań. Jeśli nie chcesz ręcznie zarządzać tokenami sesji, nie musisz korzystać z tych przykładów. Zestaw SDK automatycznie śledzi tokeny sesji. Jeśli nie ustawisz tokenu sesji ręcznie, zestaw SDK domyślnie użyje najnowszego tokenu sesji.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>Zestaw SDK .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Zestaw SDK Java v4

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   Java SDK v4 (Maven com. Azure:: Azure-Cosmos) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Zestawy SDK Java v2

# <a name="async"></a>[Async](#tab/api-async)

Async Java v2 SDK (Maven com. Microsoft. Azure:: Azure-cosmosdb)

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

Synchronizuj zestaw Java v2 SDK (Maven com. Microsoft. Azure:: Azure-DocumentDB)

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

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Zestaw SDK dla języka Python

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

Jak ostateczna jest spójność ostateczna? W przypadku średniego przypadku można zaoferować nieaktualność w odniesieniu do historii wersji i czasu. Metryka [**probabilistically ograniczona (PBS)**](https://pbs.cs.berkeley.edu/) próbuje określić prawdopodobieństwo nieodświeżoności i pokazuje ją jako metrykę. Aby wyświetlić metrykę usługi PBS, przejdź do swojego konta usługi Azure Cosmos w Azure Portal. Otwórz okienko **metryki** i wybierz kartę **spójność** . Przyjrzyj się grafowi o nazwie **prawdopodobieństwo silnie spójnych odczytów w oparciu o obciążenie (zobacz PBS)**.

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Menu spójności w witrynie Azure Portal":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu konfliktami danych lub przejdź do następnej kluczowej koncepcji w usłudze Azure Cosmos DB. Zobacz następujące artykuły:

* [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
* [Partycjonowanie i dystrybucja danych](./partitioning-overview.md)
* [Zarządzanie konfliktami między regionami](how-to-manage-conflicts.md)
* [Partycjonowanie i dystrybucja danych](partitioning-overview.md)
* [Założenia dotyczące spójności w nowoczesnych systemach rozproszonej bazy danych](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Wysoka dostępność](high-availability.md)
* [Azure Cosmos DB umowy SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)