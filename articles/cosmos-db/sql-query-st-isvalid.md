---
title: ST_ISVALID w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_ISVALID w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e7c124da91a947a0ac8426ce8c92347396236c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004436"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość logiczną wskazującą, czy określone wyrażenie GEOJSON, Wielokąt, MultiPolygon lub LineString jest prawidłowe.  
  
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
