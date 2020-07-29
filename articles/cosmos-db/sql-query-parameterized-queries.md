---
title: Zapytania sparametryzowane w Azure Cosmos DB
description: Dowiedz się, w jaki sposób zapytania parametryczne SQL zapewniają niezawodne obsługiwanie i ucieczki danych wejściowych użytkownika, i Zapobiegaj przypadkowemu narażeniu danych przez wstrzyknięcie kodu SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74870823"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Zapytania sparametryzowane w Azure Cosmos DB

Cosmos DB obsługuje zapytania z parametrami wyrażonymi przez znajomą @ Notation. Sparametryzowane SQL zapewnia niezawodną obsługę i ucieczkę danych wejściowych użytkownika i zapobiega przypadkowemu narażeniu danych przez iniekcję SQL.

## <a name="examples"></a>Przykłady

Można na przykład napisać zapytanie, które przyjmuje `lastName` i `address.state` jako parametry, i wykonać je dla różnych wartości `lastName` i `address.state` na podstawie danych wejściowych użytkownika.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Następnie można wysłać to żądanie do Cosmos DB jako sparametryzowane zapytanie JSON podobne do następujących:

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

Wartości parametrów mogą być dowolnymi prawidłowymi wartościami JSON: String, Numbers, Boolean, null, tablice parzyste lub zagnieżdżony kod JSON. Ponieważ Cosmos DB jest bez schematu, parametry nie są sprawdzane względem żadnego typu.


## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelowanie danych dokumentów](modeling-data.md)
