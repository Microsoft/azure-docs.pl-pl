---
title: DateTimeToTimestamp w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji DateTimeToTimestamp systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6de48583cbc3a7e41d6da5e7ed29abf07bba3112
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100307"
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
  |Z|Oznaczenie UTC (skoordynowany czas uniwersalny)||
  
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
