---
title: GetCurrentTicks w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji GetCurrentTicks systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2ca76d75edba6688dbe93f11a51a0ad67942677a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606941"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

Zwraca liczbę taktów 100-nanosekund, które upłynęły od 00:00:00 czwartku, 1 stycznia 1970.
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Typy zwracane

Zwraca podpisaną wartość liczbową, czyli bieżącą liczbę taktów 100-nanosekund, które upłynęły od epoki systemu UNIX. Innymi słowy, GetCurrentTicks zwraca liczbę taktów 100 nanosekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970.

## <a name="remarks"></a>Uwagi

GetCurrentTicks () jest funkcją niedeterministyczną. Zwrócony wynik to UTC (uniwersalny czas koordynowany).

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="examples"></a>Przykłady

Oto przykład, który zwraca bieżący czas mierzony w taktach:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
