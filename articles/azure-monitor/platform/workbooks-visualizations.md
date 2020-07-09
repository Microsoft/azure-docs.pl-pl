---
title: Azure Monitor wizualizacje skoroszytu
description: Dowiedz się więcej na temat wszystkich składników wizualizacji skoroszytów Azure Monitor, takich jak tekst, wykresy, siatki, drzewa i wykresy.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658034"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor wizualizacje skoroszytu

Azure Monitor skoroszyty obsługują wiele różnych stylów wizualizacji w celu uwzględnienia potrzeb związanych z raportowaniem. W tym artykule przedstawiono przykłady poszczególnych typów wizualizacji.

## <a name="text"></a>Tekst

Skoroszyty umożliwiają autorom dołączenie bloków tekstowych do ich skoroszytów. Tekst może być analizą danych telemetrycznych, informacjami, które pomagają użytkownikom interpretować dane, nagłówki sekcji itd.

![Zrzut ekranu przedstawiający tabelę Apdex tekstu](./media/workbooks-visualizations/apdex.png)

Tekst jest dodawany za pomocą kontrolki promocji, która zapewnia pełną kontrolę formatowania.

![Zrzut ekranu nieprzetworzonej promocji, który kompiluje renderowane tabelę](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Dodawanie kontrolki tekstu

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edytowanie** .
2. Użyj linku **Dodaj tekst** , aby dodać kontrolkę tekstu do skoroszytu.
3. Dodawanie promocji do formantu.
4. Kliknij przycisk **Zakończono edycję** , aby wyświetlić sformatowany tekst.

> [!TIP]
> Skorzystaj z tego [arkusza Ściągawka z promocji](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) , aby poznać różne opcje formatowania.

## <a name="charts"></a>Wykresy

Skoroszyty umożliwiają prezentowanie danych monitorowania jako wykresów. Obsługiwane typy wykresów obejmują wiersz, słupek, pasek kategorii, obszar, wykresy punktowe, wykres kołowy i czas. Autorzy mogą zdecydować się na dostosowanie wysokości, szerokości, palety kolorów, legendy, tytułów, wiadomości bez danych itp. wykresu.

Skoroszyty obsługują wykresy zarówno dla dzienników, jak i źródeł danych metryk. 

### <a name="adding-a-log-chart"></a>Dodawanie wykresu dziennika

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edytowanie** .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić [KQL](https://docs.microsoft.com/azure/kusto/query/) do analizy (na przykład trend żądań).
5. Ustaw wizualizację na jeden z: **obszar**, **pasek**, **pasek (kategorii)**, **linia**, **Wykres kołowy**, **punktowy**lub **czas**.
6. W razie konieczności Ustaw inne parametry, takie jak zakres czasu, Wizualizacja, rozmiar, paleta kolorów i Legenda.

![Zrzut ekranu przedstawiający wykres dziennika w trybie edycji](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Parametry wykresu dziennika

| Parametr | Objaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ zapytania do użycia | Dziennik, wykres zasobów platformy Azure itd. |
| `Resource Type` | Typ zasobu docelowego | Application Insights, Log Analytics lub Azure — najpierw |
| `Resources` | Zestaw zasobów, z którego ma zostać uzyskana wartość metryki | MyApp1 |
| `Time Range` | Przedział czasu, aby wyświetlić wykres dziennika | Ostatnia godzina, ostatnie 24 godziny itd. |
| `Visualization` | Wizualizacja do użycia | Obszar, słupek, linia, wykres kołowy, punktowy, czas, pasek kategorii |
| `Size` | Rozmiar w pionie kontrolki | Małe, średnie, duże lub pełne |
| `Color palette` | Paleta kolorów, która ma być używana na wykresie. Zignorowano w trybie z obsługą metryki lub z segmentacją. | Niebieski, zielony, czerwony itd. |
| `Legend` | Funkcja agregacji do użycia w legendzie | Suma lub średnia wartości lub maks., minimalna, pierwsza, Ostatnia wartość |
| `Query` | Dowolne zapytanie KQL zwracające dane w formacie oczekiwanym przez wizualizację wykresu | _żąda \| żądań wykonania serii = Count () domyślnie = 0 w sygnaturze czasowej z temu (1D) do Now () Step 1 godz_ |

### <a name="adding-a-metric-chart"></a>Dodawanie wykresu metryki

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edytowanie** .
2. Za pomocą linku **Dodaj metrykę** Dodaj do skoroszytu formant metryki.
3. Wybierz typ zasobu (na przykład konto magazynu), zasoby docelowe, przestrzeń nazw i nazwę metryki, a następnie użyj agregacji.
4. W razie konieczności Ustaw inne parametry, takie jak zakres czasu, podział przez, Wizualizacja, rozmiar i paleta kolorów.

![Zrzut ekranu przedstawiający wykres metryki w trybie edycji](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Parametry wykresu metryki

| Parametr | Objaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `Resource Type` | Typ zasobu docelowego | Magazyn lub maszyna wirtualna. |
| `Resources` | Zestaw zasobów, z którego ma zostać uzyskana wartość metryki | MyStorage1 |
| `Namespace` | Przestrzeń nazw z metryką | Magazyn > obiektów BLOB |
| `Metric` | Metryka do wizualizacji | > transakcje magazynu > obiektów BLOB |
| `Aggregation` | Funkcja agregacji, która ma zostać zastosowana do metryki | Suma, liczność, średnia itp. |
| `Time Range` | Przedział czasu, w którym ma zostać wyświetlona Metryka | Ostatnia godzina, ostatnie 24 godziny itd. |
| `Visualization` | Wizualizacja do użycia | Obszar, słupek, linia, punktowy, Siatka |
| `Split By` | Opcjonalnie Podziel metrykę na wymiar | Transakcje według typu geograficznego |
| `Size` | Rozmiar w pionie kontrolki | Małe, średnie lub duże |
| `Color palette` | Paleta kolorów, która ma być używana na wykresie. Ignorowane, jeśli `Split by` parametr jest używany | Niebieski, zielony, czerwony itd. |

## <a name="grids"></a>Siatki

Siatki lub tabele są typowym sposobem prezentowania danych użytkownikom. Skoroszyty umożliwiają użytkownikom pojedyncze Style kolumn siatki, aby zapewnić bogaty interfejs użytkownika dla swoich raportów.

W poniższym przykładzie przedstawiono siatkę łączącą ikony, map cieplnych i paski Spark w celu prezentowania złożonych informacji. Skoroszyt zawiera również sortowanie, pole wyszukiwania i przycisk przechodzenie do analizy.

![Zrzut ekranu przedstawiający siatkę opartą na dzienniku](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Dodawanie siatki opartej na dzienniku

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi **Edytowanie** .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy (na przykład maszyny wirtualne z pamięcią poniżej wartości progowej)
5. Ustaw wizualizację na **siatkę**
6. W razie konieczności Ustaw inne parametry, takie jak zakres czasu, rozmiar, paleta kolorów i Legenda.

![Zrzut ekranu przedstawiający kwerendę siatki opartą na dzienniku](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Kafelki

Kafelki to bardzo przydatny sposób prezentowania danych podsumowujących w skoroszytach. Na poniższej ilustracji przedstawiono typowy przypadek użycia kafelków — podsumowanie poziomu aplikacji na podstawie szczegółowej siatki.

![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-visualizations/tiles-summary.png)

Kafelki skoroszytu obsługują wyświetlanie tytułu, podtytuł, duże tekstu, ikon, gradientów opartych na metrykach, linii/słupków Spark, stopek itp.

### <a name="adding-a-tile"></a>Dodawanie kafelka

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu. 
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Ustaw rozmiar na **pełny**
6. Ustawianie wizualizacji na **kafelki**
7. Kliknij przycisk **Ustawienia kafelka** , aby otworzyć okienko ustawienia
8. W polu **kafelków**Ustaw:
    * Tytuły`name`
    * Left: `Requests` , Render: `Big Number` , paleta kolorów: `Green to Red` , wartość minimalna:`0`
    * Stop`appName`
9. Kliknij przycisk **Zapisz i Zamknij** w dolnej części okienka.

![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-visualizations/tile-settings.png)

Jest to sposób, w jaki kafelki będą wyglądały w trybie odczytu:

![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Drzewo

Skoroszyty obsługują widoki hierarchiczne za pośrednictwem siatek drzewa. Drzewa pozwalają na rozwijanie niektórych wierszy do następnego poziomu dla środowiska przechodzenia do szczegółów.

W poniższym przykładzie przedstawiono wizualizacje metryk (rozmiar zestawu roboczego) jako siatkę drzewa. Poniżej znajdują się węzły najwyższego poziomu, które są węzłami usługi Azure Kubernetes Service (AKS), następnym poziomem są zasobniki, a ostatnim poziomem są kontenery. Należy zauważyć, że nadal można sformatować kolumny tak jak w siatce (mapę cieplną, ikony, link). Bazowe źródło danych w tym przypadku jest obszarem roboczym Log Analytics z dziennikami AKS.

![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Dodawanie siatki drzewa
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu. 
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Ustaw wizualizację na **siatkę**
6. Kliknij przycisk **Ustawienia kolumny** , aby otworzyć okienko ustawienia
7. W sekcji **drzewo/grupowanie według ustawień** na dole Ustaw:
    * Typ drzewa:`Parent/Child`
    * Pole identyfikatora:`Id`
    * Pole identyfikatora nadrzędnego:`ParentId`
    * Pokaż Ekspander na:`Name`
    * Rozwiń najwyższy poziom drzewa:`checked`
8. W sekcji _kolumny_ w górnej części Ustaw:
    * Renderowanie kolumny _identyfikatora_ :`Hidden`
    * Moduł renderowania kolumn _nadrzędnych_ :`Hidden`
    * _Żądania_ -Column renderujące: `Bar` , Color: `Blue` , wartość minimalna:`0`
9. Kliknij przycisk _Zapisz i Zamknij_ w dolnej części okienka.    

![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Ustawienia drzewa

| Ustawienie | Objaśnienie |
|:------------- |:-------------|
| `Id Field` | Unikatowy identyfikator każdego wiersza w siatce |
| `Parent Id Field` | Identyfikator elementu nadrzędnego bieżącego wiersza |
| `Show the expander on` | Kolumna, w której ma zostać wyświetlony Ekspander drzewa. Siatka drzewa jest często ukryta, aby ukryć swoje identyfikatory i pola identyfikatorów nadrzędnych, ponieważ nie są one bardzo czytelne. Zamiast tego, Ekspander pojawia się w polu o bardziej czytelnej wartości podobnej do nazwy jednostki |
| `Expand the top level of the tree` | Jeśli ta opcja jest zaznaczona, Siatka drzewa zostanie rozwinięta na najwyższego poziomu. Przydatne, jeśli chcesz domyślnie wyświetlić więcej informacji |

## <a name="graphs"></a>Diagram

Skoroszyty obsługują wizualizację dowolnych wykresów opartych na danych z dzienników, aby pokazać relacje między jednostkami monitorowania.

Wykres poniżej przedstawia dane przepływające z komputera lub z niego za pośrednictwem różnych portów do/z komputerów zewnętrznych. Jest to kolor według typu (komputer a port a zewnętrzny adres IP), a rozmiary brzegowe odpowiadają ilości danych przepływających między. Dane podstawowe pochodzą z zapytania KQL ukierunkowanego na połączenia maszyn wirtualnych.

![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Dodawanie wykresu
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu. 
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Ustaw wizualizację na **Graph**
8. Kliknij przycisk **Ustawienia grafu** , aby otworzyć okienko ustawienia
9. W obszarze _pola układu_ u dołu ustaw:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. W obszarze _Ustawienia formatu węzła_ u góry Ustaw:
    * _Górna zawartość_— Użyj kolumny: `Name` , modułu renderowania kolumn:`Text`
    * _Wyśrodkuj zawartość_— Użyj kolumny: `Calls` , modułu renderowania kolumn: `Big Number` , paleta kolorów:`None`
    * _Dolna zawartość_— Użyj kolumny: `Kind` , modułu renderowania kolumn:`Text`
10. Kliknij przycisk _Zapisz i Zamknij_ w dolnej części okienka.

![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Następne kroki

* [Wdróż](workbooks-automate.md) skoroszyty przy użyciu Azure Resource Manager.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
