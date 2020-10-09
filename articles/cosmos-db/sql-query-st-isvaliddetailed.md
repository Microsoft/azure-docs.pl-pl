---
title: ST_ISVALIDDETAILED w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemu SQL ST_ISVALIDDETAILED w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71349356"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
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
