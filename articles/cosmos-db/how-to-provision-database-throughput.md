---
title: Obsługa przepływności bazy danych w Azure Cosmos DB interfejsie API SQL
description: Dowiedz się, jak zainicjować przepływność na poziomie bazy danych w Azure Cosmos DB interfejsu API SQL przy użyciu Azure Portal, interfejsu wiersza polecenia, programu PowerShell i różnych zestawów SDK.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63de1c970a118ddf24c52e7e14a0632a4eee7fba
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086299"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Udostępnianie standardowej (ręcznej) przepływności dla bazy danych w interfejsie API Azure Cosmos DB-SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule wyjaśniono, jak zainicjować standardową (ręczną) przepływność bazy danych w Azure Cosmos DB interfejsie API SQL. Można zainicjować przepływność dla jednego [kontenera](how-to-provision-container-throughput.md)lub dla bazy danych i udostępnić przepływność między kontenerami w tym kontenerze. Aby dowiedzieć się, kiedy należy używać poziomu kontenera i przepływności na poziomie bazy danych, zobacz artykuł [dotyczący obsługi przepływności dla kontenerów i baz danych](set-throughput.md) . Przepływność na poziomie bazy danych możesz aprowizować za pomocą witryny Azure Portal lub zestawów SDK usługi Azure Cosmos DB.

Jeśli używasz innego interfejsu API, zobacz artykuł [API for MongoDB](how-to-provision-throughput-mongodb.md), [interfejs API CASSANDRA](how-to-provision-throughput-cassandra.md), [Gremlin API](how-to-provision-throughput-gremlin.md) , aby zapewnić przepływność.

## <a name="provision-throughput-using-azure-portal"></a>Aprowizowanie przepływności przy użyciu witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. [Utwórz nowe konto usługi Azure Cosmos](create-sql-api-dotnet.md#create-account)lub Wybierz istniejące konto usługi Azure Cosmos.

1. Otwórz okienko **Data Explorer** i wybierz pozycję **Nowa baza danych** . Podaj następujące szczegóły:

   * Wprowadź identyfikator bazy danych.
   * Wybierz opcję **zainicjuj przepływność bazy danych** .
   * Wprowadź przepływność (na przykład 1000 jednostek RU).
   * Wybierz pozycję **OK** .

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Zrzut ekranu okna dialogowego Nowa baza danych":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Obsługa przepływności przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby utworzyć bazę danych o wspólnej przepływności, zobacz

* [Tworzenie bazy danych przy użyciu interfejsu wiersza polecenia platformy Azure](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Tworzenie bazy danych przy użyciu programu PowerShell](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Aprowizowanie przepływności przy użyciu zestawu .NET SDK

> [!Note]
> Za pomocą usługi Azure Cosmos SDK dla interfejsu API SQL można zainicjować obsługę przepływności dla wszystkich interfejsów API. Poniższego przykładu możesz też opcjonalnie użyć w przypadku interfejsu API Cassandra.

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

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej o aprowizacji przepływności w Azure Cosmos DB:

* [Globalne skalowanie aprowizowanej przepływności](./request-units.md)
* [Aprowizacja przepływności kontenerów i baz danych](set-throughput.md)
* [Jak zapewnić przepustowość standardową (ręczną) dla kontenera](how-to-provision-container-throughput.md)
* [Jak udostępnić przepływność automatycznego skalowania dla kontenera](how-to-provision-autoscale-throughput.md)
* [Jednostki żądania i przepływności w usłudze Azure Cosmos DB](request-units.md)