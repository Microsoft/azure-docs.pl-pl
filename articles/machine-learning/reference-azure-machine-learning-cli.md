---
title: Instalowanie & interfejsu wiersza Azure Machine Learning wiersza polecenia
description: Dowiedz się, jak za pomocą rozszerzenia interfejsu wiersza polecenia platformy Azure dla uczenia maszynowego & zarządzać zasobami, takimi jak obszar roboczy, magazyny danych, zestawy danych, potoki, modele i wdrożenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: f30cd528a329708a7478b1a4a343f7be3b9eac04
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877790"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Zainstaluj & interfejsu wiersza polecenia na Azure Machine Learning


Interfejs Azure Machine Learning wiersza polecenia to rozszerzenie interfejsu wiersza polecenia platformy [Azure](/cli/azure/), międzyplatformowego interfejsu wiersza polecenia dla platformy Azure. To rozszerzenie udostępnia polecenia do pracy z Azure Machine Learning. Umożliwia automatyzację działań uczenia maszynowego. Na poniższej liście przedstawiono kilka przykładowych akcji, które można wykonać za pomocą rozszerzenia interfejsu wiersza polecenia:

+ Uruchamianie eksperymentów w celu tworzenia modeli uczenia maszynowego

+ Rejestrowanie modeli uczenia maszynowego na potrzeby użycia przez klientów

+ Spakowania, wdrażania i śledzenia cyklu życia modeli uczenia maszynowego

Interfejs wiersza polecenia nie zastępuje zestawu AZURE MACHINE LEARNING SDK. Jest to uzupełniające się narzędzie zoptymalizowane pod kątem obsługi wysoce sparametryzowanego zadania, które dobrze pasują do automatyzacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby korzystać z interfejsu wiersza polecenia, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Aby używać poleceń interfejsu wiersza polecenia w tym dokumencie ze środowiska **lokalnego,** potrzebny jest interfejs wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli).

    Jeśli używasz interfejsu [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), interfejs wiersza polecenia jest dostępny za pośrednictwem przeglądarki i znajduje się w chmurze.

## <a name="full-reference-docs"></a>Pełna dokumenty referencyjne

Znajdź pełne [dokumenty referencyjne dotyczące rozszerzenia azure-cli-ml interfejsu wiersza polecenia platformy Azure.](/cli/azure/ml/)

## <a name="connect-the-cli-to-your-azure-subscription"></a>Łączenie interfejsu wiersza polecenia z subskrypcją platformy Azure

> [!IMPORTANT]
> Jeśli używasz tej Azure Cloud Shell, możesz pominąć tę sekcję. Usługa Cloud Shell automatycznie uwierzytelnia Cię przy użyciu konta, które logujesz się do subskrypcji platformy Azure.

Istnieje kilka sposobów uwierzytelniania w subskrypcji platformy Azure za pomocą interfejsu wiersza polecenia. Najbardziej podstawowe jest interaktywne uwierzytelnianie przy użyciu przeglądarki. Aby uwierzytelnić się interaktywnie, otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie należy otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie i [https://aka.ms/devicelogin](https://aka.ms/devicelogin) wprowadzanie kodu autoryzacji.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Aby uzyskać informacje o innych metodach uwierzytelniania, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/authenticate-azure-cli)

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Rozszerzenie jest instalowane automatycznie podczas pierwszej próby użycia polecenia, które rozpoczyna się od `az ml` .

## <a name="update-the-extension"></a>Aktualizowanie rozszerzenia

Aby zaktualizować Machine Learning interfejsu wiersza polecenia, użyj następującego polecenia:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>Usuwanie rozszerzenia

Aby usunąć rozszerzenie interfejsu wiersza polecenia, użyj następującego polecenia:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Zarządzanie zasobami

Następujące polecenia pokazują, jak za pomocą interfejsu wiersza polecenia zarządzać zasobami używanymi przez Azure Machine Learning.

+ Jeśli jeszcze jej nie masz, utwórz grupę zasobów:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Tworzenie Azure Machine Learning roboczego:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Aby uzyskać więcej informacji, zobacz [az ml workspace create](/cli/azure/ml/workspace#az_ml_workspace_create).

+ Dołączanie konfiguracji obszaru roboczego do folderu w celu włączenia świadomości kontekstowej interfejsu wiersza polecenia.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    To polecenie tworzy `.azureml` podkatalog zawierający przykładowe pliki środowiska runconfig i conda. Zawiera on również `config.json` plik, który jest używany do komunikowania się z Azure Machine Learning roboczym.

    Aby uzyskać więcej informacji, zobacz [az ml folder attach](/cli/azure/ml/folder#az_ml_folder_attach).

+ Dołączanie kontenera obiektów blob platformy Azure jako magazynu danych.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Aby uzyskać więcej informacji, zobacz [az ml datastore attach-blob](/cli/azure/ml/datastore#az_ml_datastore_attach-blob).

+ Przekazywanie plików do magazynu danych.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Aby uzyskać więcej informacji, zobacz [az ml datastore upload](/cli/azure/ml/datastore#az_ml_datastore_upload).

+ Dołączanie klastra usługi AKS jako docelowego obiektu obliczeniowego.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget attach aks](/cli/azure/ml/computetarget/attach#az_ml_computetarget_attach-aks)

### <a name="compute-clusters"></a>Klastry obliczeniowe

+ Utwórz nowy zarządzany klaster obliczeniowy.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Tworzenie nowego zarządzanego klastra obliczeniowego z tożsamością zarządzaną

  + Tożsamość zarządzana przypisana przez użytkownika

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Tożsamość zarządzana przypisana przez system

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Dodaj tożsamość zarządzaną do istniejącego klastra:

    + Tożsamość zarządzana przypisana przez użytkownika
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Tożsamość zarządzana przypisana przez system

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Aby uzyskać więcej informacji, zobacz [az ml computetarget create amlcompute](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Wystąpienie obliczeniowe
Zarządzanie wystąpieniami obliczeniowymi.  We wszystkich poniższych przykładach nazwa wystąpienia obliczeniowego to **cpu**

+ Utwórz nową moc obliczeniową.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget create computeinstance](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance).

+ Zatrzymaj wystąpienia obliczeniowe.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget computeinstance stop](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

+ Uruchamianie wystąpienia obliczeniowego.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget computeinstance start](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

+ Uruchom ponownie wystąpienia obliczeniowe.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget computeinstance restart (Az ml computetarget computeinstance restart).](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart)

+ Usuwanie wystąpienia obliczeniowego.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget delete computeinstance](/cli/azure/ml/computetarget#az_ml_computetarget_delete).


## <a name="run-experiments"></a><a id="experiments"></a>Uruchamianie eksperymentów

* Rozpocznij przebieg eksperymentu. Korzystając z tego polecenia, określ nazwę pliku runconfig (tekst przed .runconfig, jeśli patrzysz na system plików) względem \* -c parametru.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Polecenie `az ml folder attach` tworzy `.azureml` podkatalog, który zawiera dwa przykładowe pliki runconfig. 
    >
    > Jeśli masz skrypt języka Python, który tworzy obiekt konfiguracji uruchamiania programowo, możesz użyć polecenia [RunConfig.save(),](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) aby zapisać go jako plik runconfig.
    >
    > Pełny schemat runconfig można znaleźć w tym pliku [JSON.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) Schemat sam dokumentuje się za pomocą `description` klucza każdego obiektu. Ponadto istnieją wyliki dla możliwych wartości i fragment kodu szablonu na końcu.

    Aby uzyskać więcej informacji, zobacz [az ml run submit-script](/cli/azure/ml/run#az_ml_run_submit_script).

* Wyświetl listę eksperymentów:

    ```azurecli-interactive
    az ml experiment list
    ```

    Aby uzyskać więcej informacji, zobacz [az ml experiment list](/cli/azure/ml/experiment#az_ml_experiment_list).

### <a name="hyperdrive-run"></a>Uruchamianie funkcji HyperDrive

Za pomocą usługi HyperDrive z interfejsem wiersza polecenia platformy Azure można wykonywać przebiegi dostrajania parametrów. Najpierw utwórz plik konfiguracji usługi HyperDrive w następującym formacie. Aby uzyskać szczegółowe informacje na temat [parametrów dostrajania hiperparametrów,](how-to-tune-hyperparameters.md) zobacz artykuł Tune hyperparameters for your model (Dostrajanie hiperparametrów dla modelu).

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Dodaj ten plik obok plików konfiguracji uruchamiania. Następnie prześlij uruchomienie funkcji HyperDrive przy użyciu:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Zwróć uwagę *na sekcję argumentów* w pliku runconfig i *miejsce na parametry* w konfiguracji funkcji HyperDrive. Zawierają one argumenty wiersza polecenia, które mają zostać przekazane do skryptu trenowania. Wartość w pliku runconfig pozostaje taka sama dla każdej iteracji, podczas gdy zakres w konfiguracji funkcji HyperDrive jest iterowany. Nie określaj tego samego argumentu w obu plikach.

## <a name="dataset-management"></a>Zarządzanie zestawami danych

Następujące polecenia pokazują, jak pracować z zestawami danych w Azure Machine Learning:

+ Rejestrowanie zestawu danych:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Aby uzyskać informacje na temat formatu pliku JSON używanego do definiowania zestawu danych, `az ml dataset register --show-template` użyj .

    Aby uzyskać więcej informacji, zobacz [az ml dataset register](/cli/azure/ml/dataset#az_ml_dataset_register).

+ Lista wszystkich zestawów danych w obszarze roboczym:

    ```azurecli-interactive
    az ml dataset list
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset list](/cli/azure/ml/dataset#az_ml_dataset_list).

+ Pobierz szczegóły zestawu danych:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset show](/cli/azure/ml/dataset#az_ml_dataset_show).

+ Wyrejestruj zestaw danych:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset unregister](/cli/azure/ml/dataset#az_ml_dataset_archive).

## <a name="environment-management"></a>Zarządzanie środowiskiem

Następujące polecenia pokazują, jak tworzyć, rejestrować i tworzyć Azure Machine Learning [dla](how-to-configure-environment.md) obszaru roboczego:

+ Utwórz pliki szkieletu dla środowiska:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Aby uzyskać więcej informacji, zobacz [az ml environment scaffold (Szkielet środowiska az ml).](/cli/azure/ml/environment#az_ml_environment_scaffold)

+ Rejestrowanie środowiska:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Aby uzyskać więcej informacji, zobacz [az ml environment register](/cli/azure/ml/environment#az_ml_environment_register).

+ Lista zarejestrowanych środowisk:

    ```azurecli-interactive
    az ml environment list
    ```

    Aby uzyskać więcej informacji, zobacz [az ml environment list](/cli/azure/ml/environment#az_ml_environment_list).

+ Pobierz zarejestrowane środowisko:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Aby uzyskać więcej informacji, zobacz [az ml environment download](/cli/azure/ml/environment#az_ml_environment_download).

### <a name="environment-configuration-schema"></a>Schemat konfiguracji środowiska

Jeśli używasz polecenia , generuje on plik szablonu, który można zmodyfikować i który może służyć do tworzenia niestandardowych konfiguracji `az ml environment scaffold` `azureml_environment.json` środowiska za pomocą interfejsu wiersza polecenia. Obiekt najwyższego poziomu jest luźno mapowany [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) na klasę w zestawie SDK języka Python. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

W poniższej tabeli przedstawiono szczegóły każdego pola najwyższego poziomu w pliku JSON, jego typ i opis. Jeśli typ obiektu jest połączony z klasą z zestawu SDK języka Python, istnieje luźne dopasowanie 1:1 między każdym polem JSON a nazwą zmiennej publicznej w klasie języka Python. W niektórych przypadkach pole może być mapowanie na argument konstruktora, a nie zmienną klasy. Na przykład pole `environmentVariables` jest mapowe `environment_variables` na zmienną w klasie [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) .

| Pole JSON | Typ | Opis |
|---|---|---|
| `name` | `string` | Nazwa środowiska. Nie należy rozpoczynać od nazwy **Microsoft** lub **AzureML.** |
| `version` | `string` | Wersja środowiska. |
| `environmentVariables` | `{string: string}` | Mapa skrótu nazw i wartości zmiennych środowiskowych. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)Hat definiuje środowisko języka Python i interpreter do użycia w docelowym zasobie obliczeniowym. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | Definiuje ustawienia służące do dostosowywania obrazu platformy Docker sbudowaną do specyfikacji środowiska. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | W tej sekcji są konfigurowane ustawienia platformy Spark. Jest on używany tylko wtedy, gdy framework jest ustawiony na PySpark. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Konfiguruje zależności biblioteki usługi Databricks. |
| `inferencingStackVersion` | `string` | Określa wersję stosu wnioskowania dodanego do obrazu. Aby uniknąć dodawania stosu wnioskowania, pozostaw to pole `null` . Prawidłowa wartość: "latest". |

## <a name="ml-pipeline-management"></a>Zarządzanie potokiem uczenia maszynowego

Następujące polecenia pokazują, jak pracować z potokami uczenia maszynowego:

+ Tworzenie potoku uczenia maszynowego:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Aby uzyskać więcej informacji, zobacz [az ml pipeline create](/cli/azure/ml/pipeline#az_ml_pipeline_create).

    Aby uzyskać więcej informacji na temat pliku YAML potoku, zobacz [Define machine learning pipelines in YAML (Definiowanie potoków uczenia maszynowego w yaml).](reference-pipeline-yaml.md)

+ Uruchamianie potoku:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Aby uzyskać więcej informacji, zobacz [az ml run submit-pipeline](/cli/azure/ml/run#az_ml_run_submit_pipeline).

    Aby uzyskać więcej informacji na temat pliku YAML potoku, zobacz [Define machine learning pipelines in YAML (Definiowanie potoków uczenia maszynowego w yaml).](reference-pipeline-yaml.md)

+ Planowanie potoku:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Aby uzyskać więcej informacji, zobacz [az ml pipeline create-schedule](/cli/azure/ml/pipeline#az_ml_pipeline_create-schedule).

    Aby uzyskać więcej informacji na temat pliku YAML harmonogramu potoku, zobacz [Define machine learning pipelines in YAML (Definiowanie potoków uczenia maszynowego w yaml).](reference-pipeline-yaml.md#schedules)

## <a name="model-registration-profiling-deployment"></a>Rejestracja, profilowanie, wdrażanie modelu

Następujące polecenia pokazują, jak zarejestrować wytrenowany model, a następnie wdrożyć go jako usługę produkcyjną:

+ Zarejestruj model za pomocą Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Aby uzyskać więcej informacji, zobacz [az ml model register](/cli/azure/ml/model#az_ml_model_register).

+ **OPCJONALNE** Profilowanie modelu w celu uzyskania optymalnych wartości procesora CPU i pamięci do wdrożenia.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Aby uzyskać więcej informacji, zobacz [az ml model profile](/cli/azure/ml/model#az_ml_model_profile).

+ Wdrażanie modelu w u usługi AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Aby uzyskać więcej informacji na temat schematu pliku konfiguracji wnioskowania, zobacz [Schemat konfiguracji wnioskowania](#inferenceconfig).
    
    Aby uzyskać więcej informacji na temat schematu pliku konfiguracji wdrożenia, zobacz [Schemat konfiguracji wdrożenia](#deploymentconfig).

    Aby uzyskać więcej informacji, zobacz [az ml model deploy](/cli/azure/ml/model#az_ml_model_deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schemat konfiguracji wnioskowania

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia

### <a name="local-deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia lokalnego

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia wystąpienia kontenera platformy Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service konfiguracji wdrożenia

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Następne kroki

* [Informacje o poleceniach dla rozszerzenia Machine Learning interfejsu wiersza polecenia.](/cli/azure/ml)

* [Trenowania i wdrażania modeli uczenia maszynowego przy użyciu Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
