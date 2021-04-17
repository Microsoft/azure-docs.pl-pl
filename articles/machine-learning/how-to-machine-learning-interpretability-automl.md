---
title: Objaśnienia w zautomatyzowanym u mle (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki zautomatyzowany model uczenia maszynowego określa ważność funkcji i tworzy przewidywania podczas korzystania z zestawu Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 3258a1d53c4aa5010758bcd93ef32c53611f4684
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576469"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Interpretowanie: omówienie modelu w zautomatyzowanym uczeniu maszynowym (wersja zapoznawcza)


Z tego artykułu dowiesz się, jak uzyskać wyjaśnienia dotyczące zautomatyzowanego uczenia maszynowego (zautomatyzowanego uczenia maszynowego) w języku Azure Machine Learning użyciu zestawu SDK języka Python. Zautomatyzowane uczenia maszynowego pomaga zrozumieć znaczenie funkcji generowanych modeli. 

Wszystkie wersje zestawu SDK po wersji 1.0.85 `model_explainability=True` są domyślnie ustawione. W zestawie SDK w wersji 1.0.85 i wcześniejszych wersjach użytkownicy muszą ustawić w obiekcie , aby korzystać z `model_explainability=True` `AutoMLConfig` możliwości interpretacji modelu. 


W tym artykule omówiono sposób wykonywania następujących zadań:

- Wykonuj możliwości interpretowania podczas trenowania dla najlepszego modelu lub dowolnego modelu.
- Włącz wizualizacje, aby ułatwić wyświetlanie wzorców w danych i wyjaśnieniach.
- Implementowanie możliwości interpretacji podczas wnioskowania lub oceniania.

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcje możliwości interpretacji. Uruchom `pip install azureml-interpret` , aby uzyskać niezbędny pakiet.
- Wiedza na temat tworzenia eksperymentów zautomatyzowanego uczenia maszynowego. Aby uzyskać więcej informacji na temat używania zestawu SDK Azure Machine Learning, ukończ ten samouczek modelu regresji lub zobacz, jak skonfigurować [](tutorial-auto-train-models.md) [eksperymenty zautomatyzowanego uczenia maszynowego.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>Możliwości interpretowania podczas trenowania najlepszego modelu

Pobierz wyjaśnienie z pliku , który zawiera wyjaśnienia dotyczące zarówno cech pierwotnych, jak `best_run` i cech inżynierowanych.

> [!NOTE]
> Możliwości interpretowania, najlepsze wyjaśnienie modelu, nie są dostępne dla eksperymentów prognozowania automatycznego uczenia maszynowego, które zalecają następujące algorytmy jako najlepszy model: 
> * TCNForecaster
> * Funkcja autoaryma
> * ExponentialSmoothing
> * Proroka
> * Średnia 
> * Naiwne
> * Średnia sezonowa 
> * Sezonowa naiwna

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Pobieranie ważności zaprojektowanych funkcji z najlepszego uruchomienia

Możesz użyć elementu , aby pobrać inżynierowane wyjaśnienia funkcji z `ExplanationClient` magazynu artefaktów obiektu `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Pobieranie pierwotnych ważności funkcji z najlepszego uruchomienia

Możesz użyć elementu `ExplanationClient` , aby pobrać nieprzetworzone wyjaśnienia funkcji z magazynu artefaktów obiektu `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Możliwości interpretowania podczas trenowania dla dowolnego modelu 

Gdy obliczasz wyjaśnienia modelu i wizualizujesz je, nie musisz ograniczać się do istniejącego wyjaśnienia modelu autoML. Możesz również uzyskać objaśnienie dla modelu z różnymi danymi testowym. Kroki opisane w tej sekcji pokazują, jak obliczać i wizualizować znaczenie funkcji inżynierowanych na podstawie danych testowych.

### <a name="retrieve-any-other-automl-model-from-training"></a>Pobieranie dowolnego innego modelu autoML ze szkolenia

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Konfigurowanie wyjaśnień modelu

Użyj `automl_setup_model_explanations` funkcji , aby uzyskać inżynierowane i nieprzetworzone wyjaśnienia. Element `fitted_model` może generować następujące elementy:

- Polecane dane z przeszkolonych lub testowych przykładów
- Listy nazw funkcji inżynierowanych
- Klasy możliwe do znalezienia w kolumnie z etykietami w scenariuszach klasyfikacji

Zawiera `automl_explainer_setup_obj` wszystkie struktury z powyższej listy.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicjowanie objaśnienia Naśladuj dla ważności funkcji

Aby wygenerować wyjaśnienie dla modeli zautomatyzowanego uczenia maszynowego, użyj `MimicWrapper` klasy . Możesz zainicjować program MimicWrapper przy użyciu tych parametrów:

- Obiekt konfiguracji objaśnienia
- Obszar roboczy
- Model zastępczy objaśnienia zautomatyzowanego modelu `fitted_model` uczenia maszynowego

Program MimicWrapper pobiera również obiekt, do którego zostaną przekazane `automl_run` inżynierowane wyjaśnienia.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Używanie objaśnienia naśladującego do przetwarzania i wizualizowania ważności zaprojektowanych funkcji

Możesz wywołać metodę w programie MimicWrapper z przekształcone przykłady testowe, aby uzyskać ważność funkcji dla `explain()` wygenerowanych funkcji inżynierów. Możesz również zalogować się do usługi [Azure Machine Learning studio,](https://ml.azure.com/) aby wyświetlić wizualizację pulpitu nawigacyjnego objaśnień wartości ważności funkcji wygenerowanych przez funkcje inżynierów za pomocą zautomatyzowanych cech uczenia maszynowego.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
W przypadku modeli przeszkolonych za pomocą zautomatyzowanego uczenia maszynowego można uzyskać najlepszy model przy użyciu metody i wyjaśnienia `get_output()` obliczeń lokalnie.  Wyniki wyjaśnienia można zwizualizować za `ExplanationDashboard` pomocą `interpret-community` pakietu .

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Przetwarzanie i wizualizowanie pierwotnej ważności funkcji za pomocą funkcji Wyjaśnij naśladuj

Możesz wywołać metodę `explain()` w programie MimicWrapper z przekształconymi przykładami testów, aby uzyskać ważność funkcji dla nieprzetworzonych cech. W [programie Machine Learning Studio](https://ml.azure.com/)można wyświetlić wizualizację pulpitu nawigacyjnego wartości ważności funkcji pierwotnych.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Możliwości interpretowania podczas wnioskowania

W tej sekcji dowiesz się, jak zautomatyzować model zautomatyzowanego uczenia maszynowego za pomocą objaśnienia użytego do obliczenia wyjaśnień w poprzedniej sekcji.

### <a name="register-the-model-and-the-scoring-explainer"></a>Rejestrowanie modelu i objaśnienia oceniania

Użyj obiektu `TreeScoringExplainer` , aby utworzyć objaśnienia oceniania, które obliczą wartości ważności cech inżynierowanych podczas wnioskowania. Objaśnienia oceniania są inicjowane za pomocą `feature_map` obliczonego wcześniej elementu . 

Zapisz objaśnienia oceniania, a następnie zarejestruj model i objaśnienia oceniania w usłudze Zarządzanie modelami Service. Uruchom następujący kod:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Tworzenie zależności conda w celu skonfigurowania usługi

Następnie utwórz niezbędne zależności środowiska w kontenerze dla wdrożonego modelu. Należy pamiętać, że wartość azureml-defaults w wersji >= 1.0.45 musi być wymieniona jako zależność pip, ponieważ zawiera funkcje potrzebne do hostowania modelu jako usługi internetowej.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Tworzenie skryptu oceniania

Napisz skrypt, który ładuje model oraz generuje przewidywania i wyjaśnienia na podstawie nowej partii danych.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Wdrażanie usługi

Wdobyj usługę przy użyciu pliku conda i pliku oceniania z poprzednich kroków.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Wnioskowanie z danymi testowym

Wnioskowanie z niektórymi danymi testowym w celu zobaczenia przewidywanej wartości z modelu autoML, obecnie obsługiwanej tylko w Azure Machine Learning SDK. Wyświetl znaczenie funkcji, które wpływają na przewidywaną wartość. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Wizualizowanie w celu odnajdywania wzorców w danych i objaśnień w czasie trenowania

Wykres ważności funkcji można zwizualizować w obszarze roboczym [w](https://ml.azure.com)Azure Machine Learning studio . Po zakończeniu uruchamiania automl wybierz pozycję **Wyświetl szczegóły modelu,** aby wyświetlić określone uruchomienie. Wybierz **kartę Wyjaśnienia,** aby wyświetlić wizualizacje na pulpicie nawigacyjnym wyjaśnienia.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Aby uzyskać więcej informacji na temat wizualizacji pulpitu nawigacyjnego z wyjaśnieniem i określonych wykresów, zapoznaj się z [how-to-doc on interpretability](how-to-machine-learning-interpretability-aml.md)(Opis możliwości interpretacji).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat włączania objaśnień modelu i ważności funkcji w obszarach zestawu SDK usługi Azure Machine Learning innych niż zautomatyzowane uczenie maszynowe, zobacz artykuł koncepcyjny na temat [możliwości interpretowania](how-to-machine-learning-interpretability.md).
