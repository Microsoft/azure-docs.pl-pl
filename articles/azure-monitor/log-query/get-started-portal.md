---
title: 'Samouczek: Rozpoczynanie pracy z zapytaniami Log Analytics'
description: Zapoznaj się z tym samouczkiem, jak pisać i zarządzać Azure Monitor zapytań dzienników przy użyciu Log Analytics w Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: b337065f125d12e309dd1f7fcc56c2af72b1c28c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088361"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Samouczek: Rozpoczynanie pracy z zapytaniami Log Analytics

W tym samouczku pokazano, jak używać Log Analytics do pisania i wykonywania zapytań dzienników Azure Monitor i zarządzania nimi w Azure Portal. Za pomocą zapytań Log Analytics można wyszukiwać warunki, identyfikować trendy i analizować wzorce oraz dostarczać wiele innych szczegółowych informacji z danych. 

W ramach tego samouczka nauczysz się używać Log Analytics do:

> [!div class="checklist"]
> * Informacje o schemacie danych dziennika
> * Zapisuj i uruchamiaj proste zapytania oraz Modyfikuj zakres czasu dla zapytań
> * Filtrowanie, sortowanie i grupowanie wyników zapytania
> * Wyświetlanie, modyfikowanie i udostępnianie wizualizacji wyników zapytania
> * Zapisz, Załaduj, Eksportuj i Kopiuj zapytania i wyniki

Aby uzyskać więcej informacji o zapytaniach dziennika, zobacz [Omówienie zapytań dzienników w Azure monitor](log-query-overview.md).<br/>
Szczegółowy samouczek dotyczący pisania zapytań dzienników znajduje się [w temacie Rozpoczynanie pracy z dziennikami zapytań w Azure monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Otwórz Log Analytics
Aby korzystać z Log Analytics, musisz zalogować się do konta platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby wykonać większość kroków z tego samouczka, możesz użyć [tego środowiska demonstracyjnego](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), obejmującego wiele przykładowych danych. W środowisku demonstracyjnym nie będzie można zapisywać zapytań ani przypinać wyników do pulpitu nawigacyjnego.

Możesz również użyć własnego środowiska, jeśli używasz Azure Monitor do zbierania danych dziennika na co najmniej jednym zasobie platformy Azure. Aby otworzyć obszar roboczy Log Analytics, w obszarze Azure Monitor nawigacji po lewej stronie wybierz pozycję **dzienniki**. 

## <a name="understand-the-schema"></a>Informacje o schemacie
 
*Schemat* to zbiór tabel pogrupowanych pod kątem kategorii logicznych. Schemat demonstracyjny zawiera kilka kategorii z rozwiązań do monitorowania. Na przykład kategoria **LogManagement** zawiera zdarzenia systemu Windows i dziennika systemowego, dane wydajności i pulsy agentów.

Tabele schematów są wyświetlane na karcie **tabele** w obszarze roboczym log Analytics. Tabele zawierają kolumny, z których każdy ma typ danych wyświetlany przez ikonę obok nazwy kolumny. Na przykład tabela **zdarzeń** zawiera kolumny tekstowe, takie jak **komputer** i kolumny liczbowe, takie jak **EventCategory**.

![Zrzut ekranu przedstawia stronę dzienników Azure Portal z nowym zapytaniem, podświetl okienko tabele z wyróżnionym komputerem i EventCategory.](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Zapisz i uruchom podstawowe zapytania

Log Analytics otwiera z nowym pustym zapytaniem w **Edytorze zapytań**.

![Usługa Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Napisz zapytanie

Zapytania dziennika Azure Monitor korzystają z wersji języka zapytań Kusto. Zapytania mogą rozpoczynać się od nazwy tabeli lub polecenia [wyszukiwania](/azure/kusto/query/searchoperator) . 

Następujące zapytanie pobiera wszystkie rekordy z tabeli **zdarzeń** :

```Kusto
Event
```

Znak potoku (|) oddziela polecenia, więc dane wyjściowe pierwszego polecenia są danymi wejściowymi następnego polecenia. Do pojedynczego zapytania można dodać dowolną liczbę poleceń. Poniższe zapytanie pobiera rekordy z tabeli **zdarzeń** , a następnie przeszukuje je pod kątem **błędu** terminu w dowolnej właściwości:

```Kusto
Event 
| search "error"
```

Pojedynczy podział wiersza sprawia, że zapytania są łatwiejsze do odczytu. Kilka podziałów wierszy dzieli zapytanie na osobne zapytania.

Innym sposobem zapisu tego samego zapytania jest:

```Kusto
search in (Event) "error"
```

W drugim przykładzie polecenie **wyszukiwania** przeszukuje tylko rekordy w tabeli **Events** dla **błędu**terminu.

Domyślnie Log Analytics ogranicza zapytania do zakresu czasu z ostatnich 24 godzin. Aby ustawić inny zakres czasu, można dodać jawny filtr **TimeGenerated** do zapytania lub użyć kontroli **zakresu czasu** .

### <a name="use-the-time-range-control"></a>Użyj kontrolki przedział czasu
Aby użyć kontrolki **zakres czasu** , zaznacz ją na górnym pasku, a następnie wybierz wartość z listy rozwijanej lub wybierz opcję **niestandardowa** , aby utworzyć niestandardowy zakres czasu.

![Wybór godziny](media/get-started-portal/time-picker.png)

- Wartości zakresu czasu są w formacie UTC, który może być inny niż lokalna strefa czasowa.
- Jeśli zapytanie jawnie ustawi filtr dla elementu **TimeGenerated**, kontrolka selektora czas pokazuje **ustawioną wartość w kwerendzie**i jest wyłączona, aby zapobiec konfliktowi.

### <a name="run-a-query"></a>Uruchamianie zapytania
Aby uruchomić zapytanie, umieść kursor w miejscu wewnątrz zapytania, a następnie wybierz pozycję **Uruchom** na górnym pasku lub naciśnij klawisz **SHIFT** + **Enter**. Zapytanie jest uruchamiane do momentu znalezienia pustego wiersza.

## <a name="filter-results"></a>Filtrowanie wyników
Log Analytics ogranicza wyniki do maksymalnie 10 000 rekordów. Zapytanie ogólne, takie jak `Event` zwraca zbyt wiele wyników, aby być przydatne. Wyniki zapytania można filtrować w sposób ograniczający elementy tabeli w zapytaniu lub przez jawne dodanie filtru do wyników. Filtrowanie przez elementy tabeli zwraca nowy zestaw wyników, podczas gdy jawny filtr ma zastosowanie do istniejącego zestawu wyników.

### <a name="filter-by-restricting-table-elements"></a>Filtruj, ograniczając elementy tabeli
Aby filtrować `Event` wyniki zapytania do zdarzeń **błędów** przez ograniczenie elementów tabeli w zapytaniu:

1. W wynikach zapytania wybierz strzałkę listy rozwijanej obok dowolnego rekordu, który zawiera **błąd** w kolumnie **EventLevelName** . 
   
1. W rozwiniętych szczegółach Umieść wskaźnik myszy i wybierz pozycję **...** obok pozycji **EventLevelName**, a następnie wybierz pozycję **Uwzględnij "błąd"**. 
   
   ![Dodaj filtr do zapytania](media/get-started-portal/add-filter.png)
   
1. Zwróć uwagę, że zapytanie w **Edytorze zapytań** zostało teraz zmienione na:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Wybierz pozycję **Uruchom** , aby uruchomić nowe zapytanie.

### <a name="filter-by-explicitly-filtering-results"></a>Filtruj według jawnego filtrowania wyników
Aby filtrować `Event` wyniki zapytania do zdarzeń **błędów** przez filtrowanie wyników zapytania:

1. W wynikach zapytania wybierz ikonę **filtru** obok nagłówka kolumny **EventLevelName**. 
   
1. W pierwszym polu okna podręcznego wybierz opcję **równa**się, a w następnym polu wpisz *błąd*. 
   
1. Wybierz pozycję **Filtr**.
   
   ![Zrzut ekranu przedstawia tabelę wyników z menu kontekstowym do filtrowania wyników według EventLevelName.](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Sortuj, Grupuj i zaznaczaj kolumny
Aby posortować wyniki zapytania według określonej kolumny, takiej jak **TimeGenerated [UTC]**, wybierz nagłówek kolumny. Ponownie wybierz nagłówek, aby przełączać się między rosnącą a malejącą kolejnością.

![Sortuj kolumnę](media/get-started-portal/sort-column.png)

Inny sposób organizowania wyników odbywa się według grup. Aby pogrupować wyniki według określonej kolumny, przeciągnij nagłówek kolumny na pasek powyżej tabeli wyników z etykietą **przeciągnij nagłówek kolumny i upuść go tutaj, aby grupować według tej kolumny**. Aby utworzyć podgrupy, przeciągnij inne kolumny na górny pasek. Można zmienić kolejność hierarchii i sortowanie grup i podgrup na pasku.

![Zrzut ekranu przedstawia wyniki zapytania z podgrupami dla EventLevelName i komputera.](media/get-started-portal/groups.png)

Aby ukryć lub pokazać kolumny w wynikach, zaznacz **kolumny** powyżej tabeli, a następnie zaznacz lub usuń zaznaczenie żądanych kolumn z listy rozwijanej.

![Wybieranie kolumn](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Wyświetlanie i modyfikowanie wykresów
Wyniki zapytania można również wyświetlać w formatach wizualnych. Wprowadź następujący przykład zapytania:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Domyślnie wyniki są wyświetlane w tabeli. Wybierz pozycję **Wykres** powyżej tabeli, aby wyświetlić wyniki w widoku graficznym.

![Wykres słupkowy](media/get-started-portal/bar-chart.png)

Wyniki pojawiają się na skumulowanym wykresie słupkowym. Wybierz inne opcje, takie jak **skumulowany kolumnowy** lub **kołowy** , aby pokazać inne widoki wyników.

![Wykres kołowy](media/get-started-portal/pie-chart.png)

Można zmienić właściwości widoku, takie jak osie x i y, lub grupować i dzielić preferencje ręcznie z paska sterowania.

Możesz również ustawić preferowany widok w samej kwerendzie przy użyciu operatora [renderowania](/azure/kusto/query/renderoperator) .

## <a name="pin-results-to-a-dashboard"></a>Przypnij wyniki do pulpitu nawigacyjnego

Aby przypiąć tabelę wyników lub wykres z Log Analytics do udostępnionego pulpitu nawigacyjnego platformy Azure, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** na górnym pasku. 

![Przypnij do pulpitu nawigacyjnego](media/get-started-portal/pin-dashboard.png)

W **innym okienku pulpitu nawigacyjnego** , wybierz lub Utwórz udostępniony pulpit nawigacyjny, aby przypiąć do i wybierz pozycję **Zastosuj**. Tabela lub wykres pojawia się na wybranym pulpicie nawigacyjnym platformy Azure.

![Wykres przypięty do pulpitu nawigacyjnego](media/get-started-portal/pin-dashboard2.png)

Tabela lub wykres przypięcia do udostępnionego pulpitu nawigacyjnego ma następujące uproszczenia: 

- Dane są ograniczone do ostatnich 14 dni.
- Tabela zawiera tylko do czterech kolumn i pierwszych siedem wierszy.
- Wykresy z wieloma dyskretnymi kategoriami automatycznie grupują mniej wypełnione kategorie w **jeden zasobnik.**

## <a name="save-load-or-export-queries"></a>Zapisz, Załaduj lub Eksportuj zapytania

Po utworzeniu zapytania można zapisać lub udostępnić zapytanie lub wyniki z innymi osobami. 

### <a name="save-queries"></a>Zapisywanie zapytań

Aby zapisać zapytanie:

1. Wybierz pozycję **Zapisz** na górnym pasku.
   
1. W oknie dialogowym **zapisywania** nadaj kwerendzie **nazwę**, używając znaków A – Z, a – z, 0-9, spacji, łączników, podkreślenia, kropki, nawiasów lub potoku. 
   
1. Wybierz, czy kwerenda ma zostać zapisana jako **zapytanie** czy **Funkcja**. Funkcje są zapytania, do których mogą się odwoływać inne zapytania. 
   
   Aby zapisać zapytanie jako funkcję, podaj **alias funkcji**, który jest krótką nazwą innych zapytań, które mają być używane do wywołania tego zapytania.
   
1. Jeśli jesteś w obszarze roboczym Log Analytics, podaj **kategorię** **Eksploratora zapytań** do użycia w zapytaniu. (Kategorie nie są dostępne dla zapytań Application Insights)
   
1. Wybierz pozycję **Zapisz**.
   
   ![Funkcja Save](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Załaduj zapytania
Aby załadować zapisane zapytanie, wybierz pozycję **Eksplorator zapytań** w prawym górnym rogu. Zostanie otwarte okienko **Eksplorator zapytań** zawierające listę wszystkich zapytań według kategorii. Rozwiń kategorie lub wprowadź nazwę zapytania na pasku wyszukiwania, a następnie wybierz zapytanie, aby załadować je do **edytora zapytań**. Zapytanie można oznaczyć jako **Ulubione** , wybierając gwiazdkę obok nazwy zapytania.

![Eksplorator zapytań](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Eksportowanie i udostępnianie zapytań
Aby wyeksportować zapytanie, wybierz pozycję **Eksportuj** na górnym pasku, a następnie wybierz pozycję **Eksportuj do pliku CSV — wszystkie kolumny**, **Eksportuj do pliku CSV — wyświetlane kolumny**lub **Eksportuj do Power BI (M Query)** z listy rozwijanej.

Poniższy film wideo pokazuje, jak zintegrować Log Analytics z programem Excel.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Aby udostępnić link do zapytania, wybierz pozycję **Kopiuj link** na górnym pasku, a następnie wybierz pozycję **Kopiuj link do zapytania**, **skopiuj tekst zapytania**lub **Skopiuj wyniki zapytania** , aby skopiować je do Schowka. Można wysłać link zapytania do innych osób, które mają dostęp do tego samego obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się więcej na temat pisania zapytań dziennika Azure Monitor.
> [!div class="nextstepaction"]
> [Zapisz zapytania dziennika Azure Monitor](get-started-queries.md)
