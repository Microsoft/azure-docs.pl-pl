---
title: Logowanie w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji rejestrowania w systemie SQL w Azure Cosmos DB, aby zwrócić logarytm naturalny określonego wyrażenia liczbowego
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 74d07076fb322214348d52fe65dbc98bdff3cd44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798265"
---
# <a name="log-azure-cosmos-db"></a>Dziennik (Azure Cosmos DB)
 Zwraca logarytm naturalny określonego wyrażenia liczbowego.  
  
## <a name="syntax"></a>Składnia
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenty
  
*numeric_expr*  
   Jest wyrażeniem liczbowym.  
  
*base*  
   Opcjonalny argument liczbowy, który ustawia podstawę logarytmu.  
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca wyrażenie liczbowe.  
  
## <a name="remarks"></a>Uwagi
  
  Domyślnie funkcja LOG () Zwraca logarytm naturalny. Można zmienić podstawę logarytmu na inną wartość za pomocą opcjonalnego parametru podstawowego.  
  
  Logarytm naturalny to logarytm o podstawie **e**, gdzie **e** jest stałą o wartości poniżej 2,718281828.  
  
  Logarytm naturalny liczby wykładniczej jest samą liczbą: LOG (EXP (n)) = n. I wykładnicza logarytmu naturalnego liczby jest samą liczbą: EXP (LOG (n)) = n.

  Ta funkcja systemowa nie będzie używać indeksu.
  
## <a name="examples"></a>Przykłady
  
  Poniższy przykład deklaruje zmienną i zwraca wartość logarytmu określonej zmiennej (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 Poniższy przykład oblicza wartość `LOG` dla wykładnika liczby.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
