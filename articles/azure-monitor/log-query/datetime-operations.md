---
title: Praca z wartościami daty i godziny w zapytaniach dziennika Azure Monitor | Microsoft Docs
description: Opisuje sposób pracy z danymi daty i godziny w zapytaniach dziennika Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77655382"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Praca z wartościami daty i godziny w zapytaniach dziennika Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy ukończyć pracę [z portalem analizy](get-started-portal.md) i [zacząć korzystać z zapytań](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano sposób pracy z danymi daty i godziny w zapytaniach dziennika Azure Monitor.


## <a name="date-time-basics"></a>Podstawowe informacje o dacie i godzinie
Język zapytania Kusto ma dwa główne typy danych skojarzone z datami i godzinami: DateTime i TimeSpan. Wszystkie daty są wyrażone w formacie UTC. Chociaż obsługiwane są różne formaty DateTime, preferowany jest format ISO8601. 

Wartości TimeSpan są wyrażone jako liczba dziesiętna, po której następuje jednostka czasu:

|skrócon   | jednostka czasu    |
|:---|:---|
|d           | dzień          |
|h           | godzina         |
|m           | minuta       |
|s           | sekunda       |
|Arial          | milisekund  |
|mikrosekundowych | mikrosekundowych  |
|Investor        | nanosekund   |

Daty i godziny mogą być tworzone przez rzutowanie ciągu przy użyciu `todatetime` operatora. Na przykład aby przejrzeć pulsy maszyny wirtualnej wysyłane w określonym przedziale czasu, należy użyć `between` operatora, aby określić zakres godzin.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Inny typowy scenariusz polega na porównaniu typu DateTime z obecnym. Na przykład aby wyświetlić wszystkie pulsy w ciągu ostatnich dwóch minut, można użyć operatora razem z przedziałem czasu `now` , który reprezentuje dwie minuty:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Skrót jest również dostępny dla tej funkcji:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Najkrótszą i najbardziej czytelną metodą, chociaż używa `ago` operatora:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Załóżmy, że zamiast znajomości czasu rozpoczęcia i zakończenia wiadomo, że czas rozpoczęcia i czas trwania są znane. Zapytanie można napisać ponownie w następujący sposób:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Konwertowanie jednostek czasu
Możesz chcieć wyrazić wartość DateTime lub TimeSpan w jednostce czasu innej niż domyślna. Na przykład, Jeśli przeglądasz zdarzenia błędów z ostatnich 30 minut i potrzebujesz kolumny obliczeniowej pokazującej, jak długo miało miejsce zdarzenie:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo`Kolumna zawiera wartości, takie jak: "00:09:31.5118992", co oznacza, że są sformatowane jako hh: mm: SS. fffffff. Jeśli chcesz sformatować te wartości w `numver` minutach od czasu rozpoczęcia, Podziel tę wartość przez "1 minutę":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Agregacje i przedziały według interwałów czasu
Innym typowym scenariuszem jest konieczność uzyskiwania statystyk w określonym przedziale czasu w konkretnym czasie. W tym scenariuszu `bin` operator może być używany jako część klauzuli podsumowującej.

Użyj następującego zapytania, aby pobrać liczbę zdarzeń, które wystąpiły co 5 minut w ciągu ostatniej połowy godziny:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

To zapytanie tworzy poniższą tabelę:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018 r-08-01T09:30:00.000|54|
|2018 r-08-01T09:35:00.000|41|
|2018 r-08-01T09:40:00.000|42|
|2018 r-08-01T09:45:00.000|41|
|2018 r-08-01T09:50:00.000|41|
|2018 r-08-01T09:55:00.000|16|

Innym sposobem tworzenia zasobników wyników jest użycie funkcji, takich jak `startofday` :

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

To zapytanie daje następujące wyniki:

|sygnatura czasowa|count_|
|--|--|
|2018 r-07-28T00:00:00.000|7 136|
|2018 r-07-29T00:00:00.000|12 315|
|2018 r-07-30T00:00:00.000|16 847|
|2018 r-07-31T00:00:00.000|12 616|
|2018 r-08-01T00:00:00.000|5 416|


## <a name="time-zones"></a>Strefy czasowe
Ponieważ wszystkie wartości daty i godziny są wyrażone w formacie UTC, często warto przekonwertować te wartości na lokalną strefę czasową. Na przykład użyj tego obliczenia, aby przekonwertować czas UTC do PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funkcje pokrewne

| Kategoria | Funkcja |
|:---|:---|
| Konwertowanie typów danych | [ToDateTime](/azure/kusto/query/todatetimefunction)  [ToTimeSpan](/azure/kusto/query/totimespanfunction)  |
| Zaokrąglona wartość do rozmiaru pojemnika | [określonej](/azure/kusto/query/binfunction) |
| Pobierz określoną datę lub godzinę | [temu](/azure/kusto/query/agofunction) [teraz](/azure/kusto/query/nowfunction)   |
| Pobierz część wartości | [datetime_part](/azure/kusto/query/datetime-partfunction) [GetMonth](/azure/kusto/query/getmonthfunction) [MonthOfYear](/azure/kusto/query/monthofyearfunction) [GetYear](/azure/kusto/query/getyearfunction) [dayOfMonth](/azure/kusto/query/dayofmonthfunction) [DayOfWeek](/azure/kusto/query/dayofweekfunction) [dzieńroku](/azure/kusto/query/dayofyearfunction) [WeekOfYear](/azure/kusto/query/weekofyearfunction) |
| Pobierz wartość daty względnej  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [ENDOFMONTH](/azure/kusto/query/endofmonthfunction) [ENDOFYEAR](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [STARTOFMONTH](/azure/kusto/query/startofmonthfunction) [STARTOFYEAR](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi używania [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
