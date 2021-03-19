---
title: DateTimeToTimestamp w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji DateTimeToTimestamp systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 3b1cbd88b4cd6576b2c31fbeb2f3db86309c5ebf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597627"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konwertuje określoną datę i godzinę do sygnatury czasowej.
  
## <a name="syntax"></a>Składnia
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Argumenty

*Data/godzina*  
   Data i godzina UTC ISO 8601 wartość ciągu w formacie, `YYYY-MM-DDThh:mm:ss.fffffffZ` gdzie:
  
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

## <a name="return-types"></a>Typy zwracane

Zwraca podpisaną wartość liczbową, bieżącą liczbę milisekund, które upłynęły od epoki systemu UNIX, czyli liczbę milisekund, które upłynęły od 00:00:00 czwartek, 1 stycznia 1970.

## <a name="remarks"></a>Uwagi

DateTimeToTimestamp będzie zwracana `undefined` , jeśli określona wartość daty i godziny jest nieprawidłowa.

## <a name="examples"></a>Przykłady
  
Poniższy przykład konwertuje datę i godzinę do sygnatury czasowej:

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Oto kolejny przykład:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
