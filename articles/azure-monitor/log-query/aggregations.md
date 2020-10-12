---
title: Agregacje w zapytaniach dziennika Azure Monitor | Microsoft Docs
description: Opisuje funkcje agregacji w zapytaniach dziennika Azure Monitor, które oferują przydatne metody analizowania danych.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670308"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Agregacje w zapytaniach dziennika Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy ukończyć pracę [z portalem analizy](get-started-portal.md) i [zacząć korzystać z zapytań](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano funkcje agregacji w zapytaniach dziennika Azure Monitor, które oferują przydatne metody analizowania danych. Wszystkie te funkcje współpracują z `summarize` operatorem, który tworzy tabelę z zagregowanymi wynikami tabeli wejściowej.

## <a name="counts"></a>Licznik

### <a name="count"></a>count
Policz liczbę wierszy w zestawie wyników po zastosowaniu filtrów. Poniższy przykład zwraca łączną liczbę wierszy w tabeli _wydajności_ w ciągu ostatnich 30 minut. Wynik jest zwracany w kolumnie o nazwie *count_* , chyba że zostanie przypisana nazwa określona:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Wizualizacja timechart może być przydatna do wyświetlania trendu w czasie:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

Dane wyjściowe z tego przykładu przedstawiają Trend liczby rekordów wydajności w odstępach 5 minut:

![Trend liczby](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, dcountif
Użyj `dcount` i, `dcountif` Aby zliczyć różne wartości w określonej kolumnie. Następujące zapytanie szacuje, ile różnych komputerów wysłało pulsy w ciągu ostatniej godziny:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Aby zliczać tylko komputery z systemem Linux, które wysłały pulsy, należy użyć `dcountif` :

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Ocenianie podgrup
Aby wykonać liczbę lub inne agregacje dla podgrup danych, użyj `by` słowa kluczowego. Na przykład, aby policzyć liczbę różnych komputerów z systemem Linux, które wysyłają pulsy w poszczególnych krajach/regionach:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Stany Zjednoczone    | 19                  |
|Kanada           | 3                   |
|Irlandia          | 0                   |
|Zjednoczone Królestwo   | 0                   |
|Holandia      | 2                   |


Aby analizować jeszcze mniejsze podgrupy danych, Dodaj dodatkowe nazwy kolumn do `by` sekcji. Na przykład możesz chcieć obliczyć różne komputery z każdego kraju/regionu na OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentyle i Wariancja
Podczas oceniania wartości liczbowych typowym celem jest uśrednianie ich przy użyciu `summarize avg(expression)` . Na średnie mają wpływ wartości skrajne, które opisują tylko kilka przypadków. Aby rozwiązać ten problem, można użyć mniej wrażliwych funkcji, takich jak `median` lub `variance` .

### <a name="percentile"></a>Percentyl
Aby znaleźć wartość mediana, użyj `percentile` funkcji z wartością, aby określić percentyl:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Można również określić różne percentyle, aby uzyskać zagregowany wynik dla każdej z nich:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Może to wskazywać, że niektóre procesory CPU mają podobne wartości Mediane, ale podczas gdy niektóre z nich są stałe, inne komputery zgłosiły znacznie niższe i wyższe wartości procesora, co oznacza, że są one skokami.

### <a name="variance"></a>Variance (Wariancja)
Aby bezpośrednio oszacować wariancję wartości, należy użyć odchylenia standardowego i metod wariancji:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Dobrym sposobem na przeanalizowanie stabilności użycia procesora CPU jest połączenie STDEV z obliczaniem średnim:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Zapoznaj się z innymi lekcjami dotyczącymi używania [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)
