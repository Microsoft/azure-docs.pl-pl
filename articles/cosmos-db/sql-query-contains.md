---
title: Zawiera w Azure Cosmos DB języku zapytań
description: Dowiedz się, jak Funkcja systemowa SQL w Azure Cosmos DB zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu zawiera sekundę
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4877272fc2db521977a4111317118380399d27c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84322707"
---
# <a name="contains-azure-cosmos-db"></a>ZAWIERA (Azure Cosmos DB)

Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie.  
  
## <a name="syntax"></a>Składnia
  
```sql
CONTAINS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu do przeszukania.  
  
*str_expr2*  
   Jest wyrażeniem ciągu do znalezienia.  

*bool_expr* Opcjonalna wartość dla ignorowania wielkości liter. Po ustawieniu na wartość true program zawiera wyszukiwanie bez uwzględniania wielkości liter. Jeśli nie określono tego parametru, ta wartość jest równa false.
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza, czy "ABC" zawiera "AB" i czy "ABC" zawiera "A".  
  
```sql
SELECT CONTAINS("abc", "ab", false) AS c1, CONTAINS("abc", "A", false) AS c2, CONTAINS("abc", "A", true) AS c3
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

Użycie obiektu RU dla zawiera zwiększy się, ponieważ Kardynalność właściwości w funkcji systemowej rośnie. Innymi słowy, jeśli sprawdzasz, czy wartość właściwości zawiera określony ciąg, opłata za zapytanie RU będzie zależeć od liczby możliwych wartości tej właściwości.

Rozważmy na przykład dwie właściwości: Miasto i kraj. Kardynalność miejscowości to 5 000, a Kardynalność kraju wynosi 200. Poniżej przedstawiono dwie przykładowe zapytania:

```sql
    SELECT * FROM c WHERE CONTAINS(c.town, "Red", false)
```

```sql
    SELECT * FROM c WHERE CONTAINS(c.country, "States", false)
```

Pierwsze zapytanie będzie prawdopodobnie używać więcej jednostek ru niż drugie zapytanie, ponieważ Kardynalność miejscowości jest wyższa niż kraj.

Jeśli rozmiar właściwości w programie zawiera wartość większą niż 1 KB dla niektórych dokumentów, aparat zapytań będzie musiał załadować te dokumenty. W takim przypadku aparat zapytań nie będzie w stanie w pełni oszacować zawiera indeks. Opłata za usługę RU dla zawiera będzie wysoka, jeśli masz dużą liczbę dokumentów o rozmiarach o rozmiarze większym niż 1 KB.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
