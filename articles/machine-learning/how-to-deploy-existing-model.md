---
title: Używanie i wdrażanie istniejących modeli
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przenieść modele ML do chmury platformy Azure, korzystając z Azure Machine Learning. Następnie należy wdrożyć model jako usługę sieci Web lub moduł IoT.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 46b8f153e65f436fa1062a0606e0fb0136d972a5
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97824600"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Wdróż istniejący model przy użyciu Azure Machine Learning


W tym artykule dowiesz się, jak zarejestrować i wdrożyć model uczenia maszynowego, który został przeszkolony poza Azure Machine Learning. Można wdrożyć jako usługę sieci Web lub urządzenie IoT Edge.  Po wdrożeniu można monitorować model i wykrywać dryfowanie danych w Azure Machine Learning. 

Aby uzyskać więcej informacji na temat pojęć i warunków zawartych w tym artykule, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md)
  + W przykładach języka Python założono, że `ws` zmienna jest ustawiona na obszar roboczy Azure Machine Learning. Aby uzyskać więcej informacji na temat łączenia się z obszarem roboczym, zapoznaj się z [dokumentacją zestawu Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=trueworkspace).
  
  + Przykłady interfejsu wiersza polecenia używają symboli zastępczych `myworkspace` i `myresourcegroup` , które należy zamienić na nazwę obszaru roboczego i grupę zasobów, która go zawiera.

* [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).  

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) i [Machine Learning rozszerzenia interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md).

* Model szkolony. Model musi być utrwalony w co najmniej jednym pliku w środowisku deweloperskim. <br><br>W celu zaprezentowania rejestrowania modelu przeszkolonego przykładowy kod w tym artykule używa modeli z [projektu Paolo Ripamonti usługi Twitter tonacji Analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Zarejestruj modele

Zarejestrowanie modelu umożliwia przechowywanie, wersję i śledzenie metadanych na temat modeli w obszarze roboczym. W poniższych przykładach języka Python i interfejsu wiersza polecenia `models` katalog zawiera `model.h5` `model.w2v` pliki,, `encoder.pkl` i `tokenizer.pkl` . Ten przykład przekazuje pliki zawarte w `models` katalogu jako nową rejestrację modelu o nazwie `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Aby uzyskać więcej informacji, zobacz Dokumentacja [model. Register ()](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Można również ustawić obiekty Add `tags` i `properties` dictionary na zarejestrowany model. Te wartości mogą być używane później, aby ułatwić identyfikację określonego modelu. Na przykład używana struktura, parametry szkoleniowe itp.

Aby uzyskać więcej informacji, zobacz sekcję [AZ ml model Register](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) Reference.


Aby uzyskać więcej informacji na temat rejestracji modelu ogólnie, zobacz artykuł [Zarządzanie, wdrażanie i monitorowanie modeli uczenia maszynowego](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania definiuje środowisko używane do uruchamiania wdrożonego modelu. Konfiguracja wnioskowania odwołuje się do następujących jednostek, które są używane do uruchamiania modelu podczas jego wdrażania:

* Skrypt wejścia o nazwie `score.py` ładuje model podczas uruchamiania wdrożonej usługi. Ten skrypt jest również odpowiedzialny za otrzymywanie danych, przekazywanie go do modelu, a następnie zwracanie odpowiedzi.
* [Środowisko](how-to-use-environments.md)Azure Machine Learning. Środowisko definiuje zależności oprogramowania wymagające do uruchomienia modelu i skryptu wejścia.

Poniższy przykład pokazuje, jak utworzyć środowisko przy użyciu zestawu SDK, a następnie użyć go z konfiguracją wnioskowania:

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
+ Odwołanie [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) .


Interfejs wiersza polecenia ładuje konfigurację wnioskowania z pliku YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

W interfejsie wiersza polecenia środowisko Conda jest zdefiniowane w `myenv.yml` pliku, do którego odwołuje się konfiguracja wnioskowania. Następujący YAML to zawartość tego pliku:

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

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Skrypt wprowadzania (score.py)

Skrypt wejścia ma tylko dwie wymagane funkcje `init()` i `run(data)` . Te funkcje są używane do inicjowania usługi przy uruchamianiu i uruchamiania modelu przy użyciu danych żądania przekazaną przez klienta. Pozostała część skryptu obsługuje ładowanie i uruchamianie modeli.

> [!IMPORTANT]
> Brak ogólnego skryptu wejścia, który działa dla wszystkich modeli. Jest zawsze specyficzny dla modelu, który jest używany. Musi on zrozumieć sposób ładowania modelu, format danych, którego oczekuje model, oraz sposób oceny danych przy użyciu modelu.

Poniższy kod w języku Python jest przykładowym skryptem wprowadzania ( `score.py` ):

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

Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiowanie wdrożenia

Pakiet usługi [WebService](/python/api/azureml-core/azureml.core.webservice?preserve-view=true&view=azure-ml-py) zawiera klasy używane do wdrożenia. Używana Klasa określa, gdzie model jest wdrażany. Na przykład, aby wdrożyć jako usługę sieci Web w usłudze Azure Kubernetes, użyj [AksWebService.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) , aby utworzyć konfigurację wdrożenia.

Poniższy kod w języku Python definiuje konfigurację wdrożenia dla lokalnego wdrożenia. Ta konfiguracja wdraża model jako usługę sieci Web na komputerze lokalnym.

> [!IMPORTANT]
> Wdrożenie lokalne wymaga działającej instalacji [platformy Docker](https://www.docker.com/) na komputerze lokalnym:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Aby uzyskać więcej informacji, zobacz informacje o [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.localwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-port-none-) .

Interfejs wiersza polecenia ładuje konfigurację wdrożenia z pliku YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Wdrażanie w innym miejscu docelowym obliczeniowym, takim jak usługa Azure Kubernetes w chmurze platformy Azure, jest tak proste jak zmiana konfiguracji wdrożenia. Aby uzyskać więcej informacji, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Poniższy przykład ładuje informacje z zarejestrowanego modelu o nazwie `sentiment` , a następnie wdraża je jako usługę o nazwie `sentiment` . Podczas wdrażania, konfiguracja i konfiguracja wdrożenia są używane do tworzenia i konfigurowania środowiska usługi:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Aby uzyskać więcej informacji, zobacz Dokumentacja [model. deploy ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) .

Aby wdrożyć model z interfejsu wiersza polecenia, użyj następującego polecenia. To polecenie wdraża wersję 1 zarejestrowanego modelu ( `sentiment:1` ) przy użyciu konfiguracji wnioskowania i wdrożenia przechowywanej w `inferenceConfig.json` plikach i `deploymentConfig.json` :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) Reference.

Aby uzyskać więcej informacji na temat wdrażania, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Żądanie — użycie odpowiedzi

Po wdrożeniu zostanie wyświetlony identyfikator URI oceniania. Ten identyfikator URI może być używany przez klientów do przesyłania żądań do usługi. Poniższy przykład to prosty klient języka Python, który przesyła dane do usługi i wyświetla odpowiedź:

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

Aby uzyskać więcej informacji na temat korzystania ze wdrożonej usługi, zobacz [Tworzenie klienta](how-to-consume-web-service.md).

## <a name="next-steps"></a>Następne kroki

* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
* [Jak utworzyć klienta dla wdrożonego modelu](how-to-consume-web-service.md)