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
ms.date: 06/16/2020
ms.openlocfilehash: 2e26bfa484d573c0158e518b31087fb10bdcdfb9
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185686"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Konfigurowanie podziałów danych i krzyżowego sprawdzania poprawności w ramach zautomatyzowanego uczenia maszynowego

Ten artykuł zawiera informacje o różnych opcjach związanych z konfigurowaniem danych dotyczących szkoleń/weryfikacji oraz przeprowadzania krzyżowego sprawdzania poprawności dla zautomatyzowanej uczenia maszynowego, zautomatyzowanej sieci, eksperymentów.

W Azure Machine Learning, gdy używasz zautomatyzowanej ML do kompilowania wielu modeli ML, każdy przebieg podrzędny musi sprawdzać pokrewny model, obliczając metryki jakości dla tego modelu, na przykład dokładność lub AUC ważone. Te metryki są obliczane przez porównanie prognoz wykonanych z poszczególnymi modelami z rzeczywistymi etykietami z ostatnich obserwacji w danych sprawdzania poprawności. 

Automatyczne eksperymenty w MILILITRach wykonują walidację modelu automatycznie. W poniższych sekcjach opisano, jak można modyfikować ustawienia walidacji za pomocą [zestawu SDK języka Python Azure Machine Learning](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Aby zapoznać się z niską ilością kodu lub bez kodu, zobacz [Tworzenie zautomatyzowanych eksperymentów uczenia maszynowego w programie Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> W programie Studio są obecnie obsługiwane dane szkoleniowe i dotyczące sprawdzania poprawności, ale nie jest obsługiwane Określanie pojedynczych plików danych dla zestawu walidacji. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule należy

* Obszar roboczy usługi Azure Machine Learning. Aby utworzyć obszar roboczy, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* Znajomość konfigurowania zautomatyzowanego eksperymentu uczenia maszynowego przy użyciu zestawu SDK Azure Machine Learning. Postępuj zgodnie z [samouczkiem](tutorial-auto-train-models.md) lub zapoznaj się z artykułami [, aby zobaczyć](how-to-configure-auto-train.md) podstawowe wzorce projektu eksperymentu w usłudze Machine Learning.

* Zrozumienie danych związanych z pouczeniem/sprawdzaniem poprawności polega na rozdzieleniu i weryfikacji krzyżowej w formie koncepcji uczenia maszynowego. Ogólne wyjaśnienie,

    * [Informacje o szkoleniach, walidacji i zestawach testów w Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Opis krzyżowego sprawdzania poprawności w usłudze Machine Learning](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default-data-splits-and-cross-validation"></a>Domyślne podziały danych i wzajemne sprawdzanie poprawności

Użyj obiektu [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) , aby zdefiniować ustawienia eksperymentu i szkolenia. W poniższym fragmencie kodu należy zauważyć, że są zdefiniowane tylko wymagane parametry, które są parametrami dla `n_cross_validation` lub `validation_ data` **nie** są uwzględniane.

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

Jeśli nie określisz jawnie `validation_data` `n_cross_validation` parametru lub, AutoML stosuje domyślne techniki w zależności od liczby wierszy w pojedynczym zestawie danych `training_data` :

|&nbsp;Rozmiar danych szkoleniowych &nbsp;| Technika walidacji |
|---|-----|
|**Więcej &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Zastosowano podział danych szkolenia/walidacji. Wartość domyślna to przejęcie 10% początkowego zestawu danych szkoleniowych jako zestawu walidacji. Z kolei ten zestaw walidacji jest używany do obliczania metryk.
|**Mniejsze &nbsp; niż &nbsp; 20 000 &nbsp; wierszy**| Stosowana jest metoda weryfikacji krzyżowej. Domyślna liczba zagięć zależy od liczby wierszy. <br> **Jeśli zestaw danych jest mniejszy niż 1 000 wierszy**, używane są 10 zagięć. <br> **Jeśli wiersze są z zakresu od 1 000 do 20 000**, używane są trzy składowe.

## <a name="provide-validation-data"></a>Podaj dane weryfikacji

W takim przypadku można rozpocząć od pojedynczego pliku danych i podzielić go na zestawy szkoleniowe i weryfikacyjne lub udostępnić osobny plik danych dla zestawu walidacji. W obu przypadkach `validation_data` parametr w `AutoMLConfig` obiekcie przypisuje dane, które mają być używane jako zestaw walidacji. Ten parametr akceptuje tylko zestawy danych w postaci [Azure Machine Learning DataSet](how-to-create-register-datasets.md) lub Pandas Dataframe.   

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

W takim przypadku dla eksperymentu jest dostarczany tylko jeden zestaw danych. Oznacza to, że `validation_data` parametr **nie** jest określony, a podany zestaw danych jest przypisany do  `training_data` parametru.  W `AutoMLConfig` obiekcie można ustawić `validation_size` parametr, aby wytrzymać część danych szkoleniowych na potrzeby walidacji. Oznacza to, że zestaw walidacji zostanie podzielony przez AutoML z `training_data` podanego początkowego. Ta wartość powinna należeć do przedziału od 0,0 do 1,0 (na przykład 0,2 oznacza, że 20% danych jest przechowywanych na potrzeby sprawdzania poprawności danych).

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

## <a name="set-the-number-of-cross-validations"></a>Ustaw liczbę operacji krzyżowych

Aby wykonać weryfikację krzyżową, Dołącz `n_cross_validations` parametr i ustaw go na wartość. Ten parametr określa liczbę operacji sprawdzania krzyżowego do wykonania na podstawie tej samej liczby zagięć.

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

## <a name="next-steps"></a>Następne kroki

* [Uniemożliwiaj niezrównoważone dane i przemontowanie](concept-manage-ml-pitfalls.md).
* [Samouczek: automatyczne Uczenie maszynowe do przewidywania opłat za taksówke — podział danych](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Jak [wyszkolić model prognozowania szeregów czasowych](how-to-auto-train-forecast.md).