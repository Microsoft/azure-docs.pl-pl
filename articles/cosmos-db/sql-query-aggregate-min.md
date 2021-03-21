---
title: MINIMALNY w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL min (MIN) w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96555408"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ta funkcja agregująca zwraca minimum wartości w wyrażeniu.
  
## <a name="syntax"></a>Składnia
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*scalar_expr*  
   Jest wyrażeniem skalarnym. 
  
## <a name="return-types"></a>Typy zwracane
  
Zwraca wyrażenie skalarne.  
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład zwraca wartość minimalną `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy). Argumenty w `MIN` może być liczbą, ciągiem, wartością logiczną lub wartością null. Wszystkie niezdefiniowane wartości zostaną zignorowane.

Porównując różne typy danych, używane jest następujące zamówienie priorytetu (w kolejności rosnącej):

- null
- boolean
- liczba
- ciąg

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne w Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe w Azure Cosmos DB](sql-query-system-functions.md)
- [Funkcje agregujące w Azure Cosmos DB](sql-query-aggregate-functions.md)