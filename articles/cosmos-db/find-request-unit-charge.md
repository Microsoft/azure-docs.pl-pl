---
title: Znajdź opłatę jednostki żądania (RU) dla zapytania SQL w Azure Cosmos DB
description: Dowiedz się, jak znaleźć opłatę jednostki żądania (RU) dla zapytań SQL wykonywanych w odniesieniu do kontenera usługi Azure Cosmos. Możesz użyć języków Azure Portal, .NET, Java, Python i Node.js, aby znaleźć opłatę za RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: f716245d93727a0447bd1c67924ce7577c70b503
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201295"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Znajdowanie opłaty za jednostkę żądania dla operacji wykonywanych w Azure Cosmos DB interfejsie API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB obsługuje wiele interfejsów API, takich jak SQL, MongoDB, Cassandra, Gremlin i Table. Każdy interfejs API ma swój własny zestaw operacji bazy danych. Te operacje obejmują między innymi odczyty punktów prostych i zapisów w złożonych zapytaniach. Każda operacja bazy danych zużywa zasoby systemowe na podstawie złożoności operacji.

Koszt wszystkich operacji bazy danych jest normalizowany przez usługę Azure Cosmos DB i jest wyrażany za pomocą jednostek żądań (lub w skrócie RU). Opłata za żądanie jest jednostką żądania zużywaną przez wszystkie operacje bazy danych. Możesz traktować jednostek ru jako walutę wydajności, co umożliwia abstrakcję zasobów systemowych, takich jak procesor CPU, operacje we/wy i pamięć, które są wymagane do wykonania operacji bazy danych obsługiwanych przez Azure Cosmos DB. Niezależnie od tego, którego interfejsu API używasz do interakcji z kontenerem usługi Azure Cosmos, koszty są zawsze mierzone za pomocą jednostek RU. Niezależnie od tego, czy operacja bazy danych jest zapisem, punktem odczytu lub zapytania, koszty są zawsze mierzone w jednostek ru. Aby dowiedzieć się więcej, zapoznaj się z artykułem [jednostki żądania i jej uwagi](request-units.md) .

W tym artykule przedstawiono różne sposoby znajdowania zużycia [jednostek żądań](request-units.md) (ru) dla każdej operacji wykonywanej względem kontenera w Azure Cosmos DB API SQL. Jeśli używasz innego interfejsu API, zobacz [API for MongoDB](find-request-unit-charge-mongodb.md), [interfejs API CASSANDRA](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)i artykuły [interfejs API tabel](find-request-unit-charge-table.md) w celu znalezienia opłaty za ru/s.

Obecnie można mierzyć to użycie tylko przy użyciu Azure Portal lub przez sprawdzenie odpowiedzi wysyłanej z Azure Cosmos DB za pomocą jednego z zestawów SDK. Jeśli używasz interfejsu API SQL, masz wiele opcji znajdowania zużycia RU dla operacji w odniesieniu do kontenera usługi Azure Cosmos.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account) i podawanie danych z danymi lub Wybierz istniejące konto usługi Azure Cosmos, które zawiera już dane.

1. Przejdź do okienka **Eksplorator danych** , a następnie wybierz kontener, w którym chcesz rozpocząć pracę.

1. Wybierz pozycję **Nowe zapytanie SQL**.

1. Wprowadź prawidłowe zapytanie, a następnie wybierz pozycję **wykonaj zapytanie**.

1. Wybierz pozycję **Statystyka zapytania** , aby wyświetlić rzeczywistą opłatę za żądanie dla wykonywanego żądania.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Zrzut ekranu przedstawiający opłaty za żądanie zapytania SQL w Azure Portal":::

## <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

Obiekty zwracane z [zestawu .NET SDK V2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) uwidaczniają `RequestCharge` Właściwość:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

Obiekty zwracane z [zestawu .NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) uwidaczniają `RequestCharge` Właściwość:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji sieci Web platformy .NET przy użyciu konta interfejsu API SQL w Azure Cosmos DB](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>Korzystanie z zestawu SDK języka Java

Obiekty, które są zwracane z [zestawu Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) uwidaczniają `getRequestCharge()` metodę:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji Java przy użyciu konta interfejsu API SQL Azure Cosmos DB](create-sql-api-java.md).

## <a name="use-the-nodejs-sdk"></a>Korzystanie z zestawu SDK Node.js

Obiekty, które są zwracane z [ zestawuNode.js SDK](https://www.npmjs.com/package/@azure/cosmos) uwidaczniają `headers` podobiekt, który mapuje wszystkie nagłówki zwracane przez podstawowy interfejs API protokołu HTTP. Opłata za żądanie jest dostępna pod `x-ms-request-charge` kluczem:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji Node.js przy użyciu Azure Cosmos DB konta interfejsu API SQL](create-sql-api-nodejs.md). 

## <a name="use-the-python-sdk"></a>Używanie zestawu Python SDK

`CosmosClient`Obiekt z [zestawu SDK języka Python](https://pypi.org/project/azure-cosmos/) uwidacznia `last_response_headers` słownik, który mapuje wszystkie nagłówki zwrócone przez podstawowy interfejs API protokołu HTTP dla ostatniej wykonanej operacji. Opłata za żądanie jest dostępna pod `x-ms-request-charge` kluczem:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie aplikacji w języku Python przy użyciu konta interfejsu API SQL Azure Cosmos DB](create-sql-api-python.md). 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizowania użycia RU, zobacz następujące artykuły:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Globalne skalowanie aprowizowanej przepływności](./request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Obsługa przepływności dla kontenera](how-to-provision-container-throughput.md)
* [Monitoruj i Debuguj przy użyciu metryk w Azure Cosmos DB](use-metrics.md)
