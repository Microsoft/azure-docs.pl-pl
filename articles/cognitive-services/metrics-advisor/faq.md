---
title: Często zadawane pytania dotyczące klasyfikatora metryk
titleSuffix: Azure Cognitive Services
description: Często zadawane pytania dotyczące usługi Advisor metryk.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: mbullwin
ms.openlocfilehash: 6b5292ca7e1220b60b1b2a2501b3150550da8db9
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131687"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Często zadawane pytania dotyczące klasyfikatora metryk

### <a name="what-is-the-cost-of-my-instance"></a>Jaki jest koszt mojego wystąpienia?

Nie ma obecnie kosztu na korzystanie z Twojego wystąpienia w wersji zapoznawczej.

### <a name="why-is-the-demo-website-readonly"></a>Dlaczego demonstracyjna witryna sieci Web jest tylko do odczytu?

[Demonstracyjna witryna sieci Web](https://anomaly-detector.azurewebsites.net/) jest publicznie dostępna. To wystąpienie jest tylko do odczytu, aby zapobiec przypadkowemu przekazywaniu jakichkolwiek danych.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Dlaczego nie mogę utworzyć zasobu? "Warstwa cenowa" jest niedostępna i mówi "utworzono już 1 S0 dla tej subskrypcji"?

:::image type="content" source="media/pricing.png" alt-text="Komunikat, gdy istnieje już zasób F0":::

W publicznej wersji zapoznawczej można utworzyć tylko jedno wystąpienie klasyfikatora metryk dla regionu w ramach subskrypcji.

Jeśli masz już utworzone wystąpienie w tym samym regionie, w którym jest używana ta sama subskrypcja, możesz wypróbować inny region lub inną subskrypcję, aby utworzyć nowe wystąpienie. Możesz również usunąć istniejące wystąpienie, aby utworzyć nowe.

Jeśli istniejące wystąpienie zostało już usunięte, ale nadal widzisz błąd, przed utworzeniem nowego wystąpienia Zaczekaj około 20 minut od usunięcia zasobu.

## <a name="basic-concepts"></a>Podstawowe pojęcia

### <a name="what-is-multi-dimensional-time-series-data"></a>Co to jest wielowymiarowe dane szeregów czasowych?

Zobacz [wielowymiarową definicję metryki](glossary.md#multi-dimensional-metric)  w słowniku.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Ile danych jest potrzebnych do uruchomienia usługi klasyfikator metryk w celu rozpoczęcia wykrywania anomalii?

Co najmniej jeden punkt danych może wyzwolić wykrywanie anomalii. Nie przynosi to jednak najlepszej dokładności. Usługa przyjmie okno poprzednich punktów danych przy użyciu wartości określonej jako reguła "Fill-Gap" podczas tworzenia źródła danych.

Zalecamy posiadanie pewnych danych przed upływem sygnatury czasowej, która ma zostać wykrycie.
Na podstawie stopnia szczegółowości danych zalecana ilość danych różni się w zależności od tego, co podano poniżej.

| Poziom szczegółowości | Zalecana ilość danych do wykrycia |
| ----------- | ------------------------------------- |
| Mniej niż 5 minut | 4 dni danych |
| 5 minut do 1 dnia | 28 dni danych |
| Więcej niż 1 dzień, do 31 dni | 4 lata na dane |
| Więcej niż 31 dni | 48 lat danych |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Dlaczego klasyfikator metryk nie wykrywa anomalii z danych historycznych?

Usługa Advisor Metrics została zaprojektowana do wykrywania danych przesyłanych strumieniowo na żywo. Istnieje ograniczenie maksymalnej długości danych historycznych, które usługa będzie wyszukiwać z powrotem i uruchamiania wykrywania anomalii. Oznacza to, że tylko punkty danych po pewnym najwcześniejszym znaczniku czasu będą miały wyniki wykrywania anomalii. Najwcześniejsza sygnatura czasowa zależy od stopnia szczegółowości danych.

Na podstawie stopnia szczegółowości danych dane historyczne, które mają wynik wykrywania anomalii, są następujące.

| Poziom szczegółowości | Maksymalna długość danych historycznych na potrzeby wykrywania anomalii |
| ----------- | ------------------------------------- |
| Mniej niż 5 minut | Czas dołączania — 13 godzin |
| 5 minut do mniej niż 1 godzina | Czas dołączania — 4 dni  |
| 1 godzina do mniej niż 1 dzień | Czas dołączania — 14 dni  |
| 1 dzień | Czas dołączania — 28 dni  |
| Więcej niż 1 dzień, mniej niż 31 dni | Czas dołączania — 2 lata  |
| Więcej niż 31 dni | Czas dołączania — 24 lata   |

### <a name="more-concepts-and-technical-terms"></a>Więcej pojęć i warunków technicznych

Zobacz też [słownik](glossary.md) , aby uzyskać więcej informacji.

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Jak mogę zapisać prawidłowe zapytanie na potrzeby pozyskiwania danych?  

Aby móc pozyskać dane z usługi Metric Advisor, należy utworzyć zapytanie, które zwraca wymiary danych przy użyciu pojedynczej sygnatury czasowej. Doradca metryk uruchomi to zapytanie wiele razy, aby pobrać dane z każdego sygnatury czasowej. 

Należy zauważyć, że zapytanie powinno zwrócić co najwyżej jeden rekord dla każdej kombinacji wymiarów w danym znaczniku czasu. Wszystkie zwrócone rekordy muszą mieć taką samą sygnaturę czasową. Zapytanie nie powinno zawierać zduplikowanych rekordów.

Załóżmy na przykład, że tworzysz zapytanie poniżej, dla metryki dziennej: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Pamiętaj, aby użyć poprawnego stopnia szczegółowości dla szeregów czasowych. W przypadku metryki godzinowej można użyć: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Zwróć uwagę, że te zapytania zwracają tylko dane z pojedynczej sygnatury czasowej i zawierają wszystkie kombinacje wymiarów do pozyskiwania przez klasyfikatora metryk. 

:::image type="content" source="media/query-result.png" alt-text="Komunikat, gdy istnieje już zasób F0" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Jak mogę wykryć skoki & spadnie jako anomalie?

Jeśli masz wstępnie zdefiniowane progi, możesz w rzeczywistości ręcznie ustawić "próg twardy" w [konfiguracjach wykrywania anomalii](how-tos/configure-metrics.md#anomaly-detection-methods).
Jeśli nie ma żadnych progów, można użyć "Inteligentne wykrywanie", które jest obsługiwane przez AI. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Dostosowywanie konfiguracji wykrywania](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Jak mogę wykryć niezgodność ze zwykłymi (sezonowymi) wzorcami jako anomalie?

"Inteligentne wykrywanie" jest w stanie poznać wzorzec danych, w tym wzorce sezonowe. Następnie wykrywa te punkty danych, które nie są zgodne ze zwykłymi wzorcami jako anomalie. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Dostosowywanie konfiguracji wykrywania](how-tos/configure-metrics.md#tune-the-detecting-configuration) .

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Jak mogę wykryć płaskie linie jako anomalie?

Jeśli dane są zwykle dość niestabilne i wymagają dużej ilości danych, a użytkownik chce otrzymywać alerty, gdy zostanie on zbyt stabilny lub nawet stanie się linią płaską, "próg zmiany" można skonfigurować do wykrywania takich punktów danych, gdy zmiana jest zbyt mała.
Szczegóły można znaleźć w [konfiguracjach wykrywania anomalii](how-tos/configure-metrics.md#anomaly-detection-methods) .

## <a name="advanced-concepts"></a>Pojęcia zaawansowane

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Jak usługa Metric Advisor kompiluje drzewo zdarzeń dla metryk wielowymiarowych?

Metrykę można podzielić na wiele szeregów czasowych według wymiarów. Na przykład Metryka `Response latency` jest monitorowana dla wszystkich usług należących do zespołu. `Service`Kategoria może być używana jako wymiar do wzbogacania metryki, więc zostanie `Response latency` podzielna przez `Service1` , `Service2` i tak dalej. Każdą usługę można wdrożyć na różnych maszynach w wielu centrach danych, dzięki czemu Metryka może być dodatkowo podzielona przez `Machine` i `Data center` .

|Usługa| Centrum danych| Maszyna  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M |
| S1 |  DC2 |   W3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

Rozpoczynając od całkowitej `Response latency` , możemy przejść do szczegółów metryki według `Service` , `Data center` i `Machine` . Jednak może być bardziej zrozumiałe dla właścicieli usługi, aby mógł używać ścieżki `Service`  ->  `Data center`  ->  `Machine` , lub może być bardziej zrozumiałe dla inżynierów infrastruktury, aby używały ścieżki `Data Center`  ->  `Machine`  ->  `Service` . Wszystko to zależy od konkretnych wymagań firmy użytkowników. 

W usłudze Metric Advisor użytkownicy mogą określić dowolną ścieżkę do przechodzenia do szczegółów lub zestawiania z jednego węzła topologii hierarchicznej. Dokładniej, hierarchiczna topologia jest ukierunkowanym wykresem acykliczne, a nie strukturą drzewa. Istnieje pełna topologia hierarchiczna, która składa się ze wszystkich możliwych kombinacji wymiarów, takich jak: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="Komunikat, gdy istnieje już zasób F0" lightbox="media/dimension-combinations-view.png":::

Teoretycznie, jeśli wymiar `Service` ma `Ls` różne wartości, wymiar `Data center` ma `Ldc` różne wartości, a wymiar `Machine` ma `Lm` różne wartości, a następnie `(Ls + 1) * (Ldc + 1) * (Lm + 1)` w topologii hierarchicznej mogą istnieć kombinacje wymiarów. 

Ale zazwyczaj nie wszystkie kombinacje wymiarów są prawidłowe, co może znacząco zmniejszyć złożoność. Obecnie Jeśli użytkownicy łączą samą metrykę, nie ograniczamy liczby wymiarów. Jeśli musisz użyć funkcji ROLLUP dostarczonych przez klasyfikator metryk, liczba wymiarów nie powinna być większa niż 6. Jednak ograniczamy liczbę szeregów czasowych rozwiniętych przez wymiary dla metryki do mniej niż 10 000.

Narzędzie **drzewo zdarzeń** na stronie Diagnostyka zawiera tylko węzły, w których wykryto anomalię, a nie całą topologię. Jest to pomocne w skoncentrowaniu się na bieżącym problemie. Nie można także pokazać wszystkich anomalii w ramach metryki, a zamiast tego zostaną wyświetlone najważniejsze anomalie na podstawie udziału. Dzięki temu możemy szybko sprawdzić wpływ, zakres i ścieżkę rozmieszczenia nietypowych danych. Znacznie zmniejsza liczbę anomalii, na których należy się skoncentrować, i ułatwia użytkownikom zrozumienie i lokalizowanie kluczowych problemów. 
 
Na przykład jeśli wystąpi anomalia `Service = S2 | Data Center = DC2 | Machine = M5` , odchylenie anomalii wpływa na węzeł nadrzędny `Service= S2` , który również wykrył anomalię, ale anomalia nie ma wpływu na całe centrum danych na `DC2` i wszystkie usługi na `M5` . Drzewo zdarzeń zostanie skompilowane jak na poniższym zrzucie ekranu, Górna anomalia zostanie przechwycona `Service = S2` , a główna przyczyna może zostać przeanalizowana w dwóch ścieżkach, do których oba powodują `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 oznaczone wierzchołkami z dwiema unikatowymi ścieżkami połączonymi przez krawędzie ze wspólnym węzłem o nazwie S2. Najczęstsze anomalie są przechwytywane w usłudze = S2, a główna przyczyna może być analizowana przez dwie ścieżki, które prowadzą do usługi = S2 | Centrum danych = DC2 | Machine = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Następne kroki
- [Omówienie klasyfikatora metryk](overview.md)
- [Wypróbuj witrynę demonstracyjną](quickstarts/explore-demo.md)
- [Używanie portalu internetowego](quickstarts/web-portal.md)