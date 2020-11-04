---
title: Replikuj w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat replikacji funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7c0f1673c620ceadeb5ccca2a15cc9b7ce8d7685
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341607"
---
# <a name="replicate-azure-cosmos-db"></a>REPLIKACJA (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Powtarza wartość ciągu określoną liczbę razy.
  
## <a name="syntax"></a>Składnia
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.
  
*num_expr*  
   Jest wyrażeniem liczbowym. Jeśli *num_expr* jest ujemna lub nieograniczona, wynik jest niezdefiniowany.
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.
  
## <a name="remarks"></a>Uwagi

  Maksymalna długość wyniku to 10 000 znaków (długość ( *str_expr* ) *  *num_expr* ) <= 10 000. Ta funkcja systemowa nie będzie używać indeksu.

## <a name="examples"></a>Przykłady
  
  Poniższy przykład przedstawia sposób użycia `REPLICATE` w zapytaniu.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Tutaj znajduje się zestaw wyników.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
