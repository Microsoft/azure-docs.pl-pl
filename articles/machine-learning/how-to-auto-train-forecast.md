---
title: Autouczenie modelu prognozowania szeregów czasowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać Azure Machine Learning do uczenia modelu regresji prognozowania szeregów czasowych przy użyciu funkcji automatycznego uczenia maszynowego.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 47cc67b408ff7fa50a244fffa8d41e640df0ecf3
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796435"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Autouczenie modelu prognozowania szeregów czasowych


W tym artykule dowiesz się, jak skonfigurować i przeszkolić model regresji do prognozowania szeregów czasowych przy użyciu funkcji automatycznego uczenia maszynowego AutoML [Azure Machine Learning w zestawie SDK języka Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

W tym celu wykonasz następujące czynności: 

> [!div class="checklist"]
> * Przygotowywanie danych do modelowania szeregów czasowych.
> * Skonfiguruj określone parametry szeregów czasowych w [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) obiekcie.
> * Uruchamiaj przewidywania z danymi szeregów czasowych.

Aby zapoznać się z małym doświadczeniem w kodzie, zobacz [Samouczek: prognozowanie popytu na automatyczne Uczenie maszynowe](tutorial-automated-ml-forecast.md) na potrzeby prognozowania szeregów czasowych przy użyciu funkcji automatycznego uczenia maszynowego w [Azure Machine Learning Studio](https://ml.azure.com/).

W przeciwieństwie do klasycznych metod szeregów czasowych, w zautomatyzowanych ML, przeszłe wartości szeregów czasowych są "przestawne", aby stać się dodatkowymi wymiarami regresor wraz z innymi predykcyjnymi. Takie podejście obejmuje wiele zmiennych kontekstowych i ich relacji ze sobą podczas uczenia się. Ze względu na to, że wiele czynników ma wpływ na prognozę, ta metoda wyrównuje siebie dobrze z rzeczywistymi scenariuszami prognozowania. Na przykład podczas prognozowania sprzedaży, interakcji historycznych trendów, kursu wymiany i ceny wszystkie wspólnie łączą wynik sprzedaży. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule należy 

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* W tym artykule założono pewne doświadczenie w konfigurowaniu zautomatyzowanego eksperymentu uczenia maszynowego. Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) lub poradami [, aby zobaczyć](how-to-configure-auto-train.md) główne wzorce projektowe eksperymentu w usłudze Machine Learning.

## <a name="preparing-data"></a> Przygotowywanie danych

Najważniejszym różnicą między typem zadania regresja prognozowania a typem zadania regresja w ramach AutoML jest dołączenie funkcji w danych, która reprezentuje prawidłową serię czasową. Zwykła seria czasowa ma dobrze zdefiniowaną i spójną częstotliwość i ma wartość w każdym punkcie próbki w ciągłym przedziale czasu. 

Weź pod uwagę poniższą migawkę pliku `sample.csv` .
Ten zestaw danych ma dzienne dane sprzedaży dla firmy, która ma dwa różne sklepy, A i B. 

Ponadto istnieją funkcje dla programu

 *  `week_of_year`: umożliwia modelowi wykrywanie sezonowości tygodniowego.
* `day_datetime`: reprezentuje czystą serię czasową z częstotliwością dzienną.
* `sales_quantity`: kolumna docelowa do uruchamiania prognoz. 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Odczytaj dane do ramki dataPandas, a następnie użyj `to_datetime` funkcji, aby upewnić się, że serie czasowe są `datetime` typu.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

W takim przypadku dane są już sortowane rosnąco według pola Time `day_datetime` . Jednak podczas konfigurowania eksperymentu upewnij się, że kolumna żądana godzina jest posortowana w kolejności rosnącej, aby utworzyć prawidłową serię czasową. 

Poniższy kod, 
* Przyjęto, że dane zawierają 1 000 rekordów i czynią deterministyczną podział w danych w celu tworzenia zestawów danych szkoleniowych i testowych. 
* Identyfikuje kolumnę etykiety jako `sales_quantity` .
* Oddziela pole Etykieta od `test_data` , aby utworzyć `test_target` zestaw.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> Podczas uczenia modelu do prognozowania przyszłych wartości upewnij się, że wszystkie funkcje używane w szkoleniu mogą być używane podczas przewidywania dla zamierzonego horyzontu. <br> <br>Na przykład podczas tworzenia prognozy popytu, w tym funkcji dla bieżącej ceny zapasowej, można w znacznym stopniu zwiększyć dokładność szkolenia. Jeśli jednak planujesz prognozowanie za pomocą długich horyzontów, możesz nie być w stanie dokładnie przewidzieć przyszłe wartości giełdowe odpowiadające przyszłym punktom szeregów czasowych, a dokładność modelu może być niepoprawna.

<a name="config"></a>

## <a name="training-and-validation-data"></a>Dane szkoleniowe i weryfikacyjne

Można określić oddzielne zestawy pouczenia i walidacji bezpośrednio w `AutoMLConfig` obiekcie.   Dowiedz się więcej o [AutoMLConfig](#configure-experiment).

W przypadku prognozowania szeregów czasowych domyślnie do walidacji jest używana wyłącznie funkcja sprawdzania **poprawności danych pierwotnych (ROCV)** . Przekaż dane szkoleniowe i walidacji, a następnie ustaw liczbę elementów walidacji krzyżowej z `n_cross_validations` parametrem w `AutoMLConfig` . ROCV dzieli serię na dane szkoleniowe i weryfikacyjne przy użyciu punktu czasu pochodzenia. Przesuwanie źródła w czasie powoduje wygenerowanie zgięcia wzajemnego sprawdzania poprawności. Ta strategia zachowuje integralność danych szeregów czasowych i eliminuje ryzyko wycieku danych

![krzyżowe sprawdzanie poprawności źródła](./media/how-to-auto-train-forecast/ROCV.svg)

Możesz również wprowadzić własne dane sprawdzania poprawności, dowiedzieć się więcej w temacie [Konfigurowanie podziałów danych i wzajemnego sprawdzania poprawności w programie AutoML](how-to-configure-cross-validation-data-splits.md#provide-validation-data).


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

Dowiedz się więcej o tym, jak AutoML stosuje krzyżowe sprawdzanie poprawności, aby [uniknąć nadmiernego dopasowania modeli](concept-manage-ml-pitfalls.md#prevent-over-fitting).

## <a name="configure-experiment"></a>Konfigurowanie eksperymentu

[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py)Obiekt definiuje ustawienia i dane niezbędne do automatycznego zadania uczenia maszynowego. Konfiguracja dla modelu prognozowania jest podobna do konfiguracji standardowego modelu regresji, ale niektóre modele, opcje konfiguracji i cechowania czynności istnieją w odniesieniu do danych szeregów czasowych. 

### <a name="supported-models"></a>Obsługiwane modele
Automatyczne Uczenie maszynowe automatycznie próbuje różne modele i algorytmy w ramach procesu tworzenia i dostrajania modelu. Jako użytkownik nie ma potrzeby określania algorytmu. Do prognozowania eksperymentów zarówno natywna seria czasowa, jak i modele uczenia głębokiego są częścią systemu rekomendacji. W poniższej tabeli zestawiono ten podzbiór modeli. 

>[!Tip]
> Tradycyjne modele regresji są również testowane jako część systemu rekomendacji do prognozowania eksperymentów. Pełną listę modeli można znaleźć w [tabeli obsługiwanej modelu](how-to-configure-auto-train.md#supported-models) . 

Modele| Opis | Korzyści
----|----|---
Prophet (wersja zapoznawcza)|Prophet działa najlepiej z seriami czasowymi, które mają silne skutki sezonowe i kilka sezonów danych historycznych. Aby skorzystać z tego modelu, zainstaluj go lokalnie przy użyciu `pip install fbprophet` . | Dokładne & szybka, niezawodna do wartości odstających, brakujących danych i znaczących zmian w szeregach czasowych.
AutoARIMA (wersja zapoznawcza)|Funkcja autoregresywnych zintegrowanej średniej ruchomej (ARIMA) sprawdza się najlepiej, gdy dane są nieruchome. Oznacza to, że właściwości statystyczne, takie jak średnia i Wariancja, są stałe dla całego zestawu. Na przykład, jeśli przerzucasz monety, prawdopodobieństwo pobrania przez Ciebie głowice wynosi 50%, bez względu na to, czy przewracasz dzisiaj, jutro lub w następnym roku.| Świetnie dla serii univariate, ponieważ przeszłe wartości są używane do przewidywania przyszłych wartości.
ForecastTCN (wersja zapoznawcza)| ForecastTCN to model sieci neuronowych zaprojektowany z myślą o najbardziej wymagających zadaniach prognozowania, przechwytującym nieliniowe i globalne trendy w danych, a także relacje między seriami czasowymi.|Można wykorzystać złożone trendy w danych i łatwo skalować je do największych z nich.

### <a name="configuration-settings"></a>Ustawienia konfiguracji

Podobnie jak w przypadku problemu z regresją, definiuje się standardowe parametry szkolenia, takie jak typ zadania, liczba iteracji, dane szkoleniowe i liczba operacji krzyżowych. W przypadku zadań prognozowania należy ustawić dodatkowe parametry, które mają wpływ na eksperyment. 

Poniższa tabela zawiera podsumowanie tych dodatkowych parametrów. Zapoznaj się z [dokumentacją](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) dotyczącą wzorców projektu składni.

| &nbsp;Nazwa parametru | Opis | Wymagane |
|-------|-------|-------|
|`time_column_name`|Służy do określania kolumny DateTime w danych wejściowych używanych do kompilowania szeregów czasowych i wywnioskowania jej częstotliwości.|✓|
|`forecast_horizon`|Definiuje, ile okresów ma być prognozowanie. Horyzont jest w jednostkach częstotliwości szeregów czasowych. Jednostki są oparte na przedziale czasu na dane szkoleniowe, na przykład co miesiąc, co tydzień, co Prognoza powinna przewidzieć.|✓|
|`enable_dnn`|[Włącz prognozowanie DNNs]().||
|`time_series_id_column_names`|Nazwy kolumn używane do unikatowego identyfikowania szeregów czasowych w danych, które mają wiele wierszy z tą samą sygnaturą czasową. Jeśli identyfikatory szeregów czasowych nie są zdefiniowane, zakłada się, że zestaw danych jest jedną serią czasową. Aby dowiedzieć się więcej o pojedynczych seriach czasowych, zobacz [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).||
|`freq`| Częstotliwość zestawu danych szeregów czasowych. Ten parametr reprezentuje okres, w którym powinny wystąpić zdarzenia, takie jak codziennie, co tydzień, co rok itd. Częstotliwość musi być [aliasem offsetu Pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects).||
|`target_lags`|Liczba wierszy do rozłożeniu wartości docelowych na podstawie częstotliwości danych. Opóźnienie jest reprezentowane jako lista lub jedna liczba całkowita. Zwłoki należy używać, gdy relacja między zmiennymi niezależnymi i zmienną zależną nie jest zgodna ani nie jest domyślnie skorelowana. ||
|`feature_lags`| Funkcja zwłoki zostanie automatycznie podjęta przy użyciu zautomatyzowanej ML `target_lags` , gdy są ustawione i `feature_lags` ma ustawioną wartość `auto` . Włączenie funkcji spowolnienia może pomóc w zwiększeniu dokładności. Funkcja spowolnienia jest domyślnie wyłączona. ||
|`target_rolling_window_size`|*n* okresy historyczne używane do generowania prognozowanych wartości, <= rozmiar zestawu szkoleniowego. W przypadku pominięcia *n* to pełny rozmiar zestawu szkoleniowego. Określ ten parametr, jeśli chcesz wziąć pod uwagę tylko określoną ilość historii podczas uczenia modelu. Dowiedz się więcej o [agregacji przedziałów okien docelowych](#target-rolling-window-aggregation).||
|`short_series_handling_config`| Umożliwia obsługę krótkich szeregów czasowych, aby uniknąć awarii podczas szkoleń z powodu niewystarczających ilości danych. Obsługa krótkich serii jest domyślnie ustawiona na wartość `auto` . Dowiedz się więcej o [obsłudze krótkich serii](#short-series-handling).|


Poniższy kod, 
* Wykorzystuje [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py) klasę do definiowania parametrów prognozowania dla szkolenia eksperymentu
* Ustawia `time_column_name` do `day_datetime` pola w zestawie danych. 
* Definiuje `time_series_id_column_names` parametr do `"store"` . Zapewnia to, że dla danych są tworzone **dwie osobne grupy szeregów czasowych** . jeden dla sklepu A i B.
* Ustawia wartość `forecast_horizon` na 50, aby przewidzieć cały zestaw testów. 
* Ustawia okno prognozy na 10 okresów z `target_rolling_window_size`
* Określa pojedyncze opóźnienie dla wartości docelowych dla dwóch okresów wraz z `target_lags` parametrem. 
* Ustawia `target_lags` ustawienie zalecane automatyczne, które automatycznie wykryje tę wartość.

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

`forecasting_parameters`Są one następnie przesyłane do obiektu standardowego `AutoMLConfig` wraz z `forecasting` typem zadania, metryką podstawową, kryteriami wyjścia i danymi szkoleniowymi. 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

### <a name="featurization-steps"></a>Cechowania kroki

W każdym automatycznym doświadczeniu uczenia maszynowego automatyczne skalowanie i techniki normalizacji są domyślnie stosowane do danych. Techniki te są typami **cechowania** , które pomagają *określonym* algorytmom, które są wrażliwe na funkcje w różnych skali. Dowiedz się więcej o domyślnych krokach cechowania w [cechowania w AutoML](how-to-configure-auto-features.md#automatic-featurization)

Jednakże następujące kroki są wykonywane tylko w przypadku `forecasting` typów zadań:

* Wykrywaj częstotliwość próbkowania szeregów czasowych (na przykład co godzinę, codziennie, co tydzień) i Utwórz nowe rekordy dla nieobecnych punktów czasowych, aby zapewnić ciągłość serii.
* Nie ma wartości w elemencie docelowym (za pośrednictwem przekazywania) i kolumn funkcji (przy użyciu wartości kolumn mediany).
* Tworzenie funkcji opartych na identyfikatorach szeregów czasowych w celu włączenia stałych efektów w różnych seriach
* Tworzenie funkcji opartych na czasie, które ułatwiają uczenie wzorców sezonowych
* Koduj zmienne kategorii na liczby liczbowe

Aby uzyskać podsumowanie, jakie funkcje są tworzone w wyniku tych kroków, zobacz [przezroczystość cechowania](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> Zautomatyzowane kroki cechowania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Korzystając z modelu dla prognoz, te same kroki cechowania stosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

#### <a name="customize-featurization"></a>Dostosuj cechowania

Istnieje również możliwość dostosowania ustawień cechowania, aby upewnić się, że dane i funkcje, które są używane do uczenia modelu ML, powodują odpowiednie przewidywania. 

Obsługiwane dostosowania `forecasting` zadań obejmują:

|Dostosowywanie|Definicja|
|--|--|
|**Aktualizacja celu kolumny**|Przesłoń automatyczne wykrywanie typu funkcji dla określonej kolumny.|
|**Aktualizacja parametru Transformer** |Zaktualizuj parametry dla określonej funkcji przekształcania. Obecnie obsługuje *program* obsługujący program (fill_value i mediana).|
|**Upuszczanie kolumn** |Określa kolumny do porzucenia z featurized.|

Aby dostosować featurizations z zestawem SDK, określ `"featurization": FeaturizationConfig` w `AutoMLConfig` obiekcie. Dowiedz się więcej na temat [niestandardowych featurizations](how-to-configure-auto-features.md#customize-featurization).

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Jeśli używasz programu Azure Machine Learning Studio dla eksperymentu, zobacz [jak dostosować cechowania w programie Studio](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

## <a name="optional-configurations"></a>Konfiguracje opcjonalne

Dodatkowe konfiguracje opcjonalne są dostępne do prognozowania zadań, takich jak Włączanie głębokiej uczenia i określanie docelowej agregacji okna. 

### <a name="enable-deep-learning"></a>Włącz uczenie głębokie

> [!NOTE]
> Obsługa DNN w przypadku prognozowania w programie zautomatyzowanym Machine Learning jest w **wersji zapoznawczej** i nie jest obsługiwana w przypadku uruchomień lokalnych.

Możesz również wykorzystać głębokie uczenie w głębokiej sieci neuronowych, DNNs, aby poprawić wyniki modelu. Uczenie głębokie o rozbudowanej ML umożliwia prognozowanie danych szeregów czasowych univariate i wieloczynnikowa.

Modele uczenia głębokiego mają trzy możliwości wewnętrzne:
1. Mogą uczyć się z dowolnego mapowania z danych wejściowych do wyjścia
1. Obsługują one wiele wejść i wyjść
1. Mogą automatycznie wyodrębniać wzorce w danych wejściowych, które rozciągają się na długie sekwencje. 

Aby włączyć uczenie głębokie, ustaw wartość `enable_dnn=True` w `AutoMLConfig` obiekcie.

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> Po włączeniu DNN dla eksperymentów utworzonych przy użyciu zestawu SDK [najlepsze wyjaśnienia modeli](how-to-machine-learning-interpretability-automl.md) są wyłączone.

Aby włączyć DNN dla eksperymentu AutoML utworzonego w Azure Machine Learning Studio, zapoznaj się z [ustawieniami typu zadania w programie Studio How to-to](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

Aby zapoznać się ze szczegółowym przykładem kodu korzystającego z DNNs, zobacz [Notes prognozowania produkcji napojów](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) .

### <a name="target-rolling-window-aggregation"></a>Agregacja stopniowego okna docelowego
Często najlepszą informacją, jaką może mieć Prognoza, jest Ostatnia wartość elementu docelowego.  Agregacje przedziałów okien docelowych umożliwiają dodanie kroczącej agregacji wartości danych jako funkcji. Generowanie i używanie tych dodatkowych funkcji jako dodatkowych danych kontekstowych ułatwia dokładność modelu uczenia.

Załóżmy na przykład, że chcesz przewidzieć zapotrzebowanie na energię. Możesz chcieć dodać funkcję okna kroczącego o trzech dniach, aby móc wprowadzić zmiany termiczne z miejscami do magazynowania. W tym przykładzie Utwórz to okno przez ustawienie `target_rolling_window_size= 3` w `AutoMLConfig` konstruktorze. 

W tabeli przedstawiono wyniki inżynierii funkcji, która występuje, gdy stosowana jest agregacja okna. Kolumny dla wartości **minimum, maksimum** i **sum** są generowane w przedziale okna trzech na podstawie zdefiniowanych ustawień. Każdy wiersz ma nową funkcję obliczeniową. w przypadku sygnatury czasowej 8 września 2017 4:10:00 wartości maksymalne, minimum i sum są obliczane przy użyciu **wartości popytu** dla 8 września 2017 1:10:00-3:10:00. To okno z trzema zmianami i wypełnia dane dla pozostałych wierszy.

![okno kroczące docelowe](./media/how-to-auto-train-forecast/target-roll.svg)

Zobacz przykład kodu w języku Python, wykorzystując [funkcję agregacji przedziału](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)czasu dla docelowej.

### <a name="short-series-handling"></a>Obsługa krótkich serii

Automatyczna część ML traktuje serię czasową jako **krótką serię** , jeśli nie ma wystarczającej liczby punktów danych do przeprowadzenia fazy tworzenia i weryfikacji modelu. Liczba punktów danych jest różna dla każdego eksperymentu i zależy od max_horizon, liczby podziałów wzajemnego sprawdzania poprawności oraz długości modelu lookback, który jest maksymalną historią, która jest wymagana do skonstruowania funkcji szeregów czasowych. Dokładne obliczenie można znaleźć w [dokumentacji referencyjnej short_series_handling_configuration](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters?preserve-view=true&view=azure-ml-py#short-series-handling-configuration).

Zautomatyzowanej ML oferuje obsługę krótkich serii domyślnie przy użyciu `short_series_handling_configuration` parametru w `ForecastingParameters` obiekcie. 

Aby włączyć obsługę krótkich serii, `freq` parametr musi również być zdefiniowany. Aby zdefiniować częstotliwość godzinową, zostanie ustawiona `freq='H'` . W [tym miejscu](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)przejrzyj opcje ciągu częstotliwości. Aby zmienić zachowanie domyślne, `short_series_handling_configuration = 'auto'` należy zaktualizować `short_series_handling_configuration` parametr w `ForecastingParameter` obiekcie.  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
Poniższa tabela zawiera podsumowanie dostępnych ustawień programu `short_series_handling_config` .
 
|Ustawienie|Opis
|---|---
|`auto`| Poniżej przedstawiono domyślne zachowanie obsługi krótkich serii <li> *Jeśli cała seria jest krótka*, Zablokuj dane. <br> <li> *Jeśli nie wszystkie serie są krótkie*, Usuń krótką serię. 
|`pad`| Jeśli `short_series_handling_config = pad` , wówczas automatyczna wartość ml dodaje losowo wartości do każdej znalezionej serii krótkiej. Poniżej przedstawiono listę typów kolumn i ich uzupełniania: <li>Kolumny obiektów z NaNs <li> Kolumny liczbowe z wartością 0 <li> Kolumny logiczne/logiczne z wartością false <li> Kolumna docelowa jest uzupełniona losowo wartościami o wartości zero i odchylenie standardowe równe 1. 
|`drop`| Jeśli `short_series_handling_config = drop` , wówczas automatyczne odniesie się do krótkich serii i nie zostanie wykorzystane do szkolenia ani przewidywania. Przewidywania dla tych serii zwracają.
|`None`| Żadna seria nie jest uzupełniona ani porzucona

>[!WARNING]
>Uzupełnienie może mieć wpływ na dokładność modelu wynikowego, ponieważ wprowadzamy sztuczne dane tylko w celu uzyskania wcześniejszych szkoleń bez błędów. <br> <br> Jeśli wiele serii jest krótkich, można także zobaczyć wpływ na wyniki wyjaśniające

## <a name="run-the-experiment"></a>Uruchamianie eksperymentu 

Gdy `AutoMLConfig` obiekt jest gotowy, możesz przesłać eksperyment. Po zakończeniu działania modelu Pobierz iterację najlepszego przebiegu.

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>Prognozowanie przy użyciu najlepszego modelu

Użyj najlepszej iteracji modelu do prognozowania wartości dla zestawu danych testowych.

`forecast()`Funkcja umożliwia określenie, kiedy mają być uruchamiane przewidywania, w przeciwieństwie do `predict()` , które jest zwykle używane do zadań klasyfikacji i regresji.

W poniższym przykładzie należy najpierw zastąpić wszystkie wartości w `y_pred` `NaN` . Podstawą prognozy będzie na końcu danych szkoleniowych w tym przypadku. Jeśli jednak zamienisz tylko drugą połowę `y_pred` z `NaN` , funkcja spowodowałaby pozostawienie wartości liczbowych w pierwszej połowie niemodyfikowanej, ale prognozowanie `NaN` wartości w drugiej połowie. Funkcja zwraca zarówno wartości prognozowane, jak i wyrównane funkcje.

Można również użyć `forecast_destination` parametru w `forecast()` funkcji, aby prognozować wartości aż do określonej daty.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Oblicz średnią wartość "pierwiastek" w przypadku błędu (RMSE) między `actual_labels` wartościami rzeczywistymi i prognozowanymi wartościami w `predict_labels` .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Teraz, gdy ogólna dokładność modelu została określona, najbardziej realistycznym następnym krokiem jest użycie modelu do prognozowania nieznanych przyszłych wartości. 

Podaj zestaw danych w tym samym formacie co zestaw testów `test_data` , ale z przyszłymi datetimemi, a wynikający z nich zestaw prognoz to prognozowane wartości dla każdego kroku szeregów czasowych. Załóżmy, że ostatnie rekordy szeregów czasowych w zestawie danych były 12/31/2018. Aby prognozować zapotrzebowanie na następny dzień (lub wiele okresów potrzebnych do prognozowania, <= `forecast_horizon` ), Utwórz pojedynczy rekord szeregu czasowego dla każdego magazynu dla 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Powtórz kroki niezbędne do załadowania tych przyszłych danych do ramki Dataframe, a następnie uruchom polecenie `best_run.predict(test_data)` , aby przewidzieć przyszłe wartości.

> [!NOTE]
> Wartości nie mogą być przewidywane dla liczby okresów większej niż `forecast_horizon` . Model musi być przeszkolony z większym horyzontem, aby przewidzieć przyszłe wartości poza bieżącym horyzontem.


## <a name="example-notebooks"></a>Przykładowe notesy
Zapoznaj się z [przykładami prognozowanych notesów](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) , aby zapoznać się ze szczegółowymi przykładami konfiguracji prognozowania, w tym:

* [Wykrywanie świąt i cechowania](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [krzyżowe sprawdzanie poprawności źródła](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Konfigurowalne spowolnienia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [funkcje agregujące okna stopniowego](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym [, jak i gdzie wdrożyć model](how-to-deploy-and-where.md).
* Dowiedz się więcej [na temat interpretacji: informacje o modelu w zautomatyzowanej usłudze Machine Learning (wersja zapoznawcza)](how-to-machine-learning-interpretability-automl.md). 
* Dowiedz się, jak uczenie wielu modeli za pomocą AutoML w [akceleratorze rozwiązań wielu modeli](https://aka.ms/many-models).
* Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) , aby uzyskać kompleksowy przykład tworzenia eksperymentów przy użyciu zautomatyzowanej uczenia maszynowego.
