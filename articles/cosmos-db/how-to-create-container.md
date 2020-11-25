---
title: Tworzenie kontenera w Azure Cosmos DB interfejsu API SQL
description: Dowiedz się, jak utworzyć kontener w Azure Cosmos DB interfejsie API SQL przy użyciu Azure Portal, .NET, Java, Python, Node.js i innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006852"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Tworzenie kontenera w Azure Cosmos DB interfejsu API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule wyjaśniono różne sposoby tworzenia kontenera w Azure Cosmos DB interfejsie API SQL. Przedstawiono w nim sposób tworzenia kontenera przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, programu PowerShell lub obsługiwanych zestawów SDK. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i aprowizować przepływność.

W tym artykule wyjaśniono różne sposoby tworzenia kontenera w Azure Cosmos DB interfejsie API SQL. Jeśli używasz innego interfejsu API, zobacz [API for MongoDB](how-to-create-container-mongodb.md), [interfejs API CASSANDRA](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)i artykuły [interfejs API tabel](how-to-create-container-table.md) do utworzenia kontenera.

> [!NOTE]
> Podczas tworzenia kontenerów upewnij się, że nie utworzysz dwóch kontenerów o takiej samej nazwie, ale o innej wielkości liter. Wynika to z faktu, że niektóre części platformy Azure nie uwzględniają wielkości liter. może to spowodować pomylenie/kolizję danych telemetrycznych i akcji w kontenerach z takimi nazwami.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Tworzenie kontenera przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **nowy kontener**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz partycji.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz pozycję **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Zrzut ekranu przedstawiający okienko Eksplorator danych z wyróżnionym nowym kontenerem":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

[Utwórz kontener za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container). Aby zapoznać się z listą wszystkich przykładów interfejsu wiersza polecenia platformy Azure we wszystkich Azure Cosmos DB interfejsów API, zobacz [przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Tworzenie kontenera przy użyciu programu PowerShell

[Utwórz kontener za pomocą programu PowerShell](manage-with-powershell.md#create-container). Aby uzyskać listę wszystkich przykładów programu PowerShell dla wszystkich Azure Cosmos DB interfejsów API, zobacz, [przykłady dla programu PowerShell](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Tworzenie kontenera przy użyciu zestawu .NET SDK

Jeśli wystąpi wyjątek limitu czasu podczas tworzenia kolekcji, wykonaj operację odczytu, aby sprawdzić, czy kolekcja została utworzona pomyślnie. Operacja odczytu zgłasza wyjątek do momentu pomyślnego wykonania operacji tworzenia kolekcji. Aby uzyskać listę kodów stanu obsługiwanych przez operację tworzenia, zobacz [kody stanu HTTP dla Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artykułu.

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](./account-databases-containers-items.md)