---
title: Twórz interaktywne raporty usługi VM Insights ze skoroszytami
description: Uprość złożone raporty ze wstępnie zdefiniowanymi i niestandardowymi skoroszytami dla szczegółowych informacji o maszynach wirtualnych.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 695aa760d554946883f72692f0bed1babad933b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568804"
---
# <a name="create-interactive-reports-vm-insights-with-workbooks"></a>Twórz interaktywne raporty usługi VM Insights ze skoroszytami

Skoroszyty łączą tekst, [kwerendy dzienników](/azure/data-explorer/kusto/query/), metryki i parametry w rozbudowanych raportach interaktywnych. Skoroszyty mogą być edytowane przez innych członków zespołu, którzy mają dostęp do tych samych zasobów platformy Azure.

Skoroszyty są przydatne w scenariuszach takich jak:

* Zbadaj użycie maszyny wirtualnej, gdy nie wiesz, jakie są interesujące Cię metryki: użycie procesora CPU, miejsce na dysku, pamięć, zależności sieci itp. W przeciwieństwie do innych narzędzi analitycznych użycia, skoroszyty umożliwiają łączenie wielu rodzajów wizualizacji i analiz, dzięki czemu są wspaniałe dla tego rodzaju eksploracji o dowolnej postaci.
* Objaśnienie Twojego zespołu, jak ostatnio obsługiwana maszyna wirtualna jest wykonywana, pokazując metryki dla liczników kluczy i innych zdarzeń dzienników.
* Udostępnianie wyników eksperymentu zmiany rozmiarów maszyny wirtualnej z innymi członkami zespołu. Można wyjaśnić cele eksperymentu z tekstem, a następnie pokazać wszystkie metryki użycia i zapytania analityczne używane do szacowania eksperymentu, a także wyraźne wywołania dla tego, czy każda Metryka była większa niż wartość docelowa.
* Raportowanie wpływu awarii na korzystanie z maszyny wirtualnej, łączenie danych, wyjaśnienie tekstu i Omówienie następnych kroków, aby zapobiec awarii w przyszłości.

Poniższa tabela zawiera podsumowanie skoroszytów uwzględnionych w usłudze VM Insights, aby rozpocząć pracę.

| skoroszyt | Opis | Zakres |
|----------|-------------|-------|
| Wydajność | Udostępnia dostosowywalną wersję naszego górnego widoku listy i wykresów w jednym skoroszycie, który korzysta ze wszystkich Log Analytics włączonych liczników wydajności.| Wiele maszyn wirtualnych |
| Liczniki wydajności | Górny widok wykresu N dla szerokiego zestawu liczników wydajności. | Wiele maszyn wirtualnych |
| Połączenia | Połączenia zapewniają szczegółowy widok połączeń przychodzących i wychodzących z monitorowanych maszyn wirtualnych. | Wiele maszyn wirtualnych |
| Aktywne porty | Zawiera listę procesów, które zostały powiązane z portami na monitorowanych maszynach wirtualnych i ich działania w wybranym przedziale czasu. | Wiele maszyn wirtualnych |
| Otwarte porty | Zapewnia liczbę portów otwartych na monitorowanych maszynach wirtualnych oraz szczegóły dotyczące tych otwartych portów. | Wiele maszyn wirtualnych |
| Połączenia zakończone niepowodzeniem | Wyświetl liczbę nieudanych połączeń na monitorowanych maszynach wirtualnych, Trend awarii i, jeśli wartość procentowa niepowodzeń rośnie wraz z upływem czasu. | Wiele maszyn wirtualnych |
| Security and Audit | Analiza ruchu TCP/IP, która jest raportem dotyczącym ogólnych połączeń, złośliwych połączeń, gdzie punkty końcowe IP znajdują się globalnie.  Aby włączyć wszystkie funkcje, należy włączyć wykrywanie zabezpieczeń. | Wiele maszyn wirtualnych |
| Ruch TCP | Uporządkowany raport dotyczący monitorowanych maszyn wirtualnych oraz wysyłany, odbierany i łączny ruch sieciowy w siatce i wyświetlany jako linia trendu. | Wiele maszyn wirtualnych |
| Porównanie ruchu | Te skoroszyty umożliwiają porównanie trendów ruchu sieciowego dla jednej maszyny lub grupy maszyn. | Wiele maszyn wirtualnych |
| Wydajność | Udostępnia dostosowywalną wersję widoku wydajności, który wykorzystuje wszystkie Log Analytics liczniki wydajności, które zostały włączone. | Pojedyncza maszyna wirtualna | 
| Połączenia | Połączenia zapewniają szczegółowy widok połączeń przychodzących i wychodzących z maszyny wirtualnej. | Pojedyncza maszyna wirtualna |
 
## <a name="creating-a-new-workbook"></a>Tworzenie nowego skoroszytu

Skoroszyt składa się z sekcji składających się niezależnie od edytowalnych wykresów, tabel, tekstu i kontrolek wejściowych. Aby lepiej zrozumieć skoroszyty, Zacznijmy od otwarcia szablonu i przeprowadzisz przez proces tworzenia niestandardowego skoroszytu. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **Virtual Machines**.

3. Z listy wybierz maszynę wirtualną.

4. Na stronie maszyna wirtualna w sekcji **monitorowanie** wybierz pozycję **szczegółowe informacje**.

5. Na stronie usługa VM Insights wybierz pozycję **wydajność** lub **mapy** , a następnie wybierz pozycję **Wyświetl skoroszyty** z linku na stronie. Z listy rozwijanej wybierz pozycję **Przejdź do galerii**.

    ![Zrzut ekranu przedstawiający listę rozwijaną skoroszytu](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Spowoduje to uruchomienie galerii skoroszytów z użyciem kilku wstępnie skompilowanych skoroszytów, które pomogą Ci rozpocząć pracę.

7. Utwórz nowy skoroszyt, wybierając pozycję **Nowy**.

    ![Zrzut ekranu przedstawiający galerię skoroszytów](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Edytowanie sekcji skoroszytu

Skoroszyty mają dwa tryby: **tryb edycji** i **tryb odczytywania**. Po pierwszym uruchomieniu nowego skoroszytu zostanie on otwarty w **trybie edycji**. Pokazuje całą zawartość skoroszytu, w tym wszelkie kroki i parametry, które są w inny sposób ukryte. **Tryb odczytu** przedstawia uproszczony widok stylu raportu. Tryb odczytywania pozwala na abstrakcję złożoności, która pomogła utworzyć raport przy zachowaniu zasadniczej Mechanics tylko kilku kliknięć, gdy jest to potrzebne do modyfikacji.

![Zrzut ekranu przedstawiający sekcję Virtual Machines skoroszyt w Azure Monitor pokazujący nowy skoroszyt w trybie edycji z wyróżnionymi kontrolkami edycji.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Po zakończeniu edycji sekcji kliknij pozycję **Zakończono edycję** w lewym dolnym rogu sekcji.

2. Aby utworzyć duplikat sekcji, kliknij ikonę **klonowania tej sekcji** . Tworzenie zduplikowanych sekcji to doskonały sposób na iterację zapytania bez utraty poprzednich iteracji.

3. Aby przenieść sekcję w górę w skoroszycie, kliknij ikonę **Przenieś w górę** lub **Przenieś w dół** .

4. Aby trwale usunąć sekcję, kliknij ikonę **Usuń** .

## <a name="adding-text-and-markdown-sections"></a>Dodawanie sekcji tekstu i znaczników

Dodawanie nagłówków, objaśnień i komentarzy do skoroszytów ułatwia przekształcenie zestawu tabel i wykresów w narrację. Sekcje tekstowe w skoroszytach obsługują [składnię promocji](https://daringfireball.net/projects/markdown/) na potrzeby formatowania tekstu, takie jak nagłówki, pogrubienie, kursywa i listy punktowane.

Aby dodać sekcję tekstową do skoroszytu, użyj przycisku **Dodaj tekst** w dolnej części skoroszytu lub w dolnej części każdej sekcji.

## <a name="adding-query-sections"></a>Dodawanie sekcji zapytań

![Sekcja zapytania w skoroszytach](media/vminsights-workbooks/005-workbook-query-section.png)

Aby dodać sekcję zapytania do skoroszytu, użyj przycisku **Dodaj zapytanie** w dolnej części skoroszytu lub w dolnej części każdej sekcji.

Sekcje zapytań są wysoce elastyczne i mogą być używane do udzielania odpowiedzi na pytania, takie jak:

* Jak wykorzystanie procesora CPU w tym samym czasie zwiększa ruch sieciowy?
* Jaki był trend w ilości dostępnego miejsca na dysku w ciągu ostatniego miesiąca?
* Ile błędów połączenia sieciowego była moje środowisko maszyny wirtualnej w ciągu ostatnich dwóch tygodni? 

Nie można również wykonywać zapytania tylko w kontekście maszyny wirtualnej, z której został uruchomiony skoroszyt. Możesz wykonywać zapytania na wielu maszynach wirtualnych, a także Log Analytics obszary robocze, o ile masz uprawnienia dostępu do tych zasobów.

Aby dołączyć dane z innych obszarów roboczych Log Analytics lub z określonej aplikacji Application Insights przy użyciu identyfikatora **obszaru roboczego** . Aby dowiedzieć się więcej o zapytaniach między zasobami, zapoznaj się z [oficjalnymi wskazówkami](../logs/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Zaawansowane ustawienia zapytania analitycznego

Każda sekcja ma własne ustawienia zaawansowane, które są dostępne w ![ sekcji skoroszyty ustawień edytowanie ikon kontrolek ](media/vminsights-workbooks/006-settings.png) znajdujących się po prawej stronie przycisku **Dodaj parametry** .

![Zrzut ekranu okna dialogowego Ustawienia zaawansowane w Virtual Machines skoroszycie Azure Monitor. Zostanie wyróżniona ikona otwierająca okno dialogowe.](media/vminsights-workbooks/007-settings-expanded.png)

| Ustawienie | Opis |
| ---------------- |:-----|
| Szerokość niestandardowa    | Sprawia, że element jest dowolnego rozmiaru, dzięki czemu można dopasować wiele elementów w jednym wierszu, co pozwala lepiej organizować wykresy i tabele w Zaawansowane raporty interaktywne.  |
| Warunkowo widoczne | Określ, aby ukryć kroki na podstawie parametru w trybie odczytu. |
| Eksportowanie parametru| Zezwól na wybranie wiersza w siatce lub wykresie, aby spowodować późniejsze kroki zmiany wartości lub staną się widoczne.  |
| Pokaż zapytanie, gdy nie jest edytowane | Wyświetla zapytanie powyżej wykresu lub tabeli, nawet w trybie do czytania.
| Pokaż przycisk Otwórz w analizie, gdy nie jest edytowany | Dodaje ikonę Blue Analytics do prawego rogu wykresu, aby zezwolić na dostęp za pomocą jednego kliknięcia.|

Większość z tych ustawień jest dość intuicyjna, ale w celu zrozumienia **eksportu parametru** lepiej jest sprawdzić skoroszyt, który korzysta z tej funkcji.

Jeden z wbudowanych skoroszytów — **ruch TCP**, zawiera informacje dotyczące metryk połączenia z maszyny wirtualnej.

Pierwsza sekcja skoroszytu jest oparta na danych zapytania dziennika. Druga sekcja jest również oparta na danych zapytania dziennika, ale wybranie wiersza w pierwszej tabeli spowoduje interaktywną aktualizację zawartości wykresów:

![Zrzut ekranu przedstawiający sekcję Virtual Machines w Azure Monitor pokazujący ruch TCP prekompilowanego skoroszytu.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Zachowanie jest możliwe w **przypadku wybrania elementu, eksportując** zaawansowane ustawienia parametru, które są włączone w zapytaniu dziennika tabeli.

![Zrzut ekranu okna dialogowego Ustawienia zaawansowane dla skoroszytu Virtual Machines z zaznaczeniem opcji "gdy element jest zaznaczony, Eksportuj parametr".](media/vminsights-workbooks/009-settings-export.png)

Drugie zapytanie dziennika wykorzystuje eksportowane wartości, gdy wiersz jest wybrany do utworzenia zestawu wartości, które są następnie używane przez nagłówek i wykresy sekcji. Jeśli nie wybrano żadnego wiersza, spowoduje to ukrycie nagłówka i wykresów sekcji. 

Na przykład ukryty parametr w drugiej sekcji używa następującego odwołania z wiersza zaznaczonego w siatce:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Dodawanie sekcji metryk

Sekcje metryk zapewniają pełny dostęp do dołączania danych metryk Azure Monitor do raportów interaktywnych. W przypadku usługi VM Insights wstępnie skompilowane skoroszyty zwykle zawierają dane analityczne zapytań, a nie dane metryk.  Można utworzyć skoroszyty z danymi metryk, co pozwala w pełni wykorzystać zalety obu funkcji w jednym miejscu. Istnieje również możliwość ściągania danych metryk z zasobów we wszystkich subskrypcjach, do których masz dostęp.

Poniżej znajduje się przykład danych maszyny wirtualnej, które są ściągane do skoroszytu w celu zapewnienia wizualizacji wydajności procesora CPU:

![Zrzut ekranu przedstawiający sekcję metryki skoroszytu maszyny wirtualnej w Azure Monitor. Wydajność procesora CPU dla każdej maszyny wirtualnej jest pokazywana graficznie.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Dodawanie sekcji parametrów

Parametry skoroszytu umożliwiają zmianę wartości w skoroszycie bez konieczności ręcznej edycji sekcji zapytania lub tekstu. Eliminuje to konieczność zrozumienia bazowego języka zapytań analitycznych i znacząco rozszerza potencjalną grupę raportów opartych na skoroszycie.

Wartości parametrów są zamieniane w kwerendy, tekst lub inne sekcje parametrów przez umieszczenie nazwy parametru w nawiasach klamrowych, na przykład ``{parameterName}`` . Nazwy parametrów są ograniczone do podobnych reguł, takich jak identyfikatory JavaScript, znaki alfabetu lub podkreślenia, po których następuje znaki alfanumeryczne lub podkreślenia. Na przykład **a1** jest dozwolony, ale **1a** nie jest dozwolone.

Parametry są liniowe, zaczynając od góry skoroszytu i przepływają w dół do kolejnych kroków.  Parametry zadeklarowane w dalszej części skoroszytu mogą przesłaniać wcześniej zadeklarowane parametry. Umożliwia to również parametry, które używają zapytań do uzyskiwania dostępu do wartości z parametrów zdefiniowanych wcześniej. W samym kroku parametru parametry są również liniowe, od lewej do prawej, gdzie parametry po prawej stronie mogą zależeć od parametru zadeklarowanego wcześniej w tym samym kroku.
 
Istnieją cztery różne typy parametrów, które są obecnie obsługiwane:

| Parametr | Opis |
| ---------------- |:-----|
| Tekst    | Zezwala użytkownikowi na edytowanie pola tekstowego i opcjonalnie można podać zapytanie w celu wypełnienia wartości domyślnej. |
| Lista rozwijana | Zezwala użytkownikowi na wybór z zestawu wartości. |
| Selektor zakresu czasu| Zezwala użytkownikowi na wybór ze wstępnie zdefiniowanego zestawu wartości czasu lub pobranie z niestandardowego zakresu czasu.|
| Selektor zasobów | Zezwala użytkownikowi na wybór spośród zasobów wybranych dla skoroszytu.|

### <a name="using-a-text-parameter"></a>Używanie parametru tekstowego

Wartość typu użytkownika w polu tekstowym jest zastępowana bezpośrednio w zapytaniu, bez ucieczki ani nie. Jeśli potrzebna wartość jest ciągiem, zapytanie powinno zawierać cudzysłowy wokół parametru (na przykład **"{parameter}"**).

Parametr Text umożliwia użycie wartości w polu tekstowym w dowolnym miejscu. Może to być nazwa tabeli, nazwa kolumny, nazwa funkcji, operator itp.  Typ parametru tekstowego ma ustawienie **Pobierz wartość domyślną z zapytania analizy**, co umożliwia autorowi skoroszytu użycie zapytania w celu wypełnienia wartości domyślnej dla tego pola tekstowego.

Przy użyciu wartości domyślnej z kwerendy dziennika, jako wartość domyślna jest używana tylko pierwsza wartość pierwszego wiersza (wiersz 0, kolumna 0). W związku z tym zaleca się ograniczenie zapytania, aby zwracał tylko jeden wiersz i jedną kolumnę. Wszystkie inne dane zwrócone przez zapytanie są ignorowane. 

Niezależnie od wartości zwracanej przez zapytanie zostaną zamienione bezpośrednio bez ucieczki ani nie. Jeśli zapytanie nie zwraca żadnych wierszy, wynik parametru jest ciągiem pustym (Jeśli parametr nie jest wymagany) lub niezdefiniowany (Jeśli parametr jest wymagany).

### <a name="using-a-drop-down"></a>Przy użyciu listy rozwijanej

Typ parametru listy rozwijanej pozwala utworzyć kontrolkę rozwijaną, umożliwiając wybór jednej lub wielu wartości.

Lista rozwijana jest wypełniana przez zapytanie dziennika lub kod JSON. Jeśli zapytanie zwraca jedną kolumnę, wartości w tej kolumnie to zarówno wartość, jak i etykieta w kontrolce listy rozwijanej. Jeśli zapytanie zwraca dwie kolumny, pierwsza kolumna jest wartością, a druga kolumna to etykieta wyświetlana na liście rozwijanej. Jeśli zapytanie zwraca trzy kolumny, trzecia kolumna jest używana do wskazania domyślnego wyboru na liście rozwijanej. Ta kolumna może być dowolnego typu, ale najprostszą jest użycie typów bool lub numeric, gdzie 0 to false, a 1 to wartość true.

Jeśli kolumna jest typu String, ciąg o wartości null/pusty jest uznawany za fałszywy, a każda inna wartość jest uznawana za wartość true. W przypadku listy rozwijanej z pojedynczym wyborem jako domyślny wybór zostanie użyta pierwsza wartość o wartości true.  W przypadku listy rozwijanej z wieloma opcjami wszystkie wartości o wartości true są używane jako domyślny wybrany zestaw. Elementy na liście rozwijanej są wyświetlane w dowolnej kolejności, w której zapytanie zwróciło wiersze. 

Przyjrzyjmy się parametrom znajdującym się w raporcie przegląd połączeń. Kliknij przycisk Edytuj symbol obok pozycji **kierunek**.

![Zrzut ekranu przedstawiający sekcję służącą do dodawania i edytowania parametrów raportu w Azure Monitor. Wybrana jest ikona Edycja parametru kierunek.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Spowoduje to uruchomienie elementu menu **Edytuj parametr** .

![Zrzut ekranu okna dialogowego Edytowanie parametru. Nazwa parametru ma kierunek, typ parametru to lista rozwijana, a wybrano opcję Pobierz dane z pliku JSON.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

KOD JSON umożliwia generowanie dowolnej tabeli wypełnionej zawartością. Na przykład następujące dane JSON generują dwie wartości na liście rozwijanej:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Bardziej zastosowanym przykładem jest użycie listy rozwijanej do pobrania z zestawu liczników wydajności według nazwy:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

Wyniki zapytania będą wyświetlane w następujący sposób:

![Lista rozwijana licznika wydajności](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Listy rozwijane to niezwykle zaawansowane narzędzia do dostosowywania i tworzenia interaktywnych raportów.

### <a name="time-range-parameters"></a>Parametry zakresu czasu

Chociaż można utworzyć własny niestandardowy parametr zakresu czasu za pośrednictwem typu parametru listy rozwijanej, można również użyć typu parametru zakres czasu, jeśli nie jest potrzebny ten sam stopień elastyczności. 

Typy parametrów zakresu czasu mają 15 zakresów domyślnych, które przechodzą z pięciu minut do ostatnich 90 dni. Dostępna jest również opcja zezwalająca na wybór niestandardowego zakresu czasu, co umożliwia operatorowi raportu wybranie jawnych wartości początkowych i końcowych dla zakresu czasu.

### <a name="resource-picker"></a>Selektor zasobów

Typ parametru selektora zasobów umożliwia określanie zakresu raportu do określonych typów zasobów. Przykładem prekompilowanego skoroszytu, który wykorzystuje typ selektora zasobów, jest skoroszyt **wydajności** .

![Lista rozwijana obszarów roboczych](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Zapisywanie skoroszytów i udostępnianie ich zespołowi

Skoroszyty są zapisywane w obszarze roboczym Log Analytics lub w ramach zasobu maszyny wirtualnej, w zależności od sposobu uzyskania dostępu do galerii skoroszytów. Skoroszyt można zapisać w sekcji **Moje raporty** prywatnej dla Ciebie lub w sekcji **raporty udostępnione** dostępne dla wszystkich użytkowników mających dostęp do zasobu. Aby wyświetlić wszystkie skoroszyty w zasobie, kliknij przycisk **Otwórz** na pasku akcji.

Aby udostępnić skoroszyt, który jest obecnie w **Moje raporty**:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." przycisk obok skoroszytu, który chcesz udostępnić
3. Kliknij przycisk **Przenieś do raportów udostępnionych**.

Aby udostępnić skoroszyt za pomocą linku lub poczty e-mail, kliknij pozycję **Udostępnij** na pasku akcji. Należy pamiętać, że adresaci linku potrzebują dostępu do tego zasobu w Azure Portal, aby wyświetlić skoroszyt. Aby wprowadzić zmiany, adresaci muszą mieć co najmniej uprawnienia współautora dla zasobu.

Aby przypiąć link do skoroszytu na pulpicie nawigacyjnym platformy Azure:

1. Kliknij przycisk **Otwórz** na pasku akcji
2. Kliknij przycisk "..." przycisk obok skoroszytu, który chcesz przypiąć
3. Kliknij pozycję **Przypnij do pulpitu nawigacyjnego**.

## <a name="next-steps"></a>Następne kroki

- Aby zidentyfikować ograniczenia i ogólną wydajność maszyn wirtualnych, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).

- Aby dowiedzieć się więcej o wykrytych zależnościach aplikacji, zobacz [Wyświetlanie mapy usługi VM Insights](vminsights-maps.md).
