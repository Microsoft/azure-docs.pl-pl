---
title: COS w Azure Cosmos DB języku zapytań
description: Dowiedz się, w jaki sposób Funkcja systemowa SQL cosinus (COS) w Azure Cosmos DB zwraca kąt w radianach, który jest określony dla danego kąta, przy użyciu radiany, w określonym wyrażeniu
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78304025"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Zwraca trygonometryczny cosinus określonego kąta w radianach w określonym wyrażeniu.  
  
## <a name="syntax"></a>Składnia
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza `COS` określony kąt.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
