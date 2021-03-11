---
title: Wdrażanie modeli przy użyciu niestandardowego obrazu platformy Docker
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrażać modele Azure Machine Learning przy użyciu niestandardowego obrazu platformy Docker. Gdy Azure Machine Learning udostępnia domyślny obraz podstawowy, można również użyć własnego obrazu podstawowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 440723167ded15933ba46a3a58f9a873496bd6a7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609366"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Wdrażanie modelu przy użyciu niestandardowego obrazu platformy Docker

Dowiedz się, jak używać niestandardowego obrazu podstawowego platformy Docker podczas wdrażania przeszkolonych modeli przy użyciu Azure Machine Learning.

Jeśli żaden nie zostanie określony, Azure Machine Learning użyje domyślnego obrazu platformy Docker. Można znaleźć konkretny obraz platformy Docker używany z programem `azureml.core.runconfig.DEFAULT_CPU_IMAGE` . Możesz również użyć __środowiska__ Azure Machine Learning do wybrania określonego obrazu podstawowego lub użyć podanego niestandardowego.

Obraz podstawowy jest używany jako punkt wyjścia podczas tworzenia obrazu dla wdrożenia. Udostępnia on podstawowy system operacyjny i składniki. Następnie proces wdrażania dodaje do obrazu dodatkowe składniki, takie jak model, środowisko Conda i inne zasoby.

Zazwyczaj tworzysz niestandardowy obraz podstawowy, gdy chcesz używać platformy Docker do zarządzania zależnościami, zachować ściślejszą kontrolę nad wersjami składników lub zaoszczędzić czas podczas wdrażania. Możesz również zainstalować oprogramowanie wymagane przez model, w którym proces instalacji zajmuje dużo czasu. Zainstalowanie oprogramowania podczas tworzenia obrazu podstawowego oznacza, że nie trzeba go instalować dla każdego wdrożenia.

> [!IMPORTANT]
> Podczas wdrażania modelu nie można przesłonić składników podstawowych, takich jak serwer sieci Web lub składniki IoT Edge. Te składniki zapewniają znane środowisko robocze, które jest testowane i obsługiwane przez firmę Microsoft.

> [!WARNING]
> Firma Microsoft może nie pomóc w rozwiązywaniu problemów spowodowanych przez niestandardowy obraz. Jeśli wystąpią problemy, może zostać wyświetlony monit o użycie obrazu domyślnego lub jednego z obrazów firmy Microsoft, aby sprawdzić, czy problem jest specyficzny dla Twojego obrazu.

Ten dokument jest podzielony na dwie sekcje:

* Tworzenie niestandardowego obrazu podstawowego: zawiera informacje dla administratorów i DevOps o tworzeniu niestandardowego obrazu oraz konfigurowaniu uwierzytelniania do Azure Container Registry przy użyciu interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia Machine Learning.
* Wdróż model przy użyciu niestandardowego obrazu podstawowego: zawiera informacje dla analityków danych i inżynierów DevOps/ML na temat używania obrazów niestandardowych podczas wdrażania przeszkolonego modelu z zestawu SDK języka Python lub interfejsu wiersza polecenia.

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* [Zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install). 
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* [Azure Container Registry](../container-registry/index.yml) lub innych rejestrów platformy Docker, które są dostępne w Internecie.
* W procedurach przedstawionych w tym dokumencie założono, że wiesz już, jak utworzyć i użyć obiektu __konfiguracji wnioskowania__ w ramach wdrażania modelu. Aby uzyskać więcej informacji, zobacz [gdzie można wdrożyć i jak to zrobić](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Tworzenie niestandardowego obrazu podstawowego

Informacje w tej sekcji założono, że używasz Azure Container Registry do przechowywania obrazów platformy Docker. Podczas planowania tworzenia obrazów niestandardowych dla Azure Machine Learning Użyj poniższej listy kontrolnej:

* Czy będziesz używać Azure Container Registry utworzonych dla obszaru roboczego Azure Machine Learning lub autonomicznej Azure Container Registry?

    W przypadku używania obrazów przechowywanych w __rejestrze kontenerów dla obszaru roboczego__ nie trzeba uwierzytelniać się w rejestrze. Uwierzytelnianie jest obsługiwane przez obszar roboczy.

    > [!WARNING]
    > Azure Container Registry dla obszaru roboczego jest __tworzony podczas pierwszego uczenia lub wdrożenia modelu__ przy użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został przeszkolony ani utworzony model, w obszarze roboczym nie będą dostępne żadne Azure Container Registry.

    W przypadku używania obrazów przechowywanych w __autonomicznym rejestrze kontenerów__ należy skonfigurować jednostkę usługi, która ma co najmniej dostęp do odczytu. Następnie podaj identyfikator jednostki usługi (username) i hasło dla każdej osoby, która używa obrazów z rejestru. Wyjątek polega na tym, że rejestr kontenerów jest publicznie dostępny.

    Aby uzyskać informacje na temat tworzenia prywatnego Azure Container Registry, zobacz [Tworzenie prywatnego rejestru kontenerów](../container-registry/container-registry-get-started-azure-cli.md).

    Aby uzyskać informacje na temat używania nazw głównych usługi z Azure Container Registry, zobacz [Azure Container Registry Authentication z](../container-registry/container-registry-auth-service-principal.md)jednostkami usługi.

* Informacje o Azure Container Registry i obrazie: Podaj nazwę obrazu dla każdego, kto musi go używać. Na przykład obraz o nazwie `myimage` przechowywane w rejestrze o nazwie `myregistry` jest przywoływany w `myregistry.azurecr.io/myimage` przypadku użycia obrazu do wdrożenia modelu

### <a name="image-requirements"></a>Wymagania obrazu

Azure Machine Learning obsługuje tylko obrazy platformy Docker, które udostępniają następujące oprogramowanie:
* Ubuntu 16,04 lub nowszy.
* Conda 4.5. # lub nowszej.
* Python 3.5 +.

Aby użyć zestawów danych, zainstaluj pakiet libfuse-dev. Upewnij się również, że zainstalowano wszystkie pakiety miejsca użytkownika, które mogą być potrzebne.

Usługa Azure ML obsługuje zestaw obrazów podstawowych procesora CPU i procesora GPU opublikowanych w firmie Microsoft Container Registry, które można opcjonalnie wykorzystać (lub odwołać) zamiast tworzyć własne obrazy niestandardowe. Aby wyświetlić wieloetapowe dockerfile dla tych obrazów, zapoznaj się z repozytorium GitHub [platformy Azure/usługi Uczenie](https://github.com/Azure/AzureML-Containers) maszynowe.

W przypadku obrazów procesora GPU platforma Azure ML obecnie oferuje obrazy podstawowe cuda9 i cuda10. Główne zależności zainstalowane w tych obrazach podstawowych to:

| Zależności | Procesor IntelMPI | Procesor OpenMPI | Procesor GPU IntelMPI | Procesor GPU OpenMPI |
| --- | --- | --- | --- | --- |
| miniconda | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 | = = 4.5.11 |
| MPI | intelmpi = = 2018.3.222 |OpenMPI = = 3.1.2 |intelmpi = = 2018.3.222| OpenMPI = = 3.1.2 |
| cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2,4 | 2,4 |
| git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

Obrazy procesora CPU są tworzone na podstawie Ubuntu 16.04. Obrazy procesora GPU dla cuda9 są tworzone za pomocą technologii NVIDIA/CUDA: 9.0-cudnn7-devel-Ubuntu 16.04. Obrazy procesora GPU dla cuda10 są tworzone z poziomu technologii NVIDIA/CUDA: 10.0-cudnn7-devel-Ubuntu 16.04.
<a id="getname"></a>

> [!IMPORTANT]
> W przypadku używania niestandardowych obrazów platformy Docker zaleca się Przypinanie wersji pakietów w celu lepszego zapewnienia odtwarzalności.

### <a name="get-container-registry-information"></a>Pobierz informacje o rejestrze kontenera

W tej sekcji dowiesz się, jak uzyskać nazwę Azure Container Registry dla obszaru roboczego Azure Machine Learning.

> [!WARNING]
> Azure Container Registry dla obszaru roboczego jest __tworzony podczas pierwszego uczenia lub wdrożenia modelu__ przy użyciu obszaru roboczego. Jeśli utworzono nowy obszar roboczy, ale nie został przeszkolony ani utworzony model, w obszarze roboczym nie będą dostępne żadne Azure Container Registry.

Jeśli modele zostały już przeszkolone lub wdrożone przy użyciu Azure Machine Learning, dla Twojego obszaru roboczego został utworzony rejestr kontenerów. Aby znaleźć nazwę tego rejestru kontenerów, wykonaj następujące czynności:

1. Otwórz nową powłokę lub wiersz polecenia i użyj następującego polecenia, aby uwierzytelnić się w ramach subskrypcji platformy Azure:

    ```azurecli-interactive
    az login
    ```

    Postępuj zgodnie z monitami, aby uwierzytelnić się w subskrypcji.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Użyj poniższego polecenia, aby wyświetlić listę rejestrów kontenerów dla obszaru roboczego. Zamień `<myworkspace>` na nazwę obszaru roboczego Azure Machine Learning. Zamień `<resourcegroup>` na grupę zasobów platformy Azure, która zawiera obszar roboczy:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Zwracane informacje są podobne do następujących:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    `<registry_name>`Wartość to nazwa Azure Container Registry obszaru roboczego.

### <a name="build-a-custom-base-image"></a>Tworzenie niestandardowego obrazu podstawowego

Kroki opisane w tej sekcji przedstawiają Tworzenie niestandardowego obrazu platformy Docker w Azure Container Registry. Aby zapoznać się z przykładową wieloetapowe dockerfile, zobacz [Azure/Azure-Containers](https://github.com/Azure/AzureML-Containers) repozytorium GitHub.

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

2. Z poziomu powłoki lub wiersza polecenia Użyj następujących poleceń, aby uwierzytelnić się w Azure Container Registry. Zastąp ciąg `<registry_name>` nazwą rejestru kontenera, w którym ma być przechowywany obraz:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Aby przekazać pliku dockerfile i skompilować go, użyj następującego polecenia. Zamień `<registry_name>` na nazwę rejestru kontenera, w którym ma być przechowywany obraz:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > W tym przykładzie tag `:v1` jest stosowany do obrazu. Jeśli nie podano tagu, `:latest` zostanie zastosowany tag.

    W trakcie procesu kompilacji informacje są przesyłane strumieniowo z powrotem do wiersza polecenia. Jeśli kompilacja zakończy się pomyślnie, zostanie wyświetlony komunikat podobny do następującego:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Aby uzyskać więcej informacji na temat tworzenia obrazów przy użyciu Azure Container Registry, zobacz [Kompilowanie i uruchamianie obrazu kontenera przy użyciu Azure Container Registry zadań](../container-registry/container-registry-quickstart-task-cli.md)

Aby uzyskać więcej informacji na temat przekazywania istniejących obrazów do Azure Container Registry, zobacz [wypychanie pierwszego obrazu do prywatnego rejestru kontenerów platformy Docker](../container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-base-image"></a>Używanie niestandardowego obrazu podstawowego

Aby użyć obrazu niestandardowego, potrzebne są następujące informacje:

* __Nazwa obrazu__. Na przykład `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` jest ścieżką do prostego obrazu platformy Docker dostarczonego przez firmę Microsoft.

    > [!IMPORTANT]
    > W przypadku obrazów niestandardowych, które zostały utworzone, pamiętaj o uwzględnieniu wszelkich tagów, które były używane w obrazie. Na przykład, jeśli obraz został utworzony przy użyciu określonego tagu, takiego jak `:v1` . Jeśli nie korzystasz z określonego tagu podczas tworzenia obrazu, znacznik `:latest` został zastosowany.

* Jeśli obraz znajduje się w __repozytorium prywatnym__, potrzebne są następujące informacje:

    * __Adres__ rejestru. Na przykład `myregistry.azureecr.io`.
    * __Nazwa__ główna usługi i __hasło__ , które mają dostęp do odczytu do rejestru.

    Jeśli nie masz tych informacji, porozmawiaj z administratorem, aby uzyskać Azure Container Registry, który zawiera obraz.

### <a name="publicly-available-base-images"></a>Publicznie dostępne obrazy podstawowe

Firma Microsoft udostępnia kilka obrazów platformy Docker w publicznie dostępnym repozytorium, które może być używane z krokami z tej sekcji:

| Image (Obraz) | Opis |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Obraz podstawowy dla Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Zawiera środowisko uruchomieniowe ONNX dla procesora CPU inferencing |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Zawiera środowisko uruchomieniowe ONNX i CUDA dla procesora GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Zawiera środowisko uruchomieniowe ONNX i TensorRT dla procesora GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Zawiera środowisko uruchomieniowe ONNX i OpenVINO dla <sup></sup> projektu akceleratora Intel Vision na podstawie Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Zawiera środowisko uruchomieniowe ONNX i OpenVINO dla <sup></sup> urządzeń USB Intel Movidius<sup>TM</sup> |

Aby uzyskać więcej informacji na temat obrazów podstawowych środowiska uruchomieniowego ONNX, zobacz [sekcję ONNX Runtime pliku dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) w repozytorium GitHub.

> [!TIP]
> Ponieważ te obrazy są publicznie dostępne, nie trzeba podawać adresu, nazwy użytkownika ani hasła podczas ich używania.

Aby uzyskać więcej informacji, zobacz repozytorium [kontenerów Azure Machine Learning](https://github.com/Azure/AzureML-Containers) w witrynie GitHub.

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Korzystanie z obrazu z zestawem SDK Azure Machine Learning

Aby użyć obrazu przechowywanego w **Azure Container Registry dla obszaru roboczego** lub **dostępnego publicznie rejestru kontenerów**, ustaw następujące atrybuty [środowiska](/python/api/azureml-core/azureml.core.environment.environment) :

+ `docker.enabled=True`
+ `docker.base_image`: Ustaw rejestr i ścieżkę do obrazu.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Aby użyć obrazu z __prywatnego rejestru kontenerów__ , który nie znajduje się w obszarze roboczym, musisz użyć, `docker.base_image_registry` Aby określić adres repozytorium oraz nazwę użytkownika i hasło:

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

Musisz dodać wartość "Azure-Defaults" z wersją >= 1.0.45 jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web. Należy również ustawić właściwość inferencing_stack_version w środowisku na "Najnowsza". spowoduje to zainstalowanie określonych pakietów apt wymaganych przez usługę sieci Web. 

Po zdefiniowaniu środowiska należy użyć go z obiektem [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) , aby zdefiniować środowisko wnioskowania, w którym zostanie uruchomiony model i usługa sieci Web.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

W tym momencie można kontynuować wdrażanie. Na przykład poniższy fragment kodu spowoduje wdrożenie usługi sieci Web lokalnie przy użyciu konfiguracji wnioskowania i obrazu niestandardowego:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Aby uzyskać więcej informacji na temat wdrażania, zobacz [Wdrażanie modeli przy użyciu Azure Machine Learning](how-to-deploy-and-where.md).

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Korzystanie z obrazu za pomocą interfejsu wiersza polecenia Machine Learning

> [!IMPORTANT]
> Obecnie interfejs wiersza polecenia Machine Learning może używać obrazów z Azure Container Registry dla obszaru roboczego lub publicznie dostępnych repozytoriów. Nie można używać obrazów z autonomicznych rejestrów prywatnych.

Przed wdrożeniem modelu przy użyciu interfejsu wiersza polecenia Machine Learning Utwórz [środowisko](/python/api/azureml-core/azureml.core.environment.environment) , które używa obrazu niestandardowego. Następnie utwórz plik konfiguracji wnioskowania, który odwołuje się do środowiska. Możesz również zdefiniować środowisko bezpośrednio w pliku konfiguracyjnym wnioskowania. Poniższy dokument JSON pokazuje, jak odwoływać się do obrazu w rejestrze kontenera publicznego. W tym przykładzie środowisko zostało zdefiniowane w tekście:

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

Ten plik jest używany z `az ml model deploy` poleceniem. Ten `--ic` parametr służy do określania pliku konfiguracyjnego wnioskowania.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Aby uzyskać więcej informacji na temat wdrażania modelu przy użyciu interfejsu wiersza polecenia ML, zobacz sekcję "Rejestracja modelu, profilowanie i wdrażanie" [rozszerzenia interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment) artykułu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym [, gdzie wdrożyć i jak to zrobić](how-to-deploy-and-where.md).
* Dowiedz się [, jak uczenie i wdrażanie modeli uczenia maszynowego przy użyciu Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning).