---
title: Tworzenie kontenera w interfejsie API Azure Cosmos DB dla MongoDB
description: Dowiedz się, jak utworzyć kontener w interfejsie API Azure Cosmos DB dla MongoDB przy użyciu Azure Portal, .NET, Java, Node.js i innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a5669b15c041f663605a62ef8d02b206928d0c14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101599"
---
# <a name="create-a-container-in-azure-cosmos-db-api-for-mongodb"></a>Tworzenie kontenera w interfejsie API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym artykule opisano różne sposoby tworzenia kontenera w interfejsie API Azure Cosmos DB MongoDB. Przedstawiono w nim sposób tworzenia kontenera przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, programu PowerShell lub obsługiwanych zestawów SDK. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i aprowizować przepływność.

W tym artykule opisano różne sposoby tworzenia kontenera w interfejsie API Azure Cosmos DB MongoDB. Jeśli używasz innego interfejsu API, zobacz artykuł [SQL API](how-to-create-container.md), [interfejs API Cassandra](how-to-create-container-cassandra.md), [interfejs API Gremlin](how-to-create-container-gremlin.md)i artykuły [interfejs API tabel](how-to-create-container-table.md) do utworzenia kontenera.

> [!NOTE]
> Podczas tworzenia kontenerów upewnij się, że nie utworzysz dwóch kontenerów o takiej samej nazwie, ale o innej wielkości liter. Wynika to z faktu, że niektóre części platformy Azure nie uwzględniają wielkości liter. może to spowodować pomylenie/kolizję danych telemetrycznych i akcji w kontenerach z takimi nazwami.

## <a name="create-using-azure-portal"></a><a id="portal-mongodb"></a>Tworzenie za pomocą witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Eksplorator danych** i wybierz pozycję **nowy kontener**. Następnie podaj następujące szczegóły:

   * Wskaż, czy tworzysz nową bazę danych, czy używasz istniejącej.
   * Wprowadź identyfikator kontenera.
   * Wprowadź klucz fragmentu.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz przycisk **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Zrzut ekranu przedstawiający interfejs Azure Cosmos DB API dla MongoDB, okno dialogowe Dodawanie kontenera":::

## <a name="create-using-net-sdk"></a><a id="dotnet-mongodb"></a>Tworzenie przy użyciu zestawu .NET SDK

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> Protokół MongoDB Wire nie rozumie koncepcji [jednostek żądania](request-units.md). Aby utworzyć nową kolekcję z zainicjowaną przepływność, użyj Azure Portal lub Cosmos DB zestawów SDK dla interfejsu API SQL.

Jeśli wystąpi wyjątek limitu czasu podczas tworzenia kolekcji, wykonaj operację odczytu, aby sprawdzić, czy kolekcja została utworzona pomyślnie. Operacja odczytu zgłasza wyjątek do momentu pomyślnego wykonania operacji tworzenia kolekcji. Aby uzyskać listę kodów stanu obsługiwanych przez operację tworzenia, zobacz [kody stanu HTTP dla Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artykułu.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Tworzenie przy użyciu interfejsu wiersza polecenia platformy Azure

[Utwórz kolekcję dla Azure Cosmos DB interfejsu API MongoDB za pomocą interfejsu wiersza polecenia platformy Azure](./scripts/cli/mongodb/create.md). Aby zapoznać się z listą wszystkich przykładów interfejsu wiersza polecenia platformy Azure we wszystkich Azure Cosmos DB interfejsów API, zobacz [przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Tworzenie za pomocą programu PowerShell

[Utwórz kolekcję dla Azure Cosmos DB interfejsu API MongoDB przy użyciu programu PowerShell](./scripts/powershell/mongodb/create.md). Aby uzyskać listę wszystkich przykładów programu PowerShell dla wszystkich Azure Cosmos DB interfejsów API, zobacz, [przykłady dla programu PowerShell](powershell-samples.md)

## <a name="create-a-container-using-azure-resource-manager-templates"></a>Tworzenie kontenera przy użyciu szablonów Azure Resource Manager

[Utwórz kolekcję dla Azure Cosmos DB interfejsu API MongoDB z szablonem Menedżer zasobów](./manage-with-templates.md#azure-cosmos-account-with-standard-provisioned-throughput).

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](./account-databases-containers-items.md)