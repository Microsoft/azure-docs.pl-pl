---
title: Oceń wyniki eksperymentu AutoML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i szacować wykresy oraz metryki dla każdego zautomatyzowanego przebiegu eksperymentu w usłudze Machine Learning.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: b60e5f656b675a1382b8b4776975723a437183bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104773117"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Oceń automatyczne wyniki eksperymentu w usłudze Machine Learning

W tym artykule dowiesz się, jak oszacować i porównać modele przeszkolone przez zautomatyzowany eksperyment uczenia maszynowego (zautomatyzowany ML). W trakcie przebiegu zautomatyzowanego eksperymentu z tablicą tworzone są wiele uruchomień, a każdy przebieg tworzy model. Dla każdego modelu automatyczna ML generuje metryki oceny i wykresy, które ułatwiają pomiar wydajności modelu. 

Na przykład, zautomatyzowana ML generuje następujące wykresy na podstawie typu eksperymentu.

| Klasyfikacja| Regresja/prognozowanie |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Macierz pomyłek](#confusion-matrix)                       | [Histogram reszt](#residuals)        |
| [Krzywa charakterystyki (ROC) odbiornika](#roc-curve) | [Przewidywany a prawdziwy](#predicted-vs-true) |
| [Dokładność — krzywa odwołania (PR)](#precision-recall-curve)      |                                          |
| [Krzywa podnoszenia](#lift-curve)                                   |                                          |
| [Krzywa zysków skumulowanych](#cumulative-gains-curve)           |                                          |
| [Krzywa kalibracji](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. (Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://aka.ms/AMLFree) )
- Eksperyment Azure Machine Learning utworzony przy użyciu:
  - [Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md) (nie jest wymagany żaden kod)
  - [Azure Machine Learning Python SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Wyświetl wyniki przebiegu

Po zakończeniu zautomatyzowanego eksperymentu ML historia przebiegów będzie można znaleźć za pośrednictwem:
  - Przeglądarka z [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md)
  - Notes Jupyter za pomocą [widżetu Jupyter RunDetails](/python/api/azureml-widgets/azureml.widgets.rundetails)

Poniższe kroki i wideo pokazują, jak wyświetlić informacje o historii uruchamiania oraz metryki i wykresach oceny modelu w programie Studio:

1. [Zaloguj się do programu Studio](https://ml.azure.com/) i przejdź do obszaru roboczego.
1. W menu po lewej stronie wybierz pozycję **eksperymenty**.
1. Wybierz swój eksperyment z listy eksperymentów.
1. W tabeli w dolnej części strony wybierz zautomatyzowany przebiegu ML.
1. Na karcie **modele** wybierz **nazwę algorytmu** dla modelu, który chcesz oszacować.
1. Na karcie **metryki** Użyj pól wyboru po lewej stronie, aby wyświetlić metryki i wykresy.

![Procedura wyświetlania metryk w programie Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Metryki klasyfikacji

Automatyczna ML oblicza metryki wydajności dla każdego modelu klasyfikacji wygenerowanego dla eksperymentu. Te metryki są oparte na implementacji scikit. 

Wiele metryk klasyfikacji jest zdefiniowanych dla klasyfikacji binarnej w dwóch klasach i wymaga średniej klasy w celu utworzenia jednego wyniku dla klasyfikacji wieloklasowej. Scikit — uczenie zawiera kilka metod uśredniających, z których trzy są automatycznie uwidaczniane: **makro**, **mikro** i **ważone**.

- **Makro** — oblicza metrykę dla każdej klasy i przyjmuje nieważoną średnią
-  Oblicz całkowitą metrykę globalnie, obliczając łączną liczbę pozytywnych dodatnich, fałszywych negatywnych i fałszywych wartości dodatnich (niezależnie od klas).
- **Ważone** — oblicza metrykę dla każdej klasy i pobiera średnią ważoną na podstawie liczby próbek na klasę.

Chociaż każda metoda uśredniania ma swoje korzyści, jednym typowym zagadnieniem w przypadku wybrania odpowiedniej metody jest niezrównoważona Klasa. Jeśli klasy mają różne liczby próbek, może być bardziej niesformatowane, aby można było używać średniej makro, gdzie klasy mniejszości są równe wag dla większości klas. Dowiedz się więcej o [metrykach danych binarnych i wieloklasowych w zautomatyzowanej ml](#binary-vs-multiclass-classification-metrics). 

Poniższa tabela zawiera podsumowanie metryk wydajności modelu, które są obliczane przez automatyczne ML dla każdego modelu klasyfikacji wygenerowanego dla eksperymentu. Aby uzyskać więcej informacji, zobacz dokumentację scikit-dołączoną do pola **obliczenia** każdej metryki. 

|Metric|Opis|Obliczenia|
|--|--|---|
|AUC | AUC to obszar pod [krzywą charakterystyczną dla odbiornika](#roc-curve).<br><br> **Cel:** Bliżej 1 <br> **Zakres:** [0, 1]<br> <br>Obsługiwane są nazwy metryk, <li>`AUC_macro`, średnia arytmetyczna AUC dla każdej klasy.<li> `AUC_micro`obliczony przez połączenie prawdziwie dodatnich i fałszywych wartości dodatnich z każdej klasy. <li> `AUC_weighted`, średnia arytmetyczna dla każdej klasy, ważona przez liczbę wystąpień prawdziwych w każdej klasie.   |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Dokładność jest stosunkiem prognoz, które dokładnie pasują do prawdziwych etykiet klas. <br> <br>**Cel:** Bliżej 1 <br> **Zakres:** [0, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Średnia precyzja podsumowuje krzywą odwołań dokładności jako średnią ważoną dokładności osiągniętą dla każdego progu, z zwiększeniem odzyskania z poprzedniego progu używanego jako waga. <br><br> **Cel:** Bliżej 1 <br> **Zakres:** [0, 1]<br> <br>Obsługiwane są nazwy metryk,<li>`average_precision_score_macro`, średnia arytmetyczna średniego wyniku dokładności dla każdej klasy.<li> `average_precision_score_micro`, obliczony przez połączenie prawdziwie dodatnich i fałszywych wartości dodatnich dla każdego odcięcia.<li>`average_precision_score_weighted`, średnia arytmetyczna średniego wyniku dokładności dla każdej klasy, ważona przez liczbę wystąpień prawdziwe w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Równoważna dokładność to arytmetyczna średnia odwołania dla każdej klasy.<br> <br>**Cel:** Bliżej 1 <br> **Zakres:** [0, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|Wynik F1 jest średnią harmoniczną precyzji i odwołania. Jest to dobry, zrównoważony pomiar dla obu fałszywych dodatnich i fałszywych wartości ujemnych. Nie przyjmuje jednak prawdziwych negatywów do konta. <br> <br>**Cel:** Bliżej 1 <br> **Zakres:** [0, 1]<br> <br>Obsługiwane są nazwy metryk,<li>  `f1_score_macro`: orednia arytmetyczna wyniku F1 dla każdej klasy. <li> `f1_score_micro`: obliczone przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych dodatnich. <li> `f1_score_weighted`: ważone średnie według częstotliwości klasy dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Jest to funkcja strat użyta w (MULTINOMIAL) logistyki i rozszerzenia, takie jak sieci neuronowych, zdefiniowane jako negatywna prawdopodobieństwo rejestrowania dla prawdziwych etykiet z przewidywaniami klasyfikatora probabilistyczne. <br><br> **Cel:** Bliżej 0 <br> **Zakres:** [0, inf)|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Znormalizowana funkcja odwoływania makr to odwołanie do wartości średniej i znormalizowanej, dzięki czemu wydajność Losowa ma wynik równy 0, a doskonałe wydajność ma wynik 1. <br> <br>**Cel:** Bliżej 1 <br> **Zakres:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>gdzie, `R` jest oczekiwaną wartością `recall_score_macro` dla prognoz losowych.<br><br>`R = 0.5`&nbsp;dla &nbsp; klasyfikacji binarnej &nbsp; . <br>`R = (1 / C)` w przypadku problemów klasyfikacji klasy C.|
matthews_correlation | Współczynnik korelacji Matthews to zrównoważona miara dokładności, która może być używana, nawet jeśli jedna klasa ma wiele więcej próbek niż inne. Współczynnik 1 wskazuje doskonałe prognozowanie, 0 prognozowanie losowe i-1 prognozowanie odwrotne.<br><br> **Cel:** Bliżej 1 <br> **Zakres:** [-1, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precision|Precyzja to zdolność modelu, aby uniknąć etykiet próbek negatywnych jako pozytywnych. <br><br> **Cel:** Bliżej 1 <br> **Zakres:** [0, 1]<br> <br>Obsługiwane są nazwy metryk, <li> `precision_score_macro`, arytmetyczne znaczenie dla każdej klasy. <li> `precision_score_micro`obliczone globalnie przez zliczanie łącznej liczby pozytywnych dodatnich i fałszywych wartości dodatnich. <li> `precision_score_weighted`, arytmetyczne znaczenie dla każdej klasy, ważone według liczby prawdziwe wystąpienia w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
recall| Funkcja odwoływania to zdolność modelu do wykrywania wszystkich próbek pozytywnych. <br><br> **Cel:** Bliżej 1 <br> **Zakres:** [0, 1]<br> <br>Obsługiwane są nazwy metryk, <li>`recall_score_macro`: arytmetyczne znaczenie operacji odwoływania dla każdej klasy. <li> `recall_score_micro`: obliczone globalnie przez zliczanie całkowitej liczby pozytywnych dodatnich, fałszywych wartości ujemnych i fałszywych dodatnich.<li> `recall_score_weighted`: arytmetyczne znaczenie operacji odwoływania dla każdej klasy, ważone według liczby prawdziwe wystąpienia w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Waga ważona jest dokładnością, w której każda próbka jest ważona przez łączną liczbę próbek należących do tej samej klasy. <br><br>**Cel:** Bliżej 1 <br>**Zakres:** [0, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Dane binarne a wieloklasowe metryki klasyfikacji

Automatyczna ML nie różni się od metryk danych binarnych i wieloklasowych. Te same metryki walidacji są raportowane niezależnie od tego, czy zestaw danych ma dwie klasy, czy więcej niż dwie klasy. Niektóre metryki są jednak przeznaczone do klasyfikacji wieloklasowej. W przypadku zastosowania do binarnego zestawu danych te metryki nie traktują żadnej klasy jako `true` klasy, ponieważ może się to spodziewać. Metryki, które są wyraźnie przeznaczone dla wieloklasowych są sufiksami z `micro` , `macro` lub `weighted` . Przykłady obejmują `average_precision_score` , `f1_score` , `precision_score` , `recall_score` , i `AUC` .

Na przykład zamiast obliczać odwołanie jako `tp / (tp + fn)` , średniej klasy odwołania ( `micro` , `macro` lub) do `weighted` obu klas binarnego zestawu danych klasyfikacji. Jest to równoważne obliczaniu odwołań dla `true` klasy i `false` klasy oddzielnie, a następnie pobierając średnią z dwóch.

Automatyczna ML nie oblicza metryk binarnych, które są metrykami dla binarnych zestawów danych klasyfikacji. Jednak te metryki można obliczyć ręcznie przy użyciu [matrycy](#confusion-matrix) niewidocznej, która jest generowana przez zautomatyzowany ml dla danego przebiegu. Na przykład można obliczyć precyzję, `tp / (tp + fp)` przy czym prawdziwe wartości dodatnie i fałszywie dodatnie są wyświetlane na wykresie macierzy 2x2.

## <a name="confusion-matrix"></a>Macierz pomyłek

Niewidoczne macierze umożliwiają wizualizację, w jaki model uczenia maszynowego czyni błędy systematyczne w swoich przewidywaniach dla modeli klasyfikacji. Słowo "mylenie" w nazwie pochodzi z modelu "mylące" lub z nieprawidłowymi etykietami. Komórka w wierszu `i` i kolumnie w niezmienionej `j` macierzy zawiera liczbę próbek w zestawie danych oceny, które należą do klasy `C_i` i zostały sklasyfikowane przez model jako Klasa `C_j` .

W programie Studio, ciemniejszy komórka wskazuje większą liczbę próbek. Wybranie widoku **znormalizowana** na liście rozwijanej spowoduje znormalizowanie nad każdym wierszem macierzy w celu wyświetlenia procentu klasy `C_i` przewidywanej jako `C_j` Klasa. Zaletą domyślnego widoku **nieprzetworzonego** jest możliwość sprawdzenia, czy nierównoważność dystrybucji rzeczywistych klas spowodowała, że model nie klasyfikuje próbek z klasy mniejszości, typowym problemem w niezrównoważonych zestawach danych.

Macierz nieporozumień dobrego modelu będzie miała większość próbek wzdłuż przekątnej.

### <a name="confusion-matrix-for-a-good-model"></a>Zamylenie macierzy dla dobrego modelu 
![Zamylenie macierzy dla dobrego modelu ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Zamieszanie macierzy dla niewłaściwego modelu
![Zamieszanie macierzy dla niewłaściwego modelu](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>Krzywa ROC

Krzywa Charakterystyka działania odbiornika (ROC) przedstawia relację między prawdziwą dodatnią stawką (TPR) i fałszywą dodatnią stawką (zarejestrowanego) w miarę zmiany progu decyzji. Krzywa ROC może być mniej informacyjna, gdy szkolenia modeli w zestawach danych o wysokiej klasy nie są zrównoważone, ponieważ większość klasy może Drown udziały z klas mniejszościowych.

Obszar pod krzywą (AUC) może być interpretowany jako część poprawnie sklasyfikowanych próbek. Dokładniej, AUC jest prawdopodobieństwem, że klasyfikator porządkuje losowo wybraną dodatnią próbkę wyższą niż losowo wybrana próbka ujemna. Kształt krzywej daje Intuition relacji między TPR i zarejestrowanego jako funkcją progu klasyfikacji lub granicy decyzyjnej.

Krzywa, która zbliża się do lewego górnego rogu wykresu, zbliża się do 100% TPR i 0% zarejestrowanego, najlepszego możliwego modelu. Model losowy będzie generował krzywą ROC wzdłuż `y = x` linii od lewego dolnego rogu do prawej strony. Niegorszy niż model losowy będzie miał krzywą ROC, która spadnie poniżej `y = x` wiersza.
> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych produkowanych dla zautomatyzowanych modeli ML może służyć do oszacowania modelu dla klasy lub średniej dla wszystkich klas. Możesz przełączać się między tymi widokami, klikając etykiety klas w legendzie na prawo od wykresu.
### <a name="roc-curve-for-a-good-model"></a>Krzywa ROC dla dobrego modelu
![Krzywa ROC dla dobrego modelu](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Krzywa ROC dla niewłaściwego modelu
![Krzywa ROC dla niewłaściwego modelu](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Dokładność — krzywa odwołania

Krzywa odwołań dokładności przedstawia relację między dokładnością i odwołaniem w miarę zmiany progu decyzji. Funkcja odwoływania to zdolność modelu do wykrywania wszystkich próbek pozytywnych i precyzji, która umożliwia modelowi uniknięcie etykietowania próbek negatywnych jako pozytywnych. Niektóre problemy biznesowe mogą wymagać większego odzyskania i większej dokładności w zależności od względnej ważności unikania fałszywych wartości ujemnych zamiast fałszywych wartości dodatnich.
> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych produkowanych dla zautomatyzowanych modeli ML może służyć do oszacowania modelu dla klasy lub średniej dla wszystkich klas. Możesz przełączać się między tymi widokami, klikając etykiety klas w legendzie na prawo od wykresu.
### <a name="precision-recall-curve-for-a-good-model"></a>Krzywa odwołań z dokładnością dla dobrego modelu
![Krzywa odwołań z dokładnością dla dobrego modelu](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Krzywa odwołań dokładności dla niewłaściwego modelu
![Krzywa odwołań dokładności dla niewłaściwego modelu](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Krzywa zysków skumulowanych

Krzywa skumulowane zyski przedstawia procent próbek pozytywnych poprawnie sklasyfikowanych jako funkcja procentu przykładowych próbek, gdzie rozważamy przykłady w kolejności przewidywanych prawdopodobieństw.

Aby obliczyć zysk, najpierw Sortuj wszystkie próbki od najwyższego do najniższego prawdopodobieństwa przewidywanego przez model. Następnie weź pod `x%` wyższe wartości prognoz zaufania. Podziel liczbę próbek pozytywnych wykrytych w tym `x%` przez łączną liczbę próbek dodatnich, aby uzyskać zysk. Łączny wzrost to procent próbek dodatnich wykrywanych podczas rozważania pewnego procentu ilości danych, które najprawdopodobniej należą do klasy dodatniej.

Idealnym modelem będzie ranga wszystkich próbek pozytywnych powyżej wszystkich próbek negatywnych, które łączą się z dwoma segmentami prostymi. Pierwszy to linia z nachyleniem `1 / x` `(0, 0)` do `(x, 1)` gdzie `x` jest częścią próbki, która należy do klasy pozytywnej ( `1 / num_classes` Jeśli klasy są zrównoważone). Druga to linia pozioma od `(x, 1)` do `(1, 1)` . W pierwszym segmencie wszystkie próbki pozytywne są klasyfikowane prawidłowo, a zysk skumulowany przechodzi do `100%` pierwszego z nich `x%` .

Model losowy z linią bazową będzie miał skumulowaną krzywą zysków po tym, `y = x` gdzie `x%` wykryte są tylko informacje o całkowitej liczbie `x%` dodatnich próbek. Idealnym modelem będzie bardzo średnia Krzywa, która dotyka lewego górnego rogu i linię średniej z makra, która ma nachylenie `1 / num_classes` do momentu, gdy całkowity wzrost wynosi 100%, a następnie w poziomie do 100 wartości procentowej.
> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych produkowanych dla zautomatyzowanych modeli ML może służyć do oszacowania modelu dla klasy lub średniej dla wszystkich klas. Możesz przełączać się między tymi widokami, klikając etykiety klas w legendzie na prawo od wykresu.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Krzywa zysków skumulowanych dla dobrego modelu
![Krzywa zysków skumulowanych dla dobrego modelu](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Krzywa zysków skumulowanych dla niewłaściwego modelu
![Krzywa zysków skumulowanych dla niewłaściwego modelu](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Krzywa podnoszenia

Krzywa Unieś pokazuje, ile razy lepszy model jest porównywany z modelem losowym. Dźwig jest definiowany jako stosunek łącznego zysku do całkowitego wzmocnienia losowego modelu.

Ta względna wydajność bierze pod uwagę fakt, że klasyfikacja staje się trudniejsza w miarę zwiększania liczby klas. (Model losowy nieprawidłowo przewidywalna większy ułamek próbek z zestawu danych z 10 klasami w porównaniu z zestawem danych zawierającym dwie klasy)

Krzywa przenośnika linii bazowej to linia, w `y = 1` której model wydajności jest zgodny z modelem losowym. Ogólnie rzecz biorąc, krzywa przyrostu dla dobrego modelu będzie wyższa dla tego wykresu i może się dalej znajdować na osi x, pokazując, że gdy model jest najbardziej pewny w swoich przewidywaniach, wykonuje wiele razy lepszy niż losowe odgadnięcie.

> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych produkowanych dla zautomatyzowanych modeli ML może służyć do oszacowania modelu dla klasy lub średniej dla wszystkich klas. Możesz przełączać się między tymi widokami, klikając etykiety klas w legendzie na prawo od wykresu.
### <a name="lift-curve-for-a-good-model"></a>Krzywa podnoszenia dla dobrego modelu
![Krzywa podnoszenia dla dobrego modelu](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Krzywa podnoszenia dla niewłaściwego modelu
![Krzywa podnoszenia dla niewłaściwego modelu](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Krzywa kalibracji

Krzywa kalibracji przedstawia zaufanie modelu w swoich przewidywaniach względem proporcji próbek pozytywnych na każdym poziomie ufności. Dobrze skalibrowane modele poprawnie klasyfikuje 100% prognoz, do których przypisze 100% pewności, 50% przewidywanych przez dział IT 50% pewności, 20% przewidywania przypisuje 20% pewności i tak dalej. Idealnie kalibrowany model będzie miał krzywą kalibracji po `y = x` wierszu, w którym model doskonale przewidywalnuje prawdopodobieństwo, że próbki należą do każdej klasy.

Model z nadmiernym prawdopodobieństwem będzie przekroczyć przewidywany efekt, zbliżony do zera i jeden, rzadko nieokreślony dla klasy każdej próbki, a krzywa kalibracji będzie wyglądać podobnie do tyłu "S". Niezależny model przypisuje mniejsze prawdopodobieństwo średnio do klasy, w której jest przewidywalna, a skojarzona krzywa kalibracji będzie wyglądać podobnie do "S". Krzywa kalibracji nie przedstawia możliwości poprawnego klasyfikowania modelu, ale zamiast tego ma możliwość prawidłowego przypisania zaufania do jego prognoz. Niewłaściwy model nadal może mieć dobrą krzywą kalibracji, jeśli model prawidłowo przypisuje niski poziom pewności i wysoką niepewność.

> [!NOTE]
> Krzywa kalibracji jest wrażliwa na liczbę próbek, więc niewielki zestaw walidacji może generować wyniki szumu, które mogą być trudne do zinterpretowania. Nie musi to oznaczać, że model nie jest dobrze skalibrowany.

### <a name="calibration-curve-for-a-good-model"></a>Krzywa kalibracji dla dobrego modelu
![Krzywa kalibracji dla dobrego modelu](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Krzywa kalibracji dla niewłaściwego modelu
![Krzywa kalibracji dla niewłaściwego modelu](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Metryki regresji/prognozowania

Funkcja zautomatyzowanej sieci oblicza te same metryki wydajności dla każdego generowanego modelu, niezależnie od tego, czy jest to eksperyment regresji lub prognozowania. Te metryki są również poddawane normalizacji w celu umożliwienia porównania między modelami przeszkolonymi na danych z różnymi zakresami. Aby dowiedzieć się więcej, zobacz [normalizacja metryki](#metric-normalization).  

Poniższa tabela zawiera podsumowanie metryk wydajności modelu wygenerowanych na potrzeby regresji i prognozowania eksperymentów. Podobnie jak w przypadku metryk klasyfikacji, te metryki są również oparte na implementacji scikit. Odpowiednia dokumentacja scikit Dowiedz się odpowiednio w polu **obliczenia** .

|Metric|Opis|Obliczenia|
--|--|--|
explained_variance|Wyjaśnione odchylenie mierzy zakres, do którego model jest używany dla odmiany w zmiennej docelowej. Jest to procentowy spadek wariancji oryginalnych danych do wariancji błędów. Gdy średnią z błędów jest 0, jest równa współczynnikowi wyznaczania (patrz r2_score poniżej). <br> <br> **Cel:** Bliżej 1 <br> **Zakres:** (-inf, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Średni błąd bezwzględny jest oczekiwaną wartością bezwzględną różnicy między obiektem docelowym a przewidywaniam.<br><br> **Cel:** Bliżej 0 <br> **Zakres:** [0, inf) <br><br> Typ <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, mean_absolute_error podzielona przez zakres danych. | [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Średni bezwzględny błąd procentu (MAPE) to miara średniej różnicy między wartością przewidywaną a wartością rzeczywistą.<br><br> **Cel:** Bliżej 0 <br> **Zakres:** [0, inf) ||
median_absolute_error|Średni błąd bezwzględny to mediana wszystkich bezwzględnych różnic między obiektem docelowym a przewidywaniam. Ta utrata jest niezawodna dla wartości odstających.<br><br> **Cel:** Bliżej 0 <br> **Zakres:** [0, inf)<br><br>Typ <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error podzielona przez zakres danych. |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (współczynnik wyznaczania) mierzy proporcjonalną redukcję w przypadku błędu kwadratowego (MSE) względem łącznej wariancji obserwowanych danych. <br> <br> **Cel:** Bliżej 1 <br> **Zakres:** [-1, 1]<br><br>Uwaga: R<sup>2</sup> często ma zakres (-inf, 1]. Program MSE może być większy od zaobserwowanej wariancji, więc R<sup>2</sup> może mieć arbitralnie duże wartości ujemne, w zależności od danych i prognoz modeli. Automatyczne klipy ML zgłosiły wyniki R<sup>2</sup> o wartości-1, więc wartość-1 dla r<sup>2</sup> prawdopodobnie oznacza, że wynik true R<sup>2</sup> jest mniejszy niż-1. Należy wziąć pod uwagę inne wartości metryk i właściwości danych przy interpretacji ujemnego wyniku R<sup>2</sup> .|[Obliczenia](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Pierwiastek średniej wartości "RMSE" oznacza pierwiastek kwadratowy oczekiwanej różnicy kwadratowej między obiektem docelowym a przewidywaniam. W przypadku nieobciążonego szacowania wartość RMSE jest równa odchyleniu standardowemu.<br> <br> **Cel:** Bliżej 0 <br> **Zakres:** [0, inf)<br><br>Typ<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error podzielona przez zakres danych. |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Pierwiastek średnika błędu w postaci logarytmu głównego jest pierwiastek kwadratowy oczekiwanego kwadratowego błędu logarytmu.<br><br>**Cel:** Bliżej 0 <br> **Zakres:** [0, inf) <br> <br>Typ <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error podzielona przez zakres danych.  |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Korelacja Spearman jest miarą nieparametryczną monotonicity relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona korelacja Spearman nie zakłada, że oba zestawy danych są zwykle dystrybuowane. Podobnie jak inne Współczynniki korelacji, Spearman różni się od-1 do 1 z 0 oznacza brak korelacji. Korelacje-1 lub 1 implikują dokładną relację monotoniczny. <br><br> Spearman jest metryką korelacji porządku rangi, co oznacza, że zmiany wartości przewidywanych lub rzeczywistych nie zmienią wyniku Spearman, jeśli nie zmienią kolejności wartości przewidywanych lub rzeczywistych.<br> <br> **Cel:** Bliżej 1 <br> **Zakres:** [-1, 1]|[Obliczenia](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalizacja metryki

Zautomatyzowana ML normalizuje metryki regresji i prognozowania, co umożliwia porównanie modeli przeszkolonych na danych z różnymi zakresami. Model przeszkolony na danych z większym zakresem ma wyższy błąd niż ten sam model przeszkolony na danych z mniejszym zakresem, chyba że ten błąd jest znormalizowany.

Chociaż nie ma standardowej metody normalizowania metryk błędów, zautomatyzowanej ML wykonuje wspólne podejście dzielenia błędu przez zakres danych: `normalized_error = error / (y_max - y_min)`

Podczas oceniania modelu prognozowania w danych szeregów czasowych zautomatyzowany ML wykonuje dodatkowe kroki w celu zapewnienia, że normalizacja odbywa się w odniesieniu do szeregów czasowych (ziarno), ponieważ każda seria czasu prawdopodobnie ma inną dystrybucję wartości docelowych.
## <a name="residuals"></a>Wartości resztkowe

Wykres reszty to histogram dotyczący błędów przewidywania (reszty) generowanych dla eksperymentów i prognozowania. Pozostałe są obliczane jako `y_predicted - y_true` dla wszystkich próbek, a następnie wyświetlane jako histogram, aby pokazać odchylenia modelu.

W tym przykładzie należy zauważyć, że oba modele są nieco rozchylone do przewidywania mniejszej niż wartość rzeczywista. Jest to nietypowe w przypadku zestawu danych z skośną dystrybucją rzeczywistych elementów docelowych, ale wskazuje na gorszą wydajność modelu. Dobry model będzie miał rozkład reszty, który ma wartość zero w przypadku niewielkich reszt. Gorszy model będzie miał rozkład reszty rozłożonego z mniejszą liczbą próbek wokół zera.

### <a name="residuals-chart-for-a-good-model"></a>Wykres reszty dla dobrego modelu
![Wykres reszty dla dobrego modelu](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Wykres reszty dla nieprawidłowego modelu
![Wykres reszty dla nieprawidłowego modelu](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Przewidywany a prawdziwy

Aby przeprowadzić regresję i prognozowanie eksperymentu wykres przewidywany a prawda przedstawia relację między funkcją docelową (wartości prawdziwe/rzeczywiste) i przewidywaniami modelu. Prawdziwe wartości są Binned wzdłuż osi x, a dla każdego pojemnika średnia wartość jest wykreślana z słupkami błędów. Dzięki temu można sprawdzić, czy model jest rozmieszczony w kierunku przewidywania określonych wartości. Wiersz wyświetla średnią prognozę i zacieniony obszar wskazuje wariancję prognoz wokół tego znaczenia.

Często najbardziej typowa wartość true będzie miała najdokładniejsze przewidywania o najmniejszej wariancji. Odległość linii trendu od idealnego wiersza, w `y = x` którym istnieje kilka prawdziwych wartości, jest dobrym wskaźnikiem wydajności modelu w oddziałach. Możesz użyć histogramu u dołu wykresu, aby przyczynić się do faktycznej dystrybucji danych. W tym więcej próbek danych, w których dystrybucja jest rozrzedzona, może poprawić wydajność modelu dla niewidocznych danych.

W tym przykładzie należy zauważyć, że lepszym modelem jest wiersz przewidywany w porównaniu do idealnej `y = x` linii.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Wykres przewidywany w porównaniu z rzeczywistym modelem
![Wykres przewidywany w porównaniu z rzeczywistym modelem](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Wykres przewidywany dla nieprawidłowego modelu
![Wykres przewidywany dla nieprawidłowego modelu](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Objaśnienia modelu i znaczenie funkcji

Chociaż metryki i wykresy oceny modelu są dobre do mierzenia ogólnej jakości modelu, sprawdzanie, który zestaw danych zawiera model, który jest używany do zapewnienia, że jego przewidywania jest niezbędne w przypadku praktycznego odpowiedzialnego AI. Dlatego zautomatyzowanej sieci zapewnia pulpit nawigacyjny z możliwością interpretacji modelu, który umożliwia mierzenie i raportowanie względnych wkładów funkcji zestawu danych.

Aby wyświetlić pulpit nawigacyjny z interpretacją w Studio:
1. [Zaloguj się do programu Studio](https://ml.azure.com/) i przejdź do obszaru roboczego
2. W menu po lewej stronie wybierz pozycję **eksperymenty**
3. Wybierz swój eksperyment z listy eksperymentów
4. W tabeli w dolnej części strony wybierz uruchomienie AutoML
5. Na karcie **modele** wybierz **nazwę algorytmu** dla modelu, który chcesz wyjaśnić
6. Na karcie **wyjaśnienia** można zobaczyć, że wyjaśnienie zostało już utworzone, jeśli model jest najlepszy
7. Aby utworzyć nowe wyjaśnienie, wybierz pozycję **Wyjaśnij model** i wybierz zdalne obliczenie, za pomocą którego mają zostać obliczone wyjaśnienia

[Dowiedz się więcej na temat wyjaśnień modelu w zautomatyzowanej ml](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> Model ForecastTCN nie jest obecnie obsługiwany przez automatyczne objaśnienia ML, a inne modele prognozowania mogą mieć ograniczony dostęp do narzędzi do interpretacji.

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [przykładowy model uczenia maszynowego](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
* W przypadku zautomatyzowanych pytań określonych w ML należy skontaktować się z askautomatedml@microsoft.com .
