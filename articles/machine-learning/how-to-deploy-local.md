---
title: Jak uruchomić i wdrożyć lokalnie
titleSuffix: Azure Machine Learning
description: W tym artykule opisano, jak używać komputera lokalnego jako obiektu docelowego do trenowania, debugowania lub wdrażania modeli utworzonych w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 75836580fc2dc5a2090047865610e26d856387b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861223"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Wdrażanie modeli przeszkolonych za Azure Machine Learning na maszynach lokalnych 

W tym artykule opisano sposób używania komputera lokalnego jako obiektu docelowego do trenowania lub wdrażania modeli utworzonych w Azure Machine Learning. Azure Machine Learning jest wystarczająco elastyczny, aby pracować z większość platform uczenia maszynowego w języku Python. Rozwiązania uczenia maszynowego zwykle mają złożone zależności, które mogą być trudne do zduplikowania. W tym artykule opisano sposób równoważenia całkowitej kontroli z łatwością użycia.

Scenariusze wdrażania lokalnego obejmują:

* Szybkie iterowanie danych, skryptów i modeli na wczesnym etapie projektu.
* Debugowanie i rozwiązywanie problemów w późniejszych etapach.
* Końcowe wdrożenie na sprzęcie zarządzanym przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)
- Model i środowisko. Jeśli nie masz wytrenowany model, możesz użyć plików modelu i zależności podanych w [tym samouczku.](tutorial-train-models-with-aml.md)
- Zestaw [AZURE MACHINE LEARNING SDK dla języka Python.](/python/api/overview/azure/ml/intro)
- Menedżer conda, taki jak Anaconda lub Miniconda, jeśli chcesz dublować Azure Machine Learning pakietów.
- Docker, jeśli chcesz używać konteneryzowanej wersji środowiska Azure Machine Learning operacyjnego.

## <a name="prepare-your-local-machine"></a>Przygotowywanie maszyny lokalnej

Najbardziej niezawodnym sposobem lokalnego uruchamiania modelu Azure Machine Learning jest obraz platformy Docker. Obraz platformy Docker zapewnia izolowane, konteneryzowane środowisko, które duplikuje środowisko wykonawcze platformy Azure z wyjątkiem problemów sprzętowych. Aby uzyskać więcej informacji na temat instalowania i konfigurowania platformy Docker dla scenariuszy testowania, zobacz Overview of Docker remote development on Windows (Omówienie zdalnego tworzenia aplikacji [na platformie Docker w systemie Windows).](/windows/dev-environment/docker/overview)

Do procesu uruchomionego na platformie Docker można dołączyć debuger. (Zobacz [Dołączanie do uruchomionego kontenera).](https://code.visualstudio.com/docs/remote/attach-container) Jednak możesz wolisz debugować i iterować kod w języku Python bez angażowania platformy Docker. W tym scenariuszu ważne jest, aby maszyna lokalna używała tych samych bibliotek, które są używane podczas uruchamiania eksperymentu w Azure Machine Learning. Do zarządzania zależnościami języka Python platforma Azure używa [środowiska conda](https://docs.conda.io/). Środowisko można ponownie utworzyć przy użyciu innych menedżerów pakietów, ale najłatwiejszym sposobem synchronizacji jest zainstalowanie i skonfigurowanie środowiska conda na komputerze lokalnym. 

## <a name="prepare-your-entry-script"></a>Przygotowywanie skryptu wprowadzania

Nawet jeśli używasz platformy Docker do zarządzania modelem i zależnościami, skrypt oceniania języka Python musi być lokalny. Skrypt musi mieć dwie metody:

- Metoda, `init()` która nie przyjmuje argumentów i nie zwraca niczego 
- Metoda, która przyjmuje ciąg w formacie `run()` JSON i zwraca obiekt, który można serializuje w formacie JSON

Argument metody `run()` będzie w tej postaci: 

```json
{
    "data": <model-specific-data-structure>
}
```

Obiekt zwracany z `run()` metody musi implementować metodę `toJSON() -> string` .

W poniższym przykładzie pokazano sposób ładowania zarejestrowanego modelu scikit-learn i oceny go przy użyciu danych biblioteki NumPy. Ten przykład jest oparty na modelu i zależnościach tego [samouczka.](tutorial-train-models-with-aml.md)

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

Aby uzyskać bardziej zaawansowane przykłady, w tym automatyczne generowanie schematów swagger i ocenianie danych binarnych (na przykład obrazów), zobacz Advanced entry script authoring (Zaawansowane tworzenie [skryptów wejściowych).](how-to-deploy-advanced-entry-script.md) 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Wdrażanie jako lokalna usługa internetowa przy użyciu platformy Docker

Najprostszym sposobem replikowania środowiska używanego przez program Azure Machine Learning jest wdrożenie usługi internetowej przy użyciu platformy Docker. Po uruchomieniu platformy Docker na komputerze lokalnym:

1. Połącz się z Azure Machine Learning, w którym zarejestrowano model.
1. Utwórz obiekt `Model` reprezentujący model.
1. Utwórz `Environment` obiekt zawierający zależności i definiujący środowisko oprogramowania, w którym będzie uruchamiany kod.
1. Utwórz `InferenceConfig` obiekt, który kojarzy skrypt wejściowy z `Environment` obiektem .
1. Utwórz `DeploymentConfiguration` obiekt podklasy `LocalWebserviceDeploymentConfiguration` .
1. Użyj `Model.deploy()` , aby utworzyć obiekt `Webservice` . Ta metoda pobiera obraz platformy Docker i kojarzy go z `Model` , `InferenceConfig` i `DeploymentConfiguration` .
1. `Webservice`Aktywuj za pomocą . `Webservice.wait_for_deployment()`

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

Wywołanie wywołania `Model.deploy()` może potrwać kilka minut. Po początkowym wdrożeniu usługi internetowej bardziej efektywne jest użycie metody zamiast `update()` rozpoczynania od podstaw. Zobacz [Aktualizowanie wdrożonej usługi internetowej.](how-to-deploy-update-web-service.md)

### <a name="test-your-local-deployment"></a>Testowanie wdrożenia lokalnego

Po uruchomieniu poprzedniego skryptu wdrażania będzie on wyprowadzać dane URI, do których można wysłać dane POST do oceniania (na przykład `http://localhost:6789/score` ). Poniższy przykład przedstawia skrypt, który ocenia przykładowe dane przy użyciu `"sklearn-mnist-local"` lokalnie wdrożonego modelu. Jeśli model jest odpowiednio wytrenowany, wnioskuje, że należy go `normalized_pixel_values` interpretować jako "2". 

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

## <a name="download-and-run-your-model-directly"></a>Bezpośrednie pobieranie i uruchamianie modelu

Najczęstszą opcją jest wdrożenie modelu jako usługi internetowej przy użyciu platformy Docker. Jednak możesz chcieć uruchomić kod bezpośrednio przy użyciu lokalnych skryptów języka Python. Potrzebne będą dwa ważne składniki: 

- Sam model
- Zależności, na których opiera się model 

Możesz pobrać model:  

- W portalu wybierz kartę **Modele,** wybierz żądany model, a następnie na stronie **Szczegóły** wybierz pozycję **Pobierz.**
- W wierszu polecenia za pomocą polecenia `az ml model download` . (Zobacz [pobieranie modelu.](/cli/azure/ml/model#az_ml_model_download))
- Przy użyciu metody zestawu SDK `Model.download()` języka Python. (Zobacz [Klasa modelu).](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-))

Model platformy Azure to co najmniej jeden serializowany obiekt języka Python spakowany jako plik pickle języka Python (rozszerzenie pkl). Zawartość pliku z pickle zależy od biblioteki uczenia maszynowego lub techniki używanej do trenowania modelu. Jeśli na przykład używasz modelu z samouczka, możesz załadować model za pomocą:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Zależność zawsze jest trudna do uzyskania, szczególnie w przypadku uczenia maszynowego, w którym często może być deniuchem sieci Web o określonych wymaganiach dotyczących wersji. Możesz ponownie utworzyć środowisko Azure Machine Learning lokalnej jako kompletne środowisko conda lub jako obraz platformy Docker przy użyciu `build_local()` `Environment` metody klasy : 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Jeśli ustawisz `build_local()` `useDocker` argument na wartość , funkcja utworzy obraz `True` platformy Docker, a nie środowisko conda. Jeśli chcesz mieć większą kontrolę, możesz użyć metody , która zapisuje pliki `save_to_directory()` conda_dependencies.yml i azureml_environment.jsw plikach definicji, które można dostosować i użyć jako podstawy `Environment` rozszerzenia. 

Klasa ma kilka innych metod synchronizowania środowisk między sprzętem obliczeniowym, obszarem roboczym platformy `Environment` Azure i obrazami platformy Docker. Aby uzyskać więcej informacji, zobacz [Environment, klasa](/python/api/azureml-core/azureml.core.environment(class)).

Po pobraniu modelu i rozwiązaniu jego zależności nie ma żadnych ograniczeń zdefiniowanych przez platformę Azure dotyczących sposobu przeprowadzania oceniania, dostrajania modelu, korzystania z uczenia transferowego itd. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Przekaż ponownie wytrenowany model do Azure Machine Learning

Jeśli masz lokalnie wytrenowany lub ponownie wytrenowany model, możesz zarejestrować go na platformie Azure. Po zarejestrowaniu możesz kontynuować dostrajanie za pomocą usługi Azure Compute lub wdrożyć ją przy użyciu funkcji platformy Azure, takich [jak Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) lub [Triton Inference Server (wersja zapoznawcza).](how-to-deploy-with-triton.md)

Aby można było używać z zestawem SDK języka Python Azure Machine Learning, model musi być przechowywany jako serializowany obiekt języka Python w formacie pickle (plik pkl). Musi również zaimplementować `predict(data)` metodę, która zwraca obiekt z serializowalnym kodem JSON. Na przykład lokalnie wytrenowany model cukrzycy scikit-learn można przechowywać za pomocą: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Aby udostępnić model na platformie Azure, możesz użyć `register()` metody `Model` klasy :

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Nowo zarejestrowany model można następnie znaleźć na karcie Azure Machine Learning **Model:**

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Zrzut ekranu Azure Machine Learning Model przedstawiający przekazany model.":::

Aby uzyskać więcej informacji na temat przekazywania i aktualizowania modeli i środowisk, zobacz Rejestrowanie modelu i wdrażanie [lokalnie z użyciem zaawansowanym.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat zarządzania środowiskami, zobacz [Create & use software environments in Azure Machine Learning](how-to-use-environments.md).
- Aby dowiedzieć się więcej na temat uzyskiwania dostępu do danych z magazynu danych, zobacz [Connect to storage services on Azure (Nawiązywanie połączenia z usługami magazynu na platformie Azure).](how-to-access-data.md)
