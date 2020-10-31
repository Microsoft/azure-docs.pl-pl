---
title: StringToArray w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToArray systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2f9086aa89c2a3ec3ebfcb32025bace4f1db7d9e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079718"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie tłumaczone na tablicę. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie tablicy JSON. 
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie tablicy lub niezdefiniowane. 
  
## <a name="remarks"></a>Uwagi
  Zagnieżdżone wartości ciągu muszą być zapisywane z podwójnymi cudzysłowami, aby były prawidłowymi wartościami JSON. Aby uzyskać szczegółowe informacje o formacie JSON, zobacz [JSON.org](https://json.org/). Ta funkcja systemowa nie będzie używać indeksu.
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToArray` działa w różnych typach. 
  
 Poniżej przedstawiono przykłady z prawidłowymi danymi wejściowymi.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Tutaj znajduje się zestaw wyników.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Poniżej przedstawiono przykład nieprawidłowych danych wejściowych. 
   
 Pojedyncze cudzysłowy w tablicy nie są prawidłowymi formatami JSON.
Mimo że są one prawidłowe w ramach zapytania, nie będą analizowane do prawidłowych tablic. Ciągi w ciągu tablicy muszą mieć wartość ucieczki "[ \\ " \\ "]" lub otaczające cudzysłowy muszą być pojedynczym znakiem "[" "]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

Tutaj znajduje się zestaw wyników.

```json
[{}]
```

Poniżej przedstawiono przykłady nieprawidłowych danych wejściowych.
   
 Przesłane wyrażenie zostanie przeanalizowane jako tablica JSON; następujące elementy nie są oceniane do typu Array i w ten sposób zwracają wartość undefined.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Tutaj znajduje się zestaw wyników.

```json
[{}]
```

## <a name="next-steps"></a>Następne kroki

- [Azure Cosmos DB funkcje ciągów](sql-query-string-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
