---
title: Diagnozowanie zdarzeń przy użyciu klasyfikatora metryk
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zdiagnozować zdarzenia za pomocą klasyfikatora metryk i uzyskać szczegółowe widoki anomalii w danych.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: ecbfb2d9acf6c62f95c264a14e306442db25e483
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703426"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Instrukcje: diagnozowanie zdarzenia przy użyciu klasyfikatora metryk

Klasyfikator metryk zapewnia kilka funkcji diagnostyki oraz zapewnia szczegółowy widok wykrytych zdarzeń i umożliwia analizę głównych przyczyn. Gdy w metryce wykryto grupę anomalii, klasyfikator metryk będzie grupować anomalie w hierarchię i analizować ją na jej podstawie.

> [!NOTE]
> Obecnie klasyfikator metryk obsługuje diagnostykę zdarzeń dla metryk z co najmniej jednym wymiarem i mierzy przy użyciu typu  *liczbowego* . Metryka musi mieć zagregowaną wartość wymiaru, taką jak suma dla każdego wymiaru, która jest używana do tworzenia hierarchii diagnostyki. Klasyfikator metryk oferuje [**Automatyczne ustawienia zbiorcze**](onboard-your-data.md#automatic-roll-up-settings) , które pomagają generować zagregowane wartości. 

Kliknij pozycję **centrum zdarzeń** w okienku nawigacji po lewej stronie, aby zobaczyć wszystkie incydenty w ramach danej metryki. W górnej części strony można wybrać różne metryki, aby wyświetlić ich konfiguracje wykrywania oraz wyniki wykrywania i zmienić zakres czasu.

> [!TIP]
> Możesz również przejść do **centrum zdarzeń** , aby:
> * Kliknięcie punktu danych w wizualizacji dla metryki i użycie linków w dolnej części okna **Dodaj opinię** .
> * Kliknięcie jednego z anomalii na karcie **incydenty** dla metryki. 

Sekcja **Przegląd** zawiera wyniki wykrywania, w tym liczbę anomalii i alertów w ramach wybranego zakresu czasu.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Centrum zdarzeń" lightbox="../media/diagnostics/incident-hub-overview.png":::

Wykryte zdarzenia w ramach wybranej metryki i zakresu czasu są wymienione na **liście incydentów**. Dostępne są opcje filtrowania i porządkowania zdarzeń. Na przykład według ważności. Kliknij jedno ze zdarzeń, aby przejść do strony **incydentu** w celu przeprowadzenia dalszej diagnostyki.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Lista zdarzeń" lightbox="../media/diagnostics/incident-list.png":::

Sekcja **Diagnostic** umożliwia wykonywanie szczegółowych analiz dotyczących zdarzeń oraz narzędzi do identyfikowania głównych przyczyn.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Diagnozowanie zdarzenia" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Porady dotyczące przyczyny głównej

Gdy grupa anomalii zostanie wykryta w metryce i powoduje zdarzenie, Doradca metryk spróbuje przeanalizować główną przyczynę zdarzenia. **Porady dotyczące głównej przyczyny** zawierają automatyczne sugestie dotyczące możliwych przyczyn zdarzenia. Ta funkcja jest dostępna tylko wtedy, gdy w wymiarze jest zagregowana wartość. Jeśli Metryka nie ma wymiaru, główna przyczyna będzie sama. Główne przyczyny są wymienione w panelu po prawej stronie i może być wymienionych kilka przyczyn. Jeśli w tabeli nie ma żadnych danych, oznacza to, że wymiar nie spełnia wymagań do przeprowadzenia analizy.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Porady dotyczące przyczyny głównej":::


W przypadku podanej metryki głównej przyczyny z określonymi wymiarami można kliknąć pozycję **Przejdź do metryki** , aby wyświetlić więcej szczegółów dotyczących metryki.

## <a name="incident-tree"></a>Drzewo zdarzeń

Wraz z automatyczną analizą potencjalnych przyczyn głównych, klasyfikator metryk obsługuje ręczną analizę przyczyn głównych, przy użyciu **drzewa zdarzeń**. Na stronie incydentu są dwa rodzaje zdarzeń: drzewo **Szybkie diagnozowanie** i **drzewo interaktywne**.

Drzewo szybkiej diagnostyki służy do diagnozowania bieżącego zdarzenia, a węzeł główny jest ograniczony do bieżącego węzła głównego zdarzenia. Węzły drzewa można rozwijać i zwijać, klikając je, a ich seria będzie wyświetlana wraz z bieżącą serią zdarzeń na wykresie powyżej drzewa.

Interaktywne drzewo pozwala zdiagnozować bieżące incydenty, a także zdarzenia, które są ze sobą powiązane. W przypadku korzystania z drzewa interaktywnego kliknij prawym przyciskiem myszy węzeł, aby otworzyć menu akcji, w którym można wybrać wymiar do przechodzenia do szczegółów za pośrednictwem węzłów głównych, a wymiar do przechodzenia do szczegółów dla każdego węzła. Klikając przycisk Anuluj na liście wymiarów u góry, można usunąć drążenie w górę lub w dół z tego wymiaru. po lewej stronie kliknij węzeł, aby go zaznaczyć i wyświetlić jego serię wraz z bieżącą serią zdarzeń na wykresie.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Drzewo zdarzeń" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Przechodzenie do szczegółów

Podczas przeglądania informacji o zdarzeniach może być konieczne uzyskanie bardziej szczegółowych informacji, na przykład dla różnych wymiarów i sygnatur czasowych. Jeśli dane zawierają co najmniej jeden wymiar, można użyć funkcji przechodzenia do szczegółów w celu uzyskania bardziej szczegółowego widoku. 

Aby użyć funkcji przechodzenia do szczegółów, kliknij kartę **drążenie metryczne** w **centrum zdarzeń**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Drążenie metryczne ":::

Ustawienie **Wymiary** to lista wymiarów dla incydentu. można wybrać inne dostępne wartości wymiarów dla każdej z nich. Po zmianie wartości wymiarów. Ustawienie **sygnatury czasowej** umożliwia wyświetlenie bieżącego incydentu w różnych momentach.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Wybierz opcje drążenia i wybierz wymiar

Dostępne są dwa typy opcji przechodzenia do szczegółów: **drążenie** i **porównywanie w poziomie**.

> [!Note]
> 1. W celu przechodzenia do szczegółów można eksplorować dane z różnych wartości wymiarów, z wyjątkiem wybranych wymiarów currenly. 
> 2. W przypadku porównania w poziomie można eksplorować dane z różnych wartości wymiarów, z wyjątkiem wymiarów wszystkich.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Wymiar przechodzenia do szczegółów":::

### <a name="value-comparison-for-different-dimension-values"></a>Porównanie wartości dla różnych wartości wymiarów

Druga sekcja karty przechodzenie do szczegółów jest tabelą z porównaniami dla różnych wartości wymiarów. Obejmuje ona wartość, wartość bazową, wartość różnicy, wartość Delta oraz określa, czy jest to anomalia.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Porównanie przechodzenia do szczegółów" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Porównania wartości i oczekiwanych wartości dla różnych wartości wymiarów

Trzecią sekcję karty przechodzenie do szczegółów jest histogram z wartościami i oczekiwanymi wartościami dla różnych wartości wymiarów. Histogram jest posortowany według różnicy między wartością a wartością oczekiwaną. Nieoczekiwana wartość można łatwo znaleźć z największym wpływem. Na przykład w powyższym obrazie możemy znaleźć ten, z wyjątkiem wartości wszystkie, **US7** przyczynia się do najbardziej nietypowego wystąpienia.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Przechodzenie do szczegółów tabeli" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Wizualizacja wartości nieprzetworzonej
Ostatnia część karty przechodzenie do szczegółów to wykres liniowy dla nieprzetworzonych wartości. Po podaniu tego wykresu nie musisz przechodzić do strony metryki, aby wyświetlić szczegóły.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Wykres liniowy przechodzenia do szczegółów" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Wyświetlanie podobnych anomalii przy użyciu klastrów szeregów czasowych

Podczas przeglądania incydentu można użyć **podobnej karty z seriami czasowymi** , aby zobaczyć, która z nich jest skojarzona. Serie w jednej grupie są sumowane razem. Z powyższego Zdjęcia możemy wiedzieć, że istnieje co najmniej dwie grupy serii. Ta funkcja jest dostępna tylko wtedy, gdy spełnione są następujące wymagania:

1. Metryki muszą mieć co najmniej jeden wymiar lub wartość wymiaru.
2. Seria w ramach jednej metryki musi mieć podobny trend.

Dostępne wymiary są wyświetlane na górze karty i można wybrać opcję określania serii.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Grupa serii":::

## <a name="compare-time-series"></a>Porównanie szeregów czasowych

Czasami w przypadku wykrycia anomalii w określonej szeregu czasowym warto porównać ją z wieloma innymi seriami w pojedynczej wizualizacji. Kliknij kartę **PORÓWNAJ narzędzia** , a następnie kliknij przycisk niebieski **+ Dodaj** . 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Dodaj serię do porównania" lightbox="../media/diagnostics/add-series.png":::

Wybierz serię ze strumieniowego źródła danych. Możesz wybrać ten sam poziom szczegółowości lub inny. Wybierz wymiary docelowe i Załaduj trend serii, a następnie kliknij przycisk **OK** , aby porównać go z poprzednią serią. Seria zostanie umieszczona w jednej wizualizacji. Możesz nadal dodawać kolejne serie do porównania i uzyskiwać dokładniejsze informacje. Kliknij menu rozwijane w górnej części karty narzędzia do **porównywania** , aby porównać dane szeregów czasowych w okresie przenoszonym przez czas.  

> [!Warning]
> Aby dokonać porównania, analiza danych szeregów czasowych może wymagać zmian w punktach danych, dzięki czemu stopień szczegółowości danych musi go obsługiwać. Na przykład jeśli dane są cotygodniowe i używasz porównania **dziennego dnia** , nie otrzymasz żadnych wyników. W tym przykładzie zamiast tego należy użyć porównania **miesiąc do miesiąca** .

Po wybraniu porównania przesuniętego czasowo możesz wybrać, czy chcesz porównać wartości danych, wartości różnicowe czy różnice procentowe.

> [!Note]
> * **Wartość danych** to wartość danych pierwotnych.
> * **Wartość Delta** jest różnicą między wartością nieprzetworzoną a porównaną wartością.
> * **Wartość procentowa różnic** jest różnica między wartością nieprzetworzoną a porównaną wartością podzieloną przez porównaną wartość.

## <a name="related-incidents-across-metrics"></a>Zdarzenia powiązane między metrykami

Czasami może być konieczne sprawdzenie zdarzeń różnych metryk w tym samym czasie lub powiązanych zdarzeń w innych metrykach. Listę powiązanych zdarzeń można znaleźć w sekcji **Analiza krzyżowych** operacji. 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="zdarzenia powiązane między metrykami":::

Aby można było zobaczyć powiązane incydenty dla bieżącej metryki, należy dodać relację między metrykami. Kliknij pozycję **metryki ustawienia wykresu** , aby dodać relację. Powiązane są tylko metryki o tych samych nazwach wymiarów. Użyj poniższych parametrów.

- Bieżące strumieniowe źródło danych & metryki: strumieniowe źródło danych i metryka bieżącego zdarzenia
- Kierunek: kierunek relacji między dwiema metrykami. (nie ma teraz wpływu na listę powiązanych zdarzeń)
- Inne strumieniowe źródło danych & metryki: strumieniowe źródło danych i metryka do połączenia z bieżącą metryką


## <a name="next-steps"></a>Następne kroki 

- [Dostosowywanie wykrywania anomalii przy użyciu opinii](anomaly-feedback.md)
- [Konfigurowanie metryk i dostrajanie konfiguracji wykrywania](configure-metrics.md)
