---
title: Wdróż zaszyfrowaną usługę inferencing (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać programu Microsoft SEAL do wdrożenia zaszyfrowanej usługi predykcyjnej na potrzeby klasyfikacji obrazów
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 21c57257f9ce5a33585f151d38c16736f94a166c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91998688"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Jak wdrożyć zaszyfrowaną usługę sieci Web inferencing (wersja zapoznawcza)

Dowiedz się, jak wdrożyć model klasyfikacji obrazów jako zaszyfrowaną usługę sieci Web inferencing w [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Usługa sieci Web jest obrazem kontenera Docker, który zawiera model i logikę oceniania.

W tym przewodniku używasz usługi Azure Machine Learning, aby:

> [!div class="checklist"]
> * Konfigurowanie środowisk
> * Wdróż zaszyfrowaną usługę sieci Web inferencing
> * Przygotowywanie danych testowych
> * Przekształć zaszyfrowane przewidywania
> * Czyszczenie zasobów

ACI to doskonałe rozwiązanie do testowania i analizowania przepływu pracy wdrażania modelu. W przypadku skalowalnych wdrożeń produkcyjnych rozważ skorzystanie z usługi Azure Kubernetes Service. Aby uzyskać więcej informacji, zobacz [jak i gdzie wdrażać modele](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

Metoda szyfrowania użyta w tym przykładzie to [szyfrowanie homomorphic](https://github.com/Microsoft/SEAL#homomorphic-encryption). Szyfrowanie Homomorphic pozwala na przeprowadzanie obliczeń na zaszyfrowanych danych bez konieczności uzyskiwania dostępu do klucza tajnego (odszyfrowywania). Wyniki obliczeń są szyfrowane i mogą być ujawnione tylko przez właściciela klucza tajnego. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku przyjęto założenie, że istnieje model klasyfikacji obrazów zarejestrowany w Azure Machine Learning. W przeciwnym razie Zarejestruj model przy użyciu przedniego [modelu](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) lub Utwórz własny element, wykonując [polecenie uczenie modelu klasyfikacji obrazów przy użyciu Azure Machine Learning samouczka](tutorial-train-models-with-aml.md).

## <a name="configure-local-environment"></a>Konfigurowanie środowiska lokalnego

W notesie Jupyter

1. Zaimportuj pakiety języka Python, które są odpowiednie dla tego przykładu.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Zainstaluj bibliotekę szyfrowania homomorphic dla bezpiecznego inferencing.

    > [!NOTE]
    > `encrypted-inference`Pakiet jest obecnie w wersji zapoznawczej.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) jest biblioteką zawierającą powiązania dla zaszyfrowanych inferencing opartych na [programie Microsoft Seal](https://github.com/Microsoft/SEAL).

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Konfigurowanie środowiska inferencing

Utwórz środowisko dla inferencing i Dodaj `encrypted-inference` pakiet jako zależność Conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Wdróż zaszyfrowaną usługę sieci Web inferencing

Wdróż model jako usługę sieci Web hostowaną w ACI.

Aby utworzyć odpowiednie środowisko dla usługi ACI, dostarcz następujące elementy:

* Skrypt oceniania pokazujący, jak korzystać z modelu
* Plik konfiguracji umożliwiający utworzenie usługi ACI
* Model przeszkolony

### <a name="create-scoring-script"></a>Tworzenie skryptu oceniania

Utwórz skrypt oceniania `score.py` używany przez usługę sieci Web dla inferencing.

W skrypcie oceniania muszą znaleźć się dwie wymagane funkcje:

* Funkcja `init()`, która zazwyczaj ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, po uruchomieniu kontenera platformy Docker.
* Funkcja `run(input_data)` używa modelu do przewidywania wartości w oparciu o dane wejściowe. Dane wejściowe i wyjściowe do uruchomienia zazwyczaj używają formatu JSON na potrzeby serializacji i deserializacji, ale obsługiwane są też inne formaty. Funkcja pobiera klucze publiczne oparte na szyfrowaniu homomorphic, które są przekazywane przez obiekt wywołujący usługi.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Tworzenie pliku konfiguracji

Utwórz plik konfiguracji wdrożenia i określ liczbę procesorów oraz wielkość pamięci RAM (w GB) wymaganej dla kontenera usługi ACI. Chociaż zależy to od modelu, wartość domyślna określająca 1 rdzeń i 1 GB pamięci RAM zazwyczaj jest wystarczająca dla wielu modeli. Jeśli w przyszłości będzie potrzeba więcej, trzeba będzie ponownie utworzyć obraz i jeszcze raz wdrożyć usługę.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Wdrażanie w usłudze Azure Container Instances

Szacowany czas do ukończenia: **około 2-5 minut**

Skonfiguruj i wdróż obraz. Poniższy kod wykonuje następujące kroki:

1. Utwórz obiekt środowiska zawierający zależności wymagane przez model przy użyciu pliku środowiska ( `myenv.yml` )
1. Utwórz konfigurację wnioskowania, która jest niezbędna do wdrożenia modelu jako usługi sieci Web przy użyciu:
   * Plik oceniania (`score.py`)
   * Obiekt środowiska utworzony w poprzednim kroku
1. Wdróż model w kontenerze ACI.
1. Pobranie punktu końcowego HTTP usługi internetowej.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Pobierz punkt końcowy HTTP usługi internetowej oceniania akceptujący wywołania klienta REST. Ten punkt końcowy można udostępnić każdemu, kto chce przetestować usługę internetową lub zintegrować ją z aplikacją.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Przygotowywanie danych testowych

1. Pobierz dane testowe. W takim przypadku jest on zapisywany w katalogu o nazwie *dane*.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Załaduj dane testowe z katalogu *danych* .

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Przekształć zaszyfrowane przewidywania

Użyj zestawu danych testowych z modelem, aby uzyskać przewidywania.

Aby wykonać zaszyfrowane przewidywania:

1. Utwórz nowego `EILinearRegressionClient` klienta opartego na szyfrowaniu homomorphic oraz klucze publiczne.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Przekaż klucze publiczne wygenerowane przez szyfrowanie homomorphic do domyślnego magazynu obiektów BLOB obszaru roboczego. Pozwoli to udostępnić klucze na serwerze wnioskowania.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Szyfruj dane testowe

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Użyj interfejsu API zestawu SDK, `run` Aby wywołać usługę i udostępnić testowy zestaw danych dla modelu w celu uzyskania prognoz. Będziemy musieli wysłać parametry połączenia do magazynu obiektów blob, w którym zostały przekazane klucze publiczne.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Użyj klienta programu, aby odszyfrować wyniki.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń usługę sieci Web utworzoną w tym przykładzie:

```python
service.delete()
```

Jeśli nie planujesz już korzystać z utworzonych zasobów platformy Azure, usuń je. Zapobiega to naliczaniu opłat za niewykorzystane zasoby, które nadal działają. Zapoznaj się z tym przewodnikiem dotyczącym [czyszczenia zasobów](how-to-manage-workspace.md#clean-up-resources) , aby dowiedzieć się więcej.
