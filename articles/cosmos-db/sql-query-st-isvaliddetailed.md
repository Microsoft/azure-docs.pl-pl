---
title: ST_ISVALIDDETAILED w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_ISVALIDDETAILED w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: df2d4265393085a58699b3576ce2461ed63de317
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080043"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wartość JSON zawierającą wartość logiczną, jeśli określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest prawidłowe, a jeśli jest nieprawidłowe, dodatkowo zwraca przyczynę jako wartość ciągu.  
  
## <a name="syntax"></a>Składnia
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*spatial_expr*  
   Jest punktem GeoJSON lub wyrażeniem wielokąta.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wartość JSON zawierającą wartość logiczną, jeśli określony punkt GeoJSON lub wyrażenie wielokąta jest prawidłowe i jeśli jest nieprawidłowy, dodatkowo jako wartość ciągu.  
  
## <a name="examples"></a>Przykłady
  
  W poniższym przykładzie pokazano, jak sprawdzić ważność (ze szczegółami) przy użyciu polecenia `ST_ISVALIDDETAILED` .  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje przestrzenne Azure Cosmos DB](sql-query-spatial-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
