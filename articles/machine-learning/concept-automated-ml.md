---
title: Co to jest zautomatyzowana ML? AutoML
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób Azure Machine Learning automatycznie wybierać algorytm i generować z niego model, aby zaoszczędzić czas, korzystając z parametrów i kryteriów, które podajesz, aby wybrać najlepszy algorytm dla modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 10/27/2020
ms.openlocfilehash: 8ffdd8c15cf225e4f5b99a0b84b71bdbed456234
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130089"
---
# <a name="what-is-automated-machine-learning-automl"></a>Co to jest automatyczne Uczenie maszynowe (AutoML)?

Zautomatyzowana Uczenie maszynowe, nazywana również automatycznym ML lub AutoML, jest procesem automatyzacji czasochłonnych, iteracyjnych zadań tworzenia modelu uczenia maszynowego. Umożliwia ona analitykom danych, specjalistom i deweloperom tworzenie modeli ML o wysokiej skalowalności, wydajności i produktywności, a jednocześnie zapewnia wysoką jakość modelu. Automatyczna ML jest oparta na przełomie od naszego [działu badawczego firmy Microsoft](https://www.microsoft.com/research/project/automl/).

Tradycyjny rozwój modelu uczenia maszynowego jest czasochłonny i wymaga znacznej wiedzy o domenie oraz czasu na wyprodukowanie i porównanie dziesiątek modeli. Dzięki automatycznemu uczeniu maszynowego skracasz czas potrzebny do uzyskania gotowych do produkcji modeli ML z doskonałą prostotą i wydajnością.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Kiedy używać AutoML: klasyfikowanie, regresja, & prognozowanie

Zastosuj automatyczne ML, gdy chcesz, aby Azure Machine Learning szkolić i dostrajania model przy użyciu określonej metryki docelowej. Zautomatyzowana tablica demokratyzuje proces tworzenia modelu uczenia maszynowego i umożliwia jego użytkownikom, bez względu na swoją wiedzę o nauce danych, identyfikację kompleksowego potoku uczenia maszynowego dla każdego problemu.

Analitycy danych, analitykowie i deweloperzy w różnych branżach mogą używać zautomatyzowanych ML do:
+ Wdrażanie rozwiązań ML bez obszernej wiedzy programistycznej
+ Oszczędność czasu i zasobów
+ Korzystanie z najlepszych rozwiązań dotyczących analizy danych
+ Zapewnianie problemu Agile — Rozwiązywanie problemów

### <a name="classification"></a>Klasyfikacja

Klasyfikacja to typowe zadanie uczenia maszynowego. Klasyfikacja to typ nadzorowanych szkoleń, w których modele uczyją się korzystać z danych szkoleniowych i stosują te informacje do nowych danych. Azure Machine Learning oferuje featurizations dla tych zadań, takich jak głębokie neuronowych Network Text featurizers for klasyfikacji. Dowiedz się więcej o [opcjach cechowania](how-to-configure-auto-features.md#featurization). 

Głównym celem modeli klasyfikacji jest przewidywanie, do których kategorii nowe dane będą się opierać na podstawie informacji szkoleniowych. Typowe przykłady klasyfikacji obejmują wykrywanie oszustw, rozpoznawanie pisma ręcznego i wykrywanie obiektów. Dowiedz się więcej i zapoznaj się z przykładem na temat [tworzenia modelu klasyfikacji przy użyciu zautomatyzowanej](tutorial-first-experiment-automated-ml.md)tablicy.

Zobacz przykłady klasyfikacji i zautomatyzowane Uczenie maszynowe w tych notesach języka Python: [wykrywanie oszustw](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [prognozowanie marketingowe](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)i [Klasyfikacja danych grupy dyskusyjnej](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regresja

Podobnie jak w przypadku klasyfikacji, zadania regresji są również typowym nadzorowanym zadaniem szkoleniowym. Azure Machine Learning oferuje [featurizations dla tych zadań](how-to-configure-auto-features.md#featurization).

Różni się od klasyfikacji, w której przewidywane wartości wyjściowe to kategorii, modele regresji przewidywania liczbowych wartości wyjściowych na podstawie niezależnych predykcyjnych. W regresji celem jest pomoc w ustaleniu relacji między niezależnymi zmiennymi predykcyjnymi przez oszacowanie wpływu jednej zmiennej na inne. Na przykład cena na urządzeniach przenośnych oparta na funkcjach takich jak, kilometry gazu, Ocena bezpieczeństwa itp. Dowiedz się więcej i zobacz przykład [regresji przy użyciu automatycznej uczenia maszynowego](tutorial-auto-train-models.md).

Zobacz przykłady regresji i zautomatyzowane Uczenie maszynowe na potrzeby prognoz w tych notesach języka Python: [Prognoza wydajności procesora CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Prognozowanie szeregów czasowych

Tworzenie prognoz jest integralną częścią dowolnej firmy, bez względu na to, czy chodzi o dochody, spisy, sprzedaż czy zapotrzebowanie na klienta. Możesz użyć zautomatyzowanej ML do łączenia technik i podejścia i uzyskania zalecanej wysokiej jakości prognozy szeregów czasowych. Dowiedz się więcej, korzystając z tej procedury: [Automatyczne Uczenie maszynowe na potrzeby prognozowania szeregów czasowych](how-to-auto-train-forecast.md). 

Zautomatyzowany eksperyment szeregów czasowych jest traktowany jako problem z regresją wieloczynnikowa. Poprzednie wartości serii czasowych są "przestawne", aby stać się dodatkowymi wymiarami regresor wraz z innymi predykcyjnymi. Takie podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma zaletę naturalnie dołączania wielu zmiennych kontekstowych i ich relacji do siebie podczas uczenia się. Zautomatyzowana ML zdobywa pojedynczy, ale często wewnętrznie rozgałęzienie modelu dla wszystkich elementów w zestawie danych i prognozowanie Horizons. W tym celu można uzyskać więcej danych w celu oszacowania parametrów modelu i generalizacji do niewidocznej serii.

Zaawansowana konfiguracja prognozowania obejmuje:
* Wykrywanie świąt i cechowania
* DNN (autoARIMA, Prophet, ForecastTCN)
* wiele modeli obsługuje grupowanie
* krzyżowe sprawdzanie poprawności źródła
* Konfigurowalne spowolnienia
* funkcje agregujące okna stopniowego


Zobacz przykłady regresji i zautomatyzowane Uczenie maszynowe na potrzeby prognoz w tych notesach w języku Python: [prognozowanie sprzedaży](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [prognozowanie popytu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)i [prognoza produkcji napojów](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Jak działa AutoML

Podczas uczenia Azure Machine Learning tworzy wiele potoków równolegle, które próbują wykonać różne algorytmy i parametry. Usługa wykonuje iterację przez algorytmy ML sparowane z opcjami wyboru funkcji, gdzie każda iteracja tworzy model z wynikiem uczenia. Im wyższy wynik, tym lepszy model jest traktowany jako "dopasowane" do danych.  Zostanie ona zatrzymana po trafieniu kryteriów zakończenia zdefiniowanych w eksperymentie. 

Korzystając z **Azure Machine Learning** , można projektować i uruchamiać zautomatyzowane eksperymenty szkoleniowe ml z następującymi krokami:

1. **Określ problem związany z ml** , który ma zostać rozwiązany: Klasyfikacja, prognozowanie lub regresja

1. **Wybierz, czy chcesz używać zestawu SDK języka Python, czy środowiska sieci Web programu Studio** : Poznaj informacje o parzystości między [środowiskiem websdk i Studio w sieci Web](#parity).

   * W przypadku programu z ograniczeniami lub bez kodu Wypróbuj środowisko internetowe Azure Machine Learning Studio pod adresem [https://ml.azure.com](https://ml.azure.com/)  
   * W przypadku deweloperów języka Python zapoznaj się z [zestawem SDK języka python Azure Machine Learning](how-to-configure-auto-train.md) . 
    
1. **Określ źródło i format etykiet danych szkoleniowych** : Numpy Arrays lub Pandas Dataframe

1. **Skonfiguruj cel obliczeń dla szkolenia modelu** , takiego jak [komputer lokalny, Azure Machine Learning obliczeń, zdalnych maszyn wirtualnych lub Azure Databricks](how-to-set-up-training-targets.md).  Dowiedz się więcej o zautomatyzowanym szkoleniu [dla zasobu zdalnego](how-to-auto-train-remote.md).

1. **Skonfiguruj zautomatyzowane parametry uczenia maszynowego** , które określają, ile iteracji przekroczy różne modele, ustawienia parametrów, zaawansowane przetwarzanie wstępne/cechowania i jakie metryki mają być sprawdzane podczas określania najlepszego modelu.  
1. **Prześlij przebieg szkoleniowy.**

1. **Przejrzyj wyniki** 

Na poniższym diagramie przedstawiono ten proces. 
![Automatyczne Uczenie maszynowe](./media/concept-automated-ml/automl-concept-diagram2.png)


Możesz również sprawdzić zarejestrowane informacje o uruchomieniu, które [zawierają metryki](how-to-understand-automated-ml.md) zebrane podczas uruchamiania. Uruchomienie szkoleniowe powoduje utworzenie serializowanego obiektu języka Python ( `.pkl` plik) zawierającego model i przetwarzanie wstępne przetwarzania danych.

Chociaż Kompilowanie modelu jest zautomatyzowane, można również [dowiedzieć się, jak ważne lub istotne funkcje są](how-to-configure-auto-train.md#explain) wygenerowane modele.

Dowiedz się, jak używać [zdalnego obiektu docelowego obliczeń](how-to-auto-train-remote.md).



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Inżynieria cech

Inżynieria funkcji to proces polegający na użyciu znajomości domeny danych w celu utworzenia funkcji, które pomagają algorytmom ML uczyć się. W Azure Machine Learning są stosowane techniki skalowania i normalizacji w celu ułatwienia inżynierii funkcji. Wspólnie te techniki i inżynieria funkcji są określane jako cechowania.

W przypadku zautomatyzowanych eksperymentów usługi Machine Learning cechowania jest stosowana automatycznie, ale można ją również dostosować na podstawie danych. [Dowiedz się więcej na temat tego, co obejmuje cechowania](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> Zautomatyzowane kroki cechowania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Korzystając z modelu dla prognoz, te same kroki cechowania stosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

### <a name="automatic-featurization-standard"></a>Automatyczna cechowania (standardowa)

W każdym automatycznym doświadczeniu uczenia maszynowego Twoje dane są automatycznie skalowane lub znormalizowane w celu zapewnienia prawidłowego wykonywania algorytmów. Podczas uczenia modelu jedna z następujących technik skalowania lub normalizacji zostanie zastosowana do każdego modelu. Dowiedz się, w jaki sposób usługa AutoML pomaga [zapobiegać nadmiernemu dopasowywaniu i niezrównoważonym danych](concept-manage-ml-pitfalls.md) w modelach.

|&nbsp; & &nbsp; Normalizacja skalowania| Opis |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Ujednolicenie funkcji przez usunięcie średniej i skalowania do wariancji jednostek  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Przekształca funkcje, przeskalowane każdą funkcję według minimalnej i maksymalnej wartości tej kolumny  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skaluj każdą funkcję przez maksymalną wartość bezwzględną |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Ta funkcja skalowania według ich zakresu quantile |
| [Z](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Zmniejszenie liczby liniowej przy użyciu jednoznacznej dekompozycji danych w celu ich zaprojektowania do mniejszej przestrzeni wymiarowej |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Ta metoda przekształcania dokonuje redukcji liniowej, dzięki obcięciu pojedynczej dekompozycji wartości (SVD). W przeciwieństwie do UPW, ten szacowania nie Wyśrodkowuje danych przed wdrożeniem wielowartościowej dekompozycji, co oznacza, że może wydajnie współpracować z scipy. rozrzedzonych macierzy |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Każdy przykład (to oznacza, że każdy wiersz macierzy danych) z co najmniej jednym składnikiem niezerowym jest ponownie skalowany niezależnie od innych próbek, tak aby jego norma (L1 lub L2) była równa 1 |

### <a name="customize-featurization"></a>Dostosuj cechowania

Dostępne są również dodatkowe techniki inżynieryjne funkcji, takie jak kodowanie i transformacje. 

Włącz to ustawienie przy użyciu:

+ Azure Machine Learning Studio: Włącz **Automatyczne cechowania** w sekcji **Wyświetl dodatkową konfigurację** , [wykonując następujące kroki](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Zestaw SDK języka Python: Określ `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` w obiekcie [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) . Dowiedz się więcej na temat [włączania cechowania](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Modele kompletów

Automatyczne Uczenie maszynowe obsługuje modele kompletów, które są domyślnie włączone. Program dblearning podnosi wyniki uczenia maszynowego i wydajność predykcyjną przez połączenie wielu modeli w przeciwieństwie do korzystania z pojedynczych modeli. Iteracje kompletka są wyświetlane jako ostateczne iteracje przebiegu. Automatyczne Uczenie maszynowe używa metody "głosowania" i "stosu" do łączenia modeli:

* **Głosowanie** : przewidywania w oparciu o średnią ważoną przewidywanych prawdopodobieństw zajęć (dla zadań klasyfikacji) lub przewidywane cele regresji (dla zadań regresji).
* **Stosowanie** : stosy łączy heterogenicznych modele i pociąga za siebie model na podstawie danych wyjściowych z poszczególnych modeli. Bieżące domyślne meta models to LogisticRegression dla zadań klasyfikacji i ElasticNet dla zadań regresji/prognozowania.

[Algorytm wyboru Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) DBZ posortowaną inicjalizacją kompletną służy do decydowania, które modele mają być używane w ramach tego modułu. Na wysokim poziomie ten algorytm inicjuje zespół z maksymalnie pięcioma modelami z najlepszymi wynikami i sprawdza, czy te modele mieszczą się w przedziale od 5% do największej wartości, aby uniknąć słabego początkowego elementu. Następnie dla każdej iteracji, nowy model jest dodawany do istniejącej, a wynik jest obliczany. Jeśli nowy model poprawi istniejący wynik, jego kompletność zostanie zaktualizowana w celu uwzględnienia nowego modelu.

Zapoznaj się z [tematem jak](how-to-configure-auto-train.md#ensemble) zmienić domyślne ustawienia zestawu w usłudze automat Machine Learning.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Wskazówki dotyczące lokalnego i zdalnego zarządzania ML obiektów docelowych obliczeń

Interfejs sieci Web dla zautomatyzowanej ML zawsze używa zdalnego [miejsca docelowego obliczeń](concept-compute-target.md).  Ale w przypadku korzystania z zestawu SDK języka Python należy wybrać lokalne obliczenia lub zdalne miejsce docelowe obliczeń na potrzeby automatycznego szkolenia ML.

* **Lokalne Obliczanie** : szkolenie odbywa się na lokalnym komputerze przenośnym lub w ramach obliczeń dotyczących maszyn wirtualnych. 
* **Zdalne Obliczanie** : szkolenie odbywa się w przypadku Machine Learning klastrów obliczeniowych.  

### <a name="choose-compute-target"></a>Wybierz element docelowy obliczeń
Należy wziąć pod uwagę te czynniki podczas wybierania obiektu docelowego obliczeń:

 * **Wybierz lokalne obliczenie** : Jeśli Twój scenariusz dotyczy wstępnych eksploracji lub pokazów przy użyciu małych danych i krótkich pociągów (tj. sekund lub kilku minut na uruchomienie podrzędne), szkolenie na komputerze lokalnym może być lepszym wyborem.  Nie ma czasu instalacji, zasoby infrastruktury (komputer lub maszyna wirtualna) są dostępne bezpośrednio.
 * **Wybierz klaster obliczeniowy zdalnego ml** : w przypadku szkoleń z większymi zestawami danych, takimi jak w szkoleniu produkcyjnym tworzenie modeli, które wymagają dłuższych pociągów, usługa zdalne obliczenia zapewnia znacznie lepszą wydajność, ponieważ `AutoML` zrównoleglanie pociąga w węzłach klastra. W przypadku zdalnego obliczenia czas rozpoczęcia dla infrastruktury wewnętrznej zostanie dodany około 1,5 minut na uruchomienie podrzędne, a także dodatkowe minuty dla infrastruktury klastra, jeśli maszyny wirtualne nie są jeszcze uruchomione.

### <a name="pros-and-cons"></a>Specjaliści i wady
Należy wziąć pod uwagę te zalety i wady, gdy wybierzesz opcję używania lokalnego i zdalnego.

|  | Specjaliści (zalety)  |Wady (wady)  |
|---------|---------|---------|---------|
|**Lokalne miejsce docelowe obliczeń** |  <li> Brak czasu uruchamiania środowiska   | <li>  Podzbiór funkcji<li>  Nie można zrównoleglanie przebiegów <li> Gorsz w przypadku dużych ilości danych. <li>Brak przesyłania strumieniowego danych podczas uczenia <li>  Brak cechowania opartych na DNN <li> Tylko zestaw SDK języka Python |
|**Klastry obliczeniowe zdalnej ML**|  <li> Pełny zestaw funkcji <li> Uruchomienia podrzędne zrównoleglanie <li>   Obsługa dużych ilości danych<li>  Cechowania DNN <li>  Dynamiczna skalowalność klastra obliczeniowego na żądanie <li> Dostępne są również środowiska bez kodu (interfejs użytkownika sieci Web)  |  <li> Czas uruchamiania węzłów klastra <li> Czas uruchamiania każdego uruchomienia podrzędnego    |

### <a name="feature-availability"></a>Dostępność funkcji 

 Więcej funkcji jest dostępnych w przypadku korzystania ze zdalnych obliczeń, jak pokazano w poniższej tabeli. 

| Cechy                                                    | Zdalne | Lokalne | 
|------------------------------------------------------------|--------|-------|
| Przesyłanie strumieniowe danych (obsługa dużej ilości danych, do 100 GB)          | ✓      |       | 
| DNN BERT tekstu cechowania i szkolenia             | ✓      |       |
| Wbudowana obsługa procesora GPU (szkolenia i wnioskowanie)        | ✓      |       |
| Obsługa klasyfikacji i etykietowania obrazów                  | ✓      |       |
| Modele autoARIMA, Prophet i ForecastTCN do prognozowania | ✓      |       | 
| Wiele przebiegów/iteracji równolegle                       | ✓      |       |
| Tworzenie modeli z możliwością interpretacji w interfejsie użytkownika środowiska Web AutoML Studio      | ✓      |       |
| Dostosowywanie inżynierii funkcji w interfejsie użytkownika programu Studio Web Experience| ✓      |       |
| Dostrajanie parametru do usługi Azure ML                             | ✓      |       |
| Obsługa przepływu pracy potoku usługi Azure ML                         | ✓      |       |
| Kontynuuj przebieg                                             | ✓      |       |
| Prognozowanie                                                | ✓      | ✓     |
| Twórz i uruchamiaj eksperymenty w notesach                    | ✓      | ✓     |
| Zarejestruj i Wizualizuj informacje i metryki eksperymentu w interfejsie użytkownika | ✓      | ✓     |
| Guardrails danych                                            | ✓      | ✓     |

## <a name="many-models"></a>Wiele modeli 

[Akcelerator rozwiązań wielu modeli](https://aka.ms/many-models) (wersja zapoznawcza) jest oparty na Azure Machine Learning i umożliwia korzystanie z zautomatyzowanej ml do uczenia i obsługi setek, a nawet tysięcy modeli uczenia maszynowego.

Na przykład Kompilowanie modelu __dla każdego wystąpienia lub poszczególnych osób__ w następujących scenariuszach może prowadzić do udoskonalonych wyników:

* Przewidywanie sprzedaży dla poszczególnych magazynów
* Konserwacja predykcyjna dla setek ropy naftowej
* Dostosowanie środowiska dla poszczególnych użytkowników.

## <a name="automl-in-azure-machine-learning"></a>AutoML w Azure Machine Learning

Azure Machine Learning oferuje dwa środowiska do pracy z zautomatyzowanymi ML:

* W przypadku kodów doświadczonych klientów [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 

* W przypadku klientów z ograniczoną ilością kodu, Azure Machine Learning Studio w [https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Ustawienia eksperymentu 

Poniższe ustawienia umożliwiają skonfigurowanie zautomatyzowanego eksperymentu ML. 

| |Zestaw SDK języka Python|Środowisko sieci Web programu Studio|
----|:----:|:----:
|**Dzielenie danych na zestawy uczenia/walidacji**| ✓|✓
|**Obsługuje zadania ML: klasyfikacje, regresje i prognozowanie**| ✓| ✓
|**Optymalizuje na podstawie metryki podstawowej**| ✓| ✓
|**Obsługuje obliczenia z platformy Azure ML jako element docelowy obliczeń** | ✓|✓
|**Konfigurowanie horyzontu prognoz, Target spowolnienia & przedziału**|✓|✓
|**Ustawianie kryteriów zakończenia** |✓|✓ 
|**Ustaw współbieżne iteracje**| ✓|✓
|**Upuszczanie kolumn**| ✓|✓
|**Algorytmy blokowania**|✓|✓
|**Krzyżowe sprawdzanie poprawności** |✓|✓
|**Obsługuje szkolenia dotyczące klastrów Azure Databricks**| ✓|
|**Przeglądanie nazw projektowanych funkcji**|✓|
|**Podsumowanie cechowania**| ✓|
|**Cechowania dla świąt**|✓|
|**Poziomy szczegółowości pliku dziennika**| ✓|

### <a name="model-settings"></a>Ustawienia modelu

Te ustawienia mogą być stosowane do najlepszego modelu w wyniku zautomatyzowanego eksperymentu ML.

| |Zestaw SDK języka Python|Środowisko sieci Web programu Studio|
|----|:----:|:----:|
|**Najlepsza Rejestracja modelu, wdrażanie, wyjaśnienie**| ✓|✓|
|**Włącz głosujący zestawy kompletów & stosują modele**| ✓|✓|
|**Pokaż najlepszy model na podstawie metryki innej niż podstawowa**|✓||
|**Włącz/Wyłącz zgodność modelu ONNX**|✓||
|**Testowanie modelu** | ✓| |

### <a name="run-control-settings"></a>Uruchom ustawienia kontrolki

Te ustawienia umożliwiają przeglądanie i kontrolowanie przebiegów eksperymentów oraz ich przebiegów podrzędnych. 

| |Zestaw SDK języka Python|Środowisko sieci Web programu Studio|
|----|:----:|:----:|
|**Uruchom tabelę podsumowania**| ✓|✓|
|**Anulowanie przebiegów & podrzędnych przebiegów**| ✓|✓|
|**Pobierz guardrails**| ✓|✓|
|**Wstrzymywanie przebiegów & wznawiania**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Za pomocą Azure Machine Learning można użyć zautomatyzowanej ML do skompilowania modelu języka Python i przekonwertowania go na format ONNX. Gdy modele są w formacie ONNX, można je uruchamiać na różnych platformach i urządzeniach. Dowiedz się więcej [na temat przyspieszania modeli ml za pomocą ONNX](concept-onnx.md).

Zobacz jak skonwertować do formatu ONNX [w tym przykładzie notesu Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Dowiedz się, które [algorytmy są obsługiwane w ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

Środowisko uruchomieniowe ONNX obsługuje również język C#, dzięki czemu można użyć modelu skompilowanego automatycznie w aplikacjach C# bez konieczności ponownego kodowania lub dowolnych opóźnień sieci, które wprowadzają punkty końcowe REST. Dowiedz się więcej o [korzystaniu z modelu ONNX AutoML w aplikacji .NET z](./how-to-use-automl-onnx-model-dotnet.md) modelami ml.NET i [inferencing ONNX za pomocą interfejsu API języka C# środowiska uruchomieniowego ONNX](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Następne kroki

Istnieje wiele zasobów ułatwiających rozpoczęcie pracy z usługą AutoML. 

### <a name="tutorials-how-tos"></a>Samouczki/porady
Samouczki to kompleksowe przykładowe scenariusze AutoML.
+ **Aby zapoznać się z pierwszym doświadczeniem w kodzie** , postępuj zgodnie z [samouczkiem: automatyczne uczenie modelu regresji przy użyciu zestawu SDK języka Python Azure Machine Learning](tutorial-auto-train-models.md).

 + **W przypadku środowiska z niską lub brakiem kodu** zapoznaj się z [samouczkiem: Tworzenie zautomatyzowanych modeli klasyfikacji ml przy użyciu programu Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md).

Jak artykuły zawierają dodatkowe informacje o oferowanych funkcjach AutoML. Przykład: 

+ Konfigurowanie ustawień automatycznych eksperymentów szkoleniowych
    + W programie Azure Machine Learning Studio [wykonaj te kroki](how-to-use-automated-ml-for-ml-models.md). 
    + Za pomocą zestawu SDK języka Python [wykonaj te kroki](how-to-configure-auto-train.md).

+  Dowiedz się, jak korzystać z funkcji autouczenia przy użyciu danych szeregów czasowych, [wykonując następujące kroki](how-to-auto-train-forecast.md).

### <a name="jupyter-notebook-samples"></a>Przykłady notesu Jupyter 

Zapoznaj się ze szczegółowymi przykładami kodu i przypadkami użycia w [repozytorium notesu usługi GitHub w celu uzyskania automatycznych próbek usługi Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).

### <a name="python-sdk-reference"></a>Dokumentacja zestawu SDK języka Python

Pogłębij wiedzę o wzorcach projektowych zestawu SDK i specyfikacjach klas przy użyciu [dokumentacji dotyczącej klasy AutoML](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py&preserve-view=true). 

> [!Note]
> Funkcje automatycznego uczenia maszynowego są również dostępne w innych rozwiązaniach firmy Microsoft, takich jak [ml.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) i [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)

