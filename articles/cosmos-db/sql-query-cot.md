---
title: COT w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o tym, jak Funkcja systemowa "COT" w języku SQL w Azure Cosmos DB zwraca miarę trygonometryczną o określonym kącie, w radianach, w określonym wyrażeniu liczbowym
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78299491"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Zwraca trygonometryczny cotangens określonego kąta w radianach w określonym wyrażeniu liczbowym.  
  
## <a name="syntax"></a>Składnia
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza `COT` określony kąt.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
