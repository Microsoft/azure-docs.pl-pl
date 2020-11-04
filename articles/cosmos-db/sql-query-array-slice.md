---
title: ARRAY_SLICE w języku zapytań Azure Cosmos DB
description: Dowiedz się, w jaki sposób Funkcja systemowa SQL wycinka tablicy w Azure Cosmos DB zwraca część wyrażenia tablicy
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1808070f374614ae6cac9a27098ddf2f4d9d2d12
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332529"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca część wyrażenia tablicy.
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   To dowolne wyrażenie tablicowe.  
  
*num_expr*  
   Indeks liczbowy liczony od zera, od którego należy rozpocząć tablicę. Wartości ujemne mogą służyć do określenia początkowego indeksu względem ostatniego elementu tablicy, np.-1 odwołuje się do ostatniego elementu w tablicy.  

*num_expr* Opcjonalne wyrażenie liczbowe, które ustawia maksymalną liczbę elementów w tablicy wyników.    

## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie tablicy.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak uzyskać różne wycinków tablicy przy użyciu `ARRAY_SLICE` .  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
