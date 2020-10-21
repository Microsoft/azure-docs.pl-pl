---
title: Wdrażanie modelu na potrzeby wnioskowania z procesorem GPU
titleSuffix: Azure Machine Learning
description: W tym artykule przedstawiono sposób użycia Azure Machine Learning do wdrożenia modelu uczenia głębokiego z obsługą procesora GPU jako usługi sieci Web. żądania wnioskowania dotyczące usługi i oceny.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 9a18b35ce974aebb38f8f58b892fd93b94f5e3c8
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314799"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Wdrażanie modelu uczenia głębokiego na potrzeby wnioskowania z procesorem GPU


W tym artykule przedstawiono sposób użycia Azure Machine Learning do wdrożenia modelu z obsługą procesora GPU jako usługi sieci Web. Informacje przedstawione w tym artykule opierają się na wdrażaniu modelu w usłudze Azure Kubernetes Service (AKS). Klaster AKS udostępnia zasób procesora GPU, który jest używany przez model do wnioskowania.

Wnioskowanie, lub ocenianie modelu, to faza, w której wdrożony model jest używany do tworzenia prognoz. Korzystanie z procesorów GPU zamiast procesorów oferuje zalety wydajności na wysoce działania równoległego obliczeń.

> [!IMPORTANT]
> W przypadku wdrożeń usług sieci Web wnioskowanie procesora GPU jest obsługiwane tylko w usłudze Azure Kubernetes. W przypadku wnioskowania przy użyciu __potoku uczenia maszynowego__procesory GPU są obsługiwane tylko w przypadku Azure Machine Learning obliczeń. Aby uzyskać więcej informacji na temat używania potoków ML, zobacz [Samouczek: Tworzenie potoku Azure Machine Learning na potrzeby oceniania partii](tutorial-pipeline-batch-scoring-classification.md). 

> [!TIP]
> Chociaż fragmenty kodu w tym artykule korzystają z modelu TensorFlow, można zastosować informacje do dowolnej platformy uczenia maszynowego, która obsługuje procesory GPU.

> [!NOTE]
> Informacje przedstawione w tym artykule dotyczą informacji zawartych w artykule [jak wdrożyć w usłudze Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) . Jeśli ten artykuł ogólnie omawia wdrożenie AKS, ten artykuł obejmuje wdrożenie specyficzne dla procesora GPU.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

* Środowisko programistyczne języka Python z zainstalowanym zestawem SDK Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).  

* Zarejestrowany model, który używa procesora GPU.

    * Aby dowiedzieć się, jak zarejestrować modele, zobacz [Wdrażanie modeli](how-to-deploy-and-where.md#registermodel).

    * Aby utworzyć i zarejestrować model Tensorflow używany do tworzenia tego dokumentu, zobacz [Jak szkolić model Tensorflow](how-to-train-tensorflow.md).

* Ogólne zrozumienie [, jak i gdzie należy wdrażać modele](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Nawiązywanie połączenia z obszarem roboczym

Aby nawiązać połączenie z istniejącym obszarem roboczym, użyj następującego kodu:

> [!IMPORTANT]
> Ten fragment kodu oczekuje, że Konfiguracja obszaru roboczego ma zostać zapisana w bieżącym katalogu lub jego elemencie nadrzędnym. Aby uzyskać więcej informacji na temat tworzenia obszaru roboczego, zobacz [Tworzenie obszarów roboczych i zarządzanie nimi Azure Machine Learning](how-to-manage-workspace.md).   Aby uzyskać więcej informacji na temat zapisywania konfiguracji do pliku, zobacz [Tworzenie pliku konfiguracji obszaru roboczego](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Tworzenie klastra Kubernetes z procesorami GPU

Usługa Azure Kubernetes obsługuje wiele różnych opcji procesora GPU. Dowolnego z nich można użyć do wnioskowania modelu. Zapoznaj [się z listą maszyn wirtualnych serii N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) , aby zapoznać się z pełnym podziałem możliwości i kosztów.

Poniższy kod ilustruje sposób tworzenia nowego klastra AKS dla obszaru roboczego:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Na platformie Azure zostanie naliczona wartość, o ile istnieje klaster AKS. Upewnij się, że klaster AKS został usunięty po zakończeniu pracy z nim.

Aby uzyskać więcej informacji na temat korzystania z programu AKS z Azure Machine Learning, zobacz [jak wdrożyć usługę Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Napisz skrypt wpisu

Skrypt wejścia odbiera dane przesyłane do usługi sieci Web, przekazuje je do modelu i zwraca wyniki oceniania. Poniższy skrypt ładuje model Tensorflow podczas uruchamiania, a następnie używa modelu do oceny danych.

> [!TIP]
> Skrypt wejściowy jest specyficzny dla modelu. Na przykład skrypt musi znać strukturę, która ma być używana z modelem, formatami danych itp.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Ten plik ma nazwę `score.py` . Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [jak i gdzie należy wdrożyć](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Zdefiniuj środowisko Conda

Plik środowiska Conda określa zależności dla usługi. Zawiera zależności wymagane przez model i skrypt wejścia. Należy pamiętać, że należy wskazać wartość domyślną usługi Azure Wersja >= 1.0.45 jako zależność PIP, ponieważ zawiera ona funkcje wymagane do hostowania modelu jako usługi sieci Web. Poniższy YAML definiuje środowisko dla modelu Tensorflow. Określa `tensorflow-gpu` , która spowoduje użycie procesora GPU użytego w tym wdrożeniu:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

Na potrzeby tego przykładu plik jest zapisywany jako `myenv.yml` .

## <a name="define-the-deployment-configuration"></a>Definiowanie konfiguracji wdrożenia

> [!IMPORTANT]
> AKS nie zezwala na współużytkowanie procesorów GPU, ale może mieć tylko tyle replik usługi sieci Web obsługujących procesor GPU, ponieważ w klastrze są dostępne procesory GPU.

Konfiguracja wdrożenia definiuje środowisko usługi Azure Kubernetes, które jest używane do uruchamiania usługi sieci Web:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definiowanie konfiguracji wnioskowania

Konfiguracja wnioskowania wskazuje skrypt wejścia i obiekt środowiska, który używa obrazu platformy Docker z obsługą procesora GPU. Należy pamiętać, że plik YAML używany do definicji środowiska musi zawierać listę usług Azure-Defaults z wersją >= 1.0.45 jako zależność PIP, ponieważ zawiera funkcje wymagane do hostowania modelu jako usługi sieci Web.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).
Aby uzyskać więcej informacji, zobacz dokumentację referencyjną [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true).

## <a name="deploy-the-model"></a>Wdrażanie modelu

Wdróż model w klastrze AKS i poczekaj na utworzenie usługi.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Aby uzyskać więcej informacji, zobacz dokumentację referencyjną dla [modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true).

## <a name="issue-a-sample-query-to-your-service"></a>Wydaj przykładowe zapytanie do usługi

Wyślij zapytanie testowe do wdrożonego modelu. Po wysłaniu obrazu JPEG do modelu ocenia obraz. Poniższy kod przykład pobiera dane testowe, a następnie wybiera losowy obraz testu do wysłania do usługi.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Aby uzyskać więcej informacji na temat tworzenia aplikacji klienckiej, zobacz [Tworzenie klienta programu w celu użycia wdrożonej usługi sieci Web](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Oczyszczanie zasobów

Jeśli klaster AKS został utworzony w ramach tego przykładu, Usuń zasoby po zakończeniu.

> [!IMPORTANT]
> Platforma Azure jest rozliczana na podstawie tego, jak długo wdrożono klaster AKS. Pamiętaj, aby wyczyścić go po zakończeniu pracy z nim.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Następne kroki

* [Wdróż model na FPGA](how-to-deploy-fpga-web-service.md)
* [Wdróż model z ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Uczenie modeli DNN Tensorflow](how-to-train-tensorflow.md)
