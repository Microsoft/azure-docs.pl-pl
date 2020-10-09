---
title: TAN w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej na temat funkcji systemowej TAN w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78301985"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Zwraca tangens określonego kąta w radianach w określonym wyrażeniu.  
  
## <a name="syntax"></a>Składnia
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład oblicza tangens liczby PI ()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
