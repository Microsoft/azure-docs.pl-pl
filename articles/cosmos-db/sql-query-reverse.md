---
title: Odwróć w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat funkcji systemowej SQL Reverse Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b25b720b0919038ca024f7c8b11712d78fd44d52
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082848"
---
# <a name="reverse-azure-cosmos-db"></a>Odwróć (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość ciągu w odwrotnej kolejności.  
  
## <a name="syntax"></a>Składnia
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie ciągu.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład przedstawia sposób użycia `REVERSE` w zapytaniu.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
