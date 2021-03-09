---
title: MLflow śledzenie dla eksperymentów Azure Databricks ML
titleSuffix: Azure Machine Learning
description: Skonfiguruj MLflow z Azure Machine Learning, aby rejestrować metryki i artefakty z eksperymentów Azure Databricks ml.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a894a46ce7c78b65dde80c52f9516fcbe4d27bcb
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521003"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Śledź eksperymenty Azure Databricks ML z MLflow i Azure Machine Learning (wersja zapoznawcza)

W tym artykule dowiesz się, jak włączyć śledzenie identyfikatorów URI i interfejsu API rejestrowania MLflow, wspólnie znane jako [śledzenie MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), aby połączyć Azure DATABRICKS (ADB) eksperymenty, MLflow i Azure Machine Learning.

[MLflow](https://www.mlflow.org) to biblioteka typu open source służąca do zarządzania cyklem życia eksperymentów uczenia maszynowego. Śledzenie MLFlow to składnik MLflow, który rejestruje i śledzi wskaźniki uruchamiania szkoleniowe i artefakty modelu. Dowiedz się więcej na temat [Azure Databricks i MLflow](/azure/databricks/applications/mlflow/). 

Zobacz [śledzenie eksperymentów z MLflow i Azure Machine Learning,](how-to-use-mlflow.md) Aby uzyskać dodatkową integrację z funkcjami MLflow i Azure Machine Learning.

>[!NOTE]
> Jako Biblioteka open source MLflow często zmienia się. W związku z tym funkcje udostępnione w ramach integracji Azure Machine Learning i MLflow powinny być traktowane jako wersja zapoznawcza i nie są w pełni obsługiwane przez firmę Microsoft.

> [!TIP]
> Informacje przedstawione w tym dokumencie są przeznaczone głównie dla analityków danych i deweloperów, którzy chcą monitorować proces szkolenia modelu. Jeśli jesteś administratorem zainteresowani monitorowaniem użycia zasobów i zdarzeń z Azure Machine Learning, takich jak przydziały, ukończone przebiegi szkoleniowe lub wdrożone wdrożenia modelu, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj pakiet `azureml-mlflow`. 
    * Ten pakiet automatycznie łączy `azureml-core` [zestaw SDK języka Python Azure Machine Learning](/python/api/overview/azure/ml/install), który zapewnia łączność z MLflow w celu uzyskania dostępu do obszaru roboczego.
* [Azure Databricks obszar roboczy i klaster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Utwórz obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).
    * Zobacz, które [uprawnienia dostępu należy wykonać, aby wykonywać operacje MLflow w Twoim obszarze roboczym](how-to-assign-roles.md#mlflow-operations).

## <a name="track-azure-databricks-runs"></a>Śledź uruchomienia Azure Databricks

Śledzenie MLflow za pomocą Azure Machine Learning umożliwia przechowywanie zarejestrowanych metryk i artefaktów z Azure Databricks są uruchamiane zarówno w: 

* Azure Databricks obszar roboczy.
* Obszar roboczy usługi Azure Machine Learning

Po utworzeniu obszaru roboczego i klastra Azure Databricks 

1. Zainstaluj bibliotekę *Azure-mlflow* z poziomu usługi PyPi, aby upewnić się, że klaster ma dostęp do wymaganych funkcji i klas.

1. Skonfiguruj Notes eksperymentu.

1. Połącz obszar roboczy Azure Databricks i Azure Machine Learning obszar roboczy.

Dodatkowe szczegóły dotyczące tych kroków znajdują się w następujących sekcjach, dzięki czemu można pomyślnie uruchomić eksperymenty MLflow z Azure Databricks. 

## <a name="install-libraries"></a>Zainstaluj biblioteki

Aby zainstalować biblioteki w klastrze, przejdź do karty **biblioteki** i wybierz pozycję **Instaluj nowe**

 ![mlflow z usługami Azure datakostks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

W polu **pakiet** wpisz polecenie Azure-mlflow, a następnie wybierz pozycję Zainstaluj. Powtórz ten krok w razie potrzeby, aby zainstalować inne dodatkowe pakiety w klastrze dla eksperymentu.

 ![Biblioteka usługi Azure DB Install mlflow](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Konfigurowanie notesu 

Po skonfigurowaniu klastra ADB 
1. Wybierz pozycję **obszary robocze** w lewym okienku nawigacji. 
1. Rozwiń menu rozwijane obszary robocze i wybierz pozycję **Importuj** .
1. Przeciągnij i upuść lub Przeglądaj, aby znaleźć Notes eksperymentu do importowania obszaru roboczego ADB.
1. Wybierz pozycję **Importuj**. Notes eksperymentu zostanie otwarty automatycznie.
1. W obszarze tytuł notesu w lewym górnym rogu wybierz klaster, który ma zostać dołączony do notesu eksperymentu. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Łączenie Azure Databricks i Azure Machine Learning obszarów roboczych

Połączenie obszaru roboczego ADB z obszarem roboczym Azure Machine Learning umożliwia śledzenie danych eksperymentów w obszarze roboczym Azure Machine Learning.

Aby połączyć obszar roboczy ADB z nowym lub istniejącym obszarem roboczym Azure Machine Learning, 
1. Zaloguj się do [Azure Portal](https://ms.portal.azure.com).
1. Przejdź do strony **omówienia** obszaru roboczego ADB.
1. Wybierz przycisk **połącz Azure Machine Learning obszar roboczy** w prawym dolnym rogu. 

 ![Łączenie obszarów roboczych usługi Azure DB i Azure Machine Learning](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>Śledzenie MLflow w obszarach roboczych

Po utworzeniu wystąpienia obszaru roboczego śledzenie MLflow zostanie automatycznie ustawione tak, aby były śledzone we wszystkich następujących miejscach:

* Połączony obszar roboczy Azure Machine Learning.
* Oryginalny obszar roboczy ADB. 

Wszystkie eksperymenty są użytkowane w usłudze Managed Azure Machine Learning śledzenia.

Poniższy kod powinien znajdować się w notesie eksperymentu, aby pobrać połączony obszar roboczy Azure Machine Learning. 

Ten kod, 

*  Pobiera szczegóły subskrypcji platformy Azure, aby utworzyć wystąpienie obszaru roboczego Azure Machine Learning. 

* Przyjęto założenie, że masz istniejącą grupę zasobów i obszar roboczy Azure Machine Learning, w przeciwnym razie można [je utworzyć](how-to-manage-workspace.md). 

* Ustawia nazwę eksperymentu. W `user_name` tym miejscu jest zgodna z `user_name` obszarem roboczym Azure Databricks.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Ustaw śledzenie MLflow na śledzenie tylko w obszarze roboczym Azure Machine Learning

Jeśli wolisz zarządzać monitorowanymi eksperymentami w scentralizowanej lokalizacji, możesz ustawić śledzenie MLflow, aby śledzić **tylko** w obszarze roboczym Azure Machine Learning. 

Dołącz następujący kod do skryptu:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

W skrypcie szkoleniowym zaimportuj, `mlflow` Aby korzystać z interfejsów API rejestrowania MLflow i rozpocząć rejestrowanie metryk przebiegu. Poniższy przykład rejestruje metrykę utraty epoki. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Rejestrowanie modeli przy użyciu MLflow

Po przeszkoleniu modelu można rejestrować i rejestrować modele na serwerze śledzenia zaplecza przy użyciu `mlflow.<model_flavor>.log_model()` metody. `<model_flavor>`, odwołuje się do struktury skojarzonej z modelem. [Dowiedz się, jakie typy modeli są obsługiwane](https://mlflow.org/docs/latest/models.html#model-api). 

Serwer śledzenia wewnętrznej bazy danych jest domyślnie obszarem roboczym Azure Databricks; Jeśli nie wybrano, aby [śledzenie MLflow było śledzone tylko w obszarze roboczym Azure Machine Learning](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), to serwer śledzenia zaplecza jest Azure Machine Learningm obszarem roboczym.   

* **Jeśli zarejestrowany model o nazwie nie istnieje**, Metoda rejestruje nowy model, tworzy wersję 1 i zwraca obiekt ModelVersion MLflow. 

* **Jeśli zarejestrowany model o tej nazwie już istnieje**, metoda tworzy nową wersję modelu i zwraca obiekt Version. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Tworzenie punktów końcowych dla modeli MLflow

Gdy wszystko będzie gotowe do utworzenia punktu końcowego dla modeli sieci. Można wdrożyć usługę jako, 

* Usługa sieci Web Request-Response Azure Machine Learning na potrzeby interaktywnego oceniania. To wdrożenie umożliwia korzystanie z funkcji zarządzania modelami Azure Machine Learning i stosowanie ich do modeli produkcyjnych oraz zarządzanie nimi. 

* Obiekty modelu MLFlow, które mogą być używane w potokach przesyłania strumieniowego lub wsadowego jako funkcje języka Python lub UDF Pandas w obszarze roboczym Azure Databricks.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Wdróż modele w Azure Machine Learning punktach końcowych 
Za pomocą interfejsu API [mlflow. Azure. deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) można wdrożyć model w obszarze roboczym Azure Machine Learning. Jeśli model został zarejestrowany tylko w obszarze roboczym Azure Databricks, zgodnie z opisem w sekcji [zarejestruj modele przy użyciu MLflow](#register-models-with-mlflow) , określ `model_name` parametr, aby zarejestrować model w Azure Machine Learning obszarze roboczym. 

Uruchomienia Azure Databricks można wdrożyć w następujących punktach końcowych, 
* [Wystąpienie kontenera platformy Azure](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Wdróż modele do punktów końcowych ADB na potrzeby oceniania partii 

Można wybrać Azure Databricks klastrów do oceniania partii. Model MLFlow jest ładowany i używany jako system Spark Pandas UDF do oceny nowych danych. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie planujesz używania zarejestrowanych metryk i artefaktów w obszarze roboczym, możliwość ich usunięcia osobno jest obecnie niedostępna. Zamiast tego należy usunąć grupę zasobów zawierającą konto magazynu i obszar roboczy, dzięki czemu nie zostaną naliczone żadne opłaty:

1. W witrynie Azure Portal na końcu z lewej strony wybierz pozycję **Grupy zasobów**.

   ![Usuwanie w witrynie Azure Portal](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Wybierz utworzoną grupę zasobów z listy.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wpisz nazwę grupy zasobów. Następnie wybierz pozycję **Usuń**.

## <a name="example-notebooks"></a>Przykładowe notesy

[MLflow z notesami Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) prezentuje i rozwijają się w oparciu o koncepcje przedstawione w tym artykule.

## <a name="next-steps"></a>Następne kroki
* [Wdróż modele MLflow jako usługę sieci Web platformy Azure](how-to-deploy-mlflow-models.md). 
* [Zarządzaj modelami](concept-model-management-and-deployment.md).
* [Śledź przebiegi eksperymentów z MLflow i Azure Machine Learning](how-to-use-mlflow.md). 
* Dowiedz się więcej na temat [Azure Databricks i MLflow](/azure/databricks/applications/mlflow/).