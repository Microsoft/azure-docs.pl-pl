---
title: StringToBoolean w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToBoolean systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2ad7ca9e2e50395effcc50e776eee3f1740fbb7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337952"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie tłumaczone na wartość logiczną. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie logiczne.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie logiczne lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToBoolean` działa w różnych typach. 
 
 Poniżej przedstawiono przykłady z prawidłowymi danymi wejściowymi.

Biały znak jest dozwolony tylko przed lub po "prawda"/"fałsz".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Poniżej przedstawiono przykłady z nieprawidłowymi danymi wejściowymi.

 Wartości logiczne uwzględniają wielkość liter i muszą być zapisywane ze wszystkimi małymi literami, np. "true" i "false".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Tutaj znajduje się zestaw wyników.  
  
```json
[{}]
``` 

Wyrażenie przesłane zostanie przeanalizowane jako wyrażenie logiczne; te dane wejściowe nie są obliczane do typu Boolean i w ten sposób zwracają niezdefiniowane.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Tutaj znajduje się zestaw wyników.  
  
```json
[{}]
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
