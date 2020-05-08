---
title: Przewodnik rozwiązywania problemów z wdrażaniem
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak obejść, rozwiązać i rozwiązać typowe błędy wdrażania platformy Docker za pomocą usługi Azure Kubernetes i Azure Container Instances przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 01fa9c111371c3ede5d3be33f4066f325bad4680
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929251"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Rozwiązywanie problemów Azure Machine Learning usługi Azure Kubernetes i wdrożenia Azure Container Instances

Dowiedz się, jak obejść typowe błędy wdrażania platformy Docker i rozwiązać je za pomocą usług Azure Container Instances (ACI) i Azure Kubernetes Service (AKS) przy użyciu Azure Machine Learning.

W przypadku wdrażania modelu w Azure Machine Learning system wykonuje wiele zadań.

Zalecane i najbardziej aktualne podejście do wdrażania modelu polega za pośrednictwem interfejsu API [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) przy użyciu obiektu [środowiska](how-to-use-environments.md) jako parametru wejściowego. W takim przypadku Nasza usługa utworzy podstawowy obraz platformy Docker na etapie wdrażania i zainstaluje wymagane modele wszystkie w jednym wywołaniu. Podstawowe zadania wdrażania są następujące:

1. Zarejestruj model w rejestrze modelu obszaru roboczego.

2. Zdefiniuj konfigurację wnioskowania:
    1. Utwórz obiekt [środowiska](how-to-use-environments.md) na podstawie zależności określonych w pliku YAML środowiska lub użyj jednego z naszych środowisk.
    2. Utwórz konfigurację wnioskowania (obiekt InferenceConfig) w oparciu o środowisko i skrypt oceniania.

3. Wdróż model w usłudze Azure Container Instance (ACI) lub w usłudze Azure Kubernetes Service (AKS).

Dowiedz się więcej o tym procesie w [Zarządzanie modelami](concept-model-management-and-deployment.md) wprowadzenie.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Aby debugować lokalnie, musisz mieć działającą instalację platformy Docker w systemie lokalnym.

    Aby zweryfikować instalację platformy Docker, użyj polecenia `docker run hello-world` z terminalu lub wiersza polecenia. Informacje dotyczące instalowania platformy Docker lub rozwiązywania problemów z błędami platformy Docker znajdują się w [dokumentacji platformy Docker](https://docs.docker.com/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W przypadku wystąpienia dowolnego problemu najpierw należy podzielić zadanie wdrożenia (opisane wcześniej) na poszczególne kroki, aby wyizolować problem.

Przy założeniu, że używasz nowej/zalecanej metody wdrażania za pośrednictwem interfejsu API [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) z obiektem [środowiska](how-to-use-environments.md) jako parametrem wejściowym, kod może być podzielony na trzy główne kroki:

1. Zarejestrowanie modelu. Oto przykładowy kod:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Zdefiniuj konfigurację wnioskowania dla wdrożenia:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Wdróż model przy użyciu konfiguracji wnioskowania utworzonej w poprzednim kroku:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Po przeprowadzeniu procesu wdrażania do poszczególnych zadań można wyszukać najbardziej typowe błędy.

## <a name="debug-locally"></a>Debuguj lokalnie

Jeśli wystąpią problemy ze wdrożeniem modelu do ACI lub AKS, spróbuj wdrożyć go jako lokalną usługę sieci Web. Korzystanie z lokalnej usługi sieci Web ułatwia rozwiązywanie problemów. Obraz platformy Docker zawierający model zostanie pobrany i uruchomiony w systemie lokalnym.

> [!WARNING]
> Lokalne wdrożenia usługi sieci Web nie są obsługiwane w scenariuszach produkcyjnych.

Aby wdrożyć lokalnie, zmodyfikuj swój kod w celu `LocalWebservice.deploy_configuration()` użycia w celu utworzenia konfiguracji wdrożenia. Następnie użyj `Model.deploy()` do wdrożenia usługi. W poniższym przykładzie jest wdrażany model (zawarty w zmiennej modelu) jako lokalna usługa sieci Web:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Należy pamiętać, że jeśli definiujesz własną Conda specyfikację YAML, musisz wyświetlić listę opcji "Azure-Defaults" z wersją >= 1.0.45 jako zależność PIP. Ten pakiet zawiera funkcje, które są konieczne do hostowania modelu jako usługi sieci Web.

W tym momencie można korzystać z usługi w zwykły sposób. Na przykład poniższy kod ilustruje wysyłanie danych do usługi:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Aby uzyskać więcej informacji na temat dostosowywania środowiska języka Python, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md). 

### <a name="update-the-service"></a>Aktualizowanie usługi

Podczas testowania lokalnego może być konieczne zaktualizowanie `score.py` pliku w celu dodania rejestrowania lub podjęcia próby rozwiązania problemów, które zostały wykryte. Aby ponownie załadować zmiany do `score.py` pliku, użyj `reload()`. Na przykład poniższy kod ponownie ładuje skrypt dla usługi, a następnie wysyła do niej dane. Dane są oceniane przy użyciu zaktualizowanego `score.py` pliku:

> [!IMPORTANT]
> `reload` Metoda jest dostępna tylko dla wdrożeń lokalnych. Aby uzyskać informacje na temat aktualizowania wdrożenia do innego obiektu docelowego obliczeń, zobacz sekcję Update ( [Wdrażanie modeli](how-to-deploy-and-where.md#update)).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skrypt zostanie ponownie załadowany z lokalizacji określonej przez `InferenceConfig` obiekt używany przez usługę.

Aby zmienić model, Conda zależności lub konfigurację wdrożenia, użyj [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Poniższy przykład aktualizuje model używany przez usługę:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Usuwanie usługi

Aby usunąć usługę, użyj polecenie [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>Inspekcja dziennika platformy Docker

Można wydrukować szczegółowe komunikaty dziennika aparatu Docker z obiektu usługi. Dziennik można wyświetlić w przypadku wdrożeń ACI, AKS i lokalnych. Poniższy przykład ilustruje sposób drukowania dzienników.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Uruchamianie usługi nie powiodło się

Po pomyślnym skompilowaniu obrazu system podejmie próbę uruchomienia kontenera przy użyciu konfiguracji wdrożenia. W ramach procesu uruchamiania kontenera `init()` funkcja w skrypcie oceniania jest wywoływana przez system. Jeśli w `init()` funkcji występują nieprzechwycone wyjątki, w komunikacie o błędzie może pojawić się błąd **CrashLoopBackOff** .

Skorzystaj z informacji w sekcji [sprawdzanie dziennika platformy Docker](#dockerlog) , aby sprawdzić dzienniki.

## <a name="function-fails-get_model_path"></a>Niepowodzenie funkcji: get_model_path ()

Często w `init()` funkcji w skrypcie oceniania funkcja [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) jest wywoływana w celu zlokalizowania pliku modelu lub folderu plików modelu w kontenerze. Jeśli nie można znaleźć pliku lub folderu modelu, funkcja kończy się niepowodzeniem. Najprostszym sposobem debugowania tego błędu jest uruchomienie poniższego kodu w języku Python w ramach powłoki kontenera:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ten przykład drukuje ścieżkę lokalną (względem `/var/azureml-app`) w kontenerze, w którym skrypt oceniania oczekuje na znalezienie pliku lub folderu modelu. Następnie można sprawdzić, czy plik lub folder jest naprawdę tam, gdzie jest oczekiwany.

Ustawienie poziomu rejestrowania na debugowanie może spowodować zarejestrowanie dodatkowych informacji, co może być przydatne podczas identyfikowania błędu.

## <a name="function-fails-runinput_data"></a>Niepowodzenie funkcji: Uruchom (input_data)

Jeśli usługa została pomyślnie wdrożona, ale ulega awarii, gdy dane są ogłaszane w punkcie końcowym oceniania, można dodać instrukcję zwracającą `run(input_data)` błąd w funkcji tak, aby zamiast tego zwracała szczegółowy komunikat o błędzie. Przykład:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Uwaga**: Zwracanie komunikatów o błędach `run(input_data)` z wywołania powinno odbywać się tylko do celów debugowania. Ze względów bezpieczeństwa nie należy zwracać komunikatów o błędach w ten sposób w środowisku produkcyjnym.

## <a name="http-status-code-502"></a>Kod stanu HTTP 502

Kod stanu 502 wskazuje, że usługa zgłosiła wyjątek lub uległa awarii w `run()` metodzie pliku Score.py. Aby debugować plik, Skorzystaj z informacji w tym artykule.

## <a name="http-status-code-503"></a>Kod stanu HTTP 503

Wdrożenia usługi Kubernetes platformy Azure obsługują Skalowanie automatyczne, co umożliwia dodawanie replik w celu obsługi dodatkowego obciążenia. Jednak Autoskalowanie jest przeznaczone do obsługi **stopniowanych** zmian obciążenia. Jeśli otrzymujesz duże liczby żądań na sekundę, klienci mogą otrzymać kod stanu HTTP 503.

Istnieją dwie rzeczy, które mogą pomóc w zapobieganiu kodów stanu 503:

* Zmień poziom wykorzystania, przy którym automatyczne skalowanie tworzy nowe repliki.
    
    Domyślnie skalowanie użycia obiektów docelowych jest ustawione na 70%, co oznacza, że usługa może obsłużyć liczbę żądań na sekundę (RPS pliku) do 30%. Cel wykorzystania można dostosować, ustawiając wartość `autoscale_target_utilization` na niższą.

    > [!IMPORTANT]
    > Ta zmiana nie powoduje *szybszego*tworzenia replik. Zamiast tego są tworzone przy niższym progu wykorzystania. Zamiast czekać, aż usługa zostanie wykorzystana przez 70%, zmiana wartości na 30% powoduje utworzenie replik w przypadku wystąpienia 30%.
    
    Jeśli usługa sieci Web już korzysta z bieżącej maksymalnej liczby replik i nadal widzisz 503 kodów stanu, zwiększ `autoscale_max_replicas` wartość, aby zwiększyć maksymalną liczbę replik.

* Zmień minimalną liczbę replik. Zwiększenie minimalnych replik zapewnia większą pulę do obsługi przychodzących skoków.

    Aby zwiększyć minimalną liczbę replik, ustaw `autoscale_min_replicas` wyższą wartość. Wymagane repliki można obliczyć przy użyciu następującego kodu, zastępując wartości wartościami charakterystycznymi dla projektu:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Jeśli odbierane są żądania większe niż w przypadku nowych replik minimalnych, może dojść do 503s. Na przykład w miarę wzrostu ruchu do usługi może być konieczne zwiększenie minimalnej liczby replik.

Aby uzyskać więcej informacji na `autoscale_target_utilization`temat `autoscale_max_replicas`ustawiania, `autoscale_min_replicas` , i dla, zobacz odwołanie do modułu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) .

## <a name="http-status-code-504"></a>Kod stanu HTTP 504

Kod stanu 504 wskazuje, że upłynął limit czasu żądania. Domyślny limit czasu wynosi 1 minutę.

Można zwiększyć limit czasu lub spróbować przyspieszyć usługę, modyfikując score.py w celu usunięcia niepotrzebnych wywołań. Jeśli te akcje nie rozrozwiązuje problemu, użyj informacji w tym artykule, aby debugować plik score.py. Kod może być w stanie zawieszonym lub nieskończoną pętlą.

## <a name="advanced-debugging"></a>Zaawansowane debugowanie

W niektórych przypadkach może być konieczne interaktywne Debugowanie kodu w języku Python zawartego we wdrożeniu modelu. Na przykład, jeśli skrypt wejścia kończy się niepowodzeniem i powód nie może być określony przez dodatkowe rejestrowanie. Używając Visual Studio Code i Python Tools for Visual Studio (PTVSD), możesz dołączyć do kodu działającego wewnątrz kontenera Docker.

> [!IMPORTANT]
> Ta metoda debugowania nie działa w przypadku lokalnego wdrażania `Model.deploy()` modelu `LocalWebservice.deploy_configuration` i. Zamiast tego należy utworzyć obraz przy użyciu metody [model. Package ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) .

Lokalne wdrożenia usługi sieci Web wymagają pracy instalacji platformy Docker w systemie lokalnym. Aby uzyskać więcej informacji na temat korzystania z platformy Docker, zapoznaj się z [dokumentacją platformy Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Konfigurowanie środowiska programowania

1. Aby zainstalować Python Tools for Visual Studio (PTVSD) w lokalnym VS Code środowisku programistycznym, użyj następującego polecenia:

    ```
    python -m pip install --upgrade ptvsd
    ```

    Aby uzyskać więcej informacji na temat używania PTVSD z VS Code, zobacz [debugowanie zdalne](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Aby skonfigurować VS Code do komunikowania się z obrazem platformy Docker, Utwórz nową konfigurację debugowania:

    1. W obszarze VS Code wybierz menu __Debuguj__ , a następnie wybierz pozycję __Otwórz konfiguracje__. Zostanie otwarty plik o nazwie __Launch. JSON__ .

    1. W pliku __Launch. JSON__ Znajdź wiersz, który zawiera `"configurations": [`, i Wstaw następujący tekst po nim:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Jeśli w sekcji konfiguracje znajdują się już inne wpisy, Dodaj przecinek (,) po wstawionym kodzie.

        Ta sekcja dołącza do kontenera Docker przy użyciu portu 5678.

    1. Zapisz plik __Launch. JSON__ .

### <a name="create-an-image-that-includes-ptvsd"></a>Tworzenie obrazu zawierającego PTVSD

1. Zmodyfikuj środowisko Conda dla danego wdrożenia, aby zawierało PTVSD. Poniższy przykład demonstruje dodanie go przy użyciu `pip_packages` parametru:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Aby rozpocząć PTVSD i poczekać na połączenie, gdy usługa zostanie uruchomiona, Dodaj następujący element na początku `score.py` pliku:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Tworzenie obrazu na podstawie definicji środowiska i ściąganie obrazu do rejestru lokalnego. Podczas debugowania, możesz chcieć wprowadzić zmiany w plikach w obrazie bez konieczności ponownego tworzenia go. Aby zainstalować Edytor tekstu (vim) w obrazie platformy Docker, użyj właściwości `Environment.docker.base_image` i `Environment.docker.base_dockerfile` :

    > [!NOTE]
    > W tym przykładzie przyjęto założenie, że `ws` wskazuje obszar roboczy `model` Azure Machine Learning i jest to wdrażany model. `myenv.yml` Plik zawiera zależności Conda utworzone w kroku 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Po utworzeniu i pobraniu obrazu ścieżka obrazu (łącznie z repozytorium, nazwą i tagiem, która w tym przypadku jest również skrótem) zostanie wyświetlona w komunikacie podobnym do poniższego:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Aby ułatwić pracę z obrazem, użyj następującego polecenia, aby dodać tag. Zamień `myimagepath` na wartość lokalizacji z poprzedniego kroku.

    ```bash
    docker tag myimagepath debug:1
    ```

    W pozostałej części kroków można odwołać się do lokalnego obrazu jako `debug:1` zamiast pełnej wartości ścieżki obrazu.

### <a name="debug-the-service"></a>Debugowanie usługi

> [!TIP]
> Jeśli ustawisz limit czasu dla połączenia usługi PTVSD w `score.py` pliku, musisz połączyć vs Code z sesją debugowania przed upływem limitu czasu. Rozpocznij VS Code, Otwórz lokalną kopię `score.py`, ustaw punkt przerwania i przygotuj się do użycia przed wykonaniem kroków opisanych w tej sekcji.
>
> Aby uzyskać więcej informacji na temat debugowania i ustawiania punktów przerwania, zobacz [debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

1. Aby uruchomić kontener platformy Docker przy użyciu obrazu, użyj następującego polecenia:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Aby dołączyć VS Code do PTVSD wewnątrz kontenera, Otwórz VS Code i naciśnij klawisz F5 lub wybierz opcję __Debuguj__. Po wyświetleniu monitu wybierz konfigurację __Azure Machine Learning: Docker Debug__ . Możesz również wybrać ikonę debugowania z paska bocznego, __Azure Machine Learning:__ z menu rozwijanego debugowanie, a następnie użyć zielonej strzałki do dołączenia debugera.

    ![Ikona debugowania, przycisk Rozpocznij debugowanie i selektor konfiguracji](./media/how-to-troubleshoot-deployment/start-debugging.png)

W tym momencie VS Code nawiązuje połączenie z usługą PTVSD wewnątrz kontenera Docker i zostanie zatrzymane na ustawionym wcześniej punkcie przerwania. Teraz można przechodzić przez kod w trakcie jego uruchamiania, wyświetlać zmienne itp.

Aby uzyskać więcej informacji na temat używania VS Code do debugowania języka Python, zobacz [Debugowanie kodu](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)w języku Python.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modyfikowanie plików kontenera

Aby wprowadzić zmiany w plikach w obrazie, można dołączyć do uruchomionego kontenera i wykonać powłokę bash. Z tego miejsca możesz użyć usługi VIM do edycji plików:

1. Aby nawiązać połączenie z uruchomionym kontenerem i uruchomić powłokę bash w kontenerze, użyj następującego polecenia:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Aby znaleźć pliki używane przez usługę, użyj następującego polecenia z powłoki bash w kontenerze, jeśli domyślny katalog jest inny niż `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    W tym miejscu możesz użyć usługi VIM, aby edytować `score.py` plik. Aby uzyskać więcej informacji na temat korzystania z usługi VIM, zobacz [Korzystanie z edytora vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Zmiany w kontenerze nie są zwykle utrwalane. Aby zapisać wszelkie wprowadzone zmiany, użyj poniższego polecenia, przed zamknięciem powłoki uruchomionej w powyższym kroku (czyli w innej powłoce):

    ```bash
    docker commit debug debug:2
    ```

    To polecenie tworzy nowy obraz o nazwie `debug:2` , który zawiera zmiany.

    > [!TIP]
    > Aby zmiany zaczęły obowiązywać, należy zatrzymać bieżący kontener i zacząć korzystać z nowej wersji.

1. Upewnij się, że zmiany wprowadzone w plikach w kontenerze są zsynchronizowane z lokalnymi plikami, których VS Code używa. W przeciwnym razie środowisko debugera nie będzie działało zgodnie z oczekiwaniami.

### <a name="stop-the-container"></a>Zatrzymywanie kontenera

Aby zatrzymać kontener, użyj następującego polecenia:

```bash
docker stop debug
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wdrażaniu:

* [Jak wdrożyć i gdzie](how-to-deploy-and-where.md)
* [Samouczek: uczenie & Wdrażanie modeli](tutorial-train-models-with-aml.md)
