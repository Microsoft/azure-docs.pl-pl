---
title: Wdrażanie modeli z niestandardowym obrazem platformy Docker
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać niestandardowego obrazu podstawowego platformy Docker podczas wdrażania modeli usługi Azure Machine Learning. Usługa Azure Machine Learning udostępnia domyślny obraz podstawowy, ale można również użyć własnego obrazu podstawowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: a237beb72e35a236e353c58db520a8d611fdfdcd
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617999"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Wdrażanie modelu przy użyciu niestandardowego obrazu podstawowego platformy Docker
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dowiedz się, jak używać niestandardowego obrazu podstawowego platformy Docker podczas wdrażania uczonych modeli za pomocą usługi Azure Machine Learning.

Podczas wdrażania uczonego modelu w usłudze sieci web lub urządzeniu usługi IoT Edge tworzony jest pakiet zawierający serwer sieci web do obsługi żądań przychodzących.

Usługa Azure Machine Learning udostępnia domyślny obraz podstawowy platformy Docker, dzięki czemu nie musisz się martwić o jego utworzenie. Można również użyć __środowiska__ usługi Azure Machine Learning, aby wybrać określony obraz podstawowy lub użyć niestandardowego, który podasz.

Obraz podstawowy jest używany jako punkt wyjścia podczas tworzenia obrazu dla wdrożenia. Zapewnia podstawowy system operacyjny i składniki. Proces wdrażania następnie dodaje dodatkowe składniki, takie jak model, środowisko conda i inne zasoby, do obrazu przed wdrożeniem go.

Zazwyczaj można utworzyć niestandardowy obraz podstawowy, gdy chcesz używać platformy Docker do zarządzania zależnościami, utrzymania ściślejszej kontroli nad wersjami składników lub zaoszczędzenia czasu podczas wdrażania. Na przykład można ustandaryzować na określonej wersji Pythona, Conda lub innego składnika. Można również zainstalować oprogramowanie wymagane przez model, gdzie proces instalacji zajmuje dużo czasu. Zainstalowanie oprogramowania podczas tworzenia obrazu podstawowego oznacza, że nie trzeba go instalować dla każdego wdrożenia.

> [!IMPORTANT]
> Podczas wdrażania modelu nie można zastąpić podstawowych składników, takich jak serwer sieci web lub składniki usługi IoT Edge. Te składniki zapewniają znane środowisko pracy, które jest testowane i obsługiwane przez firmę Microsoft.

> [!WARNING]
> Firma Microsoft może nie być w stanie pomóc w rozwiązywaniu problemów spowodowanych przez obraz niestandardowy. W przypadku wystąpienia problemów może zostać wyświetlony monit o użycie obrazu domyślnego lub jednego z obrazów udostępnianych przez firmę Microsoft, aby sprawdzić, czy problem jest specyficzny dla obrazu.

Ten dokument jest podzielony na dwie sekcje:

* Tworzenie niestandardowego obrazu podstawowego: zawiera informacje dla administratorów i devops na temat tworzenia obrazu niestandardowego i konfigurowania uwierzytelniania do rejestru kontenerów platformy Azure przy użyciu interfejsu wiersza polecenia interfejsu wiersza polecenia usługi Azure i uczenia maszynowego.
* Wdrażanie modelu przy użyciu niestandardowego obrazu podstawowego: Zawiera informacje dla analityków danych i inżynierów DevOps / ML na temat używania niestandardowych obrazów podczas wdrażania przeszkolonego modelu z interfejsu SDK języka Python lub interfejsu wiersza polecenia ML.

## <a name="prerequisites"></a>Wymagania wstępne

* Grupa robocza usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego.](how-to-manage-workspace.md)
* Zestaw [SDK usługi Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla usługi Azure Machine Learning](reference-azure-machine-learning-cli.md).
* [Usługa Azure Container Registry](/azure/container-registry) lub inny rejestr platformy Docker, który jest dostępny w Internecie.
* Kroki w tym dokumencie zakładają, że są zaznajomieni z tworzenia i przy użyciu obiektu __konfiguracji wnioskowania w__ ramach wdrażania modelu. Aby uzyskać więcej informacji, zobacz sekcję "Przygotuj się do wdrożenia" [w sekcji Gdzie wdrożyć i jak](how-to-deploy-and-where.md#prepare-to-deploy).

## <a name="create-a-custom-base-image"></a>Tworzenie niestandardowego obrazu podstawowego

Informacje zawarte w tej sekcji przyjęto założenie, że używasz rejestru kontenerów platformy Azure do przechowywania obrazów platformy Docker. Podczas planowania tworzenia obrazów niestandardowych dla usługi Azure Machine Learning należy użyć następującej listy kontrolnej:

* Czy użyjesz rejestru kontenerów platformy Azure utworzonego dla obszaru roboczego usługi Azure Machine Learning lub autonomicznego rejestru kontenerów platformy Azure?

    Podczas korzystania z obrazów przechowywanych w __rejestrze kontenerów dla obszaru roboczego__nie trzeba uwierzytelniać się w rejestrze. Uwierzytelnianie jest obsługiwane przez obszar roboczy.

    > [!WARNING]
    > Usługa Azure Container Registry dla obszaru roboczego jest __tworzony przy pierwszym szkoleniu lub wdrażaniu modelu__ przy użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został przeszkolony lub utworzony model, nie azure container registry będzie istnieć dla obszaru roboczego.

    Aby uzyskać informacje na temat pobierania nazwy rejestru kontenerów platformy Azure dla obszaru roboczego, zobacz [Pobierz nazwę rejestru kontenera](#getname) sekcji tego artykułu.

    Podczas korzystania z obrazów przechowywanych w __rejestrze kontenerów autonomicznych__należy skonfigurować jednostkę usługi, która ma co najmniej dostęp do odczytu. Następnie należy podać identyfikator jednostki usługi (nazwa użytkownika) i hasło do każdego, kto używa obrazów z rejestru. Wyjątkiem jest sytuacja, w której rejestr kontenerów jest publicznie dostępny.

    Aby uzyskać informacje dotyczące tworzenia prywatnego rejestru kontenerów platformy Azure, zobacz [Tworzenie rejestru kontenerów prywatnych](/azure/container-registry/container-registry-get-started-azure-cli).

    Aby uzyskać informacje na temat korzystania z podmiotów korzystających z usługi Azure Container Registry, zobacz [Uwierzytelnianie rejestru kontenerów platformy Azure z podmiotami usługi.](/azure/container-registry/container-registry-auth-service-principal)

* Usługa Azure Container Registry i informacje o obrazach: Podaj nazwę obrazu każdemu, kto musi go używać. Na przykład obraz `myimage`o nazwie , `myregistry`przechowywane w rejestrze o nazwie , odwołuje się jak `myregistry.azurecr.io/myimage` podczas korzystania z obrazu do wdrożenia modelu

* Wymagania dotyczące obrazów: usługa Azure Machine Learning obsługuje tylko obrazy platformy Docker, które zapewniają następujące oprogramowanie:

    * Ubuntu 16.04 lub więcej.
    * Conda 4.5.# lub większa.
    * Python 3.5.# lub 3.6.#.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Uzyskaj informacje o rejestrze kontenerów

W tej sekcji dowiesz się, jak uzyskać nazwę rejestru kontenerów platformy Azure dla obszaru roboczego usługi Azure Machine Learning.

> [!WARNING]
> Usługa Azure Container Registry dla obszaru roboczego jest __tworzony przy pierwszym szkoleniu lub wdrażaniu modelu__ przy użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został przeszkolony lub utworzony model, nie azure container registry będzie istnieć dla obszaru roboczego.

Jeśli masz już przeszkolonych lub wdrożonych modeli przy użyciu usługi Azure Machine Learning, rejestr kontenerów został utworzony dla obszaru roboczego. Aby znaleźć nazwę tego rejestru kontenerów, należy wykonać następujące czynności:

1. Otwórz nową powłokę lub wiersz polecenia i użyj następującego polecenia do uwierzytelnienia w ramach subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

    Postępuj zgodnie z instrukcjami, aby uwierzytelnić się w subskrypcji.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Użyj następującego polecenia, aby wyświetlić listę rejestru kontenerów dla obszaru roboczego. Zamień `<myworkspace>` na nazwę obszaru roboczego usługi Azure Machine Learning. Zamień `<resourcegroup>` grupę zasobów platformy Azure zawierającą obszar roboczy:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Zwracane informacje są podobne do następującego tekstu:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Wartość `<registry_name>` to nazwa rejestru kontenerów platformy Azure dla obszaru roboczego.

### <a name="build-a-custom-base-image"></a>Tworzenie niestandardowego obrazu bazowego

Kroki opisane w tej sekcji — tworzenie niestandardowego obrazu platformy Docker w rejestrze kontenerów platformy Azure.

1. Utwórz nowy plik `Dockerfile`tekstowy o nazwie i użyj następującego tekstu jako zawartości:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. W przypadku powłoki lub wiersza polecenia użyj następujących czynności, aby uwierzytelnić się w rejestrze kontenerów platformy Azure. Zastąp `<registry_name>` nazwę rejestru kontenerów, w której chcesz zapisać obraz:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Aby przekazać plik Dockerfile i utworzyć go, użyj następującego polecenia. Zamień `<registry_name>` na nazwę rejestru kontenerów, w której chcesz zapisać obraz:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > W tym przykładzie `:v1` znacznik jest stosowany do obrazu. Jeśli nie podano tagu, `:latest` zostanie zastosowany znacznik.

    Podczas procesu kompilacji informacje są przesyłane strumieniowo z powrotem do wiersza polecenia. Jeśli kompilacja zakończy się pomyślnie, zostanie wyświetlony komunikat podobny do następującego tekstu:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Aby uzyskać więcej informacji na temat tworzenia obrazów za pomocą rejestru kontenerów platformy Azure, zobacz [Tworzenie i uruchamianie obrazu kontenera przy użyciu zadań rejestru kontenerów platformy Azure](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Aby uzyskać więcej informacji na temat przekazywania istniejących obrazów do rejestru kontenerów platformy Azure, zobacz [Wypychanie pierwszego obrazu do prywatnego rejestru kontenerów platformy Docker.](/azure/container-registry/container-registry-get-started-docker-cli)

## <a name="use-a-custom-base-image"></a>Używanie niestandardowego obrazu podstawowego

Aby użyć obrazu niestandardowego, potrzebne są następujące informacje:

* __Nazwa obrazu__. Na przykład `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` jest ścieżką do podstawowego obrazu platformy Docker dostarczonym przez firmę Microsoft.

    > [!IMPORTANT]
    > W przypadku utworzonych obrazów niestandardowych należy dołączyć wszystkie znaczniki, które były używane z obrazem. Jeśli na przykład obraz został utworzony przy określonym `:v1`tagu, takim jak . Jeśli podczas tworzenia obrazu nie użyto określonego znacznika, `:latest` zastosowano znacznik.

* Jeśli obraz znajduje się w __prywatnym repozytorium,__ potrzebne są następujące informacje:

    * __Adres__rejestru . Na przykład `myregistry.azureecr.io`.
    * Nazwa __użytkownika__ i __hasło__ jednostki usługi, która ma dostęp do odczytu do rejestru.

    Jeśli nie masz tych informacji, porozmawiaj z administratorem rejestru kontenerów platformy Azure, który zawiera obraz.

### <a name="publicly-available-base-images"></a>Publicznie dostępne obrazy podstawowe

Firma Microsoft udostępnia kilka obrazów dokowych w publicznie dostępnym repozytorium, które może być używane z krokami w tej sekcji:

| Image (Obraz) | Opis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Podstawowy obraz usługi Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Zawiera środowisko wykonawcze ONNX dla wnioskowania procesora |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Zawiera środowisko wykonawcze ONNX i CUDA dla gpu |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Zawiera onnx runtime i TensorRT dla GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Zawiera onnx runtime i OpenVINO for Intel<sup> </sup> Vision Accelerator Design based on Movidius<sup>TM</sup> MyriadX VPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Zawiera onnx runtime i OpenVINO dla pamięci USB Intel<sup> </sup> Movidius<sup>TM</sup> |

Aby uzyskać więcej informacji na temat obrazów podstawowych środowiska wykonawczego ONNX, zobacz [sekcję dockerfile środowiska wykonawczego ONNX](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) w repozytorium GitHub.

> [!TIP]
> Ponieważ te obrazy są publicznie dostępne, nie trzeba podawać adresu, nazwy użytkownika ani hasła podczas korzystania z nich.

Aby uzyskać więcej informacji, zobacz [kontenery usługi Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

> [!TIP]
>__Jeśli model jest przeszkolony w zakresie obliczeń usługi Azure Machine Learning,__ przy użyciu __wersji 1.0.22 lub większej__ sdk usługi Azure Machine Learning, obraz jest tworzony podczas szkolenia. Aby odkryć nazwę tego obrazu, użyj programu `run.properties["AzureML.DerivedImageName"]`. W poniższym przykładzie pokazano, jak używać tego obrazu:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Używanie obrazu przy użyciu zestawu SDK usługi Azure Machine Learning

Aby użyć obrazu przechowywanego w **rejestrze kontenerów platformy Azure dla obszaru roboczego**lub **rejestru kontenerów, który jest publicznie dostępny,** ustaw następujące atrybuty [środowiska:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

+ `docker.enabled=True`
+ `docker.base_image`: Ustaw rejestr i ścieżkę do obrazu.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Aby użyć obrazu z __rejestru kontenerów prywatnych,__ który nie `docker.base_image_registry` znajduje się w obszarze roboczym, należy określić adres repozytorium oraz nazwę użytkownika i hasło:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Należy dodać azureml-defaults z wersji >= 1.0.45 jako zależności pip. Ten pakiet zawiera funkcje potrzebne do obsługi modelu jako usługi sieci web. Należy również ustawić inferencing_stack_version właściwości w środowisku na "najnowsze", spowoduje zainstalowanie określonych pakietów apt potrzebnych do usługi sieci web. 

Po zdefiniowaniu środowiska, należy go używać z [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) obiektu do definiowania środowiska wnioskowania, w którym będzie uruchamiany model i usługa sieci web.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

W tym momencie można kontynuować wdrażanie. Na przykład następujący fragment kodu można wdrożyć usługę sieci web lokalnie przy użyciu konfiguracji wnioskowania i obrazu niestandardowego:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji na temat wdrażania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk szkoleniowych i wdrożeniowych oraz zarządzanie nimi.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Używanie obrazu z interfejsu wiersza polecenia uczenia maszynowego

> [!IMPORTANT]
> Obecnie interfejsu wiersza polecenia uczenia maszynowego można użyć obrazów z rejestru kontenerów platformy Azure dla obszaru roboczego lub publicznie dostępnych repozytoriów. Nie można używać obrazów z autonomicznych rejestrów prywatnych.

Przed wdrożeniem modelu przy użyciu interfejsu wiersza polecenia uczenia maszynowego należy utworzyć [środowisko,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) które używa obrazu niestandardowego. Następnie utwórz plik konfiguracyjny wnioskowania, który odwołuje się do środowiska. Środowisko można również zdefiniować bezpośrednio w pliku konfiguracyjnym wnioskowania. W poniższym dokumencie JSON pokazano, jak odwoływać się do obrazu w rejestrze kontenerów publicznych. W tym przykładzie środowisko jest zdefiniowane w linii:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Ten plik jest `az ml model deploy` używany z poleceniem. Parametr `--ic` jest używany do określania pliku konfiguracyjnego wnioskowania.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Aby uzyskać więcej informacji na temat wdrażania modelu przy użyciu interfejsu wiersza polecenia ml, zobacz sekcję "rejestracja modelu, profilowanie i wdrażanie" [rozszerzenia interfejsu wiersza polecenia dla usługi Azure Machine Learning.](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tym, gdzie wdrożyć i jak](how-to-deploy-and-where.md).
* Dowiedz się, jak [szkolić i wdrażać modele uczenia maszynowego przy użyciu usługi Azure Pipelines.](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)
