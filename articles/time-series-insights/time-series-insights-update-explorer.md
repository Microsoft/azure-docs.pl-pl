---
title: Wizualizowanie danych w Eksploratorze w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej o funkcjach i opcjach dostępnych w Eksploratorze Azure Time Series Insights w wersji zapoznawczej.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: 06f235b7f1c20681beee1f5be4c18de60d53a690
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037311"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Eksplorator Azure Time Series Insights w wersji zapoznawczej

W tym artykule opisano różne funkcje i opcje dostępne w [aplikacji sieci Web demonstracyjnej](https://insights.timeseries.azure.com/preview/demo)w wersji zapoznawczej programu Azure Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę z Eksploratorem Azure Time Series Insights w wersji zapoznawczej, należy wykonać następujące kroki:

* Zainicjowano środowisko Time Series Insights. Dowiedz się więcej o aprowizacji wystąpienia, odczytując samouczek [Azure Time Series Insights Preview](./time-series-insights-update-create-environment.md) .
* [Zapewnianie dostępu do danych](./time-series-insights-data-access.md) do środowiska Time Series Insights utworzonego dla konta. Możesz zapewnić dostęp do innych osób, jak również do siebie.
* Dodaj źródło zdarzenia do środowiska Time Series Insights, aby wypchnąć dane do środowiska:
  * Dowiedz się [, jak nawiązać połączenie z centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * Dowiedz się, [jak nawiązać połączenie z usługą IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Eksplorowanie Eksploratora Time Series Insights w wersji zapoznawczej

Eksplorator Azure Time Series Insights w wersji zapoznawczej składa się z następujących siedmiu elementów:

[![Przegląd programu Time Series Insights w wersji zapoznawczej](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Panel środowiska](#1-environment-panel): wyświetla wszystkie środowiska Azure Time Series Insights.
1. [Pasek nawigacyjny](#2-navigation-bar): umożliwia przełączenie między stronami **Analizowanie** i **modelem** .
1. [Drzewo hierarchii i panel wyszukiwania](#3-hierarchy-tree-and-search-panel): umożliwia wybranie i wyszukanie określonych elementów danych do grafu.
1. [Źródło szeregów czasowych](#4-time-series-well): pokazuje wszystkie aktualnie wybrane elementy danych.
1. [Panel wykresu](#5-chart-panel): wyświetla bieżący wykres roboczy.
1. [Oś czasu](#6-time-editor-panel): umożliwia modyfikowanie zakresu czasu pracy.
1. [Pasek aplikacji](#7-app-bar): zawiera opcje zarządzania użytkownikami (takie jak bieżąca dzierżawa) i umożliwia zmianę ustawień języka.


## <a name="1-environment-panel"></a>1. Panel środowiska

Panel środowiska zawiera wszystkie Time Series Insights środowiska, do których masz dostęp. Lista obejmuje środowiska z opcją płatność zgodnie z rzeczywistym użyciem (wersja zapoznawcza), a także środowiska S1/S2 (ogólna dostępność). Po prostu wybierz środowisko Time Series Insights, które ma być używane do natychmiastowego wykonania.

1. Wybierz strzałkę listy rozwijanej obok wyświetlanego środowiska.

   [![Panel środowiska](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Następnie wybierz odpowiednie środowisko.

## <a name="2-navigation-bar"></a>2. pasek nawigacyjny

  [![Pasek nawigacyjny](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Użyj paska nawigacyjnego, aby wybrać dwa widoki:

* **Analizuj**: Użyj go do grafowania i wykonywania bogatej analizy danych z szeregów czasowych w modelu lub w modelu.
* **Model**: Użyj go, aby wypchnąć nowe typy, hierarchie i wystąpienia podglądu Time Series Insights w modelu Time Series Insights.

### <a name="model-authoring"></a>Tworzenie modelu

Wersja zapoznawcza Azure Time Series Insights obsługuje operacje pełnego tworzenia, odczytu, aktualizacji i usuwania (CRUD) w modelu szeregów czasowych.

[![Panel wyszukiwania modelu](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Typ modelu szeregów czasowych**: można użyć typów Time Series Insights do definiowania zmiennych lub formuł do wykonywania obliczeń. Są one skojarzone z danym wystąpieniem Time Series Insights. Typ może mieć co najmniej jedną zmienną.
* **Hierarchia modelu szeregów czasowych**: hierarchie są systematycznie organizacjami danych. Hierarchie przedstawiają relacje między różnymi jednostkami w danych Time Series Insights.
* **Wystąpienie modelu szeregów czasowych**: wystąpienia są same dla szeregów czasowych. W większości przypadków są to **DeviceID** lub **AssetID**, który jest unikatowym identyfikatorem zasobu w środowisku.

Aby dowiedzieć się więcej na temat modelu szeregów czasowych, należy zapoznać się z modelami [serii godzin](./concepts-model-overview.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. drzewo hierarchii i panel wyszukiwania

Drzewo hierarchii i panel wyszukiwania umożliwiają łatwe wyszukiwanie i nawigowanie w hierarchii [modelu szeregów czasowych](./concepts-model-overview.md) , aby znaleźć określone wystąpienia szeregów czasowych, które mają być wyświetlane na wykresie. Po wybraniu wystąpień nie są one dodawane tylko do bieżącego wykresu, ale również są dodawane do obszaru dane. 

[![Drzewo hierarchii i panel wyszukiwania](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

Okienko wyników wyszukiwania umożliwia również wyświetlanie wyników w widoku hierarchii lub widoku listy, ułatwiając Znajdowanie wystąpień, które mają być wyświetlane.
 
## <a name="4-time-series-well"></a>4. okres szeregu czasowego

Wyświetla pola wystąpienia i inne metadane skojarzone z wybranymi wystąpieniami Time Series Insights. Zaznaczając pola wyboru po prawej stronie, można ukryć lub wyświetlić określone wystąpienia z bieżącego wykresu. 

  [![Źródło podglądu](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Można usunąć konkretne elementy danych z bieżących danych, zaznaczając formant Red **delete** (kosza) po lewej stronie elementu. Ponadto pozwala kontrolować sposób wyświetlania poszczególnych elementów na wykresie. Można wybrać opcję dodawania minimalnych i maksymalnych cieni, punktów danych, przesunąć element w czasie i wizualizować wystąpienie w sposób. 

Ponadto kontrolka eksploracji pozwala łatwo tworzyć zmiany czasu i wykresy punktowe.  

  [![Opcje układu dobrze](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Jeśli zostanie wyświetlony następujący komunikat, wystąpienie nie ma żadnych danych w wybranym okresie. Aby rozwiązać ten problem, należy zwiększyć przedział czasu lub potwierdzić, że wystąpienie jest wypychane dane.
>
> ![Brak powiadomienia o danych](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Panel wykresu

Wykres umożliwia wyświetlanie wystąpień szeregów czasowych jako wierszy. Możesz zwinąć panel środowiska, model danych i Panel sterowania przedział czasu, klikając formanty sieci Web, aby wykres był większy. 

  [![Przegląd wykresu podglądu](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Typ wykresu**: określa, które elementy danych są dostępne do wizualizacji.

1. **Rozmiar interwału**: Narzędzie suwaka Rozmiar interwału umożliwia powiększanie i wyświetlanie interwałów w tym samym przedziale czasu. Zapewnia to dokładniejszą kontrolę nad przenoszeniem między dużymi wycinkami czasu, które pokazują płynne trendy w dół do wycinków jako milisekundy, co pozwala na przeglądanie szczegółowych i wysokiej rozdzielczości kawałków danych. Domyślny punkt początkowy suwaka jest ustawiany jako najbardziej optymalny widok danych z wyboru. zrównoważenie rozdzielczości, szybkość zapytania i stopień szczegółowości.

1. **Powiększanie i kadrowanie**: zaznacz tę kontrolkę, aby powiększyć i przesunąć wykres.

1. **Kontrolka osi y**: przechodzenie między dostępnymi opcjami widoku osi y:

    * `Stacked`: Każdy wiersz ma pojedynczą oś Y.
    * `Overlap`: Służy do układania wielu linii na tej samej osi Y, przy czym dane osi Y zmieniają się w oparciu o wybrany wiersz.
    * `Shared`: Wszystkie dane osi Y są wyświetlane razem.

1. **Znacznik — element**: aktualnie wybrany element danych i jego skojarzone szczegóły.

Możesz kontynuować przechodzenie do określonego wycinka danych, **klikając lewym przyciskiem** myszy punkt danych na bieżącym grafie, trzymając mysz, a następnie przeciągając wybrany obszar do wybranego punktu końcowego. **Kliknij prawym przyciskiem myszy** niebieski, wybrany obszar, a następnie wybierz polecenie **powiększenie** , jak pokazano poniżej. Możesz również wyświetlić i pobrać zdarzenia telemetryczne z wybranego przedziału czasu.

  [![Podgląd powiększenia wykresu](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

Po wykonaniu akcji **powiększenie** zostanie wyświetlony wybrany zestaw danych. Wybierz kontrolkę format, aby przechodzić przez trzy reprezentacje osi y danych Time Series Insights.

  [![Podgląd osi y wykresu](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Oto przykład **nakładających się wykresów** :

  [![Nakładający się opcja wykresu](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

Przycisk **więcej akcji** rozwija się, aby wyświetlić opcję **Pobierz jako plik CSV**, **nawiązać połączenie z Power BI**, **pokazać dane wykresu jako tabelę**i zapoznać się z opcjami **nieprzetworzonych zdarzeń** .

  [![Opcja Więcej akcji](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

Dowiedz się więcej o opcji **Połącz z Power BI** w [Time Series Insights natywny łącznik Power BI](concepts-power-bi.md).

## <a name="6-time-editor-panel"></a>6. Panel edytora czasu

Podczas pracy z Time Series Insights najpierw wybierz przedział czasu. Wybrany przedział czasu będzie kontrolować zestaw danych, który jest dostępny do manipulowania za pomocą widżetów aktualizacji Time Series Insights.

  [![Panel wyboru czasu](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Część osi czasu jest wyróżniona w kolorze bursztynowym lub pomarańczowym, aby wskazać zakres danych dostępnych w sklepie ciepłym.

Poniższe kontrolki sieci Web są dostępne w ramach aktualizacji Time Series Insights do wybierania zakresu czasu pracy. 

  [![Dobrze sprawdzaj poszukiwanie](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Kontrolka suwaka wewnętrznego zakresu dat**: Użyj dwóch kontrolek punktu końcowego, przeciągając je w pożądanym okresie. Ten wewnętrzny zakres dat jest ograniczony przez kontrolkę suwaka zewnętrznego zakresu dat.

1. **Przyciski zwiększania i zmniejszania zakresu dat**: Zwiększ lub Zmniejsz zakres czasu, wybierając jeden z przycisków dla interwału, który chcesz.

1. **Kontrolka zwijania zakresu czasu**: ten formant sieci Web umożliwia ukrycie wszystkich kontrolek z wyjątkiem wewnętrznego narzędzia suwaka zakresu dat.

1. **Kontrolka suwaka zewnętrznego zakresu dat**: Użyj kontrolek punktu końcowego, aby wybrać zewnętrzny zakres dat, który będzie dostępny dla wewnętrznej kontroli zakresu dat.

1. **Kontrolka suwaka zakresu czasu**: Użyj jej, aby szybko przełączać się do wybranych opcji przedziału czasu, takich jak ostatnie **30 minut**, **ostatnie 12 godzin**lub **niestandardowy zakres**. Zmiana tej wartości powoduje także zmianę dostępnych zakresów interwału, które omówiono w narzędziu suwaka Rozmiar interwału.

   [![Do i z panelu wyboru](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. pasek aplikacji

Panel nawigacji Time Series Insights Preview pojawia się u góry aplikacji Time Series Insights. Zapewnia następujące funkcje:

### <a name="current-session-share-link-control"></a>Bieżąca kontrola linku do udziału sesji

  [![Ikona udostępniania](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Wybierz ikonę Nowy **udział** , aby udostępnić link adresu URL do zespołu.

  [![Udostępnianie adresu URL wystąpienia](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Sekcja dzierżawy

  [![Wybór dzierżawy](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Wyświetla bieżące informacje o koncie logowania Time Series Insights.
* Użyj go, aby przełączać się między dostępnymi Time Series Insightsmi motywami.
* Służy do wyświetlania [demonstracyjnej aplikacji sieci Web](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Wybór motywu

Aby wybrać nowy motyw, wybierz ikonę profilu znajdującą się w prawym górnym rogu. Następnie wybierz pozycję **Zmień motyw**.

  [![Wybór motywu](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> Wybór języka jest również dostępny przez wybranie ikony profilu.

Wersja zapoznawcza Azure Time Series Insights obsługuje dwa motywy:

* **Motyw jasny**: motyw domyślny pokazywany w tym dokumencie.
* **Ciemny motyw**: renderuje Eksploratora, jak pokazano poniżej:

  [![Wybrany motyw ciemny](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>Kontrolki środowiska S1/S2

### <a name="preview-terms-panel"></a>Panel warunków wersji zapoznawczej

Ta sekcja ma zastosowanie tylko do istniejących środowisk S1/S2, które próbują użyć Eksploratora w zaktualizowanym interfejsie użytkownika. Możesz chcieć korzystać z ogólnie dostępnego produktu i wersji zapoznawczej. Dodaliśmy pewne funkcje z istniejącego interfejsu użytkownika do zaktualizowanego Eksploratora, ale można uzyskać pełne środowisko interfejsu użytkownika dla środowiska S1/S2 w istniejącym Eksploratorze Time Series Insights. 

Zamiast hierarchii jest wyświetlany panel warunków Time Series Insights. Panel warunki pozwala definiować zapytania w danym środowisku. Umożliwia ona również filtrowanie danych na podstawie predykatu.

  [![Miejsce, w którym panel zapytań](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

Panel Edytor warunków podglądu Time Series Insights przyjmuje następujące parametry:

**Gdzie**: Użyj klauzuli WHERE, aby szybko filtrować zdarzenia przy użyciu zestawu argumentów zamieszczonych w poniższej tabeli. W przypadku przeprowadzania wyszukiwania przez wybranie operandu predykat jest automatycznie aktualizowany na podstawie tego wyszukiwania. Obsługiwane są następujące typy operandów:

| Operacja | Obsługiwane typy   | Uwagi |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Ciąg, bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Ciąg, bool, Double, DateTime, TimeSpan, NULL | Wszystkie operandy powinny być tego samego typu lub być stałe o wartości NULL. |
| `HAS` | String | Tylko stałe literały ciągu są dozwolone po prawej stronie. Pusty ciąg i wartość NULL są niedozwolone. |

Aby dowiedzieć się więcej o obsługiwanych operacjach zapytań i typach danych, zobacz [wyrażenie szeregów czasowych (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

### <a name="examples-of-where-clauses"></a>Przykłady klauzul WHERE

  [![Przykłady klauzuli WHERE](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Miara**: Lista rozwijana, która wyświetla wszystkie kolumny liczbowe (**podwaja**), których można użyć jako elementów dla bieżącego wykresu.

**Podział według**: Ta lista rozwijana zawiera wszystkie dostępne kolumny kategorii (ciągi) w modelu, według których można grupować dane. Można dodać maksymalnie pięć wyrazów, aby wyświetlić je na tej samej osi x. Wprowadź wymagane parametry, a następnie wybierz pozycję **Dodaj** , aby dodać nowy termin.

  [![Zapytania i widok filtrowany](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

Możesz pokazać i ukryć elementy w panelu wykresu, wybierając ikonę widoczne, jak pokazano na poniższej ilustracji. Aby całkowicie usunąć zapytania, wybierz czerwony znak **X**.

  [![Anulowanie zapytania i opcji filtrowania](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat pozyskiwania [danych](./concepts-ingestion-overview.md) w środowisku.

- Zapoznaj się z artykułem dotyczącym [magazynu](concepts-storage.md)

- Przeczytaj dokument Time Series Insights w wersji zapoznawczej dotyczące [modelowania danych](./concepts-model-overview.md).

- Dowiedz się [, jak diagnozować i rozwiązywać problemy](./time-series-insights-update-how-to-troubleshoot.md) z wystąpieniem Time Series Insights.
