---
title: Operatory zapytań SQL dla Azure Cosmos DB
description: Dowiedz się więcej na temat operatorów SQL, takich jak równość, porównanie i operatory logiczne obsługiwane przez Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335447"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatory w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule szczegółowo opisano różne operatory obsługiwane przez Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatory równości i porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **Operator** | **Niezdefiniowane** | **Null** | **Wartość logiczna** | **Liczba** | **Ciąg** | **Stream** | **Macierzy** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Null** | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Wartość logiczna** | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Liczba** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Ciąg** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane |
| **Stream** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane |
| **Macierzy** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** |

Dla operatorów porównania, takich jak `>` , `>=` ,,, `!=` `<` i `<=` , porównywanie między typami lub między dwoma obiektami lub tablicami powstaje `Undefined` .  

Jeśli wynik wyrażenia skalarnego to `Undefined` , element nie jest uwzględniony w wyniku, ponieważ nie jest `Undefined` równe `true` .

Na przykład porównanie następującej kwerendy między liczbą a wartością ciągu `Undefined` . W związku z tym filtr nie zawiera żadnych wyników.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)

Operatory logiczne działają na wartościach logicznych. W poniższych tabelach przedstawiono logiczne tabele prawdy dla następujących operatorów:

**OR — operator**

Zwraca `true` , gdy spełniony jest dowolny z warunków `true` .

|  | **True** | **False** | **Niezdefiniowane** |
| --- | --- | --- | --- |
| **True** |Prawda |Prawda |Prawda |
| **False** |Prawda |Fałsz |Niezdefiniowane |
| **Niezdefiniowane** |Prawda |Niezdefiniowane |Niezdefiniowane |

**AND — operator**

Zwraca, `true` gdy oba wyrażenia są `true` .

|  | **True** | **False** | **Niezdefiniowane** |
| --- | --- | --- | --- |
| **True** |Prawda |Fałsz |Niezdefiniowane |
| **False** |Fałsz |Fałsz |Fałsz |
| **Niezdefiniowane** |Niezdefiniowane |Fałsz |Niezdefiniowane |

**NOT — operator**

Odwraca wartość dowolnego wyrażenia logicznego.

|  | **NOT** |
| --- | --- |
| **True** |Fałsz |
| **False** |Prawda |
| **Niezdefiniowane** |Niezdefiniowane |

**Kolejność wykonywania działań**

Operatory logiczne `OR` `AND` i `NOT` mają poziom pierwszeństwa przedstawiony poniżej:

| **Operator** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## <a name="-operator"></a>* — Operator

Operator specjalny * projektuje cały element jako. Jeśli go używasz, musi być to jedyne pole w projekcji. Zapytanie `SELECT * FROM Families f` , które jest prawidłowe, ale `SELECT VALUE * FROM Families f` i  `SELECT *, f.id FROM Families f` jest nieprawidłowe.

## <a name="-and--operators"></a>? i? Operatory

Można użyć operatorów Trzyelementowy (?) i łączenia (??), aby kompilować wyrażenia warunkowe, jak w językach programowania, takich jak C# i JavaScript.

Można użyć ? operator do konstruowania nowych właściwości JSON na bieżąco. Na przykład następujące zapytanie klasyfikuje poziomy klasy do `elementary` lub `other` :

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Możesz również zagnieżdżać wywołania do? operator, jak w następującej kwerendzie: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Tak jak w przypadku innych operatorów zapytań,? operator wyklucza elementy, jeśli brakuje przywoływanych właściwości lub porównywane typy są różne.

Użyć? operator, który skutecznie sprawdza obecność właściwości w elemencie podczas wykonywania zapytania dotyczącego danych z częściową strukturą lub typem mieszanym. Na przykład następujące zapytanie zwraca wartość `lastName` , jeśli jest obecny, lub `surname` Jeśli `lastName` nie jest obecne.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Słowa kluczowe](sql-query-keywords.md)
- [SELECT — klauzula](sql-query-select.md)
