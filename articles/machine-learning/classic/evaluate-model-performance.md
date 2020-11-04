---
title: 'ML Studio (klasyczny): Oceń & modele weryfikacji krzyżowej — Azure'
description: Informacje o metrykach, których można użyć do monitorowania wydajności modelu w Azure Machine Learning Studio (klasyczny).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: ca369f8a3e680a4d2aae49df83dda0cdd3dc4075
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310158"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Oceń wydajność modelu w Azure Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do. ](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Ten artykuł zawiera informacje na temat metryk, których można użyć do monitorowania wydajności modelu w Azure Machine Learning Studio (klasyczny).  Ocenianie wydajności modelu jest jednym z podstawowych etapów procesu analizy danych. Wskazuje, jak pomyślne ocenianie (przewidywania) zestawu danych zostało przeprowadzone przez szkolony model. Azure Machine Learning Studio (klasyczny) obsługuje Obliczanie modelu przez dwa z głównych modułów uczenia maszynowego: 
+ [Ocena modelu][evaluate-model] 
+ [Model weryfikacji krzyżowej][cross-validate-model]

Te moduły umożliwiają sprawdzenie, w jaki sposób Twój model wykonuje różne metryki, które są często używane w uczeniu maszynowym i danych statystycznych.

Należy wziąć pod uwagę ocenę modeli wraz z:
+ [Optymalizacja parametrów dla algorytmów](algorithm-parameters-optimize.md)
+ [Możliwość interpretowania modelu](interpret-model-results.md)

Prezentowane są trzy popularne scenariusze uczenia nauki: 
* ubytk
* klasyfikacja binarna 
* Klasyfikacja wieloklasowa


## <a name="evaluation-vs-cross-validation"></a>Ocena a weryfikacja krzyżowa
Ocena i sprawdzanie krzyżowe są standardowymi sposobami mierzenia wydajności modelu. Zarówno generują metryki oceny, które można sprawdzić, jak i porównać z innymi modelami.

[Oceń model][evaluate-model] oczekuje zestawu danych z oceną jako dane wejściowe (lub dwa w przypadku, gdy chcesz porównać wydajność dwóch różnych modeli). W związku z tym należy przeprowadzić uczenie modelu przy użyciu modułu [uczenie modelu][train-model] i przeznaczyć prognozy dla niektórych zestawów danych przy użyciu modułu [modelu oceny][score-model] , aby można było oszacować wyniki. Obliczenia opierają się na etykietach i prawdopodobieństwach z wynikowymi etykietami, z których wszystkie są wyprowadzane przez moduł [modelu wyniku][score-model] .

Alternatywnie można użyć weryfikacji krzyżowej do wykonywania wielu różnych podzestawów danych wejściowych (10 zagięć) automatycznie. Dane wejściowe są podzielone na 10 części, z których jeden jest zastrzeżony do testowania, a drugi 9 do szkoleń. Ten proces jest powtarzany 10 razy, a metryki oceny są uśredniane. Pomaga to w ustaleniu, jak dobrze model będzie uogólniać do nowych zestawów danych. Moduł [modelu krzyżowego sprawdzania poprawności][cross-validate-model] przejmuje niesprawdzony model i niepewną część zestawu danych i wyprowadza wyniki oceny dla każdego z 10 zagięć, a także do wyników średniej.

W poniższych sekcjach utworzysz proste modele regresji i klasyfikacji oraz Oceń ich wydajność przy użyciu zarówno [modelu oceny][evaluate-model] , jak i modułowego [sprawdzania poprawności][cross-validate-model] .

## <a name="evaluating-a-regression-model"></a>Ocenianie modelu regresji
Załóżmy, że chcemy przewidzieć cenę samochodu przy użyciu funkcji, takich jak wymiary, możliwości techniczne, specyfikacje silnika i tak dalej. Jest to typowy problem z regresją, gdzie zmienna docelowa ( *Cena* ) jest stałą wartością liczbową. Możemy dopasować model regresji liniowej, który zapewnia wartości funkcji określonego samochodu, można przewidzieć cenę tego samochodu. Ten model regresji może służyć do oceny tego samego zestawu danych, który został przeszkolony. Po przeprowadzeniu przewidywanych cen samochodu możemy oszacować wydajność modelu, sprawdzając, ile prognoz odchyleń od rzeczywistych cen. Aby to zilustrować, korzystamy z *zestawu danych cen dla samochodów (RAW)* dostępnego w sekcji **zapisywanych zestawów** danych w Machine Learning Studio (klasyczny).

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego w Azure Machine Learning Studio (klasyczny):

* Dane cen samochodów (RAW)
* [Regresja liniowa][linear-regression]
* [Trenowanie modelu][train-model]
* [Klasyfikacja modelu][score-model]
* [Ocena modelu][evaluate-model]

Połącz porty, jak pokazano poniżej na rysunku 1 i ustaw kolumnę etykieta modułu [uczenie modelu][train-model] na Price ( *Cena* ).

![Ocenianie modelu regresji](./media/evaluate-model-performance/1.png)

Rysunek 1. Ocenianie modelu regresji.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po uruchomieniu eksperymentu możesz kliknąć port wyjściowy modułu [Oceń model][evaluate-model] i wybrać opcję *Wizualizuj* , aby wyświetlić wyniki oceny. Metryki oceny dostępne dla modeli regresji to: *średni błąd bezwzględny* , *główny średni błąd bezwzględny* , *względny* błąd względny, względny *błąd* i *współczynnik wyznaczania*.

Termin "błąd" oznacza różnicę między wartością przewidywaną a wartością rzeczywistą. Wartość bezwzględna lub kwadrat tej różnicy jest zwykle obliczany w celu przechwycenia łącznej wielkości błędu we wszystkich wystąpieniach, ponieważ różnica między wartością przewidywaną i rzeczywistą może być ujemna w niektórych przypadkach. Metryki błędów mierzą predykcyjną wydajność modelu regresji pod względem średniego odchylenia jego prognoz od wartości true. Niższe wartości błędów oznaczają, że model jest bardziej precyzyjny podczas tworzenia prognoz. Ogólna Metryka błędu równa zero oznacza, że model dopasowuje dane.

Współczynnik wyznaczania, który jest również znany jako R kwadratowy, jest również standardowym sposobem mierzenia, jak dobrze model dopasowuje dane. Można go interpretować jako proporcje zmienności wyjaśnione przez model. Wyższa wartość jest lepsza w tym przypadku, gdzie 1 oznacza idealne dopasowanie.

![Metryki oceny regresji liniowej](./media/evaluate-model-performance/2.png)

Rysunek 2. Metryki oceny regresji liniowej.

### <a name="using-cross-validation"></a>Używanie krzyżowego sprawdzania poprawności
Jak wspomniano wcześniej, można automatycznie przeprowadzać powtarzające się szkolenia, ocenianie i ocenianie przy użyciu modułu [Międzyvalidate model][cross-validate-model] . Wszystko, co jest potrzebne w tym przypadku, jest zestawem danych, niesprawdzonym modelem i modułem [Międzywalidacji modelu][cross-validate-model] (patrz rysunek poniżej). Należy ustawić wartość *Price* kolumny etykieta w module [modelu krzyżowego sprawdzania poprawności][cross-validate-model] .

![Sprawdzanie poprawności modelu regresji](./media/evaluate-model-performance/3.png)

Rysunek 3. Sprawdzanie poprawności modelu regresji.

Po uruchomieniu eksperymentu możesz sprawdzić wyniki oceny, klikając odpowiedni port wyjściowy modułu [modelu krzyżowego sprawdzania poprawności][cross-validate-model] . Spowoduje to udostępnienie szczegółowego widoku metryk dla każdej iteracji (złożenia) i średniego wyniku poszczególnych metryk (rysunek 4).

![Wyniki wzajemnego sprawdzania poprawności modelu regresji](./media/evaluate-model-performance/4.png)

Rysunek 4. Wyniki wzajemnego sprawdzania poprawności modelu regresji.

## <a name="evaluating-a-binary-classification-model"></a>Ocenianie binarnego modelu klasyfikacji
W scenariuszu klasyfikacji binarnej zmienna docelowa ma tylko dwa możliwe wyniki, na przykład: {0, 1} lub {false, true}, {ujemna, pozytywna}. Załóżmy, że otrzymujesz zestaw danych dla dorosłych pracowników z pewnymi zmiennymi demograficznymi i zatrudnieniem, i że zostanie wyświetlony monit o przewidywanie poziomu dochodów, zmienna binarna o wartościach {"<= 50 K", ">50 K"}. Innymi słowy Klasa negatywna reprezentuje pracowników, którzy nie mają więcej niż 50 K rocznie, a Klasa dodatnia reprezentuje wszystkich innych pracowników. Podobnie jak w przypadku scenariusza regresji będziemy uczyć się modelu, oceny danych i oceny wyników. Główną różnicą jest wybór metryk Azure Machine Learning Studio (klasycznych) obliczeń i danych wyjściowych. Aby zilustrować scenariusz przewidywania poziomu dochodów, użyjemy zestawu danych [dla dorosłych](https://archive.ics.uci.edu/ml/datasets/Adult) do utworzenia eksperymentu dla programu Studio (klasycznego) i oceny wydajności modelu regresji logistycznej z dwoma klasami, powszechnie używanego klasyfikatora binarnego.

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego w Azure Machine Learning Studio (klasyczny):

* Binarny zestaw danych klasyfikacji dochodów z spisu dla dorosłych
* [Dwuklasowa regresja logistyczna][two-class-logistic-regression]
* [Trenowanie modelu][train-model]
* [Klasyfikacja modelu][score-model]
* [Ocena modelu][evaluate-model]

Połącz porty, jak pokazano poniżej na rysunku 5 i ustaw kolumnę etykieta modułu [uczenie modelu][train-model] na *przychód*.

![Ocenianie binarnego modelu klasyfikacji](./media/evaluate-model-performance/5.png)

Rysunek 5. Ocenianie binarnego modelu klasyfikacji.

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Po uruchomieniu eksperymentu możesz kliknąć port wyjściowy modułu [oceny modelu][evaluate-model] i wybrać opcję *Wizualizuj* , aby wyświetlić wyniki oceny (Rysunek 7). Metryki oceny dostępne dla modeli klasyfikacji binarnych są następujące: *dokładność* , *precyzja* , *odwołanie* , *wynik F1* i *AUC*. Ponadto moduł wyprowadza macierz niepoprawną, pokazując liczbę prawdziwych dodatnich, fałszywych wartości ujemnych, fałszywych dodatnich i prawdziwych wartości ujemnych, a także *Roc* , *dokładności/odwoływania* i *podnoszenia* krzywych.

Dokładność jest po prostu proporcją poprawnie sklasyfikowanych wystąpień. Zwykle jest to pierwsza Metryka, która jest sprawdzana podczas oceny klasyfikatora. Jednak jeśli dane testowe są niezrównoważone (gdzie większość wystąpień należy do jednej z klas) lub użytkownik jest bardziej interesujący w wydajności jednej z klas, dokładność nie przechwytuje skuteczności klasyfikatora. W scenariuszu klasyfikacji poziomu dochodu przyjęto założenie, że testy są przeprowadzane na niektórych danych, gdzie 99% wystąpień reprezentuje osoby, które uzyskują mniej niż lub równą 50 000 na rok. Istnieje możliwość osiągnięcia dokładności 0,99, przewidywalność klasy "<= 50 000" dla wszystkich wystąpień. Klasyfikator w tym przypadku wygląda na to, że jest to dobre zadanie ogólne, ale w rzeczywistości nie jest klasyfikowane żadnej z dużych dochodów (1%) prawidłowego.

Z tego powodu warto obliczyć dodatkowe metryki, które przechwytują bardziej charakterystyczne aspekty oceny. Przed przechodzeniem do szczegółów takich metryk ważne jest, aby zrozumieć matrycę nieporozumienej oceny klasyfikacji binarnej. Etykiety klas w zestawie szkoleniowym mogą przyjmować tylko dwie możliwe wartości, które zwykle odnoszą się do nich jako wartość dodatnia lub ujemna. Dodatnie i ujemne wystąpienia klasyfikatora, które są prawidłowo przewidywalne, są nazywane odpowiednio prawdziwymi wynikami dodatnimi (TP) i prawdziwymi negatywnymi (TN). Podobnie niewłaściwie sklasyfikowane wystąpienia są nazywane fałszywie dodatnimi (FP) i fałszywych negatywnych (FN). Jest to po prostu tabela przedstawiająca liczbę wystąpień przypadających na poszczególne cztery kategorie. Azure Machine Learning Studio (klasyczny) automatycznie decyduje, które z dwóch klas w zestawie danych jest klasą dodatnią. Jeśli etykiety klas są typu Boolean lub Integer, wówczas wystąpienia oznaczone jako "true" lub "1" są przypisywane do klasy dodatniej. Jeśli etykiety są ciągami, na przykład z zestawem danych dochodów, etykiety są sortowane alfabetycznie, a pierwszy poziom jest wybierany jako Klasa ujemna, podczas gdy drugi poziom jest klasą dodatnią.

![Niemylenie macierzy binarnej](./media/evaluate-model-performance/6a.png)

Rysunek 6. Niemyląca Klasyfikacja w klasyfikacji binarnej.

Powracając do problemu klasyfikacji dochodu, chcemy zadać kilka pytań dotyczących oceny, które pomogą nam zrozumieć wydajność używanego klasyfikatora. Pytanie naturalne to: "poza osobami, których model przewiduje >50 K (TP + FP), ile jest poprawnie sklasyfikowane (TP)?" Na to pytanie można uzyskać odpowiedzi, sprawdzając **precyzję** modelu, która jest proporcją liczby dodatnich, które są poprawnie sklasyfikowane: TP/(TP + FP). Innym typowym pytaniem jest "poza wszystkimi pracownikami o dużym wpływie na dochody >50 000 (TP + FN), jak wiele z nich klasyfikuje prawidłowo (TP)". W rzeczywistości jest to **odwołanie** lub prawdziwa dodatnia stawka: TP/(TP + Fn) klasyfikatora. Można zauważyć, że istnieje oczywisty kompromis między precyzją i odwołaniem. Na przykład, mając relatywnie zrównoważony zestaw danych, klasyfikator, który przewiduje większość wystąpień pozytywnych, będzie miał wysoką wartość odwołania, ale niewielka precyzja jako wiele wystąpień ujemnych byłaby błędnie sklasyfikowana, co oznacza znaczną liczbę fałszywych dodatnich. Aby zobaczyć, jak te dwie metryki zależą od siebie, można kliknąć krzywą **precyzja/odwołania** na stronie danych wyjściowych wyniku oceny (w lewej górnej części rysunku 7).

![Wyniki oceny klasyfikacji danych binarnych](./media/evaluate-model-performance/7.png)

Rysunek 7. Wyniki oceny klasyfikacji danych binarnych.

Kolejną pokrewną metryką, która jest często używana, jest **wynik F1** , który przyjmuje precyzję i odwołanie do rozważenia. Jest to średnia harmoniczna tych dwóch metryk i jest obliczana w następujący sposób: F1 = 2 (precyzja x odwołania)/(precyzja + odwoływanie). Wynik F1 jest dobrym sposobem podsumowywania oceny w pojedynczej liczbie, ale zawsze dobrym rozwiązaniem jest zapoznanie się z dokładnością i odzyskanie, aby lepiej zrozumieć, jak działa klasyfikator.

Ponadto jeden może sprawdzać prawdziwą dodatnią stawkę w porównaniu z fałszywą dodatnią częstotliwością w krzywej **charakterystyki (ROC) odbiornika** i odpowiednim **obszarze pod wartością krzywej (AUC)** . Bliżej tej krzywej jest w lewym górnym rogu, tym lepsza wydajność klasyfikatora to (maksymalizuje prawdziwie dodatnią częstotliwość, jednocześnie minimalizując wynik fałszywie dodatni). Krzywe znajdujące się blisko przekątnej wykresu mogą wynikać z klasyfikatorów, które mają na celu wykonywanie prognoz, które są blisko losowego odgadnięcia.

### <a name="using-cross-validation"></a>Używanie krzyżowego sprawdzania poprawności
Podobnie jak w przykładzie regresji, możemy wykonywać krzyżowe sprawdzanie poprawności, aby wielokrotnie nauczyć się, oceny i oceny różnych podzestawów danych. Podobnie możemy użyć modułu [międzyweryfikuj model][cross-validate-model] , nauczenia się modelu regresji logistycznej i zestawu danych. W kolumnie etykieta musi być ustawiona wartość *dochodu* we właściwościach modułu [międzywalidacji modelu][cross-validate-model] . Po uruchomieniu eksperymentu i kliknięciu odpowiedniego portu wyjściowego modułu [Międzyvalidate model][cross-validate-model] można zobaczyć wartości metryk klasyfikacji binarnej dla każdego zgięcia, oprócz średniej i odchylenia standardowego każdego z nich. 

![Weryfikowanie krzyżowe modelu klasyfikacji binarnej](./media/evaluate-model-performance/8.png)

Rysunek 8. Weryfikowanie krzyżowe modelu klasyfikacji binarnej.

![Wyniki wzajemnej walidacji klasyfikatora binarnego](./media/evaluate-model-performance/9.png)

Rysunek 9. Wyniki wzajemnej walidacji klasyfikatora binarnego.

## <a name="evaluating-a-multiclass-classification-model"></a>Ocenianie modelu klasyfikacji wieloklasowej
W tym doświadczeniu będziemy używać popularnego zestawu danych [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Tęczówki") , który zawiera wystąpienia trzech różnych typów (klasy) zakładu Iris. Istnieją cztery wartości funkcji (słupka długość/szerokość i długość/szerokość) dla każdego wystąpienia. W poprzednich eksperymentach przeszkolone i przetestowano modele przy użyciu tych samych zestawów danych. W tym miejscu będziemy używać modułu [Split Data (podział danych][split] ) do tworzenia dwóch podzestawów danych, uczenia się pierwszego i oceny w drugim. Zestaw danych Iris jest publicznie dostępny w [repozytorium Machine Learning](https://archive.ics.uci.edu/ml/index.html)i można go pobrać za pomocą modułu [Importuj dane][import-data] .

### <a name="creating-the-experiment"></a>Tworzenie eksperymentu
Dodaj następujące moduły do obszaru roboczego w Azure Machine Learning Studio (klasyczny):

* [Importuj dane][import-data]
* [Wieloklasowy las decyzyjny][multiclass-decision-forest]
* [Dzielenie danych][split]
* [Trenowanie modelu][train-model]
* [Klasyfikacja modelu][score-model]
* [Ocena modelu][evaluate-model]

Połącz porty, jak pokazano poniżej na rysunku 10.

Ustaw indeks kolumny etykieta modułu [szkolenia model][train-model] na 5. Zestaw danych nie ma wiersza nagłówka, ale wiemy, że etykiety klas znajdują się w piątej kolumnie.

Kliknij moduł [Importuj dane][import-data] i ustaw właściwość *Źródło danych* na *adres URL sieci Web za pośrednictwem protokołu HTTP* i *adres URL* do http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data .

Ustaw ułamek wystąpień, które mają być używane na potrzeby szkolenia w module [Split Data][split] 0,7 (na przykład).

![Ocenianie klasyfikatora wieloklasowego](./media/evaluate-model-performance/10.png)

Rysunek 10. Ocenianie klasyfikatora wieloklasowego

### <a name="inspecting-the-evaluation-results"></a>Sprawdzanie wyników oceny
Uruchom eksperyment i kliknij port wyjściowy [oszacowania model][evaluate-model]. Wyniki oceny są prezentowane w postaci matrycy pomyłek, w tym przypadku. Macierz pokazuje rzeczywiste a przewidywane wystąpienia dla wszystkich trzech klas.

![Wyniki oceny klasyfikacji wieloklasowej](./media/evaluate-model-performance/11.png)

Rysunek 11. Wyniki oceny klasyfikacji wieloklasowej.

### <a name="using-cross-validation"></a>Używanie krzyżowego sprawdzania poprawności
Jak wspomniano wcześniej, można automatycznie przeprowadzać powtarzające się szkolenia, ocenianie i ocenianie przy użyciu modułu [Międzyvalidate model][cross-validate-model] . Potrzebny jest zestaw danych, nauczenie modelu i moduł [modelowego sprawdzania poprawności][cross-validate-model] (patrz rysunek poniżej). Ponownie należy ustawić kolumnę etykieta modułu [Wielovalidate model][cross-validate-model] (indeks kolumn 5 w tym przypadku). Po uruchomieniu eksperymentu i kliknięciu odpowiedniego portu wyjściowego [modelu krzyżowego sprawdzania poprawności][cross-validate-model]można sprawdzić wartości metryk dla każdego zgięcia, jak również średnią i odchylenie standardowe. Wyświetlane metryki są podobne do tych, które zostały omówione w przypadku klasyfikacji binarnej. Jednak w klasyfikacji wieloklasowej obliczenia prawdziwie dodatnich/negatywnych i fałszywych wartości dodatnich/negatywnych są wykonywane przez zliczanie dla poszczególnych klas, ponieważ nie ma żadnej ogólnej klasy pozytywnej ani ujemnej. Na przykład podczas obliczania dokładności lub odwoływania klasy "Iris-setosa" zakłada się, że jest to Klasa dodatnia i wszystkie pozostałe jako ujemne.

![Krzyżowe sprawdzanie poprawności modelu klasyfikacji wieloklasowej](./media/evaluate-model-performance/12.png)

Rysunek 12. Krzyżowe sprawdzanie poprawności modelu klasyfikacji wieloklasowej.

![Wyniki wzajemnego sprawdzania poprawności modelu klasyfikacji wieloklasowej](./media/evaluate-model-performance/13.png)

Rysunek 13. Wyniki wzajemnego sprawdzania poprawności modelu klasyfikacji wieloklasowej.

<!-- Module References -->
[cross-validate-model]: /azure/machine-learning/studio-module-reference/cross-validate-model
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[multiclass-decision-forest]: /azure/machine-learning/studio-module-reference/multiclass-decision-forest
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-logistic-regression]: /azure/machine-learning/studio-module-reference/two-class-logistic-regression