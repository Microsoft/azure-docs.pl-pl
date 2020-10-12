---
title: TicksToDateTime w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji TicksToDateTime systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 89a8dba97725049b86fc6b38c09e0dd125bb48d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88608847"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)

Konwertuje określone znaczniki na wartość typu DateTime.
  
## <a name="syntax"></a>Składnia
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Argumenty

*Taktów*  

Podpisana wartość liczbowa — bieżąca liczba taktów 100 w nanosekundach, które upłynęły od epoki systemu UNIX. Innymi słowy, jest to liczba taktów 100 nanosekund, które upłynęły od 00:00:00 czwartku, 1 stycznia 1970.

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
  |Z|Oznaczenie UTC (skoordynowany czas uniwersalny)||
  
  Aby uzyskać więcej informacji na temat formatu ISO 8601, zobacz [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Uwagi

TicksToDateTime będzie zwracana `undefined` , jeśli określona wartość taktów jest nieprawidłowa.

## <a name="examples"></a>Przykłady
  
Poniższy przykład konwertuje znaczniki na datę i godzinę:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
