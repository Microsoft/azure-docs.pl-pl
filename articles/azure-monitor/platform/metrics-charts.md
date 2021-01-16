---
title: Zaawansowane funkcje Eksploratora metryk platformy Azure
description: Dowiedz się więcej na temat zaawansowanych sposobów korzystania z Eksploratora metryk platformy Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: b4feb177abbdbfb9666be0ea0746c8316acdf5ae
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250761"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Zaawansowane funkcje Eksploratora metryk platformy Azure

> [!NOTE]
> W tym artykule założono, że znasz podstawowe funkcje Eksploratora metryk platformy Azure Azure Monitor. Jeśli jesteś nowym użytkownikiem i chcesz dowiedzieć się, jak utworzyć pierwszy wykres pomiarowy, zobacz [wprowadzenie do Eksploratora metryk](metrics-getting-started.md).

W Azure Monitor [metryki](data-platform-metrics.md) są serią mierzonych wartości i liczby, które są zbierane i przechowywane w czasie. Metryki mogą być standardowe (nazywane również "platformą") lub niestandardową. 

Metryki standardowe są udostępniane przez platformę Azure. Odzwierciedlają one dane statystyczne dotyczące kondycji i użycia zasobów platformy Azure. 

## <a name="resource-scope-picker"></a>Selektor zakresu zasobów
Selektor zakresu zasobów umożliwia wyświetlanie metryk dla pojedynczych zasobów i wielu zasobów. W poniższych sekcjach wyjaśniono, jak używać selektora zakresu zasobów. 

### <a name="select-a-single-resource"></a>Wybierz pojedynczy zasób
Wybierz pozycję **metryki** z menu **Azure monitor** lub z sekcji **monitorowanie** menu zasobu. Następnie wybierz **pozycję Wybierz zakres** , aby otworzyć selektor zakresu. 

Użyj selektora zakresu, aby wybrać zasoby, których metryki chcesz zobaczyć. Należy wypełnić zakres, jeśli otwarto Eksploratora metryk platformy Azure z menu zasobów. 

![Zrzut ekranu przedstawiający sposób otwierania selektora zakresu zasobów.](./media/metrics-charts/scope-picker.png)

W przypadku niektórych zasobów można wyświetlać tylko metryki jednego zasobu naraz. W menu **typy zasobów** te zasoby znajdują się w sekcji **wszystkie typy zasobów** .

![Zrzut ekranu przedstawiający pojedynczy zasób.](./media/metrics-charts/single-resource-scope.png)

Po wybraniu zasobu zobaczysz wszystkie subskrypcje i grupy zasobów zawierające ten zasób.

![Zrzut ekranu przedstawiający dostępne zasoby.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Jeśli chcesz, aby można było wyświetlić metryki dla wielu zasobów w tym samym czasie lub aby wyświetlić metryki w ramach subskrypcji lub grupy zasobów, wybierz opcję **zagłosuj**.

Gdy wszystko jest zadowalające, wybierz pozycję **Zastosuj**.

### <a name="view-metrics-across-multiple-resources"></a>Wyświetlanie metryk dla wielu zasobów
Niektóre typy zasobów mogą wykonywać zapytania dotyczące metryk dla wielu zasobów. Zasoby muszą znajdować się w tej samej subskrypcji i lokalizacji. Znajdź te typy zasobów w górnej części menu **typy zasobów** . 

Aby uzyskać więcej informacji, zobacz [Wybieranie wielu zasobów](metrics-dynamic-scope.md#select-multiple-resources).

![Zrzut ekranu przedstawiający typy wielu zasobów.](./media/metrics-charts/multi-resource-scope.png)

W przypadku typów, które są zgodne z wieloma zasobami, można wysyłać zapytania o metryki w ramach subskrypcji lub wielu grup zasobów. Aby uzyskać więcej informacji, zobacz [Wybieranie grupy zasobów lub subskrypcji](metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Wiele linii metrycznych i wykresów

W Eksploratorze metryk platformy Azure można tworzyć wykresy, które kreślą wiele linii metrycznych lub jednocześnie pokazują wiele wykresów metryk. Ta funkcja umożliwia:

- Skorelować powiązane metryki na tym samym wykresie, aby zobaczyć, jak jedna wartość odnosi się do innej.
- Wyświetlaj metryki korzystające z różnych jednostek miary w bliskim sąsiedztwie.
- Wizualnie Agreguj i porównuj metryki z wielu zasobów.

Załóżmy na przykład, że masz pięć kont magazynu i chcesz wiedzieć, ile miejsca zużywa razem. Można utworzyć wykres warstwowy (skumulowany), który pokazuje poszczególne wartości i sumę wszystkich wartości w określonych punktach w czasie.

### <a name="multiple-metrics-on-the-same-chart"></a>Wiele metryk na tym samym wykresie

Aby wyświetlić wiele metryk na tym samym wykresie, najpierw [Utwórz nowy wykres](metrics-getting-started.md#create-your-first-metric-chart). Następnie wybierz pozycję **Dodaj metrykę**. Powtórz ten krok, aby dodać kolejną metrykę na tym samym wykresie.

> [!NOTE]
> Zazwyczaj wykresy nie powinny mieszać metryk, które używają różnych jednostek miary. Na przykład Unikaj mieszania jednej metryki używającej milisekund z inną, która używa kilobajtów. Należy również unikać mieszania metryk, których skale różnią się znacznie. 
>
> W takich przypadkach należy rozważyć użycie wielu wykresów. W Eksploratorze metryk wybierz pozycję **Dodaj wykres** , aby utworzyć nowy wykres.

### <a name="multiple-charts"></a>Wiele wykresów

Aby utworzyć inny wykres, który używa innej metryki, wybierz pozycję **Dodaj wykres**.

Aby zmienić kolejność lub usunąć wiele wykresów, wybierz przycisk wielokropka (**...**), aby otworzyć menu Wykres. Następnie wybierz pozycję **Przenieś w górę**, **Przenieś w dół** lub **Usuń**.

## <a name="aggregation"></a>Agregacja

Po dodaniu metryki do wykresu Eksplorator metryk automatycznie stosuje agregację domyślną. Ustawienie domyślne jest zrozumiałe w scenariuszach podstawowych. Można jednak użyć innej agregacji, aby uzyskać więcej szczegółowych informacji na temat metryki. 

Przed użyciem różnych agregacji na wykresie należy zrozumieć, jak Eksplorator metryk je obsługuje. Metryki to serie pomiarów (lub "wartości metryk"), które są przechwytywane w przedziale czasu. Podczas kreolenia wykresu wartości wybranej metryki są agregowane w postaci *ziarna czasu*. 

Wybierasz rozmiar ziarna czasu przy użyciu [panelu selektora czasu](metrics-getting-started.md#select-a-time-range)Eksploratora metryk. Jeśli nie wybierzesz jawnie ziarna czasu, domyślnie używany jest obecnie wybrany zakres czasu. Po określeniu ziarna czasu wartości metryk, które zostały przechwycone podczas każdego ziarna, są agregowane na wykresie, jeden punkt danych na ziarno.

Załóżmy na przykład, że wykres pokazuje metrykę *czasu odpowiedzi serwera* . Używa ona *średniej* agregacji w przedziale czasu w *ciągu ostatnich 24 godzin*. W tym przykładzie:

- Jeśli stopień szczegółowości czasu jest ustawiony na 30 minut, wykres jest rysowany z 48 zagregowanych punktów danych. Oznacza to, że wykres liniowy łączy 48 kropek w obszarze wykresów wykresu (24 godziny x 2 punkty danych na godzinę). Każdy punkt danych reprezentuje *średnią* wszystkich przechwyconych czasów odpowiedzi dla żądań serwera, które wystąpiły podczas każdego z odpowiednich 30-minutowych okresów.
- Po przełączeniu stopnia szczegółowości czasu na 15 minut otrzymujesz 96 zagregowanych punktów danych.  Oznacza to, że otrzymujesz 24 godziny x 4 punktów danych na godzinę.

Eksplorator metryk ma pięć podstawowych typów agregacji statystycznych: sum, Count, min, Max i Average. Agregacja *sum* jest czasami nazywana agregacją *łączną* . W przypadku wielu metryk Eksplorator metryk ukrywa agregacje, które nie są istotne i nie można ich używać.

* **Sum**: suma wszystkich wartości przechwyconych podczas interwału agregacji.

    ![Zrzut ekranu przedstawiający żądanie sum.](./media/metrics-charts/request-sum.png)

* **Liczba**: liczba pomiarów przechwyconych podczas interwału agregacji. 
    
    Gdy Metryka jest zawsze przechwytywana z wartością 1, agregacja liczby jest równa agregacji SUM. Ten scenariusz jest typowy, gdy Metryka śledzi liczbę różnych zdarzeń, a każda pomiar reprezentuje jedno zdarzenie. Kod emituje rekord metryki za każdym razem, gdy nadejdzie nowe żądanie.

    ![Zrzut ekranu przedstawiający żądanie Count.](./media/metrics-charts/request-count.png)

* **Średnia**: średnia wartości metryk przechwyconych podczas interwału agregacji.

    ![Zrzut ekranu przedstawiający średniego żądania.](./media/metrics-charts/request-avg.png)

* **Min**: najmniejsza wartość przechwycona podczas interwału agregacji.

    ![Zrzut ekranu przedstawiający minimalne żądanie.](./media/metrics-charts/request-min.png)

* **Max**: największą wartość przechwyconą w interwale agregacji.

    ![Zrzut ekranu przedstawiający maksymalną liczbę żądań.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filtry

Filtry można stosować do wykresów, których metryki mają wymiary. Załóżmy na przykład, że Wyobraź sobie metrykę "liczba transakcji", która ma wymiar "typ odpowiedzi". Ten wymiar wskazuje, czy odpowiedź z transakcji zakończyła się powodzeniem, czy niepowodzeniem. W przypadku filtrowania dla tego wymiaru zobaczysz wiersz wykresu tylko dla transakcji zakończonych powodzeniem (lub zakończonych niepowodzeniem). 

### <a name="add-a-filter"></a>Dodawanie filtru

1. Nad wykresem wybierz pozycję **Dodaj filtr**.

2. Wybierz wymiar (Właściwość) do filtrowania.

   ![Zrzut ekranu pokazujący Wymiary (właściwości), które można filtrować.](./media/metrics-charts/028.png)

3. Wybierz wartości wymiaru, które mają zostać uwzględnione podczas kreolenia wykresu. Poniższy przykład filtruje pomyślne transakcje magazynu:

   ![Zrzut ekranu przedstawiający pomyślne odfiltrowane transakcje magazynu.](./media/metrics-charts/029.png)

4. Wybierz poza **selektorem filtrów** , aby go zamknąć. Teraz wykres pokazuje, ile transakcji magazynu nie powiodło się:

   ![Zrzut ekranu pokazujący, ile transakcji magazynu zakończyło się niepowodzeniem.](./media/metrics-charts/030.png)

Możesz powtórzyć te kroki, aby zastosować wiele filtrów do tych samych wykresów.



## <a name="metric-splitting"></a>Podział metryk

Można podzielić metrykę według wymiaru, aby wizualizować różne segmenty porównania metryki. Dzielenie może także ułatwić zidentyfikowanie nieleżących segmentów wymiaru.

### <a name="apply-splitting"></a>Zastosuj podział

1. Nad wykresem wybierz pozycję **Zastosuj podział**.
 
   > [!NOTE]
   > Wykresy z wieloma metrykami nie mogą korzystać z funkcji dzielenia. Ponadto, chociaż wykres może mieć wiele filtrów, może mieć tylko jeden wymiar dzielenia.

2. Wybierz wymiar, dla którego chcesz podzielić wykres:

   ![Zrzut ekranu pokazujący wybrany wymiar, dla którego ma zostać przesegmentować wykres.](./media/metrics-charts/031.png)

   Wykres zawiera teraz wiele wierszy, po jednym dla każdego segmentu wymiaru:

   ![Zrzut ekranu pokazujący wiersze dla każdego segmentu wymiarów.](./media/metrics-charts/032.png)

3. Wybierz poza **selektorem grupowania** , aby go zamknąć.

   > [!NOTE]
   > Aby ukryć segmenty, które nie są istotne dla danego scenariusza i aby ułatwić odczytywanie wykresów, użyj filtrowania i dzielenia w tym samym wymiarze.

## <a name="locking-the-range-of-the-y-axis"></a>Blokowanie zakresu osi y

Zablokowanie zakresu osi wartości (y) jest ważne na wykresach pokazujących niewielkie wahania dużych wartości. 

Na przykład, spadek liczby pomyślnych żądań z 99,99% do 99,5% może reprezentować znaczącą redukcję jakości usługi. Jednak obserwowanie wahania wartości liczbowej może być trudne lub nawet niemożliwe, jeśli używasz domyślnych ustawień wykresu. W takim przypadku można zablokować najniższą granicę wykresu do 99%, aby zwiększyć niewielką liczbę elementów upuszczania. 

Innym przykładem jest Wahanie dostępnej pamięci. W tym scenariuszu wartość będzie technicznie nigdy nie dociera do 0. Naprawianie zakresu do wyższego poziomu może znacznie ułatwić dostęp do dostępnej pamięci. 

Aby kontrolować zakres osi y, otwórz menu wykresu (**...**). Następnie wybierz pozycję **Ustawienia wykresu** , aby uzyskać dostęp do zaawansowanych ustawień wykresu.

![Zrzut ekranu, który podświetla zaznaczenie ustawień wykresu.](./media/metrics-charts/033.png)

Zmodyfikuj wartości w sekcji **zakres osi Y** **lub wybierz pozycję** Auto, aby przywrócić wartości domyślne.
 
 ![Zrzut ekranu, który podświetla sekcję zakresu osi Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Jeśli zachodzi konieczność zablokowania granic osi y dla wykresów, które śledzą zliczenia lub sumuje w danym okresie czasu (przy użyciu funkcji count, sum, min lub max), należy zwykle określić stały stopień szczegółowości czasu. W takim przypadku nie należy polegać na automatycznych ustawieniach domyślnych. 
>
> Wybierasz stały stopień szczegółowości czasu, ponieważ wartości wykresu zmieniają się, gdy poziom szczegółowości czasu jest modyfikowany automatycznie po zmianie rozmiaru okna przeglądarki lub zmianie rozdzielczości ekranu. Zmiana stopnia szczegółowości czasu wpływa na wygląd wykresu, co unieważnia bieżący wybór zakresu osi y.

## <a name="line-colors"></a>Kolory linii

Po skonfigurowaniu wykresów linie wykresu zostaną automatycznie przypisane do koloru z domyślnej palety. Można zmienić te kolory.

Aby zmienić kolor linii wykresu, wybierz kolorowy pasek w legendzie, który odnosi się do wykresu. Zostanie otwarte okno dialogowe selektora kolorów. Użyj próbnika kolorów, aby skonfigurować kolor linii.

![Zrzut ekranu pokazujący sposób zmiany koloru.](./media/metrics-charts/035.png)

Dostosowane kolory są zachowywane po przypięciu wykresu do pulpitu nawigacyjnego. W poniższej sekcji pokazano, jak przypiąć wykres.

## <a name="pinning-to-dashboards"></a>Przypinanie do pulpitów nawigacyjnych 

Po skonfigurowaniu wykresu warto dodać go do pulpitu nawigacyjnego. Przypinając wykres do pulpitu nawigacyjnego, możesz udostępnić go zespołowi. Możesz również uzyskać wgląd, wyświetlając go w kontekście innej telemetrii monitorowania.

Aby przypiąć skonfigurowany wykres do pulpitu nawigacyjnego, w prawym górnym rogu wykresu wybierz pozycję **Przypnij do pulpitu nawigacyjnego**.

![Zrzut ekranu przedstawiający sposób przypinania wykresu do pulpitu nawigacyjnego.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Zasady alertów

Możesz użyć kryteriów wizualizacji, aby utworzyć regułę alertu opartą na metrykach. Nowa reguła alertu obejmie zasób docelowy wykresu, metrykę, podział i wymiary filtru. Te ustawienia można modyfikować za pomocą okienka tworzenia reguły alertu.

Aby rozpocząć, wybierz pozycję **Nowa reguła alertu**.

![Zrzut ekranu pokazujący nowy przycisk reguły alertu wyróżniony kolorem czerwonym.](./media/metrics-charts/042.png)

Zostanie otwarte okienko tworzenia reguły alertu. W okienku zobaczysz wymiary metryk wykresu. Pola w okienku są wstępnie wypełniane, aby ułatwić dostosowanie reguły.

![Zrzut ekranu przedstawiający okienko tworzenia reguły.](./media/metrics-charts/041.png)

Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi](alerts-metric.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nie widzisz żadnych danych na wykresie, przejrzyj następujące informacje dotyczące rozwiązywania problemów:

* Filtry stosują się do wszystkich wykresów w okienku. Gdy skupsz się na wykresie, upewnij się, że nie ustawisz filtru, który wyklucza wszystkie dane na innym wykresie.

* Aby ustawić różne filtry na różnych wykresach, należy utworzyć wykresy w różnych blokach. Następnie Zapisz wykresy jako osobne Ulubione. Jeśli chcesz, możesz przypiąć wykresy do pulpitu nawigacyjnego, aby można było je zobaczyć razem.

* W przypadku segmentacji wykresu na podstawie właściwości, która nie jest definiowana przez metrykę, na wykresie nie jest wyświetlana żadna zawartość. Spróbuj usunąć segmentację (dzielenie) lub wybierz inną właściwość.

## <a name="next-steps"></a>Następne kroki

Aby tworzyć pulpity nawigacyjne z możliwością podejmowania działań za pomocą metryk, zobacz [Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI](../learn/tutorial-app-dashboards.md).

 