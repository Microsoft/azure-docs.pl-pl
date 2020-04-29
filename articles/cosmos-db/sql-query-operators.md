---
title: Operatory zapytań SQL dla Azure Cosmos DB
description: Dowiedz się więcej na temat operatorów SQL, takich jak równość, porównanie i operatory logiczne obsługiwane przez Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063570"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatory w Azure Cosmos DB

W tym artykule szczegółowo opisano różne operatory obsługiwane przez Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operatory równości i porównania

W poniższej tabeli przedstawiono wynik porównań równości w interfejsie API SQL między dwoma dowolnymi typami JSON.

| **Operator** | **Niezdefiniowane** | **Zero** | **Boolean** | **Liczba** | **Ciąg** | **Stream** | **Macierzy** |
|---|---|---|---|---|---|---|---|
| **Niezdefiniowane** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Zero** | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Boolean** | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Liczba** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane |
| **Ciąg** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane | Niezdefiniowane |
| **Stream** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** | Niezdefiniowane |
| **Macierzy** | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | Niezdefiniowane | **Ok** |

Dla operatorów porównania, takich `>`jak `>=`, `!=`, `<`,, `<=`i, porównywanie między typami lub między dwoma obiektami lub `Undefined`tablicami powstaje.  

Jeśli wynik wyrażenia skalarnego to `Undefined`, element nie jest uwzględniony w wyniku, ponieważ `Undefined` nie jest równe. `true`

## <a name="logical-and-or-and-not-operators"></a>Operatory logiczne (AND, OR i NOT)

Operatory logiczne działają na wartościach logicznych. W poniższych tabelach przedstawiono logiczne tabele prawdy dla następujących operatorów:

**OR — operator**

Zwraca `true` , gdy spełniony jest `true`dowolny z warunków.

|  | **Prawda** | **False** | **Niezdefiniowane** |
| --- | --- | --- | --- |
| **Prawda** |Prawda |Prawda |Prawda |
| **False** |Prawda |Fałsz |Niezdefiniowane |
| **Niezdefiniowane** |Prawda |Niezdefiniowane |Niezdefiniowane |

**AND — operator**

Zwraca `true` , gdy oba wyrażenia `true`są.

|  | **Prawda** | **False** | **Niezdefiniowane** |
| --- | --- | --- | --- |
| **Prawda** |Prawda |Fałsz |Niezdefiniowane |
| **False** |Fałsz |Fałsz |Fałsz |
| **Niezdefiniowane** |Niezdefiniowane |Fałsz |Niezdefiniowane |

**NOT — operator**

Odwraca wartość dowolnego wyrażenia logicznego.

|  | **NIEMOŻLIWE** |
| --- | --- |
| **Prawda** |Fałsz |
| **False** |Prawda |
| **Niezdefiniowane** |Niezdefiniowane |

**Kolejność wykonywania działań**

Operatory `OR` `AND`logiczne i `NOT` mają poziom pierwszeństwa przedstawiony poniżej:

| **Operator** | **Priorytet** |
| --- | --- |
| **NIEMOŻLIWE** |1 |
| **LUB** |2 |
| **ORAZ** |3 |

## <a name="-operator"></a>* — Operator

Operator specjalny * projektuje cały element jako. Jeśli go używasz, musi być to jedyne pole w projekcji. Zapytanie, które `SELECT * FROM Families f` jest prawidłowe, ale `SELECT VALUE * FROM Families f` i `SELECT *, f.id FROM Families f` jest nieprawidłowe.

## <a name="-and--operators"></a>? i? Operatory

Można użyć operatorów Trzyelementowy (?) i łączenia (??), aby kompilować wyrażenia warunkowe, jak w językach programowania, takich jak C# i JavaScript.

Można użyć ? operator do konstruowania nowych właściwości JSON na bieżąco. Na przykład następujące zapytanie klasyfikuje poziomy klasy do `elementary` lub: `other`

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

Użyć? operator, który skutecznie sprawdza obecność właściwości w elemencie podczas wykonywania zapytania dotyczącego danych z częściową strukturą lub typem mieszanym. Na przykład następujące zapytanie zwraca wartość `lastName` , jeśli jest obecny, `surname` lub `lastName` Jeśli nie jest obecne.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Następne kroki

- [Przykłady dla platformy .NET w usłudze Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Słowa kluczowe](sql-query-keywords.md)
- [SELECT — klauzula](sql-query-select.md)
