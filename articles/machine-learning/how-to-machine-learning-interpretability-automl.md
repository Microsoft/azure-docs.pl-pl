---
title: Wytłumaczenia w zautomatyzowanym ML
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać wyjaśnienia dotyczące sposobu, w jaki zautomatyzowany model uczenia maszynowego określa ważność funkcji i dokonuje prognoz podczas korzystania z zestawu SDK usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.date: 03/11/2020
ms.openlocfilehash: e9155104905ae3e686a01b90cbcad2610b6f4c91
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086423"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning"></a>Możliwość interpretacji: objaśnienia modelu w zautomatyzowanym uczeniu maszynowym

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak uzyskać wyjaśnienia dotyczące automatycznego uczenia maszynowego (ML) w usłudze Azure Machine Learning. Automatyczne ml pomaga zrozumieć znaczenie inżynierii funkcji. 

Wszystkie wersje zestawu SDK po domyślnie ustawionej wersji `model_explainability=True` 1.0.85. W zestawie SDK w wersji 1.0.85 `model_explainability=True` i `AutoMLConfig` wcześniejszych wersjach użytkownicy muszą ustawić w obiekcie, aby użyć możliwości interpretowania modelu. 

W tym artykule omówiono sposób wykonywania następujących zadań:

- Wykonywanie tłumaczeń podczas szkolenia dla najlepszego modelu lub dowolnego modelu.
- Włącz wizualizacje, aby ułatwić wyświetlanie wzorców w danych i objaśnieniach.
- Implementowanie interpretacji podczas wnioskowania lub oceniania.

## <a name="prerequisites"></a>Wymagania wstępne

- Funkcje interpretalności. Uruchom, `pip install azureml-interpret azureml-contrib-interpret` aby uzyskać niezbędne pakiety.
- Znajomość tworzenia zautomatyzowanych eksperymentów ML. Aby uzyskać więcej informacji na temat korzystania z zestawu SDK usługi Azure Machine Learning, wykonaj ten [samouczek modelu regresji](tutorial-auto-train-models.md) lub zobacz, jak [skonfigurować zautomatyzowane eksperymenty uczenia maszynowego.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>Możliwość tłumaczenia podczas szkolenia dla najlepszego modelu

Pobierz wyjaśnienie z `best_run`, który zawiera wyjaśnienia dotyczące funkcji inżynierii.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Pobierz ważność funkcji z magazynu artefaktów

Można użyć `ExplanationClient` do pobrania inżynierii objaśnienia funkcji `best_run`z magazynu artefaktów . 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Możliwość tłumaczenia ustny podczas szkolenia dla każdego modelu 

Podczas obliczania objaśnień modelu i wizualizowania ich, nie są ograniczone do istniejącego objaśnienia modelu dla zautomatyzowanego modelu ml. Można również uzyskać wyjaśnienie dla modelu z różnych danych testowych. Kroki opisane w tej sekcji pokazują, jak obliczyć i wizualizować znaczenie funkcji inżynierii na podstawie danych testowych.

### <a name="retrieve-any-other-automl-model-from-training"></a>Pobieranie dowolnego innego modelu AutoML ze szkolenia

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Konfigurowanie objaśnień modelu

Użyj, `automl_setup_model_explanations` aby uzyskać zaprojektowane wyjaśnienia. Może `fitted_model` generować następujące elementy:

- Polecane dane z przeszkolonych lub testowych próbek
- Zaprojektowane listy nazw funkcji
- Klasy do znalezienia w kolumnie oznaczonej w scenariuszach klasyfikacji

Zawiera `automl_explainer_setup_obj` wszystkie struktury z powyższej listy.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inicjowanie objaśnienia mimic pod kątem ważności funkcji

Aby wygenerować wyjaśnienie dla `MimicWrapper` modeli AutoML, należy użyć klasy. Można zainicjować MimicWrapper z następujących parametrów:

- Obiekt konfiguracji objaśnienia
- Obszar roboczy
- Model LightGBM, który działa jako `fitted_model` surogat do zautomatyzowanego modelu ML

MimicWrapper zajmuje również `automl_run` obiekt, w którym zostaną przesłane zaprojektowane wyjaśnienia.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Użyj MimicExplainer do przetwarzania i wizualizacji inżynierii znaczenie funkcji

Metodę w `explain()` MimicWrapper można wywołać za pomocą przekształconych próbek testowych, aby uzyskać znaczenie funkcji dla wygenerowanych funkcji inżynieryjnych. Można również `ExplanationDashboard` użyć do wyświetlania wizualizacji pulpitu nawigacyjnego wartości ważności funkcji generowanych funkcji inżynierii przez zautomatyzowanych featurizers ML.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Możliwość interpretacji podczas wnioskowania

W tej sekcji dowiesz się, jak operacjonalizacji zautomatyzowanego modelu ml z objaśnienia, który został użyty do obliczenia wyjaśnień w poprzedniej sekcji.

### <a name="register-the-model-and-the-scoring-explainer"></a>Zarejestruj model i objaśnienie punktacji

`TreeScoringExplainer` Użyj, aby utworzyć objaśnienie oceniania, który obliczy zaprojektowane wartości ważności operacji w czasie wnioskowania. Zainicjować objaśnienie `feature_map` punktacji z tym, który został obliczony wcześniej. 

Zapisz objaśnienie oceniania, a następnie zarejestruj model i objaśnienie oceniania w usłudze zarządzania modelami. Uruchom następujący kod:

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Tworzenie zależności conda do konfigurowania usługi

Następnie należy utworzyć niezbędne zależności środowiska w kontenerze dla wdrożonego modelu. Należy pamiętać, że azureml-defaults z wersji >= 1.0.45 musi być wymieniony jako zależność pip, ponieważ zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web.

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

### <a name="deploy-the-service"></a>Wdrażanie usługi

Wdrażanie usługi przy użyciu pliku conda i pliku oceniania z poprzednich kroków.

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

Wnioskowanie z niektórych danych testowych, aby zobaczyć przewidywaną wartość z automatycznego modelu uczenia maszynowego. Wyświetl znaczenie obiektu zaprojektowanego dla przewidywanej wartości.

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
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Wizualizuj, aby odkryć wzorce w danych i wyjaśnieniach w czasie szkolenia

Możesz wizualizować wykres ważności funkcji w obszarze roboczym w [studiu usługi Azure Machine Learning](https://ml.azure.com). Po zakończeniu automatycznego uruchamiania ml wybierz **pozycję Wyświetl szczegóły modelu,** aby wyświetlić określony przebieg. Wybierz kartę **Objaśnienia,** aby wyświetlić pulpit nawigacyjny wizualizacji objaśnień.

[![Architektura tłumaczeń uczenia maszynowego](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sposobu włączania objaśnień modelu i znaczenia funkcji w obszarach SDK usługi Azure Machine Learning innych niż zautomatyzowane uczenie maszynowe, zobacz [artykuł koncepcyjny dotyczący interpretowalności.](how-to-machine-learning-interpretability.md)
