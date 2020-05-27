---
title: EndsWith w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji systemowej ENDSWITH SQL w Azure Cosmos DB, aby zwrócić wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu kończące się drugim
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0cd927af50eca04aa8162d9d8f292077d9e4165c
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844968"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)

 Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu ma kończyć się drugim.  
  
## <a name="syntax"></a>Składnia
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu.  
  
*str_expr2*  
   Jest wyrażeniem ciągu, które ma zostać porównane z końcem *str_expr1*.

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

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
