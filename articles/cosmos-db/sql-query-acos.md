---
title: ACOS w języku zapytań Azure Cosmos DB
description: Dowiedz się, jak Funkcja systemowa SQL ACOS (arccosice) w Azure Cosmos DB zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ed5adaad6c0ee160fd55341e1d7c48fcf3e49a28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93332818"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus cosinus.  
  
## <a name="syntax"></a>Składnia
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca wartość `ACOS` -1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
