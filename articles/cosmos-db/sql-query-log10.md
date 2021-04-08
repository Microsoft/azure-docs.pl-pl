---
title: Log10 — w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej na temat funkcji systemowej log10 — SQL w Azure Cosmos DB, aby zwrócić logarytm dziesiętny określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27f7d916c1a3f84f26674fea6b04597fd9e546bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338438"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 — (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  Funkcje log10 — i energia są odwrotnie powiązane ze sobą. Na przykład 10 ^ LOG10 — (n) = n. Ta funkcja systemowa nie będzie używać indeksu.
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość log10 — określonej zmiennej (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
