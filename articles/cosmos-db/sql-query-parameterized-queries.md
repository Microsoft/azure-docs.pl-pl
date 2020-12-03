---
title: Zapytania sparametryzowane w Azure Cosmos DB
description: Dowiedz się, w jaki sposób zapytania parametryczne SQL zapewniają niezawodne obsługiwanie i ucieczki danych wejściowych użytkownika, i Zapobiegaj przypadkowemu narażeniu danych przez wstrzyknięcie kodu SQL.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549161"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Zapytania sparametryzowane w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB obsługuje zapytania z parametrami wyrażonymi przez znajomą @ Notation. Sparametryzowane SQL zapewnia niezawodną obsługę i ucieczkę danych wejściowych użytkownika i zapobiega przypadkowemu narażeniu danych przez iniekcję SQL.

## <a name="examples"></a>Przykłady

Można na przykład napisać zapytanie, które przyjmuje `lastName` i `address.state` jako parametry, i wykonać je dla różnych wartości `lastName` i `address.state` na podstawie danych wejściowych użytkownika.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Następnie można wysłać to żądanie do Azure Cosmos DB jako sparametryzowane zapytanie JSON podobne do następujących:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Poniższy przykład ustawia argument TOP z zapytaniem sparametryzowanym:

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Wartości parametrów mogą być dowolnymi prawidłowymi wartościami JSON: String, Numbers, Boolean, null, tablice parzyste lub zagnieżdżony kod JSON. Ponieważ Azure Cosmos DB jest bez schematu, parametry nie są sprawdzane względem żadnego typu.

Poniżej przedstawiono przykłady zapytań parametrycznych w każdym Azure Cosmos DB SDK:

- [Zestaw SDK platformy .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
