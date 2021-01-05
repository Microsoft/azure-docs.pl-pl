---
title: Zaawansowane funkcje Eksploratora metryk platformy Azure
description: Dowiedz się więcej na temat zaawansowanych funkcji Azure Monitor Eksplorator metryk
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: a80eaecc02fa3c8c6618341c02e22241f0dc7faf
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845038"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Zaawansowane funkcje Eksploratora metryk platformy Azure

> [!NOTE]
> W tym artykule założono, że znasz podstawowe funkcje Eksplorator metryk. Jeśli jesteś nowym użytkownikiem i chcesz dowiedzieć się, jak utworzyć pierwszy wykres pomiarowy, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Metryki na platformie Azure

[Metryki w Azure monitor](data-platform-metrics.md) są serią mierzonych wartości i liczb, które są zbierane i przechowywane w czasie. Istnieją metryki standardowe (lub platformy) i metryki niestandardowe. Standardowe metryki są udostępniane przez samą platformę Azure. Standardowe metryki odzwierciedlają dane statystyczne dotyczące kondycji i użycia zasobów platformy Azure. Niestandardowe metryki są wysyłane do platformy Azure przez aplikacje przy użyciu [interfejsu api Application Insights dla niestandardowych zdarzeń i metryk](../app/api-custom-events-metrics.md),  [rozszerzenia Windows Diagnostyka Azure (funkcji wad)](./diagnostics-extension-overview.md)lub [interfejsu API REST Azure monitor](./metrics-store-custom-rest-api.md).

## <a name="resource-scope-picker"></a>Selektor zakresu zasobów
Selektor zakresu zasobów umożliwia wyświetlanie metryk w ramach jednego i wielu zasobów. Poniżej znajdują się instrukcje dotyczące korzystania z selektora zakresu zasobów. 

### <a name="selecting-a-single-resource"></a>Wybieranie pojedynczego zasobu
Wybierz pozycję **metryki** z menu **Azure monitor** lub z sekcji **monitorowanie** menu zasobu. Kliknij przycisk "Wybierz zakres", aby otworzyć selektor zakresu, który umożliwi wybranie zasobów, dla których chcesz wyświetlić metryki. Powinno to być już wypełnione, jeśli otwarto Eksploratora metryk z menu zasobów. 

![Zrzut ekranu przedstawiający selektor zakresu zasobów](./media/metrics-charts/scope-picker.png)

W przypadku niektórych zasobów można tylko wyświetlić metryki pojedynczego zasobu naraz. Te zasoby znajdują się w sekcji "wszystkie typy zasobów" na liście rozwijanej typy zasobów.

![Zrzut ekranu przedstawiający pojedynczy zasób](./media/metrics-charts/single-resource-scope.png)

Po kliknięciu żądanego zasobu zobaczysz wszystkie subskrypcje i grupy zasobów zawierające ten zasób.

![Zrzut ekranu przedstawiający dostępne zasoby](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Jeśli chcesz wyświetlić metryki wielu zasobów w tym samym czasie lub metryki w ramach subskrypcji lub grupy zasobów, kliknij przycisk do głosowania.

Gdy wybierzesz pozycję "Zastosuj", po zakończeniu zaznaczania.

### <a name="viewing-metrics-across-multiple-resources"></a>Wyświetlanie metryk dla wielu zasobów
Niektóre typy zasobów umożliwiły możliwość wykonywania zapytań o metryki w wielu zasobach, o ile znajdują się w tej samej subskrypcji i lokalizacji. Te typy zasobów można znaleźć w górnej części listy rozwijanej "typy zasobów". Aby uzyskać więcej informacji na temat sposobu wyświetlania metryk w wielu zasobach, Wyświetl [ten dokument](metrics-dynamic-scope.md#selecting-multiple-resources).

![Zrzut ekranu przedstawiający typy zasobów krzyżowych](./media/metrics-charts/multi-resource-scope.png)

W przypadku typów zgodnych z wieloma zasobami można także wysyłać zapytania o metryki w ramach subskrypcji lub wielu grup zasobów. Aby dowiedzieć się, jak to zrobić, zobacz [ten artykuł](metrics-dynamic-scope.md#selecting-a-resource-group-or-subscription)


## <a name="create-views-with-multiple-metrics-and-charts"></a>Tworzenie widoków z wieloma metrykami i wykresami

Można utworzyć wykresy, które kreślą wiele linii metryk lub jednocześnie wyświetlają wiele wykresów metryk. Ta funkcja umożliwia:

- skorelować powiązane metryki na tym samym wykresie, aby zobaczyć, jak jedna wartość jest powiązana z inną
- Wyświetl metryki z różnymi jednostkami miary w bliskim sąsiedztwie
- wizualnie Agreguj i porównuj metryki z wielu zasobów

Jeśli na przykład masz 5 kont magazynu i chcesz dowiedzieć się, ile łącznego obszaru są używane między nimi, możesz utworzyć wykres warstwowy (skumulowany), który pokazuje indywidualną i sumę wszystkich wartości w określonych punktach w czasie.

### <a name="multiple-metrics-on-the-same-chart"></a>Wiele metryk na tym samym wykresie

Najpierw [Utwórz nowy wykres](metrics-getting-started.md#create-your-first-metric-chart). Kliknij przycisk **Dodaj metrykę** i powtórz kroki, aby dodać kolejną metrykę na tym samym wykresie.

   > [!NOTE]
   > Zwykle nie chcesz mieć metryk z różnymi jednostkami miary (tj. "milisekund" i "kilobajtów") lub z znacznie różną skalą na jednym wykresie. Zamiast tego należy rozważyć użycie wielu wykresów. Kliknij przycisk Dodaj wykres, aby utworzyć wiele wykresów w Eksploratorze metryk.

### <a name="multiple-charts"></a>Wiele wykresów

Kliknij przycisk **Dodaj wykres** i Utwórz inny wykres z inną metryką.

### <a name="order-or-delete-multiple-charts"></a>Kolejność lub usuwanie wielu wykresów

Aby zamówić lub usunąć wiele wykresów, kliknij symbol wielokropka ( **...** ), aby otworzyć menu Wykres i wybrać odpowiedni element menu **Przenieś w górę** lub **Przenieś w dół** lub **Usuń**.

## <a name="changing-aggregation"></a>Zmiana agregacji

Po dodaniu metryki do wykresu Eksplorator metryk automatycznie wybiera swoją domyślną agregację. Wartość domyślna ma sens w podstawowych scenariuszach, ale można użyć innej agregacji, aby uzyskać dodatkowe informacje o metryki. Wyświetlanie różnych agregacji na wykresie wymaga zrozumienia sposobu, w jaki Eksplorator metryk je obsługuje. 

Metryki to serie pomiarów (lub "wartości metryk") przechwytywane w danym okresie czasu. Podczas kreolenia wykresu wartości wybranej metryki są agregowane w postaci *ziarna czasu*. Rozmiar ziarna można wybrać [za pomocą panelu selektora czasu Eksplorator metryk](metrics-getting-started.md#select-a-time-range). Jeśli nie wprowadzisz jawnego wyboru ziarna czasu, stopień szczegółowości czasu zostanie automatycznie wybrany na podstawie aktualnie wybranego zakresu czasu. Po ustaleniu ziarna czasu wartości metryk, które zostały przechwycone podczas każdego interwału ziarna, są agregowane i umieszczane na wykresie — jeden punkt danych na ziarno.

Załóżmy na przykład, że wykres pokazuje metrykę **czasu odpowiedzi serwera** przy użyciu **średniej** agregacji w ciągu **ostatnich 24 godzin** :

- Jeśli stopień szczegółowości czasu jest ustawiony na 30 minut, wykres jest rysowany z 48 zagregowanych punktów kontrolnych (np. wykres liniowy łączy 48 kropek w obszarze wykresów wykresu). Oznacza to, że 24-godzinny x 2 punkty kontrolne na godzinę. Każdy punkt danych reprezentuje *średnią* wszystkich przechwyconych czasów odpowiedzi dla żądań serwera, które wystąpiły w ciągu każdego z odpowiednich 30 okresów czasu.
- Po przełączeniu stopnia szczegółowości czasu na 15 minut otrzymujesz 96 zagregowanych punktów.  Oznacza to, że 24-godzinny x 4 punkty kontrolne na godzinę.

W Eksploratorze metryk dostępne są pięć podstawowych typów agregacji statystyk: **sum**, **Count**, **min**, **Max** i **Average**. Agregacja **sum** jest czasami określana jako **Suma** agregacji. W przypadku wielu metryk Eksplorator metryk będzie ukrywać agregacje, które są całkowicie nieistotne i nie mogą być używane.

**Sum** — suma wszystkich wartości przechwyconych w interwale agregacji

![Zrzut ekranu przedstawiający sumę żądań](./media/metrics-charts/request-sum.png)

**Count** — liczba pomiarów przechwyconych w interwale agregacji. Należy zauważyć, że **Liczba** będzie równa **sumie** w przypadku, gdy Metryka jest zawsze przechwytywana z wartością 1. Jest to typowe, gdy Metryka śledzi liczbę różnych zdarzeń, a każda pomiar reprezentuje jedno zdarzenie (tzn. kod wygeneruje rekord metryk za każdym razem, gdy występuje nowe żądanie)

![Zrzut ekranu przedstawiający liczbę żądań](./media/metrics-charts/request-count.png)

**Average** — średnia wartości metryk przechwyconych w interwale agregacji

![Zrzut ekranu przedstawiający średnie żądanie](./media/metrics-charts/request-avg.png)

**Min** — najmniejsza wartość przechwycona przez interwał agregacji

![Zrzut ekranu przedstawiający minimalne żądanie](./media/metrics-charts/request-min.png)

**Max** — największa wartość przechwycona przez interwał agregacji

![Zrzut ekranu przedstawiający maksymalną liczbę żądań](./media/metrics-charts/request-max.png)

## <a name="apply-filters-to-charts"></a>Zastosuj filtry do wykresów

Można zastosować filtry do wykresów, które wyświetlają metryki z wymiarami. Na przykład jeśli Metryka "liczba transakcji" ma wymiar "typ odpowiedzi", który wskazuje, czy odpowiedź z transakcji zakończyła się powodzeniem, czy niepowodzeniem, filtrowanie w tym wymiarze spowoduje wykreślenie wiersza wykresu dla transakcji zakończonych powodzeniem (lub tylko zakończonych niepowodzeniem). 

### <a name="to-add-a-filter"></a>Aby dodać filtr

1. Wybierz pozycję **Dodaj filtr** powyżej wykresu

2. Wybierz wymiar, który ma być filtrowany

   ![Zrzut ekranu pokazujący Wymiary (właściwości), które można filtrować.](./media/metrics-charts/028.png)

3. Wybierz wartości wymiaru, które mają zostać uwzględnione podczas kreolenia wykresu (w tym przykładzie pokazano, jak odfiltrować pomyślne transakcje magazynu):

   ![Zrzut ekranu pokazujący filtrowanie z pomyślnych transakcji magazynu.](./media/metrics-charts/029.png)

4. Po wybraniu wartości filtru kliknij poza selektorem filtru, aby go zamknąć. Teraz wykres pokazuje, ile transakcji magazynu nie powiodło się:

   ![Zrzut ekranu pokazujący, ile transakcji magazynu nie powiodło się](./media/metrics-charts/030.png)

5. Możesz powtórzyć kroki 1-4, aby zastosować wiele filtrów do tych samych wykresów.



## <a name="apply-splitting-to-a-chart"></a>Zastosuj podział do wykresu

Można podzielić metrykę według wymiaru, aby wizualizować, jak różne segmenty są porównywane z innymi, i identyfikować nieleżące segmenty wymiaru.

### <a name="apply-splitting"></a>Zastosuj podział

1. Kliknij przycisk **Zastosuj podział** powyżej wykresu.
 
   > [!NOTE]
   > Nie można używać dzielenia z wykresami, które mają wiele metryk. Ponadto można mieć wiele filtrów, ale tylko jeden wymiar podziału jest zastosowany do każdego pojedynczego wykresu.

2. Wybierz wymiar, dla którego chcesz podzielić wykres:

   ![Zrzut ekranu pokazujący wybrany wymiar, w którym znajduje się wykres.](./media/metrics-charts/031.png)

   Teraz wykres zawiera teraz wiele wierszy, po jednym dla każdego segmentu wymiaru:

   ![Zrzut ekranu pokazujący wiele wierszy, jeden dla każdego segmentu wymiaru.](./media/metrics-charts/032.png)

3. Kliknij poza **selektorem grupowania** , aby go zamknąć.

   > [!NOTE]
   > Użyj filtrowania i dzielenia w tym samym wymiarze, aby ukryć segmenty nieistotne dla danego scenariusza i ułatwić odczytywanie wykresów.

## <a name="lock-boundaries-of-chart-y-axis"></a>Blokuj granice osi y wykresu

Zablokowanie zakresu osi y jest ważne, gdy wykres pokazuje mniejsze wahania większych wartości. 

Na przykład, gdy liczba pomyślnych żądań spadnie w dół od 99,99% do 99,5%, może to oznaczać znaczącą redukcję jakości usługi. Obserwowanie jednak niewielkie wahania wartości liczbowych byłyby trudne lub nawet niemożliwe z domyślnych ustawień wykresu. W takim przypadku można zablokować najniższą granicę wykresu do 99%, co sprawia, że to małe powiększanie. 

Innym przykładem jest Wahanie dostępnej pamięci, gdzie wartość będzie technicznie nigdy nie dociera do 0. Naprawianie zakresu do wyższej wartości może spowodować, że przerwy w dostępnej pamięci są łatwiejsze. 

Aby kontrolować zakres osi y, użyj "..." menu Wykres i wybierz pozycję **Ustawienia wykresu** , aby uzyskać dostęp do zaawansowanych ustawień wykresu.

![Zrzut ekranu, który podświetla opcję ustawień wykresu.](./media/metrics-charts/033.png)

 Zmodyfikuj wartości w sekcji Zakres osi Y **lub użyj przycisku** Auto, aby przywrócić wartości domyślne.
 
 ![Zrzut ekranu, który podświetla sekcję zakresu osi Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Zablokowanie granic osi y dla wykresów, które śledzą różne liczby lub sumy w danym okresie czasu (w tym przypadku użycie metody Count, sum, minimum lub Maximum) zwykle wymaga określenia stałego stopnia szczegółowości, zamiast polegania na automatycznym wykorzystaniu wartości domyślnych. Jest to konieczne, ponieważ wartości na wykresach zmieniają się, gdy poziom szczegółowości czasu jest automatycznie modyfikowany przez okno przeglądarki zmiany rozmiarów użytkownika lub przejście z jednej rozdzielczości ekranu do innej. Zmiana stopnia szczegółowości w czasie wpływa na wygląd wykresu, co unieważnia bieżący wybór zakresu osi y.

## <a name="change-colors-of-chart-lines"></a>Zmień kolory linii wykresu

Po skonfigurowaniu wykresów linie wykresu zostaną automatycznie przypisane do koloru z domyślnej palety. Można zmienić te kolory.

Aby zmienić kolor linii wykresu, kliknij kolorowy pasek w legendzie, który odnosi się do wykresu. Zostanie otwarte okno dialogowe selektora kolorów. Użyj próbnika kolorów, aby skonfigurować kolor linii.

![Zrzut ekranu pokazujący sposób zmiany koloru](./media/metrics-charts/035.png)

Po skonfigurowaniu kolorów wykresu pozostaną one w taki sposób, gdy przypinasz wykres do pulpitu nawigacyjnego. W poniższej sekcji pokazano, jak przypiąć wykres.

## <a name="pin-charts-to-dashboards"></a>Przypinanie wykresów do pulpitów nawigacyjnych

Po skonfigurowaniu wykresów możesz dodać go do pulpitów nawigacyjnych, aby można było wyświetlić go ponownie, prawdopodobnie w kontekście innej telemetrii monitorowania lub udostępnić zespołowi.

Aby przypiąć skonfigurowany wykres do pulpitu nawigacyjnego:

Po skonfigurowaniu wykresu kliknij pozycję **Przypnij do pulpitu nawigacyjnego** w prawym górnym rogu wykresu.

![Zrzut ekranu pokazujący, jak przypiąć do wykresu](./media/metrics-charts/036.png)

## <a name="create-alert-rules"></a>Tworzenie reguł alertów

Możesz użyć kryteriów ustawionych do wizualizacji metryk jako podstawy reguły alertu opartej na metrykach. Nowa reguła alertów będzie obejmować zasób docelowy, metrykę, podział i wymiary filtru z wykresu. Później będzie można modyfikować te ustawienia w okienku tworzenia reguły alertu.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Aby utworzyć nową regułę alertu, kliknij pozycję **Nowa reguła alertu** .

![Przycisk nowej reguły alertu wyróżniony czerwonymi](./media/metrics-charts/042.png)

Nastąpi przekierowanie do okienka Tworzenie reguły alertów z bazowymi wymiarami metryk z wykresu wstępnie wypełnionym, aby ułatwić generowanie niestandardowych reguł alertów.

![Tworzenie reguły alertu](./media/metrics-charts/041.png)

Zapoznaj się z tym [artykułem](alerts-metric.md) , aby dowiedzieć się więcej o konfigurowaniu alertów dotyczących metryk.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Nie widzę żadnych danych na wykresie.*

* Filtry stosują się do wszystkich wykresów w okienku. Upewnij się, że podczas skoncentrowania się na jednym wykresie nie ustawiono filtru, który wyklucza wszystkie dane z innego.

* Jeśli chcesz ustawić inne filtry na różnych wykresach, utwórz je w różnych blokach, a następnie Zapisz je jako osobne Ulubione. Jeśli chcesz, możesz przypiąć je do pulpitu nawigacyjnego, aby były widoczne obok siebie.

* W przypadku segmentacji wykresu na podstawie właściwości, która nie jest zdefiniowana w metryce, na wykresie nie będzie niczego. Spróbuj usunąć segmentację (dzielenie) lub wybierz inną właściwość.

## <a name="next-steps"></a>Następne kroki

  Przeczytaj temat [Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI](../learn/tutorial-app-dashboards.md) , aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących tworzenia pulpitów nawigacyjnych z możliwością podejmowania działań z
