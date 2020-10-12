---
title: Zrozumienie zautomatyzowanych wyników ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i zrozumieć wykresy oraz metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/05/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a38d65e66debd8e718964efdce27fe42772d8e0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91315545"
---
# <a name="understand-automated-machine-learning-results"></a>Opis wyników zautomatyzowanego uczenia maszynowego


W tym artykule dowiesz się, jak wyświetlać i zrozumieć wykresy i metryki dla każdego z zautomatyzowanych przebiegów uczenia maszynowego. 

Dowiedz się więcej na następujące tematy:
+ [Metryki i wykresy dla modeli klasyfikacji](#classification)
+ [Metryki i wykresy dla modeli regresji](#regression)
+ [Interpretacja modelu i ważność funkcji](#explain-model)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Utwórz eksperyment dla automatycznego przebiegu uczenia maszynowego z zestawem SDK lub w programie Azure Machine Learning Studio.

    * Użyj zestawu SDK, aby utworzyć [model klasyfikacji](how-to-auto-train-remote.md) lub [model regresji](tutorial-auto-train-models.md)
    * Użyj [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) , aby utworzyć model klasyfikacji lub regresji przez przekazanie odpowiednich danych.

## <a name="view-the-run"></a>Wyświetlanie przebiegu

Po uruchomieniu zautomatyzowanego eksperymentu w usłudze Machine Learning historia przebiegów jest dostępna w obszarze roboczym uczenia maszynowego. 

1. Przejdź do swojego obszaru roboczego.

1. W lewym panelu obszaru roboczego wybierz pozycję **eksperymenty**.

   ![Zrzut ekranu przedstawiający menu eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na liście eksperymentów wybierz ten, który chcesz poznać.

   [![Lista eksperymentów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. W dolnej tabeli wybierz **przebieg**.

   [ ![ Przebieg eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. W obszarze modele wybierz **nazwę algorytmu** dla modelu, który chcesz szczegółowo zbadać.

   [![Model eksperymentu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Te same wyniki są również widoczne podczas uruchamiania, gdy używasz `RunDetails` [widżetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true).

## <a name="classification-results"></a><a name="classification"></a> Wyniki klasyfikacji

Thee następujące metryki i wykresy są dostępne dla każdego modelu klasyfikacji kompilowanego przy użyciu funkcji automatycznego uczenia maszynowego Azure Machine Learning

+ [Metryki](#classification-metrics)
+ [Macierz pomyłek](#confusion-matrix)
+ [Wykres dokładności do odzyskania](#precision-recall-chart)
+ [Charakterystyki operacyjne odbiornika (lub ROC)](#roc)
+ [Krzywa podnoszenia](#lift-curve)
+ [Krzywa zysków](#gains-curve)
+ [Wykres kalibracyjny](#calibration-plot)

### <a name="classification-metrics"></a>Metryki klasyfikacji

Następujące metryki są zapisywane w każdej iteracji uruchomienia dla zadania klasyfikacji.

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

AutoML nie rozróżnia metryk danych binarnych i wieloklasowych. Te same metryki walidacji są raportowane niezależnie od tego, czy zestaw danych ma dwie klasy, czy więcej niż dwie klasy. Niektóre metryki są jednak przeznaczone do klasyfikacji wieloklasowej. W przypadku zastosowania do binarnego zestawu danych te metryki nie traktują żadnej klasy jako `true` klasy, ponieważ może się to spodziewać. Metryki, które są wyraźnie przeznaczone dla wieloklasowych są sufiksami z `micro` , `macro` lub `weighted` . Przykłady obejmują `average_precision_score` , `f1_score` , `precision_score` , `recall_score` , i `AUC` .

Konkretnym przykładem jest to, że ten predykat odróżniający jest bardziej przejrzysty: zamiast obliczać odwołanie jako `tp / (tp + fn)` , średniej klasy odwołania ( `micro` , `macro` lub `weighted` ) dla obu klas binarnego zestawu danych klasyfikacji. Jest to równoważne obliczaniu odwołania dla `true` klasy i `false` klasy oddzielnie, a następnie pobierając średnią z dwóch.

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Macierz pomyłek

#### <a name="what-is-a-confusion-matrix"></a>Co to jest macierz niepomylena?
Macierz niepomylena służy do opisywania wydajności modelu klasyfikacji. Każdy wiersz zawiera wystąpienia prawdy lub rzeczywistej klasy w zestawie danych, a każda kolumna reprezentuje wystąpienia klasy, które zostały przewidywalne przez model. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>Co robią automatyczne znaczenie w odróżnieniu od macierzy?
W przypadku problemów z klasyfikacją Azure Machine Learning automatycznie zapewnia niepomyleną macierz dla każdego skompilowanego modelu. Dla każdej matrycy nieistotnej, zautomatyzowany ML będzie wyświetlał częstotliwość każdej tabeli przewidywanej (kolumny) w porównaniu z rzeczywistą etykietą (wiersz). Ciemniejszy kolor, im wyższy licznik w danej części macierzy. 

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Porównujemy rzeczywistą wartość zestawu danych z przewidywanymi wartościami, które otrzymał model. Z tego powodu modele uczenia maszynowego mają wyższą dokładność, jeśli model ma większość wartości na przekątnej, co oznacza, że model przewiduje poprawną wartość. Jeśli model ma niezrównoważoną klasę, macierz niezauważalna pomoże wykryć model z obciążeniem.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Przykład 1: model klasyfikacji z niską dokładnością
![Model klasyfikacji z niską dokładnością](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Przykład 2: model klasyfikacji o wysokiej dokładności 
![Model klasyfikacji o wysokiej dokładności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Przykład 3: model klasyfikacji z wysoką dokładnością i wysoką bias w przewidywaniach modelu
![Model klasyfikacji z wysoką dokładnością i wysoką bias w przewidywaniach modelu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Wykres dokładności do odzyskania
#### <a name="what-is-a-precision-recall-chart"></a>Co to jest wykres z dokładnoocią dokładności?
Krzywa odwołań dokładności przedstawia relację między dokładnością i odwołaniem z modelu. Termin dokładność reprezentuje możliwość poprawnego etykietowania wszystkich wystąpień przez model. Funkcja odwoływania reprezentuje możliwość, aby klasyfikator znalazł wszystkie wystąpienia określonej etykiety.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>Co robią automatyczne ML z wykresem dokładności do odzyskania?

Za pomocą tego wykresu można porównać krzywe odwołań dokładności dla każdego modelu, aby określić model, który ma akceptowalną relację między dokładnością i odwołaniem dla konkretnego problemu biznesowego. Ten wykres pokazuje średnią precyzję makra — odwołanie, średnią precyzję i odwołanie do odwołania do wszystkich klas modelu. 

Średnie makro obliczy metrykę niezależnie od klasy, a następnie obliczy średnią, traktując wszystkie klasy w równym stopniu. Jednak funkcja Micro Average agreguje wkłady wszystkich klas, aby obliczyć średnią. Większość średniej jest preferowana, jeśli istnieje nierównoważność klasy w zestawie danych.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
W zależności od celu problemu biznesowego, idealna krzywa odwołań może się różnić. Poniżej przedstawiono kilka przykładów

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Przykład 1: model klasyfikacji z małą dokładnością i niskim wycofywaniem
![Model klasyfikacji z małą dokładnością i niskim odwołaniem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Przykład 2: model klasyfikacji o 100% dokładności i ~ 100% odwoływania 
![Model klasyfikacji o wysokiej precyzji i odwołaniu](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Wykres ROC

#### <a name="what-is-a-roc-chart"></a>Co to jest wykres ROC?
Charakterystyka działania odbiorcy (lub ROC) to wykres prawidłowo sklasyfikowanych etykiet i niewłaściwie sklasyfikowanych etykiet dla określonego modelu. Krzywa ROC może być mniej informacyjna, gdy szkolenia modeli w zestawach danych o wysokiej klasy nie są zrównoważone, ponieważ większość klas może Drown udział z klas mniejszościowych.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>Co robią automatyczne ML przy użyciu wykresu ROC?
Możesz wizualizować obszar pod wykresem ROC jako część poprawnie sklasyfikowanych próbek. Zaawansowany użytkownik wykresu ROC może wyglądać poza obszarem na krzywej i uzyskać Intuition dla prawdziwie dodatnich i fałszywych stawek dodatnich jako funkcji progu klasyfikacji lub granicy decyzyjnej.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Krzywa ROC, która zbliża się do lewego górnego rogu z 100% true dodatnią, i 0% fałszywa dodatnia stawka będzie najlepszym modelem. Model losowy będzie wyświetlany jako linia płaska od lewej dolnej krawędzi do prawego górnego rogu. Mniej niż losowy zostałby DIP poniżej wiersza y = x.

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Przykład 1: model klasyfikacji z niską rzeczywistą etykietami i silnymi etykietami fałszywymi
![Model klasyfikacji z niskimi etykietami o niskiej wartości i fałszywych etykiet](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Przykład 2: model klasyfikacji z wysoką prawdziwymi etykietami i niskimi etykietami fałszywych
![model klasyfikacji z wysoką prawdziwymi etykietami i niskimi etykietami fałszywymi](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Wykres przyrostu
#### <a name="what-is-a-lift-chart"></a>Co to jest wykres przyrostu?
Wykresy przyrostowe służą do oceny wydajności modeli klasyfikacji. Wykres przyrostowy pokazuje, ile razy lepszy model jest porównywany z modelem losowym. Zapewnia to względną wydajność, która uwzględnia fakt, że klasyfikacja staje się trudniejsza w miarę zwiększania liczby klas. Model losowy będzie nieprawidłowo przewidzieć większy ułamek próbek z zestawu danych z dziesięciu klas w porównaniu z zestawem danych z dwiema klasami.

#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>Co robią automatyczne ML z wykresem przyrostowym?
Można porównać automatycznie dźwig modelu z Azure Machine Learningm z linią bazową, aby wyświetlić jego wartość w tym konkretnym modelu.
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Przykład 1: model klasyfikacji, który jest niezgodny z losowym modelem wyboru
![Model klasyfikacji, który jest niezgodny z losowym modelem wyboru](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Przykład 2: model klasyfikacji, który wykonuje lepszy niż losowy model wyboru
![Model klasyfikacji, który wykonuje lepsze](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="cumulative-gains-chart"></a>Wykres zysków skumulowanych
#### <a name="what-is-a-cumulative-gains-chart"></a>Co to jest wykres skumulowany zysków?

Wykres skumulowanych zysków szacuje wydajność modelu klasyfikacji według każdej części danych. Dla każdego percentylu zestawu danych wykres pokazuje, ile więcej próbek zostało dokładnie sklasyfikowanych.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>Co robią automatyczne ML z wykresem zysków?
Użyj wykresu skumulowanych zysków, aby pomóc w wyborze odcięcia klasyfikacji przy użyciu wartości procentowej odpowiadającej żądanemu zyskowi z modelu. Te informacje zapewniają inny sposób przeglądania wyników na dołączonym wykresie podnoszenia.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Przykład 1: model klasyfikacji z minimalnym wzrostem
![model klasyfikacji z minimalnym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Przykład 2: model klasyfikacji z znaczącym wzrostem
![Model klasyfikacji z znaczącym wzrostem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Wykres kalibracyjny

#### <a name="what-is-a-calibration-chart"></a>Co to jest wykres kalibracyjny?
Wykres kalibracyjny służy do wyświetlania pewności dotyczącej modelu predykcyjnego. Robi to, pokazując zależność między przewidywanym prawdopodobieństwem a rzeczywistym prawdopodobieństwem, gdzie "prawdopodobieństwo" oznacza prawdopodobieństwo, że określone wystąpienie należy do pewnej etykiety.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>Co robią automatyczne ML z wykresem kalibracyjnym?
W przypadku wszystkich problemów z klasyfikacją można sprawdzić linię kalibracji dla mikrośredniej, średniej przez makro i każdej klasy w danym modelu predykcyjnym.

Średnie makro obliczy metrykę niezależnie od klasy, a następnie obliczy średnią, traktując wszystkie klasy w równym stopniu. Jednak funkcja Micro Average agreguje wkłady wszystkich klas, aby obliczyć średnią. 
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Dobrze skalibrowane modele są wyrównane z linią y = x, gdzie prawidłowo przewiduje prawdopodobieństwo, że próbki należą do każdej klasy. Model z nadmiernym prawdopodobieństwem będzie przekroczyć przewidywane działania zbliżone do zera i jednego, rzadko nie jest to konieczne dla klasy każdej próbki.


##### <a name="example-1-a-well-calibrated-model"></a>Przykład 1: model dobrze kalibrowany
![ bardziej dobrze skalibrowane modele](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Przykład 2: model o nadmiernej pewności
![Model o nadmiernej pewności](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a> Wyniki regresji

Thee następujące metryki i wykresy są dostępne dla każdego modelu regresji kompilowanego przy użyciu funkcji automatycznego uczenia maszynowego Azure Machine Learning

+ [Metryki](#reg-metrics)
+ [Przewidywany a prawdziwy](#pvt)
+ [Histogram reszty](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a> Metryki regresji

Następujące metryki są zapisywane w każdej iteracji uruchomienia dla zadania regresji lub prognozowania.

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

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a> Wykres przewidywany a rzeczywisty
#### <a name="what-is-a-predicted-vs-true-chart"></a>Co to jest wykres predykcyjny a prawda?
Przewidywany a prawda przedstawia relację między przewidywaną wartością a jej skorelowane wartości prawdziwe dla problemu z regresją. Ten wykres może służyć do mierzenia wydajności modelu, jak bliżej osi y = x, a wartości przewidywane to lepsza dokładność modelu predykcyjnego.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>Co robią automatyczne ML z przewidzianym wykresem a prawdziwym?
Po każdym uruchomieniu można zobaczyć przewidywany wykres a true dla każdego modelu regresji. Aby chronić prywatność danych, wartości są Binned razem, a rozmiar każdego pojemnika jest pokazywany jako wykres słupkowy w dolnej części obszaru wykresu. Można porównać model predykcyjny, korzystając z jaśniejszego obszaru cieniowania pokazującego marginesy błędów, względem idealnej wartości, w której powinien się znajdować model.

#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Przykład 1: model klasyfikacji o niskiej dokładności
![Model regresji z niską dokładnością w prognozie](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Przykład 2: model regresji o wysokiej dokładności 
[![Model regresji o wysokiej dokładności w jego przewidywaniach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a> Histogram wykresu reszty
#### <a name="what-is-a-residuals-chart"></a>Co to jest wykres reszty?
Reszta jest różnicą między prognozą i rzeczywistą wartością ( `y_pred - y_true` ). Aby pokazać margines błędu z niską ilością bias, histogram reszty powinien być w kształcie krzywej dzwonka, wyśrodkowany wokół 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>Co robią automatyczne ML z wykresem reszty?
Automatyczna część ML udostępnia wykres reszty, aby pokazać dystrybucję błędów w przewidywaniach.
#### <a name="what-does-a-good-model-look-like"></a>Jak wygląda dobry model?
Dobry model zwykle będzie miał pozostały w przybliżeniu równy zeru.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Przykład 1: model regresji z bias w jego błędach
![Model regresji SA z odchyleniami w jego błędach](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Przykład 2: model regresji z bardziej równomiernym rozkładem błędów
![Model regresji z większą ilością równomiernej dystrybucji błędów](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a> Interpretacja modelu i ważność funkcji
Zautomatyzowana ML udostępnia pulpit nawigacyjny do tłumaczenia maszynowego dla przebiegów.
Aby uzyskać więcej informacji na temat włączania funkcji interpretacji, zobacz [instrukcje](how-to-machine-learning-interpretability-automl.md) włączania interpretacji w zautomatyzowanych eksperymentach ml.

> [!NOTE]
> Model ForecastTCN nie jest obecnie obsługiwany przez klienta wyjaśnień. Ten model nie zwróci pulpitu nawigacyjnego wyjaśnienie, jeśli jest zwracany jako najlepszy model i nie obsługuje uruchomionych wyjaśnień na żądanie.

## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o [zautomatyzowanych ml](concept-automated-ml.md) w Azure Machine Learning.
+ Wypróbuj przykładowe notesy w programie [zautomatyzowanego modelowania Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
