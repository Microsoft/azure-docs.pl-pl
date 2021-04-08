---
title: StartsWith w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji STARTSWITH systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7217a8416354de260f958bfb5b980a8fdc391618
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334926"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu zaczyna się od drugiego.  
  
## <a name="syntax"></a>Składnia
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu.
  
*str_expr2*  
   Jest wyrażeniem ciągu, które ma zostać porównane z początkiem *str_expr1*.

*bool_expr* Opcjonalna wartość dla ignorowania wielkości liter. Po ustawieniu na wartość true, STARTSWITH będzie wykonywać wyszukiwanie bez uwzględniania wielkości liter. Jeśli nie określono tego parametru, ta wartość jest równa false.

## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład sprawdza, czy ciąg "ABC" zaczyna się od "b" i "A".  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
