---
title: ARRAY_CONCAT w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat sposobu, w jaki tablica concat funkcja SQL system w Azure Cosmos DB zwraca tablicę, która jest wynikiem łączenia dwóch lub więcej wartości tablicy
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b6424a161b5fc4d0c075ade6f852b33dff20a09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332767"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
