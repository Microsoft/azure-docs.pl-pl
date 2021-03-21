---
title: GetCurrentDateTime w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji GetCurrentDateTime systemu SQL w Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12ce8beab082674cd7672713325d4b3f4322aeae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587308"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zwraca bieżącą datę i godzinę w formacie UTC (Coordinated Universal Time) jako ciąg ISO 8601.
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Typy zwracane
  
Zwraca bieżącą wartość ciągu ISO 8601 daty i czasu UTC w formacie, `YYYY-MM-DDThh:mm:ss.fffffffZ` gdzie:
  
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

GetCurrentDateTime () jest funkcją niedeterministyczną. Zwrócony wynik to UTC. Precyzja to 7 cyfr z dokładnością do 100 nanosekund.

> [!NOTE]
> Ta funkcja systemowa nie będzie używać indeksu. Jeśli musisz porównać wartości z bieżącą godziną, uzyskaj bieżącą godzinę przed wykonaniem zapytania i Użyj tej wartości ciągu stałego w `WHERE` klauzuli.

## <a name="examples"></a>Przykłady
  
Poniższy przykład pokazuje, jak uzyskać bieżącą datę czasu UTC przy użyciu wbudowanej funkcji GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Oto przykładowy zestaw wyników.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Następne kroki

- [Funkcje daty i godziny Azure Cosmos DB](sql-query-date-time-functions.md)
- [Azure Cosmos DB funkcje systemowe](sql-query-system-functions.md)
- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
