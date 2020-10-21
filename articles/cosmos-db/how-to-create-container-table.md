---
title: Tworzenie kontenera w Azure Cosmos DB interfejs API tabel
description: Dowiedz się, jak utworzyć kontener w Azure Cosmos DB interfejs API tabel przy użyciu Azure Portal, .NET, Java, Python, Node.js i innych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: ccda92f094d28b27e48de689c3b39c4f8a9bfaa3
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284138"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Tworzenie kontenera w Azure Cosmos DB interfejs API tabel

W tym artykule opisano różne sposoby tworzenia kontenera w Azure Cosmos DB interfejs API tabel. Przedstawiono w nim sposób tworzenia kontenera przy użyciu Azure Portal, interfejsu wiersza polecenia platformy Azure, programu PowerShell lub obsługiwanych zestawów SDK. W tym artykule pokazano, jak utworzyć kontener, określić klucz partycji i aprowizować przepływność.

W tym artykule opisano różne sposoby tworzenia kontenera w Azure Cosmos DB interfejs API tabel. Jeśli używasz innego interfejsu API, zobacz artykuł [API for MongoDB](how-to-create-container-mongodb.md), [interfejs API CASSANDRA](how-to-create-container-cassandra.md), [Gremlin API](how-to-create-container-gremlin.md)i [SQL API](how-to-create-container.md) , aby utworzyć kontener.

> [!NOTE]
> Podczas tworzenia kontenerów upewnij się, że nie utworzysz dwóch kontenerów o takiej samej nazwie, ale o innej wielkości liter. Wynika to z faktu, że niektóre części platformy Azure nie uwzględniają wielkości liter. może to spowodować pomylenie/kolizję danych telemetrycznych i akcji w kontenerach z takimi nazwami.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Tworzenie za pomocą witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-table-dotnet.md#create-a-database-account)lub Wybierz istniejące konto.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa tabela**. Następnie podaj następujące szczegóły:

   * Wprowadź identyfikator tabeli.
   * Wprowadź przepływność, która ma zostać zainicjowana (na przykład 1000 jednostek ru).
   * Wybierz przycisk **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Zrzut ekranu interfejsu API tabel, okno dialogowe Dodawanie tabeli":::

> [!Note]
> W przypadku interfejsu API tabel klucz partycji jest określany każdorazowo podczas dodawania nowego wiersza.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Tworzenie przy użyciu interfejsu wiersza polecenia platformy Azure

[Utwórz tabelę interfejs API tabel za pomocą interfejsu wiersza polecenia platformy Azure](./scripts/cli/table/create.md). Aby zapoznać się z listą wszystkich przykładów interfejsu wiersza polecenia platformy Azure we wszystkich Azure Cosmos DB interfejsów API, zobacz [przykłady interfejsu wiersza polecenia platformy Azure dla Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Tworzenie za pomocą programu PowerShell

[Utwórz tabelę interfejs API tabel przy użyciu programu PowerShell](./scripts/powershell/table/create.md). Aby uzyskać listę wszystkich przykładów programu PowerShell dla wszystkich Azure Cosmos DB interfejsów API, zobacz, [przykłady dla programu PowerShell](powershell-samples.md)

## <a name="next-steps"></a>Następne kroki

* [Partitioning in Azure Cosmos DB (Partycjonowanie w usłudze Azure Cosmos DB)](partitioning-overview.md)
* [Jednostki żądania w usłudze Azure Cosmos DB](request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Praca z kontem usługi Azure Cosmos](account-overview.md)

