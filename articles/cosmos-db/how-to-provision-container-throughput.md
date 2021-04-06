---
title: Udostępnianie przepustowości kontenera w Azure Cosmos DB interfejsie API SQL
description: Dowiedz się, jak zainicjować przepływność na poziomie kontenera w Azure Cosmos DB interfejsie API SQL przy użyciu Azure Portal, interfejsu wiersza polecenia, programu PowerShell i różnych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100103"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Udostępnianie standardowej (ręcznej) przepływności na platformie Azure Cosmos — interfejs API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule wyjaśniono, jak zainicjować standardową (ręczną) przepływność na kontenerze w Azure Cosmos DB interfejsu API SQL. Można zainicjować przepływność na jednym kontenerze lub [zainicjować przepływność dla bazy danych](how-to-provision-database-throughput.md) i udostępnić ją między kontenerami w bazie danych. Przepływność można zainicjować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure Cosmos DB zestawów SDK.

Jeśli używasz innego interfejsu API, zobacz artykuł [API for MongoDB](how-to-provision-throughput-mongodb.md), [interfejs API CASSANDRA](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) , aby zapewnić przepływność.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **nowy kontener**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź wartość klucza partycji (na przykład `/ItemID`).
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz przycisk **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Zrzut ekranu okienka usługi Data Explorer z wyróżnioną pozycją Nowa kolekcja":::

## <a name="azure-cli-or-powershell"></a>Interfejs wiersza polecenia platformy Azure lub program PowerShell

Aby utworzyć kontener z dedykowaną przepływność, zobacz

* [Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container)
* [Tworzenie kontenera przy użyciu programu PowerShell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>Zestaw SDK .NET

> [!Note]
> Użyj Cosmos SDK dla interfejsu API SQL, aby zainicjować przepływność dla wszystkich Cosmos DB interfejsów API, z wyjątkiem Cassandra i MongoDB API.

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>Zestaw SDK dla języka JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [Jak zapewnić przepustowość standardową (ręczną) dla bazy danych](how-to-provision-database-throughput.md)
* [Jak udostępnić przepływność automatycznego skalowania na bazie danych](how-to-provision-autoscale-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
