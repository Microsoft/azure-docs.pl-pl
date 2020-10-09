---
title: Konwersja kafelków z Azure Monitor View na skoroszyty
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77658630"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Konwersje kafelków projektanta widoków Azure Monitor
[Projektant widoków](view-designer.md) jest funkcją Azure monitor, która umożliwia tworzenie niestandardowych widoków, które ułatwiają wizualizację danych w obszarze roboczym log Analytics, z wykresami, listami i osiami czasu. Są one wycofywane i zastępowane skoroszytami, które zapewniają dodatkową funkcjonalność. Ten artykuł zawiera szczegółowe informacje dotyczące konwertowania różnych kafelków do skoroszytów.

## <a name="donut--list-tile"></a>Kafelek listy & pierścieni

![Lista pierścieni](media/view-designer-conversion-tiles/donut-list.png)

Ponowne tworzenie kafelka listy & pierścieni w skoroszytach obejmuje dwie osobne wizualizacje. W przypadku części pierścieniowej dostępne są dwie opcje.
Dla obu startu wybierz pozycję **Dodaj zapytanie** i wklej oryginalne zapytanie z projektanta widoku do komórki.

**Opcja 1:** Wybierz **Wykres kołowy** z listy rozwijanej **Wizualizacja** : ![ menu wizualizacji wykresu kołowego](media/view-designer-conversion-tiles/pie-chart.png)

**Opcja 2:** Wybierz pozycję **Ustaw według zapytania** z listy rozwijanej **Wizualizacja** i Dodaj `| render piechart` do zapytania:

 ![Menu wizualizacji](media/view-designer-conversion-tiles/set-by-query.png)

**Przykład**

Oryginalne zapytanie
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Zaktualizowane zapytanie
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Aby utworzyć listę i włączyć wykresy przebiegu w czasie, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Poniżej przedstawiono przykład sposobu, w jaki kafelek listy & pierścienia może być interpretowany w skoroszytach:

![Skoroszyty listy pierścieni](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Kafelek listy & wykresu liniowego
![Lista wykresów liniowych](media/view-designer-conversion-tiles/line-list.png) 

Aby ponownie utworzyć wykres liniowy, należy zaktualizować zapytanie w następujący sposób:

Oryginalne zapytanie
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Zaktualizowane zapytanie
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Dostępne są dwie opcje wizualizacji wykresu liniowego.

**Opcja 1:** Wybierz **Wykres liniowy** z listy rozwijanej **Wizualizacja** :
 
 ![Menu Wykres liniowy](media/view-designer-conversion-tiles/line-visualization.png)

**Opcja 2:** Wybierz pozycję **Ustaw według zapytania** z listy rozwijanej **Wizualizacja** i Dodaj `| render linechart` do zapytania:

 ![Menu wizualizacji](media/view-designer-conversion-tiles/set-by-query.png)

**Przykład**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Aby utworzyć listę i włączyć wykresy przebiegu w czasie, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Poniżej przedstawiono przykład sposobu, w jaki wykres liniowy & kafelka listy może być interpretowany w skoroszytach:

![Skoroszyty listy wykresów liniowych](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Kafelek listy & liczb

 ![Lista kafelków](media/view-designer-conversion-tiles/tile-list-example.png)

Dla kafelka liczba zaktualizuj zapytanie w następujący sposób:

Oryginalne zapytanie
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Zaktualizowane zapytanie
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Zmień listę rozwijaną wizualizacji na **kafelki** , a następnie wybierz pozycję **Ustawienia kafelków**.
 ![Wizualizacja kafelka](media/view-designer-conversion-tiles/tile-visualization.png)

Pozostaw puste pole **tytuł** i wybierz pozycję **pozostawione**. Zmień wartość parametru **use Column:** to **Count**i **renderowanie kolumn** na **dużą liczbę**:

![Ustawienia kafelka](media/view-designer-conversion-tiles/tile-settings.png)

 
Aby utworzyć listę i włączyć wykresy przebiegu w czasie, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Poniżej przedstawiono przykład sposobu, w jaki kafelek listy & może być reinterpretowany w skoroszytach:

![Liczba skoroszytów listy](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Oś czasu i lista

 ![Lista osi czasu](media/view-designer-conversion-tiles/time-list.png)

W przypadku osi czasu zaktualizuj zapytanie w następujący sposób:

Oryginalne zapytanie
```KQL
search * 
| sort by TimeGenerated desc
```

Zaktualizowane zapytanie
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Dostępne są dwie opcje wizualizacji zapytania jako wykres słupkowy:

**Opcja 1:** Wybierz **Wykres słupkowy** z listy rozwijanej **Wizualizacja** : ![ Wizualizacja BarChart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Opcja 2:** Wybierz pozycję **Ustaw według zapytania** z listy rozwijanej **Wizualizacja** i Dodaj `| render barchart` do zapytania:

 ![Menu wizualizacji](media/view-designer-conversion-tiles/set-by-query.png)

 
Aby utworzyć listę i włączyć wykresy przebiegu w czasie, zobacz artykuł dotyczący [typowych zadań](view-designer-conversion-tasks.md).

Poniżej przedstawiono przykład sposobu reinterpretacji kafelka osi czasu & listy w skoroszytach:

![Skoroszyty listy osi czasu](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Następne kroki

- [Omówienie przejścia projektanta widoku na skoroszyty](view-designer-conversion-overview.md)
