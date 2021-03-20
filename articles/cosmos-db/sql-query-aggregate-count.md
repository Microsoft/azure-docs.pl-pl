---
title: Liczba w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji system SQL Count (COUNT) w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553402"
---
# <a name="count-azure-cosmos-db"></a>Liczba (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ta funkcja systemowa zwraca liczbę wartości w wyrażeniu.
  
## <a name="syntax"></a>Składnia
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*scalar_expr*  
   Jest dowolnym wyrażeniem skalarnym
  
## <a name="return-types"></a>Typy zwracane
  
Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład zwraca łączną liczbę elementów w kontenerze:
  
```sql
SELECT COUNT(1)
FROM c
``` 
LICZBA może przyjmować dowolne wyrażenie skalarne jako dane wejściowe. Poniższe zapytanie spowoduje wygenerowanie równoważnych wyników:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy) dla wszystkich właściwości w filtrze zapytania.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne w Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe w Azure Cosmos DB](sql-query-system-functions.md)
- [Funkcje agregujące w Azure Cosmos DB](sql-query-aggregate-functions.md)