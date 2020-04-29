---
title: Twórz interaktywne raporty przy użyciu skoroszytów Azure Monitor | Dokumentacja firmy Microsoft
description: Uprość złożone raportowanie za pomocą wstępnie skompilowanych i niestandardowych skoroszytów z parametrami
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77671005"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure Monitor

Skoroszyty łączą tekst, [kwerendy analityczne](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), metryki platformy Azure i parametry w rozbudowanych raportach interaktywnych. Skoroszyty mogą być edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Skoroszyty są przydatne w scenariuszach takich jak:

* Zbadaj użycie aplikacji, jeśli nie znasz już interesujących Cię metryk: liczbie użytkowników, stawek za przechowywanie, stawek za konwersję itp. W przeciwieństwie do innych narzędzi analitycznych użycia, skoroszyty umożliwiają łączenie wielu rodzajów wizualizacji i analiz, dzięki czemu są wspaniałe dla tego rodzaju eksploracji o dowolnej postaci.
* Objaśnienie do zespołu sposobu wykonywania nowo wydanej funkcji, pokazując liczbę użytkowników dla interakcji z kluczami i innymi metrykami.
* Udostępnianie wyników eksperymentu A/B w aplikacji innym członkom zespołu. Można wyjaśnić cele eksperymentu z tekstem, a następnie pokazać wszystkie metryki użycia i zapytania analityczne używane do szacowania eksperymentu, a także wyraźne wywołania dla tego, czy każda Metryka była większa niż wartość docelowa.
* Raportowanie wpływu awarii na korzystanie z aplikacji, łączenie danych, wyjaśnienie tekstu i Omówienie następnych kroków w celu zapobieżenia awarii w przyszłości.

## <a name="starting-with-a-template-or-saved-workbook"></a>Rozpoczynanie pracy od szablonu lub zapisanego skoroszytu

Skoroszyt składa się z sekcji składających się niezależnie od edytowalnych wykresów, tabel, tekstu i kontrolek wejściowych. Aby lepiej zrozumieć skoroszyty, najlepiej otworzyć jeden z nich. 

Wybierz pozycję **skoroszyty** z menu po lewej stronie w obszarze Application Insights środowiska aplikacji.

![Zrzut ekranu przedstawiający nawigację do skoroszytów](./media/usage-workbooks/001-workbooks.png)

Spowoduje to uruchomienie galerii skoroszytów z liczbą wstępnie skompilowanych skoroszytów, które pomogą Ci rozpocząć pracę.

![Zrzut ekranu przedstawiający galerię skoroszytów](./media/usage-workbooks/002-workbook-gallery.png)

Zaczniemy od **szablonu domyślnego**, który znajduje się w nagłówku **szybkiego startu**.

![Zrzut ekranu przedstawiający galerię skoroszytów](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Edytowanie, ponowne rozmieszczanie, klonowanie i usuwanie sekcji skoroszytu

Skoroszyty mają dwa tryby: **tryb edycji**i **tryb odczytywania**. Gdy domyślny skoroszyt zostanie uruchomiony po raz pierwszy, zostanie otwarty w **trybie edycji**. Spowoduje to wyświetlenie całej zawartości skoroszytu, w tym wszystkich kroków i parametrów, które są w inny sposób ukryte. **Tryb odczytu** przedstawia uproszczony widok stylu raportu. Dzięki temu można uzyskać streszczenie złożoności, która pomogła utworzyć raport przy zachowaniu zasadniczej Mechanics tylko kilku kliknięć, gdy jest to potrzebne do modyfikacji.

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/editing-controls-new.png)

1. Po zakończeniu edycji sekcji kliknij pozycję **Zakończono edycję** w lewym dolnym rogu sekcji.

2. Aby utworzyć duplikat sekcji, kliknij ikonę **klonowania tej sekcji** . Tworzenie zduplikowanych sekcji to doskonały sposób na iterację zapytania bez utraty poprzednich iteracji.

3. Aby przenieść sekcję w górę w skoroszycie, kliknij ikonę **Przenieś w górę** lub **Przenieś w dół** .

4. Aby trwale usunąć sekcję, kliknij ikonę **Usuń** .

## <a name="adding-text-and-markdown-sections"></a>Dodawanie sekcji tekstu i znaczników

Dodawanie nagłówków, objaśnień i komentarzy do skoroszytów ułatwia przekształcenie zestawu tabel i wykresów w narrację. Sekcje tekstowe w skoroszytach obsługują [składnię promocji](https://daringfireball.net/projects/markdown/) na potrzeby formatowania tekstu, takie jak nagłówki, pogrubienie, kursywa i listy punktowane.

Aby dodać sekcję tekstową do skoroszytu, użyj przycisku **Dodaj tekst** w dolnej części skoroszytu lub w dolnej części każdej sekcji.

## <a name="adding-query-sections"></a>Dodawanie sekcji zapytań

![Sekcja zapytania w skoroszytach](./media/usage-workbooks/analytics-section-new.png)

Aby dodać sekcję zapytania do skoroszytu, użyj przycisku **Dodaj zapytanie** w dolnej części skoroszytu lub w dolnej części każdej sekcji.

Sekcje zapytań są wysoce elastyczne i mogą być używane do udzielania odpowiedzi na pytania, takie jak:

* Ile wyjątków zostało zgłoszonych przez lokację w tym samym okresie jako odrzucanie w użyciu?
* Jaki był rozkład czasów ładowania stron dla użytkowników przeglądających stronę?
* Ilu użytkowników jest wyświetlanych w witrynie, ale nie na innych zestawach stron? Może to być przydatne w przypadku klastrów użytkowników, którzy korzystają z różnych podzestawów funkcjonalności witryny (Użyj `join` operatora z `kind=leftanti` modyfikatorem w [języku zapytania Kusto](/azure/kusto/query/)).

Nie można również wykonywać zapytania tylko w kontekście aplikacji, z której został uruchomiony skoroszyt. Możesz wykonywać zapytania dla wielu Application Insights monitorowanych aplikacji, a także Log Analytics obszarów roboczych, o ile masz uprawnienia dostępu do tych zasobów.

Aby wykonać zapytanie dotyczące dodatkowych zewnętrznych zasobów Application Insights, użyj identyfikatora **aplikacji** .

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

To zapytanie łączy żądania z trzech różnych aplikacji. Aplikacja o nazwie app01, aplikacja o nazwie app02 i żądania z lokalnego zasobu Application Insights.

Aby ściągnąć dane z zewnętrznego obszaru roboczego Log Analytics, użyj identyfikatora **obszaru roboczego** .

Aby dowiedzieć się więcej o zapytaniach między zasobami, zapoznaj się z [oficjalnymi wskazówkami](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Zaawansowane ustawienia zapytania analitycznego

Każda sekcja ma własne ustawienia zaawansowane, które są dostępne za pośrednictwem ikony ![ustawień Application Insights skoroszyty edycji sekcji](./media/usage-workbooks/005-settings.png) znajdujące się po prawej stronie przycisku **Dodaj parametry** .

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Szerokość niestandardowa**    | Ustaw tę opcję, aby utworzyć element jako dowolny rozmiar, więc można dopasować wiele elementów w jednym wierszu, co pozwala lepiej organizować wykresy i tabele w Zaawansowane raporty interaktywne.  |
   | **Warunkowo widoczne** | Służy do ukrywania kroków w oparciu o parametr w trybie do czytania. |
   | **Eksportowanie parametru**| Dzięki temu w wybranym wierszu siatki lub wykresu mogą wystąpić dalsze kroki zmiany wartości lub staną się widoczne.  |
   | **Pokaż zapytanie, gdy nie jest edytowane** | Spowoduje to wyświetlenie zapytania powyżej wykresu lub tabeli, nawet w trybie do czytania.
   | **Pokaż przycisk Otwórz w analizie, gdy nie jest edytowany** | Spowoduje to dodanie ikony Blue Analytics do prawego rogu wykresu, aby zezwolić na dostęp za pomocą jednego kliknięcia.|

Większość z tych ustawień jest dość intuicyjna, ale w celu zrozumienia **eksportu parametru** lepiej jest sprawdzić skoroszyt, który korzysta z tej funkcji.

Jeden z wstępnie skompilowanych skoroszytów zawiera informacje dotyczące aktywnych użytkowników.

Pierwsza sekcja skoroszytu opiera się na danych zapytania analitycznego:

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/003-active-users.png)

Druga sekcja jest również oparta na danych zapytania analitycznego, ale wybranie wiersza w pierwszej tabeli spowoduje interaktywną aktualizację zawartości wykresu:

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/004-active-users-trend.png)

 Jest to możliwe poprzez użycie **elementu, gdy element jest zaznaczony, wyeksportuj** zaawansowane ustawienia parametrów, które są włączone w kwerendzie analizy tabeli.

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/007-settings-export.png)

Drugie zapytanie analityczne następnie wykorzystuje eksportowane wartości, gdy wiersz jest zaznaczony. Jeśli nie wybrano żadnego wiersza, domyślnie jest to wiersz reprezentujący wartości całkowite. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Dodawanie sekcji metryk

Sekcje metryk zapewniają pełny dostęp do dołączania danych metryk Azure Monitor do raportów interaktywnych. Wiele wstępnie skompilowanych skoroszytów będzie zawierać zarówno dane zapytania analitycznego, jak i dane metryk, dzięki czemu można w pełni wykorzystać zalety obu funkcji w jednym miejscu. Istnieje również możliwość ściągania danych metryk z zasobów we wszystkich subskrypcjach, do których masz dostęp.

Poniżej znajduje się przykład danych maszyny wirtualnej, które są ściągane do skoroszytu w celu zapewnienia wizualizacji wydajności procesora CPU:

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Dodawanie sekcji parametrów

Parametry skoroszytu umożliwiają zmianę wartości w skoroszycie bez konieczności ręcznej edycji sekcji zapytania lub tekstu.  Eliminuje to konieczność zrozumienia bazowego języka zapytań analitycznych i znacząco rozszerza potencjalną grupę raportów opartych na skoroszycie.

Wartości parametrów są zamieniane w kwerendy, tekst lub inne sekcje parametrów przez umieszczenie nazwy parametru w nawiasach klamrowych, na przykład ``{parameterName}``.  Nazwy parametrów są ograniczone do podobnych reguł, takich jak identyfikatory JavaScript, podstawowe znaki alfabetyczne lub podkreślenia, po których następuje znaki alfanumeryczne lub podkreślenia. Na przykład **a1** jest dozwolony, ale **1a** nie jest dozwolone.

Parametry są liniowe, zaczynając od góry skoroszytu i przepływają w dół do kolejnych kroków.  Parametry zadeklarowane później w skoroszycie mogą przesłonić te, które zostały zadeklarowane w dalszej części.  Umożliwia to również parametry, które używają zapytań w celu uzyskania dostępu do wartości z określonych parametrów.  W samym kroku parametru parametry są również liniowe, od lewej do prawej, gdzie parametry po prawej stronie mogą zależeć od parametru zadeklarowanego wcześniej w tym samym kroku.
 
Istnieją cztery różne typy parametrów, które są obecnie obsługiwane:

  |         |          |
   | ---------------- |:-----|
   | **Tekst**    | użytkownik edytuje pole tekstowe i opcjonalnie można podać zapytanie w celu wypełnienia wartości domyślnej. |
   | **Lista rozwijana** | Użytkownik zdecyduje się na podstawie zestawu wartości. |
   | **Selektor zakresu czasu**| Użytkownik zdecyduje się na podstawie wstępnie zdefiniowanego zestawu wartości zakresu czasu lub wybierać z niestandardowego zakresu czasu.|
   | **Selektor zasobów** | Użytkownik zdecyduje się wybrać spośród zasobów wybranych dla skoroszytu.|

### <a name="using-a-text-parameter"></a>Używanie parametru tekstowego

Wartość typu użytkownika w polu tekstowym jest zastępowana bezpośrednio w zapytaniu bez ucieczki ani tworzenia. Jeśli potrzebna wartość jest ciągiem, zapytanie powinno zawierać cudzysłowy wokół parametru (na przykład **"{parameter}"**).

Pozwala to na użycie wartości w polu tekstowym w dowolnym miejscu. Może to być nazwa tabeli, nazwa kolumny, nazwa funkcji, operator itp.

Typ parametru tekstowego ma ustawienie **Pobierz wartość domyślną z zapytania analizy**, co umożliwia autorowi skoroszytu użycie zapytania w celu wypełnienia wartości domyślnej dla tego pola tekstowego.

W przypadku używania wartości domyślnej z kwerendy analitycznej jako wartości domyślnej jest używana tylko pierwsza wartość pierwszego wiersza (wiersz 0, kolumna 0). W związku z tym zaleca się ograniczenie zapytania, aby zwracał tylko jeden wiersz i jedną kolumnę. Wszystkie inne dane zwrócone przez zapytanie są ignorowane. 

Niezależnie od wartości zwracanej przez zapytanie zostaną zamienione bezpośrednio bez ucieczki ani nie. Jeśli zapytanie nie zwraca żadnych wierszy, wynik parametru jest ciągiem pustym (Jeśli parametr nie jest wymagany) lub niezdefiniowany (Jeśli parametr jest wymagany).

### <a name="using-a-dropdown"></a>Korzystanie z listy rozwijanej

Typ parametru listy rozwijanej pozwala utworzyć kontrolkę listy rozwijanej, umożliwiając wybór jednej lub wielu wartości.

Lista rozwijana jest wypełniana przez zapytanie analityczne. Jeśli zapytanie zwraca jedną kolumnę, wartości w tej kolumnie są zarówno **wartością** , jak i **etykietą** w kontrolce listy rozwijanej. Jeśli zapytanie zwraca dwie kolumny, pierwsza kolumna jest **wartością**, a druga kolumna to **etykieta** wyświetlana na liście rozwijanej.  Jeśli zapytanie zwraca trzy kolumny, trzecia kolumna służy do wskazania domyślnego wyboru na liście rozwijanej.  Ta kolumna może być dowolnego typu, ale najprostszą jest użycie typów bool lub numeric, gdzie 0 to false, a 1 to wartość true.

 Jeśli kolumna jest typu String, ciąg o wartości null/pusty jest uznawany za fałszywy, a każda inna wartość jest uznawana za wartość true. W przypadku list rozwijanych z pojedynczym wyborem jako domyślny wybór jest używana pierwsza wartość o wartości true.  W przypadku listy rozwijanej z wieloma wyboru wszystkie wartości o wartości true są używane jako domyślny wybrany zestaw. Elementy na liście rozwijanej są wyświetlane w dowolnej kolejności, w której zapytanie zwróciło wiersze. 

Przyjrzyjmy się parametrom znajdującym się w raporcie aktywni użytkownicy. Kliknij przycisk Edytuj symbol obok pozycji **TimeRange**.

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/009-time-range.png)

Spowoduje to uruchomienie elementu menu Edytuj parametr:

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/010-time-range-edit.png)

Zapytanie używa funkcji języka zapytań analitycznych o nazwie **DataTable** , która pozwala wygenerować arbitralną tabelę, pełną zawartość z wąskim powietrzem. Na przykład następujące zapytanie analityczne:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Generuje wynik:

![Kontrolki edycji sekcji Application Insights skoroszytów](./media/usage-workbooks/011-data-table.png)

Bardziej zastosowanym przykładem jest użycie listy rozwijanej w celu wybrania z zestawu krajów/regionów według nazwy:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

Wyniki zapytania będą wyświetlane w następujący sposób:

![Lista rozwijana kraju](./media/usage-workbooks/012-country-dropdown.png)

Listy rozwijane to niezwykle zaawansowane narzędzia do dostosowywania i tworzenia interaktywnych raportów.

### <a name="time-range-parameters"></a>Parametry zakresu czasu

Chociaż można utworzyć własny niestandardowy parametr zakresu czasu za pośrednictwem typu parametru listy rozwijanej, można również użyć typu parametru zakres czasu, jeśli nie jest potrzebny ten sam stopień elastyczności. 

Typy parametrów zakresu czasu mają 15 zakresów domyślnych, które przechodzą z pięciu minut do ostatnich 90 dni. Dostępna jest również opcja zezwalająca na wybór niestandardowego zakresu czasu, co umożliwia operatorowi raportu wybranie jawnych wartości początkowych i końcowych dla zakresu czasu.

### <a name="resource-picker"></a>Selektor zasobów

Typ parametru selektora zasobów umożliwia określanie zakresu raportu do określonych typów zasobów. Przykładem prekompilowanego skoroszytu, który korzysta z typu selektora zasobów, jest skoroszyt o **niepowodzeniach szczegółowych** informacji.

![Lista rozwijana kraju](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie skoroszytów i udostępnianie ich zespołowi

Skoroszyty są zapisywane w ramach zasobu Application Insights, w sekcji **Moje raporty** , które są prywatne dla Ciebie lub w sekcji **raporty udostępnione** dostępne dla wszystkich użytkowników mających dostęp do zasobów Application Insights. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij przycisk **Otwórz** na pasku akcji.

Aby udostępnić skoroszyt, który jest obecnie w **Moje raporty**:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." przycisk obok skoroszytu, który chcesz udostępnić
3. Kliknij przycisk **Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt za pomocą linku lub poczty e-mail, kliknij pozycję **Udostępnij** na pasku akcji. Należy pamiętać, że adresaci linku potrzebują dostępu do tego zasobu w Azure Portal, aby wyświetlić skoroszyt. Aby wprowadzić zmiany, adresaci muszą mieć co najmniej uprawnienia współautora dla zasobu.

Aby przypiąć link do skoroszytu na pulpicie nawigacyjnym platformy Azure:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." przycisk obok skoroszytu, który chcesz przypiąć
3. Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

## <a name="contributing-workbook-templates"></a>Współtworzenie szablonów skoroszytów

Czy utworzono szablon skoroszytu awesome i chcesz go udostępnić społeczności? Aby dowiedzieć się więcej, odwiedź nasze [repozytorium GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Następne kroki
- Aby włączyć środowiska użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetleń stron](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli masz już wysłane zdarzenia niestandardowe lub widoki stron, zapoznaj się z narzędziami użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Użytkownicy, sesje, zdarzenia](../../azure-monitor/app/usage-segmentation.md)
    - [Lejki](../../azure-monitor/app/usage-funnels.md)
    - [Przechowywanie](../../azure-monitor/app/usage-retention.md)
    - [Przepływy użytkowników](../../azure-monitor/app/usage-flows.md)
    - [Dodawanie kontekstu użytkownika](../../azure-monitor/app/usage-send-user-context.md)
