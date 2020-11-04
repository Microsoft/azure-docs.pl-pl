---
title: DateTimeToTicks w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji DateTimeToTicks systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336337"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
