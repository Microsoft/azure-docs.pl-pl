---
title: Automatyczne szkolenie modelu prognozy szeregów czasowych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z usługi Azure Machine Learning do uczenia modelu regresji prognozowania szeregów czasowych przy użyciu zautomatyzowanego uczenia maszynowego.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: be3046a343e14be4a527363751081ba3f2593cd3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605894"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Automatyczne szkolenie modelu prognozy szeregów czasowych
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak wyszkolić model regresji prognozowania szeregów czasowych przy użyciu zautomatyzowanego uczenia maszynowego w usłudze Azure Machine Learning. Konfigurowanie modelu prognozowania jest podobny do konfigurowania standardowego modelu regresji przy użyciu automatycznego uczenia maszynowego, ale istnieją pewne opcje konfiguracji i kroki przetwarzania wstępnego do pracy z danymi szeregów czasowych. Poniższe przykłady pokazują, jak:

* Przygotowywanie danych do modelowania szeregów czasowych
* Konfigurowanie określonych parametrów szeregów czasowych w obiekcie [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)
* Uruchamianie prognoz z danymi szeregów czasowych

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Za pomocą zautomatyzowanej funkcji uczenia maszynowego można łączyć techniki i podejścia oraz uzyskać zalecaną prognozę serii czasowych wysokiej jakości. Eksperyment automatycznych szeregów czasowych jest traktowany jako problem regresji wielowymiarowej. Przeszłe wartości szeregów czasowych są "przestawne", aby stać się dodatkowymi wymiarami dla regressora wraz z innymi predyktorami.

Takie podejście, w przeciwieństwie do klasycznych metod szeregów czasowych, ma tę zaletę, że naturalnie uwzględnia wiele zmiennych kontekstowych i ich wzajemne relacje podczas treningu. W rzeczywistych aplikacjach prognozowania na prognozę może mieć wpływ wiele czynników. Na przykład podczas prognozowania sprzedaży interakcje trendów historycznych, kurs wymiany i cena wspólnie napędzają wynik sprzedaży. Kolejną korzyścią jest to, że wszystkie najnowsze innowacje w modelach regresji mają zastosowanie natychmiast do prognozowania.

Można [skonfigurować,](#config) jak daleko w przyszłości prognoza powinna rozszerzyć (horyzont prognozy), a także opóźnienia i więcej. Automatyczna funkcja uczenia maszynowego uczy się pojedynczego, ale często wewnętrznie rozgałęziony model dla wszystkich elementów w zestawie danych i horyzontów prognozowania. W związku z tym dostępnych jest więcej danych do oszacowania parametrów modelu i uogólnienie do niewidocznych serii staje się możliwe.

Funkcje wyodrębnione z danych szkoleniowych odgrywają kluczową rolę. Automatyczne przetwarzanie danych ml umożliwia wykonanie standardowych kroków przetwarzania wstępnego i generuje dodatkowe funkcje szeregów czasowych w celu rejestrowania efektów sezonowych i maksymalizacji dokładności predykcyjnej.

## <a name="time-series-and-deep-learning-models"></a>Modele szeregów czasowych i uczenia głębokiego


Zautomatyzowane ml zapewnia użytkownikom zarówno natywnych szeregów czasowych i głębokiego uczenia modeli w ramach systemu rekomendacji. Wśród uczących się są:
+ Proroka
+ Auto-ARIMA
+ ForecastTCN (ForecastTCN)

Zautomatyzowane uczenie głębokie ml pozwala na prognozowanie jednozmiennych i wielowymiarowych danych szeregów czasowych.

Modele uczenia głębokiego mają trzy wewnętrzne możliwości:
1. Mogą uczyć się od dowolnych mapowań od wejść do wyjść
1. Obsługują one wiele wejść i wyjść
1. Mogą automatycznie wyodrębniać wzorce w danych wejściowych, które rozciągają się na długie sekwencje

Biorąc pod uwagę większe dane, modele uczenia głębokiego, takie jak ForecastTCN firmy Microsoft, mogą poprawić wyniki wynikowego modelu. 

Natywne osoby uczące się szeregów czasowych są również dostarczane w ramach zautomatyzowanego uczenia maszynowego. Prorok działa najlepiej z szeregami czasowymi, które mają silne efekty sezonowe i kilka sezonów danych historycznych. Prorok jest dokładny & szybki, solidny dla odstającej, brakujące dane i dramatyczne zmiany w szeregach czasowych. 

Autoregresywna zintegrowana średnia ruchoma (ARIMA) jest popularną metodą statystyczną prognozowania szeregów czasowych. Ta technika prognozowania jest powszechnie stosowana w scenariuszach prognozowania krótkoterminowego, w których dane pokazują dowody trendów, takich jak cykle, które mogą być nieprzewidywalne i trudne do modelowania lub prognozowania. Auto-ARIMA przekształca dane w dane stacjonarne, aby uzyskać spójne i wiarygodne wyniki.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego usługi Azure Machine Learning](how-to-manage-workspace.md).
* W tym artykule przyjęto założenie, że podstawowe znajomości konfigurowania eksperymentu uczenia maszynowego zautomatyzowanego. Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) lub [instrukcjami, aby](how-to-configure-auto-train.md) zobaczyć podstawowe wzorce projektowania eksperymentu automatycznego uczenia maszynowego.

## <a name="preparing-data"></a> Przygotowywanie danych

Najważniejszą różnicą między typem zadania regresji prognozowania a typem zadania regresji w ramach automatycznego uczenia maszynowego jest uwzględnienie funkcji w danych, która reprezentuje prawidłowe serie czasowe. Regularne szeregi czasowe mają dobrze zdefiniowaną i spójną częstotliwość i mają wartość w każdym punkcie próbki w ciągłym przedziale czasu. Należy wziąć pod uwagę `sample.csv`następującą migawkę pliku .

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

Ten zestaw danych jest prostym przykładem dziennych danych sprzedaży dla firmy, która ma dwa różne `week_of_year` sklepy, A i B. Ponadto istnieje funkcja, która pozwoli modelowi wykryć sezonowość tygodniową. Pole `day_datetime` reprezentuje czyste serie czasowe z częstotliwością dzienną, a pole `sales_quantity` jest kolumną docelową dla uruchamiania prognoz. Odczyt danych do dataframe Pandas, `to_datetime` a następnie użyć funkcji, `datetime` aby upewnić się, że szeregi czasowe jest typem.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

W takim przypadku dane są już sortowane rosnąco według pola `day_datetime`czasu . Jednak podczas konfigurowania eksperymentu upewnij się, że kolumna żądanego czasu jest sortowana w porządku rosnącym, aby utworzyć prawidłowe serie czasowe. Załóżmy, że dane zawierają 1000 rekordów i dokonać deterministycznego podziału w danych do tworzenia zestawów danych szkoleniowych i testowych. Zidentyfikuj nazwę kolumny etykiety i ustaw ją na etykietę. W tym przykładzie etykieta będzie `sales_quantity`. Następnie oddziel pole `test_data` etykiety, `test_target` aby utworzyć zestaw.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Podczas szkolenia modelu prognozowania przyszłych wartości, upewnij się, że wszystkie funkcje używane w szkoleniu mogą być używane podczas uruchamiania prognoz dla zamierzonego horyzontu. Na przykład podczas tworzenia prognozy popytu, w tym funkcji dla bieżącej ceny akcji może znacznie zwiększyć dokładność szkolenia. Jeśli jednak zamierzasz prognozować z długim horyzontem, możesz nie być w stanie dokładnie przewidzieć przyszłych wartości zapasów odpowiadających przyszłym punktom szeregów czasowych, a dokładność modelu może ucierpieć.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Konfigurowanie i uruchamianie eksperymentu

Do prognozowania zadań zautomatyzowane uczenie maszynowe używa kroków przetwarzania wstępnego i szacowania, które są specyficzne dla danych szeregów czasowych. Zostaną wykonane następujące etapy przetwarzania wstępnego:

* Wykryj częstotliwość próbkowania szeregów czasowych (na przykład co godzinę, codziennie, co tydzień) i utwórz nowe rekordy dla nieobecnych punktów czasowych, aby seria była ciągła.
* Przypisywanie brakujących wartości w kolumnach docelowych (za pośrednictwem wypełnienia do przodu) i kolumnach elementów (przy użyciu wartości kolumny mediany)
* Tworzenie operacji opartych na ziarnach w celu umożliwienia efektów stałych w różnych seriach
* Tworzenie funkcji opartych na czasie, aby pomóc w nauce wzorców sezonowych
* Kodowanie zmiennych kategorii do ilości liczbowych

Obiekt [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) definiuje ustawienia i dane niezbędne do zadania automatycznego uczenia maszynowego. Podobnie jak problem regresji, można zdefiniować standardowe parametry szkolenia, takie jak typ zadania, liczba iteracji, dane szkoleniowe i liczba krzyżowych weryfikacji. W przypadku zadań prognozowania istnieją dodatkowe parametry, które muszą być ustawione, które wpływają na eksperyment. W poniższej tabeli wyjaśniono każdy parametr i jego użycie.

| Nazwa&nbsp;parametru | Opis | Wymagany |
|-------|-------|-------|
|`time_column_name`|Służy do określania kolumny datetime w danych wejściowych używanych do tworzenia szeregów czasowych i wnioskowania o jej częstotliwości.|✓|
|`grain_column_names`|Nazwy definiujące poszczególne grupy serii w danych wejściowych. Jeśli ziarno nie jest zdefiniowane, zakłada się, że zestaw danych jest jedną serią czasową.||
|`max_horizon`|Definiuje maksymalny żądany horyzont prognozy w jednostkach częstotliwości szeregów czasowych. Jednostki są oparte na przedziale czasu danych treningowych, na przykład co miesiąc, co tydzień, które prognostyk powinien przewidzieć.|✓|
|`target_lags`|Liczba wierszy, które mają opóźnić wartości docelowe na podstawie częstotliwości danych. Opóźnienie jest reprezentowane jako lista lub pojedyncza liczba całkowita. Opóźnienie powinno być używane, gdy relacja między zmiennymi niezależnymi a zmienną zależną nie jest domyślnie zgodna lub skorelowana. Na przykład podczas próby prognozowania popytu na produkt, popyt w dowolnym miesiącu może zależeć od ceny określonych towarów 3 miesiące wcześniej. W tym przykładzie można opóźnić obiekt docelowy (popyt) negatywnie o 3 miesiące, tak aby model trenował poprawną relację.||
|`target_rolling_window_size`|*n* okresy historyczne używane do generowania prognozowanych wartości, <= rozmiar zestawu szkoleniowego. Jeśli pominięto, *n* jest pełny rozmiar zestawu szkoleniowego. Określ ten parametr, jeśli chcesz wziąć pod uwagę tylko pewną ilość historii podczas szkolenia modelu.||
|`enable_dnn`|Włącz prognozowanie dnn.||

Więcej informacji można znaleźć w [dokumentacji referencyjnej.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)

Utwórz ustawienia szeregów czasowych jako obiekt słownika. Ustaw `time_column_name` pole `day_datetime` w zestawie danych. Zdefiniuj parametr, `grain_column_names` aby upewnić się, że dla danych są tworzone **dwie oddzielne grupy szeregów czasowych;** jeden dla sklepu A i B. `max_horizon` Wreszcie, ustawić do 50 w celu przewidzenia dla całego zestawu testów. Ustaw okno prognozy na 10 okresów z `target_rolling_window_size`programem i określ `target_lags` pojedyncze opóźnienie wartości docelowych dla dwóch okresów przed parametrem. Zaleca się ustawienie `max_horizon` `target_rolling_window_size` i `target_lags` "auto", które automatycznie wykryje te wartości. W poniższym przykładzie dla tych parametrów użyto ustawień "auto". 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> Zautomatyzowane kroki wstępnego przetwarzania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na numeryczne itp.) stają się częścią modelu źródłowego. Podczas korzystania z modelu dla prognoz, te same kroki przetwarzania wstępnego stosowane podczas szkolenia są stosowane do danych wejściowych automatycznie.

Definiując `grain_column_names` we wytyp kodu powyżej, AutoML utworzy dwie oddzielne grupy szeregów czasowych, znany również jako wiele szeregów czasowych. Jeśli nie zdefiniowano ziarna, AutoML przyjmie, że zestaw danych jest pojedynczą serią czasową. Aby dowiedzieć się więcej o pojedynczych seriach czasowych, zobacz [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

Teraz utwórz `AutoMLConfig` obiekt standardowy, określając typ `forecasting` zadania i prześlij eksperyment. Po zakończeniu modelu należy pobrać najlepiej uruchom iterację.

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
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Szczegółowe przykłady konfiguracji prognozowania można znaleźć w [przykładowych notesach prognozowania,](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) w tym:

* [wykrywanie i featurization wakacje](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Walidacja krzyżowa pochodzenia toczenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [konfigurowalne opóźnienia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [funkcje agregacji okien tocznych](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Dnn](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Konfigurowanie eksperymentu włączania prognozowania dnn

> [!NOTE]
> Obsługa DNN prognozowania w zautomatyzowanym uczeniu maszynowym jest w wersji zapoznawczej i nie jest obsługiwana dla uruchomień lokalnych.

Aby wykorzystać nazwy DNn do prognozowania, należy `enable_dnn` ustawić parametr w AutoMLConfig na true. 

Zaleca się używanie klastra obliczeniowego AML z jednostkami SKU GPU i co najmniej dwoma węzłami jako obiektem docelowym obliczeń. Aby zapewnić wystarczającą ilość czasu na ukończenie szkolenia DNN, zalecamy ustawienie limitu czasu eksperymentu na co najmniej kilka godzin.
Aby uzyskać więcej informacji na temat rozmiarów obliczeń AML i maszyn wirtualnych, które obejmują procesory [graficzne, zobacz dokumentację obliczeń AML](how-to-set-up-training-targets.md#amlcompute) i [dokumentację zoptymalizowanych dla maszyn wirtualnych.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)

Zobacz [notes prognozowania produkcji napojów,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) aby uzyskać szczegółowy przykład kodu wykorzystujący nazwy DNN.

### <a name="view-feature-engineering-summary"></a>Wyświetlanie podsumowania inżynierii funkcji

W przypadku typów zadań szeregów czasowych w zautomatyzowanym uczeniu maszynowym można wyświetlić szczegóły z procesu inżynierii funkcji. Poniższy kod przedstawia każdą nieprzetworzoną operację wraz z następującymi atrybutami:

* Nazwa obiektu nieprzetworzonego
* Liczba zaprojektowanych funkcji utworzonych z tej surowej funkcji
* Wykryto typ
* Czy funkcja została upuszczona
* Lista przekształceń operacji dla obiektu nieprzetworzonego

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Prognozowanie z najlepszym modelem

Użyj najlepszej iteracji modelu do prognozowania wartości dla zestawu danych testowych.

Funkcja `forecast()` powinna być używana `predict()`zamiast , to pozwoli na specyfikacje, kiedy prognozy powinny się rozpocząć. W poniższym przykładzie najpierw należy `y_pred` `NaN`zastąpić wszystkie wartości w pliku . Prognozowane pochodzenie będzie na końcu danych szkoleniowych w tym przypadku, `predict()`jak to zwykle bywa podczas korzystania . Jeśli jednak zastąpisz tylko drugą połowę `y_pred` , `NaN`funkcja pozostawi wartości liczbowe w pierwszej połowie `NaN` bez modyfikacji, ale prognozuje wartości w drugiej połowie. Funkcja zwraca zarówno prognozowane wartości, jak i wyrównane operacje.

Można również użyć `forecast_destination` parametru `forecast()` w funkcji do prognozowania wartości do określonej daty.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Oblicz RMSE (główny średni błąd `actual_labels` kwadratowy) między wartościami `predict_labels`rzeczywistymi a prognozowanymi wartościami w .

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Teraz, gdy ogólna dokładność modelu została określona, najbardziej realistycznym następnym krokiem jest użycie modelu do prognozowania nieznanych przyszłych wartości. Podaj zestaw danych w tym `test_data` samym formacie co zestaw testów, ale z przyszłych dat, a wynikowy zestaw prognozowania jest prognozowane wartości dla każdego kroku szeregów czasowych. Załóżmy, że ostatnie rekordy serii czasowych w zestawie danych były dla 12/31/2018. Aby prognozować popyt na następny dzień (lub tyle okresów, ile `max_horizon`trzeba prognozować, <= ), utwórz pojedynczy rekord szeregów czasowych dla każdego sklepu na dzień 01.01.2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Powtórz niezbędne kroki, aby załadować te przyszłe `best_run.predict(test_data)` dane do ramki danych, a następnie uruchom, aby przewidzieć przyszłe wartości.

> [!NOTE]
> Nie można przewidzieć wartości dla liczby `max_horizon`okresów większych niż . Model musi być przeszkolony z większym horyzontem, aby przewidzieć przyszłe wartości poza bieżącym horyzontem.

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z [samouczkiem,](tutorial-auto-train-models.md) aby dowiedzieć się, jak tworzyć eksperymenty z automatycznym uczeniem maszynowym.
* Wyświetl zestaw [SDK usługi Azure Machine Learning dla](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) dokumentacji referencyjnej języka Python.
