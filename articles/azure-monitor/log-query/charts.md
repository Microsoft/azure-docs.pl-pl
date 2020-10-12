---
title: Tworzenie wykresów i diagramów z zapytań dzienników Azure Monitor | Microsoft Docs
description: Opisuje różne wizualizacje w Azure Monitor do wyświetlania danych dzienników na różne sposoby.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77670325"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Tworzenie wykresów i diagramów z zapytań dzienników Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy wykonać [Zaawansowane agregacje w zapytaniach dziennika Azure monitor](advanced-aggregations.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano różne wizualizacje w Azure Monitor do wyświetlania danych dzienników na różne sposoby.

## <a name="charting-the-results"></a>Tworzenie wykresów wyników
Zacznij od przejrzenia liczby komputerów dla każdego systemu operacyjnego w ciągu ostatniej godziny:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Domyślnie wyniki są wyświetlane w postaci tabeli:

![tabela](media/charts/table-display.png)

Aby uzyskać lepszy widok, wybierz pozycję **Wykres**i wybierz opcję **kołowy** , aby wizualizować wyniki:

![Wykres kołowy](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Pokaż średnie, pięćdziesiąt i używany 95. percentyly czasu procesora w pojemnikach o wartości 1 godziny. Zapytanie generuje wiele serii, a następnie można wybrać serie do wyświetlenia na wykresie czasu:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Wybierz opcję wyświetlania wykresu **liniowego** :

![Wykres liniowy](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Wiersz odwołania

Wiersz odwołania może ułatwić identyfikację, czy Metryka przekroczyła określony próg. Aby dodać linię do wykresu, rozwiń zestaw danych za pomocą kolumny stałej:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Wiersz odwołania](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Wiele wymiarów
Wiele wyrażeń w `by` klauzuli `summarize` Create wiele wierszy w wynikach, po jednym dla każdej kombinacji wartości.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Po wyświetleniu wyników w postaci wykresu zostanie użyta pierwsza kolumna z `by` klauzuli. Poniższy przykład przedstawia skumulowany wykres kolumnowy przy użyciu _EventId._ Wymiary muszą być `string` typu, więc w tym przykładzie _EventID_ jest rzutowany na ciąg. 

![EventID wykresu słupkowego](media/charts/charts-and-diagrams-multiDimension1.png)

Możesz przełączać się między, wybierając listę rozwijaną z nazwą kolumny. 

![Pasek kont wykresu słupkowego](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z innymi lekcjami dotyczącymi używania [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane](advanced-aggregations.md)
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Pisanie zapytań zaawansowanych](advanced-query-writing.md)
- [Sprzężenia](joins.md)
