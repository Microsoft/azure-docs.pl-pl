---
title: Używanie i wdrażanie istniejących modeli
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przeszkolić modele uczenia maszynowego poza platformą Azure w chmurze platformy Azure przy użyciu Azure Machine Learning. Następnie należy wdrożyć model jako usługę internetową lub moduł IoT.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 46dc9e48ba68189253885ae823457a62c3c4426e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877808"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Wdrażanie istniejącego modelu za pomocą Azure Machine Learning


Z tego artykułu dowiesz się, jak zarejestrować i wdrożyć model uczenia maszynowego wytrenowany poza Azure Machine Learning. Można wdrożyć jako usługę internetową lub na urządzeniu IoT Edge sieci Web.  Po wdrożeniu możesz monitorować model i wykrywać dryf danych w Azure Machine Learning. 

Aby uzyskać więcej informacji na temat pojęć i terminów w tym artykule, zobacz Zarządzanie modelami uczenia [maszynowego,](concept-model-management-and-deployment.md)ich wdrażanie i monitorowanie.

## <a name="prerequisites"></a>Wymagania wstępne

* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md)
  + Przykłady w języku Python `ws` zakładają, że zmienna jest ustawiona na Azure Machine Learning obszaru roboczego. Aby uzyskać więcej informacji na temat nawiązywania połączenia z obszarem roboczym, zapoznaj się z dokumentacją Azure Machine Learning [SDK dla języka Python.](/python/api/overview/azure/ml/#workspace)
  
  + Przykłady interfejsu wiersza polecenia używają symboli zastępczych i , które należy zastąpić nazwą obszaru roboczego i grupą `myworkspace` `myresourcegroup` zasobów, która go zawiera.

* Zestaw [SDK Azure Machine Learning Python.](/python/api/overview/azure/ml/install)  

* Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i Machine Learning [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md).

* Wytrenowany model. Model musi być utrwalony w co najmniej jednym pliku w środowisku dewelopera. <br><br>Aby zademonstrować rejestrowanie wytrenowany model, przykładowy kod w tym artykule korzysta z modeli z projektu analizy tonacji w serwisie [Twitter Paolo Ripamonti.](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)

## <a name="register-the-models"></a>Rejestrowanie modeli

Zarejestrowanie modelu umożliwia przechowywanie, przechowywanie wersji i śledzenie metadanych dotyczących modeli w obszarze roboczym. W poniższych przykładach języka Python i interfejsu wiersza polecenia `models` katalog zawiera pliki , , i `model.h5` `model.w2v` `encoder.pkl` `tokenizer.pkl` . W tym przykładzie pliki zawarte w katalogu zostaną przesłane jako nowa `models` rejestracja modelu o nazwie `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Aby uzyskać więcej informacji, zobacz [informacje dotyczące modelu.register().](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Można również ustawić obiekty `tags` add i `properties` dictionary na zarejestrowany model. Tych wartości można użyć później, aby ułatwić identyfikację określonego modelu. Na przykład używana framework, parametry trenowania itp.

Aby uzyskać więcej informacji, zobacz az ml model register reference [(az ml model register](/cli/azure/ml/model#az_ml_model_register) reference).


Aby uzyskać więcej ogólnych informacji na temat rejestracji modelu, zobacz [Manage, deploy, and monitor machine learning models](concept-model-management-and-deployment.md)(Zarządzanie modelami uczenia maszynowego, ich wdrażanie i monitorowanie).

## <a name="define-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania definiuje środowisko używane do uruchamiania wdrożonego modelu. Konfiguracja wnioskowania odwołuje się do następujących jednostek, które są używane do uruchamiania modelu po jego wdrożeniu:

* Skrypt wejściowy o nazwie `score.py` ładuje model podczas uruchamiania wdrożonej usługi. Ten skrypt jest również odpowiedzialny za odbieranie danych, przekazywanie ich do modelu, a następnie zwracanie odpowiedzi.
* Środowisko Azure Machine Learning [.](how-to-use-environments.md) Środowisko definiuje zależności oprogramowania wymagane do uruchomienia modelu i skryptu wejścia.

W poniższym przykładzie pokazano, jak za pomocą zestawu SDK utworzyć środowisko, a następnie użyć go z konfiguracją wnioskowania:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Aby uzyskać więcej informacji, zobacz następujące artykuły:

+ [Jak używać środowisk](how-to-use-environments.md).
+ [Odwołanie inferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)


Interfejs wiersza polecenia ładuje konfigurację wnioskowania z pliku YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

W interfejsie wiersza polecenia środowisko conda jest definiowane w pliku, do którym `myenv.yml` odwołuje się konfiguracja wnioskowania. Zawartość tego pliku to następujący kod YAML:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz Deploy models with Azure Machine Learning (Wdrażanie [modeli za pomocą Azure Machine Learning](how-to-deploy-and-where.md)).

### <a name="entry-script-scorepy"></a>Skrypt wejściowy (score.py)

Skrypt wejściowy ma tylko dwie wymagane funkcje: `init()` i `run(data)` . Te funkcje są używane do inicjowania usługi podczas uruchamiania i uruchamiania modelu przy użyciu danych żądania przekazywanych przez klienta. Pozostała część skryptu obsługuje ładowanie i uruchamianie modeli.

> [!IMPORTANT]
> Nie istnieje ogólny skrypt wejściowy, który działa dla wszystkich modeli. Jest ona zawsze specyficzna dla używanego modelu. Musi on zrozumieć, jak załadować model, format danych, których oczekuje model, oraz jak liczyć dane przy użyciu modelu.

Poniższy kod w języku Python jest przykładowym skryptem wejściowym ( `score.py` ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Aby uzyskać więcej informacji na temat skryptów wejściowych, zobacz [Wdrażanie modeli za pomocą Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiowanie wdrożenia

Pakiet [usługi sieci](/python/api/azureml-core/azureml.core.webservice) Web zawiera klasy używane do wdrażania. Klasa, której używasz, określa miejsce wdrożenia modelu. Aby na przykład wdrożyć usługę jako usługę internetową na platformie Azure Kubernetes Service, użyj funkcji [AksWebService.deploy_configuration(),](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) aby utworzyć konfigurację wdrożenia.

Poniższy kod w języku Python definiuje konfigurację wdrożenia dla wdrożenia lokalnego. Ta konfiguracja wdraża model jako usługę internetową na komputerze lokalnym.

> [!IMPORTANT]
> Wdrożenie lokalne wymaga roboczej instalacji platformy [Docker na](https://www.docker.com/) komputerze lokalnym:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Aby uzyskać więcej informacji, zobacz [LocalWebservice.deploy_configuration().](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-)

Interfejs wiersza polecenia ładuje konfigurację wdrożenia z pliku YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Wdrażanie do innego docelowego obiektu obliczeniowego, takiego Azure Kubernetes Service w chmurze platformy Azure, jest tak proste, jak zmiana konfiguracji wdrożenia. Aby uzyskać więcej informacji, zobacz How and where to deploy models (Jak [i gdzie wdrażać modele).](how-to-deploy-and-where.md)

## <a name="deploy-the-model"></a>Wdrażanie modelu

Poniższy przykład ładuje informacje do zarejestrowanego modelu o nazwie , a następnie `sentiment` wdraża je jako usługę o nazwie `sentiment` . Podczas wdrażania konfiguracja wnioskowania i konfiguracja wdrożenia są używane do tworzenia i konfigurowania środowiska usługi:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Aby uzyskać więcej informacji, zobacz [informacje dotyczące modelu.deploy().](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Aby wdrożyć model z interfejsu wiersza polecenia, użyj następującego polecenia. To polecenie wdraża wersję 1 zarejestrowanego modelu ( ) przy użyciu konfiguracji wnioskowania i wdrażania `sentiment:1` przechowywanej w `inferenceConfig.json` plikach `deploymentConfig.json` i :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Aby uzyskać więcej informacji, zobacz az ml model deploy reference [(Az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy) reference).

Aby uzyskać więcej informacji na temat wdrażania, zobacz How and where to deploy models (Jak [i gdzie wdrażać modele).](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Zużycie żądań i odpowiedzi

Po wdrożeniu zostanie wyświetlony URI oceniania. Ten URI może być używany przez klientów do przesyłania żądań do usługi. Poniższy przykład to prosty klient języka Python, który przesyła dane do usługi i wyświetla odpowiedź:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Aby uzyskać więcej informacji na temat sposobu korzystać z wdrożonej usługi, zobacz [Tworzenie klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie modeli Azure Machine Learning za pomocą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Jak utworzyć klienta dla wdrożonego modelu](how-to-consume-web-service.md)