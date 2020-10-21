---
title: Obsługa przepływności na Azure Cosmos DB interfejsie API dla zasobów MongoDB
description: Dowiedz się, jak udostępnić przepływność kontenera, bazy danych i skalowania automatycznego w Azure Cosmos DB interfejsie API dla zasobów MongoDB. Będziesz używać Azure Portal, interfejsu wiersza polecenia, programu PowerShell i różnych innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 44a69581f0763972dd30a016bf0826b31657a3d4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284001"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Inicjowanie obsługi administracyjnej bazy danych, kontenera lub automatycznego skalowania na Azure Cosmos DB API dla zasobów MongoDB

W tym artykule wyjaśniono, jak zainicjować przepływność w Azure Cosmos DB interfejsie API dla MongoDB. Można udostępniać standardowe (ręczne) lub automatyczne skalowanie przepływności dla kontenera lub bazy danych i udostępnić je między kontenerami w bazie danych. Przepływność można zainicjować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure Cosmos DB zestawów SDK.

Jeśli używasz innego interfejsu API, zobacz artykuł [SQL API](how-to-provision-container-throughput.md), [interfejs API Cassandra](how-to-provision-throughput-cassandra.md), artykuły [interfejsu API Gremlin](how-to-provision-throughput-gremlin.md) , aby zapewnić przepływność.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa kolekcja**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej. Wybierz opcję **zainicjuj przepływność bazy danych** , jeśli chcesz udostępnić przepływność na poziomie bazy danych.
   * Wprowadź identyfikator kolekcji.
   * Wprowadź wartość klucza partycji (na przykład `/ItemID`).
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz przycisk **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Zrzut ekranu przedstawiający Eksplorator danych, podczas tworzenia nowej kolekcji z przepływność na poziomie bazy danych":::

> [!Note]
> Jeśli zainicjowano przepływność na kontenerze na koncie usługi Azure Cosmos skonfigurowanym za pomocą interfejsu API Azure Cosmos DB dla MongoDB, użyj `/myShardKey` dla ścieżki klucza partycji.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> ZESTAW SDK PLATFORMY .NET

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Szablony Azure Resource Manager mogą służyć do aprowizacji przepływności automatycznego skalowania dla zasobów na poziomie bazy danych lub kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać więcej przykładów, zobacz [Azure Resource Manager Azure Cosmos DB templates](templates-samples-mongodb.md) .

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do aprowizacji przepływności automatycznego skalowania dla bazy danych lub zasobów na poziomie kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać przykłady, zobacz [przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell może służyć do aprowizacji przepływności automatycznego skalowania dla bazy danych lub zasobów na poziomie kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać przykłady, zobacz [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)