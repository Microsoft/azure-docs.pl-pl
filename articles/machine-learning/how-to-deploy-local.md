---
title: Jak uruchamiać i wdrażać lokalnie
titleSuffix: Azure Machine Learning
description: W tym artykule opisano, jak używać komputera lokalnego jako celu do szkolenia, debugowania lub wdrażania modeli utworzonych w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: a7d1212d1106f0883d05a860b498b90e4e5f8e00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517518"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Wdrażanie modeli szkolonych za pomocą Azure Machine Learning na maszynach lokalnych 

W tym artykule opisano sposób używania komputera lokalnego jako celu do szkolenia lub wdrażania modeli utworzonych w Azure Machine Learning. Azure Machine Learning jest wystarczająco elastyczny, aby można było korzystać z większości platform uczenia maszynowego w języku Python. Rozwiązania do uczenia maszynowego zazwyczaj mają złożone zależności, które mogą być trudne do duplikowania. W tym artykule pokazano, jak zrównoważyć całkowitą kontrolę z łatwością użytkowania.

Scenariusze wdrożenia lokalnego obejmują:

* Wczesne Iterowanie danych, skryptów i modeli w projekcie.
* Debugowanie i rozwiązywanie problemów w późniejszych etapach.
* Ostateczne wdrożenie na sprzęcie zarządzanym przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).
- Model i środowisko. Jeśli nie masz przeszkolonego modelu, możesz użyć modelu i plików zależności, które są dostępne w [tym samouczku](tutorial-train-models-with-aml.md).
- [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/intro).
- Conda Manager, na przykład Anaconda lub Miniconda, jeśli chcesz dublować Azure Machine Learning zależności pakietów.
- Docker, jeśli chcesz używać kontenera Azure Machine Learning środowiska.

## <a name="prepare-your-local-machine"></a>Przygotowywanie komputera lokalnego

Najbardziej niezawodnym sposobem na lokalnie uruchomienie modelu Azure Machine Learning jest obraz platformy Docker. Obraz platformy Docker zapewnia odizolowane, wielokontenerowe środowisko, które duplikaty, z wyjątkiem problemów sprzętowych, środowiska wykonywania platformy Azure. Aby uzyskać więcej informacji na temat instalowania i konfigurowania platformy Docker for Development, zobacz [Omówienie zdalnego tworzenia aplikacji platformy Docker w systemie Windows](/windows/dev-environment/docker/overview).

Istnieje możliwość dołączenia debugera do procesu działającego w platformie Docker. (Zobacz [dołączanie do uruchomionego kontenera](https://code.visualstudio.com/docs/remote/attach-container)). Ale warto debugować i powtarzać kod języka Python bez obejmuje platformy Docker. W tym scenariuszu należy pamiętać, że komputer lokalny używa tych samych bibliotek, które są używane podczas uruchamiania eksperymentu w Azure Machine Learning. Aby zarządzać zależnościami języka Python, platforma Azure używa [Conda](https://docs.conda.io/). Możesz ponownie utworzyć środowisko przy użyciu innych menedżerów pakietów, ale Instalowanie i Konfigurowanie Conda na komputerze lokalnym jest najprostszym sposobem synchronizacji. 

## <a name="prepare-your-entry-script"></a>Przygotowywanie skryptu wprowadzania

Nawet jeśli używasz platformy Docker do zarządzania modelem i zależnościami, skrypt oceniania języka Python musi być lokalny. Skrypt musi mieć dwie metody:

- `init()`Metoda, która nie przyjmuje argumentów i zwraca wartość Nothing 
- `run()`Metoda, która pobiera ciąg w formacie JSON i zwraca obiekt możliwy do serializacji JSON

Argument `run()` metody będzie w tej formie: 

```json
{
    "data": <model-specific-data-structure>
}
```

Obiekt, który zwracasz z `run()` metody, musi implementować `toJSON() -> string` .

Poniższy przykład ilustruje sposób ładowania zarejestrowanego modelu scikit-Dowiedz się i oceny przy użyciu danych NumPy. Ten przykład bazuje na modelu i zależnościach [tego samouczka](tutorial-train-models-with-aml.md).

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Aby uzyskać bardziej zaawansowane przykłady, w tym automatyczne generowanie schematów Swagger i dane binarne danych wyjściowych (na przykład obrazy), zobacz [Zaawansowane Tworzenie skryptów wprowadzania](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Wdrażanie jako lokalnej usługi sieci Web przy użyciu platformy Docker

Najprostszym sposobem replikowania środowiska używanego przez Azure Machine Learning jest wdrożenie usługi sieci Web przy użyciu platformy Docker. Przy użyciu platformy Docker działającej na komputerze lokalnym:

1. Nawiąż połączenie z obszarem roboczym Azure Machine Learning, w którym jest zarejestrowany model.
1. Utwórz `Model` obiekt, który reprezentuje model.
1. Utwórz `Environment` obiekt, który zawiera zależności i definiuje środowisko oprogramowania, w którym zostanie uruchomiony kod.
1. Utwórz `InferenceConfig` obiekt, który kojarzy skrypt wprowadzania z `Environment` .
1. Utwórz `DeploymentConfiguration` obiekt podklasy `LocalWebserviceDeploymentConfiguration` .
1. Użyj `Model.deploy()` , aby utworzyć `Webservice` obiekt. Ta metoda pobiera obraz platformy Docker i kojarzy go z `Model` , `InferenceConfig` i `DeploymentConfiguration` .
1. Aktywuj program przy `Webservice` użyciu polecenia `Webservice.wait_for_deployment()` .

Poniższy kod przedstawia następujące kroki:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

Wywołanie `Model.deploy()` może potrwać kilka minut. Po wstępnym wdrożeniu usługi sieci Web bardziej wydajne jest użycie `update()` metody zamiast od podstaw. Zobacz [Aktualizowanie wdrożonej usługi sieci Web](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Testowanie lokalnego wdrożenia

Po uruchomieniu poprzedniego skryptu wdrożenia będzie on wyprowadzał identyfikator URI, do którego można publikować dane do oceny (na przykład `http://localhost:6789/score` ). Poniższy przykład przedstawia skrypt, który ocenia przykładowe dane przy użyciu `"sklearn-mnist-local"` modelu wdrożonego lokalnie. Model, jeśli został prawidłowo przeszkolony, wniosku, który `normalized_pixel_values` powinien być interpretowany jako "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Pobierz i uruchom model bezpośrednio

Użycie platformy Docker do wdrożenia modelu jako usługi sieci Web to najbardziej typowa opcja. Ale możesz chcieć uruchomić kod bezpośrednio, używając lokalnych skryptów języka Python. Potrzebne są dwa ważne składniki: 

- Sam model
- Zależności, na których bazuje model 

Możesz pobrać model:  

- W portalu, wybierając kartę **modele** , wybierając odpowiedni model i na stronie **szczegółów** wybierz pozycję **Pobierz**.
- Z wiersza polecenia przy użyciu `az ml model download` . (Zobacz artykuł [pobieranie modelu).](/cli/azure/ext/azure-cli-ml/ml/model#ext_azure_cli_ml_az_ml_model_download)
- Za pomocą metody zestawu SDK języka Python `Model.download()` . (Zobacz [model klasy).](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-)

Model platformy Azure to jeden lub więcej serializowanych obiektów języka Python, spakowanych jako plik pakietu pickle języka Python (rozszerzenie PKL). Zawartość pliku pakietu pickle zależy od biblioteki lub techniki uczenia maszynowego używanej do uczenia modelu. Na przykład jeśli korzystasz z modelu z tego samouczka, możesz załadować model z:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Zależności są zawsze trudne do uzyskania, szczególnie w przypadku uczenia maszynowego, w którym często może być dizzying w sieci Web o konkretnych wymaganiach dotyczących wersji. Środowisko Azure Machine Learning można utworzyć ponownie na komputerze lokalnym jako kompletne środowisko Conda lub jako obraz platformy Docker przy użyciu `build_local()` metody `Environment` klasy: 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Jeśli ustawisz `build_local()` `useDocker` argument na `True` , funkcja utworzy obraz platformy Docker, a nie środowisko Conda. Jeśli potrzebujesz większej kontroli, możesz użyć `save_to_directory()` metody `Environment` , która zapisuje conda_dependencies. yml i azureml_environment.jsw plikach definicji, które można dostrajać i stosować jako podstawę dla rozszerzenia. 

`Environment`Klasa ma wiele innych metod synchronizowania środowisk w ramach sprzętu obliczeniowego, obszaru roboczego platformy Azure i obrazów platformy Docker. Aby uzyskać więcej informacji, zobacz [Klasa środowiska](/python/api/azureml-core/azureml.core.environment(class)).

Po pobraniu modelu i rozwiązaniu jego zależności nie ma żadnych zdefiniowanych przez platformę Azure ograniczeń dotyczących sposobu wykonywania oceniania, dostrajania modelu, używania uczenia transferowego itd. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Przekaż ponownie przemieszczony model do Azure Machine Learning

Jeśli masz lokalnie nauczone lub przeszkolony model, możesz zarejestrować go na platformie Azure. Po jego zarejestrowaniu możesz kontynuować dostosowywanie go przy użyciu usługi Azure COMPUTE lub wdrożyć ją przy użyciu obiektów platformy Azure, takich jak [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) lub [Triton wnioskowanie (wersja zapoznawcza)](how-to-deploy-with-triton.md).

Aby można było korzystać z zestawu SDK języka Python Azure Machine Learning, model musi być przechowywany jako serializowany obiekt Python w formacie pakietu pickle (plik. PKL). Należy również zaimplementować `predict(data)` metodę, która zwraca obiekt możliwy do serializacji JSON. Można na przykład przechowywać lokalnie przeszkolony model scikit — uczenie cukrzycą z: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Aby udostępnić model na platformie Azure, możesz użyć `register()` metody `Model` klasy:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Następnie można znaleźć nowo zarejestrowany model na karcie **model** Azure Machine Learning:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Zrzut ekranu przedstawiający kartę Azure Machine Learning model, który zawiera przekazany model.":::

Aby uzyskać więcej informacji na temat przekazywania i aktualizowania modeli i środowisk, zobacz artykuł [Rejestrowanie modelu i wdrażanie lokalnie przy użyciu zaawansowanych zastosowań](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zarządzania środowiskami, zobacz [tworzenie & używanie środowisk oprogramowania w programie Azure Machine Learning](how-to-use-environments.md).
- Aby dowiedzieć się więcej o uzyskiwaniu dostępu do danych ze sklepu datastore, zobacz [nawiązywanie połączenia z usługami magazynu na platformie Azure](how-to-access-data.md).
