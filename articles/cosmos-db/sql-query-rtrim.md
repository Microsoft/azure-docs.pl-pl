---
title: RTRIM w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji RTRIM systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0765269fb085c8d4fd8485790772a91c9f2e6373
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341539"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie ciągu, po usunięciu końcowej wartości pustej.  
  
## <a name="syntax"></a>Składnia
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest dowolnym prawidłowym wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak używać `RTRIM` wewnątrz zapytania.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
