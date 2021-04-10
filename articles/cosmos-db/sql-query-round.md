---
title: ZAOKRĄGLIj w Azure Cosmos DB języku zapytań
description: Więcej informacji na temat funkcji systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dc4d78f7af1086f9a4de9aa7392acb388df966e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590674"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
Operacja zaokrąglania jest wykonywana po zaokrągleniu punktu środkowego od zera. Jeśli dane wejściowe to wyrażenie liczbowe, które jest równe dokładnie dwóch liczb całkowitych, wynik będzie najbliższej wartości całkowitej z przedziału od zera. Ta funkcja systemowa będzie korzystać z [indeksu zakresu](index-policy.md#includeexclude-strategy).
  
|<numeric_expr>|Dokładności|
|-|-|
|-6,5000|-7|
|-0,5|-1|
|0,5|1|
|6,5000|7|
  
## <a name="examples"></a>Przykłady
  
Poniższy przykład zaokrągla następujące liczby dodatnie i ujemne do najbliższej liczby całkowitej.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
Tutaj znajduje się zestaw wyników.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
