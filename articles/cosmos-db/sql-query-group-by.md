---
title: Klauzula GROUP BY w Azure Cosmos DB
description: Zapoznaj się z klauzulą GROUP BY dla Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: b602b56d37cec0e23d31318f6675d031bdd6bcdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83700991"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Klauzula GROUP BY w Azure Cosmos DB

Klauzula GROUP BY dzieli wyniki zapytania zgodnie z wartościami jednej lub więcej określonych właściwości.

## <a name="syntax"></a>Składnia

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumenty

- `<scalar_expression_list>`

   Określa wyrażenia, które będą używane do dzielenia wyników zapytania.

- `<scalar_expression>`
  
   Każde wyrażenie skalarne jest dozwolone z wyjątkiem skalarnych podzapytań i agregacji skalarnych. Każde wyrażenie skalarne musi zawierać co najmniej jedno odwołanie do właściwości. Nie ma żadnego limitu liczby pojedynczych wyrażeń ani kardynalności poszczególnych wyrażeń.

## <a name="remarks"></a>Uwagi
  
  Gdy zapytanie używa klauzuli GROUP BY, klauzula SELECT może zawierać tylko podzestaw właściwości i funkcji systemowych zawartych w klauzuli GROUP BY. Jedynym wyjątkiem są [zagregowane funkcje systemowe](sql-query-aggregates.md), które mogą występować w klauzuli SELECT bez uwzględniania ich w klauzuli Group by. Można zawsze uwzględnić wartości literału w klauzuli SELECT.

  Klauzula GROUP BY musi być późniejsza niż klauzula SELECT, FROM i WHERE oraz przed klauzulą LIMIT przesunięcia. Obecnie nie można używać klauzuli GROUP BY z klauzulą ORDER BY, ale jest to planowane.

  Klauzula GROUP BY nie zezwala na żadną z następujących wartości:
  
- Właściwości aliasowania lub funkcje systemowe aliasowania (aliasowanie jest nadal dozwolone w klauzuli SELECT)
- Zapytania podrzędne
- Agreguj funkcje systemowe (są dozwolone tylko w klauzuli SELECT)

Zapytania z zagregowaną funkcją systemową i podzapytaniem z `GROUP BY` nie są obsługiwane. Na przykład następujące zapytanie nie jest obsługiwane:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Przykłady

W poniższych przykładach użyto zestawu danych odżywiania dostępnego za pomocą [środowisko testowe zapytań Azure Cosmos DB](https://www.documentdb.com/sql/demo).

Na przykład Oto zapytanie, które zwraca łączną liczbę elementów w każdej z nich:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Niektóre wyniki to (pierwsze słowo kluczowe jest używane do ograniczania wyników):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

To zapytanie ma dwa wyrażenia używane do dzielenia wyników:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Oto niektóre wyniki:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

To zapytanie zawiera funkcję systemową w klauzuli GROUP BY:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Oto niektóre wyniki:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

To zapytanie używa zarówno słów kluczowych, jak i funkcji systemowych w wyrażeniu właściwości elementu:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Wyniki są następujące:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie pracy](sql-query-getting-started.md)
- [SELECT — klauzula](sql-query-select.md)
- [Funkcje agregujące](sql-query-aggregates.md)
