---
title: StringToBoolean w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToBoolean systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78296545"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
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
