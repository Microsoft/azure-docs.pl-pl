---
title: Rozwiązywanie problemów z wdrażaniem platformy Docker
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak obejść, rozwiązać i rozwiązać typowe błędy wdrażania platformy Docker za pomocą usługi Azure Kubernetes i Azure Container Instances przy użyciu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 82b9db2f3575e50367ed154246f9fb69b74c60cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333776"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Rozwiązywanie problemów z wdrażaniem modeli przez platformę Docker za pomocą usługi Azure Kubernetes Service i Azure Container Instances 

Dowiedz się, jak rozwiązywać problemy i rozwiązywać typowe błędy wdrażania platformy Docker za pomocą usług Azure Container Instances (ACI) i Azure Kubernetes Service (AKS) przy użyciu Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* [Zestaw SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Aby debugować lokalnie, musisz mieć działającą instalację platformy Docker w systemie lokalnym.

    Aby zweryfikować instalację platformy Docker, użyj polecenia `docker run hello-world` z terminalu lub wiersza polecenia. Informacje dotyczące instalowania platformy Docker lub rozwiązywania problemów z błędami platformy Docker znajdują się w [dokumentacji platformy Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Kroki wdrażania modeli uczenia maszynowego w programie Docker

Podczas wdrażania modelu w Azure Machine Learning należy użyć interfejsu API [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) i obiektu [środowiska](how-to-use-environments.md) . Usługa tworzy podstawowy obraz platformy Docker na etapie wdrażania i instaluje wymagane modele wszystkie w jednym wywołaniu. Podstawowe zadania wdrażania są następujące:

1. Zarejestruj model w rejestrze modelu obszaru roboczego.

2. Zdefiniuj konfigurację wnioskowania:
    1. Utwórz obiekt [środowiska](how-to-use-environments.md) . Ten obiekt może korzystać z zależności w pliku YAML środowiska, jednego z naszych środowisk nadzorowanych.
    2. Utwórz konfigurację wnioskowania (obiekt InferenceConfig) w oparciu o środowisko i skrypt oceniania.

3. Wdróż model w usłudze Azure Container Instance (ACI) lub w usłudze Azure Kubernetes Service (AKS).

Dowiedz się więcej o tym procesie w [Zarządzanie modelami](concept-model-management-and-deployment.md) wprowadzenie.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W przypadku wystąpienia dowolnego problemu najpierw należy podzielić zadanie wdrożenia (opisane wcześniej) na poszczególne kroki, aby wyizolować problem.

W przypadku używania [modelu model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) z obiektem [środowiska](how-to-use-environments.md) jako parametru wejściowego kod może być podzielony na trzy główne kroki:

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

Przerwanie procesu wdrażania Thee w poszczególnych zadaniach ułatwia identyfikowanie niektórych bardziej typowych błędów.

## <a name="debug-locally"></a>Debuguj lokalnie

Jeśli masz problemy podczas wdrażania modelu w programie ACI lub AKS, wdróż go jako lokalną usługę sieci Web. Korzystanie z lokalnej usługi internetowej ułatwia rozwiązywanie problemów.

Przykładowy [Notes lokalnego wdrożenia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) można znaleźć w repozytorium  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) , aby poznać możliwy do uruchomienia przykład.

> [!WARNING]
> Lokalne wdrożenia usługi sieci Web nie są obsługiwane w scenariuszach produkcyjnych.

Aby wdrożyć lokalnie, zmodyfikuj swój kod w celu użycia `LocalWebservice.deploy_configuration()` w celu utworzenia konfiguracji wdrożenia. Następnie użyj `Model.deploy()` do wdrożenia usługi. W poniższym przykładzie jest wdrażany model (zawarty w zmiennej modelu) jako lokalna usługa sieci Web:

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

Jeśli definiujesz własną specyfikację Conda YAML, Wyświetl listę wartości domyślnych dla programu Azure >= 1.0.45 jako zależność PIP. Ten pakiet jest wymagany do hostowania modelu jako usługi sieci Web.

W tym momencie można korzystać z usługi w zwykły sposób. Poniższy kod ilustruje wysyłanie danych do usługi:

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

Podczas testowania lokalnego może być konieczne zaktualizowanie `score.py` pliku w celu dodania rejestrowania lub podjęcia próby rozwiązania problemów, które zostały wykryte. Aby ponownie załadować zmiany do `score.py` pliku, użyj `reload()` . Na przykład poniższy kod ponownie ładuje skrypt dla usługi, a następnie wysyła do niej dane. Dane są oceniane przy użyciu zaktualizowanego `score.py` pliku:

> [!IMPORTANT]
> `reload`Metoda jest dostępna tylko dla wdrożeń lokalnych. Aby uzyskać informacje na temat aktualizowania wdrożenia do innego obiektu docelowego obliczeń, zobacz [jak zaktualizować usługę sieci Web](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skrypt zostanie ponownie załadowany z lokalizacji określonej przez `InferenceConfig` obiekt używany przez usługę.

Aby zmienić model, Conda zależności lub konfigurację wdrożenia, użyj [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueupdate--args-). Poniższy przykład aktualizuje model używany przez usługę:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Usuwanie usługi

Aby usunąć usługę, użyj polecenie [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Inspekcja dziennika platformy Docker

Można wydrukować szczegółowe komunikaty dziennika aparatu Docker z obiektu usługi. Dziennik można wyświetlić w przypadku wdrożeń ACI, AKS i lokalnych. Poniższy przykład ilustruje sposób drukowania dzienników.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Jeśli wiersz `Booting worker with pid: <pid>` występuje wielokrotnie w dziennikach, oznacza to, że nie ma wystarczającej ilości pamięci do uruchomienia procesu roboczego.
Możesz rozwiązać ten problem, zwiększając wartość `memory_gb` w `deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>Nie można zaplanować kontenera

W przypadku wdrażania usługi do elementu docelowego obliczeń usługi Azure Kubernetes Azure Machine Learning próbuje zaplanować usługę z żądaną ilością zasobów. Jeśli w klastrze nie ma dostępnych węzłów z odpowiednią ilością zasobów po 5 minutach, wdrożenie zakończy się niepowodzeniem. Komunikat o błędzie to `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Ten błąd można rozwiązać, dodając więcej węzłów, zmieniając jednostkę SKU węzłów lub zmieniając wymagania dotyczące zasobów usługi. 

Komunikat o błędzie będzie zazwyczaj wskazywał zasób, którego potrzebujesz więcej — na przykład jeśli zostanie wyświetlony komunikat o błędzie informujący, że `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` usługa wymaga procesora GPU, a w klastrze istnieją trzy węzły, które nie mają dostępnych procesorów GPU. Można to rozwiązać, dodając więcej węzłów, jeśli używasz jednostki SKU procesora GPU, przełączając się do jednostki SKU z włączoną obsługą procesora GPU, jeśli nie zmienisz środowiska, aby nie wymagały procesora GPU.  

## <a name="service-launch-fails"></a>Uruchamianie usługi nie powiodło się

Po pomyślnym skompilowaniu obrazu system podejmie próbę uruchomienia kontenera przy użyciu konfiguracji wdrożenia. W ramach procesu uruchamiania kontenera `init()` Funkcja w skrypcie oceniania jest wywoływana przez system. Jeśli w funkcji występują nieprzechwycone wyjątki `init()` , w komunikacie o błędzie może pojawić się błąd **CrashLoopBackOff** .

Skorzystaj z informacji w sekcji [sprawdzanie dziennika platformy Docker](#dockerlog) , aby sprawdzić dzienniki.

## <a name="function-fails-get_model_path"></a>Niepowodzenie funkcji: get_model_path ()

Często w `init()` funkcji w skrypcie oceniania funkcja [model.get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) jest wywoływana w celu zlokalizowania pliku modelu lub folderu plików modelu w kontenerze. Jeśli nie można znaleźć pliku lub folderu modelu, funkcja kończy się niepowodzeniem. Najprostszym sposobem debugowania tego błędu jest uruchomienie poniższego kodu w języku Python w ramach powłoki kontenera:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ten przykład drukuje ścieżkę lokalną (względem `/var/azureml-app` ) w kontenerze, w którym skrypt oceniania oczekuje na znalezienie pliku lub folderu modelu. Następnie można sprawdzić, czy plik lub folder jest naprawdę tam, gdzie jest oczekiwany.

Ustawienie poziomu rejestrowania na debugowanie może spowodować zarejestrowanie dodatkowych informacji, co może być przydatne podczas identyfikowania błędu.

## <a name="function-fails-runinput_data"></a>Niepowodzenie funkcji: Uruchom (input_data)

Jeśli usługa została pomyślnie wdrożona, ale ulega awarii, gdy dane są ogłaszane w punkcie końcowym oceniania, można dodać instrukcję zwracającą błąd w `run(input_data)` funkcji tak, aby zamiast tego zwracała szczegółowy komunikat o błędzie. Na przykład:

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

**Uwaga**: Zwracanie komunikatów o błędach z `run(input_data)` wywołania powinno odbywać się tylko do celów debugowania. Ze względów bezpieczeństwa nie należy zwracać komunikatów o błędach w ten sposób w środowisku produkcyjnym.

## <a name="http-status-code-502"></a>Kod stanu HTTP 502

Kod stanu 502 wskazuje, że usługa zgłosiła wyjątek lub uległa awarii w `run()` metodzie pliku Score.py. Aby debugować plik, Skorzystaj z informacji w tym artykule.

## <a name="http-status-code-503"></a>Kod stanu HTTP 503

Wdrożenia usługi Kubernetes platformy Azure obsługują Skalowanie automatyczne, co umożliwia dodawanie replik w celu obsługi dodatkowego obciążenia. Skalowanie automatyczne jest przeznaczone do obsługi **stopniowych** zmian obciążenia. Jeśli otrzymujesz duże liczby żądań na sekundę, klienci mogą otrzymać kod stanu HTTP 503. Mimo że automatyczne skalowanie reaguje szybko, zajmie dużo czasu, aby utworzyć dodatkowe kontenery.

Decyzje dotyczące skalowania w górę/w dół opierają się na wykorzystaniu bieżących replik kontenerów. Liczba replik, które są zajęte (przetwarzanie żądania) podzielona przez łączną liczbę bieżących replik jest bieżącym wykorzystaniem. Jeśli ta liczba przekroczy `autoscale_target_utilization` , zostanie utworzona więcej replik. Jeśli jest niższa, repliki są skracane. Decyzje dotyczące dodawania replik to eager i Fast (około 1 s). Decyzje dotyczące usuwania replik są ostrożne (około 1 minutę). Domyślnie skalowanie użycia obiektów docelowych jest ustawione na **70%**, co oznacza, że usługa może obsłużyć liczbę żądań na sekundę (RPS pliku) **do 30%**.

Istnieją dwie rzeczy, które mogą pomóc w zapobieganiu kodów stanu 503:

> [!TIP]
> Te dwa podejścia mogą być używane pojedynczo lub w połączeniu.

* Zmień poziom wykorzystania, przy którym automatyczne skalowanie tworzy nowe repliki. Cel wykorzystania można dostosować, ustawiając `autoscale_target_utilization` wartość na niższą.

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

Aby uzyskać więcej informacji na temat ustawiania `autoscale_target_utilization` , `autoscale_max_replicas` , i `autoscale_min_replicas` dla, zobacz odwołanie do modułu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true) .

## <a name="http-status-code-504"></a>Kod stanu HTTP 504

Kod stanu 504 wskazuje, że upłynął limit czasu żądania. Domyślny limit czasu wynosi 1 minutę.

Można zwiększyć limit czasu lub spróbować przyspieszyć usługę, modyfikując score.py w celu usunięcia niepotrzebnych wywołań. Jeśli te akcje nie rozrozwiązuje problemu, użyj informacji w tym artykule, aby debugować plik score.py. Kod może być w stanie innym niż odpowiada lub nieskończona pętla.

## <a name="advanced-debugging"></a>Zaawansowane debugowanie

Może być konieczne interaktywne Debugowanie kodu w języku Python zawartego we wdrożeniu modelu. Na przykład, jeśli skrypt wejścia kończy się niepowodzeniem i powód nie może być określony przez dodatkowe rejestrowanie. Za pomocą Visual Studio Code i debugpy można dołączyć do kodu działającego wewnątrz kontenera Docker.

Aby uzyskać więcej informacji, zobacz [interaktywny debugowanie w przewodniku vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Forum użytkownika dotyczące wdrażania modelu](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wdrażaniu:

* [Jak wdrożyć i gdzie](how-to-deploy-and-where.md)
* [Samouczek: uczenie & Wdrażanie modeli](tutorial-train-models-with-aml.md)
