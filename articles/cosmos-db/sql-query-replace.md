---
title: Zastąp w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat funkcji systemowej SQL Zastąp w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3afd2524949b1209ac42542a012b74559234d5
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098114"
---
# <a name="replace-azure-cosmos-db"></a>Zamień (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu do przeszukania.  
  
*str_expr2*  
   Jest wyrażeniem ciągu, który ma zostać znaleziony.  
  
*str_expr3*  
   Jest wyrażeniem ciągu, które służy do zastępowania wystąpień *str_expr2* w *str_expr1* .  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład przedstawia sposób użycia `REPLACE` w zapytaniu.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
