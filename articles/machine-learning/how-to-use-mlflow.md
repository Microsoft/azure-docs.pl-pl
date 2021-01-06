---
title: MLflow śledzenie dla eksperymentów ML
titleSuffix: Azure Machine Learning
description: Skonfiguruj MLflow z Azure Machine Learning, aby rejestrować metryki i artefakty z modeli ML, a także wdrażać modele sieci w postaci usługi sieci Web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ed9d41a84e455241ed3cfc41b905a671f2a2d499
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97912958"
---
# <a name="train-and-track-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Uczenie i śledzenie modeli ML przy użyciu MLflow i Azure Machine Learning (wersja zapoznawcza)

W tym artykule dowiesz się, jak włączyć śledzenie MLflow identyfikatorów URI i interfejsów API rejestrowania, wspólnie znane jako [śledzenie MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), aby połączyć Azure Machine Learning jako zaplecze eksperymentów MLflow. 

Obsługiwane są następujące możliwości: 

+ Śledź i Rejestruj metryki eksperymentów i artefakty w [obszarze roboczym Azure Machine Learning](./concept-azure-machine-learning-architecture.md#workspace). Jeśli już używasz śledzenia MLflow do eksperymentów, obszar roboczy zapewnia scentralizowaną, bezpieczną i skalowalną lokalizację do przechowywania metryk i modeli szkoleniowych.

+ Przesyłaj zadania szkoleniowe z [projektami MLflow](https://www.mlflow.org/docs/latest/projects.html) z obsługą zaplecza Azure Machine Learning (wersja zapoznawcza). Zadania można przesyłać lokalnie, korzystając z Azure Machine Learning śledzenia lub migrowania przebiegów w chmurze, podobnie jak w przypadku [obliczeń Azure Machine Learning](./how-to-create-attach-compute-cluster.md).

+ Śledź modele i zarządzaj nimi w rejestrze MLflow i Azure Machine Learning model.

[MLflow](https://www.mlflow.org) to biblioteka typu open source służąca do zarządzania cyklem życia eksperymentów uczenia maszynowego. Śledzenie MLFlow jest składnikiem MLflow, który rejestruje i śledzi przebiegi szkoleniowe i artefakty modelu, niezależnie od środowiska eksperymentu — lokalnie na komputerze, na zdalnym serwerze docelowym obliczeń, maszynie wirtualnej lub [klastrze Azure Databricks](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> Jako Biblioteka open source MLflow często zmienia się. W związku z tym funkcje udostępnione w ramach integracji Azure Machine Learning i MLflow powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.

Na poniższym diagramie przedstawiono, że śledzenie MLflow umożliwia śledzenie metryk uruchamiania i modeli magazynu eksperymentu w obszarze roboczym Azure Machine Learning.

![mlflow z diagramem usługi Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowani monitorowaniem użycia zasobów i zdarzeń z Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porównanie klientów MLflow i Azure Machine Learning

 Poniższa tabela zawiera podsumowanie różnych klientów, którzy mogą używać Azure Machine Learning i ich możliwości funkcji.

 Śledzenie MLflow oferuje funkcje rejestrowania metryk i magazynu artefaktów, które są dostępne tylko w innym przypadku za pośrednictwem [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

| Możliwość | MLflow śledzenie & | Azure Machine Learning Python SDK |  Interfejs wiersza polecenia usługi Azure Machine Learning | Studio uczenia maszynowego Azure|
|---|---|---|---|---|
| Zarządzanie obszarem roboczym |   | ✓ | ✓ | ✓ |
| Korzystanie z magazynów danych  |   | ✓ | ✓ | |
| Metryki dzienników      | ✓ | ✓ |   | |
| Przekaż artefakty | ✓ | ✓ |   | |
| Wyświetlanie metryk     | ✓ | ✓ | ✓ | ✓ |
| Zarządzanie obliczeniami   |   | ✓ | ✓ | ✓ |
| Wdrażanie modeli    | ✓ | ✓ | ✓ | ✓ |
|Monitorowanie wydajności modelu||✓|  |   |
| Wykrywanie dryfu danych |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj pakiet `azureml-mlflow`. 
    * Ten pakiet automatycznie łączy `azureml-core` [zestaw SDK języka Python Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), który zapewnia łączność z MLflow w celu uzyskania dostępu do obszaru roboczego.
* [Utwórz obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).
    * Zobacz, które [uprawnienia dostępu należy wykonać, aby wykonywać operacje MLflow w Twoim obszarze roboczym](how-to-assign-roles.md#mlflow-operations).

## <a name="track-local-runs"></a>Śledź uruchomienia lokalne

Śledzenie MLflow za pomocą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z lokalnych przebiegów w obszarze roboczym Azure Machine Learning.

Zaimportuj `mlflow` klasy i, [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py) Aby uzyskać dostęp do śledzenia identyfikatorów URI MLflow i skonfigurować obszar roboczy.

W poniższym kodzie `get_mlflow_tracking_uri()` Metoda przypisuje unikatowy adres URI śledzenia do obszaru roboczego, `ws` a następnie wskazuje na ten `set_tracking_uri()` adres identyfikator URI śledzenia MLflow.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identyfikator URI śledzenia jest prawidłowy do godziny lub mniej. Jeśli ponownie uruchomisz skrypt po pewnym czasie bezczynności, użyj interfejsu API get_mlflow_tracking_uri, aby uzyskać nowy identyfikator URI.

Ustaw nazwę eksperymentu MLflow z `set_experiment()` i zacznij korzystać z szkolenia w systemie `start_run()` . Następnie użyj, `log_metric()` Aby aktywować interfejs API rejestrowania MLflow i rozpocząć rejestrowanie metryk przebiegu szkoleniowego.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Śledź zdalne uruchomienia

Zdalne uruchomienia umożliwiają uczenie modeli przy większej liczbie zaawansowanych obliczeń, takich jak maszyny wirtualne obsługujące procesor GPU lub klastry środowisko obliczeniowe usługi Machine Learning. Aby dowiedzieć się więcej o różnych opcjach obliczeniowych, zobacz [use Target COMPUTE for model Training](how-to-set-up-training-targets.md) .

Śledzenie MLflow za pomocą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z zdalnych przebiegów w obszarze roboczym Azure Machine Learning. Wszystkie uruchomienia z kodem śledzącym MLflow w tym obszarze będą automatycznie rejestrowane metryki w obszarze roboczym. 

Poniższe przykładowe środowisko Conda obejmuje `mlflow` i `azureml-mlflow` jako pakiety PIP. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

W skrypcie Skonfiguruj środowisko uruchomieniowe obliczeniowe i szkoleniowe z [`Environment`](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) klasą. Następnie Utwórz  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) z użyciem obliczeń zdalnych jako element docelowy obliczeń.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

W przypadku konfiguracji przebiegu obliczeń i szkoleń Użyj `Experiment.submit()` metody do przesyłania przebiegu. Ta metoda automatycznie ustawia identyfikator URI śledzenia MLflow i kieruje rejestrowanie z MLflow do obszaru roboczego.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Uczenie się z projektami MLflow

[Projekty MLflow](https://mlflow.org/docs/latest/projects.html) umożliwiają organizowanie i opisywanie kodu w celu umożliwienia im uruchamiania innych naukowców z danymi (lub zautomatyzowanych narzędzi). Projekty MLflow z Azure Machine Learning umożliwiają śledzenie przebiegów szkoleniowych i zarządzanie nimi w obszarze roboczym. 

W tym przykładzie przedstawiono sposób lokalnego przesyłania projektów MLflow przy użyciu śledzenia Azure Machine Learning.

Zainstaluj `azureml-mlflow` pakiet, aby korzystać z funkcji śledzenia MLflow z Azure Machine Learning na eksperymentach lokalnie. Eksperymenty mogą działać za pośrednictwem Jupyter Notebook lub edytora kodu.

```shell
pip install azureml-mlflow
```

Zaimportuj `mlflow` klasy i, [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py) Aby uzyskać dostęp do śledzenia identyfikatorów URI MLflow i skonfigurować obszar roboczy.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Ustaw nazwę eksperymentu MLflow z `set_experiment()` i zacznij korzystać z szkolenia w systemie `start_run()` . Następnie użyj usługi, `log_metric()` Aby aktywować interfejs API rejestrowania MLflow i rozpocząć rejestrowanie metryk uruchamiania szkoleniowego.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Utwórz obiekt konfiguracji zaplecza do przechowywania informacji niezbędnych do integracji, takich jak, obiekt docelowy obliczeń i typ zarządzanego środowiska do użycia.

```python
backend_config = {"USE_CONDA": False}
```
Dodaj `azureml-mlflow` pakiet jako zależność PIP do pliku konfiguracji środowiska w celu śledzenia metryk i kluczowych artefaktów w obszarze roboczym. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Prześlij przebieg lokalny i upewnij się, że parametr został ustawiony `backend = "azureml" ` . Za pomocą tego ustawienia można przesłać uruchomienia lokalnie i uzyskać dodatkową obsługę funkcji automatycznego śledzenia danych wyjściowych, plików dziennika, migawek i wydrukowanych błędów w obszarze roboczym. 

Wyświetl swoje uruchomienia i metryki w [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Wyświetlanie metryk i artefaktów w obszarze roboczym

Metryki i artefakty z rejestrowania MLflow są przechowywane w Twoim obszarze roboczym. Aby wyświetlić je w dowolnym momencie, przejdź do obszaru roboczego i Znajdź eksperyment według nazwy w obszarze roboczym w programie [Azure Machine Learning Studio](https://ml.azure.com).  Lub uruchom poniższy kod. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Zarządzanie modelami 

Zarejestruj i śledź modele przy użyciu [rejestru modelu Azure Machine Learning](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) , który obsługuje rejestr modelu MLflow. Modele Azure Machine Learning są wyrównane ze schematem modelu MLflow, dzięki czemu można łatwo eksportować i importować te modele w różnych przepływach pracy. Metadane pokrewne MLflow, takie jak, identyfikator przebiegu są również oznaczone zarejestrowanym modelem na potrzeby śledzenia. Użytkownicy mogą przesyłać przebiegi szkoleniowe, rejestrować i wdrażać modele wytwarzane z MLflow. 

Jeśli chcesz wdrożyć i zarejestrować gotowy do zastosowania model produkcyjny w jednym kroku, zobacz temat [wdrażanie i rejestrowanie modeli MLflow](how-to-deploy-mlflow-models.md).

Aby zarejestrować i wyświetlić model z przebiegu, wykonaj następujące czynności:

1. Po zakończeniu przebiegu wywołania `register_model()` metody.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Wyświetl zarejestrowany model w obszarze roboczym przy użyciu programu [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    W poniższym przykładzie zarejestrowany model `my-model` ma otagowane metadane śledzenia MLflow. 

    ![Register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Wybierz kartę **artefakty** , aby wyświetlić wszystkie pliki modelu, które są wyrównane ze schematem modelu MLflow (Conda. YAML, MLmodel, model. PKL).

    ![Model — schemat](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Wybierz pozycję MLmodel, aby wyświetlić plik MLmodel wygenerowany przez uruchomienie.

    ![MLmodel — schemat](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz używania zarejestrowanych metryk i artefaktów w obszarze roboczym, możliwość ich usunięcia osobno jest obecnie niedostępna. Zamiast tego należy usunąć grupę zasobów zawierającą konto magazynu i obszar roboczy, dzięki czemu nie zostaną naliczone żadne opłaty:

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.

   ![Usuwanie w witrynie Azure Portal](./media/how-to-use-mlflow/delete-resources.png)

1. Wybierz utworzoną grupę zasobów z listy.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

## <a name="example-notebooks"></a>Przykładowe notesy

[MLflow z usługami Azure ml notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) pokazują i rozszerzają w oparciu o koncepcje przedstawione w tym artykule.

> [!NOTE]
> Repozytorium oparte na społeczności przykłady korzystające z usługi mlflow można znaleźć pod adresem https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Następne kroki

* [Wdróż modele przy użyciu MLflow](how-to-deploy-mlflow-models.md).
* Monitoruj modele produkcyjne pod kątem [dryfowania danych](./how-to-enable-data-collection.md).
* [Śledzenie przebiegów Azure Databricks z MLflow](how-to-use-mlflow-azure-databricks.md).
* [Zarządzaj modelami](concept-model-management-and-deployment.md).