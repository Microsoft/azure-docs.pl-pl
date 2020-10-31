---
title: StringToNull w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji StringToNull systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1dde94be96b7d8990f51f7f8b73e54c4edf05130
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098097"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca wyrażenie przetłumaczone na wartość null. Jeśli wyrażenia nie można przetłumaczyć, funkcja zwraca wartość undefined.  
  
## <a name="syntax"></a>Składnia
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*str_expr*  
   Jest wyrażeniem ciągu, które ma być analizowane jako wyrażenie o wartości null.
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie o wartości null lub niezdefiniowane.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak `StringToNull` działa w różnych typach. 

Poniżej przedstawiono przykłady z prawidłowymi danymi wejściowymi.

 Odstęp jest dozwolony tylko przed lub po "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Poniżej przedstawiono przykłady z nieprawidłowymi danymi wejściowymi.

Wartość null uwzględnia wielkość liter i musi być zapisana ze wszystkimi małymi literami, np. "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{}]
```  

Wyrażenie przesłane zostanie przeanalizowane jako wyrażenie o wartości null; te dane wejściowe nie są oceniane do typu null i w rezultacie zwracają niezdefiniowane.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
