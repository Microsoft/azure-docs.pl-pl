---
title: Tworzenie i dołączanie usługi Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Usługi Azure Kubernetes Service (AKS) można użyć do wdrożenia modelu uczenia maszynowego jako usługi sieci Web. Dowiedz się, jak utworzyć nowy klaster AKS za pomocą Azure Machine Learning. Dowiesz się również, jak dołączyć istniejący klaster AKS do obszaru roboczego Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 10/02/2020
ms.openlocfilehash: cade5a4329cdfc11c1b256ba01e9764f60a476a6
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667864"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Tworzenie i dołączanie klastra usługi Azure Kubernetes Service

Azure Machine Learning można wdrożyć przeszkolone modele uczenia maszynowego w usłudze Azure Kubernetes Service. Należy jednak najpierw __utworzyć__ klaster usługi Azure Kubernetes Service (AKS) w obszarze roboczym usługi Azure ml lub __dołączyć__ istniejący klaster AKS. Ten artykuł zawiera informacje dotyczące tworzenia i dołączania klastra.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Jeśli planujesz używać Virtual Network platformy Azure do zabezpieczania komunikacji między obszarem roboczym usługi Azure ML a klastrem AKS, zapoznaj się z [izolacją sieci podczas szkolenia & artykułu wnioskowania](how-to-enable-virtual-network.md) .

## <a name="limitations"></a>Ograniczenia

- Jeśli potrzebujesz **Usługa Load Balancer w warstwie Standardowa (moduł równoważenia obciążenia)** wdrożonego w klastrze zamiast podstawowego Load BALANCER (BLB), Utwórz klaster w portalu AKS/interfejsie wiersza polecenia/SDK, a następnie **Dołącz** go do obszaru roboczego AML.

- Jeśli masz Azure Policy, które ograniczają tworzenie publicznych adresów IP, tworzenie klastra AKS zakończy się niepowodzeniem. AKS wymaga publicznego adresu IP dla [ruchu wychodzącego](/azure/aks/limit-egress-traffic). Artykuł dotyczący ruchu wychodzącego zawiera również wskazówki dotyczące blokowania ruchu wyjściowego z klastra za pośrednictwem publicznego adresu IP, z wyjątkiem kilku w pełni kwalifikowanych nazw domen. Istnieją dwa sposoby włączania publicznego adresu IP:
    - Klaster może korzystać z publicznego adresu IP utworzonego domyślnie przy użyciu BLB lub modułu równoważenia obciążenia lub
    - Klaster można utworzyć bez publicznego adresu IP, a następnie publiczny adres IP skonfigurowany za pomocą zapory z trasą zdefiniowaną przez użytkownika. Aby uzyskać więcej informacji, zobacz Dostosowywanie ruchu wychodzącego [klastra przy użyciu trasy zdefiniowanej przez użytkownika](/azure/aks/egress-outboundtype).
    
    Płaszczyzna sterująca AML nie komunikuje się z tym publicznym adresem IP. Komunikuje się ona z płaszczyzną kontroli AKS dla wdrożeń. 

- W przypadku **dołączania** klastra AKS z [włączonym dozwolonym zakresem adresów IP w celu uzyskania dostępu do serwera interfejsu API](/azure/aks/api-server-authorized-ip-ranges)Włącz zakres adresów IP płaszczyzny kontroli AML dla klastra AKS. Płaszczyzna kontroli AMLa jest wdrażana w sparowanych regionach i wdraża zbiory wnioskowania w klastrze AKS. Bez dostępu do serwera interfejsu API nie można wdrożyć wywnioskowanych zasobów. W przypadku włączenia zakresów adresów IP w klastrze AKS należy użyć [zakresów adresów IP](https://www.microsoft.com/download/confirmation.aspx?id=56519) dla obu [par regionów](/azure/best-practices-availability-paired-regions) .

    Autoryzowane zakresy adresów IP współdziałają z usługa Load Balancer w warstwie Standardowa.

- Jeśli chcesz użyć prywatnego klastra AKS (przy użyciu prywatnego linku platformy Azure), musisz najpierw utworzyć klaster, a następnie **dołączyć** go do obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Tworzenie prywatnego klastra usługi Azure Kubernetes Service](/azure/aks/private-clusters).

- Nazwa obliczeniowa klastra AKS musi być unikatowa w obszarze roboczym usługi Azure ML.
    - Nazwa jest wymagana i musi mieć długość od 3 do 24 znaków.
    - Prawidłowe znaki to wielkie i małe litery, cyfry i znaki.
    - Nazwa musi rozpoczynać się od litery.
    - Nazwa musi być unikatowa we wszystkich istniejących obliczeniach w regionie świadczenia usługi Azure. Jeśli wybrana nazwa nie jest unikatowa, zostanie wyświetlony alert.
   
 - Jeśli chcesz wdrożyć modele na węzłach **GPU** lub węzłach **FPGA** (lub dowolnej określonej jednostce SKU), należy utworzyć klaster z określoną jednostką SKU. Nie jest obsługiwane tworzenie puli węzłów pomocniczych w istniejącym klastrze i wdrażanie modeli w puli węzłów pomocniczych.
 
- Podczas tworzenia lub dołączania klastra możesz wybrać, czy chcesz utworzyć klaster na potrzeby programowania i __testowania__ czy __produkcji__. Jeśli chcesz utworzyć klaster AKS na potrzeby tworzenia, __sprawdzania poprawności__i __testowania__ zamiast __środowiska__produkcyjnego, ustaw __cel klastra__ na __Dev-Test__. Jeśli nie określisz celu klastra, tworzony jest klaster __produkcyjny__ . 

    > [!IMPORTANT]
    > Klaster __deweloperski-test__ nie jest odpowiedni dla ruchu na poziomie produkcyjnym i może zwiększyć czas wnioskowania. Klastry deweloperskie i testowe nie gwarantują odporności na uszkodzenia.

- W przypadku tworzenia lub dołączania klastra, Jeśli klaster będzie używany do __produkcji__, musi on zawierać co najmniej 12 __wirtualnych procesorów CPU__. Liczbę wirtualnych procesorów CPU można obliczyć przez pomnożenie __liczby węzłów__ w klastrze przez __liczbę rdzeni__ zapewnioną przez wybrany rozmiar maszyny wirtualnej. Na przykład jeśli używasz rozmiaru maszyny wirtualnej "Standard_D3_v2", który ma 4 rdzenie wirtualne, należy wybrać 3 lub większą liczbę węzłów.

    W przypadku klastra __deweloperskiego-testowego__ polecenie jest używane co najmniej 2 wirtualne procesory CPU.

- Zestaw SDK Azure Machine Learning nie zapewnia obsługi skalowania klastra AKS. Aby skalować węzły w klastrze, użyj interfejsu użytkownika dla klastra AKS w programie Azure Machine Learning Studio. Można zmienić tylko liczbę węzłów, a nie rozmiar maszyn wirtualnych klastra. Więcej informacji na temat skalowania węzłów w klastrze AKS można znaleźć w następujących artykułach:

    - [Ręczne skalowanie liczby węzłów w klastrze AKS](../aks/scale-cluster.md)
    - [Konfigurowanie automatycznego skalowania klastra w AKS](../aks/cluster-autoscaler.md)

## <a name="azure-kubernetes-service-version"></a>Wersja usługi Azure Kubernetes

Usługa Azure Kubernetes umożliwia tworzenie klastra przy użyciu różnych wersji programu Kubernetes. Aby uzyskać więcej informacji na temat dostępnych wersji, zobacz [obsługiwane wersje Kubernetes w usłudze Azure Kubernetes Service](/azure/aks/supported-kubernetes-versions).

Podczas **tworzenia** klastra usługi Azure Kubernetes przy użyciu jednej z następujących metod *nie masz możliwości wyboru w używanej wersji* klastra:

* Azure Machine Learning Studio lub sekcja Azure Machine Learning Azure Portal.
* Machine Learning rozszerzenie interfejsu wiersza polecenia platformy Azure.
* Azure Machine Learning SDK.

Te metody tworzenia klastra AKS używają __domyślnej__ wersji klastra. *Wersja domyślna zmienia się w* miarę upływu czasu, gdy dostępne są nowe wersje Kubernetes.

W przypadku **dołączania** istniejącego klastra AKS obsługiwane są wszystkie obecnie obsługiwane wersje AKS.

> [!NOTE]
> Mogą istnieć sytuacje, w których istnieje starszy klaster, który nie jest już obsługiwany. W takim przypadku operacja Attach spowoduje zwrócenie błędu i wyświetlenie listy aktualnie obsługiwanych wersji.
>
> Możesz dołączyć wersje **zapoznawcze** . Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Obsługa korzystania z wersji zapoznawczej może być ograniczona. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Wersje dostępne i domyślne

Aby znaleźć dostępne i domyślne wersje programu AKS, należy użyć polecenia [platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) polecenie [AZ AKS Get-Versions](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_versions). Na przykład następujące polecenie zwraca wersje dostępne w regionie zachodnie stany USA:

```azurecli-interactive
az aks get-versions -l westus -o table
```

Dane wyjściowe tego polecenia są podobne do następującego tekstu:

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Aby znaleźć domyślną wersję używaną podczas **tworzenia** klastra za pomocą Azure Machine Learning, można użyć `--query` parametru, aby wybrać domyślną wersję:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

Dane wyjściowe tego polecenia są podobne do następującego tekstu:

```text
Result
--------
1.16.13
```

Jeśli chcesz **programowo sprawdzić dostępne wersje**, użyj interfejsu API REST [usługi Container Service Client](https://docs.microsoft.com/rest/api/container-service/container%20service%20client/listorchestrators) . Aby znaleźć dostępne wersje, należy zapoznać się z wpisami, gdzie `orchestratorType` jest `Kubernetes` . Skojarzone `orchestrationVersion` wpisy zawierają dostępne wersje, które można **dołączyć** do obszaru roboczego.

Aby znaleźć domyślną wersję używaną podczas **tworzenia** klastra za pomocą Azure Machine Learning, Znajdź wpis, gdzie `orchestratorType` jest `Kubernetes` i `default` jest `true` . Skojarzona `orchestratorVersion` wartość jest wersją domyślną. Poniższy fragment kodu JSON przedstawia przykładowy wpis:

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>Tworzenie nowego klastra AKS

**Szacowany czas**: około 10 minut.

Tworzenie i dołączanie klastra AKS jest jednym procesem czasu dla Twojego obszaru roboczego. Można ponownie użyć tego klastra dla wielu wdrożeń. W przypadku usunięcia klastra lub grupy zasobów, która zawiera tę usługę, należy utworzyć nowy klaster przy następnym wdrożeniu. Do obszaru roboczego można dołączyć wiele klastrów AKS.

W poniższym przykładzie pokazano, jak utworzyć nowy klaster AKS przy użyciu zestawu SDK i interfejsu wiersza polecenia:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać informacje na temat tworzenia klastra AKS w portalu, zobacz [Tworzenie obiektów docelowych obliczeń w programie Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Dołącz istniejący klaster AKS

**Szacowany czas:** Około 5 minut.

Jeśli klaster AKS jest już w ramach subskrypcji platformy Azure i jest w wersji 1,17 lub niższej, można go użyć do wdrożenia obrazu.

> [!TIP]
> Istniejący klaster AKS może znajdować się w regionie świadczenia usługi Azure innym niż obszar roboczy Azure Machine Learning.


> [!WARNING]
> Nie należy tworzyć wielu jednoczesnych załączników do tego samego klastra AKS z obszaru roboczego. Na przykład po dołączeniu jednego klastra AKS do obszaru roboczego przy użyciu dwóch różnych nazw. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.
>
> Jeśli chcesz ponownie dołączyć klaster AKS, na przykład aby zmienić ustawienia konfiguracji TLS lub innego klastra, musisz najpierw usunąć istniejący załącznik przy użyciu [AksCompute. Odłącz ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#detach--).

Aby uzyskać więcej informacji na temat tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu, zobacz następujące artykuły:

* [Tworzenie klastra AKS (interfejs wiersza polecenia)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest&preserve-view=true#az-aks-create)
* [Tworzenie klastra AKS (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest&preserve-view=true)
* [Tworzenie klastra AKS (szablon ARM w szablonach szybkiego startu platformy Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

W poniższym przykładzie pokazano, jak dołączyć istniejący klaster AKS do obszaru roboczego:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Aby uzyskać więcej informacji na temat klas, metod i parametrów używanych w tym przykładzie, zobacz następujące dokumenty referencyjne:

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py&preserve-view=true#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby dołączyć istniejący klaster przy użyciu interfejsu wiersza polecenia, należy uzyskać identyfikator zasobu istniejącego klastra. Aby uzyskać tę wartość, użyj następującego polecenia. Zamień `myexistingcluster` na nazwę klastra AKS. Zamień na `myresourcegroup` grupę zasobów zawierającą klaster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

To polecenie zwraca wartość podobną do następującego tekstu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Aby dołączyć istniejący klaster do obszaru roboczego, użyj następującego polecenia. Zamień na `aksresourceid` wartość zwracaną przez poprzednie polecenie. Zamień na `myresourcegroup` grupę zasobów, która zawiera obszar roboczy. Zamień `myworkspace` na nazwę obszaru roboczego.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-attach-aks) Reference.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać informacje na temat dołączania klastra AKS w portalu, zobacz [Tworzenie obiektów docelowych obliczeń w programie Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="detach-an-aks-cluster"></a>Odłączanie klastra AKS

Aby odłączyć klaster od obszaru roboczego, należy użyć jednej z następujących metod:

> [!WARNING]
> Korzystając z Azure Machine Learning Studio, SDK lub rozszerzenia interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning w celu odłączenia klastra AKS nie **powoduje usunięcia klastra AKS**. Aby usunąć klaster, zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure z AKS](/azure/aks/kubernetes-walkthrough#delete-the-cluster).

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby odłączyć istniejący klaster do obszaru roboczego, użyj następującego polecenia. Zamień na `myaks` nazwę, do której jest dołączony klaster AKS. Zamień na `myresourcegroup` grupę zasobów, która zawiera obszar roboczy. Zamień `myworkspace` na nazwę obszaru roboczego.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Machine Learning Studio wybierz pozycję __obliczenia__, __klastry wnioskowania__i klaster, który chcesz usunąć. Użyj linku __Odłącz__ , aby odłączyć klaster.

## <a name="next-steps"></a>Następne kroki

* [Jak i gdzie należy wdrożyć model](how-to-deploy-and-where.md)
* [Wdrażanie modelu w klastrze usługi Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)