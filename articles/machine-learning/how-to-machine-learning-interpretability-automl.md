---
title: Wyjaśnienie w zautomatyzowanej ML (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki twój zautomatyzowany model ML określa ważność funkcji i wykonuje przewidywania przy użyciu zestawu SDK Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 709c85bed4a028c6c168c79cd9fffd6b7b40cb68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100008047"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Interpretowanie: omówienie modelu w zautomatyzowanym uczeniu maszynowym (wersja zapoznawcza)



W tym artykule dowiesz się, jak uzyskać wyjaśnienia dotyczące automatycznego uczenia maszynowego (AutoML) w Azure Machine Learning. AutoML pomaga zrozumieć ważność funkcji generowanych modeli. 

Wszystkie wersje zestawu SDK po 1.0.85 są `model_explainability=True` domyślnie ustawione. W wersji zestawu SDK 1.0.85 i starszych wersji użytkownicy muszą ustawić `model_explainability=True` w obiekcie, aby `AutoMLConfig` można było użyć interpretera modelu. 

W tym artykule omówiono sposób wykonywania następujących zadań:

- Przeprowadzaj interpretację podczas szkolenia dla najlepszego modelu lub modelu.
- Włącz wizualizacje, aby ułatwić Wyświetlanie wzorców danych i objaśnień.
- Implementowanie interpretacji podczas wnioskowania lub oceniania.

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcje interpretacji. Uruchom, `pip install azureml-interpret` Aby pobrać wymagany pakiet.
- Wiedza o tworzeniu eksperymentów AutoML. Aby uzyskać więcej informacji na temat używania zestawu SDK Azure Machine Learning, wykonaj ten [samouczek model regresji](tutorial-auto-train-models.md) lub Zobacz, jak [skonfigurować eksperymenty AutoML](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretacja dla najlepszego modelu

Pobierz wyjaśnienie z programu, w tym `best_run` wyjaśnienia dotyczące zarówno nieprzetworzonych, jak i przetworzonych funkcji.

> [!NOTE]
> Interpretacja, najlepsze wyjaśnienie modelu nie jest dostępne dla eksperymentów prognozowania samochodów, które zalecają następujące algorytmy jak najlepszy model: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * Średnia 
> * Algorytm Bayesa
> * Średnia sezonowa 
> * Algorytm Bayesa sezonowe

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Pobieranie zaprojektowanych znaczenia funkcji z najlepszego przebiegu

Za pomocą `ExplanationClient` programu można pobrać z magazynu artefaktów `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Pobierz surowe znaczenia funkcji z najlepszego przebiegu

Za pomocą `ExplanationClient` programu można pobrać nieprzetworzone wyjaśnienia funkcji z magazynu artefaktów `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretowanie w ramach szkolenia dla dowolnego modelu 

Podczas obliczania objaśnień modelu i wizualizacji są one nieograniczone do istniejącego wyjaśnienia modelu dla modelu AutoML. Możesz również uzyskać wyjaśnienie dla modelu z różnymi danymi testowymi. Kroki opisane w tej sekcji pokazują, jak obliczyć i wizualizować znaczenie funkcji na podstawie danych testowych.

### <a name="retrieve-any-other-automl-model-from-training"></a>Pobierz inny model AutoML z szkoleń

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Konfigurowanie wyjaśnień modelu

Służy `automl_setup_model_explanations` do uzyskiwania przetworzonych i nieprzetworzonych wyjaśnień. `fitted_model`Może generować następujące elementy:

- Polecane dane z próbek przeszkolonych lub testowych
- Zaprojektowane listy nazw funkcji
- Klasy odnalezione w kolumnie oznaczonej etykietą w scenariuszach klasyfikacji

`automl_explainer_setup_obj`Zawiera wszystkie struktury z powyższej listy.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Zainicjuj objaśnienie śladowe pod kątem ważności funkcji

Aby wygenerować wyjaśnienie dla modeli AutoML, użyj `MimicWrapper` klasy. Możesz zainicjować MimicWrapper z następującymi parametrami:

- Obiekt konfiguracji programu wyjaśniającego
- Twój obszar roboczy
- Model zastępczy do wyjaśnienia `fitted_model` modelu AutoML

MimicWrapper pobiera również obiekt, `automl_run` w którym zostaną przekazane przetworzone wyjaśnienia.

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

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Korzystaj z objaśnień śladowych dla obliczeń i wizualizacji wagi funkcji

Możesz wywołać `explain()` metodę w MimicWrapper z przekształconymi próbkami testowymi, aby uzyskać ważność funkcji dla wygenerowanych funkcji. Możesz również zalogować się do [Azure Machine Learning Studio](https://ml.azure.com/) , aby wyświetlić wizualizację pulpitu nawigacyjnego wartości znaczenia funkcji dla wygenerowanych funkcji przez AutoML featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Korzystanie z objaśnień do obliczania i wizualizacji ważności nieprzetworzonej funkcji

Możesz wywołać `explain()` metodę w MimicWrapper z przekształconymi próbkami testowymi, aby uzyskać ważność funkcji dla nieprzetworzonych funkcji. W [Machine Learning Studio](https://ml.azure.com/)można wyświetlić wizualizację pulpitu nawigacyjnego wartości znaczenia funkcji nieprzetworzonych funkcji.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Interpretowanie podczas wnioskowania

W tej sekcji dowiesz się, jak operacjonalizować model AutoML z wyjaśnieniem użytym do obliczenia wyjaśnień w poprzedniej sekcji.

### <a name="register-the-model-and-the-scoring-explainer"></a>Zarejestruj model i wyjaśnienie oceniania

Użyj, `TreeScoringExplainer` Aby utworzyć program do oceny oceniania, który będzie obliczać wartości znaczenia funkcji w czasie wnioskowania. Należy zainicjować wyjaśnienie oceniania przy użyciu `feature_map` , który został wcześniej obliczony. 

Zapisz wyjaśnienie oceniania, a następnie zarejestruj model i wyjaśnienie oceniania przy użyciu usługi Zarządzanie modelami. Uruchom następujący kod:

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Tworzenie zależności Conda na potrzeby konfigurowania usługi

Następnie utwórz wymagane zależności środowiska w kontenerze dla wdrożonego modelu. Należy zwrócić uwagę, że usługa Azure-Defaults z wersją >= 1.0.45 musi być wymieniona jako zależność PIP, ponieważ zawiera funkcje wymagane do hostowania modelu jako usługi sieci Web.

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

### <a name="create-the-scoring-script"></a>Utwórz skrypt oceniania

Napisz skrypt ładujący model i generuje prognozy i wyjaśnienia na podstawie nowej partii danych.

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

Wdróż usługę przy użyciu pliku Conda i pliku oceniania z poprzednich kroków.

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

### <a name="inference-with-test-data"></a>Wnioskowanie z danymi testowymi

Wnioskowanie z niektórymi danymi testowymi w celu wyświetlenia przewidywanej wartości z modelu AutoML, obecnie obsługiwane tylko w Azure Machine Learning SDK. Wyświetl wagi funkcji, które uczestniczą w realizacji przewidywanej wartości. 

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

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Wizualizowanie w celu odnajdywania wzorców danych i wyjaśnień w czasie uczenia

Możesz wizualizować wykres ważności funkcji w obszarze roboczym w [Machine Learning Studio](https://ml.azure.com). Po zakończeniu działania programu AutoML wybierz pozycję **Wyświetl szczegóły modelu** , aby wyświetlić konkretny przebieg. Wybierz kartę **wyjaśnienia** , aby wyświetlić pulpit nawigacyjny wizualizacji z wyjaśnieniem.

[![Architektura Machine Learningj interpretacji](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Więcej informacji na temat wizualizacji pulpitu nawigacyjnego i określonych wykresów można znaleźć w [dokumentacji dotyczącej interpretacji](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sposobu włączania objaśnień modelu i ważności funkcji w obszarach zestawu Azure Machine Learning SDK innego niż automatyczne Uczenie maszynowe, zobacz [artykuł dotyczący koncepcji interpretacji](how-to-machine-learning-interpretability.md).
