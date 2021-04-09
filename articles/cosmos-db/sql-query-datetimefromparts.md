---
title: DateTimeFromParts w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji DateTimeFromParts systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b0e7996b71e68db371201da1f0f5c93486ae4e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592100"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zwraca wartość typu DateTime, która została zbudowana z wartości wejściowych.
  
## <a name="syntax"></a>Składnia
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argumenty
  
*numberYear* Wartość całkowita roku w formacie `YYYY`

*numberMonth*  
   Wartość całkowita miesiąca w formacie `MM`

*numberDay*  
   Wartość całkowita dla dnia w formacie `DD`

*numberHour* (opcjonalnie) wartość całkowita dla godziny w formacie `hh`

*numberMinute* (opcjonalna) wartość liczby całkowitej dla minuty w formacie `mm`

*numberSecond* (opcjonalnie) wartość całkowita dla sekundy w formacie `ss`

*numberOfFractionsOfSecond* (opcjonalnie) wartość całkowita ułamka części sekundy w formacie `.fffffff`

## <a name="return-types"></a>Typy zwracane

Zwraca wartość ciągu ISO 8601 daty i czasu UTC w formacie, `YYYY-MM-DDThh:mm:ss.fffffffZ` gdzie:
  
|Format|Opis|
|-|-|
|YYYY|rok czterocyfrowy|
|MM|dwucyfrowy miesiąc (01 = styczeń itd.)|
|DD|dwucyfrowy dzień miesiąca (od 01 do 31)|
|T|Oznaczanie na początku elementów czasu|
|hh|godzina dwucyfrowy (od 00 do 23)|
|mm|minuty dwucyfrowe (od 00 do 59)|
|ss|dwa cyfry sekund (od 00 do 59)|
|. fffffff|siedem cyfr sekund|
|Z|Oznaczenie UTC (skoordynowany czas uniwersalny)|
  
 Aby uzyskać więcej informacji na temat formatu ISO 8601, zobacz [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Uwagi

Jeśli określone liczby całkowite spowodują utworzenie nieprawidłowej daty i godziny, DateTimeFromParts zwróci `undefined` .

Jeśli opcjonalny argument nie jest określony, jego wartość będzie równa 0.

## <a name="examples"></a>Przykłady

Oto przykład, który zawiera tylko wymagane argumenty do konstruowania wartości DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Oto inny przykład, który używa również opcjonalnych argumentów do konstruowania wartości typu DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Oto inny przykład, który używa również wszystkich opcjonalnych argumentów do konstruowania wartości typu DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
