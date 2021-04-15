---
title: Wdrażanie modeli przy użyciu niestandardowego obrazu platformy Docker
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać niestandardowego obrazu podstawowego platformy Docker do wdrażania Azure Machine Learning podstawowych. Chociaż Azure Machine Learning udostępnia domyślny obraz podstawowy, możesz również użyć własnego obrazu podstawowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: f621bb2a7d4543620d22ab85fb8b44752c9989ac
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376260"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Wdrażanie modelu przy użyciu niestandardowego obrazu podstawowego platformy Docker

Dowiedz się, jak używać niestandardowego obrazu podstawowego platformy Docker podczas wdrażania wytrenowane modele za pomocą Azure Machine Learning.

Azure Machine Learning użyje domyślnego podstawowego obrazu platformy Docker, jeśli żaden nie zostanie określony. Określony obraz platformy Docker używany z programem można znaleźć w pliku `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Możesz również użyć __Azure Machine Learning,__ aby wybrać określony obraz podstawowy, lub użyć obrazu niestandardowego, który po prostu po prostu udostępnisz.

Obraz podstawowy jest używany jako punkt początkowy podczas tworzenia obrazu dla wdrożenia. Udostępnia podstawowy system operacyjny i składniki. Następnie proces wdrażania dodaje do obrazu dodatkowe składniki, takie jak model, środowisko conda i inne zasoby.

Zazwyczaj niestandardowy obraz podstawowy tworzy się, gdy chcesz zarządzać zależnościami przy użyciu platformy Docker, zachować ściślejszą kontrolę nad wersjami składników lub zaoszczędzić czas podczas wdrażania. Można również zainstalować oprogramowanie wymagane przez model, w przypadku którego proces instalacji zajmuje dużo czasu. Zainstalowanie oprogramowania podczas tworzenia obrazu podstawowego oznacza, że nie trzeba instalować go dla każdego wdrożenia.

> [!IMPORTANT]
> Podczas wdrażania modelu nie można zastąpić podstawowych składników, takich jak serwer internetowy lub IoT Edge składników. Te składniki zapewniają znane środowisko pracy, które jest testowane i obsługiwane przez firmę Microsoft.

> [!WARNING]
> Firma Microsoft może nie być w stanie rozwiązać problemów spowodowanych przez obraz niestandardowy. Jeśli wystąpią problemy, może zostać wyświetlony monit o użycie obrazu domyślnego lub jednego z obrazów dostępnych przez firmę Microsoft, aby sprawdzić, czy problem jest specyficzny dla danego obrazu.

Ten dokument został podzielić na dwie sekcje:

* Tworzenie niestandardowego obrazu podstawowego: udostępnia administratorom i metodyce DevOps informacje dotyczące tworzenia obrazu niestandardowego i konfigurowania uwierzytelniania w usłudze Azure Container Registry przy użyciu interfejsu wiersza polecenia platformy Azure i interfejsu Machine Learning wiersza polecenia.
* Wdrażanie modelu przy użyciu niestandardowego obrazu podstawowego: udostępnia informacje dla badaczy danych i metodyki DevOps/inżynierów ds. uczenia maszynowego dotyczące używania obrazów niestandardowych podczas wdrażania wytrenowany model z zestawu SDK języka Python lub interfejsu wiersza polecenia uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [artykuł Tworzenie obszaru roboczego.](how-to-manage-workspace.md)
* Zestaw [AZURE MACHINE LEARNING SDK](/python/api/overview/azure/ml/install). 
* Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* Rozszerzenie [interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Rejestr [Azure Container Registry](../container-registry/index.yml) lub inny rejestr platformy Docker, który jest dostępny w Internecie.
* W krokach w tym dokumencie założono,  że wiesz już, jak tworzyć i używać obiektu konfiguracji wnioskowania w ramach wdrażania modelu. Aby uzyskać więcej informacji, zobacz [Gdzie wdrożyć i jak](how-to-deploy-and-where.md)to zrobić.

## <a name="create-a-custom-base-image"></a>Tworzenie niestandardowego obrazu podstawowego

W informacjach w tej sekcji przyjęto założenie, że używasz Azure Container Registry do przechowywania obrazów platformy Docker. Podczas planowania tworzenia niestandardowych obrazów dla aplikacji należy użyć następującej Azure Machine Learning:

* Czy będziesz używać aplikacji utworzonej Azure Container Registry dla Azure Machine Learning roboczego, czy autonomicznego Azure Container Registry?

    W przypadku korzystania z obrazów __przechowywanych w rejestrze kontenerów__ dla obszaru roboczego nie trzeba uwierzytelniać się w rejestrze. Uwierzytelnianie jest obsługiwane przez obszar roboczy.

    > [!WARNING]
    > Adres Azure Container Registry obszaru roboczego jest tworzony podczas pierwszego trenowania lub __wdrażania modelu przy__ użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został on wytrenowany ani utworzony model, nie Azure Container Registry dla tego obszaru roboczego.

    W przypadku korzystania z obrazów przechowywanych w autonomicznym rejestrze __kontenerów__ należy skonfigurować jednostkę usługi, która ma co najmniej dostęp do odczytu. Następnie podaj identyfikator jednostki usługi (nazwę użytkownika) i hasło wszystkim użytkownikom, którzy będą korzystali z obrazów z rejestru. Wyjątek stanowi sytuacja, gdy rejestr kontenerów zostanie publicznie dostępny.

    Aby uzyskać informacje na temat tworzenia prywatnego Azure Container Registry, zobacz [Tworzenie prywatnego rejestru kontenerów.](../container-registry/container-registry-get-started-azure-cli.md)

    Aby uzyskać informacje na temat używania jednostki usługi z Azure Container Registry, zobacz [Azure Container Registry uwierzytelniania za pomocą jednostki usługi](../container-registry/container-registry-auth-service-principal.md).

* Azure Container Registry i informacje o obrazie: podaj nazwę obrazu wszystkim osobom, które muszą z niego korzystać. Na przykład obraz o nazwie , przechowywany w rejestrze o nazwie , jest przywołyny jako podczas używania obrazu `myimage` `myregistry` do `myregistry.azurecr.io/myimage` wdrażania modelu

### <a name="image-requirements"></a>Wymagania obrazu

Azure Machine Learning obsługuje tylko obrazy platformy Docker, które zapewniają następujące oprogramowanie:
* Ubuntu 16.04 lub więcej.
* Conda 4.5.# lub więcej.
* Python 3.6+.

Aby korzystać z zestawów danych, zainstaluj pakiet libfuse-dev. Pamiętaj również, aby zainstalować wszystkie pakiety przestrzeni użytkownika, które mogą być potrzebne.

Usługa Azure ML utrzymuje zestaw podstawowych obrazów procesora CPU i procesora GPU opublikowanych w usłudze Microsoft Container Registry, z których można opcjonalnie korzystać (lub odwoływać się) zamiast tworzyć własny obraz niestandardowy. Aby wyświetlić pliki Dockerfile dla tych obrazów, zapoznaj się z repozytorium [GitHub Azure/AzureML-Containers.](https://github.com/Azure/AzureML-Containers)

W przypadku obrazów procesora GPU usługa Azure ML oferuje obecnie zarówno obrazy podstawowe cuda9, jak i cuda10. Główne zależności zainstalowane w tych obrazach podstawowych to:

| Zależności | Procesor IntelMPI | OpenMPI CPU | IntelMPI GPU | OpenMPI GPU |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| Mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2,4 | 2,4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

Obrazy procesora CPU są zbudowane z systemu ubuntu16.04. Obrazy procesora GPU dla systemu Cuda9 są oparte na procesorze nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04. Obrazy procesorów GPU dla systemu Cuda10 są oparte na procesorze nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04.
<a id="getname"></a>

> [!IMPORTANT]
> W przypadku korzystania z niestandardowych obrazów platformy Docker zaleca się przypięcie wersji pakietu w celu zapewnienia lepszej odtwarzalności.

### <a name="get-container-registry-information"></a>Uzyskiwanie informacji o rejestrze kontenerów

W tej sekcji dowiesz się, jak uzyskać nazwę Azure Container Registry dla Azure Machine Learning roboczego.

> [!WARNING]
> Obszar Azure Container Registry jest tworzony podczas pierwszego trenowania lub wdrażania __modelu przy__ użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został on wytrenowany ani utworzony model, nie Azure Container Registry dla tego obszaru roboczego.

Jeśli już wytrenowaliśmy lub wdrożyliśmy modele przy użyciu Azure Machine Learning, dla obszaru roboczego został utworzony rejestr kontenerów. Aby znaleźć nazwę tego rejestru kontenerów, należy wykonać następujące czynności:

1. Otwórz nową powłokę lub wiersz polecenia i użyj następującego polecenia, aby uwierzytelnić się w subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

    Postępuj zgodnie z monitami, aby uwierzytelnić się w subskrypcji.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Użyj następującego polecenia, aby wyświetlić listę rejestru kontenerów dla obszaru roboczego. Zastąp `<myworkspace>` nazwą Azure Machine Learning obszaru roboczego. Zastąp `<resourcegroup>` grupę zasobów platformy Azure zawierającą obszar roboczy:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Zwrócone informacje są podobne do następującego tekstu:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Wartość `<registry_name>` to nazwa nazwy Azure Container Registry obszaru roboczego.

### <a name="build-a-custom-base-image"></a>Tworzenie niestandardowego obrazu podstawowego

Kroki opisane w tej sekcji dotyczące tworzenia niestandardowego obrazu platformy Docker w Azure Container Registry. Przykładowe pliki dockerfile można znaleźć w repozytorium [GitHub Azure/AzureML-Containers).](https://github.com/Azure/AzureML-Containers)

1. Utwórz nowy plik tekstowy o nazwie `Dockerfile` i użyj następującego tekstu jako zawartości:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. W powłoki lub wierszu polecenia użyj następującego polecenia, aby uwierzytelnić się w Azure Container Registry. Zastąp wartość nazwą rejestru kontenerów, w którym chcesz `<registry_name>` przechowywać obraz:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Aby przekazać plik Dockerfile i skompilować go, użyj następującego polecenia. Zastąp `<registry_name>` wartość nazwą rejestru kontenerów, w którym chcesz przechowywać obraz:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > W tym przykładzie do obrazu `:v1` jest stosowany tag . Jeśli tag nie zostanie podany, zostanie `:latest` zastosowany tag .

    Podczas procesu kompilacji informacje są przesyłane strumieniowo do wiersza polecenia. Jeśli kompilacja powiedzie się, zostanie wyświetlony komunikat podobny do następującego tekstu:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Aby uzyskać więcej informacji na temat tworzenia obrazów za pomocą Azure Container Registry, zobacz [Build and run a container image using zadania usługi Azure Container Registry (Kompilowanie](../container-registry/container-registry-quickstart-task-cli.md) i uruchamianie obrazu kontenera przy użyciu zadania usługi Azure Container Registry

Aby uzyskać więcej informacji na temat przekazywania istniejących obrazów do Azure Container Registry, zobacz Push your first image to a private Docker container registry (Wypychanie pierwszego obrazu do prywatnego rejestru [kontenerów platformy Docker).](../container-registry/container-registry-get-started-docker-cli.md)

## <a name="use-a-custom-base-image"></a>Używanie niestandardowego obrazu podstawowego

Aby użyć obrazu niestandardowego, potrzebne są następujące informacje:

* Nazwa __obrazu__. Na przykład jest `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` to ścieżka do prostego obrazu platformy Docker dostarczonego przez firmę Microsoft.

    > [!IMPORTANT]
    > W przypadku utworzonych obrazów niestandardowych pamiętaj, aby dołączyć tagi, które zostały użyte z obrazem. Na przykład jeśli obraz został utworzony przy użyciu określonego tagu, takiego jak `:v1` . Jeśli podczas tworzenia obrazu nie używasz określonego tagu, został `:latest` zastosowany tag .

* Jeśli obraz znajduje się w __prywatnym repozytorium__, potrzebne są następujące informacje:

    * Adres __rejestru__. Na przykład `myregistry.azureecr.io`.
    * Nazwa użytkownika __i__ hasło __jednostki__ usługi z dostępem do odczytu do rejestru.

    Jeśli nie masz tych informacji, porozmawiaj z administratorem w celu Azure Container Registry zawierającego Twój obraz.

### <a name="publicly-available-base-images"></a>Publicznie dostępne obrazy podstawowe

Firma Microsoft udostępnia kilka obrazów platformy Docker w publicznie dostępnym repozytorium, którego można użyć w krokach w tej sekcji:

| Obraz | Opis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Podstawowy obraz dla Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Zawiera środowisko uruchomieniowe ONNX do wnioskowania procesora CPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Zawiera środowisko uruchomieniowe ONNX i cuda dla procesora GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Zawiera środowisko uruchomieniowe ONNX i tensorRT dla procesora GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Zawiera środowisko uruchomieniowe ONNX i projekt OpenVINO dla akceleratora usługi Intel Vision oparty na <sup></sup> procesorach<sup>VPU</sup> Movidius TM MyriadX |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Zawiera środowisko uruchomieniowe ONNX i pamięć USB OpenVINO for Intel <sup></sup> Movidius<sup>TM</sup> |

Aby uzyskać więcej informacji na temat obrazów podstawowych środowiska uruchomieniowego ONNX, zobacz sekcję [plik dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) środowiska uruchomieniowego ONNX w repozytorium GitHub.

> [!TIP]
> Ponieważ te obrazy są publicznie dostępne, podczas korzystania z nich nie trzeba podaniem adresu, nazwy użytkownika ani hasła.

Aby uzyskać więcej informacji, [zobacz Azure Machine Learning repozytorium kontenerów](https://github.com/Azure/AzureML-Containers) w witrynie GitHub.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Używanie obrazu z zestawem SDK Azure Machine Learning SDK

Aby użyć obrazu przechowywanego w Azure Container Registry dla obszaru roboczego lub publicznie dostępnego rejestru kontenerów, ustaw [następujące](/python/api/azureml-core/azureml.core.environment.environment) atrybuty środowiska:  

+ `docker.enabled=True`
+ `docker.base_image`: ustaw wartość na rejestr i ścieżkę do obrazu.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Aby użyć obrazu  z prywatnego rejestru kontenerów, który nie znajduje się w obszarze roboczym, należy użyć funkcji , aby określić adres repozytorium oraz nazwę użytkownika `docker.base_image_registry` i hasło:

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

Musisz dodać wartość azureml-defaults z wersją >= 1.0.45 jako zależność pip. Ten pakiet zawiera funkcje potrzebne do hostowania modelu jako usługi internetowej. Należy również ustawić inferencing_stack_version w środowisku na "latest", co spowoduje zainstalowanie określonych pakietów apt wymaganych przez usługę internetową. 

Po zdefiniowaniu środowiska użyj go z obiektem [InferenceConfig,](/python/api/azureml-core/azureml.core.model.inferenceconfig) aby zdefiniować środowisko wnioskowania, w którym będzie uruchamiany model i usługa internetowa.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

W tym momencie możesz kontynuować wdrażanie. Na przykład poniższy fragment kodu wdroży usługę internetową lokalnie przy użyciu konfiguracji wnioskowania i obrazu niestandardowego:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji na temat wdrażania, zobacz [Wdrażanie modeli za pomocą Azure Machine Learning](how-to-deploy-and-where.md).

Aby uzyskać więcej informacji na temat dostosowywania środowiska Python, zobacz Tworzenie środowisk do trenowania i wdrażania [oraz zarządzanie nimi.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Używanie obrazu z interfejsem wiersza polecenia Machine Learning interfejsu wiersza polecenia

> [!IMPORTANT]
> Obecnie interfejs wiersza Machine Learning może używać obrazów z witryny Azure Container Registry dla obszaru roboczego lub publicznie dostępnych repozytoriów. Nie może używać obrazów z autonomicznych rejestrów prywatnych.

Przed wdrożeniem modelu przy użyciu interfejsu wiersza Machine Learning utwórz środowisko [korzystające](/python/api/azureml-core/azureml.core.environment.environment) z obrazu niestandardowego. Następnie utwórz plik konfiguracji wnioskowania, który odwołuje się do środowiska. Środowisko można również zdefiniować bezpośrednio w pliku konfiguracji wnioskowania. Poniższy dokument JSON pokazuje, jak odwoływać się do obrazu w publicznym rejestrze kontenerów. W tym przykładzie środowisko jest zdefiniowane w tekście:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
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

Ten plik jest używany z `az ml model deploy` poleceniem . Parametr `--ic` służy do określania pliku konfiguracji wnioskowania.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Aby uzyskać więcej informacji na temat wdrażania modelu przy użyciu interfejsu wiersza polecenia [uczenia maszynowego,](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) zobacz sekcję "Rejestracja, profilowanie i wdrażanie modelu" w artykule Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning uczenia maszynowego.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tym, gdzie wdrożyć i jak](how-to-deploy-and-where.md)to zrobić.
* Dowiedz się, jak [trenować i wdrażać modele uczenia maszynowego przy użyciu Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning).