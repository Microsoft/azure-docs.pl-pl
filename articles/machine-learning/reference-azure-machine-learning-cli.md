---
title: Rozszerzenie interfejsu wiersza polecenia
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o rozszerzeniu interfejsu wiersza polecenia usługi Azure Machine Learning dla interfejsu wiersza polecenia platformy Azure. Narzędzie interfejsu wiersza polecenia platformy Azure to wieloplatformowe narzędzie wiersza polecenia, które umożliwia pracę z zasobami w chmurze platformy Azure. Rozszerzenie uczenia maszynowego umożliwia pracę z usługą Azure Machine Learning. Narzędzie nr wiersza polecenia ml tworzy zasoby, takie jak obszar roboczy, magazyny danych, zestawy danych, potoki, modele i wdrożenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 16f9080487af95e7de5c5f8c91fd5c8d356b7bde
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618064"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Korzystanie z rozszerzenia interfejsu wiersza polecenia dla usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Interfejs wiersza polecenia usługi Azure Machine Learning jest rozszerzeniem [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)— interfejsu wiersza polecenia między platformami dla platformy Azure. To rozszerzenie zawiera polecenia do pracy z usługą Azure Machine Learning. Umożliwia automatyzację działań uczenia maszynowego. Poniższa lista zawiera kilka przykładowych akcji, które można wykonać z rozszerzeniem interfejsu wiersza polecenia:

+ Uruchamianie eksperymentów w celu tworzenia modeli uczenia maszynowego

+ Rejestrowanie modeli uczenia maszynowego do użytku klienta

+ Pakowanie, wdrażanie i śledzenie cyklu życia modeli uczenia maszynowego

Zestaw wiersza polecenia nie zastępuje zestawu SDK usługi Azure Machine Learning. Jest to uzupełniające narzędzie, które jest zoptymalizowane do obsługi wysoce sparametryzowanych zadań, które dobrze pasują do automatyzacji.

## <a name="prerequisites"></a>Wymagania wstępne

* Aby korzystać z interfejsu wiersza polecenia, musisz mieć subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Aby użyć poleceń interfejsu wiersza polecenia w tym dokumencie ze **środowiska lokalnego,** potrzebujesz [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

    Jeśli używasz [usługi Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)interfejs wiersza polecenia jest dostępny za pośrednictwem przeglądarki i mieszka w chmurze.

## <a name="full-reference-docs"></a>Pełne dokumenty referencyjne

Znajdź [pełne dokumenty referencyjne dla rozszerzenia azure-cli-ml interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)

## <a name="connect-the-cli-to-your-azure-subscription"></a>Łączenie interfejsu wiersza polecenia z subskrypcją platformy Azure

> [!IMPORTANT]
> Jeśli używasz usługi Azure Cloud Shell, możesz pominąć tę sekcję. Powłoka w chmurze automatycznie uwierzytelnia cię przy użyciu konta, które logujesz się do subskrypcji platformy Azure.

Istnieje kilka sposobów, które można uwierzytelnić do subskrypcji platformy Azure z interfejsu wiersza polecenia. Najbardziej podstawowe jest interaktywne uwierzytelnienie za pomocą przeglądarki. Aby uwierzytelnić się interaktywnie, otwórz wiersz polecenia lub terminal i użyj następującego polecenia:

```azurecli-interactive
az login
```

Jeśli interfejs wiersza polecenia może otworzyć Twoją domyślną przeglądarkę, zrobi to i załaduje stronę logowania. W przeciwnym razie musisz otworzyć przeglądarkę i postępować zgodnie z instrukcjami w wierszu polecenia. Instrukcje obejmują przeglądanie [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadzanie kodu autoryzacyjnego.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Aby uzyskać inne metody uwierzytelniania, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie interfejsu wiersza polecenia uczenia maszynowego, należy użyć następującego polecenia:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Przykładowe pliki, których można użyć za pomocą poniższych poleceń, można znaleźć [tutaj](https://aka.ms/azml-deploy-cloud).

Po wyświetleniu `y` monitu wybierz opcję zainstalowania rozszerzenia.

Aby sprawdzić, czy rozszerzenie zostało zainstalowane, użyj następującego polecenia, aby wyświetlić listę podpoleceń specyficznych dla modułu ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Aktualizowanie rozszerzenia

Aby zaktualizować rozszerzenie interfejsu wiersza polecenia uczenia maszynowego, użyj następującego polecenia:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Usuwanie rozszerzenia

Aby usunąć rozszerzenie interfejsu wiersza polecenia, użyj następującego polecenia:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Zarządzanie zasobami

Poniższe polecenia pokazują, jak używać interfejsu wiersza polecenia do zarządzania zasobami używanymi przez usługę Azure Machine Learning.

+ Jeśli jeszcze go nie masz, utwórz grupę zasobów:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Tworzenie obszaru roboczego usługi Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > To polecenie tworzy podstawowy obszar roboczy edycji. Aby utworzyć obszar roboczy `--sku enterprise` przedsiębiorstwa, `az ml workspace create` użyj przełącznika z poleceniem. Aby uzyskać więcej informacji na temat wersji usługi Azure Machine Learning, zobacz [Co to jest usługa Azure Machine Learning.](overview-what-is-azure-ml.md#sku)

    Aby uzyskać więcej informacji, zobacz [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Dołącz konfigurację obszaru roboczego do folderu, aby włączyć rozpoznawanie kontekstów interfejsu wiersza polecenia.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    To polecenie `.azureml` tworzy podkatalog zawierający przykładowe pliki środowiska runconfig i conda. Zawiera również `config.json` plik, który jest używany do komunikowania się z obszarem roboczym usługi Azure Machine Learning.

    Aby uzyskać więcej informacji, zobacz [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Dołącz kontener obiektów blob platformy Azure jako magazyn danych.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Aby uzyskać więcej informacji, zobacz [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Przekazywanie plików do magazynu danych.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Aby uzyskać więcej informacji, zobacz [az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Dołącz klaster AKS jako obiekt docelowy obliczeń.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Utwórz nowy obiekt docelowy AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Aby uzyskać więcej informacji, zobacz [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Eksperymenty

* Rozpocznij przebieg eksperymentu. Korzystając z tego polecenia, określ nazwę pliku runconfig (tekst przed \*.runconfig, jeśli patrzysz na system plików) względem parametru -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Polecenie `az ml folder attach` tworzy `.azureml` podkatalog, który zawiera dwa przykładowe pliki runconfig. 
    >
    > Jeśli masz skrypt Języka Python, który tworzy obiekt konfiguracji uruchamiania programowo, można użyć [RunConfig.save(),](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) aby zapisać go jako plik runconfig.
    >
    > Pełny schemat runconfig można znaleźć w tym [pliku JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). Schemat jest samodokumianie `description` za pośrednictwem klucza każdego obiektu. Ponadto istnieją wyliczenia dla możliwych wartości i fragment szablonu na końcu.

    Aby uzyskać więcej informacji, zobacz [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Wyświetl listę eksperymentów:

    ```azurecli-interactive
    az ml experiment list
    ```

    Aby uzyskać więcej informacji, zobacz [listę eksperymentów az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Zarządzanie zestawem danych

Następujące polecenia pokazują, jak pracować z zestawami danych w usłudze Azure Machine Learning:

+ Zarejestruj zestaw danych:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Aby uzyskać informacje na temat formatu pliku JSON `az ml dataset register --show-template`użytego do zdefiniowania zestawu danych, należy użyć programu .

    Aby uzyskać więcej informacji, zobacz [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archiwizuj aktywny lub przestarzały zestaw danych:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Przestarzałe zestaw danych:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Wyświetl listę wszystkich zestawów danych w obszarze roboczym:

    ```azurecli-interactive
    az ml dataset list
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Uzyskaj szczegółowe informacje o zestawie danych:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Ponowne aktywowanie zarchiwizowanego lub przestarzałego zestawu danych:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset reaktywuj](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Wyrejestruuj zestaw danych:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Aby uzyskać więcej informacji, zobacz [az ml dataset wyrejestrować](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Zarządzanie środowiskiem

Poniższe polecenia pokazują, jak tworzyć, rejestrować i listy [środowisk](how-to-configure-environment.md) usługi Azure Machine Learning dla obszaru roboczego:

+ Tworzenie plików szkieletu dla środowiska:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Aby uzyskać więcej informacji, zobacz [az ml środowiska rusztowania](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Zarejestruj środowisko:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Aby uzyskać więcej informacji, zobacz [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Lista zarejestrowanych środowisk:

    ```azurecli-interactive
    az ml environment list
    ```

    Aby uzyskać więcej informacji, zobacz [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Pobierz zarejestrowane środowisko:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Aby uzyskać więcej informacji, zobacz [az ml środowiska download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Schemat konfiguracji środowiska

Jeśli użyto `az ml environment scaffold` tego polecenia, generuje `azureml_environment.json` plik szablonu, który może być modyfikowany i używany do tworzenia niestandardowych konfiguracji środowiska za pomocą interfejsu wiersza polecenia. Obiekt najwyższego poziomu luźno [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) mapuje do klasy w SDK języka Python. 

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

W poniższej tabeli opisano każde pole najwyższego poziomu w pliku JSON, jego typ i opis. Jeśli typ obiektu jest połączony z klasą z modułu SDK języka Python, istnieje luźne dopasowanie 1:1 między każdym polem JSON a nazwą zmiennej publicznej w klasie Python. W niektórych przypadkach pole może mapować do argumentu konstruktora, a nie zmiennej klasy. Na przykład `environmentVariables` pole jest `environment_variables` mapowana [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) na zmienną w klasie.

| Pole JSON | Typ | Opis |
|---|---|---|
| `name` | `string` | Nazwa środowiska. Nie należy rozpoczynać nazwy od **microsoftu** lub **usługi AzureML**. |
| `version` | `string` | Wersja środowiska. |
| `environmentVariables` | `{string: string}` | Mapa mieszania nazw i wartości zmiennych środowiskowych. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Obiekt, który definiuje środowisko Języka Python i interpreter do użycia w zasób obliczeniowy docelowy. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Definiuje ustawienia, aby dostosować obraz platformy Docker zbudowany zgodnie ze specyfikacjami środowiska. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | Sekcja konfiguruje ustawienia platformy Spark. Jest on używany tylko wtedy, gdy struktura jest ustawiona na PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Konfiguruje zależności biblioteki Databricks. |
| `inferencingStackVersion` | `string` | Określa wersję stosu wnioskowania dodaną do obrazu. Aby uniknąć dodawania stosu wnioskowania, `null`pozostaw to pole . Prawidłowa wartość: "najnowsza". |

## <a name="ml-pipeline-management"></a>Zarządzanie rurociągami ML

Następujące polecenia pokazują, jak pracować z potokami uczenia maszynowego:

+ Tworzenie potoku uczenia maszynowego:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Aby uzyskać więcej informacji, zobacz [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Aby uzyskać więcej informacji na temat pliku YAML potoku, zobacz [Definiowanie potoków uczenia maszynowego w YAML](reference-pipeline-yaml.md).

+ Uruchamianie potoku:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Aby uzyskać więcej informacji, zobacz [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Aby uzyskać więcej informacji na temat pliku YAML potoku, zobacz [Definiowanie potoków uczenia maszynowego w YAML](reference-pipeline-yaml.md).

+ Zaplanuj potok:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Aby uzyskać więcej informacji, zobacz [az ml potoku create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Aby uzyskać więcej informacji na temat pliku YAML harmonogramu potoku, zobacz [Definiowanie potoków uczenia maszynowego w YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Rejestracja modelu, profilowanie, wdrażanie

Następujące polecenia pokazują, jak zarejestrować przeszkolony model, a następnie wdrożyć go jako usługę produkcyjną:

+ Zarejestruj model za pomocą usługi Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Aby uzyskać więcej informacji, zobacz [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **OPCJONALNIE** Profil modelu, aby uzyskać optymalne wartości procesora CPU i pamięci do wdrożenia.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Aby uzyskać więcej informacji, zobacz [profil modelu az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Wdrażanie modelu w udręki AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Aby uzyskać więcej informacji na temat schematu pliku konfiguracyjnego wnioskowania, zobacz [Schemat konfiguracji wnioskowania](#inferenceconfig).
    
    Aby uzyskać więcej informacji na temat schematu pliku konfiguracji wdrożenia, zobacz [Schemat konfiguracji wdrożenia](#deploymentconfig).

    Aby uzyskać więcej informacji, zobacz [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Schemat konfiguracji wnioskowania

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia

### <a name="local-deployment-configuration-schema"></a>Schemat konfiguracji wdrożenia lokalnego

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Schemat konfiguracji wdrażania wystąpienia kontenera platformy Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Schemat konfiguracji wdrażania usługi Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Następne kroki

* [Odwołanie do polecenia rozszerzenia interfejsu wiersza polecenia uczenia maszynowego](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Szkolenie i wdrażanie modeli uczenia maszynowego przy użyciu usługi Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
