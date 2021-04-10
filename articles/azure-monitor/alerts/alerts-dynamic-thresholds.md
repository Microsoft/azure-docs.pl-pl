---
title: Tworzenie alertów z progami dynamicznymi w Azure Monitor
description: Tworzenie alertów przy użyciu dynamicznych progów opartych na usłudze Machine Learning
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 9cf4caf77d14dd1296276ca48827c170ef265e9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101718067"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Alerty metryk z progami dynamicznymi w Azure Monitor

Alert dotyczący metryki z funkcją dynamicznego wykrywania progów korzysta z zaawansowanych funkcji uczenia maszynowego (ML), aby dowiedzieć się, jak działa historycznie metryki, zidentyfikować wzorce i anomalie wskazujące potencjalne problemy z usługą. Zapewnia ona obsługę prostego interfejsu użytkownika i operacji na dużą skalę, umożliwiając użytkownikom konfigurowanie reguł alertów za pomocą interfejsu API Azure Resource Manager w pełni zautomatyzowany sposób.

Po utworzeniu reguły alertu będzie ona uruchamiana tylko wtedy, gdy monitorowana Metryka nie zadziała zgodnie z oczekiwaniami, na podstawie jego dostosowanych progów.

Chętnie poznamy Twoją opinię <azurealertsfeedback@microsoft.com> .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Dlaczego i kiedy jest zalecany typ warunku dynamicznego?

1. **Skalowalne alerty** — reguły alertów dotyczących progów dynamicznych mogą tworzyć dostosowane progi dla setek serii metrycznych jednocześnie, zapewniając tym samym łatwość definiowania reguły alertu w ramach jednej metryki. Zapewniają one mniejszą liczbę alertów do utworzenia i zarządzania. Aby je utworzyć, można użyć Azure Portal lub interfejsu API Azure Resource Manager. Skalowalne podejście jest szczególnie przydatne podczas pracy z wymiarami metryk lub stosowania do wielu zasobów, na przykład do wszystkich zasobów subskrypcji.  [Dowiedz się więcej o sposobie konfigurowania alertów metryk z progami dynamicznymi przy użyciu szablonów](./alerts-metric-create-templates.md).

1. **Inteligentne Rozpoznawanie wzorców metryk** — korzystając z technologii ml, możemy automatycznie wykrywać wzorce metryk i dostosowywać je do zmian metryk w miarę upływu czasu, co może często obejmować sezonowości (co godzinę/codziennie/tydzień). Dostosowanie do zachowania metryk w czasie i alertów na podstawie odchyleń od jej wzorca zwalnia obciążenie dotyczące progu "Right" dla każdej metryki. Algorytm ML używany w dynamicznych progach został zaprojektowany w celu zapobiegania zakłóceniom (o niskiej dokładności) lub szerokim (niskim odwróceniu), które nie mają oczekiwanego wzorca.

1. **Intuicyjna konfiguracja** — progi dynamiczne umożliwiają Konfigurowanie alertów metryk przy użyciu koncepcji wysokiego poziomu, co pozwala uniknąć konieczności posiadania obszernej wiedzy na temat tej metryki.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Jak skonfigurować reguły alertów z progami dynamicznymi?

Alerty z progami dynamicznymi można skonfigurować za pomocą alertów metryk w Azure Monitor. [Dowiedz się więcej o sposobie konfigurowania alertów dotyczących metryk](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Jak są obliczane progi?

Dynamiczne progi ciągle poznają dane z serii metryk i próbują modelować je przy użyciu zestawu algorytmów i metod. Wykrywa on wzorce danych, takie jak sezonowości (co godzinę/codziennie/co tydzień) i może obsługiwać metryki szumów (na przykład procesora CPU lub pamięci), a także metryki z niskim rozpraszaniem (na przykład dostępność i częstotliwość błędów).

Progi są wybierane w taki sposób, że odchylenie od tych progów wskazuje anomalię w zachowaniu metryki.

> [!NOTE]
> Progi dynamiczne mogą wykrywać sezonowościy dla wzorców co godzinę, codziennie lub co tydzień. Mogą nie zostać wykryte inne wzorce, takie jak analiza godzinowa lub krótkoterminowa sezonowości. Aby wykryć cotygodniowe sezonowości, wymagane są co najmniej trzy tygodnie danych historycznych. 

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Co oznacza ustawienie "czułość" w progach dynamicznych?

Czułość progowa alertu to koncepcja wysokiego poziomu, która kontroluje wielkość odchylenia od zachowania metryki wymaganego do wyzwolenia alertu.
Ta opcja nie wymaga znajomości domeny informacji o metrykach, takich jak próg statyczny. Dostępne opcje:

- Wysoka — progi będą ciasne i blisko wzorca serii metryk. Reguła alertu zostanie wyzwolona na najmniejszym odchyleniu, co spowoduje wyświetlenie większej liczby alertów.
- Średnia — mniej ścisłe i bardziej zrównoważone progi, mniejsze alerty niż w przypadku wysokiej czułości (wartość domyślna).
- Niski — progi będą luźne z większą odległością od wzorca serii metrycznych. Reguła alertu będzie wyzwalana tylko w przypadku dużych odchyleń, co spowodowało zmniejszenie liczby alertów.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Jakie są opcje Ustawienia "operator" w progach dynamicznych?

Reguła alertów dotyczących progów dynamicznych umożliwia tworzenie dostosowanych progów na podstawie zachowania metryki dla górnego i dolnego ograniczenia przy użyciu tej samej reguły alertu.
Można wybrać alert, który zostanie wyzwolony na jednym z następujących trzech warunków:

- Większy niż próg górny lub niższy niż dolny próg (wartość domyślna)
- Większy niż górny próg
- Niższy niż dolny próg.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Co oznaczają ustawienia zaawansowane progów dynamicznych?

**Okresy zakończone niepowodzeniem** — progi dynamiczne umożliwiają również skonfigurowanie "naruszeń liczbowych w celu wyzwolenia alertu", co oznacza minimalną liczbę odchyleń w określonym przedziale czasu dla systemu, aby zgłosić alert (domyślny przedział czasu to cztery odchylenia w ciągu 20 minut). Użytkownik może skonfigurować okresy zakończone niepowodzeniem i wybrać, do czego ma być wysyłany alert, zmieniając okresy niepowodzenia i przedział czasu. Ta możliwość zmniejsza poziom hałasu alertu wygenerowanego przez przejścia przejściowe. Na przykład:

Aby wyzwolić alert, gdy problem jest ciągły przez 20 minut, 4 kolejne razy w danym okresie grupowania 5 minut, użyj następujących ustawień:

![Ustawienia okresów zakończonych niepowodzeniem dla ciągłego problemu przez 20 minut, 4 kolejne razy w danym przedziale czasu (5 minut)](media/alerts-dynamic-thresholds/0008.png)

Aby wyzwolić alert w przypadku naruszenia progów dynamicznych przez 20 minut z ostatnich 30 minut z okresem wynoszącym 5 minut, użyj następujących ustawień:

![Ustawienia okresów zakończonych niepowodzeniem dla problemu przez 20 minut z ostatnich 30 minut z grupą okresów wynoszącą 5 minut](media/alerts-dynamic-thresholds/0009.png)

**Ignoruj dane przed** — użytkownicy mogą również opcjonalnie definiować datę początkową, z której system powinien rozpocząć obliczanie progów. Typowy przypadek użycia może wystąpić, gdy zasób był uruchomiony w trybie testowania i teraz został podwyższony do obsługi obciążenia produkcyjnego, a w związku z tym zachowanie jakiejkolwiek metryki w fazie testowania powinno być nieuznane.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Jak dowiedzieć się, dlaczego wyzwolono alert dotyczący progów dynamicznych?

Wyzwolone wystąpienia alertów można eksplorować w widoku alertów przez kliknięcie linku w wiadomości e-mail lub tekstowej lub w przeglądarce w celu wyświetlenia widoku alertów w Azure Portal. [Dowiedz się więcej o widoku alertów](./alerts-overview.md#alerts-experience).

Widok alertu wyświetla następujące:

- Wszystkie szczegóły metryki w momencie uruchomienia alertu dotyczącego progów dynamicznych.
- Wykres okresu, w którym alert został wyzwolony, zawierający progi dynamiczne używane w tym momencie.
- Możliwość przekazywania opinii na temat alertu dotyczącego progów dynamicznych i wyświetlania alertów, co może poprawić wykrycie w przyszłości.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Czy w ramach metryki zostaną powolne zmiany w wyzwoleniu alertu?

Prawdopodobnie nie. Dynamiczne progi są dobrym środkiem do wykrywania znaczących odchyleń, a nie od wolniejszych problemów.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Ile danych jest używanych do podglądu, a następnie obliczania progów?

Po pierwszym utworzeniu reguły alertu progi pojawiające się na wykresie są obliczane na podstawie wystarczających danych historycznych w celu obliczenia godzin lub dziennych wzorców sezonowych (10 dni). Po utworzeniu reguły alertu dynamiczne progi korzystają ze wszystkich wymaganych danych historycznych, które są dostępne i będą stale uczyć się i dostosowywać na podstawie nowych danych, aby zapewnić dokładniejsze wartości progowe. Oznacza to, że po obliczeniu, wykres będzie również wyświetlał wzorce cotygodniowe.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Ile danych jest potrzebnych do wyzwolenia alertu?

Jeśli masz nowy zasób lub brakuje danych metryk, dynamiczne progi nie będą wyzwalać alertów przed upływem trzech dni, a co najmniej 30 próbek danych metryk są dostępne, aby zapewnić dokładne progi.
W przypadku istniejących zasobów o wystarczającej ilości danych metryk dynamiczne progi mogą natychmiast wyzwolić alerty.

## <a name="dynamic-thresholds-best-practices"></a>Najlepsze praktyki dotyczące progów dynamicznych

Progi dynamiczne mogą być stosowane do większości platform i metryk niestandardowych w Azure Monitor i zostały również dostrojone dla typowych metryk aplikacji i infrastruktury.
Poniżej przedstawiono najlepsze rozwiązania dotyczące konfigurowania alertów dla niektórych z tych metryk przy użyciu progów dynamicznych.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Progi dynamiczne w metrykach procentowych procesora CPU maszyny wirtualnej

1. W [Azure Portal](https://portal.azure.com)kliknij pozycję **Monitoruj**. Widok monitora konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

2. Kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów zawiera również **alerty** w menu zasobów w obszarze **monitorowanie**, ale można również utworzyć alerty.

3. Kliknij pozycję **Wybierz element docelowy**, w okienku kontekstu, który ładuje, wybierz zasób docelowy, na którym chcesz utworzyć alert. Użyj  listy rozwijanej **Typ zasobów subskrypcja i "Virtual Machines"** , aby znaleźć zasób, który chcesz monitorować. Możesz również użyć paska wyszukiwania, aby znaleźć zasób.

4. Po wybraniu zasobu docelowego kliknij pozycję **Dodaj warunek**.

5. Wybierz **wartość "procent użycia procesora"**.

6. Opcjonalnie możesz ograniczyć metrykę przez dostosowanie **okresu** i **agregacji**. Nie zaleca się używania typu agregacji "maksimum" dla tego typu metryki, ponieważ jest on mniej reprezentatywny dla zachowania. Dla maksymalnego progu statycznego typu agregacji może być bardziej odpowiedni.

7. Zostanie wyświetlony wykres metryki dla ostatnich 6 godzin. Zdefiniuj parametry alertu:
    1. **Typ warunku** — wybierz opcję "dynamiczna".
    1. **Czułość** — wybierz średnią/niską czułość, aby zmniejszyć liczbę szumów alertów.
    1. **Operator** — wybierz opcję "większe niż", chyba że zachowanie reprezentuje użycie aplikacji.
    1. **Częstotliwość** — Rozważ obniżenie poziomu wpływu alertu na działania biznesowe.
    1. **Okresy zakończone niepowodzeniem** (opcja zaawansowana) — okno wyszukiwania powinno wynosić co najmniej 15 minut. Na przykład jeśli okres jest ustawiony na pięć minut, okresy zakończone niepowodzeniem powinny wynosić co najmniej trzy.

8. Na wykresie metryki zostaną wyświetlone obliczone progi na podstawie ostatnich danych.

9. Kliknij przycisk **Gotowe**.

10. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.

11. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

12. Kliknij przycisk **gotowe** , aby zapisać regułę alertu metryki.

> [!NOTE]
> Reguły alertów metryk utworzonych za pomocą portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Progi dynamiczne na Application Insights czasie wykonywania żądania HTTP

1. W [Azure Portal](https://portal.azure.com)kliknij pozycję **Monitoruj**. Widok monitora konsoliduje wszystkie ustawienia monitorowania i dane w jednym widoku.

2. Kliknij pozycję **alerty** , a następnie kliknij pozycję **+ Nowa reguła alertu**.

    > [!TIP]
    > Większość bloków zasobów zawiera również **alerty** w menu zasobów w obszarze **monitorowanie**, ale można również utworzyć alerty.

3. Kliknij pozycję **Wybierz element docelowy**, w okienku kontekstu, który ładuje, wybierz zasób docelowy, na którym chcesz utworzyć alert. Użyj  listy rozwijanej **Typ zasobów subskrypcja i "Application Insights"** , aby znaleźć zasób, który chcesz monitorować. Możesz również użyć paska wyszukiwania, aby znaleźć zasób.

4. Po wybraniu zasobu docelowego kliknij pozycję **Dodaj warunek**.

5. Wybierz pozycję **"czas wykonywania żądania HTTP"**.

6. Opcjonalnie możesz ograniczyć metrykę przez dostosowanie **okresu** i **agregacji**. Nie zaleca się używania typu agregacji "maksimum" dla tego typu metryki, ponieważ jest on mniej reprezentatywny dla zachowania. Dla maksymalnego progu statycznego typu agregacji może być bardziej odpowiedni.

7. Zostanie wyświetlony wykres metryki dla ostatnich 6 godzin. Zdefiniuj parametry alertu:
    1. **Typ warunku** — wybierz opcję "dynamiczna".
    1. **Operator** — wybierz opcję "większe niż", aby zmniejszyć liczbę alertów, które zostały wyzwolone w czasie trwania.
    1. **Częstotliwość** — Rozważ obniżenie poziomu wpływu alertu na działania biznesowe.

8. Na wykresie metryki zostaną wyświetlone obliczone progi na podstawie ostatnich danych.

9. Kliknij przycisk **Gotowe**.

10. Wypełnij **szczegóły alertu** , takie jak nazwa, **Opis** i **ważność** **reguły alertu**.

11. Dodaj grupę akcji do alertu, wybierając istniejącą grupę akcji lub tworząc nową grupę akcji.

12. Kliknij przycisk **gotowe** , aby zapisać regułę alertu metryki.

> [!NOTE]
> Reguły alertów metryk utworzonych za pomocą portalu są tworzone w tej samej grupie zasobów co zasób docelowy.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretowanie wykresów progów dynamicznych

Poniżej znajduje się wykres przedstawiający metrykę, jej limity progów dynamicznych, a niektóre alerty są wywoływane, gdy wartość wykracza poza dozwolone progi.

![Dowiedz się więcej o sposobie konfigurowania alertów metryk](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Użyj poniższych informacji, aby zinterpretować poprzedni wykres.

- **Niebieska linia** — rzeczywisty pomiar metryki w czasie.
- **Niebieski zacieniony obszar** — pokazuje dozwolony zakres dla metryki. O ile wartości metryk pozostają w tym zakresie, żaden alert nie zostanie wyświetlony.
- **Niebieskie kropki** — Jeśli klikniesz część wykresu, a następnie umieścisz kursor nad niebieską linią, zobaczysz niebieską kropkę w obszarze kursora pokazującym indywidualną wartość metryki.
- **Wyskakujące okienko z niebieską kropką** — pokazuje zmierzoną wartość metryki (niebieską kropkę) oraz górną i dolną wartość dozwolonego zakresu.  
- **Czerwona kropka z czarnym kółkiem** — pokazuje pierwszą wartość metryki z dozwolonego zakresu. Jest to wartość, która wyzwala alert metryki i umieszcza ją w stanie aktywnym.
- **Czerwone kropki**— wskazuje dodatkowe wartości mierzone poza dozwolonym zakresem. Nie będą wyzwalać dodatkowych alertów metryk, ale alert pozostaje aktywny.
- **Czerwony obszar** — pokazuje czas, w którym wartość metryki mieści się poza dozwolonym zakresem. Alert pozostaje w stanie aktywnym, tak długo, jak kolejne mierzone wartości są poza dozwolonym zakresem, ale nie są wyzwalane żadne nowe alerty.
- **Koniec czerwonego obszaru** — gdy niebieska linia znajduje się w dozwolonych wartościach, czerwona powierzchnia zostaje zatrzymana, a wartość pola przemierzona jest niebieska. Stan alertu metryki, który został wyzwolony w momencie czerwonej kropki z czarnym konturem jest ustawiony na rozwiązane.