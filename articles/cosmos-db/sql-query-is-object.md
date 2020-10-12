---
title: IS_OBJECT w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL IS_OBJECT w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78303804"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest obiektem JSON.  
  
## <a name="syntax"></a>Składnia
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*wyrażenie*  
   To dowolne wyrażenie.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład sprawdza obiekty typu Boolean, Number, String, null, Object, Array i undefined przy użyciu `IS_OBJECT` funkcji.  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje sprawdzania typu Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
