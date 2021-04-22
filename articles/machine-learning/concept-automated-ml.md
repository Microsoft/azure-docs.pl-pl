---
title: Co to jest zautomatyzowane uczenia maszynowego? AutoML
titleSuffix: Azure Machine Learning
description: Dowiedz się, Azure Machine Learning automatycznie wygenerować model przy użyciu określonych parametrów i kryteriów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.custom: automl
ms.openlocfilehash: 6f8f775e474b47cbfd5f3b4aca8987a009a7f6e1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874139"
---
# <a name="what-is-automated-machine-learning-automl"></a>Co to jest zautomatyzowane uczenie maszynowe ?

Zautomatyzowane uczenie maszynowe, nazywane również zautomatyzowanym uczeniem maszynowym lub automatycznym uczeniem maszynowym, to proces automatyzacji czasochłonnych, iteracyjnych zadań związanych z opracowywaniem modelu uczenia maszynowego. Dzięki temu analitycy danych, analitycy i deweloperzy mogą tworzyć modele uczenia maszynowego o dużej skali, wydajności i produktywności przy jednoczesnym zachowaniu jakości modelu. Zautomatyzowane uczenia maszynowego w Azure Machine Learning opiera się na przełomie z naszego [działu badań firmy Microsoft.](https://www.microsoft.com/research/project/automl/)

Opracowywanie tradycyjnego modelu uczenia maszynowego wymaga dużej ilości zasobów, co wymaga dużej wiedzy i czasu na tworzenie i porównywanie dziesiątek modeli. Dzięki zautomatyzowanemu uczeniu maszynowemu przyspieszysz czas, który zajmuje bardzo łatwe i efektywne uzyskiwanie modeli uczenia maszynowego gotowych do użycia w środowisku produkcyjnym.

## <a name="automl-in-azure-machine-learning"></a>AutoML w Azure Machine Learning

Azure Machine Learning oferuje dwa środowiska pracy z zautomatyzowanym mle:

* W przypadku klientów, którzy doświadczyli kodu, [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).  Wprowadzenie do [samouczka: używanie zautomatyzowanego uczenia maszynowego do przewidywania opłat za taksówkę.](tutorial-auto-train-models.md)

* W przypadku klientów z ograniczonymi możliwościami obsługi kodu lub bez Azure Machine Learning studio na stronie [https://ml.azure.com](https://ml.azure.com/) .  Wprowadzenie do tych samouczków:
    * [Samouczek: tworzenie modelu klasyfikacji za pomocą zautomatyzowanego uczenia maszynowego w Azure Machine Learning](tutorial-first-experiment-automated-ml.md).
    *  [Samouczek: prognozowanie zapotrzebowania przy użyciu zautomatyzowanego uczenia maszynowego](tutorial-automated-ml-forecast.md)


## <a name="when-to-use-automl-classify-regression--forecast"></a>Kiedy używać autoML: klasyfikowanie, regresja, & prognozy

Zastosuj zautomatyzowane mleowanie, Azure Machine Learning trenować i dostrajać model przy użyciu określisz metryki docelowej. Zautomatyzowane uczenie maszynowe demokratyzuje proces tworzenia modelu uczenia maszynowego i umożliwia użytkownikom, niezależnie od ich wiedzy z zakresu nauki o danych, zidentyfikowanie owego potoku uczenia maszynowego dla każdego problemu.

Analitycy danych, analitycy i deweloperzy w różnych branżach mogą używać zautomatyzowanego uczenia maszynowego do:
+ Implementowanie rozwiązań uczenia maszynowego bez obszernej wiedzy z zakresu programowania
+ Oszczędzaj czas i zasoby
+ Korzystanie z najlepszych rozwiązań w zakresie nauki o danych
+ Zapewnianie elastycznego rozwiązywania problemów

### <a name="classification"></a>Klasyfikacja

Klasyfikacja to typowe zadanie uczenia maszynowego. Klasyfikacja to rodzaj uczenia nadzorowanego, w którym modele uczą się przy użyciu danych szkoleniowych i stosują te informacje do nowych danych. Azure Machine Learning oferuje cechowanie przeznaczone specjalnie dla tych zadań, takie jak cechatory tekstu głębokiej sieci neuronowej do klasyfikacji. Dowiedz się więcej o [opcjach do wyboru cech.](how-to-configure-auto-features.md#featurization) 

Głównym celem modeli klasyfikacji jest przewidywanie, do których kategorii będą wchodzić nowe dane, na podstawie informacji z danych treningowych. Typowe przykłady klasyfikacji obejmują wykrywanie oszustw, rozpoznawanie pisma ręcznego i wykrywanie obiektów. Dowiedz się więcej i zobacz przykład z tematu [Create a classification model with automated ML (Tworzenie modelu klasyfikacji przy użyciu zautomatyzowanego uczenia maszynowego).](tutorial-first-experiment-automated-ml.md)

Zobacz przykłady klasyfikacji i zautomatyzowanego uczenia maszynowego w tych notesach języka Python: [Wykrywanie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)oszustw, [Przewidywanie marketingowe](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)i [Klasyfikacja danych grupy wiadomości](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regresja

Podobnie jak w przypadku klasyfikacji, zadania regresji są również powszechnym nadzorowym zadaniem uczenia. Azure Machine Learning oferuje [cechowanie specjalnie dla tych zadań.](how-to-configure-auto-features.md#featurization)

Różni się od klasyfikacji, w której przewidywane wartości wyjściowe są podzielone na kategorii, modele regresji przewidują liczbowe wartości wyjściowe na podstawie niezależnych predyktorów. W regresji celem jest pomoc w ujednaniu relacji między tymi niezależnymi zmiennymi predyktora przez oszacowanie wpływu jednej zmiennej na inne. Na przykład cena samochodów na podstawie takich funkcji, jak przebieg gazów, ocena bezpieczeństwa itp. Dowiedz się więcej i zobacz przykład regresji za [pomocą zautomatyzowanego uczenia maszynowego.](tutorial-auto-train-models.md)

Zobacz przykłady regresji i zautomatyzowanego uczenia maszynowego dla przewidywań w tych notesach języka Python: [Przewidywanie wydajności procesora CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych

Prognozy dotyczące tworzenia są integralną częścią każdej firmy, niezależnie od tego, czy jest to przychód, zapasy, sprzedaż czy zapotrzebowanie klientów. Zautomatyzowanego uczenia maszynowego można użyć do łączenia technik i podejść oraz uzyskania zalecanej prognozy szeregów czasu wysokiej jakości. Dowiedz się więcej z tego how-to: automated machine learning for time series forecasting (Zautomatyzowane [uczenie maszynowe do prognozowania szeregów czasu).](how-to-auto-train-forecast.md) 

Zautomatyzowany eksperyment szeregów czasu jest traktowany jako problem regresji wielowariiowej. Przeszłe wartości szeregów czasu są "przestawne", aby stały się dodatkowymi wymiarami dla regresora wraz z innymi predyktorami. To podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma zaletę naturalnego dołączania wielu zmiennych kontekstowych i ich relacji ze sobą podczas trenowania. Zautomatyzowane uczenia maszynowego uczy się jednego, ale często wewnętrznie rozgałęzienia modelu dla wszystkich elementów w zestawie danych i horyzontach przewidywania. W związku z tym dostępnych jest więcej danych do szacowania parametrów modelu, a uogólnianie do niewyjechanych serii staje się możliwe.

Zaawansowana konfiguracja prognozowania obejmuje:
* wykrywanie dni wolnych i cechowanie
* dla uczących się szeregów czasu i DNN (Auto-ARIMA, Pogoń, ForecastTCN)
* obsługa wielu modeli przez grupowanie
* rolling-origin cross validation
* konfigurowalne opóźnienia
* funkcje agregowania okien kroczących


Zobacz przykłady regresji i zautomatyzowanego uczenia maszynowego dla przewidywań w tych notesach języka Python: [Prognozowanie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)sprzedaży, [Prognozowanie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)popytu i [Prognoza produkcji napojów](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automated-ml-works"></a>Jak działa zautomatyzowane uczenia maszynowego

Podczas trenowania Azure Machine Learning wiele potoków równolegle, które wypróbują różne algorytmy i parametry. Usługa iteruje po algorytmach uczenia maszynowego w połączeniu z wyborami cech, gdzie każda iteracja tworzy model z wynikiem trenowania. Im wyższa ocena, tym lepiej model jest uznawany za "dopasowany" do danych.  Zostanie ona zatrzymana, gdy znajdzie się w kryteriach zakończenia zdefiniowanych w eksperymencie. 

Za **Azure Machine Learning** można zaprojektować i uruchomić eksperymenty zautomatyzowanego trenowania uczenia maszynowego, korzystając z poniższych kroków:

1. **Identyfikowanie problemu uczenia maszynowego** do rozwiązania: klasyfikacja, prognozowanie lub regresja

1. **Wybierz, czy chcesz używać zestawu SDK** języka Python, czy środowiska internetowego studio: dowiedz się, jaka jest parzystość między zestawem SDK języka Python a [środowiskiem internetowym studio.](#parity)

   * Aby uzyskać ograniczone środowisko kodu lub go nie ma, wypróbuj środowisko Azure Machine Learning studio na stronie [https://ml.azure.com](https://ml.azure.com/)  
   * Dla deweloperów języka Python zapoznaj się z [zestawem SDK Azure Machine Learning Python](how-to-configure-auto-train.md) 
    
1. Określ źródło i format danych treningowych z **etykietą:** tablice Numpy lub ramki danych pandas

1. **Skonfiguruj docelowy obiekt obliczeniowy do** trenowania modelu, taki jak komputer lokalny, Azure Machine Learning [obliczeniowe,](how-to-set-up-training-targets.md)zdalne maszyny wirtualne lub Azure Databricks .

1. Skonfiguruj parametry **zautomatyzowanego uczenia** maszynowego, które określają, ile iteracji w różnych modelach, ustawienia hiperparametrów, zaawansowane przetwarzanie wstępne/cechowanie oraz jakie metryki należy sprawdzić podczas określania najlepszego modelu.  
1. **Prześlij przebieg trenowania.**

1. **Przeglądanie wyników** 

Ten proces został zilustrowany na poniższym diagramie. 
![Zautomatyzowane uczenie maszynowe](./media/concept-automated-ml/automl-concept-diagram2.png)


Możesz również sprawdzić informacje o zarejestrowanym uruchomieniu, które [zawierają metryki zebrane](how-to-understand-automated-ml.md) podczas uruchomienia. Przebieg trenowania tworzy serializowany obiekt (plik) języka Python, który zawiera przetwarzanie wstępne modelu i `.pkl` danych.

Gdy tworzenie modelu jest zautomatyzowane, możesz również dowiedzieć się, jak ważne lub [istotne funkcje](how-to-configure-auto-train.md#explain) są dla wygenerowanych modeli.



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Inżynieria cech

Inżynieria cech to proces używania wiedzy o danych w domenie do tworzenia funkcji, które pomagają lepiej uczyć się algorytmów uczenia maszynowego. W Azure Machine Learning stosowane są techniki skalowania i normalizacji w celu ułatwienia inżynierii cech. Zbiorczo te techniki i inżynieria cech są określane jako cechowanie.

W przypadku eksperymentów zautomatyzowanego uczenia maszynowego cechowanie jest stosowane automatycznie, ale można je również dostosowywać na podstawie danych. [Dowiedz się więcej o tym, co obejmuje cechowanie.](how-to-configure-auto-features.md#featurization)  

> [!NOTE]
> Kroki cechowania zautomatyzowanego uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) stają się częścią bazowego modelu. W przypadku używania modelu do przewidywania te same kroki cechowania zastosowane podczas trenowania są automatycznie stosowane do danych wejściowych.

### <a name="automatic-featurization-standard"></a>Automatyczna cechowanie (standardowa)

W każdym eksperymencie zautomatyzowanego uczenia maszynowego dane są automatycznie skalowane lub znormalizowane, aby pomóc algorytmom działać dobrze. Podczas trenowania modelu do każdego modelu zostanie zastosowana jedna z następujących technik skalowania lub normalizacji. Dowiedz się, w jaki sposób autoML [pomaga zapobiegać zbyt dopasowaniu i niezrównoważyniu danych](concept-manage-ml-pitfalls.md) w modelach.

|Skalowanie &nbsp; & &nbsp; przetwarzania| Opis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standaryzacja funkcji przez usunięcie średniej i skalowania w celu wariancji jednostkowej  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Przekształca funkcje przez skalowanie każdej funkcji według minimalnej i maksymalnej wartości tej kolumny  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skalowanie każdej funkcji według maksymalnej wartości bezwzględnej |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Funkcje tego skalowania według zakresu kwantylu |
| [Pca](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redukcja wymiarowości liniowej przy użyciu wartości pojedynczej Dekompozycja danych w celu rzutowania ich na przestrzeń o mniejszej wymiarowości |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ten transformator zmniejsza liniową wymiarowość za pomocą obciętej dekompozycji wartości pojedynczej (SVD). W przeciwieństwie do pca, ten narzędzie do szacowania nie wyśrodkuje danych przed obliczaniem dekompozycji wartości pojedynczej, co oznacza, że może wydajnie pracować z macierzami scipy.sparse |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Każda próbka (czyli każdy wiersz macierzy danych) z co najmniej jednym składnikiem innym niż zero jest przeskalowana niezależnie od innych próbek, tak aby jej norma (l1 lub l2) równa się jednej |

### <a name="customize-featurization"></a>Dostosowywanie cech

Dostępne są również dodatkowe techniki inżynierii cech, takie jak kodowanie i przekształcenia. 

Włącz to ustawienie za pomocą:

+ Azure Machine Learning studio: Włącz **automatyczną cechowanie** w **sekcji Wyświetl dodatkową konfigurację** [za pomocą tych kroków.](how-to-use-automated-ml-for-ml-models.md#customize-featurization)

+ Zestaw SDK języka Python: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` określ wartość w [obiekcie AutoMLConfig.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) Dowiedz się więcej na [temat włączania cechowania](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Modele zespołów

Zautomatyzowane uczenie maszynowe obsługuje modele grupy, które są domyślnie włączone. Uczenie zespołowe poprawia wyniki uczenia maszynowego i wydajność predykcyjną, łącząc wiele modeli, w przeciwieństwie do korzystania z pojedynczych modeli. Iteracje zespołu są wyświetlane jako końcowe iteracje uruchomienia. Zautomatyzowane uczenie maszynowe używa metod zespołów głosujących i skumulowanych do łączenia modeli:

* **Głosowanie**: przewiduje na podstawie średniej ważonej przewidywanych prawdopodobieństw klasy (dla zadań klasyfikacji) lub przewidywanych celów regresji (dla zadań regresji).
* **Układanie na** stosie: stos łączy heterogeniczne modele i trenuje meta model na podstawie danych wyjściowych z poszczególnych modeli. Bieżące domyślne meta-modele to LogisticRegression dla zadań klasyfikacji i ElasticNet dla zadań regresji/prognozowania.

Algorytm [wyboru zespołów Caruana z](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) inicjacją sortowanych zespołów służy do decydowania, które modele mają być używane w ramach zespołu. Na wysokim poziomie ten algorytm inicjuje zespół z maksymalnie pięcioma modelami z najlepszymi indywidualnymi wynikami i sprawdza, czy te modele znajdują się w granicach 5% od najlepszego wyniku, aby uniknąć słabego początkowego zespołu. Następnie dla każdej iteracji zespołu do istniejącej zespołów dodawany jest nowy model, a wynikowy wynik jest obliczany. Jeśli nowy model poprawi istniejącą ocenę zespołu, zespół zostanie zaktualizowany, aby uwzględnić nowy model.

Zapoznaj się z [daniem zmiany](how-to-configure-auto-train.md#ensemble) domyślnych ustawień grupy w zautomatyzowanym uczeniu maszynowym.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Wskazówki dotyczące lokalnych i zdalnych zarządzanych docelowych obiektów obliczeniowych uczenia maszynowego

Interfejs internetowy zautomatyzowanego uczenia maszynowego zawsze używa zdalnego [docelowego obiektu obliczeniowego.](concept-compute-target.md)  Jednak w przypadku korzystania z zestawu SDK języka Python wybierzesz lokalne zasoby obliczeniowe lub zdalny docelowy obiekt obliczeniowy do zautomatyzowanego trenowania uczenia maszynowego.

* **Lokalne obliczenia:** szkolenie odbywa się na lokalnym laptopie lub na komputerze obliczeniowym maszyny wirtualnej. 
* **Zdalne obliczenia:** trenowanie odbywa się Machine Learning klastrach obliczeniowych.  

### <a name="choose-compute-target"></a>Wybieranie docelowego obiektu obliczeniowego
Podczas wybierania docelowego obiektu obliczeniowego należy wziąć pod uwagę następujące czynniki:

 * **Wybierz** lokalne zasoby obliczeniowe: jeśli twój scenariusz ma na celu wstępne eksploracje lub pokazy przy użyciu małych danych i krótkich trenów (tj. sekund lub kilku minut na przebieg podrzędny), szkolenie na komputerze lokalnym może być lepszym wyborem.  Nie ma czasu instalacji, zasoby infrastruktury (komputer lub maszyna wirtualna) są bezpośrednio dostępne.
 * Wybierz zdalny klaster obliczeniowy uczenia maszynowego: w przypadku trenowania z większymi zestawami danych, na przykład w ramach trenowania produkcyjnego, tworzenia modeli, które wymagają dłuższych trenowania, zdalne obliczenia zapewniają znacznie lepszą wydajność czasu end-to-end, ponieważ zrównolegli szkolenie między węzłami `AutoML` klastra. W przypadku zdalnych obliczeń czas uruchamiania infrastruktury wewnętrznej wynosi około 1,5 min na przebieg podrzędny oraz dodatkowe minuty dla infrastruktury klastra, jeśli maszyny wirtualne nie są jeszcze uruchomione.

### <a name="pros-and-cons"></a>Zalety i wady
Weź pod uwagę te zalety i wady podczas wybierania opcji użycia lokalnego i zdalnego.

|  | Zaletami (zaletami)  |Cons (Dosyć)  |
|---------|---------|---------|---------|
|**Lokalny docelowy obiekt obliczeniowy** |  <li> Brak czasu uruchamiania środowiska   | <li>  Podzbiór funkcji<li>  Nie można zrównoleglić przebiegów <li> Co gorsza w przypadku dużych ilości danych. <li>Brak przesyłania strumieniowego danych podczas trenowania <li>  Brak cechowania opartego na sieci DNN <li> Tylko zestaw SDK języka Python |
|**Klastry obliczeniowe zdalnego uczenia maszynowego**|  <li> Pełny zestaw funkcji <li> Zrównoleglić przebiegi podrzędne <li>   Obsługa dużych ilości danych<li>  Cechowanie na podstawie sieci DNN <li>  Dynamiczna skalowalność klastra obliczeniowego na żądanie <li> Środowisko bez kodu (internetowy interfejs użytkownika) jest również dostępne  |  <li> Czas uruchamiania węzłów klastra <li> Czas uruchamiania dla każdego uruchomienia podrzędnego    |

### <a name="feature-availability"></a>Dostępność funkcji 

 Więcej funkcji jest dostępnych w przypadku korzystania ze zdalnego obliczenia, jak pokazano w poniższej tabeli. 

| Cecha                                                    | Zdalne | Lokalne | 
|------------------------------------------------------------|--------|-------|
| Przesyłanie strumieniowe danych (obsługa dużych ilości danych, do 100 GB)          | ✓      |       | 
| Cechowanie i trenowanie tekstu na podstawie DNN-BERT             | ✓      |       |
| Obsługa procesora GPU w trybie out-of-the-box (trenowanie i wnioskowanie)        | ✓      |       |
| Obsługa klasyfikacji i etykietowania obrazów                  | ✓      |       |
| Modele Auto-ARIMA, ForecastTCN do prognozowania | ✓      |       | 
| Wiele przebiegów/iteracji równolegle                       | ✓      |       |
| Tworzenie modeli z możliwością interpretacji w interfejsie użytkownika środowiska internetowego programu AutoML Studio      | ✓      |       |
| Dostosowywanie inżynierii cech w interfejsie użytkownika środowiska internetowego programu Studio| ✓      |       |
| Dostrajanie hiperparametryczne usługi Azure ML                             | ✓      |       |
| Obsługa przepływu pracy potoku usługi Azure ML                         | ✓      |       |
| Kontynuowanie uruchomienia                                             | ✓      |       |
| Prognozowanie                                                | ✓      | ✓     |
| Tworzenie i uruchamianie eksperymentów w notesach                    | ✓      | ✓     |
| Rejestrowanie i wizualizowanie informacji i metryk eksperymentu w interfejsie użytkownika | ✓      | ✓     |
| Bariery danych                                            | ✓      | ✓     |

## <a name="many-models"></a>Wiele modeli 

[Akcelerator](https://aka.ms/many-models) rozwiązań do obsługi wielu modeli (wersja zapoznawcza) jest Azure Machine Learning i umożliwia używanie zautomatyzowanego uczenia maszynowego do trenowania i obsługi setek, a nawet tysięcy modeli uczenia maszynowego oraz zarządzania nimi.

Na przykład tworzenie modelu dla __każdego wystąpienia__ lub jednostki w następujących scenariuszach może prowadzić do lepszych wyników:

* Przewidywanie sprzedaży dla każdego sklepu
* Konserwacja predykcyjna setek smyków olejowych
* Dostosowywanie obsługi dla poszczególnych użytkowników.

<a name="parity"></a>

### <a name="experiment-settings"></a>Ustawienia eksperymentu 

Poniższe ustawienia umożliwiają skonfigurowanie zautomatyzowanego eksperymentu uczenia maszynowego. 

| |Zestaw SDK języka Python|Środowisko internetowe studio|
----|:----:|:----:
|**Dzielenie danych na zestawy trenowania/walidacji**| ✓|✓
|**Obsługuje zadania uczenia maszynowego: klasyfikację, regresję i prognozowanie**| ✓| ✓
|**Optymalizacja na podstawie metryki podstawowej**| ✓| ✓
|**Obsługuje obliczenia usługi Azure ML jako docelowy obiekt obliczeniowy** | ✓|✓
|**Konfigurowanie horyzontu prognozy, opóźnień docelowych & okna kroczącego**|✓|✓
|**Ustawianie kryteriów zakończenia** |✓|✓ 
|**Ustawianie iteracji współbieżnych**| ✓|✓
|**Usuwanie kolumn**| ✓|✓
|**Algorytmy blokowe**|✓|✓
|**Krzyżowe sprawdzanie poprawności** |✓|✓
|**Obsługuje trenowanie Azure Databricks klastrach**| ✓|
|**Wyświetlanie nazw funkcji zaprojektowanych przez inżynierów**|✓|
|**Podsumowanie cech**| ✓|
|**Cechowanie na dni wolne od pracy**|✓|
|**Poziomy szczegółowości pliku dziennika**| ✓|

### <a name="model-settings"></a>Ustawienia modelu

Te ustawienia można zastosować do najlepszego modelu w wyniku eksperymentu zautomatyzowanego uczenia maszynowego.

| |Zestaw SDK języka Python|Środowisko internetowe studio|
|----|:----:|:----:|
|**Najlepsza rejestracja, wdrożenie, objaśnienia modelu**| ✓|✓|
|**Włączanie zespołów głosujących & modeli zespołów stosów**| ✓|✓|
|**Pokazywanie najlepszego modelu na podstawie metryki nie podstawowej**|✓||
|**Włączanie/wyłączanie zgodności modelu ONNX**|✓||
|**Testowanie modelu** | ✓| |

### <a name="run-control-settings"></a>Uruchamianie ustawień kontrolki

Te ustawienia umożliwiają przeglądanie i kontrolowanie przebiegów eksperymentu i jego przebiegów podrzędnych. 

| |Zestaw SDK języka Python|Środowisko internetowe studio|
|----|:----:|:----:|
|**Uruchamianie tabeli podsumowania**| ✓|✓|
|**Anulowanie przebiegów & podrzędne**| ✓|✓|
|**Uzyskiwanie barier**| ✓|✓|
|**Wstrzymywanie & wznawiania przebiegów**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Dzięki Azure Machine Learning automatycznego uczenia maszynowego można utworzyć model języka Python i przekonwertować go na format ONNX. Modele w formacie ONNX można uruchamiać na różnych platformach i urządzeniach. Dowiedz się więcej o [przyspieszaniu modeli uczenia maszynowego za pomocą języka ONNX.](concept-onnx.md)

Zobacz, jak przekonwertować na format ONNX [w tym przykładzie notesu Jupyter.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) Dowiedz się, [które algorytmy są obsługiwane w programie ONNX.](how-to-configure-auto-train.md#select-your-experiment-type)

Środowisko uruchomieniowe ONNX obsługuje również język C#, dzięki czemu można używać modelu wbudowanego automatycznie w aplikacje języka C# bez konieczności ponownego używania ani jakichkolwiek opóźnień sieci, które wprowadzają punkty końcowe REST. Dowiedz się więcej na temat używania modelu [ONNX](./how-to-use-automl-onnx-model-dotnet.md) automatycznego uczenia automatycznego w aplikacji .NET z ML.NET i wnioskowania modeli ONNX przy użyciu interfejsu API języka C# środowiska uruchomieniowego [ONNX.](https://github.com/plaidml/onnxruntime/blob/plaidml/docs/CSharp_API.md) 

## <a name="next-steps"></a>Następne kroki

Istnieje wiele zasobów, dzięki których możesz rozpocząć działanie przy użyciu automatycznegoml. 

### <a name="tutorials-how-tos"></a>Samouczki/ poradniki
Samouczki to end-to-end, wprowadzające przykłady scenariuszy autoML.
+ **Aby uzyskać pierwsze doświadczenie z kodem,** postępuj zgodnie z [samouczkiem Samouczek:](tutorial-auto-train-models.md)automatyczne trenowanie modelu regresji przy użyciu zestawu SDK Azure Machine Learning Python.

 + **Aby uzyskać informacje o niskim poziomie kodu lub** bez kodu, zobacz [Samouczek:](tutorial-first-experiment-automated-ml.md)tworzenie modeli klasyfikacji zautomatyzowanego uczenia maszynowego za pomocą Azure Machine Learning studio .

Artykuły z dodatkowymi szczegółami na temat funkcji, które oferuje autoML. Na przykład 

+ Konfigurowanie ustawień dla eksperymentów automatycznego trenowania
    + W Azure Machine Learning studio [użyj tych kroków.](how-to-use-automated-ml-for-ml-models.md) 
    + Za pomocą zestawu SDK języka Python [użyj tych kroków.](how-to-configure-auto-train.md)

+  Dowiedz się, jak automatycznie trenować przy użyciu danych szeregów czasu, [korzystając z tych kroków.](how-to-auto-train-forecast.md)

### <a name="jupyter-notebook-samples"></a>Przykłady notesów Jupyter 

Przejrzyj szczegółowe przykłady kodu i przypadki użycia w [repozytorium notesów GitHub, aby uzyskać przykłady zautomatyzowanego uczenia maszynowego.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

### <a name="python-sdk-reference"></a>Informacje o zestawie SDK języka Python

Pogłębiaj swoją wiedzę na temat wzorców projektowych i specyfikacji klas zestawu SDK za pomocą [dokumentacji referencyjnej klasy AutoML.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 

> [!Note]
> Funkcje zautomatyzowanego uczenia maszynowego są również dostępne w innych rozwiązaniach firmy Microsoft, takich jak [ML.NET,](/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](/power-bi/service-machine-learning-automated) i [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
