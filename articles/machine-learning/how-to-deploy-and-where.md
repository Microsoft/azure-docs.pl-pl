---
title: Jak i gdzie wdrażać modele
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak i gdzie wdrażać modele Azure Machine Learning, w tym Azure Container Instances, Kubernetes usługi Azure, Azure IoT Edge i programowalne pola tablic bram.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.custom: seoapril2019, tracking-python
ms.openlocfilehash: bc9ab6ddf3a9032fd1919b70d830f0d65cdc06ed
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817976"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Wdrażanie modeli za pomocą usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak wdrożyć model uczenia maszynowego jako usługę sieci Web w chmurze platformy Azure lub Azure IoT Edge urządzeń.

Przepływ pracy jest podobny do tego, [gdzie wdrażasz](#target) model:

1. Zarejestrowanie modelu.
1. Przygotowanie do wdrożenia. (Określenie zasobów, użycia, docelowego obiektu obliczeniowego).
1. Wdrożenie modelu na docelowym obiekcie obliczeniowym.
1. Przetestuj wdrożony model, nazywany również usługą sieci Web.

Aby uzyskać więcej informacji na temat pojęć związanych z przepływem pracy wdrożenia, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- Model. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](https://aka.ms/azml-deploy-cloud).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning](reference-azure-machine-learning-cli.md), [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Poniższy kod przedstawia sposób nawiązywania połączenia z obszarem roboczym Azure Machine Learning przy użyciu informacji w pamięci podręcznej w lokalnym środowisku programistycznym:

+ **Używanie zestawu SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Aby uzyskać więcej informacji na temat używania zestawu SDK do łączenia się z obszarem roboczym, zobacz dokumentację [zestawu Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Korzystanie z interfejsu wiersza polecenia**

   Korzystając z interfejsu wiersza polecenia, należy `-w` użyć `--workspace-name` parametru lub, aby określić obszar roboczy dla poleceń.

+ **Korzystanie z narzędzia Visual Studio Code**

   Gdy używasz Visual Studio Code, wybierz obszar roboczy przy użyciu interfejsu graficznego. Aby uzyskać więcej informacji, zobacz [Wdrażanie modeli i zarządzanie nimi](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) w dokumentacji rozszerzenia Visual Studio Code.

## <a name="register-your-model"></a><a id="registermodel"></a>Zarejestruj model

Zarejestrowany model to logiczny kontener dla co najmniej jednego pliku, który składa się z modelu. Na przykład jeśli masz model, który jest przechowywany w wielu plikach, możesz zarejestrować je jako jeden model w obszarze roboczym. Po zarejestrowaniu plików można pobrać lub wdrożyć zarejestrowany model i odebrać wszystkie zarejestrowane pliki.

> [!TIP]
> Po zarejestrowaniu modelu podajesz ścieżkę do lokalizacji w chmurze (z przebiegu szkoleniowego) lub katalogu lokalnego. Ta ścieżka służy tylko do lokalizowania plików do przekazania w ramach procesu rejestracji. Nie musi być zgodna ze ścieżką używaną w skrypcie wprowadzania. Aby uzyskać więcej informacji, zobacz [Lokalizowanie plików modelu w skrypcie wprowadzania](#load-model-files-in-your-entry-script).

Modele uczenia maszynowego są rejestrowane w obszarze roboczym Azure Machine Learning. Model może pochodzić z Azure Machine Learning lub w innym miejscu. Podczas rejestrowania modelu można opcjonalnie dostarczyć metadane dotyczące modelu. `tags` `properties` Do filtrowania modeli można używać słowników i, które są stosowane do rejestracji modelu.

W poniższych przykładach pokazano, jak zarejestrować model.

### <a name="register-a-model-from-an-experiment-run"></a>Rejestrowanie modelu na podstawie przebiegu eksperymentu

Fragmenty kodu w tej sekcji pokazują, jak zarejestrować model z poziomu przebiegu szkoleniowego:

> [!IMPORTANT]
> Aby móc korzystać z tych fragmentów, musisz wcześniej wykonać przebieg szkolenia i musisz mieć dostęp do `Run` obiektu (przykład SDK) lub wartość identyfikatora uruchomienia (przykład interfejsu wiersza polecenia). Aby uzyskać więcej informacji na temat modeli szkoleniowych, zobacz [set up COMPUTE targets for model Training](how-to-set-up-training-targets.md).

+ **Używanie zestawu SDK**

  Gdy korzystasz z zestawu SDK do uczenia modelu, możesz otrzymać obiekt [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) lub obiekt [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) , w zależności od tego, jak został przeszkolony model. Każdy obiekt może służyć do rejestrowania modelu utworzonego przez uruchomienie eksperymentu.

  + Zarejestruj model na podstawie `azureml.core.Run` obiektu:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `model_path` ścieżkę folderu zawierającego pliki. Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [uruchamiania. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Zarejestruj model na podstawie `azureml.train.automl.run.AutoMLRun` obiektu:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    W tym przykładzie `metric` `iteration` Parametry i nie są określone, więc iteracja z najlepszą metryką podstawową zostanie zarejestrowana. `model_id`Wartość zwracana z przebiegu jest używana zamiast nazwy modelu.

    Aby uzyskać więcej informacji, zobacz dokumentację [AutoMLRun. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  `--asset-path`Parametr odnosi się do lokalizacji w chmurze modelu. W tym przykładzie jest używana ścieżka pojedynczego pliku. Aby uwzględnić w rejestracji modelu wiele plików, ustaw `--asset-path` ścieżkę folderu zawierającego pliki.

+ **Korzystanie z narzędzia Visual Studio Code**

  Zarejestruj modele przy użyciu dowolnych plików lub folderów modelu przy użyciu rozszerzenia [Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) .

### <a name="register-a-model-from-a-local-file"></a>Rejestrowanie modelu z pliku lokalnego

Model można zarejestrować, podając ścieżkę lokalną modelu. Można podać ścieżkę do folderu lub pojedynczego pliku. Za pomocą tej metody można rejestrować modele przeszkolone przy użyciu Azure Machine Learning, a następnie pobrać. Tej metody można również użyć do rejestrowania modeli przeszkolonych poza Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Korzystanie z zestawu SDK i ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Aby uwzględnić w rejestracji modelu wiele plików, ustaw `model_path` ścieżkę folderu zawierającego pliki.

+ **Korzystanie z interfejsu wiersza polecenia**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Aby uwzględnić w rejestracji modelu wiele plików, ustaw `-p` ścieżkę folderu zawierającego pliki.

**Szacowany czas**: około 10 sekund.

Aby uzyskać więcej informacji, zobacz dokumentację [klasy model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Aby uzyskać więcej informacji na temat pracy z modelami przeszkolonymi poza Azure Machine Learning, zobacz [jak wdrożyć istniejący model](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Pojedyncze i wielomodelowe punkty końcowe
Usługa Azure ML obsługuje wdrażanie jednego lub wielu modeli za pojedynczym punktem końcowym.

Punkty końcowe wielu modeli używają kontenera udostępnionego do hostowania wielu modeli. Pozwala to zmniejszyć koszty narzutu, zwiększyć wykorzystanie i umożliwia łączenie modułów w zestawy kompletne. Modele określone w skrypcie wdrażania są instalowane i udostępniane na dysku obsługującym kontener — można ładować je do pamięci na żądanie i oceny na podstawie określonego modelu w czasie.

Przykład E2E, który pokazuje, jak używać wielu modeli za pojedynczym punktem końcowym kontenerów, zobacz [ten przykład](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Przygotowywanie do wdrożenia

Aby wdrożyć model jako usługę, potrzebne są następujące składniki:

* **Zdefiniuj środowisko wnioskowania**. To środowisko hermetyzuje zależności wymagane do uruchomienia modelu do wnioskowania.
* **Zdefiniuj kod oceniania**. Ten skrypt akceptuje żądania, ocenia żądania przy użyciu modelu i zwraca wyniki.
* **Zdefiniuj konfigurację wnioskowania**. Konfiguracja wnioskowania określa konfigurację środowiska, skrypt wpisu i inne składniki, które są konieczne do uruchomienia modelu jako usługi.

Po zainstalowaniu niezbędnych składników można profilować usługę, która zostanie utworzona w wyniku wdrożenia modelu, aby zrozumieć jego wymagania dotyczące procesora i pamięci.

### <a name="1-define-inference-environment"></a>1. Zdefiniuj środowisko wnioskowania

Konfiguracja wnioskowania opisuje sposób konfigurowania usługi sieci Web zawierającej model. Jest on używany później podczas wdrażania modelu.

Konfiguracja wnioskowania używa środowisk Azure Machine Learning do definiowania zależności oprogramowania potrzebnych do wdrożenia. Środowiska umożliwiają tworzenie i ponowne używanie zależności oprogramowania wymaganych do szkolenia i wdrażania, a także zarządzanie nimi. Środowisko można utworzyć na podstawie niestandardowych plików zależności lub użyć jednego z nadzorowanych środowisk Azure Machine Learning. Następujący YAML jest przykładem pliku zależności Conda dla wnioskowania. Należy zauważyć, że należy wskazać wartość domyślną usługi Azure Wersja >= 1.0.45 jako zależność PIP, ponieważ zawiera ona funkcje wymagane do hostowania modelu jako usługi sieci Web. Jeśli chcesz użyć automatycznej generacji schematu, skrypt wejściowy musi również zaimportować `inference-schema` pakiety.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Jeśli zależność jest dostępna zarówno w Conda, jak i PIP (od PyPi), firma Microsoft zaleca korzystanie z wersji Conda, ponieważ pakiety Conda zwykle są dostarczane z wstępnie skompilowanymi plikami binarnymi, które sprawiają, że instalacja jest bardziej niezawodna.
>
> Aby uzyskać więcej informacji, zobacz [Omówienie Conda i PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Aby sprawdzić, czy zależność jest dostępna za pomocą Conda, użyj `conda search <package-name>` polecenia lub użyj indeksów pakietów w [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) i [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Możesz użyć pliku zależności, aby utworzyć obiekt środowiska i zapisać go w obszarze roboczym do użycia w przyszłości:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Zdefiniuj kod oceniania

Skrypt wejściowy odbiera dane przesyłane do wdrożonej usługi internetowej i przekazuje je do modelu. Następnie pobiera odpowiedź zwróconą przez model i zwraca ją do klienta. *Skrypt jest specyficzny dla modelu*. Musi on zrozumieć dane, które są oczekiwane i zwracane przez model.

Skrypt zawiera dwie funkcje, które ładują i uruchamiają model:

* `init()`: Zazwyczaj ta funkcja ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, gdy zostanie uruchomiony kontener platformy Docker dla usługi sieci Web.

* `run(input_data)`: Ta funkcja używa modelu do przewidywania wartości na podstawie danych wejściowych. Dane wejściowe i wyjściowe uruchomienia zazwyczaj używają formatu JSON na potrzeby serializacji i deserializacji. Można także pracować z nieprzetworzonymi danymi binarnymi. Dane można przekształcić przed wysłaniem ich do modelu lub przed zwróceniem do klienta.

#### <a name="load-model-files-in-your-entry-script"></a>Załaduj pliki modelu w skrypcie wprowadzania

Istnieją dwa sposoby lokalizowania modeli w skrypcie wprowadzania:
* `AZUREML_MODEL_DIR`: Zmienna środowiskowa zawierająca ścieżkę do lokalizacji modelu.
* `Model.get_model_path`: Interfejs API, który zwraca ścieżkę do pliku modelu przy użyciu nazwy zarejestrowanego modelu.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR jest zmienną środowiskową utworzoną podczas wdrażania usługi. Możesz użyć tej zmiennej środowiskowej, aby znaleźć lokalizację wdrożonych modeli.

W poniższej tabeli opisano wartość AZUREML_MODEL_DIR w zależności od liczby wdrożonych modeli:

| wdrażania | Wartość zmiennej środowiskowej |
| ----- | ----- |
| Jeden model | Ścieżka do folderu zawierającego model. |
| Wiele modeli | Ścieżka do folderu zawierającego wszystkie modele. Modele są zlokalizowane według nazwy i wersji w tym folderze ( `$MODEL_NAME/$VERSION` ) |

Podczas rejestracji i wdrażania modelu modele są umieszczane w ścieżce AZUREML_MODEL_DIR i ich oryginalne nazwy plików są zachowywane.

Aby uzyskać ścieżkę do pliku modelu w skrypcie wpisu, Połącz zmienną środowiskową z szukaną ścieżką pliku.

**Przykład pojedynczego modelu**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Przykład wielu modeli**

W tym scenariuszu są rejestrowane dwa modele z obszarem roboczym:

* `my_first_model`: Zawiera jeden plik ( `my_first_model.pkl` ) i istnieje tylko jedna wersja ( `1` ).
* `my_second_model`: Zawiera jeden plik ( `my_second_model.pkl` ), a istnieją dwie wersje: `1` i `2` .

Podczas wdrażania usługi w operacji wdrażania są dostępne oba modele:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

W obrazie platformy Docker, który obsługuje usługę, `AZUREML_MODEL_DIR` zmienna środowiskowa zawiera katalog, w którym znajdują się modele.
W tym katalogu każdy model znajduje się w ścieżce katalogu `MODEL_NAME/VERSION` . Gdzie `MODEL_NAME` jest nazwą zarejestrowanego modelu i `VERSION` jest wersją modelu. Pliki, które składają się na zarejestrowany model, są przechowywane w tych katalogach.

W tym przykładzie ścieżki są `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` i `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Podczas rejestrowania modelu należy podać nazwę modelu, która jest używana do zarządzania modelem w rejestrze. Ta nazwa jest używana z metodą [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) w celu pobrania ścieżki pliku lub plików modelu w lokalnym systemie plików. Po zarejestrowaniu folderu lub kolekcji plików ten interfejs API zwróci ścieżkę do katalogu, który zawiera te pliki.

Po zarejestrowaniu modelu nadaj mu nazwę. Nazwa odnosi się do lokalizacji modelu, lokalnie lub podczas wdrażania usługi.

#### <a name="optional-define-model-web-service-schema"></a>Obowiązkowe Zdefiniuj schemat usługi sieci Web modelu

Aby automatycznie wygenerować schemat dla usługi sieci Web, podaj przykład danych wejściowych i/lub wyjściowych w konstruktorze dla jednego z obiektów typu zdefiniowane. Typ i przykład są używane do automatycznego tworzenia schematu. Azure Machine Learning następnie tworzy specyfikację [openapi](https://swagger.io/docs/specification/about/) (Swagger) dla usługi sieci Web podczas wdrażania.

Te typy są obecnie obsługiwane:

* `pandas`
* `numpy`
* `pyspark`
* Standardowy obiekt języka Python

Aby użyć generacji schematu, Dołącz pakiet "open source" do `inference-schema` pliku zależności. Aby uzyskać więcej informacji na temat tego pakietu, zobacz [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Zdefiniuj formaty próbek wejściowych i wyjściowych w `input_sample` zmiennych i `output_sample` , które reprezentują formaty żądań i odpowiedzi dla usługi sieci Web. Te przykłady są używane w funkcji Input i Output dekoratory `run()` funkcji. Poniższy przykład scikit-Dowiedz się, jak użyć generowania schematu.

##### <a name="example-entry-script"></a>Przykładowy skrypt wprowadzania

W poniższym przykładzie pokazano, jak przyjmować i zwracać dane JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

##### <a name="power-bi-compatible-endpoint"></a>Power BI zgodny punkt końcowy 

Poniższy przykład ilustruje sposób definiowania danych wejściowych jako `<key: value>` słownika przy użyciu ramki Dataframe. Ta metoda jest obsługiwana w przypadku używania wdrożonej usługi sieci Web na podstawie Power BI. ([Dowiedz się więcej na temat korzystania z usługi sieci Web z programu Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)).

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample, enforce_shape=False))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Aby uzyskać więcej przykładów, zobacz następujące skrypty:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Dane binarne](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Zdefiniuj konfigurację wnioskowania
    
Poniższy przykład ilustruje ładowanie środowiska z obszaru roboczego, a następnie używanie go z konfiguracją wnioskowania:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz dokumentację klasy [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Przykład interfejsu wiersza polecenia InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Następujące polecenie pokazuje, jak wdrożyć model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

W tym przykładzie konfiguracja określa następujące ustawienia:

* Model wymaga języka Python.
* [Skrypt wejścia](#script), który jest używany do obsługi żądań sieci Web wysyłanych do wdrożonej usługi.
* Plik Conda, który opisuje pakiety języka Python, które są zbędne do wnioskowania.

Aby uzyskać informacje na temat używania niestandardowego obrazu platformy Docker z konfiguracją wnioskowania, zobacz [jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (opcjonalnie) profilować model, aby określić wykorzystanie zasobów

Po zarejestrowaniu modelu i przygotowaniu innych składników niezbędnych do wdrożenia można określić procesor i pamięć, która będzie potrzebna przez wdrożoną usługę. Profilowanie testuje usługę, która uruchamia model i zwraca informacje takie jak użycie procesora CPU, użycie pamięci i opóźnienie odpowiedzi. Zawiera również zalecenia dotyczące procesora CPU i pamięci na podstawie użycia zasobów.

Aby profilować model, potrzebne są:
* Zarejestrowany model.
* Konfiguracja wnioskowania na podstawie skryptu wejścia i definicji środowiska wnioskowania.
* Pojedynczy kolumnowy tabelaryczny zestaw danych, gdzie każdy wiersz zawiera ciąg reprezentujący przykładowe dane żądania.

> [!IMPORTANT]
> W tym momencie obsługujemy tylko profilowanie usług, które oczekują, aby dane żądania były ciągiem, na przykład: szeregowany ciąg JSON, tekst, serializowany ciąg znaków itd. Zawartość każdego wiersza zestawu danych (String) zostanie umieszczona w treści żądania HTTP i wysłana do usługi hermetyzowania modelu na potrzeby oceniania.

Poniżej znajduje się przykład sposobu konstruowania wejściowego zestawu danych w celu profilowania usługi, która oczekuje, że jej przychodzące dane żądania zawierają Zserializowany kod JSON. W takim przypadku utworzyliśmy zestaw danych oparty na 100 wystąpieniach tej samej zawartości danych żądania. W realnie świecie zalecamy korzystanie z większych zestawów danych zawierających różne dane wejściowe, zwłaszcza jeśli użycie/zachowanie zasobów modelu jest zależne od danych wejściowych.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Gdy zestaw danych zawierający przykładowe dane żądania są gotowe, Utwórz konfigurację wnioskowania. Konfiguracja wnioskowania jest oparta na score.py i definicji środowiska. W poniższym przykładzie pokazano, jak utworzyć konfigurację wnioskowania i uruchomić profilowanie:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

Następujące polecenie pokazuje, jak profilować model przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Aby zachować informacje zwracane przez profilowanie, użyj tagów lub właściwości dla modelu. Użycie tagów lub właściwości przechowuje dane w modelu w rejestrze modelu. W poniższych przykładach pokazano, jak dodać nowy tag `requestedCpu` zawierający `requestedMemoryInGb` informacje i:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Wdróż do celu

Wdrożenie używa konfiguracji wdrożenia konfiguracji wnioskowania do wdrożenia modeli. Proces wdrażania jest podobny niezależnie od elementu docelowego obliczeń. Wdrażanie do AKS jest nieco inne, ponieważ należy podać odwołanie do klastra AKS.

### <a name="choose-a-compute-target"></a>Wybierz element docelowy obliczeń

Do hostowania wdrożenia usługi sieci Web można użyć następujących obiektów docelowych obliczeń lub zasobów obliczeniowych:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Definiowanie konfiguracji wdrożenia

Przed wdrożeniem modelu należy zdefiniować konfigurację wdrożenia. *Konfiguracja wdrożenia jest specyficzna dla elementu docelowego obliczeń, który będzie hostować usługę sieci Web.* Na przykład podczas lokalnego wdrażania modelu należy określić port, w którym usługa akceptuje żądania. Konfiguracja wdrożenia nie jest częścią skryptu wejścia. Służy do definiowania właściwości obiektu docelowego obliczeń, który będzie hostować model i skrypt wejścia.

Może być również konieczne utworzenie zasobu obliczeniowego, jeśli na przykład nie masz jeszcze wystąpienia usługi Azure Kubernetes Service (AKS) skojarzonego z Twoim obszarem roboczym.

Poniższa tabela zawiera przykład tworzenia konfiguracji wdrożenia dla każdego obiektu docelowego obliczeń:

| Docelowy zasób obliczeniowy | Przykład konfiguracji wdrożenia |
| ----- | ----- |
| Lokalny | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasy dla lokalnych, Azure Container Instances i usług sieci Web AKS można zaimportować z `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Zabezpieczanie wdrożeń przy użyciu protokołu TLS

Aby uzyskać więcej informacji na temat zabezpieczania wdrożenia usługi sieci Web, zobacz [Włączanie protokołów TLS i Deploy](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Wdrożenie lokalne

Aby lokalnie wdrożyć model, należy zainstalować platformę Docker na komputerze lokalnym.

#### <a name="using-the-sdk"></a>Używanie zestawu SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)i usługi sieci [Web](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Korzystanie z interfejsu wiersza polecenia

Aby wdrożyć model przy użyciu interfejsu wiersza polecenia, należy użyć poniższe polecenie. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Więcej informacji można znaleźć w dokumentacji [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="understanding-service-state"></a>Informacje o stanie usługi

Podczas wdrażania modelu może zostać wyświetlona zmiana stanu usługi podczas jej pełnego wdrożenia.

W poniższej tabeli opisano różne stany usług:

| Stan usługi WebService | Opis | Końcowy stan?
| ----- | ----- | ----- |
| Przechodzenie | Usługa jest w trakcie wdrażania. | Nie |
| Nieprawidłowy | Usługa została wdrożona, ale jest obecnie nieosiągalna.  | Nie |
| Unschedulable | Nie można teraz wdrożyć usługi z powodu braku zasobów. | Nie |
| Niepowodzenie | Wdrożenie usługi nie powiodło się z powodu błędu lub awarii. | Tak |
| Dobra kondycja | Usługa jest w dobrej kondycji, a punkt końcowy jest dostępny. | Tak |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Usługa sieci Web wystąpienia obliczeniowego (Tworzenie i testowanie)

Zobacz [Wdrażanie modelu do Azure Machine Learning wystąpienia obliczeniowego](how-to-deploy-local-container-notebook-vm.md).

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Azure Container Instances (Tworzenie i testowanie)

Zobacz [wdrażanie do Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Usługa Azure Kubernetes (Tworzenie i testowanie i produkcja)

Zobacz [wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Testowanie A/B (kontrolowane wdrażanie)
Aby uzyskać więcej informacji [, zobacz kontrolowane wprowadzanie modeli ml](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) .

## <a name="consume-web-services"></a>Korzystaj z usług sieci Web

Każda wdrożona usługa sieci Web udostępnia punkt końcowy REST, dzięki czemu można tworzyć aplikacje klienckie w dowolnym języku programowania.
Jeśli włączono uwierzytelnianie oparte na kluczach dla usługi, musisz podać klucz usługi jako token w nagłówku żądania.
Jeśli włączono uwierzytelnianie oparte na tokenach dla usługi, musisz podać Azure Machine Learning token w sieci Web JSON (JWT) jako token okaziciela w nagłówku żądania. 

Podstawowa różnica polega na tym, że **klucze są statyczne i mogą być ponownie generowane ręcznie**, a **tokeny muszą być odświeżane po wygaśnięciu**. Uwierzytelnianie oparte na kluczach jest obsługiwane dla wystąpień kontenerów platformy Azure i wdrożonych usług sieci Web usługi Azure Kubernetes, a uwierzytelnianie oparte na tokenach jest dostępne **tylko** dla wdrożeń usługi Azure Kubernetes. Zobacz instrukcje [dotyczące](how-to-setup-authentication.md#web-service-authentication) uwierzytelniania, aby uzyskać więcej informacji i przykłady kodu.

> [!TIP]
> Po wdrożeniu usługi można pobrać dokument JSON schematu. Użyj [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) ze wdrożonej usługi sieci Web (na przykład `service.swagger_uri` ), aby uzyskać identyfikator URI do pliku struktury Swagger lokalnej usługi sieci Web.

### <a name="request-response-consumption"></a>Żądanie — użycie odpowiedzi

Oto przykład sposobu wywoływania usługi w języku Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji klienckich do korzystania z usług sieci Web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schemat usługi sieci Web (Specyfikacja OpenAPI)

Jeśli używasz automatycznej generacji schematu w ramach wdrożenia, możesz uzyskać adres specyfikacji OpenAPI dla usługi przy użyciu [właściwości swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Na przykład `print(service.swagger_uri)` .) Użyj żądania GET lub Otwórz identyfikator URI w przeglądarce, aby pobrać specyfikację.

Poniższy dokument JSON jest przykładem schematu (Specyfikacja OpenAPI) wygenerowanego dla wdrożenia:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz [Specyfikacja openapi](https://swagger.io/specification/).

Aby uzyskać narzędzie, które może tworzyć biblioteki klienckie ze specyfikacją, zobacz [Swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Wnioskowanie partii
Azure Machine Learning obiekty docelowe obliczeń są tworzone i zarządzane przez Azure Machine Learning. Mogą one służyć do prognozowania partii z potoków Azure Machine Learning.

Aby zapoznać się z przewodnikiem dotyczącym wnioskowania partii przy użyciu obliczeń Azure Machine Learning, zobacz [jak uruchamiać przewidywania wsadowe](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>IoT Edge wnioskowanie
Obsługa wdrażania na brzegu jest w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Machine Learning jako moduł IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Aktualizowanie usług sieci Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Ciągle Wdrażaj modele

Możesz ciągle wdrażać modele przy użyciu rozszerzenia Machine Learning dla [usługi Azure DevOps](https://azure.microsoft.com/services/devops/). Możesz użyć rozszerzenia Machine Learning dla usługi Azure DevOps, aby wyzwolić potok wdrożenia w przypadku zarejestrowania nowego modelu uczenia maszynowego w obszarze roboczym Azure Machine Learning.

1. Utwórz konto, aby uzyskać [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), które zapewnia ciągłą integrację i dostarczanie aplikacji na dowolną platformę lub chmurę. (Należy zauważyć, że Azure Pipelines nie są takie same jak [potoki Machine Learning](concept-ml-pipelines.md#compare).)

1. [Utwórz projekt usługi Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Zainstaluj [rozszerzenie Machine Learning dla Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Użyj połączeń usługi, aby skonfigurować połączenie główne usługi z obszarem roboczym Azure Machine Learning, aby umożliwić dostęp do artefaktów. Przejdź do pozycji Ustawienia projektu, wybierz pozycję **połączenia usługi**, a następnie wybierz pozycję **Azure Resource Manager**:

    [![Wybierz Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na liście **poziom zakresu** wybierz pozycję **AzureMLWorkspace**, a następnie wprowadź resztę wartości:

    ![Wybierz AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Aby ciągle wdrożyć model uczenia maszynowego przy użyciu Azure Pipelines, w obszarze potoki wybierz pozycję **Zwolnij**. Dodaj nowy artefakt, a następnie wybierz artefakt **modelu usługi Azure** i utworzone wcześniej połączenie z usługą. Wybierz model i wersję, aby wyzwolić wdrożenie:

    [![Wybierz model Azure](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Włącz wyzwalacz modelu w artefaktie modelu. Po włączeniu wyzwalacza za każdym razem, gdy określona wersja (czyli Najnowsza wersja) tego modelu jest zarejestrowana w obszarze roboczym, zostanie wyzwolony potok wydania usługi Azure DevOps.

    [![Włączanie wyzwalacza modelu](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Aby uzyskać więcej przykładowych projektów i przykładów, zobacz te przykładowe repozytoria w witrynie GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Pobierz model
Jeśli chcesz pobrać model do użycia w środowisku wykonawczym, możesz to zrobić za pomocą następujących poleceń zestawu SDK/interfejsu wiersza polecenia:

ZESTAWIE
```python
model_path = Model(ws,'mymodel').download()
```

Interfejs
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Przeglądania Wdrożenie modelu bez kodu

Wdrożenie modelu bez kodu jest obecnie dostępne w wersji zapoznawczej i obsługuje następujące platformy uczenia maszynowego:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel
Modele Tensorflow muszą być zarejestrowane w **formacie SavedModel** do pracy z wdrożeniem modelu bez kodu.

Zobacz [ten link](https://www.tensorflow.org/guide/saved_model) , aby uzyskać informacje na temat tworzenia SavedModel.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modele ONNX

Rejestracja i wdrożenie modelu ONNX są obsługiwane w przypadku grafu wnioskowania ONNX. Kroki preprocesora i PostProcess nie są obecnie obsługiwane.

Oto przykład sposobu rejestrowania i wdrażania modelu ONNX MNIST ręcznie:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Jeśli używasz Pytorch, [Eksportowanie modeli z Pytorch do ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) zawiera szczegółowe informacje dotyczące konwersji i ograniczeń. 

### <a name="scikit-learn-models"></a>Scikit — uczenie modeli

Żadne wdrożenie modelu kodu nie jest obsługiwane dla wszystkich wbudowanych typów modeli scikit.

Oto przykład sposobu rejestrowania i wdrażania modelu skryptu sklearn bez dodatkowego kodu:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Uwaga: modele, które obsługują predict_proba będą używać tej metody domyślnie. Aby zastąpić ten sposób, można zmodyfikować treść wpisu w następujący sposób:
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Uwaga: te zależności są zawarte we wstępnie skompilowanym kontenerze wnioskowania scikit:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```

## <a name="package-models"></a>Modele pakietów

W niektórych przypadkach może być konieczne utworzenie obrazu platformy Docker bez wdrażania modelu (Jeśli na przykład planujesz [wdrożyć program w Azure App Service](how-to-deploy-app-service.md)). Możesz też chcieć pobrać obraz i uruchomić go w lokalnej instalacji platformy Docker. Możesz nawet pobrać pliki użyte do skompilowania obrazu, sprawdzić je, zmodyfikować i ręcznie utworzyć obraz.

Pakowanie modeli umożliwia wykonywanie tych czynności. Wszystkie zasoby potrzebne do hostowania modelu jako usługi sieci Web i umożliwiają pobranie w pełni skompilowanego obrazu platformy Docker lub plików niezbędnych do jego skompilowania. Istnieją dwa sposoby używania pakowania modelu:

**Pobierz spakowany model:** Pobierz obraz platformy Docker zawierający model i inne pliki niezbędne do hostowania go jako usługi sieci Web.

**Generuj element pliku dockerfile:** Pobierz pliku dockerfile, model, skrypt wejściowy i inne zasoby, które są konieczne do skompilowania obrazu platformy Docker. Następnie można przeprowadzić inspekcję plików lub wprowadzić zmiany przed rozpoczęciem tworzenia obrazu lokalnie.

Oba pakiety mogą służyć do pobrania lokalnego obrazu platformy Docker.

> [!TIP]
> Tworzenie pakietu jest podobne do wdrażania modelu. Używasz zarejestrowanego modelu i konfiguracji wnioskowania.

> [!IMPORTANT]
> Aby pobrać pełny obraz lub utworzyć obraz lokalnie, należy zainstalować [platformę Docker](https://www.docker.com) w środowisku deweloperskim.

### <a name="download-a-packaged-model"></a>Pobierz spakowany model

Poniższy przykład tworzy obraz, który jest rejestrowany w usłudze Azure Container Registry dla Twojego obszaru roboczego:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Po utworzeniu pakietu można użyć `package.pull()` programu w celu ściągnięcia obrazu do lokalnego środowiska platformy Docker. W danych wyjściowych tego polecenia zostanie wyświetlona nazwa obrazu. Przykład: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Po pobraniu modelu Użyj `docker images` polecenia, aby wyświetlić listę obrazów lokalnych:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Aby uruchomić kontener lokalny na podstawie tego obrazu, użyj następującego polecenia, aby uruchomić nazwany kontener z powłoki lub wiersza polecenia. Zastąp `<imageid>` wartość identyfikatorem obrazu zwracanym przez `docker images` polecenie.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

To polecenie powoduje uruchomienie najnowszej wersji obrazu o nazwie `myimage` . Mapuje port lokalny 6789 na port w kontenerze, w którym usługa sieci Web nasłuchuje (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po rozpoczęciu kontenera można przesłać żądania do `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Generowanie pliku dockerfile i zależności

Poniższy przykład pokazuje, jak pobrać pliku dockerfile, model i inne zasoby, które są konieczne do lokalnego utworzenia obrazu. `generate_dockerfile=True`Parametr wskazuje, że chcesz, aby pliki nie były w pełni skompilowane.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Ten kod pobiera pliki, które są konieczne do skompilowania obrazu do `imagefiles` katalogu. Pliku dockerfile zawarte w zapisanych plikach odwołuje się do obrazu podstawowego przechowywanego w rejestrze kontenerów platformy Azure. Podczas tworzenia obrazu w lokalnej instalacji platformy Docker należy użyć adresu, nazwy użytkownika i hasła w celu uwierzytelnienia w rejestrze. Wykonaj następujące kroki, aby skompilować obraz przy użyciu lokalnej instalacji platformy Docker:

1. Z poziomu powłoki lub sesji wiersza polecenia Użyj następującego polecenia, aby uwierzytelnić platformę Docker za pomocą usługi Azure Container Registry. Zastąp `<address>` `<username>` wartości, i `<password>` wartościami pobranymi przez `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Aby skompilować obraz, użyj następującego polecenia. Zamień na `<imagefiles>` ścieżkę do katalogu, w którym `package.save()` zapisano pliki.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    To polecenie ustawia nazwę obrazu na `myimage` .

Aby sprawdzić, czy obraz został skompilowany, użyj `docker images` polecenia. Obraz powinien zostać wyświetlony `myimage` na liście:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Aby rozpocząć nowy kontener oparty na tym obrazie, użyj następującego polecenia:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

To polecenie powoduje uruchomienie najnowszej wersji obrazu o nazwie `myimage` . Mapuje port lokalny 6789 na port w kontenerze, w którym usługa sieci Web nasłuchuje (5001). Przypisuje również nazwę `mycontainer` do kontenera, co ułatwia zatrzymanie kontenera. Po rozpoczęciu kontenera można przesłać żądania do `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Przykładowy klient do testowania lokalnego kontenera

Poniższy kod stanowi przykład klienta języka Python, który może być używany z kontenerem:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Na przykład klientów w innych językach programowania można znaleźć w temacie [Korzystanie z modeli wdrożonych jako usługi sieci Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Zatrzymaj kontener platformy Docker

Aby zatrzymać kontener, użyj następującego polecenia z innej powłoki lub wiersza polecenia:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć wdrożoną usługę sieci Web, użyj programu `service.delete()` .
Aby usunąć zarejestrowany model, użyj `model.delete()` .

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą usługi [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) i [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Tworzenie zaawansowanego skryptu wprowadzania

<a id="binary"></a>

### <a name="binary-data"></a>Dane binarne

Jeśli model akceptuje dane binarne, takie jak obraz, należy zmodyfikować `score.py` plik używany we wdrożeniu w celu akceptowania żądań nieprzetworzonych http. Aby zaakceptować dane pierwotne, użyj `AMLRequest` klasy w skrypcie wpisu i Dodaj `@rawhttp` dekoratora do `run()` funkcji.

Oto przykład `score.py` , który akceptuje dane binarne:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest`Klasa znajduje się w `azureml.contrib` przestrzeni nazw. Jednostki w tej przestrzeni nazw często zmieniają się, gdy pracujemy nad ulepszeniem usługi. Wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza, która nie jest w pełni obsługiwana przez firmę Microsoft.
>
> Jeśli konieczne jest przetestowanie go w lokalnym środowisku programistycznym, można zainstalować składniki przy użyciu następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest`Klasa pozwala tylko na dostęp do nieprzetworzonych danych w Score.py nie ma składnika klienta. Z poziomu klienta dane są ogłaszane jako normalne. Na przykład poniższy kod w języku Python odczytuje plik obrazu i zapisuje dane:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

Udostępnianie zasobów między źródłami to sposób zezwalania na żądanie zasobów na stronie sieci Web z innej domeny. Mechanizm CORS działa przez nagłówki HTTP wysyłane z żądaniem klienta i zwracane z odpowiedzią usługi. Aby uzyskać więcej informacji na temat mechanizmu CORS i prawidłowych nagłówków, zobacz [udostępnianie zasobów między źródłami](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) w witrynie Wikipedia.

Aby skonfigurować wdrożenie modelu do obsługi mechanizmu CORS, użyj `AMLResponse` klasy w skrypcie wpisu. Ta klasa umożliwia ustawianie nagłówków obiektu Response.

Poniższy przykład ustawia `Access-Control-Allow-Origin` Nagłówek odpowiedzi ze skryptu wprowadzania:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse`Klasa znajduje się w `azureml.contrib` przestrzeni nazw. Jednostki w tej przestrzeni nazw często zmieniają się, gdy pracujemy nad ulepszeniem usługi. Wszystkie elementy w tej przestrzeni nazw powinny być traktowane jako wersja zapoznawcza, która nie jest w pełni obsługiwana przez firmę Microsoft.
>
> Jeśli konieczne jest przetestowanie go w lokalnym środowisku programistycznym, można zainstalować składniki przy użyciu następującego polecenia:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning będzie kierować tylko żądania POST i GET do kontenerów z uruchomioną usługą oceniania. Może to spowodować błędy ze względu na to, że przeglądarki używają żądań opcji do wstępnych żądań CORS.
> 

## <a name="next-steps"></a>Następne kroki

* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Użyj protokołu TLS do zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](how-to-secure-web-service.md)
* [Korzystanie z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)

