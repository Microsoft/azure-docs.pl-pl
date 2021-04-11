---
title: Wprowadzenie do Eksploratora metryk platformy Azure
description: Dowiedz się, jak utworzyć pierwszy wykres metryki za pomocą Eksploratora metryk platformy Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.openlocfilehash: df745e7612dbd5b5bb9029b89d7f74974270c2d1
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962717"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Wprowadzenie do Eksploratora metryk platformy Azure

## <a name="where-do-i-start"></a>Od czego zacząć
Eksplorator metryk Azure Monitor jest składnikiem Microsoft Azure Portal, który umożliwia Wykreślanie wykresów, wizualnie skorelowanych trendów oraz badanie i przechodzenie w wartości metryk. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów. Rozpocznij w następującej kolejności:

1. [Wybierz zasób i metrykę](#create-your-first-metric-chart) , a zobaczysz wykres podstawowy. Następnie [Wybierz zakres czasu](#select-a-time-range) , który jest istotny dla danego badania.

1. Spróbuj [zastosować filtry wymiarów i je podzielić](#apply-dimension-filters-and-splitting). Filtry i podział umożliwiają analizowanie segmentów metryk współtworzące ogólną wartość metryki i identyfikowanie możliwych wartości odstających.

1. Użyj [ustawień zaawansowanych](#advanced-chart-settings) , aby dostosować wykres przed przypinaniem do pulpitów nawigacyjnych. [Skonfiguruj alerty](../alerts/alerts-metric-overview.md) , aby otrzymywać powiadomienia, gdy wartość metryki przekracza lub spadnie poniżej wartości progowej.

## <a name="create-your-first-metric-chart"></a>Tworzenie pierwszego wykresu metryki

Aby utworzyć wykres metryki, z poziomu zasobu, grupy zasobów, subskrypcji lub Azure Monitor widoku, Otwórz kartę **metryki** i wykonaj następujące kroki:

1. Kliknij przycisk "Wybierz zakres", aby otworzyć selektor zakresu zasobów. Umożliwi to wybranie zasobów, dla których mają być wyświetlane metryki. Zasób należy już wypełnić, jeśli otwarto Eksploratora metryk z menu zasobów. Aby dowiedzieć się, jak wyświetlać metryki w wielu zasobach, [Przeczytaj ten artykuł](./metrics-dynamic-scope.md).
    > ![Wybieranie zasobu](./media/metrics-getting-started/scope-picker.png)

2. W przypadku niektórych zasobów należy wybrać przestrzeń nazw. Przestrzeń nazw jest tylko sposobem organizowania metryk, aby można je było łatwo znaleźć. Na przykład konta magazynu mają oddzielne przestrzenie nazw do przechowywania plików, tabel, obiektów blob i metryki kolejek. Wiele typów zasobów ma tylko jedną przestrzeń nazw.

3. Wybierz metrykę z listy dostępnych metryk.

    > ![Wybierz metrykę](./media/metrics-getting-started/metrics-dropdown.png)

4. Opcjonalnie można [zmienić agregację metryki](../essentials/metrics-charts.md#aggregation). Na przykład możesz chcieć, aby wykres pokazywał minimalną, maksymalną lub średnią wartość metryki.

> [!TIP]
> Użyj przycisku **Dodaj metrykę** i powtórz te kroki, jeśli chcesz zobaczyć wiele metryk wykreślonych na tym samym wykresie. W przypadku wielu wykresów w jednym widoku wybierz przycisk **Dodaj wykres** u góry.

## <a name="select-a-time-range"></a>Wybierz zakres czasu

> [!WARNING]
> [Większość metryk na platformie Azure jest przechowywanych przez 93 dni](../essentials/data-platform-metrics.md#retention-of-metrics). Można jednak wykonywać zapytania o nie więcej niż 30 dni danych na jednym wykresie. Możesz [przesunąć](metrics-charts.md#pan) wykres, aby wyświetlić pełne przechowywanie. Ograniczenie 30-dniowe nie ma zastosowania do [metryk opartych na dziennikach](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

Domyślnie wykres pokazuje ostatnie 24 godziny danych metryk. Aby zmienić zakres czasu, powiększyć lub pomniejszyć wykres, użyj panelu **wyboru czasu** . 

![Zmień panel zakresu czasu](./media/metrics-getting-started/time.png)

> [!TIP]
> Użyj **pędzla czasu** , aby zbadać interesujący obszar wykresu (zbiór lub DIP). Umieść wskaźnik myszy na początku obszaru, kliknij i przytrzymaj lewy przycisk myszy, przeciągnij do drugiej strony obszaru, a następnie zwolnij przycisk. Wykres powiększy się dla tego zakresu czasu. 

## <a name="apply-dimension-filters-and-splitting"></a>Zastosuj filtry i podziały wymiarów

[Filtrowanie](../essentials/metrics-charts.md#filters) i [dzielenie](../essentials/metrics-charts.md#apply-splitting) to zaawansowane narzędzia diagnostyczne dla metryk, które mają wymiary. Te funkcje pokazują, jak różne segmenty metryk ("wartości wymiaru") wpływają na ogólną wartość metryki i umożliwiają identyfikowanie możliwych wartości odstających.

- **Filtrowanie** umożliwia wybranie wartości wymiarów uwzględnionych na wykresie. Na przykład możesz chcieć pokazać pomyślne żądania podczas grafowania metryki *czas odpowiedzi serwera* . Należy zastosować filtr dla *sukcesu wymiaru żądania* . 

- **Podział** kontroluje, czy wykres wyświetla osobne wiersze dla każdej wartości wymiaru, czy agreguje wartości w jednym wierszu. Na przykład można zobaczyć jeden wiersz dla średniego czasu odpowiedzi dla wszystkich wystąpień serwera lub zobaczyć oddzielne wiersze dla każdego serwera. Należy zastosować podział w wymiarze *wystąpienia serwera* , aby zobaczyć oddzielne wiersze.

Zobacz [przykłady wykresów](../essentials/metric-chart-samples.md) z zastosowanym filtrowaniem i podziałem. W tym artykule przedstawiono kroki, które zostały użyte do skonfigurowania wykresów.

## <a name="share-your-metric-chart"></a>Udostępnianie wykresu metryki
Istnieją obecnie dwa sposoby udostępniania wykresu metryki. Poniżej znajdują się instrukcje dotyczące sposobu udostępniania informacji z wykresów metryk za pośrednictwem programu Excel i łącza.
 
### <a name="download-to-excel"></a>Pobierz do programu Excel
Kliknij pozycję "Udostępnij" i wybierz pozycję "Pobierz do programu Excel". Pobieranie powinno zacząć się od razu.

![zrzut ekranu przedstawiający sposób udostępniania wykresu metryki za pośrednictwem programu Excel](./media/metrics-getting-started/share-excel.png)

### <a name="share-a-link"></a>Udostępnianie linku
Kliknij pozycję "Udostępnij" i wybierz pozycję "Kopiuj link". Należy otrzymać powiadomienie, że łącze zostało pomyślnie skopiowane.

![zrzut ekranu przedstawiający sposób udostępniania wykresu metryki za pośrednictwem linku](./media/metrics-getting-started/share-link.png)


## <a name="advanced-chart-settings"></a>Zaawansowane ustawienia wykresu

Można dostosować styl wykresu, tytuł i zmodyfikować zaawansowane ustawienia wykresu. Po zakończeniu dostosowywania przypnij go do pulpitu nawigacyjnego, aby zapisać swoją służbę. Alerty metryk można również konfigurować. Zapoznaj się z [dokumentacją produktu](../essentials/metrics-charts.md) , aby dowiedzieć się więcej na temat tych i innych zaawansowanych funkcji eksploratora metryk Azure monitor.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat zaawansowanych funkcji Eksplorator metryk](../essentials/metrics-charts.md)
* [Wyświetlanie wielu zasobów w Eksplorator metryk](./metrics-dynamic-scope.md)
* [Rozwiązywanie problemów z Eksploratorem metryk](metrics-troubleshoot.md)
* [Zobacz listę dostępnych metryk dla usług platformy Azure](./metrics-supported.md)
* [Zobacz przykłady skonfigurowanych wykresów](../essentials/metric-chart-samples.md)
