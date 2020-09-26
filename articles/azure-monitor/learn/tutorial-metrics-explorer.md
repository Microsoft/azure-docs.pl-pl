---
title: Samouczek — Tworzenie wykresu metryk w Azure Monitor
description: Dowiedz się, jak utworzyć wykres metryki za pomocą Eksploratora metryk platformy Azure.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 26b155484dafac4a63fe2a6c7ff292ee8bbacbc0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317212"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Samouczek: Tworzenie wykresu metryk w Azure Monitor
Eksplorator metryk jest funkcją Azure Monitor w Azure Portal, która umożliwia tworzenie wykresów z wartości metryk, wizualnie skorelowanych trendów oraz badanie skoków i wartości w metrykach. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów platformy Azure lub wykresy z metryk niestandardowych. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wybierz metrykę, dla której chcesz wykreślić wykres
> * Wykonaj różne agregacje wartości metryk
> * Modyfikuj zakres czasu i stopień szczegółowości wykresu

Poniżej znajduje się film wideo, który pokazuje bardziej obszerny scenariusz niż procedura opisana w tym artykule. Jeśli dopiero zaczynasz metryki, zalecamy zapoznanie się z tym artykułem, a następnie wyświetlenie filmu wideo w celu wyświetlenia bardziej szczegółowych informacji. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności w tym samouczku potrzebny jest zasób platformy Azure do monitorowania. Możesz użyć dowolnego zasobu w subskrypcji platformy Azure, który obsługuje metryki. Aby określić, czy zasób obsługuje metryki, przejdź do jego menu w Azure Portal i sprawdź, czy w sekcji **monitorowanie** menu znajduje się opcja **metryki** .


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com) .

## <a name="open-metrics-explorer-and-select-a-scope"></a>Otwórz Eksploratora metryk i wybierz zakres
Eksploratora metryk można otworzyć z menu Azure Monitor lub z menu zasobów w Azure Portal. Metryki ze wszystkich zasobów są dostępne niezależnie od używanej opcji. 

1. Wybierz pozycję **metryki** z menu **Azure monitor** lub z sekcji **monitorowanie** menu zasobu.

1. Wybierz **zakres**, który jest zasobem, dla którego chcesz wyświetlić metryki. Zakres jest już wypełniony, jeśli otwarto Eksploratora metryk z menu zasobów.

    ![Wybierz zakres](media/tutorial-metrics-explorer/scope-picker.png)

2. Wybierz **przestrzeń nazw** , jeśli zakres ma więcej niż jeden. Przestrzeń nazw jest tylko sposobem organizowania metryk, aby można je było łatwo znaleźć. Na przykład konta magazynu mają oddzielne przestrzenie nazw do przechowywania plików, tabel, obiektów blob i metryki kolejek. Wiele typów zasobów ma tylko jedną przestrzeń nazw.

3. Wybierz metrykę z listy dostępnych metryk dla wybranego zakresu i przestrzeni nazw.

    ![Wybierz metrykę](media/tutorial-metrics-explorer/metric-picker.png)

4. Opcjonalnie można zmienić **agregację**metryki. Definiuje, jak wartości metryk będą agregowane w czasie dla wykresu. Na przykład jeśli stopień szczegółowości czasu jest ustawiony na 15 minut, a agregacja jest ustawiona na suma, wówczas każdy punkt na wykresie będzie sumą wszystkich zebranych wartości w każdym segmencie 15 minut.

    ![Zrzut ekranu przedstawia wykres z tytułem suma ruchu przychodzącego dla contosoretailweb.](media/tutorial-metrics-explorer/chart.png)

5. Użyj przycisku **Dodaj metrykę** i powtórz te kroki, jeśli chcesz zobaczyć wiele metryk wykreślonych na tym samym wykresie. W przypadku wielu wykresów w jednym widoku wybierz przycisk **Nowy wykres** .

## <a name="select-a-time-range-and-granularity"></a>Wybierz zakres czasu i stopień szczegółowości

Domyślnie wykres pokazuje ostatnie 24 godziny danych metryk. Za pomocą selektora czasu można zmienić **zakres czasu** dla wykresu lub **szczegółowości czasu** , który definiuje zakres czasu dla każdego punktu danych. Wykres używa określonej agregacji, aby obliczyć wszystkie wartości próbkowane w określonym czasie.

![Zmień panel zakresu czasu](media/tutorial-metrics-explorer/time-picker.png)


Użyj **pędzla czasu** , aby zbadać interesujący obszar wykresu, taki jak skok lub wartość DIP. Umieść wskaźnik myszy na początku obszaru, kliknij i przytrzymaj lewy przycisk myszy, przeciągnij do drugiej strony obszaru i zwolnij przycisk. Wykres powiększy się dla tego zakresu czasu. 

![Pędzel czasu](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Zastosuj filtry i podziały wymiarów
Zapoznaj się z następującymi odwołaniami do zaawansowanych funkcji, które umożliwiają wykonywanie dodatkowej analizy metryk i Identyfikowanie potencjalnych wartości odstających danych.

- [Filtrowanie](../platform/metrics-charts.md#apply-filters-to-charts) umożliwia wybranie wartości wymiarów uwzględnionych na wykresie. Na przykład może być konieczne wyświetlenie tylko pomyślnych żądań podczas wykresów metryki *czas odpowiedzi serwera* . 

- [Podział](../platform/metrics-charts.md#apply-splitting-to-a-chart) kontroluje, czy wykres wyświetla osobne wiersze dla każdej wartości wymiaru, czy agreguje wartości w jednym wierszu. Na przykład, możesz chcieć zobaczyć jeden wiersz dla średniego czasu odpowiedzi dla wszystkich wystąpień serwera lub można potrzebować oddzielnych wierszy dla każdego serwera. 

Zobacz [przykłady wykresów](../platform/metric-chart-samples.md) z zastosowanym filtrowaniem i podziałem.

## <a name="advanced-chart-settings"></a>Zaawansowane ustawienia wykresu

Można dostosować styl wykresu, tytuł i zmodyfikować zaawansowane ustawienia wykresu. Po zakończeniu dostosowywania przypnij go do pulpitu nawigacyjnego, aby zapisać swoją służbę. Alerty metryk można również konfigurować. Zobacz [Zaawansowane funkcje usługi Azure Eksplorator metryk](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) , aby dowiedzieć się więcej o tych i innych zaawansowanych funkcjach Azure monitor Eksploratorze metryk.


## <a name="next-steps"></a>Następne kroki
Teraz, gdy wiesz już, jak korzystać z metryk w Azure Monitor, Dowiedz się, jak wysyłać aktywne alerty przy użyciu metryk.

> [!div class="nextstepaction"]
> [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi w usłudze Azure Monitor](../platform/alerts-metric.md)

