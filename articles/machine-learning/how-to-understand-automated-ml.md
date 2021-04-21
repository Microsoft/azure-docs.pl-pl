---
title: Ocena wyników eksperymentu autoML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wyświetlać i oceniać wykresy i metryki dla każdego z przebiegów eksperymentu zautomatyzowanego uczenia maszynowego.
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 2bed95385823a167c7a31eed11d752894984ea38
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791881"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Ocena wyników eksperymentu zautomatyzowanego uczenia maszynowego

W tym artykule dowiesz się, jak oceniać i porównywać modele wytrenowane przez eksperyment zautomatyzowanego uczenia maszynowego (zautomatyzowanego uczenia maszynowego). W trakcie eksperymentu zautomatyzowanego uczenia maszynowego jest tworzona wiele przebiegów, a każdy przebieg tworzy model. Dla każdego modelu zautomatyzowane ml generuje metryki oceny i wykresy, które ułatwiają mierzenie wydajności modelu. 

Na przykład zautomatyzowane ml generuje następujące wykresy na podstawie typu eksperymentu.

| Klasyfikacja| Regresja/prognozowanie |
| ----------------------------------------------------------- | ---------------------------------------- |
| [Macierz pomyłek](#confusion-matrix)                       | [Histogram reszt](#residuals)        |
| [Krzywa ROC (Receiver operating Characteristic)](#roc-curve) | [Przewidywana a prawda](#predicted-vs-true) |
| [Krzywa precyzji i przywołania](#precision-recall-curve)      |                                          |
| [Lift curve](#lift-curve)                                   |                                          |
| [Krzywa skumulowanych zysków](#cumulative-gains-curve)           |                                          |
| [Krzywa krzywa krzywa](#calibration-curve)                     |                     


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. (Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz](https://aka.ms/AMLFree) bezpłatne konto)
- Eksperyment Azure Machine Learning utworzony za pomocą jednego z tych eksperymentów:
  - Kod [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) (nie jest wymagany kod)
  - Zestaw [SDK Azure Machine Learning Python](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>Wyświetlanie wyników uruchomienia

Po zakończeniu eksperymentu zautomatyzowanego uczenia maszynowego można znaleźć historię przebiegów za pośrednictwem:
  - Przeglądarka z [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)
  - Notes Jupyter korzystający z [widżetu RunDetails Jupyter](/python/api/azureml-widgets/azureml.widgets.rundetails)

Poniższe kroki i klip wideo pokazują, jak wyświetlać historię uruchamiania oraz metryki i wykresy oceny modelu w programie Studio:

1. [Zaloguj się do programu Studio](https://ml.azure.com/) i przejdź do obszaru roboczego.
1. W menu po lewej stronie wybierz pozycję **Experiments (Eksperymenty).**
1. Wybierz eksperyment z listy eksperymentów.
1. W tabeli w dolnej części strony wybierz zautomatyzowany przebieg uczenia maszynowego.
1. Na karcie **Modele** wybierz nazwę **algorytmu** dla modelu, który chcesz ocenić.
1. Na karcie **Metryki** użyj pól wyboru po lewej stronie, aby wyświetlić metryki i wykresy.

![Kroki wyświetlania metryk w programie Studio](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>Metryki klasyfikacji

Zautomatyzowane uczenia maszynowego oblicza metryki wydajności dla każdego modelu klasyfikacji wygenerowanego dla eksperymentu. Te metryki są oparte na implementacji biblioteki scikit learn. 

Wiele metryk klasyfikacji jest zdefiniowanych dla klasyfikacji binarnej w dwóch klasach i wymaga uśredniania w klasach, aby uzyskać jeden wynik dla klasyfikacji wieloklasowej. Scikit-learn udostępnia kilka metod uśredniania, z których trzy zautomatyzowane uczenia maszynowego uwidacznia: **makra,** **mikro** i **ważone**.

- **Makro** — obliczanie metryki dla każdej klasy i obliczenie średniej bez wagi
- **Mikro** — oblicz metrykę globalnie, licząc łączną liczbę wyników prawdziwie dodatnich, fałszywie ujemnych i fałszywie dodatnich (niezależnie od klas).
- **Ważone** — oblicz metrykę dla każdej klasy i weź średnią ważoną na podstawie liczby próbek na klasę.

Chociaż każda metoda uśredniania ma swoje zalety, jedną z typowych kwestii podczas wybierania odpowiedniej metody jest dysproporcja klas. Jeśli klasy mają różne liczby próbek, bardziej wartością informacyjną może być użycie średniej makra, w przypadku której klasy wywłaszczające mają równe wagi dla większości klas. Dowiedz się więcej o [metrykach binarnych i wieloklasowych w zautomatyzowanym u ml.](#binary-vs-multiclass-classification-metrics) 

W poniższej tabeli podsumowano metryki wydajności modelu, które zautomatyzowane uczenia maszynowego oblicza dla każdego modelu klasyfikacji wygenerowanego na podstawie eksperymentu. Aby uzyskać więcej informacji, zobacz dokumentację biblioteki scikit-learn powiązaną w polu **Obliczanie** każdej metryki. 

|Metric|Opis|Obliczenia|
|--|--|---|
|AUC | AUC to obszar pod krzywą [cech operacyjnych odbiornika](#roc-curve).<br><br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1]<br> <br>Obsługiwane nazwy metryk: <li>`AUC_macro`, średnią arytmetyczną AUC dla każdej klasy.<li> `AUC_micro`obliczany przez połączenie wyników prawdziwie dodatnich i fałszywie dodatnich z każdej klasy. <li> `AUC_weighted`, średnia arytmetyczna wyniku dla każdej klasy, ważona przez liczbę wystąpień true w każdej klasie.<br><br>Uwaga: Wartości AUC zgłaszane przez zautomatyzowane uczenia maszynowego mogą nie być zgodne z wykresem ROC, jeśli istnieją tylko dwie klasy. W przypadku klasyfikacji binarnej podstawowa implementacja biblioteki scikit-learn AUC w rzeczywistości nie stosuje uśredniania makro/mikro/wagi. Zamiast tego jest zwracana AUC najbardziej prawdopodobnej dodatniej klasy. Wykres ROC w dalszym ciągu stosuje klasę do klasyfikacji binarnej tak samo jak dla wieloklasowej.  |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|accuracy| Dokładność to stosunek przewidywań, które dokładnie pasują do prawdziwych etykiet klas. <br> <br>**Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Średnia precyzja podsumowuje krzywą precyzji i precyzji jako średnią ważoną dokładności osiąganą przy każdym progu, ze wzrostem wartości odwołania z poprzedniego progu użytego jako waga. <br><br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1]<br> <br>Obsługiwane nazwy metryk:<li>`average_precision_score_macro`, średnią arytmetyczną średniej precyzji dla każdej klasy.<li> `average_precision_score_micro`obliczany przez połączenie wyników prawdziwie dodatnich i fałszywie dodatnich w każdym ćwiknie.<li>`average_precision_score_weighted`, średnia arytmetyczna średniej precyzji dla każdej klasy, ważona przez liczbę wystąpień true w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Zrównoważona dokładność to średnia arytmetyczna przywoływania dla każdej klasy.<br> <br>**Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|Wynik F1 to średnia czasowa precyzji i przywoływu. Jest to dobra zrównoważona miara zarówno wyników fałszywie dodatnich, jak i fałszywie ujemnych. Nie uwzględnia ono jednak wartości prawdziwie ujemnych. <br> <br>**Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1]<br> <br>Obsługiwane nazwy metryk:<li>  `f1_score_macro`: średnia arytmetyczna wyniku F1 dla każdej klasy. <li> `f1_score_micro`: obliczane przez zliczanie wszystkich wyników prawdziwie dodatnich, fałszywie ujemnych i fałszywie dodatnich. <li> `f1_score_weighted`: średnia ważona według częstotliwości klas oceny F1 dla każdej klasy.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|Jest to funkcja utraty używana w regresji logistycznej (wielomianowej) i jej rozszerzeniach, takich jak sieci neuronowe, zdefiniowana jako ujemne prawdopodobieństwo dziennika dla prawdziwych etykiet przy użyciu przewidywań klasyfikatora probabilistycznego. <br><br> **Cel:** Im bliżej 0, tym lepiej <br> **Zakres:** [0, inf)|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| Znormalizowane odwołania makra są uśrednione i znormalizowane w makrach, dzięki czemu wydajność losowa ma wynik 0, a idealna wydajność ma wynik 1. <br> <br>**Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>gdzie jest `R` oczekiwaną wartością `recall_score_macro` dla przewidywań losowych.<br><br>`R = 0.5`&nbsp;klasyfikacja &nbsp; &nbsp; binarna. <br>`R = (1 / C)` w przypadku problemów klasyfikacji klasy C.|
matthews_correlation | Współczynnik korelacji Matthewsa jest zrównoważoną miarą dokładności, której można użyć, nawet jeśli jedna klasa ma o wiele więcej próbek niż inna. Współczynnik 1 oznacza doskonałe przewidywanie, 0 przewidywań losowych i -1 przewidywania odwrotnego.<br><br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [-1, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
precision|Precyzja to zdolność modelu do unikania oznaczania próbek negatywnych jako pozytywnych. <br><br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1]<br> <br>Obsługiwane nazwy metryk: <li> `precision_score_macro`, średnią arytmetyczną precyzji dla każdej klasy. <li> `precision_score_micro`, obliczane globalnie przez zliczanie wszystkich wyników prawdziwie dodatnich i fałszywie dodatnich. <li> `precision_score_weighted`, średnią arytmetyczną precyzji dla każdej klasy, ważoną liczbą wystąpień true w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
recall| Przypomnij sobie, że model jest w stanie wykryć wszystkie dodatnie próbki. <br><br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [0, 1]<br> <br>Obsługiwane nazwy metryk: <li>`recall_score_macro`: średnia arytmetyczna odwołania dla każdej klasy. <li> `recall_score_micro`: obliczane globalnie przez zliczanie wszystkich wyników prawdziwie dodatnich, fałszywie ujemnych i fałszywie dodatnich.<li> `recall_score_weighted`: średnia arytmetyczna odwołania dla każdej klasy, ważona przez liczbę wystąpień true w każdej klasie.|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|Dokładność ważona to dokładność, w której każda próbka jest ważona przez łączną liczbę próbek należących do tej samej klasy. <br><br>**Cel:** Im bliżej 1, tym lepiej <br>**Zakres:** [0, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>Metryki klasyfikacji binarnej a wieloklasowej

Zautomatyzowane uczenia maszynowego nie rozróżnia metryk binarnych i wieloklasowych. Te same metryki walidacji są zgłaszane, czy zestaw danych ma dwie klasy, czy więcej niż dwie klasy. Jednak niektóre metryki są przeznaczone do klasyfikacji wieloklasowej. Po zastosowaniu do binarnego zestawu danych te metryki nie będą traktować żadnej klasy jako `true` klasy, jak można się było spodziewać. Metryki, które są wyraźnie przeznaczone dla wieloklasowych, mają sufiks `micro` , `macro` lub `weighted` . Przykłady `average_precision_score` obejmują , , , i `f1_score` `precision_score` `recall_score` `AUC` .

Na przykład zamiast obliczać przywołanie jako , wieloklasowe średnie odwołania ( , lub ) są uśrednione w obu klasach binarnego zestawu `tp / (tp + fn)` `micro` danych `macro` `weighted` klasyfikacji. Jest to równoważne oddzielne obliczaniu odwołania dla klasy i klasy, a następnie obliczaniu `true` `false` średniej z tych dwóch klas.

Zautomatyzowane uczenia maszynowego nie oblicza metryk binarnych, czyli metryk dla binarnych zestawów danych klasyfikacji. Jednak te metryki można ręcznie obliczyć [](#confusion-matrix) przy użyciu macierzy pomyłek wygenerowanej przez zautomatyzowane mleć dla tego konkretnego uruchomienia. Na przykład można obliczyć precyzję , z wartościami prawdziwie dodatnimi i fałszywie dodatnimi pokazanymi na wykresie `tp / (tp + fp)` macierzy pomyłek 2x2.

## <a name="confusion-matrix"></a>Macierz pomyłek

Macierze pomyłek zapewniają wizualizację tego, w jaki sposób model uczenia maszynowego popełnia systematyczne błędy w przewidywaniach modeli klasyfikacji. Słowo "pomyłek" w nazwie pochodzi z przykładów modelu "mylących" lub błędnych etykiet. Komórka w wierszu i kolumnie w macierzy pomyłek zawiera liczbę próbek w zestawie danych oceny, które należą do klasy i zostały sklasyfikowane przez `i` `j` model jako klasa `C_i` `C_j` .

W studio ciemniejsze komórki wskazują większą liczbę próbek. Wybranie **widoku Znormalizowane** na liście rozwijanej spowoduje normalizację w każdym wierszu macierzy w celu pokazania procentu klasy przewidywanej `C_i` jako klasa `C_j` . Zaletą domyślnego widoku **Raw** (Nieprzetworzone) jest to, że można zobaczyć, czy dysproporcja w rozmieszczeniach rzeczywistych klas spowodowała, że model błędnie sklasyfikował próbki z klasy wytworzonej, co jest częstym problemem w niezrównoważonych zestawach danych.

Macierz pomyłek dobrego modelu będzie zawierała większość próbek wzdłuż przekątnej.

### <a name="confusion-matrix-for-a-good-model"></a>Macierz pomyłek dla dobrego modelu 
![Macierz pomyłek dla dobrego modelu ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>Macierz pomyłek dla złego modelu
![Macierz pomyłek dla złego modelu](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>Krzywa ROC

Krzywa ROC (Receiver Operating Characteristic) wykreśla relację między wartościami prawdziwie dodatnimi (TPR) i wskaźnikami fałszywie dodatnimi (FPR, false positive rate) wraz ze zmianami progu decyzyjnego. Krzywa ROC może być mniej wartościowa w przypadku trenowania modeli w zestawach danych z wysoką dysproporcją klas, ponieważ większość klas może zaciemniać składowe z klas wytłaczających.

Obszar pod krzywą (AUC) może być interpretowany jako proporcja poprawnie sklasyfikowanych próbek. Dokładniej, wartość AUC jest prawdopodobieństwem, że klasyfikator klasyfikuje losowo wybraną próbę dodatnią na wyższym poziomie niż losowo wybrana próbka ujemna. Kształt krzywej daje intuicję dla relacji między TPR i FPR jako funkcja progu klasyfikacji lub granicy decyzyjnej.

Krzywa, która zbliża się do lewego górnego rogu wykresu, zbliża się do 100% czasu TPR i 0% wartości FPR, najlepszego możliwego modelu. Model losowy wyprodukuje krzywą ROC wzdłuż linii od lewego dolnego `y = x` rogu do prawego górnego rogu. Gorszy niż model losowy będzie miał krzywą ROC, która ma spadki poniżej `y = x` linii.
> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych wytwarzanych dla zautomatyzowanych modeli uczenia maszynowego może służyć do oceny modelu dla każdej klasy lub uśredniać go we wszystkich klasach. Możesz przełączać się między tymi różnymi widokami, klikając etykiety klas w legendzie po prawej stronie wykresu.

### <a name="roc-curve-for-a-good-model"></a>Krzywa ROC dla dobrego modelu
![Krzywa ROC dla dobrego modelu](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>Krzywa ROC dla złego modelu
![Krzywa ROC dla złego modelu](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>Krzywa precyzji i odwołania

Krzywa precyzji i przywołania wykreśla relację między precyzją a przywoływem w przypadku zmiany progu decyzyjnego. Jak pamiętasz, zdolność modelu do wykrywania wszystkich próbek dodatnich i precyzji to zdolność modelu do unikania oznaczania próbek ujemnych jako pozytywnych. Niektóre problemy biznesowe mogą wymagać większej precyzji i większej precyzji w zależności od względnego znaczenia unikania wyników fałszywie ujemnych i wyników fałszywie dodatnich.
> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych wytwarzanych dla zautomatyzowanych modeli uczenia maszynowego może służyć do oceny modelu dla każdej klasy lub uśredniać go we wszystkich klasach. Możesz przełączać się między tymi różnymi widokami, klikając etykiety klas w legendzie po prawej stronie wykresu.
### <a name="precision-recall-curve-for-a-good-model"></a>Krzywa precyzji i przywołania dla dobrego modelu
![Krzywa precyzji i przywołania dla dobrego modelu](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>Krzywa precyzji i przywołania dla słabego modelu
![Krzywa precyzji i przywołania dla słabego modelu](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>Krzywa skumulowanych zysków

Na krzywej skumulowanych zysków kreślony jest procent prób dodatnich prawidłowo sklasyfikowanych jako funkcja procentu próbek, które rozważamy, gdy rozważamy próbki w kolejności przewidywanego prawdopodobieństwa.

Aby obliczyć zysk, najpierw posortuj wszystkie próbki od najwyższego do najniższego prawdopodobieństwa przewidywanego przez model. Następnie skorzystaj `x%` z przewidywań o najwyższym poziomie ufności. Podziel liczbę wykrytych w nich próbek dodatnich przez `x%` łączną liczbę próbek dodatnich, aby uzyskać zysk. Skumulowany zysk to procent dodatnich próbek wykrywanych podczas rozważania pewnego procentu danych, które najprawdopodobniej będą należeć do klasy dodatniej.

Idealny model będzie oceniać wszystkie dodatnie próbki powyżej wszystkich prób negatywnych, dając skumulowaną krzywą wzrostu, która składa się z dwóch prostych segmentów. Pierwszy to linia z nachyleniem od do gdzie jest ułamkiem próbek należących do klasy dodatniej `1 / x` ( jeśli klasy są `(0, 0)` `(x, 1)` `x` `1 / num_classes` zrównoważone). Drugi to pozioma linia od `(x, 1)` do `(1, 1)` . W pierwszym segmencie wszystkie próbki dodatnie są klasyfikowane poprawnie, a skumulowany zysk sięga `100%` pierwszego `x%` z rozważanych próbek.

Podstawowy model losowy będzie miał skumulowaną krzywą wzrostu, po której dla próbek uważanych tylko za łączną liczbę `y = x` `x%` `x%` próbek dodatnich wykryto. Idealny model będzie miał średnią krzywą mikro, która dotyka lewego górnego rogu, oraz linię średniej makra, która ma nachylenie do momentu uzyskania skumulowanego zysku w 100%, a następnie w poziomie do momentu, gdy procent danych wynosi `1 / num_classes` 100.
> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych wytwarzanych dla modeli zautomatyzowanego uczenia maszynowego może służyć do oceny modelu według klasy lub uśredniony dla wszystkich klas. Możesz przełączać się między tymi różnymi widokami, klikając etykiety klas w legendzie po prawej stronie wykresu.
### <a name="cumulative-gains-curve-for-a-good-model"></a>Skumulowana krzywa zysków dla dobrego modelu
![Skumulowana krzywa zysków dla dobrego modelu](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>Skumulowana krzywa zysków dla złego modelu
![Skumulowana krzywa zysków dla złego modelu](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>Lift curve

Krzywa lift pokazuje, ile razy lepiej działa model w porównaniu z modelem losowym. Lift jest definiowany jako stosunek skumulowanego zysku do skumulowanego zysku modelu losowego.

Ta względna wydajność uwzględnia fakt, że klasyfikacja jest trudniejsza w przypadku zwiększania liczby klas. (Model losowy niepoprawnie przewiduje większą część próbek z zestawu danych z 10 klasami w porównaniu z zestawem danych z dwiema klasami)

Podstawowa krzywa lift to linia, w której wydajność modelu jest spójna `y = 1` z wydajnością modelu losowego. Ogólnie rzecz biorąc, krzywa lift dla dobrego modelu będzie wyższa na tym wykresie i dalej od osi X, co pokazuje, że gdy model jest najbędszy w swoich przewidywaniach, działa wielokrotnie lepiej niż losowe zgadywanie.

> [!TIP]
> W przypadku eksperymentów klasyfikacji każdy z wykresów liniowych wytwarzanych dla zautomatyzowanych modeli uczenia maszynowego może służyć do oceny modelu dla każdej klasy lub uśredniać go we wszystkich klasach. Możesz przełączać się między tymi różnymi widokami, klikając etykiety klas w legendzie po prawej stronie wykresu.
### <a name="lift-curve-for-a-good-model"></a>Lift curve for a good model (Lift curve for a good model)
![Lift curve for a good model (Lift curve for a good model)](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>Lift curve for a bad model (Lift curve for a bad model)
![Lift curve for a bad model (Lift curve for a bad model)](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>Krzywa krzywa krzywa

Krzywa krzywa krzywa wykreśla zaufanie modelu do przewidywań względem proporcji próbek dodatnich na każdym poziomie ufności. Dobrze skalibrowany model prawidłowo sklasyfikuje 100% przewidywań, do których przypisuje 100% ufności, 50% przewidywań przypisuje 50% ufności, 20% przewidywań przypisuje 20% pewności i tak dalej. Idealnie skalibrowany model będzie miał krzywą rozkładu po linii, gdzie model doskonale przewiduje prawdopodobieństwo, że próbki należą `y = x` do każdej klasy.

Model o zbyt wielu pewnościach będzie zbyt przewidywać prawdopodobieństwa zbliżone do zera i jedno, rzadko nie mieć pewności co do klasy każdej próbki, a krzywa będzie wyglądać podobnie do odwrotnego "S". Model o mniejszej pewności przypisze średnie prawdopodobieństwo do przewidywanej klasy, a skojarzona krzywa będzie wyglądać podobnie do "S". Krzywa krzywa nie przedstawia zdolności modelu do poprawnego klasyfikowania, ale zamiast tego zdolności do poprawnego przypisywania pewności do swoich przewidywań. Zły model może nadal mieć dobrą krzywą, jeśli model prawidłowo przypisze niską pewność i wysoką niepewność.

> [!NOTE]
> Krzywa krzywa jest wrażliwa na liczbę próbek, więc mały zestaw weryfikacyjny może dawać zaszumione wyniki, które mogą być trudne do interpretacji. Nie musi to oznaczać, że model nie jest dobrze skalibrowany.

### <a name="calibration-curve-for-a-good-model"></a>Krzywa krzywa krzywa krzywa dla dobrego modelu
![Krzywa krzywa krzywa krzywa dla dobrego modelu](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>Krzywa krzywa krzywa krzywa dla słabego modelu
![Krzywa krzywa krzywa krzywa dla słabego modelu](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>Metryki regresji/prognozowania

Zautomatyzowane uczenia maszynowego oblicza te same metryki wydajności dla każdego wygenerowanego modelu, niezależnie od tego, czy jest to eksperyment regresji czy prognozowania. Te metryki są również poddawane normalizacji, aby umożliwić porównywanie modeli przeszkolonych na danych z różnymi zakresami. Aby dowiedzieć się więcej, zobacz [normalizację metryk.](#metric-normalization)  

W poniższej tabeli podsumowano metryki wydajności modelu wygenerowane dla eksperymentów regresji i prognozowania. Podobnie jak metryki klasyfikacji, te metryki są również oparte na implementacjach biblioteki scikit learn. Odpowiednia dokumentacja biblioteki scikit learn jest odpowiednio połączona w **polu** Obliczenia.

|Metric|Opis|Obliczenia|
--|--|--|
explained_variance|Wyjaśniona wariancja mierzy zakres, w jakim model odpowiada odmianie zmiennej docelowej. Jest to procent spadku wariancji oryginalnych danych do wariancji błędów. Gdy średnia błędów wynosi 0, jest ona równa współczynnikowi ustalenia (zobacz r2_score poniżej). <br> <br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** (-inf, 1]|[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|Średni błąd bezwzględny to oczekiwana wartość bezwzględna różnicy między wartością docelową a przewidywaną.<br><br> **Cel:** Im bliżej 0, tym lepiej <br> **Zakres:** [0, inf) <br><br> Typy: <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`, mean_absolute_error podzielone przez zakres danych. | [Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|Średni bezwzględny błąd procentowy (MAPE) jest miarą średniej różnicy między wartością przewidywaną a wartością rzeczywistą.<br><br> **Cel:** Im bliżej 0, tym lepiej <br> **Zakres:** [0, inf) ||
median_absolute_error|Mediana bezwzględnego błędu to mediana wszystkich bezwzględnych różnic między wartością docelową i przewidywaną. Ta strata jest niezawodna dla odejścia.<br><br> **Cel:** Im bliżej 0, tym lepiej <br> **Zakres:** [0, inf)<br><br>Typy: <br> `median_absolute_error`<br> `normalized_median_absolute_error`: median_absolute_error podzielone przez zakres danych. |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|R<sup>2</sup> (współczynnik oznaczania) mierzy proporcjonalną redukcję błędu średniego kwadratu (MSE) względem całkowitej wariancji obserwowanych danych. <br> <br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [-1, 1]<br><br>Uwaga: R<sup>2</sup> często ma zakres (-inf, 1]. MsE może być większe niż obserwowana wariancja, więc R<sup>2</sup> może mieć dowolnie duże wartości ujemne, w zależności od danych i przewidywań modelu. Automatyczne klipy uczenia maszynowego zgłosiły wyniki R<sup>2</sup> na -1, więc wartość -1 dla R<sup>2 prawdopodobnie</sup> oznacza, że prawdziwy wynik R<sup>2</sup> jest mniejszy niż -1. Podczas interpretowania negatywnego wyniku R<sup>2</sup> należy wziąć pod uwagę inne wartości metryk i właściwości danych.|[Obliczenia](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Główny błąd średniego kwadratu (RMSE, root mean squared error) to kwadrat kwadratowy oczekiwanej kwadratowej różnicy między wartością docelową a przewidywaną. W przypadku nieobsadnego estymatora RMSE jest równe odchyleniu standardowej.<br> <br> **Cel:** Im bliżej 0, tym lepiej <br> **Zakres:** [0, inf)<br><br>Typy:<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`: root_mean_squared_error podzielone przez zakres danych. |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|Główny błąd dziennika średniego kwadratu to kwadrat kwadratowy oczekiwanego błędu logarytmii kwadratowej.<br><br>**Cel:** Im bliżej 0, tym lepiej <br> **Zakres:** [0, inf) <br> <br>Typy: <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`: root_mean_squared_log_error podzielone przez zakres danych.  |[Obliczenia](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Korelacja Spearmana to nieparametryczna miara monotonii relacji między dwoma zestawami danych. W przeciwieństwie do korelacji Pearsona, korelacja Spearmana nie zakłada, że oba zestawy danych są zwykle rozproszone. Podobnie jak w przypadku innych współczynników korelacji, spearman waha się od -1 do 1, a wartość 0 oznacza brak korelacji. Korelacje -1 lub 1 oznaczają dokładną relację monotoniczną. <br><br> Spearman to metryka korelacji kolejności rangi, co oznacza, że zmiany w wartościach przewidywanych lub rzeczywistych nie zmienią wyniku Spearmana, jeśli nie zmienią one kolejności klasyfikacji wartości przewidywanych lub rzeczywistych.<br> <br> **Cel:** Im bliżej 1, tym lepiej <br> **Zakres:** [-1, 1]|[Obliczenia](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>Normalizacja metryk

Zautomatyzowane ml normalizuje metryki regresji i prognozowania, co umożliwia porównywanie modeli wytrenowych na danych z różnymi zakresami. Model wytrenowany na danych z większym zakresem ma większy błąd niż ten sam model wytrenowany na danych o mniejszym zakresie, chyba że ten błąd jest znormalizowany.

Chociaż nie istnieje standardowa metoda normalizacji metryk błędów, zautomatyzowane mleowanie przyjmuje typową metodę dzielenia błędu przez zakres danych: `normalized_error = error / (y_max - y_min)`

Podczas oceny modelu prognozowania na podstawie danych szeregów czasowych zautomatyzowane ml podejmuje dodatkowe kroki, aby zapewnić, że normalizacja będzie odbywać się na identyfikator szeregu czasowego (ziarno), ponieważ każdy szereg czasowy prawdopodobnie ma inny rozkład wartości docelowych.
## <a name="residuals"></a>Wartości resztkowe

Wykres reszt jest histogramem błędów przewidywania (reszt) wygenerowanych dla eksperymentów regresji i prognozowania. Reszty są obliczane jako `y_predicted - y_true` dla wszystkich próbek, a następnie wyświetlane jako histogram, aby pokazać odchylenie modelu.

W tym przykładzie należy zauważyć, że oba modele są nieco odchyleniem do przewidywania wartości niższej niż wartość rzeczywista. Nie jest to niczym niezwykłym w przypadku zestawu danych ze skośną dystrybucją rzeczywistych obiektów docelowych, ale wskazuje na słabszą wydajność modelu. Dobry model będzie miał rozkład reszt, który ma wartość szczytową z zerem i kilkoma resztami na skrajnych wartościach. Gorszy model będzie miał rozkład reszt z mniejszą liczbą próbek około zera.

### <a name="residuals-chart-for-a-good-model"></a>Wykres reszt dla dobrego modelu
![Wykres reszt dla dobrego modelu](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>Wykres reszt dla złego modelu
![Wykres reszt dla złego modelu](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>Przewidywana a prawda

W przypadku eksperymentu regresji i prognozowania wykres przewidywany względem wartości true przedstawia relację między cechą docelową (wartościami true/actual) i przewidywaniami modelu. Wartości true są kreślone wzdłuż osi x, a dla każdego pojemnika średnia przewidywana wartość jest kreślona za pomocą pasków błędów. Dzięki temu można zobaczyć, czy model jest stronniczy w przewidywaniu określonych wartości. Linia wyświetla średnie przewidywanie, a zacieniony obszar wskazuje wariancja przewidywań wokół tej średniej.

Często najbardziej typowa wartość true będzie mieć najdokładniejsze przewidywania o najniższej wariancji. Odległość linii trendu od linii idealnej, gdzie istnieje kilka prawdziwych wartości, jest dobrą miarą wydajności modelu `y = x` na wartościach od zewnątrz. Możesz użyć histogramu w dolnej części wykresu, aby określić rzeczywistą rozkład danych. Do uwzględnienia większej liczby przykładów danych, w których rozkład jest rozrzedziony, można poprawić wydajność modelu w przypadku niezaukanych danych.

W tym przykładzie należy zauważyć, że lepszy model ma przewidywaną i prawdziwą linię, która jest bliżej linii `y = x` idealnej.

### <a name="predicted-vs-true-chart-for-a-good-model"></a>Wykres przewidywany w porównaniu z wartościami true dla dobrego modelu
![Wykres przewidywany w porównaniu z wartościami true dla dobrego modelu](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>Wykres przewidywany w porównaniu z wartościami true dla słabego modelu
![Wykres przewidywany w porównaniu z wartościami true dla słabego modelu](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>Wyjaśnienia modelu i ważność funkcji

Mimo że metryki i wykresy oceny modelu są dobre do mierzenia ogólnej jakości modelu, sprawdzanie, które zestawy danych mają cechy modelu używanego do prognozowania, jest niezbędne podczas praktyk odpowiedzialnego korzystania ze AI. Dlatego zautomatyzowane uczenia maszynowego udostępnia pulpit nawigacyjny z wyjaśnieniami modeli, który umożliwia pomiar i zgłaszanie względnego wkładu funkcji zestawu danych. Zobacz, jak [wyświetlić pulpit nawigacyjny z wyjaśnieniami na Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md#model-explanations-preview).

Aby uzyskać informacje na temat pierwszego środowiska kodu, zobacz, jak skonfigurować wyjaśnienia modelu dla eksperymentów zautomatyzowanego uczenia maszynowego przy [użyciu zestawu SDK Azure Machine Learning Python.](how-to-machine-learning-interpretability-automl.md)

> [!NOTE]
> Model ForecastTCN nie jest obecnie obsługiwany przez zautomatyzowane wyjaśnienia uczenia maszynowego, a inne modele prognozowania mogą mieć ograniczony dostęp do narzędzi do interpretacji.

## <a name="next-steps"></a>Następne kroki
* Wypróbuj [przykładowe notesy z wyjaśnieniem zautomatyzowanego modelu uczenia maszynowego.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
* W przypadku pytań specyficznych dla zautomatyzowanego uczenia maszynowego należy uzyskać dostęp do . askautomatedml@microsoft.com
