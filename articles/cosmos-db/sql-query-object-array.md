---
title: Praca z tablicami i obiektami w Azure Cosmos DB
description: Poznaj składnię SQL, aby utworzyć tablice i obiekty w Azure Cosmos DB. W tym artykule przedstawiono również przykłady wykonywania operacji na obiektach Array
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493791"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Praca z tablicami i obiektami w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kluczową funkcją interfejsu API SQL Azure Cosmos DB jest tworzenie tablic i obiektów. Ten dokument zawiera przykłady, które można odtworzyć przy użyciu [zestawu danych rodziny](sql-query-getting-started.md#upload-sample-data).

Oto przykład elementu w tym zestawie danych:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Tablice

Można skonstruować tablice, jak pokazano w następującym przykładzie:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Wyniki są następujące:

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

Możesz również użyć [wyrażenia Array](sql-query-subquery.md#array-expression) , aby utworzyć tablicę z wyników [podzapytania](sql-query-subquery.md) . To zapytanie pobiera wszystkie różne nazwy elementów podrzędnych w tablicy.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Wyniki są następujące:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Iteracja

Interfejs API SQL zapewnia obsługę iteracji w tablicach JSON przy użyciu [słowa kluczowego in](sql-query-keywords.md#in) w źródle from. W poniższym przykładzie:

```sql
SELECT *
FROM Families.children
```

Wyniki są następujące:

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

Następne zapytanie wykonuje iterację `children` w `Families` kontenerze. Tablica wyjściowa różni się od powyższego zapytania. Ten przykład dzieli `children` i spłaszcza wyniki do pojedynczej tablicy:  

```sql
SELECT *
FROM c IN Families.children
```

Wyniki są następujące:

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

Można filtrować więcej według poszczególnych wpisów tablicy, jak pokazano w następującym przykładzie:

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

Wyniki są następujące:

```json
[{
  "givenName": "Lisa"
}]
```

Można również agregować wynik iteracji tablicy. Na przykład następujące zapytanie liczy liczbę elementów podrzędnych między wszystkimi rodzinami:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Wyniki są następujące:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> W przypadku użycia słowa kluczowego IN dla iteracji nie można filtrować ani projektować żadnych właściwości poza tablicą. Zamiast tego należy użyć [sprzężeń](sql-query-join.md).

Aby uzyskać dodatkowe przykłady, Przeczytaj nasz [wpis w blogu na temat pracy z tablicami w Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Sprzężenia](sql-query-join.md)
