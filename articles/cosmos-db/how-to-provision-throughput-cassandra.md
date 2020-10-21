---
title: Obsługa przepływności na Azure Cosmos DB interfejs API Cassandra zasobów
description: Dowiedz się, jak udostępnić przepływność kontenera, bazy danych i skalowania automatycznego w Azure Cosmos DB interfejs API Cassandra zasoby. Będziesz używać Azure Portal, interfejsu wiersza polecenia, programu PowerShell i różnych innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 6b8b145c919a1f4e2ea9129a032da69bd30e6b71
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284119"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Zapewnianie elastycznej przepływności bazy danych, kontenera lub automatycznego skalowania dla zasobów interfejs API Cassandra Azure Cosmos DB

W tym artykule wyjaśniono, jak zainicjować przepływność w interfejs API Cassandra Azure Cosmos DB. Można udostępniać standardowe (ręczne) lub automatyczne skalowanie przepływności dla kontenera lub bazy danych i udostępnić je między kontenerami w bazie danych. Przepływność można zainicjować przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure Cosmos DB zestawów SDK.

Jeśli używasz innego interfejsu API, zobacz [SQL API](how-to-provision-container-throughput.md), [API for MONGODB](how-to-provision-throughput-mongodb.md), [Gremlin API](how-to-provision-throughput-gremlin.md) artykuły, aby zapewnić przepływność.

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową przestrzeń kluczy lub korzystając z istniejącej. Wybierz opcję **zainicjuj przepływność bazy danych** , jeśli chcesz udostępnić przepływność na poziomie przestrzeni kluczy.
   * Wprowadź identyfikator tabeli w poleceniu CQL.
   * Wprowadź wartość klucza podstawowego (na przykład `/userrID` ).
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz przycisk **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Zrzut ekranu przedstawiający Eksplorator danych, podczas tworzenia nowej kolekcji z przepływność na poziomie bazy danych":::

> [!Note]
> Jeśli zainicjowano przepływność na kontenerze na koncie usługi Azure Cosmos skonfigurowanym pod warunkiem interfejs API Cassandra, użyj `/myPrimaryKey` dla ścieżki klucza partycji.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> ZESTAW SDK PLATFORMY .NET

### <a name="provision-throughput-for-a-cassandra-table"></a>Obsługa przepływności dla tabeli Cassandra

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Podobne polecenia mogą być wydawane za poorednictwem dowolnego sterownika zgodnego z CQL.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Zmienianie lub zmiana przepływności tabeli Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Podobne polecenie można wykonać przy użyciu dowolnego sterownika zgodnego z CQL.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Szablony Azure Resource Manager mogą służyć do aprowizacji przepływności automatycznego skalowania dla zasobów na poziomie bazy danych lub kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać więcej przykładów, zobacz [Azure Resource Manager Azure Cosmos DB templates](templates-samples-cassandra.md) .

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure może służyć do aprowizacji przepływności automatycznego skalowania dla bazy danych lub zasobów na poziomie kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać przykłady, zobacz [przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples-cassandra.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell może służyć do aprowizacji przepływności automatycznego skalowania dla bazy danych lub zasobów na poziomie kontenera dla wszystkich Azure Cosmos DB interfejsów API. Aby uzyskać przykłady, zobacz [Azure PowerShell Samples for Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat aprowizowania przepływności w usłudze Azure Cosmos DB:

* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)