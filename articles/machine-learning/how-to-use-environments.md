---
title: Tworzenie środowisk ml wielokrotnego użytkowania
titleSuffix: Azure Machine Learning
description: Tworzenie środowisk i zarządzanie nimi do szkolenia i wdrażania modelu. Zarządzanie pakietami języka Python i innymi ustawieniami środowiska.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: dc30318027962247f7504b734385d7642550ec87
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536356"
---
# <a name="reuse-environments-for-training-and-deployment-by-using-azure-machine-learning"></a>Ponowne używanie środowisk do szkoleń i wdrażania przy użyciu usługi Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak tworzyć [środowiska](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)usługi Azure Machine Learning i zarządzać nimi. Środowiska można używać do śledzenia i odtwarzania zależności oprogramowania projektów w miarę ich rozwoju.

Zarządzanie zależnościami oprogramowania jest typowym zadaniem dla deweloperów. Chcesz mieć pewność, że kompilacje są powtarzalne bez rozbudowanej konfiguracji oprogramowania ręcznego. Klasa usługi `Environment` Azure Machine Learning uwzględnia lokalne rozwiązania programistyczne, takie jak pip i Conda, i zapewnia rozwiązanie zarówno dla lokalnego, jak i rozproszonego rozwoju chmury.

Przykłady w tym artykule pokazują, jak:

* Tworzenie środowiska i określanie zależności pakietu.
* Pobieranie i aktualizowanie środowisk.
* Użyj środowiska do szkolenia.
* Użyj środowiska do wdrażania usługi sieci web.

Aby uzyskać ogólne omówienie sposobu działania środowisk w usłudze Azure Machine Learning, zobacz [Co to są środowiska ml?](concept-environments.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw [SDK usługi Azure Machine Learning dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>Tworzenie środowiska

W poniższych sekcjach przedstawiono wiele sposobów tworzenia środowiska eksperymentów.

### <a name="use-a-curated-environment"></a>Używanie wyselekcjonowanego środowiska

Można wybrać jedno z wyselekcjonowanych środowisk, aby rozpocząć od: 

* _Środowisko AzureML-Minimal_ zawiera minimalny zestaw pakietów, aby włączyć śledzenie uruchamiania i przekazywanie zasobów. Można go używać jako punktu wyjścia dla własnego środowiska.

* Środowisko _AzureML-Tutorial_ zawiera typowe pakiety nauki o danych. Pakiety te obejmują Scikit-Learn, Pandas, Matplotlib i większy zestaw pakietów azureml-sdk.

Wyselekcjonowane środowiska są wspierane przez buforowane obrazy platformy Docker. To wsparcie zmniejsza koszty przygotowania do biegu.

Użyj `Environment.get` metody, aby wybrać jedno z wyselekcjonowanych środowisk:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Można wyświetlić listę wyselekcjonowanych środowisk i ich pakietów przy użyciu następującego kodu:

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  Nie uruchamiaj własnej nazwy środowiska z prefiksem _AzureML._ Ten prefiks jest zarezerwowany dla wyselekcjonowanych środowisk.

### <a name="instantiate-an-environment-object"></a>Tworzenie wystąpienia obiektu środowiska

Aby ręcznie utworzyć środowisko, `Environment` zaimportuj klasę z SDK. Następnie użyj następującego kodu, aby utworzyć wystąpienie obiektu środowiska.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-conda-and-pip-specification-files"></a>Użyj plików specyfikacji Conda i pip

Można również utworzyć środowisko ze specyfikacji Conda lub pliku wymagań pip. Użyj [`from_conda_specification()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-conda-specification-name--file-path-) metody lub [`from_pip_requirements()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-pip-requirements-name--file-path-) metody. W argumicie metody uwzględnij nazwę środowiska i ścieżkę pliku, który ma zostać uwzględnionych.

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv"
                                          file_path = "path-to-pip-requirements-file")
```

### <a name="use-existing-conda-environments"></a>Korzystanie z istniejących środowisk Conda

Jeśli masz istniejące środowisko Conda na komputerze lokalnym, możesz użyć usługi do utworzenia obiektu środowiska. Korzystając z tej strategii, można ponownie użyć lokalnego środowiska interaktywnego w zdalnych działach.

Poniższy kod tworzy obiekt środowiska z `mycondaenv`istniejącego środowiska Conda . Używa tej [`from_existing_conda_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#from-existing-conda-environment-name--conda-environment-name-) metody.

``` python
myenv = Environment.from_existing_conda_environment(name = "myenv",
                                                    conda_environment_name = "mycondaenv")
```

### <a name="create-environments-automatically"></a>Automatyczne tworzenie środowisk

Automatyczne tworzenie środowiska przez przesłanie przebiegu szkolenia. Prześlij uruchomić `submit()` przy użyciu metody. Po przesłaniu przebiegu szkoleniowego budowa nowego środowiska może potrwać kilka minut. Czas trwania kompilacji zależy od rozmiaru wymaganych zależności. 

Jeśli nie określisz środowiska w konfiguracji uruchamiania przed przesłaniem uruchomienia, zostanie utworzone środowisko domyślne.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
runconfig = ScriptRunConfig(source_directory=".", script="example.py")
runconfig.run_config.target = "local"

# Submit the run
run = myexp.submit(config=runconfig)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

Podobnie, jeśli używasz [`Estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) obiektu do szkolenia, można bezpośrednio przesłać wystąpienie estymatora jako uruchomienie bez określania środowiska. Obiekt `Estimator` już hermetyzuje środowisko i cel obliczeniowy.

## <a name="add-packages-to-an-environment"></a>Dodawanie pakietów do środowiska

Dodawanie pakietów do środowiska przy użyciu plików kół Conda, pip lub prywatnych. Określ każdą zależność [`CondaDependency`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) pakietu przy użyciu klasy. Dodaj go do środowiska `PythonSection`.

### <a name="conda-and-pip-packages"></a>Pakiety Conda i pip

Jeśli pakiet jest dostępny w repozytorium pakietów Conda, zaleca się użycie instalacji Conda, a nie instalacji pip. Pakiety Conda zazwyczaj są wyposażone w wstępnie wbudowane pliki binarne, które sprawiają, że instalacja jest bardziej niezawodna.

Poniższy przykład dodaje do środowiska. Dodaje wersję 1.17.0 `numpy`z . Dodaje również `pillow` pakiet, `myenv`. W przykładzie [`add_conda_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-conda-package-conda-package-) użyto [`add_pip_package()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py#add-pip-package-pip-package-) metody i metody, odpowiednio.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> Jeśli używasz tej samej definicji środowiska dla innego uruchomienia, usługa Azure Machine Learning ponownie używa buforowanego obrazu środowiska. Jeśli utworzysz środowisko z nieprzypiętą zależnością pakietu, na przykład, ```numpy```to środowisko będzie nadal używać wersji pakietu _zainstalowanej w momencie tworzenia środowiska_. Ponadto każde przyszłe środowisko z dopasowującą definicją będzie nadal używać starej wersji. Aby uzyskać więcej informacji, zobacz [Tworzenie środowiska, buforowanie i ponowne użycie](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse).

### <a name="private-wheel-files"></a>Prywatne pliki kół

Możesz użyć prywatnych plików kół pip, najpierw przesyłając je do magazynu obszaru roboczego. Przesyłasz je przy użyciu [`add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) metody statycznej. Następnie przechwycić adres URL `add_pip_package()` magazynu i przekazać adres URL do metody.

```python
# During environment creation the service replaces the URL by secure SAS URL, so your wheel file is kept private and secure
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

## <a name="manage-environments"></a>Zarządzanie środowiskami

Zarządzaj środowiskami, aby aktualizować je, śledzić i używać ponownie w różnych celach obliczeniowych i u innych użytkowników obszaru roboczego.

### <a name="register-environments"></a>Zarejestruj środowiska

Środowisko jest automatycznie rejestrowane w obszarze roboczym podczas przesyłania uruchomienia lub wdrażania usługi sieci web. Można również ręcznie zarejestrować środowisko [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#register-workspace-) przy użyciu metody. Ta operacja sprawia, że środowisko w jednostki, która jest śledzona i wersjona w chmurze. Encja może być współużytkowana przez użytkowników obszaru roboczego.

Poniższy kod rejestruje `myenv` środowisko `ws` do obszaru roboczego.

```python
myenv.register(workspace=ws)
```

Podczas korzystania ze środowiska po raz pierwszy w szkolenia lub wdrożenia, jest zarejestrowany w obszarze roboczym. Następnie jest on alokowany i wdrażany na docelowej mocy obliczeniowej. Usługa buforuje środowiska. Ponowne użycie środowiska buforowanego zajmuje znacznie mniej czasu niż przy użyciu nowej usługi lub usługi, która została zaktualizowana.

### <a name="get-existing-environments"></a>Uzyskaj istniejące środowiska

Klasa `Environment` oferuje metody, które umożliwiają pobieranie istniejących środowisk w obszarze roboczym. Środowiska można pobierać według nazwy, jako listy lub przez określony przebieg szkolenia. Te informacje są przydatne do rozwiązywania problemów, inspekcji i odtwarzalności.

#### <a name="view-a-list-of-environments"></a>Wyświetlanie listy środowisk

Wyświetlanie środowisk w obszarze roboczym [`Environment.list(workspace="workspace_name")`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#list-workspace-) przy użyciu klasy. Następnie wybierz środowisko do ponownego użycia.

#### <a name="get-an-environment-by-name"></a>Uzyskaj środowisko według nazwy

Można również uzyskać określone środowisko według nazwy i wersji. Poniższy kod używa [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#get-workspace--name--version-none-) metody do `1` pobierania `myenv` wersji środowiska `ws` w obszarze roboczym.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>Szkolenie środowiska specyficznego dla przebiegu

Aby uzyskać środowisko, które zostało użyte dla określonego uruchomienia [`get_environment()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-environment--) po `Run` zakończeniu szkolenia, należy użyć metody w klasie.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>Aktualizowanie istniejącego środowiska

Załóżmy, że zmieniasz istniejące środowisko, na przykład przez dodanie pakietu Pythona. Nowa wersja środowiska jest następnie tworzona podczas przesyłania uruchomienia, wdrażania modelu lub ręcznego rejestrowania środowiska. Przechowywanie wersji umożliwia wyświetlanie zmian w środowisku w czasie.

Aby zaktualizować wersję pakietu Języka Python w istniejącym środowisku, określ numer wersji dla tego pakietu. Jeśli nie używasz dokładnego numeru wersji, usługa Azure Machine Learning ponownie użyje istniejącego środowiska z oryginalnymi wersjami pakietu.

### <a name="debug-the-image-build"></a>Debugowanie kompilacji obrazu

W poniższym przykładzie [`build()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py#build-workspace--image-build-compute-none-) użyto metody do ręcznego tworzenia środowiska jako obrazu platformy Docker. Monitoruje dzienniki wyjściowe z kompilacji [`wait_for_completion()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-)obrazu za pomocą . Wbudowany obraz pojawia się w wystąpieniu rejestru kontenerów azure obszaru roboczego. Te informacje są przydatne do debugowania.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

## <a name="enable-docker"></a>Włącz dokowanie

 Klasa [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) usługi Azure `Environment` Machine Learning umożliwia precyzyjne dostosowanie i sterowanie systemem operacyjnym gościa, na którym można uruchomić szkolenie.

Po włączeniu platformy Docker usługa tworzy obraz platformy Docker. Tworzy również środowisko Języka Python, który używa specyfikacji w tym kontenerze platformy Docker. Ta funkcja zapewnia dodatkową izolację i odtwarzalność dla przebiegów szkoleniowych.

```python
# Creates the environment inside a Docker container.
myenv.docker.enabled = True
```

Domyślnie nowo utworzony obraz platformy Docker jest wyświetlany w rejestrze kontenerów skojarzonym z obszarem roboczym.  Nazwa repozytorium ma formularz *azureml/azureml_\<uuid\>*. Unikatowy identyfikator *(uuid)* część nazwy odpowiada skrót, który jest obliczany z konfiguracji środowiska. Ta korespondencja umożliwia usłudze ustalenie, czy obraz dla danego środowiska już istnieje do ponownego użycia.

Dodatkowo usługa automatycznie korzysta z jednego z [obrazów podstawowych](https://github.com/Azure/AzureML-Containers)opartych na systemie Ubuntu Linux. Instaluje określone pakiety Pythona. Obraz podstawowy ma wersje procesora i gpu. Usługa Azure Machine Learning automatycznie wykrywa, której wersji użyć.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

Można również określić niestandardowy plik dockerfile. Najprościej jest rozpocząć od jednego z obrazów ```FROM``` podstawowych usługi Azure Machine Learning przy użyciu polecenia platformy Docker, a następnie dodać własne kroki niestandardowe. Użyj tej metody, jeśli chcesz zainstalować pakiety innych niż Python jako zależności.

```python
# Specify docker steps as a string. Alternatively, load the string from a file.
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
```

### <a name="use-user-managed-dependencies"></a>Korzystanie z zależności zarządzanych przez użytkownika

W niektórych sytuacjach niestandardowy obraz podstawowy może już zawierać środowisko Języka Python z pakietami, których chcesz użyć.

Domyślnie usługa Azure Machine Learning będzie tworzyć środowisko Conda z określonymi zależnościami i wykona uruchamianie w tym środowisku zamiast używać bibliotek języka Python zainstalowanych na obrazie podstawowym. 

Aby użyć własnych zainstalowanych pakietów, należy ustawić parametr `Environment.python.user_managed_dependencies = True`. Upewnij się, że obraz podstawowy zawiera interpreter języka Python i ma pakiety, których potrzebuje skrypt szkoleniowy.

Na przykład, aby uruchomić w podstawowym środowisku Miniconda, w które zainstalowany jest pakiet NumPy, należy najpierw określić plik Dockerfile z krokiem do zainstalowania pakietu. Następnie ustaw zależności zarządzane przez `True`użytkownika na . 

Można również określić ścieżkę do określonego interpretera języka `Environment.python.interpreter_path` Python w obrazie, ustawiając zmienną.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

## <a name="use-environments-for-training"></a>Używanie środowisk do szkolenia

Aby przesłać przebieg szkolenia, musisz połączyć środowisko, [obiekt docelowy obliczeń](concept-compute-target.md)i skrypt szkolenia Pythona w konfiguracji uruchamiania. Ta konfiguracja jest obiektem otoki, który jest używany do przesyłania przebiegów.

Po przesłaniu przebiegu szkoleniowego budowa nowego środowiska może potrwać kilka minut. Czas trwania zależy od rozmiaru wymaganych zależności. Środowiska są buforowane przez usługę. Tak długo, jak definicja środowiska pozostaje niezmieniona, należy ponieść pełny czas konfiguracji tylko raz.

Poniższy przykład uruchamiania skryptu [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) lokalnego pokazuje, gdzie można użyć jako obiektu otoki.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Add training script to run config
runconfig = ScriptRunConfig(source_directory=".", script="train.py")

# Attach compute target to run config
runconfig.run_config.target = "local"

# Attach environment to run config
runconfig.run_config.environment = myenv

# Submit run 
run = exp.submit(runconfig)
```

> [!NOTE]
> Aby wyłączyć historię uruchamiania lub uruchomić migawki, użyj ustawienia w obszarze `ScriptRunConfig.run_config.history`.

Jeśli nie określisz środowiska w konfiguracji uruchamiania, usługa tworzy środowisko domyślne podczas przesyłania uruchomienia.

### <a name="use-an-estimator-for-training"></a>Użyj estymatora do treningu

Jeśli używasz [estymatora](how-to-train-ml-models.md) do szkolenia, możesz przesłać wystąpienie estymatora bezpośrednio. Już hermetyzuje środowisko i cel obliczeniowy.

Poniższy kod używa estymatora dla przebiegu szkolenia z jednym węzłem. Działa na zdalnym obliczeń `scikit-learn` dla modelu. Przyjęto założenie, że wcześniej utworzono `compute_target`obiekt docelowy obliczeń `ds`i obiekt magazynu danych.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])

# Submit the run 
run = experiment.submit(sk_est)
```

## <a name="use-environments-for-web-service-deployment"></a>Używanie środowisk do wdrażania usług sieci web

Środowiska można używać podczas wdrażania modelu jako usługi sieci web. Ta funkcja umożliwia odtwarzalny, połączony przepływ pracy. W tym przepływie pracy można szkolić, testować i wdrażać model przy użyciu tych samych bibliotek w obliczeniach szkoleniowych i wnioskowania.

Aby wdrożyć usługę sieci web, połącz środowisko, wnioskować obliczenia, oceniać [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)skrypt i zarejestrowany model w obiekcie wdrażania, . Aby uzyskać więcej informacji, zobacz [Jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).

W tym przykładzie załóżmy, że ukończyłeś przebieg szkolenia. Teraz chcesz wdrożyć ten model do wystąpienia kontenera platformy Azure. Podczas tworzenia usługi sieci web, model i oceniania plików są zainstalowane na obrazie i stos wnioskowania usługi Azure Machine Learning jest dodawany do obrazu.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="example-notebooks"></a>Przykładowe notesy

W tym [przykładzie notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments) rozszerza pojęcia i metody przedstawione w tym artykule.

[Wdrażanie modelu przy użyciu niestandardowego obrazu podstawowego platformy Docker](how-to-deploy-custom-docker-image.md) pokazuje, jak wdrożyć model przy użyciu niestandardowego obrazu podstawowego platformy Docker.

W tym [przykładzie notesu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark) pokazano, jak wdrożyć model platformy Spark jako usługę sieci web.

## <a name="create-and-manage-environments-with-the-cli"></a>Tworzenie środowisk i zarządzanie nimi za pomocą interfejsu wiersza polecenia

Interfejs [wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md) odzwierciedla większość funkcji zestawu SDK języka Python. Można go używać do tworzenia środowisk i zarządzania nimi. Polecenia omówione w tej sekcji zademonstrują podstawowe funkcje.

Następujące polecenie skondysktorów plików dla domyślnej definicji środowiska w określonym katalogu. Pliki te są pliki JSON. Działają one jak odpowiedniej klasy w SDK. Za pomocą plików można tworzyć nowe środowiska, które mają ustawienia niestandardowe. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

Uruchom następujące polecenie, aby zarejestrować środowisko z określonego katalogu.

```azurecli-interactive
az ml environment register -d myenvdir
```

Uruchom następujące polecenie, aby wyświetlić listę wszystkich zarejestrowanych środowisk.

```azurecli-interactive
az ml environment list
```

Pobierz zarejestrowane środowisko za pomocą następującego polecenia.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="next-steps"></a>Następne kroki

* Aby użyć zarządzanego celu obliczeniowego do szkolenia modelu, zobacz [Samouczek: Trenuj model](tutorial-train-models-with-aml.md).
* Po zapoznaniu się z przeszkolonym modelem dowiedz się, [jak i gdzie wdrożyć modele](how-to-deploy-and-where.md).
* Wyświetl [ `Environment` odwołanie do sdk klasy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Aby uzyskać więcej informacji na temat pojęć i metod opisanych w tym artykule, zobacz [przykładowy notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/using-environments).
