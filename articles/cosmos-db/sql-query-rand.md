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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
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

  `RAND`jest funkcją niedeterministyczną. Powtarzające się wywołania elementu `RAND` nie zwracają tych samych wyników.

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
