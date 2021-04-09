---
title: Wizualizacje grafu Azure Monitor
description: Dowiedz się więcej o wszystkich wizualizacjach grafu Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100620660"
---
# <a name="graph-visualizations"></a>Wizualizacje grafu

Skoroszyty obsługują wizualizację dowolnych wykresów opartych na danych z dzienników, aby pokazać relacje między jednostkami monitorowania.

Wykres poniżej przedstawia dane przepływające do/z komputera za pośrednictwem różnych portów do/z komputerów zewnętrznych. Jest to kolor według typu (komputer a port a zewnętrzny adres IP), a rozmiary brzegowe odpowiadają ilości danych przepływających między. Dane podstawowe pochodzą z zapytania KQL ukierunkowanego na połączenia maszyn wirtualnych.

[![Zrzut ekranu przedstawiający Widok podsumowania kafelków](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Dodawanie wykresu
1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy.

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

5. Ustaw wizualizację na **Graph**
6. Wybierz przycisk **Ustawienia grafu** , aby otworzyć okienko ustawienia
7. W obszarze _pola układu_ u dołu ustaw:
    * Identyfikator węzła: `Id`
    * Identyfikator źródła: `SourceId`
    * Identyfikator obiektu docelowego: `TargetId`
    * Etykieta krawędziowa: `None`
    * Rozmiar krawędzi: `Calls`
    * Rozmiar węzła: `None`
    * Typ kolorowania: `Categorical`
    * Pole koloru węzła: `Kind`
    * Paleta kolorów: `Pastel`
8. W obszarze _Ustawienia formatu węzła_ u góry Ustaw:
    * _Górna zawartość_— Użyj kolumny: `Name` , modułu renderowania kolumn: `Text`
    * _Wyśrodkuj zawartość_— Użyj kolumny: `Calls` , modułu renderowania kolumn: `Big Number` , paleta kolorów: `None`
    * _Dolna zawartość_— Użyj kolumny: `Kind` , modułu renderowania kolumn: `Text`
9. Wybierz przycisk _Zapisz i Zamknij_ w dolnej części okienka.

[![Zrzut ekranu przedstawiający Widok podsumowania kafelków z powyższym zapytaniem i ustawieniami.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Ustawienia wykresu

| Ustawienie         | Wyjaśnienie                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Wybiera kolumnę, która dostarcza unikatowy identyfikator węzłów na wykresie. Wartością kolumny może być ciąg lub liczba. |
| `Source Id`     | Wybiera kolumnę, która zawiera identyfikatory węzłów źródłowych dla krawędzi wykresu. Wartości muszą być mapowane na wartość w kolumnie _identyfikator węzła_ . |
| `Target Id`     | Wybiera kolumnę, która zawiera identyfikatory węzłów docelowych dla krawędzi wykresu. Wartości muszą być mapowane na wartość w kolumnie _identyfikator węzła_ . |
| `Edge Label`    | Wybiera kolumnę, która dostarcza etykiety krawędzi wykresu.                                                            |
| `Edge Size`     | Wybiera kolumnę, która dostarcza metrykę, na której będzie oparta szerokość krawędzi.                                |
| `Node Size`     | Wybiera kolumnę, która dostarcza metrykę, na której będą oparte obszary węzła.                                 |
| `Coloring Type` | Służy do wybierania schematu kolorowania węzła.                                                                            |

## <a name="node-coloring-types"></a>Typy kolorowania węzła

| Typ kolorowania | Wyjaśnienie |
|:------------- |:------------|
| `None`        | Wszystkie węzły mają ten sam kolor. |
| `Categorical` | Do węzłów są przypisywane kolory oparte na wartości lub kategorii z kolumny w zestawie wyników. W powyższym przykładzie kolorowanie jest oparte na _rodzaju_ kolumn zestawu wyników. Obsługiwane palety to `Default` , `Pastel` i `Cool tone` .  |
| `Field Based` | W tym typie kolumna zawiera określone wartości RGB, które mają być używane dla węzła. Zapewnia największą elastyczność, ale zazwyczaj wymaga więcej pracy do włączenia.  |

## <a name="node-format-settings"></a>Ustawienia formatu węzła

Autorzy wykresów mogą określać zawartość przechodzącą do różnych części węzła: Top, Left, Center, Right i Bottom. Wykresy mogą korzystać z dowolnych skoroszytów renderowania (tekst, wielkie liczby, linie Spark, ikona itp.).

## <a name="field-based-node-coloring"></a>Kolorowanie węzła na podstawie pola

1. Przełącz skoroszyt do trybu edycji, klikając element paska narzędzi _Edytowanie_ .
2. Użyj linku **Dodawanie zapytania** , aby dodać kontrolkę zapytania dziennika do skoroszytu.
3. Wybierz typ zapytania jako **Dziennik**, typ zasobu (na przykład Application Insights) i zasoby, które mają być docelowe.
4. Użyj edytora zapytań, aby wprowadzić KQL do analizy.

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
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Ustaw wizualizację na *Graph*
6. Wybierz przycisk **Ustawienia grafu**  , aby otworzyć okienko ustawienia.
7. W obszarze *pola układu* u dołu ustaw:
    * Identyfikator węzła:`Id`
    * Identyfikator źródła: `SourceId`
    * Identyfikator obiektu docelowego: `TargetId`
    * Etykieta krawędziowa: `None`
    * Rozmiar krawędzi: `Calls`
    * Rozmiar węzła: `Node`
    * Typ kolorowania: `Field Based`
    * Pole koloru węzła: `Color`
8. W obszarze *Ustawienia formatu węzła* u góry wprowadź następujące polecenie.
    * W *górnej zawartości* Ustaw:
        * Użyj kolumny: `Name` .
        * Renderowanie kolumn: `Text` .
    * W *centrum zawartości* Ustaw:
        * Użyj kolumny: `Calls`
        * Renderowanie kolumn: `Big Number`
        * Paleta kolorów: `None`
    * W *dolnej zawartości* Ustaw:
        * Użyj kolumny: `Kind`
        * Renderowanie kolumn: `Text` .
9. Wybierz **przycisk Zapisz i Zamknij** w dolnej części okienka.

[![Zrzut ekranu przedstawiający tworzenie wizualizacji wykresu przy użyciu kolorowania węzła podstawowego pola.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Następne kroki

* Wykresy obsługują również moduł renderowania złożonego słupków. Więcej informacji można znaleźć w [dokumentacji paska złożonego](workbooks-composite-bar.md).
* Dowiedz się więcej na temat [źródeł danych](workbooks-data-sources.md) , których można używać w skoroszytach.
