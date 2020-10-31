---
title: DateTimePart w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji DateTimePart systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: dd88f8d6a0f5271da97d6f2f95eecb60bf94ed78
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095807"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zwraca wartość określonego DateTimePart między określoną datą i godziną.
  
## <a name="syntax"></a>Składnia
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argumenty
  
*DateTimePart*  
   Część daty, dla której DateTimePart zwróci wartość. Ta tabela zawiera listę wszystkich prawidłowych argumentów DateTimePart:

| DateTimePart | skróty        |
| ------------ | -------------------- |
| Rok         | "Year", "RRRR", "YY" |
| Month (Miesiąc)        | "Month", "mm", "m"   |
| Dzień          | "Day", "DD", "d"     |
| Godzina         | "Hour", "HH"         |
| Minuta       | "minute", "mi", "n"  |
| Second       | "Second", "SS", "s"  |
| Milisekund  | "milisekundy", "MS"  |
| Mikrosekundowych  | "mikrosekundowych", "MCS" |
| Nanosekund   | "nanosekund", "NS"   |

*Data/godzina*  
   Wartość ciągu ISO 8601 daty i czasu UTC w formacie `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Typy zwracane

Zwraca dodatnią liczbę całkowitą.

## <a name="remarks"></a>Uwagi

DateTimePart zwróci `undefined` następujące przyczyny:

- Określona wartość DateTimePart jest nieprawidłowa.
- Wartość DateTime nie jest prawidłową datą i godziną ISO 8601

Ta funkcja systemowa nie będzie używać indeksu.

## <a name="examples"></a>Przykłady

Oto przykład, który zwraca wartość całkowitą miesiąca:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Oto przykład, który zwraca liczbę mikrosekund:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
