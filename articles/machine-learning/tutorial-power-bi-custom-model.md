---
title: 'Samouczek: Tworzenie modelu predykcyjnego za pomocą notesu (część 1 z 2)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć i wdrożyć model uczenia maszynowego przy użyciu kodu w Jupyter Notebook, dzięki czemu można go użyć do przewidywania wyników w programie Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370267"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Samouczek: integracja Power BI — Tworzenie modelu predykcyjnego za pomocą notesu (część 1 z 2)

W pierwszej części tego samouczka nauczysz się i wdrożyć model uczenia maszynowego przy użyciu kodu w Jupyter Notebook. W części 2 zostanie następnie użyty model do przewidywania wyników w programie Microsoft Power BI.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie notesu Jupyter
> * Tworzenie wystąpienia obliczeniowego Azure Machine Learning
> * Uczenie modelu regresji przy użyciu scikit — uczenie się
> * Wdrażanie modelu w punkcie końcowym oceniania w czasie rzeczywistym

Istnieją trzy różne sposoby tworzenia i wdrażania modelu, który będzie używany w Power BI.  W tym artykule omówiono opcję A: uczenie i wdrażanie modeli przy użyciu notesów.  Ta opcja umożliwia pokazanie środowiska tworzenia kodu przy użyciu notesów Jupyter hostowanych w programie Azure Machine Learning Studio. 

Zamiast tego można użyć:

* [Opcja B: uczenie i wdrażanie modeli przy użyciu narzędzia Projektant](tutorial-power-bi-designer-model.md)— środowisko tworzenia z niską ilością kodu przy użyciu narzędzia Projektant (interfejs użytkownika typu "przeciągnij i upuść").
* [Opcja C: uczenie i wdrażanie modeli przy użyciu zautomatyzowanej](tutorial-power-bi-automated-model.md) sieci — środowisko tworzenia bez kodu, które w pełni automatyzuje przygotowanie danych i szkolenia modeli.


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure ([dostępna jest bezpłatna wersja próbna](https://aka.ms/AMLFree)). 
- Obszar roboczy usługi Azure Machine Learning. Jeśli jeszcze nie masz obszaru roboczego, postępuj zgodnie z instrukcjami [tworzenia obszar roboczy usługi Azure Machine Learning](./how-to-manage-workspace.md#create-a-workspace).
- Wstępna wiedza dotycząca języka Python i przepływów pracy uczenia maszynowego.

## <a name="create-a-notebook-and-compute"></a>Tworzenie notesu i obliczeń

Na stronie głównej [Azure Machine Learning Studio](https://ml.azure.com) wybierz pozycję **Utwórz nowy** , a następnie wpisz **Notes**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia notesu":::
 
Zostanie wyświetlone okno dialogowe umożliwiające **utworzenie nowego pliku** :

1. Nazwa pliku dla notesu (na przykład `my_model_notebook` )
1. Zmień **Typ pliku** na **Notes**

Wybierz pozycję **Utwórz**. Następnie musisz utworzyć pewne obliczenia i dołączyć je do notesu w celu uruchomienia komórek kodu. W tym celu wybierz ikonę Plus w górnej części notesu:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia wystąpienia obliczeniowego":::

Następnie na stronie **Tworzenie wystąpienia obliczeniowego** :

1. Wybierz rozmiar maszyny wirtualnej procesora CPU — na potrzeby tego samouczka **Standard_D11_v2** (dwa rdzenie, 14 GB pamięci RAM) będą działać prawidłowo.
1. Wybierz pozycję **Dalej**. 
1. Na stronie **Konfigurowanie ustawień** podaj prawidłową **nazwę obliczeniową** (prawidłowe znaki to wielkie i małe litery, cyfry i znaki).
1. Wybierz pozycję **Utwórz**.

Możesz zauważyć, że w notesie, który został obrócony obok pozycji **obliczeniowej** , został włączony błękitny, co oznacza, że tworzone jest wystąpienie obliczeniowe:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Zrzut ekranu przedstawiający tworzenie obliczeń":::

> [!NOTE]
> Zainicjowanie obsługi obliczeń może potrwać około 2-4 minut.

Po aprowizacji obliczeń można użyć notesu do wykonania komórek kodu. Na przykład wpisz w komórce:

```python
import numpy as np

np.sin(3)
```

A następnie **klawisz Shift-Enter** (lub **Control-Enter** lub zaznacz przycisk odtwarzania obok komórki). Powinny zostać wyświetlone następujące dane wyjściowe:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Zrzut ekranu przedstawiający wykonywanie komórki":::

Teraz możesz przystąpić do kompilowania modelu Machine Learning!

## <a name="build-a-model-using-scikit-learn"></a>Tworzenie modelu przy użyciu scikit — informacje

W tym samouczku użyjesz [zestawu danych cukrzycą](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), który jest dostępny na [platformie Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Importowanie danych

Aby zaimportować dane, skopiuj i wklej poniższy kod do nowej **komórki kodu** w notesie:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df`Ramka danych Pandas zawiera 10 zmiennych wejściowych linii bazowej (takich jak wiek, płeć, indeks masy ciała, średnie ciśnienie krwi oraz sześć pomiarów surowicy krwi). `y_df`Ramka danych Pandas to zmienna docelowa zawierająca ilościową miarę postępów chorobowych w jednym roku po linii bazowej. Istnieje łącznie 442 rekordów.

### <a name="train-model"></a>Trenowanie modelu

Utwórz nową **komórkę kodu** w notesie, a następnie skopiuj i wklej poniższy fragment kodu, który konstruuje model regresji pierścieniowej i serializować model przy użyciu formatu pakietu pickle języka Python:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Rejestrowanie modelu

Oprócz zawartości samego pliku modelu zarejestrowany model będzie również przechowywać metadane modelu — opis modelu, Tagi i informacje o strukturze, które będą przydatne podczas zarządzania i wdrażania modeli w obszarze roboczym. Za pomocą tagów można na przykład kategoryzację modeli i zastosować filtry podczas wyświetlania listy modeli w obszarze roboczym. Ponadto oznaczenie tego modelu przy użyciu struktury scikit-uczenie upraszcza wdrażanie go jako usługi sieci Web, ponieważ będzie on widoczny w dalszej części.

Skopiuj i wklej poniższy kod do nowej **komórki kodu** w notesie:

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Możesz również wyświetlić model w Azure Machine Learning Studio, przechodząc do **punktów końcowych** w menu po lewej stronie:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Zrzut ekranu przedstawiający model":::

### <a name="define-the-scoring-script"></a>Zdefiniuj skrypt oceniania

Podczas wdrażania modelu do zintegrowania z programem Microsoft Power BI należy zdefiniować *skrypt oceniania* w języku Python i środowisko niestandardowe. Skrypt oceniania zawiera dwie funkcje:

- `init()` — Ta funkcja jest wykonywana po uruchomieniu usługi. Ta funkcja ładuje model (należy zauważyć, że model jest automatycznie pobierany z rejestru modelu) i deserializacji.
- `run(data)` — Ta funkcja jest wykonywana w przypadku wywołania do usługi z niektórymi danymi wejściowymi wymagającymi oceniania. 

>[!NOTE]
> Używamy języka Python dekoratory do definiowania schematu danych wejściowych i wyjściowych, co jest ważne dla integracji z programem Microsoft Power BI.

Skopiuj i wklej poniższy kod do nowej **komórki kodu** w notesie. Poniższy fragment kodu ma magiczną komórkę, która zapisze kod w zachowanej nazwie score.py.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definiowanie środowiska niestandardowego

Następnie musimy zdefiniować środowisko do oceny modelu — musimy zdefiniować w tym środowisku pakiety języka Python wymagane przez skrypt oceniania (score.py) zdefiniowane powyżej, takie jak Pandas, scikit-uczenie itp.

Aby zdefiniować środowisko, skopiuj i wklej poniższy kod do nowej **komórki kodu** w notesie:

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Wdrażanie modelu

Aby wdrożyć model, skopiuj i wklej poniższy kod do nowej **komórki kodu** w notesie:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Wdrożenie usługi może potrwać 2-4 minut.

Powinny zostać wyświetlone następujące dane wyjściowe pomyślnie wdrożonej usługi:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Możesz również wyświetlić usługę w Azure Machine Learning Studio, przechodząc do **punktów końcowych** w menu po lewej stronie:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Zrzut ekranu przedstawiający punkt końcowy":::

Zaleca się przetestowanie usługi sieci Web, aby upewnić się, że działa zgodnie z oczekiwaniami. Wróć do notesu, wybierając pozycję **notesy** w menu po lewej stronie w Azure Machine Learning Studio. Skopiuj i wklej poniższy kod do nowej **komórki kodu** w notesie, aby przetestować usługę:

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Dane wyjściowe powinny wyglądać jak w następującej strukturze JSON: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób kompilowania i wdrażania modelu w taki sposób, aby mogły być używane przez firmę Microsoft Power BI. W następnej części dowiesz się, jak korzystać z tego modelu z raportu Power BI.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z modelu w Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
