---
title: 'Samouczek: Tworzenie modelu predykcyjnego za pomocą notesu (część 1 z 2)'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skompilować i wdrożyć model uczenia maszynowego przy użyciu kodu w Jupyter Notebook. Utwórz także skrypt oceniania, który definiuje dane wejściowe i wyjściowe umożliwiające łatwą integrację z programem Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 00d5fa43245fb25b8ee99a0523d680bef891b71e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387006"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Samouczek: integracja Power BI — Tworzenie modelu predykcyjnego za pomocą Jupyter Notebook (część 1 z 2)

W części 1 tego samouczka nauczysz się i wdrożyć model uczenia maszynowego przy użyciu kodu w Jupyter Notebook. Utworzysz również skrypt oceniania, aby zdefiniować schemat danych wejściowych i wyjściowych modelu do integracji w Power BI.  W części 2 będziesz używać modelu do przewidywania wyników w programie Microsoft Power BI.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Tworzenie notesu Jupyter
> * Utwórz wystąpienie obliczeniowe Azure Machine Learning.
> * Uczenie modelu regresji przy użyciu scikit — uczenie się.
> * Napisz skrypt oceniający, który definiuje dane wejściowe i wyjściowe umożliwiające łatwą integrację z programem Microsoft Power BI.
> * Wdróż model w punkcie końcowym oceniania w czasie rzeczywistym.

Istnieją trzy sposoby tworzenia i wdrażania modelu, który będzie używany w Power BI.  W tym artykule opisano "opcja A: uczenie i wdrażanie modeli przy użyciu notesów".  Ta opcja jest interfejsem tworzenia kodu. Używa ona notesów Jupyter, które są hostowane w Azure Machine Learning Studio. 

Zamiast tego można użyć jednej z innych opcji:

* [Opcja B: uczenie i wdrażanie modeli przy użyciu narzędzia Azure Machine Learning Designer](tutorial-power-bi-designer-model.md). W tym środowisku tworzenia kodu jest wykorzystywany interfejs użytkownika typu "przeciągnij i upuść".
* [Opcja C: uczenie i wdrażanie modeli przy użyciu funkcji automatycznego uczenia maszynowego](tutorial-power-bi-automated-model.md). To środowisko tworzenia kodu nie pełni całkowicie automatyzuje przygotowanie danych i szkolenia modeli.


## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji, możesz skorzystać z [bezpłatnej wersji próbnej](https://aka.ms/AMLFree). 
- Obszar roboczy usługi Azure Machine Learning. Jeśli nie masz jeszcze obszaru roboczego, zapoznaj się z tematem [tworzenie Azure Machine Learning obszarów roboczych i zarządzanie nimi](./how-to-manage-workspace.md#create-a-workspace).
- Wstępna wiedza dotycząca języka Python i przepływów pracy uczenia maszynowego.

## <a name="create-a-notebook-and-compute"></a>Tworzenie notesu i obliczeń

Na stronie głównej [**Azure Machine Learning Studio**](https://ml.azure.com) wybierz pozycję **Utwórz nowy**  >  **Notes**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia notesu.":::
 
Na stronie **Tworzenie nowego pliku** :

1. Nazwij Notes (na przykład *my_model_notebook*).
1. Zmień **Typ pliku** na **Notes**.
1. Wybierz przycisk **Utwórz**. 
 
Następnie, aby uruchomić komórki kodu, Utwórz wystąpienie obliczeniowe i Dołącz je do notesu. Zacznij od wybrania ikony Plus w górnej części notesu:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia wystąpienia obliczeniowego.":::

Na stronie **Tworzenie wystąpienia obliczeniowego** :

1. Wybierz rozmiar maszyny wirtualnej procesora CPU. Na potrzeby tego samouczka można wybrać **Standard_D11_v2** z 2 rdzeniami i 14 GB pamięci RAM.
1. Wybierz opcję **Dalej**. 
1. Na stronie **Konfigurowanie ustawień** podaj prawidłową **nazwę obliczeniową**. Prawidłowe znaki to wielkie i małe litery, cyfry i łączniki (-).
1. Wybierz przycisk **Utwórz**.

W notesie można zauważyć, że okrąg **znajduje się obok** pozycji "niebieskozielony". Ta zmiana koloru wskazuje, że wystąpienie obliczeniowe jest tworzone:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Zrzut ekranu przedstawiający tworzenie obliczeń.":::

> [!NOTE]
> Zainicjowanie obsługi wystąpienia obliczeniowego może potrwać od 2 do 4 minut.

Po aprowizacji obliczeń można użyć notesu do uruchamiania komórek kodu. Na przykład w komórce można wpisać następujący kod:

```python
import numpy as np

np.sin(3)
```

Następnie wybierz klawisze Shift + Enter (lub Wybierz kontrolkę + Enter lub wybierz przycisk **odtwarzania** obok komórki). Powinny zostać wyświetlone następujące dane wyjściowe:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Zrzut ekranu przedstawiający dane wyjściowe komórki.":::

Teraz możesz przystąpić do kompilowania modelu uczenia maszynowego.

## <a name="build-a-model-by-using-scikit-learn"></a>Tworzenie modelu przy użyciu scikit — uczenie się

W tym samouczku użyjesz [zestawu danych cukrzycą](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Ten zestaw danych jest dostępny w [usłudze Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Importowanie danych

Aby zaimportować dane, Skopiuj poniższy kod i wklej go w nowej *komórce kodu* w notesie.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df`Ramka danych Pandas zawiera 10 zmiennych wejściowych linii bazowej. Te zmienne obejmują wiek, płeć, indeks masy ciała, średnie ciśnienie krwi oraz sześć pomiarów surowicy krwi. `y_df`Ramka danych Pandas to zmienna docelowa. Zawiera ona ilościową miarę postępów chorób w jednym roku po linii bazowej. Ramka danych zawiera 442 rekordów.

### <a name="train-the-model"></a>Trenowanie modelu

Utwórz nową *komórkę kodu* w notesie. Następnie skopiuj Poniższy kod i wklej go w komórce. Ten fragment kodu tworzy model regresji pierścieniowej i serializować model przy użyciu formatu pakietu pickle języka Python.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Rejestrowanie modelu

Oprócz zawartości samego pliku modelu zarejestrowany model będzie przechowywać metadane. Metadane obejmują opis modelu, Tagi i informacje dotyczące struktury. 

Metadane są przydatne podczas zarządzania i wdrażania modeli w obszarze roboczym. Za pomocą tagów można na przykład kategoryzację modeli i zastosować filtry podczas wyświetlania listy modeli w obszarze roboczym. Ponadto, jeśli oznaczesz ten model przy użyciu struktury scikit — Dowiedz się, jak uprościć wdrażanie jej jako usługi sieci Web.

Skopiuj poniższy kod, a następnie wklej go w nowej *komórce kodu* w notesie.

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

Możesz również wyświetlić model w Azure Machine Learning Studio. W menu po lewej stronie wybierz pozycję **modele**:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania modelu.":::

## <a name="define-the-scoring-script"></a>Zdefiniuj skrypt oceniania

Podczas wdrażania modelu, który zostanie zintegrowany z Power BI, należy zdefiniować *skrypt oceniania* w języku Python i środowisko niestandardowe. Skrypt oceniania zawiera dwie funkcje:

- `init()`Funkcja jest uruchamiana, gdy usługa zostanie uruchomiona. Ładuje on model (który jest automatycznie pobierany z rejestru modelowego) i deserializacji.
- `run(data)`Funkcja jest uruchamiana, gdy wywołanie do usługi zawiera dane wejściowe, które muszą zostać ocenione. 

>[!NOTE]
> Dekoratory języka Python w kodzie poniżej definiuje schemat danych wejściowych i wyjściowych, co jest ważne dla integracji w Power BI.

Skopiuj poniższy kod i wklej go do nowej *komórki kodu* w notesie. Poniższy fragment kodu ma magiczną komórkę, która zapisuje kod w pliku o nazwie *Score.py*.

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
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definiowanie środowiska niestandardowego

Następnie zdefiniuj środowisko do oceny modelu. W środowisku Zdefiniuj pakiety języka Python, takie jak Pandas i scikit — Dowiedz się, że wymagany jest skrypt oceniania (*Score.py*).

Aby zdefiniować środowisko, Skopiuj poniższy kod i wklej go w nowej *komórce kodu* w notesie.

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

Aby wdrożyć model, Skopiuj poniższy kod i wklej go w nowej *komórce kodu* w notesie:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Wdrożenie usługi może potrwać od 2 do 4 minut.

Jeśli usługa została pomyślnie wdrożona, powinny zostać wyświetlone następujące dane wyjściowe:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Usługę można także wyświetlić w Azure Machine Learning Studio. W menu po lewej stronie wybierz pozycję **punkty końcowe**:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Zrzut ekranu przedstawiający sposób wyświetlania usługi.":::

Zalecamy przetestowanie usługi sieci Web, aby upewnić się, że działa zgodnie z oczekiwaniami. Aby zwrócić Notes, w Azure Machine Learning Studio w menu po lewej stronie wybierz pozycję **Notes**. Następnie skopiuj Poniższy kod i wklej go do nowej *komórki kodu* w notesie w celu przetestowania usługi.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

Dane wyjściowe powinny wyglądać podobnie do tej struktury JSON: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Następne kroki

W tym samouczku pokazano, jak skompilować i wdrożyć model, aby mógł być wykorzystany przez Power BI. W następnej części dowiesz się, jak korzystać z tego modelu w raporcie Power BI.

> [!div class="nextstepaction"]
> [Samouczek: korzystanie z modelu w Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
