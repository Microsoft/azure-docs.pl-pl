---
title: ZAOKRĄGLIj w Azure Cosmos DB języku zapytań
description: Więcej informacji na temat funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302121"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
 Zwraca wartość liczbową zaokrągloną do najbliższej wartości całkowitej.  
  
## <a name="syntax"></a>Składnia
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="remarks"></a>Uwagi
  
  Operacja zaokrąglania jest wykonywana po zaokrągleniu punktu środkowego od zera. Jeśli dane wejściowe to wyrażenie liczbowe, które jest równe dokładnie dwóch liczb całkowitych, wynik będzie najbliższej wartości całkowitej z przedziału od zera.  
  
  |<numeric_expr>|Dokładności|
  |-|-|
  |-6,5000|-7|
  |-0,5|-1|
  |0,5|1|
  |6,5000|7||
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład zaokrągla następujące liczby dodatnie i ujemne do najbliższej liczby całkowitej.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Tutaj znajduje się zestaw wyników.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
