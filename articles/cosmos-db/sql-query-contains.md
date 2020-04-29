---
title: ZAWIERA w Azure Cosmos DB języku zapytań
description: Dowiedz się, jak Funkcja systemowa SQL w Azure Cosmos DB zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu zawiera sekundę
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302597"
---
# <a name="contains-azure-cosmos-db"></a>ZAWIERA (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie.  
  
## <a name="syntax"></a>Składnia
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr1*  
   Jest wyrażeniem ciągu do przeszukania.  
  
*str_expr2*  
   Jest wyrażeniem ciągu do znalezienia.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza, czy "ABC" zawiera "AB" i czy "ABC" zawiera "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
