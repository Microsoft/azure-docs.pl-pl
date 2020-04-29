---
title: RAND w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji RAND w systemie SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302223"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Zwraca losowo wygenerowaną wartość liczbową z [0, 1).
 
## <a name="syntax"></a>Składnia
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Typy zwracane

  Zwraca wyrażenie liczbowe.

## <a name="remarks"></a>Uwagi

  `RAND`jest funkcją niedeterministyczną. Powtarzające się `RAND` wywołania elementu nie zwracają tych samych wyników.

## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca losowo wygenerowaną wartość liczbową.
  
```sql
SELECT RAND() AS rand 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Uwagi

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
