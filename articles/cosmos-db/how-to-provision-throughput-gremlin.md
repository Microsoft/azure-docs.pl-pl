---
title: Obsługa przepływności na Azure Cosmos DB zasobów interfejsu API Gremlin
description: Dowiedz się, jak zainicjować przepływność kontenera, bazy danych i skalowania automatycznego w Azure Cosmos DB zasobów interfejsu API Gremlin. Będziesz używać Azure Portal, interfejsu wiersza polecenia, programu PowerShell i różnych innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 70eecc7843867a5832d962b7efaecda1b6ab4ae4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284002"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Inicjowanie obsługi administracyjnej bazy danych, kontenera lub automatycznego skalowania na Azure Cosmos DB zasobów interfejsu API Gremlin

W tym artykule wyjaśniono, jak zainicjować przepływność w Azure Cosmos DB interfejsie API Gremlin. Można udostępniać standardowe (ręczne) lub automatyczne skalowanie przepływności dla kontenera lub bazy danych i udostępnić je między kontenerami w bazie danych. Przepływność można zainicjować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure Cosmos DB zestawów SDK.

Jeśli używasz innego interfejsu API, zobacz [SQL API](how-to-provision-container-throughput.md), [interfejs API Cassandra](how-to-provision-throughput-cassandra.md), [API for MongoDB](how-to-provision-throughput-mongodb.md) — artykuły, aby zapewnić przepływność.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowy graf**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej. Wybierz opcję **zainicjuj przepływność bazy danych** , jeśli chcesz udostępnić przepływność na poziomie bazy danych.
   * Wprowadź identyfikator grafu.
   * Wprowadź wartość klucza partycji (na przykład `/ItemID`).
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz przycisk **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Zrzut ekranu przedstawiający Eksplorator danych, podczas tworzenia nowego wykresu z przepływności na poziomie bazy danych":::

## <a name="net-sdk"></a>Zestaw SDK .NET

> [!Note]
> Użyj Cosmos SDK dla interfejsu API SQL, aby zainicjować przepływność dla wszystkich Azure Cosmos DB interfejsów API, z wyjątkiem Cassandra i MongoDB API.

### <a name="provision-container-level-throughput"></a>Inicjowanie obsługi przepływności na poziomie kontenera

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

### <a name="provision-database-level-throughput"></a>Obsługa przepływności na poziomie bazy danych

# <a name="net-sdk-v2"></a>[ZESTAW .NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[ZESTAW .NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Szablony Azure Resource Manager mogą służyć do aprowizacji przepływności automatycznego skalowania dla zasobów na poziomie bazy danych lub kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać więcej przykładów, zobacz [Azure Resource Manager Azure Cosmos DB templates](templates-samples-gremlin.md) .

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do aprowizacji przepływności automatycznego skalowania dla bazy danych lub zasobów na poziomie kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać przykłady, zobacz [przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell może służyć do aprowizacji przepływności automatycznego skalowania dla bazy danych lub zasobów na poziomie kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać przykłady, zobacz [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)