---
title: Agregacje zaawansowane w zapytaniach dziennika Azure Monitor | Microsoft Docs
description: Opisuje niektóre bardziej zaawansowane opcje agregacji dostępne do Azure Monitor zapytań dzienników.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: dba058dce09e958a2ae769d927a5569fb3e42113
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87324560"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Agregacje zaawansowane w zapytaniach dziennika Azure Monitor

> [!NOTE]
> Przed ukończeniem tej lekcji należy ukończyć [agregacje w Azure monitor zapytaniach](./aggregations.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

W tym artykule opisano niektóre bardziej zaawansowane opcje agregacji dostępne dla Azure Monitor zapytań.

## <a name="generating-lists-and-sets"></a>Generowanie list i zestawów
Możesz użyć, `makelist` Aby przestawić dane według kolejności wartości w określonej kolumnie. Na przykład możesz chcieć poznać najczęściej występujące zdarzenia dotyczące zamówienia na Twoich komputerach. Dane można zasadniczo przestawiać według kolejności EventIDs na poszczególnych komputerach. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer (Komputer)|list_EventID|
|---|---|
| Komputer1 | [704, no, 1501, 1500, 1085, 704, 704 |
| KOMPUTER2 | [326 105 302 301 300 102] |
| ... | ... |

`makelist` generuje listę w kolejności, w której przekazano dane. Aby sortować zdarzenia od najstarszych do najnowszych, użyj `asc` w instrukcji Order zamiast `desc` . 

Warto również utworzyć listę tylko odrębnych wartości. Jest to tzw. _zestaw_ , który można wygenerować za pomocą `makeset` :

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer (Komputer)|list_EventID|
|---|---|
| Komputer1 | [704, no, 1501, 1500, 1085] |
| KOMPUTER2 | [326 105 302 301 300 102] |
| ... | ... |

Podobnie jak `makelist` , `makeset` działa również z danymi uporządkowanymi i generuje tablice na podstawie kolejności wierszy, które są do niego przenoszone.

## <a name="expanding-lists"></a>Rozwijanie list
Operacja odwrotna `makelist` lub `makeset` jest `mvexpand` , która rozwija listę wartości do oddzielnych wierszy. Może ona zostać rozszerzona na dowolną liczbę kolumn dynamicznych, zarówno JSON, jak i tablicę. Można na przykład sprawdzić tabelę *pulsu* dla rozwiązań wysyłających dane z komputerów, które wysłały puls w ciągu ostatniej godziny:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer (Komputer) | Rozwiązania | 
|--------------|----------------------|
| Komputer1 | "zabezpieczenia", "aktualizacje", "śledzenia zmian" |
| KOMPUTER2 | "zabezpieczenia", "aktualizacje" |
| computer3 | "oprogramowanie chroniące przed złośliwym kodem", "śledzenia zmian" |
| ... | ... |

Użyj `mvexpand` , aby pokazać każdą wartość w osobnym wierszu zamiast listy rozdzielanej przecinkami:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer (Komputer) | Rozwiązania | 
|--------------|----------------------|
| Komputer1 | bezpieczeństw |
| Komputer1 | dostępności |
| Komputer1 | Śledzenia zmian |
| KOMPUTER2 | bezpieczeństw |
| KOMPUTER2 | dostępności |
| computer3 | Antimalware |
| computer3 | Śledzenia zmian |
| ... | ... |


Następnie można użyć `makelist` ponownie do pogrupowania elementów, a tym razem listę komputerów na rozwiązanie:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Rozwiązania | list_Computer |
|--------------|----------------------|
| bezpieczeństw | ["Komputer1", "KOMPUTER2"] |
| dostępności | ["Komputer1", "KOMPUTER2"] |
| Śledzenia zmian | ["Komputer1", "computer3"] |
| Antimalware | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Obsługa brakujących pojemników
Przydatną aplikacją `mvexpand` jest potrzeba wypełnienia wartości domyślnych w przypadku brakujących pojemników. Załóżmy na przykład, że szukasz czasu przestoju konkretnej maszyny przez Eksplorowanie jej pulsu. Warto również zobaczyć Źródło pulsu, który znajduje się w kolumnie _Kategoria_ . Zwykle będziemy używać prostej instrukcji podsumowującej w następujący sposób:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Kategoria | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent bezpośredni | 2017 — 06-06T17:00:00Z | 15 |
| Agent bezpośredni | 2017 — 06-06T18:00:00Z | 60 |
| Agent bezpośredni | 2017 — 06-06T20:00:00Z | 55 |
| Agent bezpośredni | 2017 — 06-06T21:00:00Z | 57 |
| Agent bezpośredni | 2017 — 06-06T22:00:00Z | 60 |
| ... | ... | ... |

W tych rezultatach brakuje zasobnika skojarzonego z "2017-06-06T19:00:00Z", ponieważ nie ma żadnych danych pulsu dla tej godziny. Użyj `make-series` funkcji, aby przypisać wartość domyślną do pustych zasobników. Spowoduje to wygenerowanie wiersza dla każdej kategorii zawierającej dwie dodatkowe kolumny tablicowe, jedną dla wartości i jeden dla zgodnych przedziałów czasu:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategoria | count_ | TimeGenerated |
|---|---|---|
| Agent bezpośredni | [15, 60, 0, 55, 60, 57, 60,...] | ["2017-06-06T17:00:00.0000000 Z", "2017-06-06T18:00:00.0000000 Z", "2017-06-06T19:00:00.0000000 Z", "2017-06-06T20:00:00.0000000 Z", "2017-06-06T21:00:00.0000000 Z",...] |
| ... | ... | ... |

Trzeci element tablicy *count_* ma wartość 0 zgodnie z oczekiwaniami i istnieje zgodna sygnatura czasowa "2017-06-06T19:00:00.0000000 z" w tablicy _TimeGenerated_ . Ten format tablicy jest trudny do odczytania. Użyj, `mvexpand` Aby rozwinąć tablice i wygenerować ten sam format danych wyjściowych, które zostały wygenerowane przez `summarize` :

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Kategoria | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent bezpośredni | 2017 — 06-06T17:00:00Z | 15 |
| Agent bezpośredni | 2017 — 06-06T18:00:00Z | 60 |
| Agent bezpośredni | 2017 — 06-06T19:00:00Z | 0 |
| Agent bezpośredni | 2017 — 06-06T20:00:00Z | 55 |
| Agent bezpośredni | 2017 — 06-06T21:00:00Z | 57 |
| Agent bezpośredni | 2017 — 06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Zawężanie wyników do zestawu elementów: `let` , `makeset` , `toscalar` , `in`
Typowym scenariuszem jest wybranie nazw niektórych określonych jednostek na podstawie zestawu kryteriów, a następnie odfiltrowanie innych danych do tego zestawu jednostek. Na przykład można znaleźć komputery, na których wiadomo, że brakuje aktualizacji i zidentyfikować adresy IP, do których te komputery wezwały:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi lekcjami dotyczącymi używania [języka zapytań Kusto](/azure/kusto/query/) z danymi dziennika Azure Monitor:

- [Operacje dotyczące ciągów](string-operations.md)
- [Operacje dotyczące daty i godziny](datetime-operations.md)
- [Funkcje agregacji](aggregations.md)
- [Agregacje zaawansowane]()
- [Notacja JSON i struktury danych](json-data-structures.md)
- [Sprzężenia](joins.md)
- [Wykresy](charts.md)

