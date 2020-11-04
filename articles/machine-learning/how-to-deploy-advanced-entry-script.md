---
title: Tworzenie skryptu wprowadzania dla zaawansowanych scenariuszy
titleSuffix: Azure Machine Learning entry script authoring
description: Dowiedz się, jak pisać skrypty wprowadzania Azure Machine Learning na potrzeby przetwarzania wstępnego i po wdrożeniu.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 2225ef42d8862935e5cd682a3c11a7ce687babab
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325562"
---
# <a name="advanced-entry-script-authoring"></a>Tworzenie zaawansowanego skryptu wejściowego

W tym artykule pokazano, jak napisać skrypty wprowadzania dla wyspecjalizowanych przypadków użycia.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz już szkolony model uczenia maszynowego, który zamierzasz wdrożyć za pomocą Azure Machine Learning. Aby dowiedzieć się więcej na temat wdrażania modelu, zobacz [ten samouczek](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Automatyczne generowanie schematu struktury Swagger

Aby automatycznie wygenerować schemat dla usługi sieci Web, podaj przykład danych wejściowych i/lub wyjściowych w konstruktorze dla jednego z obiektów typu zdefiniowane. Typ i przykład są używane do automatycznego tworzenia schematu. Azure Machine Learning następnie tworzy specyfikację [openapi](https://swagger.io/docs/specification/about/) (Swagger) dla usługi sieci Web podczas wdrażania.

Te typy są obecnie obsługiwane:

* `pandas`
* `numpy`
* `pyspark`
* Standardowy obiekt języka Python

Aby można było użyć generacji schematu, w pliku zależności należy uwzględnić pakiet "open source" `inference-schema` w wersji 1.1.0 lub nowszej. Aby uzyskać więcej informacji na temat tego pakietu, zobacz [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . Aby wygenerować zgodne z automatycznym użyciem usługi sieci Web Swagger, funkcja uruchamiania skryptu oceniania () musi mieć kształt interfejsu API:
* Pierwszy parametr typu "StandardPythonParameterType", nazwane dane wejściowe, zagnieżdżony, zawierający PandasDataframeParameterTypes.
* Opcjonalny drugi parametr typu "StandardPythonParameterType" o nazwie GlobalParameter, który nie jest zagnieżdżony.
* Zwróć słownik typu "StandardPythonParameterType", który może być zagnieżdżony, zawierający PandasDataFrameParameterTypes.
Zdefiniuj formaty próbek wejściowych i wyjściowych w `input_sample` zmiennych i `output_sample` , które reprezentują formaty żądań i odpowiedzi dla usługi sieci Web. Te przykłady są używane w funkcji Input i Output dekoratory `run()` funkcji. Poniższy przykład scikit-Dowiedz się, jak użyć generowania schematu.



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
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

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

## <a name="power-bi-compatible-endpoint"></a>Power BI zgodny punkt końcowy 

Poniższy przykład ilustruje sposób definiowania kształtu interfejsu API zgodnie z powyższą instrukcją. Ta metoda jest obsługiwana w przypadku używania wdrożonej usługi sieci Web na podstawie Power BI. ([Dowiedz się więcej na temat korzystania z usługi sieci Web z programu Power BI](/power-bi/service-machine-learning-integration)).

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)

# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                            'input2': pandas_sample_input, 
                                            'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) #this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])

@input_schema('inputs', sample_input)
@input_schema('global_parameters', sample_global_parameters) #this is optional
@output_schema(sample_output)
def run(inputs, global_parameters):
    try:
        data = inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Dane binarne (tj. obrazu)

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

## <a name="cross-origin-resource-sharing-cors"></a>Współużytkowanie zasobów między źródłami (CORS)

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


## <a name="load-registered-models"></a>Załaduj zarejestrowane modele

Istnieją dwa sposoby lokalizowania modeli w skrypcie wprowadzania:
* `AZUREML_MODEL_DIR`: Zmienna środowiskowa zawierająca ścieżkę do lokalizacji modelu.
* `Model.get_model_path`: Interfejs API, który zwraca ścieżkę do pliku modelu przy użyciu nazwy zarejestrowanego modelu.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR jest zmienną środowiskową utworzoną podczas wdrażania usługi. Możesz użyć tej zmiennej środowiskowej, aby znaleźć lokalizację wdrożonych modeli.

W poniższej tabeli opisano wartość AZUREML_MODEL_DIR w zależności od liczby wdrożonych modeli:

| Wdrożenie | Wartość zmiennej środowiskowej |
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

### <a name="get_model_path"></a>get_model_path

Podczas rejestrowania modelu należy podać nazwę modelu, która jest używana do zarządzania modelem w rejestrze. Ta nazwa jest używana z metodą [model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) w celu pobrania ścieżki pliku lub plików modelu w lokalnym systemie plików. Po zarejestrowaniu folderu lub kolekcji plików ten interfejs API zwróci ścieżkę do katalogu, który zawiera te pliki.

Po zarejestrowaniu modelu nadaj mu nazwę. Nazwa odnosi się do lokalizacji modelu, lokalnie lub podczas wdrażania usługi.

## <a name="framework-specific-examples"></a>Przykłady specyficzne dla platformy

Więcej przykładów skryptu wprowadzania dla konkretnych przypadków użycia uczenia maszynowego można znaleźć poniżej:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.yml)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z niepowodzeniem wdrożenia](how-to-troubleshoot-deployment.md)
* [Wdrażanie w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Tworzenie aplikacji klienckich w celu korzystania z usług sieci Web](how-to-consume-web-service.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Tworzenie alertów zdarzeń i wyzwalaczy na potrzeby wdrożeń modeli](how-to-use-event-grid.md)