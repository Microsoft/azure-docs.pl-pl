---
title: Aprowizowanie przepływności kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak zainicjować przepływność na poziomie kontenera w Azure Cosmos DB przy użyciu Azure Portal, interfejsu wiersza polecenia, programu PowerShell i różnych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273292"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Aprowizowanie przepływności dla kontenera usługi Azure Cosmos

W tym artykule wyjaśniono, jak zainicjować przepływność na kontenerze (kolekcji, grafie lub tabeli) w Azure Cosmos DB. Można zainicjować przepływność na jednym kontenerze lub [zainicjować przepływność dla bazy danych](how-to-provision-database-throughput.md) i udostępnić ją między kontenerami w bazie danych. Przepływność można zainicjować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure Cosmos DB zestawów SDK.

## <a name="azure-portal"></a>Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera (lub tabeli lub grafu).
   * Wprowadź wartość klucza partycji (na przykład `/userid`).
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz przycisk **OK**.

    ![Zrzut ekranu okienka usługi Data Explorer z wyróżnioną pozycją Nowa kolekcja](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Interfejs wiersza polecenia platformy Azure lub program PowerShell

Aby utworzyć kontener z dedykowaną przepływność, zobacz

* [Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container)
* [Tworzenie kontenera przy użyciu programu PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Jeśli zainicjowano przepływność na kontenerze na koncie usługi Azure Cosmos skonfigurowanym za pomocą interfejsu API Azure Cosmos DB dla MongoDB, `/myShardKey` Użyj dla ścieżki klucza partycji. Jeśli zainicjowano przepływność na kontenerze na koncie usługi Azure Cosmos skonfigurowanym pod warunkiem interfejs API Cassandra `/myPrimaryKey` , użyj dla ścieżki klucza partycji.

## <a name="net-sdk"></a>Zestaw SDK .NET

> [!Note]
> Użyj Cosmos SDK dla interfejsu API SQL, aby zainicjować przepływność dla wszystkich Cosmos DB interfejsów API, z wyjątkiem interfejs API Cassandra.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>Język SQL, usługa MongoDB, język Gremlin oraz interfejsy API tabel
### <a name="net-v2-sdk"></a>Zestaw .NET V2 SDK

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

### <a name="net-v3-sdk"></a>Zestaw SDK dla platformy .NET v3

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

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

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Interfejs API rozwiązania Cassandra

Podobne polecenia mogą być wydawane za poorednictwem dowolnego sterownika zgodnego z CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Zmienianie lub zmiana przepływności dla tabeli Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [Jak zapewnić przepustowość bazy danych](how-to-provision-database-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)
