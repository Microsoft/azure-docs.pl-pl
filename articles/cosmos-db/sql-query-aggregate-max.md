---
title: MAKS w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat funkcji systemu SQL Max (MAX) w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553411"
---
# <a name="max-azure-cosmos-db"></a>MAKS. (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ta funkcja agregująca zwraca maksymalną liczbę wartości w wyrażeniu.
  
## <a name="syntax"></a>Składnia
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argumenty

*scalar_expr*  
   Jest wyrażeniem skalarnym. 
  
## <a name="return-types"></a>Typy zwracane
  
Zwraca wyrażenie skalarne.  
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład zwraca wartość maksymalną `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy). Argumenty w `MAX` może być liczbą, ciągiem, wartością logiczną lub wartością null. Wszystkie niezdefiniowane wartości zostaną zignorowane.

Porównując różne typy danych, jest używana następująca kolejność priorytetów (w kolejności malejącej):

- ciąg
- liczba
- boolean
- wartość null

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne w Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funkcje systemowe w Azure Cosmos DB](sql-query-system-functions.md)
- [Funkcje agregujące w Azure Cosmos DB](sql-query-aggregate-functions.md)