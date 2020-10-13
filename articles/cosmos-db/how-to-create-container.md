---
title: Tworzenie kontenera w usłudze Azure Cosmos DB
description: Dowiedz się, jak utworzyć kontener w Azure Cosmos DB przy użyciu Azure Portal, .NET, Java, Python, Node.js i innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2362326bccd90af997aa9237ec5f14e39ae62c85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020001"
---
# <a name="create-an-azure-cosmos-container"></a>Tworzenie kontenera usługi Azure Cosmos

W tym artykule opisano różne sposoby tworzenia kontenera usługi Azure Cosmos (kolekcji, tabeli lub grafów) przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, programu PowerShell lub obsługiwanych zestawów SDK. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i aprowizować przepływność.

> [!NOTE]
> Podczas tworzenia kontenerów upewnij się, że nie utworzysz dwóch kontenerów o takiej samej nazwie, ale o innej wielkości liter. Wynika to z faktu, że niektóre części platformy Azure nie uwzględniają wielkości liter. może to spowodować pomylenie/kolizję danych telemetrycznych i akcji w kontenerach z takimi nazwami.

## <a name="create-a-container-using-azure-portal"></a>Tworzenie kontenera przy użyciu witryny Azure Portal

### <a name="sql-api"></a><a id="portal-sql"></a>INTERFEJS API SQL

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **nowy kontener**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz partycji.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Kliknij przycisk **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Zrzut ekranu przedstawiający okienko Eksplorator danych z wyróżnionym nowym kontenerem":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **nowy kontener**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz fragmentu.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Kliknij przycisk **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Zrzut ekranu przedstawiający okienko Eksplorator danych z wyróżnionym nowym kontenerem":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Interfejs API rozwiązania Cassandra

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową przestrzeń kluczy, czy używasz istniejącej.
   * Wprowadź nazwę tabeli.
   * Wprowadź właściwości i określ klucz podstawowy.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Kliknij przycisk **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Zrzut ekranu przedstawiający okienko Eksplorator danych z wyróżnionym nowym kontenerem":::

> [!NOTE]
> W przypadku interfejsu API rozwiązania Cassandra jako klucz partycji jest używany klucz podstawowy.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Interfejs API języka Gremlin

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-graph-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowy graf**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator grafu.
   * Wybierz pojemność magazynu **Bez ograniczeń**.
   * Wprowadź klucz partycji dla wierzchołków.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Kliknij przycisk **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Zrzut ekranu przedstawiający okienko Eksplorator danych z wyróżnionym nowym kontenerem":::

### <a name="table-api"></a><a id="portal-table"></a>Interfejs API tabel

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-table-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wprowadź identyfikator tabeli.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Kliknij przycisk **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Zrzut ekranu przedstawiający okienko Eksplorator danych z wyróżnionym nowym kontenerem":::

> [!Note]
> W przypadku interfejsu API tabel klucz partycji jest określany każdorazowo podczas dodawania nowego wiersza.

## <a name="create-a-container-using-azure-cli"></a>Tworzenie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Poniższe linki pokazują, jak utworzyć zasoby kontenera dla Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure.

Aby zapoznać się z listą wszystkich przykładów interfejsu wiersza polecenia platformy Azure we wszystkich Azure Cosmos DB interfejsów API, zobacz [przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md).

* [Tworzenie kontenera za pomocą interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-container)
* [Tworzenie kolekcji dla Azure Cosmos DB interfejsu API MongoDB w interfejsie wiersza polecenia platformy Azure](./scripts/cli/mongodb/create.md)
* [Tworzenie tabeli Cassandra przy użyciu interfejsu wiersza polecenia platformy Azure](./scripts/cli/cassandra/create.md)
* [Tworzenie grafu Gremlin przy użyciu interfejsu wiersza polecenia platformy Azure](./scripts/cli/gremlin/create.md)
* [Tworzenie tabeli interfejs API tabel przy użyciu interfejsu wiersza polecenia platformy Azure](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>Tworzenie kontenera przy użyciu programu PowerShell

Poniższe linki pokazują, jak utworzyć zasoby kontenera dla Azure Cosmos DB przy użyciu programu PowerShell.

Aby uzyskać listę wszystkich przykładów programu PowerShell dla wszystkich Azure Cosmos DB interfejsów API, zobacz, [przykłady dla programu PowerShell](powershell-samples.md)

* [Tworzenie kontenera przy użyciu programu PowerShell](manage-with-powershell.md#create-container)
* [Tworzenie kolekcji dla Azure Cosmos DB interfejsu API MongoDB przy użyciu programu PowerShell](./scripts/powershell/mongodb/create.md)
* [Tworzenie tabeli Cassandra przy użyciu programu PowerShell](./scripts/powershell/cassandra/create.md)
* [Tworzenie grafu Gremlin przy użyciu programu PowerShell](./scripts/powershell/gremlin/create.md)
* [Tworzenie tabeli interfejs API tabel przy użyciu programu PowerShell](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>Tworzenie kontenera przy użyciu zestawu .NET SDK

Jeśli wystąpi wyjątek limitu czasu podczas tworzenia kolekcji, wykonaj operację odczytu, aby sprawdzić, czy kolekcja została utworzona pomyślnie. Operacja odczytu zgłasza wyjątek do momentu pomyślnego wykonania operacji tworzenia kolekcji. Aby uzyskać listę kodów stanu obsługiwanych przez operację tworzenia, zobacz [kody stanu HTTP dla Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artykułu.

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>Interfejs API SQL i interfejs API języka Gremlin

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Protokół MongoDB Wire nie rozumie koncepcji [jednostek żądania](request-units.md). Aby utworzyć nową kolekcję z zainicjowaną przepływność, użyj Azure Portal lub Cosmos DB zestawów SDK dla interfejsu API SQL.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Interfejs API rozwiązania Cassandra

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](account-overview.md)
