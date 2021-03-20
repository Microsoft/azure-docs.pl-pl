---
title: Suma w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji system SQL sum (SUM) w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553426"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ta funkcja agregująca zwraca sumę wartości w wyrażeniu.
  
## <a name="syntax"></a>Składnia
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład zwraca sumę `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy). Jeśli którykolwiek z argumentów w `SUM` jest ciąg, wartość logiczna lub wartość null, zostanie zwrócona cała agregowana Funkcja systemowa `undefined` . Jeśli którykolwiek z argumentów ma `undefined` wartość, nie będzie to miało wpływu na `SUM` obliczenia.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne w Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe w Azure Cosmos DB](sql-query-system-functions.md)
- [Funkcje agregujące w Azure Cosmos DB](sql-query-aggregate-functions.md)