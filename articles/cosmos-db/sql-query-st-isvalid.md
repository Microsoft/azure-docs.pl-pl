---
title: ST_ISVALID w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_ISVALID w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9b20e57672e86c2b5a6a2a25151d779ea7bc92f3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335164"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest wyrażeniem GEOJSON, wielokątem lub LineString.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak sprawdzić, czy punkt jest prawidłowy przy użyciu ST_VALID.  
  
  Na przykład ten punkt ma wartość szerokości geograficznej, która nie znajduje się w prawidłowym zakresie wartości [-90, 90], dlatego zapytanie zwraca wartość false.  
  
  W przypadku wielokątów Specyfikacja GEOJSON wymaga, aby podana para współrzędnych była taka sama jak pierwsza, aby utworzyć kształt zamknięty. Punkty w obrębie wielokąta muszą być określone w kolejności licznika w prawo. Wielokąt określony w kolejności w prawo oznacza odwrotność regionu w nim.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne Azure Cosmos DB](sql-query-spatial-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
