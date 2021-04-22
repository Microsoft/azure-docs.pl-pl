---
title: Wdrażanie modeli uczenia maszynowego w usłudze Kubernetes Service
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak wdrażać modele Azure Machine Learning jako usługę internetową przy użyciu Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: 6030462fc7c9678200aa14fa852a82d35f8703b6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877826"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Wdrażanie modelu w klastrze Azure Kubernetes Service klastra

Dowiedz się, jak używać Azure Machine Learning do wdrażania modelu jako usługi internetowej w usłudze Azure Kubernetes Service (AKS). Azure Kubernetes Service jest dobrym rozwiązaniem w przypadku wdrożeń produkcyjnych na dużą skalę. Użyj usługi Azure Kubernetes, jeśli potrzebujesz co najmniej jednej z następujących możliwości:

- __Krótki czas odpowiedzi__
- __Skalowanie automatyczne__ wdrożonej usługi
- __Rejestrowanie__
- __Zbieranie danych modelu__
- __Authentication__
- __Zakończenie TLS__
- __Opcje przyspieszania__ sprzętowego, takie jak procesor GPU i układ FPGA (Field-Programmable Gate Array)

Podczas wdrażania w Azure Kubernetes Service wdrażania w klastrze usługi AKS, który __jest połączony z obszarem roboczym.__ Aby uzyskać informacje na temat łączenia klastra usługi AKS z obszarem roboczym, zobacz Create and attach an Azure Kubernetes Service cluster (Tworzenie i [dołączanie klastra Azure Kubernetes Service usługi](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Zalecamy debugowanie lokalne przed wdrożeniem w usłudze internetowej. Aby uzyskać więcej informacji, zobacz [Debugowanie lokalne](./how-to-troubleshoot-deployment-local.md)
>
> Możesz również skorzystać z usługi Azure Machine Learning — [Wdrażanie w notesie lokalnym](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)

- Model uczenia maszynowego zarejestrowany w obszarze roboczym. Jeśli nie masz zarejestrowanego modelu, zobacz [Jak i gdzie wdrażać modele.](how-to-deploy-and-where.md)

- Rozszerzenie [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md)platformy Azure Machine Learning service , Azure Machine Learning python [SDK](/python/api/overview/azure/ml/intro)lub rozszerzenie [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

- We __fragmentach__ kodu w języku Python w tym artykule założono, że ustawiono następujące zmienne:

    * `ws` — Ustaw na swój obszar roboczy.
    * `model` — ustaw wartość na zarejestrowany model.
    * `inference_config` — ustaw na konfigurację wnioskowania dla modelu.

    Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [How and where to deploy models (Jak i gdzie wdrażać modele).](how-to-deploy-and-where.md)

- Fragmenty __kodu__ interfejsu wiersza polecenia w tym artykule zakładają, że utworzono `inferenceconfig.json` dokument. Aby uzyskać więcej informacji na temat tworzenia tego dokumentu, zobacz How and where to deploy models (Jak [i gdzie wdrażać modele).](how-to-deploy-and-where.md)

- Klaster Azure Kubernetes Service połączony z obszarem roboczym. Aby uzyskać więcej informacji, [zobacz Tworzenie i dołączanie klastra Azure Kubernetes Service klastra](how-to-create-attach-kubernetes.md).

    - Jeśli chcesz wdrożyć modele w węzłach procesora GPU lub węzłach FPGA (lub dowolnej konkretnej sku), musisz utworzyć klaster z określoną sku. Nie ma obsługi tworzenia puli węzłów pomocniczych w istniejącym klastrze i wdrażania modeli w puli węzłów pomocniczych.

## <a name="understand-the-deployment-processes"></a>Opis procesów wdrażania

Wyraz "wdrożenie" jest używany zarówno w u usługi Kubernetes, jak i Azure Machine Learning. "Wdrożenie" ma różne znaczenie w tych dwóch kontekstach. Na platformie Kubernetes element to `Deployment` konkretna jednostka określona za pomocą deklaratywnego pliku YAML. Kubernetes ma zdefiniowany cykl życia i konkretne relacje z `Deployment` innymi jednostkami kubernetes, takimi jak `Pods` i `ReplicaSets` . Więcej informacji na temat kubernetes można znaleźć na stronie [Co to jest kubernetes?](https://aka.ms/k8slearning).

W Azure Machine Learning", "wdrożenie" jest używane w bardziej ogólnym sensie, że zasoby projektu są dostępne i czyszczące. Kroki, które Azure Machine Learning część wdrożenia, to:

1. Spakowanie plików w folderze projektu z pominięciem plików określonych w pliku amlignore lub gitignore
1. Skalowanie w górę klastra obliczeniowego (odnosi się do usługi Kubernetes)
1. Budowania lub pobierania pliku dockerfile do węzła obliczeniowego (w odniesieniu do platformy Kubernetes)
    1. System oblicza skrót: 
        - Obraz podstawowy 
        - Niestandardowe kroki platformy Docker (zobacz [Wdrażanie modelu przy użyciu niestandardowego obrazu podstawowego platformy Docker)](./how-to-deploy-custom-docker-image.md)
        - Definicja conda YAML (zobacz [Create & use software environments in Azure Machine Learning](./how-to-use-environments.md))
    1. System używa tego skrótu jako klucza w wyszukiwaniach obszaru roboczego Azure Container Registry (ACR)
    1. Jeśli nie zostanie znaleziony, szuka dopasowania w globalnym acr
    1. Jeśli nie zostanie znaleziony, system skompilował nowy obraz (który będzie buforowany i wypychany do usługi ACR obszaru roboczego)
1. Pobieranie pliku zip projektu do magazynu tymczasowego w węźle obliczeniowym
1. Rozpakowanie pliku projektu
1. Wykonywanie węzła obliczeniowego `python <entry script> <arguments>`
1. Zapisywanie dzienników, plików modelu i innych plików zapisywanych na `./outputs` koncie magazynu skojarzonym z obszarem roboczym
1. Skalowanie w dół zasobów obliczeniowych, w tym usuwanie magazynu tymczasowego (odnosi się do usługi Kubernetes)

### <a name="azure-ml-router"></a>Router usługi Azure ML

Składnik frontony (azureml-fe), który kieruje przychodzące żądania wnioskowania do wdrożonych usług, automatycznie skaluje się zgodnie z potrzebami. Skalowanie pliku azureml-fe zależy od przeznaczenia i rozmiaru klastra AKS (liczby węzłów). Przeznaczenie klastra i węzły są konfigurowane podczas tworzenia lub dołączania klastra [usługi AKS.](how-to-create-attach-kubernetes.md) Istnieje jedna usługa azureml-fe na klaster, która może być uruchomiona w wielu zasobnikach.

> [!IMPORTANT]
> W przypadku korzystania z klastra skonfigurowanego jako __dev-test__ samoobsługowy program skalowania jest **wyłączony.**

Narzędzie Azureml-fe skaluje w górę (w pionie), aby używać większej liczby rdzeni, i w poziomie (w poziomie), aby używać większej liczby zasobników. Podczas podejmowania decyzji o skalowania w górę jest używany czas, który zajmuje do przekierowania przychodzących żądań wnioskowania. Jeśli ten czas przekroczy wartość progową, nastąpi skalowanie w górę. Jeśli czas do przekierowania żądań przychodzących będzie nadal przekraczać próg, nastąpi skalowanie w zewnątrz.

Podczas skalowania w dół i do dołu używane jest użycie procesora CPU. Jeśli próg użycia procesora CPU zostanie osiągnięty, frontony najpierw zostaną przeskalowane w dół. Jeśli użycie procesora CPU spadnie do progu skalowania w skali w, nastąpi operacja skalowania w skali w. Skalowanie w górę i w górę będzie mieć miejsce tylko wtedy, gdy jest dostępna wystarczająca ilość zasobów klastra.

## <a name="understand-connectivity-requirements-for-aks-inferencing-cluster"></a>Informacje o wymaganiach dotyczących łączności dla klastra wnioskowania usługi AKS

Gdy Azure Machine Learning tworzy lub dołącza klaster AKS, klaster usługi AKS jest wdrażany przy użyciu jednego z następujących dwóch modeli sieciowych:
* Sieć Kubenet — zasoby sieciowe są zwykle tworzone i konfigurowane podczas wdrażania klastra usługi AKS.
* Sieć Azure Container Networking Interface (CNI) — klaster usługi AKS jest połączony z istniejącymi zasobami i konfiguracjami sieci wirtualnej.

W pierwszym trybie sieci sieć jest tworzona i skonfigurowana prawidłowo dla Azure Machine Learning service. W przypadku drugiego trybu sieciowego, ponieważ klaster jest połączony z istniejącą siecią wirtualną, szczególnie gdy dla istniejącej sieci wirtualnej jest używany niestandardowy system DNS, klient musi zwrócić szczególną uwagę na wymagania dotyczące łączności dla klastra wnioskowania usługi AKS i zapewnić rozpoznawanie nazw DNS oraz łączność wychodzącą na potrzeby wnioskowania usługi AKS.

Na poniższym diagramie przedstawiono wszystkie wymagania dotyczące łączności w przypadku wnioskowania usługi AKS. Czarne strzałki reprezentują rzeczywistą komunikację, a niebieskie strzałki reprezentują nazwy domen, które powinien rozpoznać system DNS kontrolowany przez klienta.

 ![Wymagania dotyczące łączności dla wnioskowania usługi AKS](./media/how-to-deploy-aks/aks-network.png)

### <a name="overall-dns-resolution-requirements"></a>Ogólne wymagania dotyczące rozpoznawania nazw DNS
Rozpoznawanie nazw DNS w istniejącej sieci wirtualnej jest pod kontrolą klienta. Następujące wpisy DNS powinny być rozpoznawalne:
* Serwer interfejsu API usługi AKS w postaci \<cluster\> \<region\> hcp. . azmk8s.io
* Microsoft Container Registry (MCR): mcr.microsoft.com
* Klienta Azure Container Registry (ARC) w postaci \<ACR name\> .azurecr.io
* Konto usługi Azure Storage w postaci \<account\> plików table.core.windows.net \<account\> i blob.core.windows.net
* (Opcjonalnie) W przypadku uwierzytelniania usługi AAD: api.azureml.ms
* Nazwa domeny punktu końcowego oceniania wygenerowana automatycznie przez usługę Azure ML lub niestandardowa nazwa domeny. Automatycznie wygenerowana nazwa domeny będzie wyglądać tak: \<leaf-domain-label \+ auto-generated suffix\> \<region\> . cloudapp.azure.com

### <a name="connectivity-requirements-in-chronological-order-from-cluster-creation-to-model-deployment"></a>Wymagania dotyczące łączności w kolejności chronologicznej: od utworzenia klastra do wdrożenia modelu

W procesie tworzenia lub dołączania usługi AKS router usługi Azure ML (azureml-fe) jest wdrażany w klastrze usługi AKS. Aby wdrożyć router usługi Azure ML, węzeł usługi AKS powinien mieć możliwość:
* Rozpoznawanie usługi DNS dla serwera interfejsu API usługi AKS
* Rozpoznawanie systemu DNS dla mcr w celu pobrania obrazów platformy Docker dla routera usługi Azure ML
* Pobieranie obrazów z mcr, gdzie łączność wychodząca jest wymagana

Zaraz po wdrożeniu narzędzia azureml-fe zostanie podejmowana próba uruchomienia, co wymaga:
* Rozpoznawanie usługi DNS dla serwera interfejsu API usługi AKS
* Wykonywanie zapytań do serwera interfejsu API usługi AKS w celu odnajdywania innych wystąpień (jest to usługa z wieloma zasobnikami)
* Nawiązywanie połączenia z innymi wystąpieniami

Po zakończeniu działania narzędzia azureml-fe wymagana jest dodatkowa łączność:
* Nawiązywanie połączenia z usługą Azure Storage w celu pobrania konfiguracji dynamicznej
* Rozpoznawanie nazw DNS dla serwera uwierzytelniania usługi AAD api.azureml.ms i komunikowanie się z nim, gdy wdrożona usługa korzysta z uwierzytelniania usługi AAD.
* Wykonywanie zapytań o serwer interfejsu API usługi AKS w celu odnajdywania wdrożonych modeli
* Komunikacja z wdrożonym modelem POD

W czasie wdrażania modelu węzeł usługi AKS powinien mieć możliwość: 
* Rozpoznawanie systemu DNS dla usługi ACR klienta
* Pobieranie obrazów z ACR klienta
* Rozpoznawanie systemu DNS dla bloków BLOB platformy Azure, w których jest przechowywany model
* Pobieranie modeli z plików BLOB platformy Azure

Po wdrożeniu modelu i wdrożeniu usługi azureml-fe automatycznie odnajduje go przy użyciu interfejsu API usługi AKS i będzie gotowy do przekierowania do niego żądania. Musi mieć możliwość komunikowania się z modelem POD.
>[!Note]
>Jeśli wdrożony model wymaga łączności (np. odpytując zewnętrzną bazę danych lub inną usługę REST, pobierając obiekt BLOB itp.), należy włączyć rozpoznawanie nazw DNS i komunikację wychodzącą dla tych usług.

## <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Aby wdrożyć model do Azure Kubernetes Service, utwórz konfigurację wdrożenia __opisującą__ wymagane zasoby obliczeniowe. Na przykład liczba rdzeni i pamięć. Potrzebna jest również konfiguracja __wnioskowania__ opisującą środowisko potrzebne do hostowania modelu i usługi internetowej. Aby uzyskać więcej informacji na temat tworzenia konfiguracji wnioskowania, zobacz How and where to deploy models (Jak [i gdzie wdrażać modele).](how-to-deploy-and-where.md)

> [!NOTE]
> Liczba modeli do wdrożenia jest ograniczona do 1000 modeli na wdrożenie (na kontener).

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

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wdrożyć przy użyciu interfejsu wiersza polecenia, użyj następującego polecenia. Zastąp `myaks` element nazwą docelowego obiektu obliczeniowego AKS. Zastąp `mymodel:1` nazwą i wersją zarejestrowanego modelu. Zastąp `myservice` nazwą, aby nadać tej usłudze:

```azurecli-interactive
az ml model deploy --ct myaks -m mymodel:1 -n myservice --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Aby uzyskać więcej informacji, zobacz informacje dotyczące [wdrażania modelu az ml.](/cli/azure/ml/model#az_ml_model_deploy)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Aby uzyskać informacje na temat używania VS Code, zobacz [wdrażanie do usługi AKS za pośrednictwem VS Code usługi](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Wdrażanie za pośrednictwem VS Code wymaga wcześniejszego utworzenia klastra usługi AKS lub dołączeniu go do obszaru roboczego.

---

### <a name="autoscaling"></a>Skalowanie automatyczne

Składnik, który obsługuje skalowanie automatyczne dla wdrożeń modelu usługi Azure ML, to azureml-fe, który jest routerem żądań inteligentnych. Ponieważ wszystkie żądania wnioskowania przechodzi przez ten proces, zawiera on dane niezbędne do automatycznego skalowania wdrożonych modeli.

> [!IMPORTANT]
> * **Nie włączaj rozwiązania Kubernetes Horizontal Pod Autoscaler (HPA) dla wdrożeń modeli.** Może to spowodować, że dwa składniki skalowania automatycznego będą ze sobą konkurować. Projekt Azureml-fe jest przeznaczony do automatycznego skalowania modeli wdrożonych przez usługę Azure ML, gdzie narzędzie HPA musi odgadnąć lub przybliżone wykorzystanie modelu na podstawie metryk ogólnych, takich jak użycie procesora CPU lub konfiguracja metryk niestandardowych.
> 
> * **Narzędzie Azureml-fe nie skaluje** liczby węzłów w klastrze usługi AKS, ponieważ może to prowadzić do nieoczekiwanego wzrostu kosztów. Zamiast tego **skaluje liczbę replik dla modelu w** granicach klastra fizycznego. Jeśli musisz skalować liczbę węzłów w klastrze, możesz ręcznie skalować klaster lub skonfigurować automatyczne skalowanie [klastra usługi AKS.](../aks/cluster-autoscaler.md)

Autoskalowanie można kontrolować za pomocą ustawień `autoscale_target_utilization` , i dla usługi internetowej `autoscale_min_replicas` `autoscale_max_replicas` AKS. W poniższym przykładzie pokazano, jak włączyć skalowanie automatyczne:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Decyzje dotyczące skalowania w górę/w dół są podejmowane na podstawie użycia bieżących replik kontenerów. Liczba replik zajętych (przetwarzanych w żądaniu) podzielona przez łączną liczbę bieżących replik to bieżące wykorzystanie. Jeśli ta liczba przekracza `autoscale_target_utilization` wartość , tworzone są kolejne repliki. Jeśli jest niższa, repliki są zmniejszane. Domyślnie wykorzystanie docelowe wynosi 70%.

Decyzje dotyczące dodawania replik są wczesne i szybkie (około 1 sekundy). Decyzje dotyczące usuwania replik są ostrożne (około 1 minuty).

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

Aby uzyskać więcej informacji na temat `autoscale_target_utilization` ustawiania , i , zobacz `autoscale_max_replicas` `autoscale_min_replicas` AksWebservice module reference (Informacje o module [AksWebservice).](/python/api/azureml-core/azureml.core.webservice.akswebservice)

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Wdrażanie modeli do usługi AKS przy użyciu kontrolowanego wdrażania (wersja zapoznawcza)

Analizowanie i podszycie wersji modelu w kontrolowany sposób przy użyciu punktów końcowych. Za pojedynczym punktem końcowym można wdrożyć maksymalnie sześć wersji. Punkty końcowe zapewniają następujące możliwości:

* Skonfiguruj procent __oceniania ruchu wysyłanego do każdego punktu końcowego.__ Na przykład przekieruj 20% ruchu do punktu końcowego "test" i 80% do "produkcyjnego".

    > [!NOTE]
    > Jeśli nie uwzględniasz 100% ruchu, wszelkie pozostałe wartości procentowe są kierowane do __domyślnej wersji__ punktu końcowego. Jeśli na przykład skonfigurujesz wersję punktu końcowego "test" w celu uzyskania 10% ruchu, a "prod" dla 30%, pozostałe 60% zostanie wysłanych do domyślnej wersji punktu końcowego.
    >
    > Pierwsza utworzona wersja punktu końcowego jest automatycznie konfigurowana jako domyślna. Można to zmienić, ustawiając `is_default=True` ustawienia podczas tworzenia lub aktualizowania wersji punktu końcowego.
     
* Otaguj wersję punktu końcowego __jako kontrolę__ lub __leczenie.__ Na przykład bieżącą wersją produkcyjnego punktu końcowego może być kontrolka, podczas gdy potencjalne nowe modele są wdrażane jako wersje leczenia. Po dokonaniu oceny wydajności wersji leczenia, jeśli jedna z nich będzie okazać się atńsza od bieżącej kontrolki, może zostać podniesiena do nowej produkcji/kontroli.

    > [!NOTE]
    > Możesz mieć tylko jedną __kontrolkę.__ Można mieć wiele sposobów na leczenie.

Możesz włączyć usługę App Insights, aby wyświetlać metryki operacyjne punktów końcowych i wdrożonych wersji.

### <a name="create-an-endpoint"></a>Tworzenie punktu końcowego
Gdy wszystko będzie gotowe do wdrożenia modeli, utwórz punkt końcowy oceniania i wdobyj swoją pierwszą wersję. W poniższym przykładzie pokazano, jak wdrożyć i utworzyć punkt końcowy przy użyciu zestawu SDK. Pierwsze wdrożenie zostanie zdefiniowane jako wersja domyślna, co oznacza, że nieokreślony percentyl ruchu we wszystkich wersjach zostanie przejść do wersji domyślnej.  

> [!TIP]
> W poniższym przykładzie konfiguracja ustawia początkową wersję punktu końcowego do obsługi 20% ruchu. Ponieważ jest to pierwszy punkt końcowy, jest to również wersja domyślna. A ponieważ nie mamy żadnych innych wersji dla pozostałych 80% ruchu, jest on również przekierowyny do wartości domyślnej. Do momentu wdrożenia innych wersji, które przejmują procent ruchu, ta skutecznie odbiera 100% ruchu.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

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

### <a name="update-and-add-versions-to-an-endpoint"></a>Aktualizowanie i dodawanie wersji do punktu końcowego

Dodaj kolejną wersję do punktu końcowego i skonfiguruj percentyl ruchu oceniania przechodzący do wersji. Istnieją dwa typy wersji: wersja kontrolna i wersja do leczenia. Istnieje wiele wersji leczenia, które ułatwiają porównanie z pojedynczą wersją kontrolną.

> [!TIP]
> Druga wersja, utworzona przez poniższy fragment kodu, akceptuje 10% ruchu. Pierwsza wersja jest skonfigurowana dla 20%, więc tylko 30% ruchu jest konfigurowane dla określonych wersji. Pozostałe 70% jest wysyłanych do pierwszej wersji punktu końcowego, ponieważ jest to również wersja domyślna.

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

Zaktualizuj istniejące wersje lub usuń je w punkcie końcowym. Możesz zmienić domyślny typ wersji, typ kontrolki i percentyl ruchu. W poniższym przykładzie druga wersja zwiększa ruch do 40% i jest teraz wartością domyślną.

> [!TIP]
> Po poniższym fragmencie kodu druga wersja jest teraz domyślna. Jest ona teraz skonfigurowana dla 40%, podczas gdy oryginalna wersja jest nadal skonfigurowana dla 20%. Oznacza to, że 40% ruchu nie jest uwzględnianych przez konfiguracje wersji. Pozostałą część ruchu będzie przekierowywaną do drugiej wersji, ponieważ jest ona teraz domyślna. Efektywnie odbiera 80% ruchu.

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

## <a name="web-service-authentication"></a>Uwierzytelnianie usługi internetowej

Podczas wdrażania w Azure Kubernetes Service __uwierzytelnianie__ oparte na kluczach jest domyślnie włączone. Można również włączyć __uwierzytelnianie oparte na tokenach.__ Uwierzytelnianie oparte na tokenach wymaga, aby klienci używali konta Azure Active Directory do żądania tokenu uwierzytelniania, który jest używany do żądania do wdrożonej usługi.

Aby __wyłączyć__ uwierzytelnianie, `auth_enabled=False` ustaw parametr podczas tworzenia konfiguracji wdrożenia. Poniższy przykład wyłącza uwierzytelnianie przy użyciu zestawu SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Aby uzyskać informacje na temat uwierzytelniania z aplikacji klienckiej, zobacz Temat Consume an Azure Machine Learning model deployed as a web service (Stosowanie modelu Azure Machine Learning [wdrożonego jako usługa internetowa).](how-to-consume-web-service.md)

### <a name="authentication-with-keys"></a>Uwierzytelnianie przy użyciu kluczy

Jeśli jest włączone uwierzytelnianie klucza, możesz użyć metody , aby pobrać podstawowy i `get_keys` pomocniczy klucz uwierzytelniania:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Jeśli musisz ponownie wygenerować klucz, użyj [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29)

### <a name="authentication-with-tokens"></a>Uwierzytelnianie przy użyciu tokenów

Aby włączyć uwierzytelnianie tokenu, `token_auth_enabled=True` ustaw parametr podczas tworzenia lub aktualizowania wdrożenia. Poniższy przykład umożliwia uwierzytelnianie tokenu przy użyciu zestawu SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Jeśli uwierzytelnianie tokenu jest włączone, możesz użyć metody , aby pobrać token JWT i czas `get_token` wygaśnięcia tego tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po upływie czasu tokenu należy zażądać nowego `refresh_by` tokenu.
>
> Firma Microsoft zdecydowanie zaleca utworzenie obszaru roboczego Azure Machine Learning w tym samym regionie, w Azure Kubernetes Service klastra. Aby uwierzytelnić się przy użyciu tokenu, usługa internetowa wywoła region, w którym Azure Machine Learning obszar roboczy. Jeśli region obszaru roboczego jest niedostępny, nie będzie można pobrać tokenu dla usługi internetowej, nawet jeśli klaster znajduje się w innym regionie niż obszar roboczy. Dzięki temu uwierzytelnianie oparte na tokenach będzie niedostępne, dopóki region obszaru roboczego nie będzie ponownie dostępny. Ponadto im większa odległość między regionem klastra i regionem obszaru roboczego, tym dłużej będzie trwać pobieranie tokenu.
>
> Aby pobrać token, musisz użyć zestawu SDK Azure Machine Learning lub [polecenia az ml service get-access-token.](/cli/azure/ml/service#az_ml_service_get_access_token)


### <a name="vulnerability-scanning"></a>Skanowanie pod kątem luk w zabezpieczeniach

Usługa Azure Security Center zapewnia ujednolicone zarządzanie zabezpieczeniami i zaawansowaną ochronę przed zagrożeniami na potrzeby różnych obciążeń chmury hybrydowej. Należy zezwolić Azure Security Center na skanowanie zasobów i postępuj zgodnie z zaleceniami. Aby uzyskać więcej informacji, [zobacz Integracja usług Azure Kubernetes Services z Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Następne kroki

* [Używanie kontroli RBAC platformy Azure na użytek autoryzacji platformy Kubernetes](../aks/manage-azure-rbac.md)
* [Bezpieczne środowisko wnioskowania za pomocą usługi Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Jak wdrożyć model przy użyciu niestandardowego obrazu platformy Docker](how-to-deploy-custom-docker-image.md)
* [Rozwiązywanie problemów z wdrażaniem](how-to-troubleshoot-deployment.md)
* [Aktualizowanie usługi internetowej](how-to-deploy-update-web-service.md)
* [Zabezpieczanie usługi internetowej za pomocą usługi Azure Machine Learning przy użyciu protokołu TLS](how-to-secure-web-service.md)
* [Wykorzystanie modelu uczenia maszynowego wdrożonego jako usługa internetowa](how-to-consume-web-service.md)
* [Monitorowanie modeli Azure Machine Learning za pomocą Application Insights](how-to-enable-app-insights.md)
* [Zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md)
