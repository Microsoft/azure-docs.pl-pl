---
title: Wdróż modele ML w usłudze Kubernetes Service
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrożyć modele Azure Machine Learning jako usługę sieci Web przy użyciu usługi Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 50f8768aec12b8bda8d9d489462a8f61e8d83c18
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999187"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Wdrażanie modelu w klastrze usługi Azure Kubernetes Service


Dowiedz się, jak za pomocą Azure Machine Learning wdrożyć model jako usługę sieci Web w usłudze Azure Kubernetes Service (AKS). Usługa Azure Kubernetes Service jest dobra w przypadku dużych wdrożeń produkcyjnych. Użyj usługi Azure Kubernetes Service, jeśli potrzebujesz co najmniej jednej z następujących możliwości:

- __Krótki czas odpowiedzi__
- __Skalowanie__ automatyczne wdrożonej usługi
- __Rejestrowanie__
- __Zbieranie danych modelu__
- __Authentication__
- __Zakończenie protokołu TLS__
- Opcje __przyspieszania sprzętowego__ , takie jak macierze oparte na procesorach GPU i polach (FPGA)

Podczas wdrażania w usłudze Azure Kubernetes należy wdrożyć klaster AKS, który jest __połączony z obszarem roboczym__. Aby uzyskać informacje na temat łączenia klastra AKS z obszarem roboczym, zobacz [Tworzenie i dołączanie klastra usługi Azure Kubernetes](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Zalecamy debugowanie lokalnie przed wdrożeniem w usłudze sieci Web. Aby uzyskać więcej informacji, zobacz [debugowanie lokalne](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> Możesz również skorzystać z usługi Azure Machine Learning — [Wdrażanie w notesie lokalnym](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- Model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz zarejestrowanego modelu, zapoznaj [się z tematem jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- W fragmentach kodu w języku __Python__ w tym artykule założono, że ustawiono następujące zmienne:

    * `ws` -Ustaw na obszar roboczy.
    * `model` -Ustaw na zarejestrowany model.
    * `inference_config` -Ustaw na konfigurację wnioskowania dla modelu.

    Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- W fragmentach __interfejsu wiersza polecenia__ w tym artykule przyjęto założenie, że dokument został utworzony `inferenceconfig.json` . Aby uzyskać więcej informacji na temat tworzenia tego dokumentu, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

- Klaster usługi Kubernetes platformy Azure połączony z obszarem roboczym. Aby uzyskać więcej informacji, zobacz [Tworzenie i dołączanie klastra usługi Azure Kubernetes Service](how-to-create-attach-kubernetes.md).

    - Jeśli chcesz wdrożyć modele na węzłach GPU lub węzłach FPGA (lub dowolnej określonej jednostce SKU), należy utworzyć klaster z określoną jednostką SKU. Nie jest obsługiwane tworzenie puli węzłów pomocniczych w istniejącym klastrze i wdrażanie modeli w puli węzłów pomocniczych.

## <a name="understand-the-deployment-processes"></a>Informacje o procesach wdrażania

Słowo "Deployment" jest używane zarówno w Kubernetes, jak i Azure Machine Learning. "Wdrożenie" ma inne znaczenie w tych dwóch kontekstach. W Kubernetes, a `Deployment` jest konkretną jednostką określoną przy użyciu deklaratywnego pliku YAML. Kubernetes `Deployment` ma zdefiniowany cykl życia i konkretne relacje z innymi jednostkami Kubernetes, takimi jak `Pods` i `ReplicaSets` . Aby dowiedzieć się więcej na temat Kubernetes z dokumentów i filmów wideo, zobacz [co to jest Kubernetes?](https://aka.ms/k8slearning).

W Azure Machine Learning "wdrożenie" jest używane w bardziej ogólnym sensie udostępniania i czyszczenia zasobów projektu. Kroki, które Azure Machine Learning rozważają część wdrożenia, to:

1. Zapakowywanie plików w folderze projektu, ignorując te określone w. amlignore lub. gitignore
1. Skalowanie w górę klastra obliczeniowego (odnosi się do Kubernetes)
1. Kompilowanie lub pobieranie pliku dockerfile do węzła obliczeniowego (odnosi się do Kubernetes)
    1. System oblicza wartość skrótu: 
        - Obraz podstawowy 
        - Niestandardowe kroki platformy Docker (zobacz [Wdrażanie modelu przy użyciu niestandardowego obrazu platformy Docker](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image))
        - Conda Definition YAML (zobacz [tworzenie & używanie środowisk oprogramowania w Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. System używa tego skrótu jako klucza w odnośniku Azure Container Registry obszaru roboczego (ACR)
    1. Jeśli nie zostanie znaleziona, szuka dopasowania w ACR globalnym
    1. Jeśli nie zostanie znaleziona, system kompiluje nowy obraz (który zostanie zbuforowany i wypychany do obszaru roboczego ACR)
1. Pobieranie spakowanego pliku projektu do tymczasowego magazynu w węźle obliczeniowym
1. Rozpakowywanie pliku projektu
1. Wykonywanie węzła obliczeniowego `python <entry script> <arguments>`
1. Zapisywanie dzienników, plików modelu i innych plików zapisywanych na `./outputs` koncie magazynu skojarzonym z obszarem roboczym
1. Skalowanie zasobów obliczeniowych w dół, w tym Usuwanie magazynu tymczasowego (odnosi się do Kubernetes)

### <a name="azure-ml-router"></a>Router Azure ML

Składnik frontonu (Azure-Fe), który przekierowuje przychodzące żądania wnioskowania do wdrożonych usług automatycznie skaluje się w razie potrzeby. Skalowanie programu Uczenie maszynowe — Fe opiera się na przeznaczeniu i rozmiarze klastra AKS (liczba węzłów). Przeznaczenie i węzły klastra są konfigurowane podczas [tworzenia lub dołączania klastra AKS](how-to-create-attach-kubernetes.md). Istnieje jedna usługa Azure-Fe na klaster, która może być uruchomiona w wielu zasobnikach.

> [!IMPORTANT]
> W przypadku korzystania z klastra skonfigurowanego jako programowanie __-testowanie__samodzielny jest **wyłączony**.

Uczenie maszynowe — Fe skaluje się w górę (w pionie), aby używać większej liczby rdzeni i wychodzące (w poziomie) do korzystania z więcej wartości Gdy podejmowana jest decyzja o skalowaniu w górę, używany jest czas potrzebny do rozesłania przychodzących żądań wnioskowania. Jeśli ten czas przekroczy wartość progową, nastąpi skalowanie w górę. Jeśli czas do skierowania żądań przychodzących nadal przekroczy wartość progową, nastąpi skalowanie w poziomie.

Podczas skalowania w dół i w programie używane jest użycie procesora CPU. Jeśli zostanie osiągnięty próg użycia procesora CPU, fronton zostanie najpierw przeskalowany w dół. Jeśli użycie procesora spadnie do wartości progowej skalowania, odbywa się Operacja skalowania w poziomie. Skalowanie w górę i w dół będzie odbywać się tylko wtedy, gdy dostępne są wystarczające zasoby klastra.

## <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Aby wdrożyć model w usłudze Azure Kubernetes Service, Utwórz __konfigurację wdrożenia__ opisującą wymaganą wartość zasobów obliczeniowych. Na przykład liczba rdzeni i pamięć. Potrzebna jest również __Konfiguracja wnioskowania__opisująca środowisko wymagane do hostowania modelu i usługi sieci Web. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz [jak i gdzie wdrażać modele](how-to-deploy-and-where.md).

> [!NOTE]
> Liczba modeli do wdrożenia jest ograniczona do 1 000 modeli na wdrożenie (na kontener).

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py&preserve-view=true)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py&preserve-view=true)
* [Model. deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, należy użyć poniższe polecenie. Zamień `myaks` na nazwę elementu docelowego obliczeń AKS. Zastąp `mymodel:1` wartość nazwą i wersją zarejestrowanego modelu. Zamień na `myservice` nazwę, która ma zostać przydana do tej usługi:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz [AZ ml model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) Reference.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aby uzyskać informacje na temat korzystania z VS Code, zobacz [wdrażanie do AKS za pomocą rozszerzenia vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Wdrożenie za pomocą VS Code wymaga, aby klaster AKS został utworzony lub dołączony do obszaru roboczego z wyprzedzeniem.

---

### <a name="autoscaling"></a>Skalowanie automatyczne

Składnik obsługujący Skalowanie automatyczne dla wdrożeń modeli Azure ML to platforma Azure-FE, która jest routerem żądania inteligentnego. Ze względu na to, że wszystkie żądania wnioskowania przechodzą przez niego, ma dane niezbędne do automatycznego skalowania wdrożonych modeli.

> [!IMPORTANT]
> * **Nie należy włączać funkcji automatycznego skalowania w poziomie Kubernetes pod kątem wdrożeń modeli**. W takim przypadku dwa składniki skalowania automatycznego będą konkurować ze sobą nawzajem. Usługa Uczenie maszynowe — Fe została zaprojektowana w celu automatycznego skalowania modeli wdrożonych przez usługę Azure ML, gdzie HPA musiałaby odgadnąć lub przybliżyć użycie modelu z ogólnej metryki, takiej jak użycie procesora CPU lub Konfiguracja metryki niestandardowej.
> 
> * Usługa **Azure-Fe nie skaluje liczby węzłów w KLASTRZE AKS**, ponieważ może to prowadzić do nieoczekiwanego wzrostu kosztów. Zamiast tego **skaluje liczbę replik dla modelu** w granicach klastra fizycznego. Jeśli potrzebujesz skalować liczbę węzłów w klastrze, możesz ręcznie skalować klaster lub [skonfigurować automatyczne skalowanie klastra AKS](/azure/aks/cluster-autoscaler).

Skalowanie automatyczne może być kontrolowane przez ustawienie `autoscale_target_utilization` , `autoscale_min_replicas` i `autoscale_max_replicas` dla usługi sieci Web AKS. W poniższym przykładzie pokazano, jak włączyć skalowanie automatyczne:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Decyzje dotyczące skalowania w górę/w dół opierają się na wykorzystaniu bieżących replik kontenerów. Liczba replik, które są zajęte (przetwarzanie żądania) podzielona przez łączną liczbę bieżących replik jest bieżącym wykorzystaniem. Jeśli ta liczba przekroczy `autoscale_target_utilization` , zostanie utworzona więcej replik. Jeśli jest niższa, repliki są skracane. Domyślnie użycie docelowe to 70%.

Decyzje dotyczące dodawania replik to eager i Fast (około 1 s). Decyzje dotyczące usuwania replik są ostrożne (około 1 minutę).

Wymagane repliki można obliczyć przy użyciu następującego kodu:

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

Aby uzyskać więcej informacji na temat ustawienia `autoscale_target_utilization` , `autoscale_max_replicas` , i `autoscale_min_replicas` , zobacz odwołanie do modułu [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true) .

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Wdróż modele do AKS przy użyciu kontrolowanego wdrożenia (wersja zapoznawcza)

Analizuj i podnieś wersje modeli w kontrolowany sposób za pomocą punktów końcowych. Można wdrożyć maksymalnie sześć wersji za pojedynczym punktem końcowym. Punkty końcowe zapewniają następujące możliwości:

* Skonfiguruj __procent ruchu oceniania wysyłany do każdego punktu końcowego__. Na przykład Roześlij 20% ruchu do punktu końcowego "test" i 80% do "produkcji".

    > [!NOTE]
    > W przypadku braku konta dla 100% ruchu każdy pozostały procent jest kierowany do __domyślnej__ wersji punktu końcowego. Na przykład jeśli skonfigurujesz punkt końcowy w wersji "test", aby uzyskać 10% ruchu i "prod" dla 30%, pozostałe 60% zostanie wysłane do domyślnej wersji punktu końcowego.
    >
    > Utworzona wersja pierwszego punktu końcowego jest automatycznie konfigurowana jako domyślna. Można to zmienić przez ustawienie `is_default=True` podczas tworzenia lub aktualizowania wersji punktu końcowego.
     
* Oznacz wersję punktu końcowego jako __kontrolkę__ lub __leczenie__. Na przykład bieżąca wersja punktu końcowego produkcji może być kontrolką, podczas gdy potencjalne nowe modele są wdrażane jako wersje obróbki. Po ocenie wydajności wersji obróbki, jeśli jeden z nich wykonuje bieżącą kontrolkę, można ją podnieść do nowej produkcji/kontroli.

    > [!NOTE]
    > Możesz mieć tylko __jedną__ kontrolkę. Można mieć wiele przeróbek.

Możesz włączyć usługi App Insights, aby wyświetlać metryki operacyjne punktów końcowych i wdrożonych wersji.

### <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
Gdy wszystko będzie gotowe do wdrożenia modeli, Utwórz punkt końcowy oceniania i Wdróż swoją pierwszą wersję. Poniższy przykład pokazuje, jak wdrożyć i utworzyć punkt końcowy przy użyciu zestawu SDK. Pierwsze wdrożenie zostanie zdefiniowane jako wersja domyślna, co oznacza, że nieokreślony ruch o percentylu we wszystkich wersjach będzie przechodził do wersji domyślnej.  

> [!TIP]
> W poniższym przykładzie konfiguracja ustawia początkową wersję punktu końcowego, aby obsługiwała 20% ruchu. Ponieważ jest to pierwszy punkt końcowy, jest to również wersja domyślna. I ponieważ nie mamy żadnych innych wersji dla innych 80% ruchu, jest on również kierowany do wartości domyślnej. Dopóki nie zostaną wdrożone inne wersje, które pobierają procent ruchu, to jeden z nich przybierze 100% ruchu.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aktualizowanie i Dodawanie wersji do punktu końcowego

Dodaj kolejną wersję do punktu końcowego i skonfiguruj przechodzenie oceniające ruch do wersji. Istnieją dwa typy wersji, kontrolki i wersja leczenia. Może istnieć wiele wersji obróbki, które ułatwią porównanie z wersją jednego formantu.

> [!TIP]
> Druga wersja utworzona przez Poniższy fragment kodu akceptuje 10% ruchu. Pierwsza wersja została skonfigurowana pod kątem 20%, więc skonfigurowano tylko 30% ruchu dla określonych wersji. Pozostałe 70% jest wysyłane do pierwszej wersji punktu końcowego, ponieważ jest to również wersja domyślna.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Zaktualizuj istniejące wersje lub usuń je w punkcie końcowym. Można zmienić domyślny typ wersji, typ kontrolki i percentyl ruchu. W poniższym przykładzie druga wersja zwiększa swój ruch do 40% i jest teraz domyślnie.

> [!TIP]
> Po poniższym fragmencie kodu druga wersja jest teraz domyślna. Jest teraz skonfigurowany do 40%, podczas gdy wersja oryginalna jest nadal skonfigurowana dla 20%. Oznacza to, że 40% ruchu nie jest uwzględniana przez konfiguracje wersji. Ruch pozostały do drugiej wersji, ponieważ jest teraz domyślny. Skutecznie otrzymuje 80% ruchu.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Uwierzytelnianie usługi sieci Web

W przypadku wdrażania w usłudze Azure Kubernetes uwierzytelnianie __oparte na kluczach__ jest domyślnie włączone. Można również włączyć uwierzytelnianie __oparte na tokenach__ . Uwierzytelnianie oparte na tokenach wymaga, aby klienci używali konta Azure Active Directory do żądania tokenu uwierzytelniania, który jest używany do wysyłania żądań do wdrożonej usługi.

Aby __wyłączyć__ uwierzytelnianie, należy ustawić `auth_enabled=False` parametr podczas tworzenia konfiguracji wdrożenia. Poniższy przykład powoduje wyłączenie uwierzytelniania przy użyciu zestawu SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Informacje o uwierzytelnianiu z aplikacji klienckiej można znaleźć w temacie [Korzystanie z modelu Azure Machine Learning wdrożonego jako usługa sieci Web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Jeśli uwierzytelnianie klucza jest włączone, można użyć metody, `get_keys` Aby pobrać podstawowy i pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli musisz ponownie wygenerować klucz, użyj [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true)

### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Aby włączyć uwierzytelnianie tokenu, należy ustawić `token_auth_enabled=True` parametr podczas tworzenia lub aktualizowania wdrożenia. Poniższy przykład umożliwia uwierzytelnianie tokenu przy użyciu zestawu SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Jeśli jest włączone uwierzytelnianie tokenu, można użyć metody, `get_token` Aby pobrać token JWT i czas wygaśnięcia tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po upływie czasu tokenu trzeba będzie zażądać nowego tokenu `refresh_by` .
>
> Firma Microsoft zdecydowanie zaleca utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w którym znajduje się klaster usługi Azure Kubernetes. W celu uwierzytelnienia przy użyciu tokenu usługa sieci Web wykona wywołanie do regionu, w którym jest tworzony obszar roboczy Azure Machine Learning. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi sieci Web nawet wtedy, gdy klaster znajduje się w innym regionie niż obszar roboczy. W efekcie uwierzytelnianie oparte na tokenach jest niedostępne do momentu ponownego udostępnienia regionu obszaru roboczego. Ponadto im większa odległość między regionem klastra a regionem obszaru roboczego, tym dłużej potrwa pobieranie tokenu.
>
> Aby pobrać token, należy użyć zestawu SDK Azure Machine Learning lub polecenia [AZ ml Service Get-Access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-service-get-access-token) .


### <a name="vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach

Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. Należy zezwolić Azure Security Center na skanowanie zasobów i postępować zgodnie z zaleceniami. Aby uzyskać więcej informacji, zobacz [Integracja usług Azure Kubernetes Services z usługą Security Center](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration).

## <a name="next-steps"></a>Następne kroki

* [Bezpieczne środowisko inferencing z platformą Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Korzystanie z modelu ML wdrożonego jako usługa sieci Web](how-to-consume-web-service.md)
* [Monitoruj modele Azure Machine Learning przy użyciu Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
