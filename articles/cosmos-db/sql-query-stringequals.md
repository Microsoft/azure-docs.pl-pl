---
title: StringEquals w języku zapytań Azure Cosmos DB
description: Dowiedz się, jak Funkcja systemowa SQL StringEquals w Azure Cosmos DB zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu jest zgodne z drugim
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47d899534de535b5cd6a7c3fb2df78cdadbe11f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338054"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu jest zgodne z drugim.  
  
## <a name="syntax"></a>Składnia
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   To pierwsze wyrażenie ciągu do porównania.  
  
*str_expr2*  
   To drugie wyrażenie ciągu do porównania.  

*bool_expr* Opcjonalna wartość dla ignorowania wielkości liter. Po ustawieniu na wartość true, StringEquals będzie wykonywać wyszukiwanie bez uwzględniania wielkości liter. Jeśli nie określono tego parametru, ta wartość jest równa false.
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza, czy "ABC" pasuje do "ABC" i czy "ABC" pasuje do "ABC".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
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

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
