---
title: 'Samouczek klasyfikacji obrazów: Wdrażanie modeli'
titleSuffix: Azure Machine Learning
description: W tym samouczku, drugiej części serii dwóch części, pokazano, jak używać Azure Machine Learning do wdrażania modelu klasyfikacji obrazów przy użyciu scikit — uczenie się w notesie Jupyter języka Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: f0d78fc55ca60fa883c742885acf2fa98ede61ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90896582"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>Samouczek: Wdrażanie modelu klasyfikacji obrazów w Azure Container Instances


Ten samouczek jest **drugą częścią dwuczęściowej serii samouczków**. W [poprzednim samouczku](tutorial-train-models-with-aml.md) przeszkoliliśmy modele uczenia maszynowego, a następnie zarejestrowaliśmy model w Twoim obszarze roboczym w chmurze.  Teraz możesz przystąpić do wdrażania modelu jako usługi sieci Web. Usługa internetowa jest obrazem, w tym przypadku obrazem platformy Docker. Hermetyzuje ona logikę oceniania i sam model. 

W tej części samouczka użyjesz Azure Machine Learning dla następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie środowiska testowego.
> * Pobieranie modelu z obszaru roboczego.
> * Wdrażanie modelu w usłudze Container Instances.
> * Testowanie wdrożonego modelu.

Usługa Container Instances to doskonałe rozwiązanie do testowania i interpretowania przepływu pracy. W przypadku skalowalnych wdrożeń produkcyjnych rozważ skorzystanie z usługi Azure Kubernetes Service. Aby uzyskać więcej informacji, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK Azure Machine Learning 1.0.83.

## <a name="prerequisites"></a>Wymagania wstępne

Aby uruchomić Notes, najpierw Ukończ szkolenia modelu w [samouczku (część 1): uczenie modelu klasyfikacji obrazów](tutorial-train-models-with-aml.md).   Następnie Otwórz Notes  *IMG-klasyfikacyjn-part2-Deploy. ipynb* w folderze klony */Klasyfikacja obrazu-mnist ręcznie-Data* .

Ten samouczek jest również dostępny w witrynie [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) , jeśli chcesz używać go w [środowisku lokalnym](how-to-configure-environment.md#local).  Upewnij się, że zainstalowano `matplotlib` i `scikit-learn` w Twoim środowisku. 

> [!Important]
> Pozostała część tego artykułu zawiera tę samą zawartość, która jest wyświetlana w notesie.  
>
> Przełącz się do notesu Jupyter teraz, jeśli chcesz czytać wraz z uruchamianiem kodu.
> Aby uruchomić pojedynczą komórkę kodu w notesie, kliknij komórkę kod i naciśnij **klawisze SHIFT + ENTER**. Lub Uruchom cały Notes, wybierając pozycję **Uruchom wszystkie** z górnego paska narzędzi.

## <a name="set-up-the-environment"></a><a name="start"></a>Konfigurowanie środowiska

Zacznij od skonfigurowania środowiska testowego.

### <a name="import-packages"></a>Importowanie pakietów

Zaimportuj pakiety języka Python potrzebne w tym samouczku.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>Wdrażanie w postaci usługi internetowej

Wdróż model jako usługę sieci Web hostowaną w ACI. 

Aby utworzyć odpowiednie środowisko dla usługi ACI, dostarcz następujące elementy:
* Skrypt oceniania pokazujący, jak korzystać z modelu
* Plik konfiguracji umożliwiający utworzenie usługi ACI
* Przeszkolony wcześniej model

### <a name="create-scoring-script"></a>Tworzenie skryptu oceniania

Utwórz skrypt oceniania o nazwie score.py, który będzie używany przez wywołanie usługi internetowej do zaprezentowania sposobu korzystania z modelu.

W skrypcie oceniania muszą znaleźć się dwie wymagane funkcje:
* Funkcja `init()`, która zazwyczaj ładuje model do obiektu globalnego. Ta funkcja jest uruchamiana tylko raz, po uruchomieniu kontenera platformy Docker. 

* Funkcja `run(input_data)` używa modelu do przewidywania wartości w oparciu o dane wejściowe. Dane wejściowe i wyjściowe do uruchomienia zazwyczaj używają formatu JSON na potrzeby serializacji i deserializacji, ale obsługiwane są też inne formaty.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>Tworzenie pliku konfiguracji

Utwórz plik konfiguracji wdrożenia i określ liczbę procesorów oraz wielkość pamięci RAM (w GB) wymaganej dla kontenera usługi ACI. Chociaż zależy to od modelu, wartość domyślna określająca 1 rdzeń i 1 GB pamięci RAM zazwyczaj jest wystarczająca dla wielu modeli. Jeśli w przyszłości będzie potrzeba więcej, trzeba będzie ponownie utworzyć obraz i jeszcze raz wdrożyć usługę.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>Wdrażanie w usłudze ACI
Szacowany czas do ukończenia: **około 2-5 minut**

Skonfiguruj i wdróż obraz. Poniższy kod wykonuje następujące kroki:

1. Utwórz obiekt środowiska zawierający zależności wymagane przez model przy użyciu środowiska ( `tutorial-env` ) zapisanego podczas szkolenia.
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


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Pobierz punkt końcowy HTTP usługi internetowej oceniania akceptujący wywołania klienta REST. Ten punkt końcowy można udostępnić każdemu, kto chce przetestować usługę internetową lub zintegrować ją z aplikacją.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>Testowanie modelu


### <a name="download-test-data"></a>Pobierz dane testowe
Pobierz dane testowe do katalogu **./Data/**


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>Ładowanie danych testowych

Załaduj dane testowe z katalogu **./Data/** utworzonego podczas samouczka szkoleniowego.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>Przewidywanie danych testowych

Dostarcz do modelu zestaw danych testowych, aby uzyskać przewidywania.


Poniższy kod wykonuje następujące kroki:
1. Wysłanie danych w postaci tablicy JSON do usługi internetowej hostowanej w usłudze ACI. 

1. Wywołanie usługi przy użyciu interfejsu API `run` zestawu SDK. Możesz również wykonać wywołania nieprzetworzone, używając dowolnego narzędzia HTTP, takiego jak curl.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>Badanie macierzy pomyłek

Wygeneruj macierz pomyłek, aby sprawdzić, ile próbek z zestawu testowego zostało poprawnie sklasyfikowanych. Zwróć uwagę na błędnie sklasyfikowane wartości niepoprawnych przewidywań.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

Dane wyjściowe przedstawiają macierz pomyłek:

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

Użyj polecenia `matplotlib`, aby wyświetlić macierz pomyłek w postaci wykresu. Na tym wykresie oś X reprezentuje wartości rzeczywiste, a oś Y — przewidywane. Kolor w każdej siatce oznacza współczynnik błędów. Im jaśniejszy kolor, tym wyższy współczynnik błędów. Na przykład wiele wartości 5 zostało błędnie sklasyfikowanych jako 3. Tak więc zobaczysz jasne siatki pod adresem (5, 3).

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![Wykres przedstawiający macierz pomyłek](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>Pokaż przewidywania

Przetestuj wdrożony model z losową próbką 30 obrazów z danych testowych.  


1. Wydrukowanie zwróconych przewidywań i wykreślenie ich razem z obrazami wejściowymi. Do wyróżniania błędnie sklasyfikowanych próbek służy czerwona czcionka i odwrócony obraz (biały na czarnym tle). 

 Ponieważ dokładność modelu jest wysoka, może być konieczne kilkukrotne uruchomienie poniższego kodu, zanim uda się zobaczyć błędnie sklasyfikowaną próbkę.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

W celu przetestowania usługi internetowej możesz również wysłać nieprzetworzone żądanie HTTP.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby pozostawić grupę zasobów i obszar roboczy na potrzeby innych samouczków i eksploracji, możesz usunąć tylko wdrożenie usługi Container Instances, używając następującego wywołania interfejsu API:

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>Następne kroki

+ Dowiedz się więcej o wszystkich [opcjach wdrażania dla Azure Machine Learning](how-to-deploy-and-where.md).
+ Dowiedz się, jak [tworzyć klientów na potrzeby usługi internetowej](how-to-consume-web-service.md).
+  Asynchronicznie [twórz prognozy dotyczące dużych ilości danych](how-to-use-parallel-run-step.md).
+ Monitoruj swoje modele usługi Azure Machine Learning przy użyciu usługi [Application Insights](how-to-enable-app-insights.md).
+ Wypróbuj samouczek dotyczący [algorytmu automatycznego wyboru](tutorial-auto-train-models.md). 
