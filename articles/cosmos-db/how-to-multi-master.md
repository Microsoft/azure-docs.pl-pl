---
title: Jak skonfigurować wielowzorcowość w usłudze Azure Cosmos DB
description: Dowiedz się, jak skonfigurować wiele wzorców dla aplikacji przy użyciu różnych zestawów SDK w Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: tracking-python, devx-track-javascript
ms.openlocfilehash: 37200983389f73cf733969b4b30dcc04efc3227c
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422185"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Konfigurowanie wielu wzorców w aplikacjach korzystających z Azure Cosmos DB

Po utworzeniu konta z włączoną obsługą wielu regionów zapisu należy wprowadzić dwie zmiany w aplikacji do ConnectionPolicy dla DocumentClient, aby włączyć obsługę wielu wzorców i wielu multihostingu w Azure Cosmos DB. W ConnectionPolicy Ustaw UseMultipleWriteLocations na true i przekaż nazwę regionu, w którym aplikacja jest wdrażana na SetCurrentLocation. Spowoduje to wypełnienie właściwości PreferredLocations w oparciu o bliskość geograficzną z przekazaną lokalizacją. Jeśli nowy region zostanie później dodany do konta, aplikacja nie musi być aktualizowana ani ponownie wdrażana, a w przypadku wystąpienia zdarzenia regionalnego zostanie automatycznie wykryta bliższy region.

> [!Note]
> Konta Cosmos początkowo skonfigurowane z pojedynczym regionem zapisu można skonfigurować dla wielu regionów zapisu (tj. wielu wzorców) z zerem o wartości zero. Aby dowiedzieć się więcej, zobacz [Konfigurowanie regionów wielokrotnego zapisu](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK 2

Aby włączyć wiele wzorców w aplikacji, ustaw wartość `UseMultipleWriteLocations` `true` . Ponadto ustaw `SetCurrentLocation` do regionu, w którym aplikacja jest wdrażana i gdzie Azure Cosmos DB jest replikowana:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK 3

Aby włączyć wiele wzorców w aplikacji, ustaw `ApplicationRegion` dla regionu, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcjonalnie możesz użyć `CosmosClientBuilder` i, `WithApplicationRegion` Aby osiągnąć ten sam wynik:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-master"></a>Zestaw SDK Java v4

Aby włączyć obsługę wielu wzorców w aplikacji, wywołaj `.multipleWriteRegionsEnabled(true)` i `.preferredRegions(preferredRegions)` w konstruktorze klienta, gdzie `preferredRegions` jest `List` zawierający jeden element — czyli region, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana:

# <a name="async"></a>[Asynchroniczne](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Synchronizacja](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) — interfejs API synchronizacji

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-milti-master"></a>Asynchroniczne środowisko Java w wersji 2 SDK

Zestaw Java v2 SDK użył Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Aby włączyć wiele wzorców w aplikacji, ustaw `policy.setUsingMultipleWriteLocations(true)` i Ustaw region, `policy.setPreferredLocations` w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript i zestawy SDK TypeScript

Aby włączyć wiele wzorców w aplikacji, ustaw wartość `connectionPolicy.UseMultipleWriteLocations` `true` . Ponadto ustaw `connectionPolicy.PreferredLocations` do regionu, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Zestaw SDK dla języka Python

Aby włączyć wiele wzorców w aplikacji, ustaw wartość `connection_policy.UseMultipleWriteLocations` `true` . Ponadto ustaw `connection_policy.PreferredLocations` na region, w którym aplikacja jest wdrażana i gdzie Cosmos DB jest replikowana.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły:

* [Używanie tokenów sesji do zarządzania spójnością w Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Conflict types and resolution policies in Azure Cosmos DB (Typy konfliktów i zasady ich rozwiązywania w usłudze Azure Cosmos DB)](conflict-resolution-policies.md)
* [High availability in Azure Cosmos DB (Wysoka dostępność w usłudze Azure Cosmos DB)](high-availability.md)
* [Poziomy spójności w Azure Cosmos DB](consistency-levels.md)
* [Wybierz odpowiedni poziom spójności w Azure Cosmos DB](consistency-levels-choosing.md)
* [Wady dotyczące spójności, dostępności i wydajności w Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Wady dostępności i wydajności dla różnych poziomów spójności](consistency-levels-tradeoffs.md)
* [Globalne skalowanie aprowizowanej przepływności](scaling-throughput.md)
* [Dystrybucja globalna: pod okapem](global-dist-under-the-hood.md)
