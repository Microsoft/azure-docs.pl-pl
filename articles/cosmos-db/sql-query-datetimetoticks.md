---
title: DateTimeToTicks w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji DateTimeToTicks systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2e2c9e8f2bf0d4760bf030fb19a90737cdb54525
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605147"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)

Konwertuje określony element DateTime na takty. Pojedynczy takt reprezentuje 100 nanosekund lub 1 10-milionowego sekundy. 

## <a name="syntax"></a>Składnia
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumenty
  
*Data/godzina*  
   Wartość ciągu ISO 8601 daty i czasu UTC w formacie `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Typy zwracane

Zwraca podpisaną wartość liczbową, czyli bieżącą liczbę taktów 100-nanosekund, które upłynęły od epoki systemu UNIX. Innymi słowy, DateTimeToTicks zwraca liczbę taktów 100-nanosekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970.

## <a name="remarks"></a>Uwagi

DateTimeDateTimeToTicks zwróci wartość `undefined` , jeśli DateTime nie jest prawidłową datą i godziną ISO 8601

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="examples"></a>Przykłady

Oto przykład, który zwraca liczbę taktów:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Oto przykład, który zwraca liczbę taktów bez określania liczby sekund ułamkowych:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
