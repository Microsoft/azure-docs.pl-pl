---
title: Podział danych i wzajemne sprawdzanie poprawności w ramach automatycznej uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować podziały zestawu danych i wzajemne sprawdzanie poprawności dla zautomatyzowanych eksperymentów usługi Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 02/23/2021
ms.openlocfilehash: 31d3dc2c2d8194541ba1fe7d0865e6c939d75f73
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501586"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Konfigurowanie podziałów danych i krzyżowego sprawdzania poprawności w ramach zautomatyzowanego uczenia maszynowego

W tym artykule przedstawiono różne opcje konfigurowania danych szkoleniowych i podziałów danych sprawdzania poprawności oraz ustawienia wzajemnego sprawdzania poprawności dla zautomatyzowanej uczenia maszynowego, zautomatyzowanej sieci, eksperymentów.

W Azure Machine Learning, gdy używasz zautomatyzowanej ML do kompilowania wielu modeli ML, każdy przebieg podrzędny musi sprawdzać pokrewny model, obliczając metryki jakości dla tego modelu, na przykład dokładność lub AUC ważone. Te metryki są obliczane przez porównanie prognoz wykonanych z poszczególnymi modelami z rzeczywistymi etykietami z ostatnich obserwacji w danych sprawdzania poprawności. [Dowiedz się więcej o tym, jak metryki są obliczane na podstawie typu walidacji](#metric-calculation-for-cross-validation-in-machine-learning). 

Automatyczne eksperymenty w MILILITRach wykonują walidację modelu automatycznie. W poniższych sekcjach opisano, jak można modyfikować ustawienia walidacji za pomocą [zestawu SDK języka Python Azure Machine Learning](/python/api/overview/azure/ml/). 

Aby zapoznać się z niską ilością kodu lub bez kodu, zobacz [Tworzenie zautomatyzowanych eksperymentów uczenia maszynowego w programie Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment). 

> [!NOTE]
> W programie Studio są obecnie obsługiwane dane szkoleniowe i weryfikacyjne oraz opcje sprawdzania poprawności, ale nie obsługują one określania poszczególnych plików danych dla zestawu weryfikacyjnego. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule należy

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* Znajomość konfigurowania zautomatyzowanego eksperymentu uczenia maszynowego przy użyciu zestawu SDK Azure Machine Learning. Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) lub zapoznaj się z artykułami [, aby zobaczyć](how-to-configure-auto-train.md) podstawowe wzorce projektu eksperymentu w usłudze Machine Learning.

* Zrozumienie danych związanych z pouczeniem/sprawdzaniem poprawności polega na rozdzieleniu i weryfikacji krzyżowej w formie koncepcji uczenia maszynowego. Ogólne wyjaśnienie,

    * [Informacje o szkoleniu, sprawdzaniu poprawności i testowaniu danych w usłudze Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Opis krzyżowego sprawdzania poprawności w usłudze Machine Learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd) 

## <a name="default-data-splits-and-cross-validation-in-machine-learning"></a>Domyślne podziały danych i wzajemne sprawdzanie poprawności w usłudze Machine Learning

Użyj obiektu [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) , aby zdefiniować ustawienia eksperymentu i szkolenia. W poniższym fragmencie kodu należy zauważyć, że są zdefiniowane tylko wymagane parametry, które są parametrami dla `n_cross_validation` lub `validation_ data` **nie** są uwzględniane.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Jeśli nie określisz jawnie `validation_data` `n_cross_validation` parametru lub, automatyczna ml stosuje domyślne techniki w zależności od liczby wierszy podanych w pojedynczym zestawie danych `training_data` :

|&nbsp;Rozmiar danych szkoleniowych &nbsp;| Technika walidacji |
|---|-----|
|**Więcej &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Zastosowano podział danych szkolenia/walidacji. Wartość domyślna to przejęcie 10% początkowego zestawu danych szkoleniowych jako zestawu walidacji. Z kolei ten zestaw walidacji jest używany do obliczania metryk.
|**Mniejsze &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Stosowana jest metoda weryfikacji krzyżowej. Domyślna liczba zagięć zależy od liczby wierszy. <br> **Jeśli zestaw danych jest mniejszy niż 1 000 wierszy**, używane są 10 zagięć. <br> **Jeśli wiersze są z zakresu od 1 000 do 20 000**, używane są trzy składowe.

## <a name="provide-validation-data"></a>Podaj dane weryfikacji

W takim przypadku można rozpocząć od pojedynczego pliku danych i podzielić go na dane szkoleniowe i zestawy danych sprawdzania poprawności lub podać osobny plik danych dla zestawu walidacji. W obu przypadkach `validation_data` parametr w `AutoMLConfig` obiekcie przypisuje dane, które mają być używane jako zestaw walidacji. Ten parametr akceptuje tylko zestawy danych w postaci [Azure Machine Learning DataSet](how-to-create-register-datasets.md) lub Pandas Dataframe.   

> [!NOTE]
> Ten `validation_size` parametr nie jest obsługiwany w scenariuszach prognozowania.

Poniższy przykład kodu jawnie definiuje, która część dostarczonych danych jest używana do `dataset` szkolenia i weryfikacji.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Podaj rozmiar zestawu walidacji

W takim przypadku dla eksperymentu jest dostarczany tylko jeden zestaw danych. Oznacza to, że `validation_data` parametr **nie** jest określony, a podany zestaw danych jest przypisany do  `training_data` parametru.  

W `AutoMLConfig` obiekcie można ustawić `validation_size` parametr, aby wytrzymać część danych szkoleniowych na potrzeby walidacji. Oznacza to, że zestaw walidacji zostanie podzielony przez zautomatyzowaną ML z `training_data` podanego początkowego. Ta wartość powinna należeć do przedziału od 0,0 do 1,0 (na przykład 0,2 oznacza, że 20% danych jest przechowywanych na potrzeby sprawdzania poprawności danych).

> [!NOTE]
> Ten `validation_size` parametr nie jest obsługiwany w scenariuszach prognozowania. 

Zobacz następujący przykład kodu:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="k-fold-cross-validation"></a>K — złożenie krzyżowego sprawdzania poprawności

Aby wykonać krzyżowe sprawdzanie poprawności, Dołącz `n_cross_validations` parametr i ustaw go na wartość. Ten parametr określa liczbę operacji sprawdzania krzyżowego do wykonania na podstawie tej samej liczby zagięć.

> [!NOTE]
> `n_cross_validations`Parametr nie jest obsługiwany w scenariuszach klasyfikacji, które używają głębokiej sieci neuronowych.
 
W poniższym kodzie zdefiniowane są pięć założenia na potrzeby krzyżowego sprawdzania poprawności. W związku z tym pięć różnych szkoleń, każde szkolenie korzystające z 4/5 danych i każde sprawdzanie poprawności przy użyciu 1/5 danych z innym wstrzymanianym zgięciem.

W związku z tym metryki są obliczane z uwzględnieniem średniej z pięciu metryk walidacji.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```
## <a name="monte-carlo-cross-validation"></a>Monte Carlo, krzyżowe sprawdzanie poprawności

Aby wykonać Monte Carlo, należy uwzględnić oba `validation_size` `n_cross_validations` Parametry w `AutoMLConfig` obiekcie. 

W przypadku Monte Carlo krzyżowego sprawdzania poprawności, zautomatyzowanej ML ustawia część danych szkoleniowych określonych przez `validation_size` parametr do walidacji, a następnie przypisuje resztę danych do szkolenia. Ten proces jest następnie powtarzany na podstawie wartości określonej w `n_cross_validations` parametrze, która generuje nowe rozbicie i sprawdzanie poprawności, losowo, za każdym razem.

> [!NOTE]
> Monte Carloe krzyżowe nie jest obsługiwane w scenariuszach prognozowania.

Poniżej znajduje się kod, 7 założenia w celu krzyżowego sprawdzania poprawności oraz 20% danych szkoleniowych do weryfikacji. W związku z tym, 7 różnych szkoleń, każde uczenie używa 80% danych, a każde sprawdzanie poprawności używa 20% danych z różnymi wstrzymaniami.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 7
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Określanie niestandardowych założenia danych krzyżowego sprawdzania poprawności

Możesz również zapewnić własne zgięcia danych krzyżowych (CV). Jest to bardziej zaawansowany scenariusz, ponieważ określasz, które kolumny należy podzielić i użyć do walidacji.  Dołącz niestandardowe kolumny OKS w danych szkoleniowych i określ, które kolumny mają być wypełniane nazwami kolumn w `cv_split_column_names` parametrze. Każda kolumna reprezentuje jeden podział między sprawdzaniem i jest wypełniana wartościami całkowitymi 1 lub 0--gdzie 1 wskazuje, że wiersz ma być używany do szkolenia i 0 wskazuje, że wiersz ma być używany do walidacji.

Poniższy fragment kodu zawiera dane marketingowe banku z dwoma kolumnami podziału OKS "CV1" i "CV2".

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Aby użyć programu `cv_split_column_names` z programem `training_data` i `label_column_name` , Uaktualnij Azure Machine Learning Python SDK w wersji 1.6.0 lub nowszej. W przypadku poprzednich wersji zestawu SDK zapoznaj się z tematem using `cv_splits_indices` , ale pamiętaj, że jest on używany `X` `y` tylko z danymi wejściowymi i zestawem danych. 


## <a name="metric-calculation-for-cross-validation-in-machine-learning"></a>Obliczanie metryki dla krzyżowego sprawdzania poprawności w usłudze Machine Learning

Gdy używana jest funkcja Monte lub Carlo krzyżowego sprawdzania poprawności, metryki są obliczane dla każdego złożenia walidacji, a następnie agregowane. Operacja agregacji jest średnią dla metryk skalarnych i sumy dla wykresów. Metryki obliczane podczas wykonywania krzyżowego walidacji są oparte na wszystkich zgięciach i dlatego wszystkich próbkach z zestawu szkoleniowego. [Dowiedz się więcej o metrykach w obszarze Automatyczne Uczenie maszynowe](how-to-understand-automated-ml.md).

Gdy jest używany niestandardowy zestaw walidacji lub automatycznie wybrany zestaw walidacji, metryki oceny modelu są obliczane tylko przez ten zestaw walidacji, a nie dane szkoleniowe.

## <a name="next-steps"></a>Następne kroki

* [Uniemożliwiaj niezrównoważone dane i przemontowanie](concept-manage-ml-pitfalls.md).
* [Samouczek: automatyczne Uczenie maszynowe do przewidywania opłat za taksówke — podział danych](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Jak [wyszkolić model prognozowania szeregów czasowych](how-to-auto-train-forecast.md).
