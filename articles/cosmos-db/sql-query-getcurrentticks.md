---
title: GetCurrentTicks w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji GetCurrentTicks systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524307"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zwraca liczbę taktów 100-nanosekund, które upłynęły od 00:00:00 czwartku, 1 stycznia 1970.
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Typy zwracane

Zwraca podpisaną wartość liczbową, czyli bieżącą liczbę taktów 100-nanosekund, które upłynęły od epoki systemu UNIX. Innymi słowy, GetCurrentTicks zwraca liczbę taktów 100 nanosekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970.

## <a name="remarks"></a>Uwagi

GetCurrentTicks () jest funkcją niedeterministyczną. Zwrócony wynik to UTC (uniwersalny czas koordynowany).

> [!NOTE]
> Ta funkcja systemowa nie będzie używać indeksu. Jeśli musisz porównać wartości z bieżącą godziną, uzyskaj bieżącą godzinę przed wykonaniem zapytania i Użyj tej wartości ciągu stałego w `WHERE` klauzuli.

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
