---
title: GetCurrentDateTime w języku zapytań Azure Cosmos DB
description: Dowiedz się więcej o funkcji GetCurrentDateTime systemu SQL w Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303906"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Zwraca bieżącą datę i godzinę w formacie UTC (Coordinated Universal Time) jako ciąg ISO 8601.
  
## <a name="syntax"></a>Składnia
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Typy zwracane
  
  Zwraca bieżącą wartość ciągu ISO 8601 daty i czasu UTC w formacie `YYYY-MM-DDThh:mm:ss.fffffffZ` , gdzie:
  
  |||
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

  GetCurrentDateTime () jest funkcją niedeterministyczną. 
  
  Zwrócony wynik to UTC.

  Precyzja to 7 cyfr z dokładnością do 100 nanosekund.

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
