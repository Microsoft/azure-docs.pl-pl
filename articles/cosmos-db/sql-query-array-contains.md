---
title: ARRAY_CONTAINS w języku zapytań Azure Cosmos DB
description: Dowiedz się, w jaki sposób tablica zawiera funkcję systemową SQL w Azure Cosmos DB zwraca wartość Boolean wskazującą, czy tablica zawiera określoną wartość
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff88d66ee6d1ceee4f1cf4e7b6501ab323dbc79e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332665"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zwraca wartość logiczną wskazującą, czy tablica zawiera określoną wartość. Można wyszukać częściowe lub pełne dopasowanie obiektu za pomocą wyrażenia logicznego w poleceniu. 

## <a name="syntax"></a>Składnia
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumenty
  
*arr_expr*  
   Jest wyrażeniem tablicy, które ma być przeszukiwane.  
  
*wyrażenie*  
   Jest wyrażeniem, które ma zostać znalezione.  

*bool_expr*  
   Jest wyrażeniem logicznym. Jeśli wartość jest równa "true", a w przypadku określonej wartości wyszukiwania jest obiektem, polecenie sprawdza obecność częściowego dopasowania (obiekt wyszukiwania jest podzbiorem jednego z obiektów). Jeśli wartość jest równa "false", polecenie sprawdza pełne dopasowanie wszystkich obiektów w tablicy. Wartość domyślna, jeśli nie zostanie określona, jest równa false. 
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość logiczną.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak sprawdzić członkostwo w tablicy przy użyciu `ARRAY_CONTAINS` .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"b1": true, "b2": false}]  
```  

W poniższym przykładzie pokazano, jak sprawdzić częściowe dopasowanie JSON w tablicy przy użyciu ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje tablicowe Azure Cosmos DB](sql-query-array-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
