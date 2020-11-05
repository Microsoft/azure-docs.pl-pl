---
title: Oceń wyniki eksperymentu AutoML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i szacować wykresy oraz metryki dla każdego zautomatyzowanego przebiegu eksperymentu w usłudze Machine Learning.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, automl
ms.openlocfilehash: fcbe0fc5049f6e892f80f048a885c75420bc636e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359089"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Oceń automatyczne wyniki eksperymentu w usłudze Machine Learning

W tym artykule dowiesz się, jak wyświetlać i szacować wyniki zautomatyzowanej uczenia maszynowego, zautomatyzowanej ML, eksperymentów. Te eksperymenty składają się z wielu przebiegów, w których każdy przebieg tworzy model. Aby ułatwić ocenę poszczególnych modeli, zautomatyzowanej sieci automatycznie generuje metryki wydajności i wykresy specyficzne dla typu eksperymentu. 

Na przykład, zautomatyzowana ML oferuje różne wykresy dla modeli klasyfikacji i regresji. 

|Klasyfikacja|Regresja
|---|---|
|<li> [Macierz pomyłek](#confusion-matrix) <li>[Wykres dokładności do odzyskania](#precision-recall-chart) <li> [Charakterystyki operacyjne odbiornika (lub ROC)](#roc) <li> [Krzywa podnoszenia](#lift-curve)<li> [Krzywa zysków](#gains-curve)<li> [Wykres kalibracyjny](#calibration-plot) | <li> [Przewidywany a prawdziwy](#pvt) <li> [Histogram reszty](#histo)|

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Utwórz eksperyment dla automatycznego przebiegu uczenia maszynowego z zestawem SDK lub w programie Azure Machine Learning Studio.

    * Użyj zestawu SDK, aby utworzyć [model klasyfikacji](how-to-auto-train-remote.md) lub [model regresji](tutorial-auto-train-models.md)
    * Użyj [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) , aby utworzyć model klasyfikacji lub regresji przez przekazanie odpowiednich danych.

## <a name="view-run-results"></a>Wyświetl wyniki przebiegu

Po zakończeniu eksperymentu automatycznego uczenia maszynowego historia przebiegów będzie dostępna w obszarze roboczym uczenia maszynowego za pośrednictwem [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 

W przypadku eksperymentów zestawu SDK te same wyniki można zobaczyć podczas przebiegu, gdy używasz `RunDetails` [widżetu Jupyter](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py).

Poniższe kroki i animacje pokazują, jak wyświetlić informacje o historii uruchamiania oraz metryki wydajności i wykresy określonego modelu w programie Studio.

![Kroki umożliwiające wyświetlenie historii przebiegu i metryki wydajności modelu oraz wykresy](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

Aby wyświetlić metryki i wykresy wydajności historii uruchamiania i modelu w programie Studio: 

1. [Zaloguj się do programu Studio](https://ml.azure.com/) i przejdź do obszaru roboczego.
1. W lewym panelu obszaru roboczego wybierz pozycję **uruchomienia**.
1. Na liście eksperymentów wybierz ten, który chcesz poznać.
1. W dolnej tabeli wybierz **przebieg**.
1. Na karcie **modele** wybierz **nazwę algorytmu** dla modelu, który chcesz zbadać.
1. Na karcie **metryki** wybierz metryki i wykresy, które chcesz oszacować dla tego modelu. 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>Metryki wydajności klasyfikacji

Poniższa tabela zawiera podsumowanie metryk wydajności modelu, które są obliczane przez automatyczne ML dla każdego modelu klasyfikacji wygenerowanego dla eksperymentu. 

Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--
AUC_macro| AUC to obszar pod krzywą charakterystyczną dla odbiornika. Makro jest średnią arytmetyczną AUC dla każdej klasy.  | [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "Macro"|
AUC_micro| AUC to obszar pod krzywą charakterystyczną dla odbiornika. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie z każdej klasy.| [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Średnia = "mikro"|
AUC_weighted  | AUC to obszar pod krzywą charakterystyczną dla odbiornika. Ważone to średnia arytmetyczna wyniku dla każdej klasy, ważona przez liczbę wystąpień prawdziwych w każdej klasie.| [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|Średnia = "ważone"
accuracy|Dokładność to procent prognozowanych etykiet, które dokładnie pasują do prawdziwych etykiet. |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Brak|
average_precision_score_macro|Średnia precyzja podsumowuje krzywą odwołań dokładności jako średnią ważoną dokładności osiągniętą dla każdego progu, z zwiększeniem odzyskania z poprzedniego progu używanego jako waga. Makro jest średnią arytmetyczną średniego wyniku dokładności dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "Macro"|
average_precision_score_micro|Średnia precyzja podsumowuje krzywą odwołań dokładności jako średnią ważoną dokładności osiągniętą dla każdego progu, z zwiększeniem odzyskania z poprzedniego progu używanego jako waga. Mikro jest obliczany globalnie, łącząc prawdziwe dodatnie i fałszywie dodatnie dla każdego odcięcia.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średnia = "mikro"|
average_precision_score_weighted|Średnia precyzja podsumowuje krzywą odwołań dokładności jako średnią ważoną dokładności osiągniętą dla każdego progu, z zwiększeniem odzyskania z poprzedniego progu używanego jako waga. Ważone jest średnią arytmetyczną średniego oceny dokładności dla każdej klasy, ważonej przez liczbę wystąpień prawdziwych w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Średnia = "ważone"|
balanced_accuracy|Równoważna dokładność to arytmetyczna średnia odwołania dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
f1_score_macro|Wynik F1 jest średnią harmoniczną precyzji i odwołania. Makro jest średnią arytmetyczną wyniku F1 dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "Macro"|
f1_score_micro|Wynik F1 jest średnią harmoniczną precyzji i odwołania. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych dodatnich.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średnia = "mikro"|
f1_score_weighted|Wynik F1 jest średnią harmoniczną precyzji i odwołania. Ważone średnie według częstotliwości klasy wyników F1 dla każdej klasy|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Średnia = "ważone"|
log_loss|Jest to funkcja strat użyta w (MULTINOMIAL) logistyki i rozszerzenia, takie jak sieci neuronowych, zdefiniowane jako negatywna prawdopodobieństwo rejestrowania dla prawdziwych etykiet z przewidywaniami klasyfikatora probabilistyczne. Dla pojedynczego przykładu o prawdziwej etykiecie yt w {0,1} i szacowane prawdopodobieństwo YP, że yt = 1, utrata dziennika to-log P (yt&#124;YP) =-(YT log (YP) + (1-yt) log (1-YP)).|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Brak|
norm_macro_recall|Znormalizowane makro odwołuje się do znormalizowanego, tak aby losowo wydajność miało wynik 0, a doskonałe wydajność ma wynik 1. Jest to osiągane przez norm_macro_recall: = (recall_score_macro-R)/(1-R), gdzie R jest oczekiwaną wartością recall_score_macro dla prognoz losowych (tj. R = 0,5 dla klasyfikacji binarnej i języka R = (1/C) dla błędów klasyfikacji klasy C).|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro" |
precision_score_macro|Precyzja to procent nieprzewidywalnych elementów, które są poprawnie oznaczone etykietami. Makro jest arytmetyczną dokładnością dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "Macro"|
precision_score_micro|Precyzja to procent nieprzewidywalnych elementów, które są poprawnie oznaczone etykietami. Mikro jest obliczany globalnie przez obliczenie całkowitej liczby pozytywnych dodatnich i fałszywych wartości dodatnich.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średnia = "mikro"|
precision_score_weighted|Precyzja to procent nieprzewidywalnych elementów, które są poprawnie oznaczone etykietami. Ważone jest średnią arytmetyczną dokładności dla każdej klasy, ważonej według liczby prawdziwych wystąpień w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Średnia = "ważone"|
recall_score_macro|Funkcja odwoływania jest wartością procentową poprawnie oznaczonych elementów pewnej klasy. Makro jest średnią arytmetyczną operacji odwoływania dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "Macro"|
recall_score_micro|Funkcja odwoływania jest wartością procentową poprawnie oznaczonych elementów pewnej klasy. Mikro jest obliczany globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych wartości dodatnich|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średnia = "mikro"|
recall_score_weighted|Funkcja odwoływania jest wartością procentową poprawnie oznaczonych elementów pewnej klasy. Ważone to średnia arytmetyczna operacji odwoływania dla każdej klasy, ważona według liczby prawdziwych wystąpień w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Średnia = "ważone"|
weighted_accuracy|Waga ważona jest dokładnością, gdzie waga określona dla każdego przykładu jest równa proporcji prawdziwe wystąpienia w tym przykładzie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight to wektor równy proporcji tej klasy dla każdego elementu w elemencie docelowym|

### <a name="binary-vs-multiclass-metrics"></a>Dane binarne a wieloklasowe metryki

Automatyczna ML nie różni się od metryk danych binarnych i wieloklasowych. Te same metryki walidacji są raportowane niezależnie od tego, czy zestaw danych ma dwie klasy, czy więcej niż dwie klasy. Niektóre metryki są jednak przeznaczone do klasyfikacji wieloklasowej. W przypadku zastosowania do binarnego zestawu danych te metryki nie traktują żadnej klasy jako `true` klasy, ponieważ może się to spodziewać. Metryki, które są wyraźnie przeznaczone dla wieloklasowych są sufiksami z `micro` , `macro` lub `weighted` . Przykłady obejmują `average_precision_score` , `f1_score` , `precision_score` , `recall_score` , i `AUC` .

Na przykład zamiast obliczać odwołanie jako `tp / (tp + fn)` , średniej klasy odwołania ( `micro` , `macro` lub) do `weighted` obu klas binarnego zestawu danych klasyfikacji. Jest to równoważne obliczaniu odwołań dla `true` klasy i `false` klasy oddzielnie, a następnie pobierając średnią z dwóch.

## <a name="confusion-matrix"></a>Macierz pomyłek

Macierz zamylenia opisuje wydajność modelu klasyfikacji. Każdy wiersz zawiera wystąpienia prawdy lub rzeczywistej klasy w zestawie danych, a każda kolumna reprezentuje wystąpienia klasy, które zostały przewidywalne przez model. 

W przypadku każdej matrycy niewidocznej, zautomatyzowany ML pokazuje częstotliwość każdej tabeli przewidywanej (kolumny) w porównaniu z rzeczywistą etykietą (wiersz). Ciemniejszy kolor, im wyższy licznik w danej części macierzy. 

### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?

Macierz nieporozumieną porównuje rzeczywistą wartość zestawu danych z przewidywanymi wartościami, które otrzymał model. Z tego powodu modele uczenia maszynowego mają wyższą dokładność, jeśli model ma większość wartości na przekątnej, co oznacza, że model przewiduje poprawną wartość. Jeśli model ma niezrównoważoną klasę, matryca pomyłek pomaga wykryć rozmnożony model.

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Przykład 1: model klasyfikacji z niską dokładnością
![Model klasyfikacji z niską dokładnością](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>Przykład 2: model klasyfikacji o wysokiej dokładności 
![Model klasyfikacji o wysokiej dokładności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Przykład 3: model klasyfikacji z wysoką dokładnością i wysoką bias w przewidywaniach modelu
![Model klasyfikacji z wysoką dokładnością i wysoką bias w przewidywaniach modelu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>Wykres dokładności do odzyskania

Krzywa odwołań dokładności przedstawia relację między dokładnością i odwołaniem z modelu. Termin dokładność reprezentuje możliwość poprawnego etykietowania wszystkich wystąpień przez model. Funkcja odwoływania reprezentuje możliwość, aby klasyfikator znalazł wszystkie wystąpienia określonej etykiety.

Za pomocą tego wykresu można porównać krzywe odwołań dokładności dla każdego modelu, aby określić model, który ma akceptowalną relację między dokładnością i odwołaniem dla konkretnego problemu biznesowego. Ten wykres pokazuje średnią precyzję makra — odwołanie, średnią precyzję i odwołanie do odwołania do wszystkich klas modelu. 

**Średnie makro** oblicza metrykę niezależnie od poszczególnych klas, a następnie pobiera średnią, traktując jednocześnie wszystkie klasy. Jednak większość **średniej** agreguje wkłady wszystkich klas do obliczenia średniej. Większość średniej jest preferowana, jeśli istnieje nierównoważność klasy w zestawie danych.

### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
W zależności od celu problemu biznesowego, idealna krzywa odwołań może się różnić. 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Przykład 1: model klasyfikacji z małą dokładnością i niskim wycofywaniem
![Model klasyfikacji z małą dokładnością i niskim odwołaniem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Przykład 2: model klasyfikacji o 100% dokładności i ~ 100% odwoływania 
![Model klasyfikacji o wysokiej precyzji i odwołaniu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>Wykres ROC

Charakterystyka działania odbiorcy (lub ROC) to wykres prawidłowo sklasyfikowanych etykiet i niewłaściwie sklasyfikowanych etykiet dla określonego modelu. Krzywa ROC może być mniej informacyjna, gdy szkolenia modeli w zestawach danych o wysokiej klasy nie są zrównoważone, ponieważ większość klas może Drown udział z klas mniejszościowych.

Możesz wizualizować obszar pod wykresem ROC jako część poprawnie sklasyfikowanych próbek. Zaawansowany użytkownik wykresu ROC może wyglądać poza obszarem na krzywej i uzyskać Intuition dla prawdziwie dodatnich i fałszywych stawek dodatnich jako funkcji progu klasyfikacji lub granicy decyzyjnej.

### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Krzywa ROC, która zbliża się do lewego górnego rogu z 100% true dodatnią, i 0% fałszywa dodatnia stawka będzie najlepszym modelem. Model losowy będzie wyświetlany jako linia płaska od lewej dolnej krawędzi do prawego górnego rogu. Mniej niż losowy zostałby DIP poniżej wiersza y = x.

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Przykład 1: model klasyfikacji z niską rzeczywistą etykietami i silnymi etykietami fałszywymi
![Model klasyfikacji z niskimi etykietami o niskiej wartości i fałszywych etykiet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Przykład 2: model klasyfikacji z wysoką prawdziwymi etykietami i niskimi etykietami fałszywych

![model klasyfikacji z wysoką prawdziwymi etykietami i niskimi etykietami fałszywymi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>Wykres przyrostu

Wykresy przyrostowe umożliwiają ocenę wydajności modeli klasyfikacji. Wykres przyrostowy pokazuje, ile razy lepszy model jest porównywany z modelem losowym. Zapewnia to względną wydajność, która uwzględnia fakt, że klasyfikacja staje się trudniejsza w miarę zwiększania liczby klas. Model losowy nieprawidłowo przewidywalna większy ułamek próbek z zestawu danych z dziesięciu klas w porównaniu z zestawem danych z dwiema klasami.

Można porównać automatycznie dźwig modelu z Azure Machine Learningm z linią bazową (model losowy), aby wyświetlić wzrost wartości tego określonego modelu.

### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?

Lepszym modelem jest krzywa podnoszenia, która jest wyższa na grafie i od linii bazowej. 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>Przykład 1: model klasyfikacji, który jest niewłaściwie porównywany z losowym modelem wyboru
![Model klasyfikacji, który jest niezgodny z losowym modelem wyboru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Przykład 2: model klasyfikacji, który wykonuje lepszy niż losowy model wyboru
![Model klasyfikacji, który wykonuje lepsze](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>Wykres zysków skumulowanych

Wykres skumulowanych zysków szacuje wydajność modelu klasyfikacji według każdej części danych. Dla każdego percentylu zestawu danych wykres pokazuje, ile więcej próbek zostało dokładnie sklasyfikowanych w porównaniu do modelu, który jest zawsze nieprawidłowy. Te informacje zapewniają inny sposób przeglądania wyników na dołączonym wykresie podnoszenia.

Wykres skumulowane zyski pomaga wybrać próg klasyfikacji przy użyciu wartości procentowej odpowiadającej żądanemu zyskowi z modelu. Wykres skumulowanych zysków można porównać z linią bazową (nieprawidłowy model), aby zobaczyć procent próbek, które zostały poprawnie sklasyfikowane pod kątem każdego zaufania.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?

Podobnie jak w przypadku wykresu przyrostowego, wyższa krzywa zysków skumulowanych jest wyższa od linii bazowej, tym lepszy jest model. Ponadto ściślejsza krzywa zysków skumulowanych jest w lewym górnym rogu wykresu, tym większym wzrostem jest osiągnięcie modelu w porównaniu z linią bazową. 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Przykład 1: model klasyfikacji z minimalnym wzrostem
![model klasyfikacji z minimalnym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Przykład 2: model klasyfikacji z znaczącym wzrostem
![Model klasyfikacji z znaczącym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>Wykres kalibracyjny

Wykres kalibracyjny przedstawia zaufanie do modelu predykcyjnego. Robi to, pokazując zależność między przewidywanym prawdopodobieństwem a rzeczywistym prawdopodobieństwem, gdzie "prawdopodobieństwo" oznacza prawdopodobieństwo, że określone wystąpienie należy do pewnej etykiety.

W przypadku wszystkich problemów z klasyfikacją można sprawdzić linię kalibracji dla mikrośredniej, średniej przez makro i każdej klasy w danym modelu predykcyjnym.

**Średnie makro** oblicza metrykę niezależnie od każdej klasy, a następnie obliczy średnią, traktując wszystkie klasy w równym stopniu. Jednak większość **średniej** agreguje wkłady wszystkich klas do obliczenia średniej. 

### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Dobrze skalibrowane modele są wyrównane z linią y = x, gdzie prawidłowo przewiduje prawdopodobieństwo, że próbki należą do każdej klasy. Model z nadmiernym prawdopodobieństwem będzie przekroczyć przewidywane działania zbliżone do zera i jednego, rzadko nie jest to konieczne dla klasy każdej próbki.

#### <a name="example-1-a-well-calibrated-model"></a>Przykład 1: model dobrze kalibrowany
![ bardziej dobrze skalibrowane modele](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>Przykład 2: model o nadmiernej pewności
![Model o nadmiernej pewności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>Metryki wydajności regresji

Poniższa tabela zawiera podsumowanie metryk wydajności modelu, które są obliczane przez automatyczne ML dla każdego regresji lub modelu prognozowania, który jest generowany dla eksperymentu. 

|Metryka|Opis|Obliczenia|Dodatkowe parametry
--|--|--|--|
explained_variance|Wyjaśniono wariancję to proporcja, do której model matematyczny jest przeznaczony dla odmiany danego zestawu danych. Jest to procentowy spadek wariancji oryginalnych danych do wariancji błędów. Gdy średnią z błędów jest 0, jest równa współczynnikowi wyznaczania (patrz r2_score poniżej).|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Brak|
r2_score|R ^ 2 to współczynnik wyznaczania lub procent redukcji w przypadku błędów kwadratowych w porównaniu z modelem bazowym, który wyprowadza średnią. |[Obliczenia](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Brak|
spearman_correlation|Korelacja Spearman jest miarą nieparametryczną monotonicity relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacja Spearman nie zakłada, że oba zestawy danych są zwykle dystrybuowane. Podobnie jak inne Współczynniki korelacji, ta wartość różni się od-1 do + 1 i 0 oznacza brak korelacji. Korelacje-1 lub + 1 implikują dokładną relację monotoniczny. Korelacje pozytywne implikują, że w miarę wzrostu x, więc jest to wartość y. Korelacje negatywne oznacza, że w miarę wzrostu x zmniejsza się wartość y.|[Obliczenia](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Brak|
mean_absolute_error|Średni błąd bezwzględny jest oczekiwaną wartością bezwzględną różnicy między obiektem docelowym a prognozą|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Brak|
normalized_mean_absolute_error|Znormalizowany błąd bezwzględny oznacza błąd bezwzględny podzielony przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dzielenie według zakresu danych|
median_absolute_error|Średni błąd bezwzględny to mediana wszystkich bezwzględnych różnic między obiektem docelowym a przewidywaniam. Ta utrata jest niezawodna dla wartości odstających.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Brak|
normalized_median_absolute_error|Znormalizowany błąd bezwzględny jest średnim błędem bezwzględnym podzielonym przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dzielenie według zakresu danych|
root_mean_squared_error|Wartość "pierwiastek" oznacza pierwiastek kwadratowy o oczekiwanej różnicy kwadratowej między elementem docelowym a prognozą|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Brak|
normalized_root_mean_squared_error|Znormalizowany, średni błąd oznaczający, że błąd oznaczający pierwiastek jako średni, podzielony przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dzielenie według zakresu danych|
root_mean_squared_log_error|Główny średni kwadratowy błąd w dzienniku jest pierwiastek kwadratowy oczekiwanego kwadratowego błędu logarytmicznego|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Brak|
normalized_root_mean_squared_log_error|Znormalizowany błąd oznaczający, że w przypadku standardowego elementu głównego jest średni kwadratowy błąd dziennika podzielony przez zakres danych|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dzielenie według zakresu danych|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a>Wykres przewidywany a rzeczywisty

Przewidywany a prawda przedstawia relację między przewidywaną wartością a jej skorelowane wartości prawdziwe dla problemu z regresją. 

Po każdym uruchomieniu można zobaczyć przewidywany wykres a true dla każdego modelu regresji. Aby chronić prywatność danych, wartości są Binned razem, a rozmiar każdego pojemnika jest pokazywany jako wykres słupkowy w dolnej części obszaru wykresu. Można porównać model predykcyjny, korzystając z jaśniejszego obszaru cieniowania pokazującego marginesy błędów, względem idealnej wartości, w której powinien się znajdować model.

### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Ten wykres może służyć do mierzenia wydajności modelu, jak bliżej osi y = x, a wartości przewidywane to lepsza wydajność modelu predykcyjnego.

#### <a name="example-1-a-regression-model-with-low-performance"></a>Przykład 1: model regresji z niską wydajnością
![Model regresji z niską dokładnością w prognozie](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-performance"></a>Przykład 2: model regresji o wysokiej wydajności
![Model regresji o wysokiej dokładności w jego przewidywaniach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a>Histogram wykresu reszty

Automatyczna ML automatycznie udostępnia wykres resztkowy, aby pokazać rozkład błędów w przewidywaniach modelu regresji. Reszta jest różnicą między prognozą i rzeczywistą wartością ( `y_pred - y_true` ). 

### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Aby pokazać margines błędu z niską ilością bias, histogram reszty powinien być w kształcie krzywej dzwonka, wyśrodkowany wokół zera.

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Przykład 1: model regresji z bias w jego błędach
![Model regresji SA z odchyleniami w jego błędach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-a-more-even-distribution-of-errors"></a>Przykład 2: model regresji z bardziej równomiernym rozkładem błędów
![Model regresji z większą ilością równomiernej dystrybucji błędów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a>Interpretacja modelu i ważność funkcji
Zautomatyzowana ML udostępnia pulpit nawigacyjny do tłumaczenia maszynowego dla przebiegów.

Aby uzyskać więcej informacji na temat włączania funkcji interpretacji, zobacz [interpretowanie: wyjaśnienie modelu w zautomatyzowanym uczeniu maszynowym](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> Model ForecastTCN nie jest obecnie obsługiwany przez klienta wyjaśnień. Ten model nie zwraca pulpitu nawigacyjnego wyjaśnienie, jeśli jest zwracany jako najlepszy model i nie obsługuje uruchomionych wyjaśnień na żądanie.

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w Azure Machine Learning.
+ Wypróbuj przykładowy [model uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .