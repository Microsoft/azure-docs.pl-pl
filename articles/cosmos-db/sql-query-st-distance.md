---
title: ST_DISTANCE w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_DISTANCE w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79537299"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Zwraca odległość między dwoma wyrażeniami GEOJSON, Wielokąt, MultiPolygon lub LineString. Aby dowiedzieć się więcej, zobacz artykuł [dane lokalizacji geograficznej i GEOJSON](sql-query-geospatial-intro.md) .
  
## <a name="syntax"></a>Składnia
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest dowolnym prawidłowym wyrażeniem GEOJSON, wielokąta lub obiektem LineString.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe zawierające odległość. Jest to wyrażane w metrach dla domyślnego systemu referencyjnego.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak zwrócić wszystkie dokumenty rodziny, które znajdują się w zakresie 30 km od określonej lokalizacji przy użyciu `ST_DISTANCE` wbudowanej funkcji. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu geoprzestrzennego](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne Azure Cosmos DB](sql-query-spatial-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
