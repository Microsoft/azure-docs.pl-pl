---
title: Rozwiązywanie problemów z zdalnym wdrożeniem usługi sieci Web
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak obejść, rozwiązać i rozwiązać typowe błędy wdrażania platformy Docker za pomocą usługi Azure Kubernetes i Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy, contperfq2
ms.openlocfilehash: 0b8da0be16adc79b606b59f394b223b001453607
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185066"
---
# <a name="troubleshoot-model-deployment"></a>Rozwiązywanie problemów z wdrażaniem modelu

Dowiedz się, jak rozwiązywać problemy i rozwiązywać typowe błędy wdrażania platformy Docker przy użyciu usług Azure Container Instances (ACI) i Azure Kubernetes Service (AKS) za pomocą Azure Machine Learning.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).
* [Zestaw SDK Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* [Rozszerzenie interfejsu wiersza polecenia dla Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Kroki wdrażania modeli uczenia maszynowego w programie Docker

Po wdrożeniu modelu do obliczeń nielokalnych w Azure Machine Learning są wykonywane następujące czynności:

1. Pliku dockerfile określony w obiekcie Environments w InferenceConfig jest wysyłany do chmury wraz z zawartością katalogu źródłowego
1. Jeśli wcześniej skompilowany obraz nie jest dostępny w rejestrze kontenerów, nowy obraz platformy Docker zostanie skompilowany w chmurze i zapisany w domyślnym rejestrze kontenera obszaru roboczego.
1. Obraz platformy Docker z rejestru kontenerów jest pobierany do obiektu docelowego obliczeń.
1. Domyślny magazyn obiektów BLOB obszaru roboczego jest instalowany w miejscu docelowym obliczeń, zapewniając dostęp do zarejestrowanych modeli
1. Serwer sieci Web jest inicjowany przez uruchomienie funkcji skryptu wejścia `init()`
1. Gdy wdrożony model odbiera żądanie, `run()` funkcja obsługuje to żądanie

Główną różnicą podczas korzystania z lokalnego wdrożenia jest to, że obraz kontenera jest zbudowany na komputerze lokalnym, co oznacza, że należy zainstalować platformę Docker na potrzeby lokalnego wdrożenia.

Zrozumienie tych ogólnych kroków powinno ułatwić zrozumienie, gdzie występują błędy.

## <a name="get-deployment-logs"></a>Pobierz dzienniki wdrożenia

Pierwszym krokiem w debugowaniu błędów jest pobieranie dzienników wdrożenia. Najpierw postępuj zgodnie z [instrukcjami w tym miejscu](how-to-deploy-and-where.md#connect-to-your-workspace) , aby połączyć się z obszarem roboczym.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

Aby pobrać dzienniki ze wdrożonej usługi sieci Web, wykonaj następujące czynności:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Przy założeniu, że masz obiekt typu `azureml.core.Workspace` o nazwie `ws` , możesz wykonać następujące czynności:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Debuguj lokalnie

Jeśli masz problemy podczas wdrażania modelu w programie ACI lub AKS, wdróż go jako lokalną usługę sieci Web. Korzystanie z lokalnej usługi internetowej ułatwia rozwiązywanie problemów. Aby rozwiązać problem z wdrożeniem lokalnym, zobacz artykuł dotyczący [rozwiązywania problemów lokalnych](./how-to-troubleshoot-deployment-local.md).

## <a name="container-cannot-be-scheduled"></a>Nie można zaplanować kontenera

Podczas wdrażania usługi w docelowym elemencie obliczeniowym usługi Azure Kubernetes Service usługa Azure Machine Learning podejmie próbę zaplanowania usługi przy użyciu żądanej ilości zasobów. Jeśli w klastrze nie ma dostępnych węzłów z odpowiednią ilością zasobów po 5 minutach, wdrożenie zakończy się niepowodzeniem. Komunikat o błędzie to `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Ten błąd można rozwiązać, dodając więcej węzłów, zmieniając jednostkę SKU węzłów lub zmieniając wymagania dotyczące zasobów usługi. 

Komunikat o błędzie będzie zazwyczaj wskazywał zasób, którego potrzebujesz więcej — na przykład jeśli zostanie wyświetlony komunikat o błędzie informujący, że `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` usługa wymaga procesora GPU, a w klastrze istnieją trzy węzły, które nie mają dostępnych procesorów GPU. Można to rozwiązać, dodając więcej węzłów, jeśli używasz jednostki SKU procesora GPU, przełączając się do jednostki SKU z włączoną obsługą procesora GPU, jeśli nie zmienisz środowiska, aby nie wymagały procesora GPU.  

## <a name="service-launch-fails"></a>Uruchamianie usługi nie powiodło się

Po pomyślnym skompilowaniu obrazu system podejmie próbę uruchomienia kontenera przy użyciu konfiguracji wdrożenia. W ramach procesu uruchamiania kontenera `init()` Funkcja w skrypcie oceniania jest wywoływana przez system. Jeśli w funkcji występują nieprzechwycone wyjątki `init()` , w komunikacie o błędzie może pojawić się błąd **CrashLoopBackOff** .

Skorzystaj z informacji znajdujących się w artykule [Inspekcja dziennika platformy Docker](how-to-troubleshoot-deployment-local.md#dockerlog) .

## <a name="function-fails-get_model_path"></a>Niepowodzenie funkcji: get_model_path ()

Często w `init()` funkcji w skrypcie oceniania funkcja [model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) jest wywoływana w celu zlokalizowania pliku modelu lub folderu plików modelu w kontenerze. Jeśli nie można znaleźć pliku lub folderu modelu, działanie funkcji kończy się niepowodzeniem. Najprostszym sposobem debugowania tego błędu jest uruchomienie poniższego kodu w języku Python w ramach powłoki kontenera:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ten przykład drukuje ścieżkę lokalną (względem `/var/azureml-app` ) w kontenerze, w którym skrypt oceniania oczekuje na znalezienie pliku lub folderu modelu. Następnie można sprawdzić, czy plik lub folder jest naprawdę tam, gdzie jest oczekiwany.

Ustawienie poziomu rejestrowania na debugowanie może spowodować zarejestrowanie dodatkowych informacji, co może być przydatne podczas identyfikowania błędu.

## <a name="function-fails-runinput_data"></a>Niepowodzenie funkcji: Uruchom (input_data)

Jeśli usługa została pomyślnie wdrożona, ale ulega awarii, gdy dane są ogłaszane w punkcie końcowym oceniania, można dodać instrukcję zwracającą błąd w `run(input_data)` funkcji tak, aby zamiast tego zwracała szczegółowy komunikat o błędzie. Przykład:

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
    > Ta zmiana nie powoduje *szybszego* tworzenia replik. Zamiast tego są tworzone przy niższym progu wykorzystania. Zamiast czekać, aż usługa zostanie wykorzystana przez 70%, zmiana wartości na 30% powoduje utworzenie replik w przypadku wystąpienia 30%.
    
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

Aby uzyskać więcej informacji na temat ustawiania `autoscale_target_utilization` , `autoscale_max_replicas` , i `autoscale_min_replicas` dla, zobacz odwołanie do modułu [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) .

## <a name="http-status-code-504"></a>Kod stanu HTTP 504

Kod stanu 504 wskazuje, że upłynął limit czasu żądania. Domyślny limit czasu wynosi 1 minutę.

Można zwiększyć limit czasu lub spróbować przyspieszyć usługę, modyfikując score.py w celu usunięcia niepotrzebnych wywołań. Jeśli te akcje nie rozrozwiązuje problemu, użyj informacji w tym artykule, aby debugować plik score.py. Kod może być w stanie innym niż odpowiada lub nieskończona pętla.

## <a name="advanced-debugging"></a>Zaawansowane debugowanie

Może być konieczne interaktywne debugowanie kodu Python zawartego we wdrożeniu modelu. Na przykład, jeśli skrypt wejścia kończy się niepowodzeniem i powód nie może być określony przez dodatkowe rejestrowanie. Za pomocą Visual Studio Code i debugpy można dołączyć do kodu działającego wewnątrz kontenera Docker.

Aby uzyskać więcej informacji, zobacz [interaktywny debugowanie w przewodniku vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Forum użytkownika dotyczące wdrażania modelu](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wdrażaniu:

* [Jak wdrożyć i gdzie](how-to-deploy-and-where.md)
* [Samouczek: uczenie & Wdrażanie modeli](tutorial-train-models-with-aml.md)
* [Jak uruchamiać i debugować eksperymenty lokalnie](./how-to-debug-visual-studio-code.md)