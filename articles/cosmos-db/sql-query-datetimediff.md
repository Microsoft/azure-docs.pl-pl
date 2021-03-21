---
title: DateTimeDiff w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji DateTimeDiff systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595647"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Zwraca liczbę (jako wartość całkowitą ze znakiem) dla określonych granic DateTimePart przekraczających określone *StartDate* i *EndDate*.
  
## <a name="syntax"></a>Składnia
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>Argumenty
  
*DateTimePart*  
   Część daty, do której DateTimeAdd dodaje liczbę całkowitą. Ta tabela zawiera listę wszystkich prawidłowych argumentów DateTimePart:

| DateTimePart | skróty        |
| ------------ | -------------------- |
| Year (Rok)         | "Year", "RRRR", "YY" |
| Month (Miesiąc)        | "Month", "mm", "m"   |
| Dzień          | "Day", "DD", "d"     |
| Godzina         | "Hour", "HH"         |
| Minuta       | "minute", "mi", "n"  |
| Second       | "Second", "SS", "s"  |
| Milisekund  | "milisekundy", "MS"  |
| Mikrosekundowych  | "mikrosekundowych", "MCS" |
| Nanosekund   | "nanosekund", "NS"   |

*StartDate*  
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

*EndDate*  
   Wartość ciągu ISO 8601 daty i czasu UTC w formacie `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Typy zwracane

Zwraca liczbę całkowitą ze znakiem.

## <a name="remarks"></a>Uwagi

DateTimeDiff zwróci `undefined` następujące przyczyny:

- Określona wartość DateTimePart jest nieprawidłowa.
- StartDate lub EndDate nie jest prawidłową datą i godziną ISO 8601

DateTimeDiff zawsze zwróci liczbę całkowitą ze znakiem i jest pomiar liczby przekroczeń DateTimePartch, a nie pomiaru przedziału czasu.

## <a name="examples"></a>Przykłady
  
Poniższy przykład oblicza liczbę granic dni między `2020-01-01T01:02:03.1234527Z` i `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

Poniższy przykład oblicza liczbę granic roku między `2028-01-01T01:02:03.1234527Z` i `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

Poniższy przykład oblicza liczbę granic godzin między `2020-01-01T01:00:00.1234527Z` i `2020-01-01T01:59:59.1234567Z` . Mimo że te wartości daty i godziny przekraczają 0,99 godzin, `DateTimeDiff` zwraca wartość 0, ponieważ nie przekroczy żadnych granic godzin.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
