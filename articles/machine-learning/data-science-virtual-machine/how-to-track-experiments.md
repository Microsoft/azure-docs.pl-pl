---
title: Śledzenie eksperymentu i wdrażanie modeli
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak śledzić i rejestrować eksperymenty z Data Science Virtual Machine za pomocą Azure Machine Learning i/lub MLFlow.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 205aed1811c3d9d21a10be7bc4f01c73eb7295b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89254804"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Śledź eksperymenty i wdrażaj modele w Azure Machine Learning

Usprawnij proces tworzenia modelu, śledząc eksperymenty i monitorowane metryki. W tym artykule dowiesz się, jak dodać kod rejestrowania do skryptu szkoleniowego za pomocą interfejsu API [MLflow](https://mlflow.org/) i śledzić eksperyment w Azure Machine Learning.

Na poniższym diagramie przedstawiono, że śledzenie MLflow umożliwia śledzenie metryk uruchamiania i modeli magazynu eksperymentu w obszarze roboczym Azure Machine Learning.

![Śledzenie eksperymentów](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz [zainicjować obsługę administracyjną obszar roboczy usługi Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)

## <a name="create-a-new-notebook"></a>Tworzenie nowego notesu

Azure Machine Learning i MLFlow SDK są preinstalowane na Data Science VM i można uzyskać do nich dostęp w środowisku **azureml_py36_ \* ** Conda. W Jupyterlab, kliknij przycisk Uruchom i wybierz następujące jądro:

![wybór jądra](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>Konfigurowanie obszaru roboczego

Przejdź do [Azure Portal](https://portal.azure.com) i wybierz obszar roboczy, który został zainicjowany w ramach wymagań wstępnych. Zobaczysz __config.jspobierania__ (patrz poniżej) — Pobierz konfigurację i upewnij się, że jest ona przechowywana w katalogu roboczym na DSVM.

![Pobierz plik konfiguracji](./media/how-to-track-experiments/experiment-tracking-2.png)

Konfiguracja zawiera informacje, takie jak nazwa obszaru roboczego, subskrypcja itp. i nie jest konieczne, aby nie trzeba było twardych kodów.

## <a name="track-dsvm-runs"></a>Śledź uruchomienia DSVM

Dodaj następujący kod do notesu (lub skryptu), aby ustawić obiekt obszaru roboczego Azure.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
Identyfikator URI śledzenia jest prawidłowy do godziny lub mniej. Jeśli ponownie uruchomisz skrypt po pewnym czasie bezczynności, użyj interfejsu API get_mlflow_tracking_uri, aby uzyskać nowy identyfikator URI.

### <a name="load-the-data"></a>Ładowanie danych

W tym przykładzie używa zestawu danych cukrzycą, dobrze znanego małego zestawu danych, który jest dostarczany z scikit. Ta komórka ładuje zestaw danych i dzieli go na losowe szkolenia i zestawy testów.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Dodaj śledzenie

Dodaj śledzenie eksperymentu przy użyciu zestawu SDK Azure Machine Learning i przekaż utrwalony model do rekordu przebiegu eksperymentu. Poniższy kod dodaje dzienniki i przekazuje plik modelu do przebiegu eksperymentu. Model jest również rejestrowany w rejestrze modelu Azure Machine Learning.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Wyświetl przebiegi w Azure Machine Learning

Możesz wyświetlić przebieg eksperymentu w [Azure Machine Learning Studio](https://ml.azure.com). Kliknij pozycję __eksperymenty__ w menu po lewej stronie i wybierz "experiment_with_mlflow" (lub jeśli zdecydujesz się na zmianę nazwy eksperymentu w powyższym fragmencie kodu, kliknij nazwę użytą):

![Wybierz eksperyment](./media/how-to-track-experiments/mlflow-experiments.png)

Powinien pojawić się komunikat o błędzie z błędami kwadratowymi (MSE):

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

Jeśli klikniesz przebieg, zobaczysz inne szczegóły, a także model wybierany w __dziennikach Output +__

## <a name="deploy-model-in-azure-machine-learning"></a>Wdróż model w Azure Machine Learning

W tej sekcji opisano sposób wdrażania modeli przeszkolonych na DSVM do Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>Krok 1. tworzenie obliczeń wnioskowania

W menu po lewej stronie w programie [Azure Studio](https://ml.azure.com) kliknij pozycję __obliczenia__ , a następnie kartę __klastry wnioskowania__ . Następnie kliknij pozycję __+ Nowy__ , jak opisano poniżej:

![Tworzenie obliczeń wnioskowania](./media/how-to-track-experiments/mlflow-experiments-6.png)

W okienku __nowe informacje o klastrze wnioskowania__ o:

* Nazwa obliczeniowa
* Usługa Kubernetes — wybierz pozycję Utwórz nowe
* Wybierz region
* Wybierz rozmiar maszyny wirtualnej (na potrzeby tego samouczka wartość domyślna Standard_D3_v2 jest wystarczająca)
* Cel klastra — Wybieranie opcji tworzenie __i testowanie__
* Liczba węzłów powinna być równa __1__
* Konfiguracja sieci — podstawowe

Następnie kliknij pozycję __Utwórz__.

![Szczegóły obliczeń](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Krok 2. wdrażanie usługi wnioskowania bez kodu

Po zarejestrowaniu modelu w naszym kodzie przy użyciu programu `register_model` określono strukturę jako skryptu sklearn. Azure Machine Learning nie obsługuje wdrożeń kodu dla następujących platform:

* scikit-learn
* Tensorflow SaveModel
* Format modelu ONNX

Wdrożenie bez kodu oznacza, że można wdrożyć bezpośrednio z artefaktu modelu bez konieczności określania określonego skryptu oceniania.

Aby wdrożyć model cukrzycą, przejdź do menu po lewej stronie w [Azure Machine Learning Studio](https://ml.azure.com) i wybierz pozycję __modele__. Następnie kliknij zarejestrowaną diabetes_model:

![Wybierz model](./media/how-to-track-experiments/mlflow-experiments-3.png)

Następnie kliknij przycisk __Wdróż__ w okienku szczegółów modelu:

![Wdrażanie](./media/how-to-track-experiments/mlflow-experiments-4.png)

Zostanie wdrożony model do klastra wnioskowania (usługa Azure Kubernetes), który został utworzony w kroku 1. Wypełnij poniższe informacje, podając nazwę usługi i nazwę klastra AKS COMPUTE (utworzonego w kroku 1). Zalecamy również zwiększenie __pojemności rezerwy procesora CPU__ do 1 (od 0,1) i __pojemności rezerwowej pamięci__ do 1 (od 0,5) — można zwiększyć ten wzrost, klikając pozycję __Zaawansowane__ i wypełniając szczegóły. Następnie kliknij przycisk __Wdróż__.

![Wdróż szczegóły](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Krok 3. Korzystanie z

Po pomyślnym wdrożeniu modelu powinny zostać wyświetlone następujące elementy (Aby uzyskać dostęp do tej strony, kliknij punkty końcowe w menu po lewej stronie > następnie kliknij nazwę wdrożonej usługi):

![Korzystanie z modelu](./media/how-to-track-experiments/mlflow-experiments-8.png)

Powinieneś zobaczyć, że stan wdrożenia przechodzi od __przejścia__ do __dobrej kondycji__. Ponadto ta sekcja zawiera informacje o punktach końcowych REST i adresach URL programu Swagger, które mogą być używane przez dewelopera aplikacji do integrowania modelu ML z aplikacjami.

Punkt końcowy można przetestować przy użyciu programu [Poster](https://www.postman.com/)lub użyć zestawu Azure SDK:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Krok 4. Czyszczenie

Usuń obliczenia wnioskowania utworzone w kroku 1, tak aby nie były naliczane stałe opłaty za obliczenia. W menu po lewej stronie w Azure Machine Learning Studio kliknij pozycję zasoby obliczeniowe > klastrów > wybierz pozycję obliczenia > Usuń.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wdrażaniu modeli na platformie Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)
