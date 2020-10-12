---
title: GetCurrentTimestamp w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji GetCurrentTimestamp systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c1e0a8d69edab0c01005268ee49c23625236f03a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606924"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)

 Zwraca liczbę milisekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970.
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Typy zwracane
  
Zwraca podpisaną wartość liczbową, bieżącą liczbę milisekund, które upłynęły od epoki systemu UNIX, czyli liczbę milisekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970.

## <a name="remarks"></a>Uwagi

GetCurrentTimestamp () jest funkcją niedeterministyczną. Zwrócony wynik to UTC (uniwersalny czas koordynowany).

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="examples"></a>Przykłady
  
  Poniższy przykład pokazuje, jak uzyskać bieżącą sygnaturę czasową przy użyciu wbudowanej funkcji GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Oto przykładowy zestaw wyników.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
