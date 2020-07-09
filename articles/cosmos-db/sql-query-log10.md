---
title: Log10 — w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemowej log10 — SQL w Azure Cosmos DB, aby zwrócić logarytm dziesiętny określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302495"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 — (Azure Cosmos DB)
 Zwraca logarytm dziesiętny określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expression*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="remarks"></a>Uwagi
  
  Funkcje log10 — i energia są odwrotnie powiązane ze sobą. Na przykład 10 ^ LOG10 — (n) = n.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość log10 — określonej zmiennej (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
