---
title: RAND w Azure Cosmos DB języku zapytań
description: Dowiedz się więcej o funkcji RAND w systemie SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e287f119a67c8bb00a309833e2ad6fe0d88975a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076099"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Zwraca losowo wygenerowaną wartość liczbową z [0, 1).
 
## <a name="syntax"></a>Składnia
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Typy zwracane

  Zwraca wyrażenie liczbowe.

## <a name="remarks"></a>Uwagi

  `RAND` jest funkcją niedeterministyczną. Powtarzające się wywołania elementu `RAND` nie zwracają tych samych wyników. Ta funkcja systemowa nie będzie używać indeksu.


## <a name="examples"></a>Przykłady
  
  Poniższy przykład zwraca losowo wygenerowaną wartość liczbową.
  
```sql
SELECT RAND() AS rand 
```  
  
 Tutaj znajduje się zestaw wyników.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Następne kroki

- [Funkcje matematyczne Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
