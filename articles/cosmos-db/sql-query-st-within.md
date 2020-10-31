---
title: ST_WITHIN w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_WITHIN w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: da475ced067f19cddd4a20284ba556c8a0b52677
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079975"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie logiczne wskazujące, czy obiekt GEOJSON (punkt, wielokąt lub LineString) określony w pierwszym argumencie znajduje się w obrębie GEOJSON (punkt, wielokąt lub LineString) w drugim argumencie.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest wyrażeniem typu GEOJSON, wielokąta lub LineString.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość logiczną.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak znaleźć wszystkie dokumenty rodziny w obrębie wielokąta przy użyciu `ST_WITHIN` .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu geoprzestrzennego](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne Azure Cosmos DB](sql-query-spatial-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
