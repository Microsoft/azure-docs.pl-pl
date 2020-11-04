---
title: Użyj zautomatyzowanej ML w potokach ML
titleSuffix: Azure Machine Learning
description: AutoMLStep umożliwia korzystanie z zautomatyzowanej uczenia maszynowego w potokach.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 08/26/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 9cde7fe32d1b7b13c5f95bf3d99497926f68c88e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311197"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Korzystanie z zautomatyzowanej tablicy w potoku Azure Machine Learning w języku Python


Funkcja zautomatyzowanej ML Azure Machine Learning umożliwia odnajdywanie modeli o wysokiej wydajności bez konieczności ponownego wdrażania każdej możliwej metody. W połączeniu z potokami Azure Machine Learning można tworzyć możliwe do wdrożenia przepływy pracy, które mogą szybko wykryć algorytm, który najlepiej sprawdza się w przypadku danych. W tym artykule pokazano, jak efektywnie dołączać krok przygotowania danych do zautomatyzowanego kroku ML. Automatycznej ML mogą szybko wykrywać algorytm, który najlepiej sprawdza się w przypadku danych, jednocześnie zapewniając drogę do MLOps i model cyklu życia operacjonalizacji z potokami.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Obszar roboczy usługi Azure Machine Learning. Zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).  

* Zapoznaj się z [automatycznymi usługami uczenia maszynowego](concept-automated-ml.md) i uczenia [maszynowego](concept-ml-pipelines.md) platformy Azure.

## <a name="review-automated-mls-central-classes"></a>Przegląd klas centralnych o zautomatyzowanej ML

Automatyczna tablica w potoku jest reprezentowana przez `AutoMLStep` obiekt. `AutoMLStep`Klasa jest podklasą `PipelineStep` . Wykres `PipelineStep` obiektów definiuje `Pipeline` .

Istnieje kilka podklas `PipelineStep` . Oprócz programu, w `AutoMLStep` tym artykule przedstawiono `PythonScriptStep` przygotowanie do przygotowania danych i inne do zarejestrowania modelu.

Preferowanym sposobem na wstępne przeniesienie danych _do_ potoku jest z `Dataset` obiektami. Aby przenieść dane _między_ krokami, preferowanym sposobem jest z `PipelineData` obiektami. Aby można było używać `AutoMLStep` go z, `PipelineData` obiekt musi być przekształcony w `PipelineOutputTabularDataset` obiekt. Aby uzyskać więcej informacji, zobacz [dane wejściowe i wyjściowe z potoków ml](how-to-move-data-in-out-of-pipelines.md).


> [!TIP]
> Udoskonalone środowisko przekazywania danych tymczasowych między etapami potoku jest dostępne w publicznych wersjach zapoznawczych  [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) i [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py) .  Te klasy to [eksperymentalne](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) funkcje w wersji zapoznawczej i mogą ulec zmianie w dowolnym momencie.

`AutoMLStep`Konfiguracja jest konfigurowana za pośrednictwem `AutoMLConfig` obiektu. `AutoMLConfig` jest elastyczną klasą, jak opisano w temacie [Konfigurowanie zautomatyzowanych eksperymentów ml w języku Python](./how-to-configure-auto-train.md#configure-your-experiment-settings). 

`Pipeline`Działa w `Experiment` . Potok `Run` ma dla każdego kroku element podrzędny `StepRun` . Dane wyjściowe zautomatyzowanej części ML `StepRun` to metryki szkoleniowe i model o najwyższej wydajności.

W celu utworzenia konkretnych elementów ten artykuł tworzy prosty potok dla zadania klasyfikacji. Zadanie jest przewidywane przewidywalność Titanic, ale nie będziemy omawiać danych ani zadań poza przekazywaniem.

## <a name="get-started"></a>Wprowadzenie

### <a name="retrieve-initial-dataset"></a>Pobierz początkowy zestaw danych

Często przepływ pracy w usłudze ML zaczyna się od istniejących danych linii bazowej. Jest to dobry scenariusz dla zarejestrowanego zestawu danych. Zestawy danych są widoczne w obszarze roboczym, obsługują wersje i mogą być interaktywnie zbadane. Istnieje wiele sposobów tworzenia i wypełniania zestawu danych, zgodnie z opisem w temacie [create Azure Machine Learning DataSets](how-to-create-register-datasets.md). Ponieważ będziemy używać zestawu SDK języka Python do utworzenia naszego potoku, użyj zestawu SDK, aby pobrać dane linii bazowej i zarejestrować go przy użyciu nazwy "titanic_ds".

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

Kod najpierw loguje się do obszaru roboczego Azure Machine Learning zdefiniowanego w **config.jsna** (Aby uzyskać wyjaśnienie, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace). Jeśli nie istnieje jeszcze zestaw danych o nazwie `'titanic_ds'` zarejestrowane, zostanie on utworzony. Kod pobiera dane CSV z sieci Web, używa ich do tworzenia wystąpienia a `TabularDataset` , a następnie rejestruje zestaw danych z obszarem roboczym. Na koniec funkcja `Dataset.get_by_name()` przypisuje `Dataset` do `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>Konfigurowanie magazynu i celu obliczeń

Dodatkowe zasoby, które będą potrzebne do potoku, to magazyn i, ogólnie Azure Machine Learning zasoby obliczeniowe. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Pośrednie dane między przygotowaniem danych i etapem zautomatyzowanej ML mogą być przechowywane w domyślnym magazynie elementów roboczych obszaru roboczego, więc nie trzeba wykonywać więcej niż wywołania `get_default_datastore()` na `Workspace` obiekcie. 

Następnie kod sprawdza, czy element docelowy obliczeń AML `'cpu-cluster'` już istnieje. Jeśli nie, firma Microsoft określa, że chcemy użyć małych procesorów opartych na procesorach. Jeśli planujesz korzystanie z funkcji uczenia głębokiego (na przykład cechowania text with DNN Support), należy wybrać obliczenia o silnej obsłudze procesora GPU, zgodnie z opisem w obszarze [rozmiary maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU](../virtual-machines/sizes-gpu.md). 

Kod jest blokowany do momentu zainicjowania obsługi administracyjnej, a następnie drukuje pewne szczegóły właśnie utworzonego elementu docelowego obliczeń. Na koniec nazwanego elementu docelowego obliczeń jest pobierana z obszaru roboczego i przypisana do `compute_target` . 

### <a name="configure-the-training-run"></a>Konfigurowanie przebiegu szkoleniowego

Następnym krokiem jest upewnienie się, że uruchomienie szkolenia zdalnego ma wszystkie zależności wymagane przez kroki szkoleniowe. Zależności i kontekst środowiska uruchomieniowego są ustawiane przez utworzenie i skonfigurowanie `RunConfiguration` obiektu. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk[automl]', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

W powyższym kodzie przedstawiono dwie opcje obsługi zależności. Jak przedstawiono w programie `USE_CURATED_ENV = True` , konfiguracja jest oparta na środowisku nadzorowanym. Środowiska nadzorowane są "prebaked" z typowymi bibliotekami zależnymi i mogą być znacznie szybsze do przełączenia do trybu online. Środowiska nadzorowane zawierają wstępnie skompilowane obrazy platformy Docker w [programie Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner). Ścieżka wykonana, jeśli zmieni `USE_CURATED_ENV` się, aby `False` wyświetlić wzorzec jawnego ustawiania zależności. W tym scenariuszu nowy niestandardowy obraz platformy Docker zostanie utworzony i zarejestrowany w Azure Container Registry w ramach grupy zasobów (zobacz [wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure](../container-registry/container-registry-intro.md)). Kompilowanie i rejestrowanie tego obrazu może potrwać kilka minut. 

## <a name="prepare-data-for-automated-machine-learning"></a>Przygotowywanie danych do automatycznego uczenia maszynowego

### <a name="write-the-data-preparation-code"></a>Napisz kod przygotowywania danych

Zestaw danych bazowych Titanic składa się z danych liczbowych i tekstowych, z których brakuje niektórych wartości. Aby przygotować go do automatycznego uczenia maszynowego, krok potoku przygotowywania danych będzie:

- Wypełnij brakujące dane danymi losowymi lub z kategorią odpowiadającą "Nieznane"
- Przekształcanie danych kategorii na liczby całkowite
- Upuść kolumny, których nie zamierzasz używać
- Podziel dane na zestawy szkoleniowe i testowe
- Zapisz przekształcone dane w jeden z nich.
    - `PipelineData` ścieżki wyjściowe

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

Powyższy fragment kodu stanowi kompletny, ale minimalny przykład przygotowania danych dla danych Titanic. Fragment kodu rozpoczyna się od Jupyter "Magic polecenie", aby wyprowadzić kod do pliku. Jeśli nie używasz notesu Jupyter, Usuń ten wiersz i Utwórz plik ręcznie.

Różne `prepare_` funkcje w powyższym fragmencie kodu modyfikują odpowiednią kolumnę w wejściowym zestawie danych. Te funkcje działają na danych po zmianie do `DataFrame` obiektu Pandas. W każdym przypadku brakujące dane są wypełniane za pomocą reprezentatywnych danych losowych lub kategorii danych wskazujących "nieznany". Dane kategorii oparte na tekście są mapowane na liczby całkowite. Kolumny, które nie są już potrzebne, są zastępowane lub usuwane. 

Po zdefiniowaniu przez kod funkcji przygotowywania danych kod analizuje argument wejściowy, który jest ścieżką, do której chcemy napisać dane. (Te wartości zostaną określone przez `PipelineData` obiekty, które zostaną omówione w następnym kroku). Kod pobiera zarejestrowane `'titanic_cs'` `Dataset` , konwertuje go na Pandas `DataFrame` i wywołuje różne funkcje przygotowywania danych. 

Ponieważ `output_path` jest w pełni kwalifikowana, funkcja służy `os.makedirs()` do przygotowywania struktury katalogów. W tym momencie można użyć, `DataFrame.to_csv()` Aby napisać dane wyjściowe, ale pliki Parquet są bardziej wydajne. Ta wydajność prawdopodobnie nie jest istotna dla tego małego zestawu danych, ale korzystanie **PyArrow** z pakietu PyArrow `from_pandas()` i `write_table()` funkcji to tylko kilka naciśnięć klawiszy niż `to_csv()` .

Pliki Parquet są natywnie obsługiwane przez krok zautomatyzowanej ML opisany poniżej, więc nie jest wymagane żadne specjalne przetwarzanie do ich użycia. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Napisz krok potoku przygotowywania danych ( `PythonScriptStep` )

Kod przygotowywania danych opisany powyżej musi być skojarzony z `PythonScripStep` obiektem, który ma być używany z potokiem. Ścieżka, do której jest zapisywana dane wyjściowe przygotowywania danych Parquet, jest generowana przez `PipelineData` obiekt. Przygotowane wcześniej zasoby, takie jak,, `ComputeTarget` `RunConfig` i `'titanic_ds' Dataset` są używane do wykonania specyfikacji.

PipelineData użytkownicy
```python
from azureml.pipeline.core import PipelineData

from azureml.pipeline.steps import PythonScriptStep
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```
`prepped_data_path`Obiekt jest typu `PipelineOutputFileDataset` . Należy zauważyć, że jest on określony zarówno `arguments` w `outputs` argumentach, jak i. W przypadku przejrzenia poprzedniego kroku zobaczysz, że w kodzie przygotowywania danych wartość argumentu `'--output_path'` jest ścieżką pliku, do której zapisano plik Parquet. 

> [!TIP]
> Udoskonalone środowisko przekazywania pośrednich danych między etapami potoku jest dostępne z publiczną klasą zapoznawczą [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) . Aby zapoznać się z przykładem kodu przy użyciu `OutputFileDatasetConfig` klasy, zobacz jak [utworzyć dwa krokowe potoku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="train-with-automlstep"></a>Uczenie z AutoMLStep

Konfigurowanie zautomatyzowanego kroku potoku ML jest wykonywane z `AutoMLConfig` klasą. Ta elastyczna Klasa została opisana w artykule [Konfigurowanie zautomatyzowanych eksperymentów ml w języku Python](./how-to-configure-auto-train.md). Dane wejściowe i wyjściowe danych są jedynymi aspektami konfiguracji, które wymagają specjalnej uwagi w potoku. Dane wejściowe i wyjściowe dla `AutoMLConfig` potoków zostały omówione szczegółowo poniżej. Poza danymi, zaletą potoków ML jest możliwość używania różnych elementów docelowych obliczeń dla różnych kroków. Możesz wybrać bardziej zaawansowane `ComputeTarget` tylko dla procesu zautomatyzowanej sieci. Takie działanie jest tak proste jak przypisanie `RunConfiguration` do parametru obiektu bardziej zaawansowanego `AutoMLConfig` `run_configuration` .

### <a name="send-data-to-automlstep"></a>Wyślij dane do `AutoMLStep`

W potoku ML dane wejściowe muszą być `Dataset` obiektem. Najwyższą z nich jest dostarczenie danych wejściowych w postaci `PipelineOutputTabularDataset` obiektów. Tworzysz obiekt tego typu z `parse_parquet_files()` lub `parse_delimited_files()` na a `PipelineOutputFileDataset` , taki jak `prepped_data_path` obiekt.

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

Poniższy fragment kodu tworzy wysoką wydajność na `PipelineOutputTabularDataset` podstawie `PipelineOutputFileDataset` danych wyjściowych kroku przygotowywania danych.

> [!TIP]
> Klasa publicznej wersji zapoznawczej [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) zawiera metodę [read_delimited_files ()](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py#&preserve-view=trueread-delimited-files-include-path-false--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none--path-glob-none--set-column-types-none-) , która konwertuje `OutputFileDatasetConfig` do na wartość w [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py) celu użycia w AutoML uruchomieniach.

Innym rozwiązaniem jest użycie `Dataset` obiektów zarejestrowanych w obszarze roboczym:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Porównanie dwóch technik:

| Technika | Korzyści i wady | 
|-|-|
|`PipelineOutputTabularDataset`| Wyższa wydajność | 
|| Naturalna trasa z `PipelineData` | 
|| Dane nie są utrwalane po uruchomieniu potoku |
|| [Notes pokazujący `PipelineOutputTabularDataset` technikę](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Użytkownikiem `Dataset` | Niższa wydajność |
| | Można generować na wiele sposobów | 
| | Dane utrwalają się i są widoczne w całym obszarze roboczym |
| | [Notes przedstawiający zarejestrowaną `Dataset` technikę](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>Określ zautomatyzowane dane wyjściowe ML

Wyniki `AutoMLStep` są końcowymi wynikami metryk modelu wyższego poziomu i samym modelem. Aby użyć tych danych wyjściowych w dalszych krokach potoku, przygotuj `PipelineData` obiekty do odebrania.

```python

from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

Poniższy fragment kodu tworzy dwa `PipelineData` obiekty dla danych wyjściowych metryk i modelu. Każda z nich jest nazywana, przypisywana do domyślnego magazynu danych pobranego wcześniej i skojarzona z określonym z `type` `TrainingOutput` `AutoMLStep` . Ponieważ przypiszemy `pipeline_output_name` do tych `PipelineData` obiektów, ich wartości będą dostępne nie tylko z poszczególnych etapów potoku, ale z potoku jako całości, jak zostanie omówione w sekcji "sprawdzanie wyników potoku". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Skonfiguruj i Utwórz zautomatyzowany krok potoku

Po zdefiniowaniu danych wejściowych i wyjściowych czas tworzenia `AutoMLConfig` i `AutoMLStep` . Szczegóły konfiguracji będą zależeć od zadania, zgodnie z opisem w temacie [Konfigurowanie zautomatyzowanych eksperymentów ml w języku Python](./how-to-configure-auto-train.md). W przypadku zadania klasyfikacji przeżycia Titanic w poniższym fragmencie kodu pokazano prostą konfigurację.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
Fragment kodu przedstawia idiom często używany z `AutoMLConfig` . Argumenty, które są bardziej płynne (parametr-ish), są określane w osobnym słowniku, podczas gdy wartości mniejsze od zmiany są określane bezpośrednio w `AutoMLConfig` konstruktorze. W takim przypadku należy `automl_settings` określić krótki przebieg: przebieg zostanie zatrzymany po upływie 2 iteracji lub 15 minut, zależnie od tego, co nastąpi wcześniej.

`automl_settings`Słownik jest przesyłany do `AutoMLConfig` konstruktora jako kwargs. Inne parametry nie są złożone:

- `task` jest ustawiony na `classification` dla tego przykładu. Inne prawidłowe wartości to `regression` i `forecasting`
- `path` i `debug_log` opisz ścieżkę do projektu i plik lokalny, do którego zostaną nadane informacje debugowania. 
- `compute_target` jest to wcześniej zdefiniowane, `compute_target` które w tym przykładzie jest niedrogią maszyną opartą na procesorze. Jeśli korzystasz z usług uczenia głębokiego AutoML, chcesz zmienić cel obliczeń na oparty na procesorach GPU
- `featurization` jest ustawiony na `auto` . Więcej szczegółów znajduje się w sekcji [Data cechowania](./how-to-configure-auto-train.md#data-featurization) w dokumencie zautomatyzowanej konfiguracji ml. 
- `label_column_name` wskazuje, która kolumna chcemy przewidzieć 
- `training_data` jest ustawiony na `PipelineOutputTabularDataset` obiekty wykonane z danych wyjściowych kroku przygotowywania 

`AutoMLStep`Samo przyjmuje `AutoMLConfig` i ma jako `PipelineData` dane wyjściowe obiekty utworzone w celu przechowywania metryk i danych modelu. 

>[!Important]
> Należy ustawić `enable_default_model_output` i na wartość tylko wtedy, `enable_default_metrics_output` `True` gdy jest używany program  `AutoMLStepRun` .

W tym przykładzie zautomatyzowany proces ML wykona walidację na `training_data` . Można kontrolować liczbę operacji sprawdzania krzyżowego z `n_cross_validations` argumentem. Jeśli dane szkoleniowe zostały już podzielone w ramach kroków przygotowywania danych, można ustawić `validation_data` własne ustawienia `Dataset` .

Czasami może być widoczne użycie `X` funkcji danych i `y` etykiet danych. Ta technika jest przestarzała i powinna być używana `training_data` do wprowadzania danych. 

## <a name="register-the-model-generated-by-automated-ml"></a>Zarejestruj model wygenerowany przez zautomatyzowaną ML 

Ostatnim krokiem w prostym potoku jest zarejestrowanie utworzonego modelu. Po dodaniu modelu do rejestru modelu obszaru roboczego będzie on dostępny w portalu i może być w wersji. Aby zarejestrować model, napisz inny, `PythonScriptStep` który pobiera `model_data` dane wyjściowe `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>Napisz kod, aby zarejestrować model

Model jest zarejestrowany w `Workspace` . Prawdopodobnie wiesz już `Workspace.from_config()` , jak używać do logowania się do obszaru roboczego na komputerze lokalnym, ale istnieje inny sposób na uzyskanie obszaru roboczego z działającego potoku. `Run.get_context()`Pobiera aktywny `Run` . Ten `run` obiekt umożliwia dostęp do wielu ważnych obiektów, w tym `Workspace` użytych tutaj.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>Napisz kod PythonScriptStep

Model — Rejestracja `PythonScriptStep` używa `PipelineParameter` dla jednego z argumentów. Parametry potoku są argumentami potoków, które można łatwo ustawić w czasie wykonywania. Po zadeklarowaniu są one przenoszone jako argumenty normalne. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Utwórz i uruchom zautomatyzowany potok ML

Tworzenie i uruchamianie potoku, który zawiera element, `AutoMLStep` nie różni się od normalnego potoku. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

Powyższy kod łączy Przygotowywanie danych, automatyczne ML i rejestrowanie modeli w `Pipeline` obiekcie. Następnie tworzy `Experiment` obiekt. `Experiment`Konstruktor pobierze nazwany eksperyment, jeśli istnieje, lub utwórz go w razie potrzeby. Przesyła `Pipeline` do `Experiment` , tworząc `Run` obiekt, który będzie asynchronicznie uruchamiać potok. `wait_for_completion()`Funkcja jest blokowana do momentu zakończenia przebiegu.

### <a name="examine-pipeline-results"></a>Sprawdzanie wyników potoku 

Po `run` zakończeniu można pobrać `PipelineData` obiekty, które zostały przypisane `pipeline_output_name` . Możesz pobrać wyniki i załadować je do dalszej obróbki.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Pobrane pliki są zapisywane w podkatalogu `azureml/{run.id}/` . Plik metryk jest sformatowany w formacie JSON i może być konwertowany do Pandas Dataframe na potrzeby badania.

W przypadku przetwarzania lokalnego może być konieczne zainstalowanie odpowiednich pakietów, takich jak Pandas, pakietu pickle, zestaw SDK platformy Azure i tak dalej. W tym przykładzie najprawdopodobniej najlepszym modelem znalezionym przez zautomatyzowane ML będzie zależeć od XGBoost.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

W powyższym fragmencie kodu przedstawiono ładowany plik metryk z lokalizacji w magazynie danych platformy Azure. Możesz również załadować ją z pobranego pliku, jak pokazano w komentarzu. Po ukończeniu deserializacji i przekonwertowaniu go do Pandas Dataframe można zobaczyć szczegółowe metryki dla każdej iteracji zautomatyzowanego kroku ML.

Plik modelu można zdeserializować do `Model` obiektu, którego można użyć do inferencing, dalszej analizy metryk i tak dalej. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Aby uzyskać więcej informacji na temat ładowania i pracy z istniejącymi modelami, zobacz [Korzystanie z istniejącego modelu z Azure Machine Learning](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Pobierz wyniki zautomatyzowanego przebiegu ML

Jeśli po wykonaniu tych czynności zamieszczono w artykule, masz obiekt z utworzonymi wystąpieniami `run` . Można jednak również pobrać ukończone `Run` obiekty z `Workspace` metody za pomocą `Experiment` obiektu.

Obszar roboczy zawiera pełny rekord wszystkich eksperymentów i przebiegów. Możesz użyć portalu, aby znaleźć i pobrać dane wyjściowe eksperymentów lub użyć kodu. Aby uzyskać dostęp do rekordów z przebiegu historycznego, użyj Azure Machine Learning, aby znaleźć identyfikator przebiegu, w którym interesują Cię zainteresowania. Przy użyciu tego identyfikatora można wybrać odpowiednie opcje w `run` obszarze `Workspace` i `Experiment` .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Należy zmienić ciągi w powyższym kodzie na charakterystykę przebiegu historycznego. W powyższym fragmencie kodu przyjęto założenie, że użytkownik jest przypisany `ws` do odpowiednich `Workspace` z normalną `from_config()` . Eksperyment jest pobierany bezpośrednio, a następnie kod znajdzie `Run` interesu, dopasowując `run.id` wartość.

Po utworzeniu `Run` obiektu można pobrać metryki i model. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Każdy `Run` obiekt zawiera `StepRun` obiekty, które zawierają informacje o przebiegu poszczególnych etapów potoku. `run`Jest wyszukiwany `StepRun` obiekt dla elementu `AutoMLStep` . Metryki i modele są pobierane przy użyciu ich domyślnych nazw, które są dostępne, nawet jeśli nie przechodzą `PipelineData` obiektów do `outputs` parametru `AutoMLStep` . 

Na koniec rzeczywiste metryki i modele są pobierane na maszynę lokalną, jak zostało to omówione w sekcji "Zbadaj wyniki potoku" powyżej.

## <a name="next-steps"></a>Następne kroki

- Uruchom ten Notes Jupyter z [pełnym przykładem zautomatyzowanej części ml w potoku](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) , który używa regresji do przewidywania opłat za taksówkę
- [Twórz zautomatyzowane eksperymenty ML bez pisania kodu](how-to-use-automated-ml-for-ml-models.md)
- Poznaj różne [notesy Jupyter pokazujące zautomatyzowaną ml](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Zapoznaj się z tematem integrowania potoku w celu uzyskania [kompleksowej MLOps](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle) lub zbadania [repozytorium GitHub MLOps](https://github.com/Microsoft/MLOpspython)