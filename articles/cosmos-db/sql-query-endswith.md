---
title: EndsWith w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemowej ENDSWITH SQL w Azure Cosmos DB, aby zwrócić wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu kończące się drugim
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd34a41fd9757d91845eb53626b00381c7ddda7a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100290"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu ma kończyć się drugim.  
  
## <a name="syntax"></a>Składnia
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu.  
  
*str_expr2*  
   Jest wyrażeniem ciągu, które ma zostać porównane z końcem *str_expr1* .

*bool_expr* Opcjonalna wartość dla ignorowania wielkości liter. Po ustawieniu na wartość true, ENDSWITH będzie wykonywać wyszukiwanie bez uwzględniania wielkości liter. Jeśli nie określono tego parametru, ta wartość jest równa false.
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład sprawdza, czy ciąg "ABC" jest zakończony znakiem "b" i "bC".  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

Użycie RU przez EndsWith zwiększy się w miarę kardynalności właściwości w funkcji systemowej. Innymi słowy, jeśli sprawdzasz, czy wartość właściwości jest zakończona określonym ciągiem, opłata za zapytanie RU będzie zależeć od liczby możliwych wartości tej właściwości.

Rozważmy na przykład dwie właściwości: Miasto i kraj. Kardynalność miejscowości to 5 000, a Kardynalność kraju wynosi 200. Poniżej przedstawiono dwie przykładowe zapytania:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

Pierwsze zapytanie będzie prawdopodobnie używać więcej jednostek ru niż drugie zapytanie, ponieważ Kardynalność miejscowości jest wyższa niż kraj.

Jeśli rozmiar właściwości w EndsWith jest większy niż 1 KB dla niektórych dokumentów, aparat zapytań będzie musiał załadować te dokumenty. W takim przypadku aparat zapytań nie będzie w stanie w pełni oszacować EndsWith z indeksem. Opłata za usługę RU dla EndsWith będzie wysoka, jeśli masz dużą liczbę dokumentów o rozmiarze większym niż 1 KB.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
