---
title: Przechowywanie wersji zestawu danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak w wersji Machine Learning zestawy danych i jak działają wersje przy użyciu potoków uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: d72d2d094e220bd4e460cfca6b422f0609c083af
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880071"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>Wersja i śledzenie Azure Machine Learning zestawy danych

Ten artykuł zawiera informacje na temat wersji i śledzenia Azure Machine Learning zestawów danych w celu uzyskania odtwarzalności. Wersja zestawu danych to sposób zakładania stanu danych, aby można było zastosować określoną wersję zestawu danych do przyszłych eksperymentów.

Typowe scenariusze obsługi wersji:

* Gdy nowe dane są dostępne do ponownego szkolenia
* W przypadku stosowania różnych metod przygotowywania lub opracowywania funkcji

## <a name="prerequisites"></a>Wymagania wstępne

W celu skorzystania z tego samouczka potrzebne są następujące elementy:

- [Azure Machine Learning zestawu SDK dla języka Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). Ten zestaw SDK zawiera pakiet [Azure-DataSets](/python/api/azureml-core/azureml.core.dataset?preserve-view=true&view=azure-ml-py) .
    
- [Obszar roboczy Azure Machine Learning](concept-workspace.md). Pobierz istniejący kod lub [Utwórz nowy obszar roboczy](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Zestaw danych Azure Machine Learning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Rejestrowanie i pobieranie wersji zestawu danych

Rejestrując zestaw danych, można w wersji, ponownie użyć i udostępnić go w eksperymentach i współpracownikom. Można zarejestrować wiele zestawów danych w tej samej nazwie i pobrać określoną wersję według nazwy i numeru wersji.

### <a name="register-a-dataset-version"></a>Rejestrowanie wersji zestawu danych

Poniższy kod rejestruje nową wersję `titanic_ds` zestawu danych przez ustawienie `create_new_version` parametru na `True` . Jeśli nie ma istniejącego `titanic_ds` zestawu danych zarejestrowanego w obszarze roboczym, kod utworzy nowy zestaw danych o nazwie `titanic_ds` i ustawi jego wersję na 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Pobieranie zestawu danych według nazwy

Domyślnie metoda [get_by_name ()](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) `Dataset` klasy zwraca najnowszą wersję zestawu danych zarejestrowanego w obszarze roboczym. 

Poniższy kod pobiera wersję 1 `titanic_ds` zestawu danych.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Najlepsze rozwiązanie w zakresie wersji

Podczas tworzenia wersji zestawu danych *nie* jest tworzona dodatkowa kopia danych z obszarem roboczym. Ze względu na to, że zestawy danych są odwołaniami do dane w usłudze Storage, masz pojedyncze Źródło prawdy, zarządzane przez usługę magazynu.

>[!IMPORTANT]
> Jeśli dane, do których odwołuje się zestaw danych, zostaną zastąpione lub usunięte, wywołanie określonej wersji zestawu *danych nie powoduje przywrócenia zmiany* .

Podczas ładowania danych z zestawu danych bieżąca zawartość danych, do której odwołuje się zestaw danych, jest zawsze ładowana. Jeśli chcesz upewnić się, że każda wersja zestawu danych jest powtarzalna, zalecamy, aby nie modyfikować zawartości danych, do której odwołuje się wersja zestawu danych. Po dojściu do nowych danych Zapisz nowe pliki danych w osobnym folderze danych, a następnie utwórz nową wersję zestawu danych, aby dołączyć dane z tego nowego folderu.

Na poniższym obrazie i przykładowym kodzie przedstawiono zalecany sposób struktury folderów danych i tworzenia wersji zestawu danych, które odwołują się do tych folderów:

![Struktura folderów](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>Wersja zestawu danych wyjściowych potoku ML

Zestawu danych można użyć jako danych wejściowych i wyjściowych poszczególnych etapów [potoku](concept-ml-pipelines.md) . Po ponownym uruchomieniu potoków dane wyjściowe poszczególnych etapów potoku są rejestrowane jako nowa wersja zestawu danych.

Potoki ML wypełniają dane wyjściowe każdego kroku do nowego folderu przy każdym uruchomieniu potoku. Takie zachowanie umożliwia przywrócenie zestawów danych wyjściowych z wersjami. Dowiedz się więcej o [zestawach danych w potokach](./how-to-create-machine-learning-pipelines.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>Śledź dane w eksperymentach

Azure Machine Learning śledzi dane w trakcie eksperymentu jako wejściowe i wyjściowe zestawy danych.  

Poniżej przedstawiono scenariusze, w których dane są śledzone jako **wejściowy zestaw danych**. 

* Jako `DatasetConsumptionConfig` obiekt za pomocą `inputs` albo parametru lub `arguments` `ScriptRunConfig` obiektu podczas przesyłania eksperymentu. 

* Gdy metody takie jak, get_by_name () lub get_by_id () są wywoływane w skrypcie. W tym scenariuszu nazwa przypisana do zestawu danych, gdy zarejestrowano go w obszarze roboczym, jest nazwą wyświetlaną. 

Poniżej przedstawiono scenariusze, w których dane są śledzone jako **wyjściowy zestaw danych**.  

* Przekaż `OutputFileDatasetConfig` obiekt za pomocą `outputs` albo `arguments` parametru podczas przesyłania eksperymentu. `OutputFileDatasetConfig` obiekty mogą być również używane do utrwalania danych między etapami potoku. Zobacz [przenoszenie danych między etapami potoku.](how-to-move-data-in-out-of-pipelines.md)
  
* Zarejestruj zestaw danych w skrypcie. W tym scenariuszu nazwa przypisana do zestawu danych, gdy zarejestrowano go w obszarze roboczym, jest nazwą wyświetlaną. W poniższym przykładzie `training_ds` jest nazwą, która ma być wyświetlana.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Prześlij podrzędny przebieg z niezarejestrowanego zestawu danych w skrypcie. Powoduje to anonimowy, zapisany zestaw danych.

### <a name="trace-datasets-in-experiment-runs"></a>Śledzenie zestawów danych w przebiegach eksperymentu

Dla każdego Machine Learning eksperymentu można łatwo śledzić zestawy danych używane jako dane wejściowe z `Run` obiektem eksperymentu.

Poniższy kod używa [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) metody do śledzenia, które wejściowe zestawy danych zostały użyte z przebiegiem eksperymentu:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Eksperymenty można również znaleźć za `input_datasets` pomocą programu [Azure Machine Learning Studio](). 

Na poniższej ilustracji przedstawiono, gdzie znaleźć zestaw danych wejściowych eksperymentu w programie Azure Machine Learning Studio. Na potrzeby tego przykładu przejdź do okienka **eksperymenty** i Otwórz kartę **Właściwości** dla określonego uruchomienia eksperymentu `keras-mnist` .

![Wejściowe zestawy danych](./media/how-to-version-track-datasets/input-datasets.png)

Użyj następującego kodu, aby zarejestrować modele z zestawami danych:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Po zarejestrowaniu można zobaczyć listę modeli zarejestrowanych w zestawie danych przy użyciu języka Python lub przejdź do [Studio](https://ml.azure.com/).

Poniższy widok pochodzi z okienka **zestawy danych** w obszarze **zasoby**. Wybierz zestaw danych, a następnie wybierz kartę **modele** , aby wyświetlić listę modeli zarejestrowanych z zestawem danych. 

![Modele wejściowych zestawów danych](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Następne kroki

* [Szkolenie przy użyciu zestawów danych](how-to-train-with-datasets.md)
* [Więcej przykładowych notesów zestawu danych](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)