---
title: STOPnie w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o STOPNIach funkcji systemowej SQL w Azure Cosmos DB, aby zwrócić odpowiedni kąt w stopniach dla kąta określonego w radianach
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5c7aa5496533a42ad726f9ed8efd8ed7429375fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338904"
---
# <a name="degrees-azure-cosmos-db"></a>STOPnie (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca kąt w stopniach odpowiadający kątowi określonemu w radianach.  
  
## <a name="syntax"></a>Składnia
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca liczbę stopni w kącie kąta PI/2 radianów.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
