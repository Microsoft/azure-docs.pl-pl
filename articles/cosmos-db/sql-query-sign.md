---
title: Logowanie Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat logowania do funkcji systemu SQL Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 84335acbee1e4166e5346f5c5cac6ef8dae11e99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302070"
---
# <a name="sign-azure-cosmos-db"></a>Podpisz (Azure Cosmos DB)
 Zwraca liczbę dodatnią (+ 1), zero (0) lub ujemną (-1) znak w określonym wyrażeniu liczbowym.  
  
## <a name="syntax"></a>Składnia
  
```sql
SIGN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca `SIGN` wartości liczb z-2 do 2.  
  
```sql
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
