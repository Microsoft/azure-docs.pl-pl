---
title: ARRAY_CONCAT w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat sposobu, w jaki tablica concat funkcja SQL system w Azure Cosmos DB zwraca tablicę, która jest wynikiem łączenia dwóch lub więcej wartości tablicy
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78295882"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Zwraca tablicę, która jest wynikiem połączenia co najmniej dwóch wartości tablicy.  
  
## <a name="syntax"></a>Składnia
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   To wyrażenie tablicowe umożliwiające połączenie z innymi wartościami. `ARRAY_CONCAT`Funkcja wymaga co najmniej dwóch argumentów *arr_expr* .  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie tablicy.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład przedstawia sposób łączenia dwóch tablic.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
