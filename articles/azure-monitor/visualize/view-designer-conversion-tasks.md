---
title: Azure Monitor widoku projektanta na skoroszyty typowe zadania konwersji
description: Typowe zadania podczas przechodzenia z widoków do skoroszytów w Azure Monitor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 4e9d9e65dedae083138f3b29a2a609e124ce0fc8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619120"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Wyświetlanie typowych zadań konwersji projektanta na skoroszyty
[Projektant widoków](view-designer.md) jest funkcją Azure monitor, która umożliwia tworzenie niestandardowych widoków, które ułatwiają wizualizację danych w obszarze roboczym log Analytics, z wykresami, listami i osiami czasu. Są one wycofywane i zastępowane skoroszytami, które zapewniają dodatkową funkcjonalność. W tym artykule szczegółowo opisano zadania, które są wspólne w przypadku konwertowania widoków do skoroszytów.


## <a name="quickstart-with-preset-view-designer-templates"></a>Przewodnik Szybki Start z predefiniowanymi szablonami projektanta widoków

Skoroszyty w Log Analytics obszarach roboczych mają już szablony, które są zgodne z niektórymi widokami w projektancie widoków. W kategorii **Wyświetl prowadnice projektanta** wybierz pozycję **Wyświetl przewodnik przejścia projektanta** , aby dowiedzieć się więcej na temat opcji, lub wybierz jeden z predefiniowanych szablonów.

![Przykładowe szablony](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Włączanie filtru zakresu czasu
Projektant widoków ma wbudowany domyślny filtr zakresu czasu, jednak w skoroszytach to ustawienie nie jest domyślnie włączone. Skoroszyty umożliwiają użytkownikom tworzenie własnych filtrów zakresu czasu, które mogą być bardziej odpowiednie dla dzienników danych. Poniżej przedstawiono procedurę generowania filtru:

Wybierz opcję **Dodaj parametry** . Domyślny **styl** jest ustawiony na *Pills*.

![Dodaj param](media/view-designer-conversion-tasks/add-param.png)

 Wybierz przycisk **Dodaj parametr** .

![Dodaj parametr](media/view-designer-conversion-tasks/add-parameter.png)

Z menu paska bocznego w polu tekstowym **Nazwa parametru** wpisz *TimeRange*. Ustaw **Typ parametru** jako *Selektor zakresu czasu*. Zaznacz pole wyboru **wymagane?**

![Menu parametrów](media/view-designer-conversion-tasks/parameter-menu.png)

Zapisz parametr w lewym górnym rogu menu paska bocznego. Listę rozwijaną można pozostawić domyślnie jako *nieustawioną* lub wybrać domyślną wartość **TimeRange** , na przykład *24 godziny*. Wybierz pozycję **gotowe edytowanie**.

Parametry mogą być używane w zapytaniach przez dodanie nawiasów klamrowych {} otaczających nazwę parametru. Więcej szczegółów na temat parametrów można znaleźć w [dokumentacji skoroszytów dotyczącej parametrów](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Aktualizowanie zapytań za pomocą parametru TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opcja 1: wybierz pozycję TimeRange z listy rozwijanej zakresu czasu

![Parametr czasu](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opcja 2: aktualizowanie zapytań dziennika

W zapytaniu Dodaj wiersz: `| where TimeGenerated {TimeRange}` jak w poniższym przykładzie:

Oryginalne zapytanie
```KQL
search * 
| summarize count() by Type
```

Zaktualizowane zapytanie
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Dołączanie listy
Większość widoków projektanta widoku zawiera listę i można ją odtworzyć w skoroszycie.

![Lista kafelków](media/view-designer-conversion-tasks/tile-list.png)

Dodaj wizualizację, klikając pozycję **Dodaj zapytanie** w obszarze Opcje komórki.

![Dodaj param](media/view-designer-conversion-tasks/add-param.png)

Projektant widoków korzysta z zapytania domyślnego, które jest zgodne z składnią z oryginalnego przykładu. Można to zaktualizować przez zmianę zapytania do zaktualizowanego formularza, tak jak w poniższym przykładzie:

Oryginalne zapytanie
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Zaktualizowane zapytanie
```KQL
search * 
| summarize Count = count() by Type
```

Spowoduje to wygenerowanie listy podobnej do następującej:

![Przykład listy](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Włączanie wykresów przebiegu w czasie
Typową funkcją dla siatek jest dodanie wykresów przebiegu w celu podsumowania różnych wzorców danych w czasie. Projektant widoków oferuje funkcję **włączania wykresów przebiegu w czasie** dla wszystkich list, tak jak w przypadku skoroszytów. Aby uwzględnić wykresy przebiegu w danych, które pasują do projektanta widoków, Dołącz dane przy użyciu oryginalnego zapytania, jak w poniższym przykładzie:

Oryginalne zapytanie
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Zaktualizowane zapytanie
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Wybierz pozycję **Ustawienia kolumny**.
![Ustawienia kolumny](media/view-designer-conversion-tasks/column-settings.png)

Zaktualizuj listę rozwijaną **renderowania kolumn** , aby była *obszarem platformy Spark*.
![Wykresy przebiegu](media/view-designer-conversion-tasks/sparkline.png)

Zapisz ustawienia i ponownie uruchom zapytanie, aby zaktualizować tabelę w celu uwzględnienia wykresu przebiegu w czasie.

Siatka wyników będzie wyglądać podobnie do następującej: ![ przykład wykresu przebiegu w czasie](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Zaawansowane ustawienia komórki
Aby wyświetlić widok duplikatów, można wykonywać zadania, takie jak zmiana rozmiaru komórek skoroszytu lub Dodawanie kodów PIN i linków zewnętrznych do dzienników.

Aby uzyskać dostęp do **ustawień zaawansowanych** , wybierz ikonę koła zębatego u dołu każdej komórki.

![Ustawienia zaawansowane](media/view-designer-conversion-tasks/advanced-settings.png)

Spowoduje to wyświetlenie menu z różnymi opcjami:

![Ustawienia zaawansowane ustawień](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Aby dodać kod PIN i link do zapytania zewnętrznego, zaznacz odpowiednie pola wyboru. Aby dodać tytuł do komórki, wpisz odpowiedni tytuł w sekcji **tytuł wykresu** .

Domyślnie każda komórka skoroszytów jest ustawiana jako cała szerokość strony, ale można ją dostosować, przenosząc komórkę w dół na karcie **styl** menu **Ustawienia zaawansowane** .

![Styl ustawień zaawansowanych](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Dodatkowe parametry
Wybierz pozycję **Dodaj parametr** , aby utworzyć nowy parametr w skoroszycie. 

Aby wybrać subskrypcję, wpisz *subskrypcję* w polu **Nazwa parametru** w menu po stronie i wybierz opcję *wybór subskrypcji* z listy rozwijanej **Typ parametru**

![Menu subskrypcji](media/view-designer-conversion-tasks/subscription-filter.png)

Aby wybrać zasób, wpisz *zasób* w polu **Nazwa parametru** w menu po stronie i wybierz pozycję *Selektor zasobów* z listy rozwijanej **Typ parametru** .

![Menu zasobów](media/view-designer-conversion-tasks/resource-filter.png)

Spowoduje to wstawienie list rozwijanych, aby umożliwić dostęp do różnych subskrypcji i zasobów.

![Lista rozwijana zasobów subskrypcji](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Następne kroki
- [Konwertowanie kafelka](view-designer-conversion-tiles.md)
