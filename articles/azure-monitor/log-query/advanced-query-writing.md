---
title: Zaawansowane zapytania w Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera samouczek dotyczący używania portalu analizy do pisania zapytań w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 3d228c62cd2d1bcb7f4515cd698186e2ebcbe929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670291"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Pisanie zaawansowanych zapytań w Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy ukończyć pracę [z Azure Monitor Log Analytics](get-started-portal.md) i [rozpocząć pracę z zapytaniami](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Używanie kodu z Let
Służy `let` do przypisywania wyników do zmiennej i odwoływania się do niej w dalszej części zapytania:

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Możesz również przypisać stałe wartości do zmiennych. Obsługuje to metodę konfigurowania parametrów dla pól, które należy zmienić przy każdym wykonaniu zapytania. Zmodyfikuj te parametry zgodnie z wymaganiami. Na przykład, aby obliczyć ilość wolnego miejsca na dysku i wolnej pamięci (w percentylach), w danym przedziale czasu:

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Ułatwia to zmianę początku czasu zakończenia przy następnym uruchomieniu zapytania.

### <a name="local-functions-and-parameters"></a>Lokalne funkcje i parametry
Użyj `let` instrukcji, aby utworzyć funkcje, które mogą być używane w tym samym zapytaniu. Na przykład Zdefiniuj funkcję, która przyjmuje pole DateTime (w formacie UTC) i konwertuje go na format standardowy US. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Drukuj
`print` zwróci tabelę z pojedynczą kolumną i pojedynczym wierszem, pokazując wynik obliczenia. Jest to często używane w przypadkach, gdy potrzebne jest proste obliczenie. Na przykład, aby znaleźć bieżący czas w pliku PST i dodać kolumnę z opcją EST:

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Columns
`datatable` umożliwia zdefiniowanie zestawu danych. Podajesz schemat i zbiór wartości, a następnie potok tabeli w innych elementach zapytania. Na przykład, aby utworzyć tabelę użycia pamięci RAM i obliczyć średnią wartość na godzinę:

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Konstrukcje DataTable są również bardzo przydatne podczas tworzenia tabeli odnośników. Na przykład, aby mapować dane tabeli, takie jak identyfikatory zdarzeń z tabeli _SecurityEvent_ , do typów zdarzeń wymienionych w innym miejscu, Utwórz tabelę odnośników z typami zdarzeń przy użyciu `datatable` i Dołącz do tego elementu DataTable z danymi _SecurityEvent_ :

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi używania [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
