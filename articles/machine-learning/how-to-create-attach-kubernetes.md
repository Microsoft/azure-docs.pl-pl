---
title: Tworzenie i dołączanie usługi Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć nowy klaster usługi Azure Kubernetes za pomocą Azure Machine Learning, lub dołączać istniejący klaster AKS do obszaru roboczego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/11/2021
ms.openlocfilehash: bc8f7aa6827ce251799acd0673d43344c0833c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149328"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Tworzenie i dołączanie klastra usługi Azure Kubernetes Service

Azure Machine Learning można wdrożyć przeszkolone modele uczenia maszynowego w usłudze Azure Kubernetes Service. Należy jednak najpierw __utworzyć__ klaster usługi Azure Kubernetes Service (AKS) w obszarze roboczym usługi Azure ml lub __dołączyć__ istniejący klaster AKS. Ten artykuł zawiera informacje dotyczące tworzenia i dołączania klastra.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie obszaru roboczego Azure Machine Learning](how-to-manage-workspace.md).

- [Rozszerzenie interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning Service](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK języka Python](/python/api/overview/azure/ml/intro)lub [rozszerzenia Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Jeśli planujesz używać Virtual Network platformy Azure do zabezpieczania komunikacji między obszarem roboczym usługi Azure ML a klastrem AKS, zapoznaj się z [izolacją sieci podczas szkolenia & artykułu wnioskowania](./how-to-network-security-overview.md) .

## <a name="limitations"></a>Ograniczenia

- Jeśli potrzebujesz **Usługa Load Balancer w warstwie Standardowa (moduł równoważenia obciążenia)** wdrożonego w klastrze zamiast podstawowego Load BALANCER (BLB), Utwórz klaster w portalu AKS/interfejsie wiersza polecenia/SDK, a następnie **Dołącz** go do obszaru roboczego AML.

- Jeśli masz Azure Policy, które ograniczają tworzenie publicznych adresów IP, tworzenie klastra AKS zakończy się niepowodzeniem. AKS wymaga publicznego adresu IP dla [ruchu wychodzącego](../aks/limit-egress-traffic.md). Artykuł dotyczący ruchu wychodzącego zawiera również wskazówki dotyczące blokowania ruchu wyjściowego z klastra za pośrednictwem publicznego adresu IP, z wyjątkiem kilku w pełni kwalifikowanych nazw domen. Istnieją dwa sposoby włączania publicznego adresu IP:
    - Klaster może korzystać z publicznego adresu IP utworzonego domyślnie przy użyciu BLB lub modułu równoważenia obciążenia lub
    - Klaster można utworzyć bez publicznego adresu IP, a następnie publiczny adres IP skonfigurowany za pomocą zapory z trasą zdefiniowaną przez użytkownika. Aby uzyskać więcej informacji, zobacz Dostosowywanie ruchu wychodzącego [klastra przy użyciu trasy zdefiniowanej przez użytkownika](../aks/egress-outboundtype.md).
    
    Płaszczyzna sterująca AML nie komunikuje się z tym publicznym adresem IP. Komunikuje się ona z płaszczyzną kontroli AKS dla wdrożeń. 

- W przypadku **dołączania** klastra AKS z [włączonym dozwolonym zakresem adresów IP w celu uzyskania dostępu do serwera interfejsu API](../aks/api-server-authorized-ip-ranges.md)Włącz zakres adresów IP płaszczyzny kontroli AML dla klastra AKS. Płaszczyzna kontroli AMLa jest wdrażana w sparowanych regionach i wdraża zbiory wnioskowania w klastrze AKS. Bez dostępu do serwera interfejsu API nie można wdrożyć wywnioskowanych zasobów. W przypadku włączenia zakresów adresów IP w klastrze AKS należy użyć [zakresów adresów IP](https://www.microsoft.com/download/confirmation.aspx?id=56519) dla obu [par regionów](../best-practices-availability-paired-regions.md) .

    Autoryzowane zakresy adresów IP współdziałają z usługa Load Balancer w warstwie Standardowa.

- W przypadku **dołączania** klastra AKS musi on znajdować się w tej samej subskrypcji platformy Azure co obszar roboczy Azure Machine Learning.

- Jeśli chcesz użyć prywatnego klastra AKS (przy użyciu prywatnego linku platformy Azure), musisz najpierw utworzyć klaster, a następnie **dołączyć** go do obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Tworzenie prywatnego klastra usługi Azure Kubernetes Service](../aks/private-clusters.md).

- Nazwa obliczeniowa klastra AKS musi być unikatowa w obszarze roboczym usługi Azure ML.
    - Nazwa jest wymagana i musi mieć długość od 3 do 24 znaków.
    - Prawidłowe znaki to wielkie i małe litery, cyfry i znaki.
    - Nazwa musi rozpoczynać się od litery.
    - Nazwa musi być unikatowa we wszystkich istniejących obliczeniach w regionie świadczenia usługi Azure. Jeśli wybrana nazwa nie jest unikatowa, zostanie wyświetlony alert.
   
 - Jeśli chcesz wdrożyć modele na węzłach **GPU** lub węzłach **FPGA** (lub dowolnej określonej jednostce SKU), należy utworzyć klaster z określoną jednostką SKU. Nie jest obsługiwane tworzenie puli węzłów pomocniczych w istniejącym klastrze i wdrażanie modeli w puli węzłów pomocniczych.
 
- Podczas tworzenia lub dołączania klastra możesz wybrać, czy chcesz utworzyć klaster na potrzeby programowania i __testowania__ czy __produkcji__. Jeśli chcesz utworzyć klaster AKS na potrzeby tworzenia, __sprawdzania poprawności__ i __testowania__ zamiast __środowiska__ produkcyjnego, ustaw __cel klastra__ na __Dev-Test__. Jeśli nie określisz celu klastra, tworzony jest klaster __produkcyjny__ . 

    > [!IMPORTANT]
    > Klaster __deweloperski-test__ nie jest odpowiedni dla ruchu na poziomie produkcyjnym i może zwiększyć czas wnioskowania. Klastry deweloperskie i testowe nie gwarantują odporności na uszkodzenia.

- W przypadku tworzenia lub dołączania klastra, Jeśli klaster będzie używany do __produkcji__, musi on zawierać co najmniej 12 __wirtualnych procesorów CPU__. Liczbę wirtualnych procesorów CPU można obliczyć przez pomnożenie __liczby węzłów__ w klastrze przez __liczbę rdzeni__ zapewnioną przez wybrany rozmiar maszyny wirtualnej. Na przykład jeśli używasz rozmiaru maszyny wirtualnej "Standard_D3_v2", który ma 4 rdzenie wirtualne, należy wybrać 3 lub większą liczbę węzłów.

    W przypadku klastra __deweloperskiego-testowego__ polecenie jest używane co najmniej 2 wirtualne procesory CPU.

- Zestaw SDK Azure Machine Learning nie zapewnia obsługi skalowania klastra AKS. Aby skalować węzły w klastrze, użyj interfejsu użytkownika dla klastra AKS w programie Azure Machine Learning Studio. Można zmienić tylko liczbę węzłów, a nie rozmiar maszyn wirtualnych klastra. Więcej informacji na temat skalowania węzłów w klastrze AKS można znaleźć w następujących artykułach:

    - [Ręczne skalowanie liczby węzłów w klastrze AKS](../aks/scale-cluster.md)
    - [Konfigurowanie automatycznego skalowania klastra w AKS](../aks/cluster-autoscaler.md)

- __Nie należy bezpośrednio aktualizować klastra przy użyciu konfiguracji YAML__. Chociaż usługi Azure Kubernetes Services obsługują aktualizacje za pośrednictwem konfiguracji YAML, wdrożenia Azure Machine Learning przesłonią zmiany. Tylko dwa pola YAML, które nie zostaną zastępować, są __limitami żądań__ oraz __procesora CPU i pamięci__.

## <a name="azure-kubernetes-service-version"></a>Wersja usługi Azure Kubernetes Service

Usługa Azure Kubernetes umożliwia tworzenie klastra przy użyciu różnych wersji programu Kubernetes. Aby uzyskać więcej informacji na temat dostępnych wersji, zobacz [obsługiwane wersje Kubernetes w usłudze Azure Kubernetes Service](../aks/supported-kubernetes-versions.md).

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

Aby znaleźć dostępne i domyślne wersje programu AKS, należy użyć polecenia [platformy Azure](/cli/azure/install-azure-cli) polecenie [AZ AKS Get-Versions](/cli/azure/aks#az_aks_get_versions). Na przykład następujące polecenie zwraca wersje dostępne w regionie zachodnie stany USA:

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

Jeśli chcesz **programowo sprawdzić dostępne wersje**, użyj interfejsu API REST [usługi Container Service Client](/rest/api/container-service/container%20service%20client/listorchestrators) . Aby znaleźć dostępne wersje, należy zapoznać się z wpisami, gdzie `orchestratorType` jest `Kubernetes` . Skojarzone `orchestrationVersion` wpisy zawierają dostępne wersje, które można **dołączyć** do obszaru roboczego.

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

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać informacje na temat tworzenia klastra AKS w portalu, zobacz [Tworzenie obiektów docelowych obliczeń w programie Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="attach-an-existing-aks-cluster"></a>Dołącz istniejący klaster AKS

**Szacowany czas:** Około 5 minut.

Jeśli masz już klaster AKS w ramach subskrypcji platformy Azure, możesz go używać z obszarem roboczym.

> [!TIP]
> Istniejący klaster AKS może znajdować się w regionie świadczenia usługi Azure innym niż obszar roboczy Azure Machine Learning.


> [!WARNING]
> Nie należy tworzyć wielu jednoczesnych załączników do tego samego klastra AKS z obszaru roboczego. Na przykład po dołączeniu jednego klastra AKS do obszaru roboczego przy użyciu dwóch różnych nazw. Każdy nowy załącznik spowoduje przerwanie poprzednich istniejących załączników.
>
> Jeśli chcesz ponownie dołączyć klaster AKS, na przykład aby zmienić ustawienia konfiguracji TLS lub innego klastra, musisz najpierw usunąć istniejący załącznik przy użyciu [AksCompute. Odłącz ()](/python/api/azureml-core/azureml.core.compute.akscompute#detach--).

Aby uzyskać więcej informacji na temat tworzenia klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu, zobacz następujące artykuły:

* [Tworzenie klastra AKS (interfejs wiersza polecenia)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az-aks-create)
* [Tworzenie klastra AKS (Portal)](../aks/kubernetes-walkthrough-portal.md)
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

* [AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute. Attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

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

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Attach AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks) Reference.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać informacje na temat dołączania klastra AKS w portalu, zobacz [Tworzenie obiektów docelowych obliczeń w programie Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Utwórz lub Dołącz klaster AKS z zakończeniem TLS
Podczas [tworzenia lub dołączania klastra AKS](how-to-create-attach-kubernetes.md)można włączyć zakończenie protokołu TLS z obiektami konfiguracji **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** i **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Obie metody zwracają obiekt konfiguracji, który ma metodę **enable_ssl** i można użyć metody **enable_ssl** do włączenia protokołu TLS.

Poniższy przykład pokazuje, jak włączyć działanie protokołu TLS z automatyczną generowaniem i konfiguracją certyfikatów TLS przy użyciu certyfikatu Microsoft w ramach okapu.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
Poniższy przykład pokazuje, jak włączyć zakończenie protokołu TLS z certyfikatem niestandardowym i niestandardową nazwą domeny. W przypadku domeny niestandardowej i certyfikatu należy zaktualizować rekord DNS, aby wskazywał adres IP punktu końcowego oceniania, zobacz [Aktualizowanie usługi DNS](how-to-secure-web-service.md#update-your-dns) .

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> Aby uzyskać więcej informacji na temat zabezpieczania wdrażania modelu w klastrze AKS, zobacz [Korzystanie z protokołu TLS w celu zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](how-to-secure-web-service.md)

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Utwórz lub Dołącz klaster AKS, aby użyć wewnętrznego Load Balancer z prywatnym adresem IP
Podczas tworzenia lub dołączania klastra AKS można skonfigurować klaster tak, aby korzystał z wewnętrznego Load Balancer. Przy użyciu wewnętrznego Load Balancer punkty końcowe oceniania dla wdrożeń AKS będą używać prywatnego adresu IP w ramach sieci wirtualnej. Poniższe fragmenty kodu przedstawiają sposób konfigurowania wewnętrznego Load Balancer klastra AKS.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning nie obsługuje zakończenia protokołu TLS z wewnętrznym Load Balancer. Wewnętrzny Load Balancer ma prywatny adres IP, a prywatny adres IP może być w innej sieci, a certyfikat może być recused. 

>[!NOTE]
> Aby uzyskać więcej informacji na temat zabezpieczania środowiska inferencing, zobacz [zabezpieczanie środowiska Azure Machine Learning inferencing](how-to-secure-inferencing-vnet.md) .

## <a name="detach-an-aks-cluster"></a>Odłączanie klastra AKS

Aby odłączyć klaster od obszaru roboczego, należy użyć jednej z następujących metod:

> [!WARNING]
> Korzystając z Azure Machine Learning Studio, SDK lub rozszerzenia interfejsu wiersza polecenia platformy Azure dla usługi Machine Learning w celu odłączenia klastra AKS nie **powoduje usunięcia klastra AKS**. Aby usunąć klaster, zobacz [Korzystanie z interfejsu wiersza polecenia platformy Azure z AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

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

W Azure Machine Learning Studio wybierz pozycję __obliczenia__, __klastry wnioskowania__ i klaster, który chcesz usunąć. Użyj linku __Odłącz__ , aby odłączyć klaster.

---

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="update-the-cluster"></a>Aktualizowanie klastra

Należy ręcznie zastosować aktualizacje Azure Machine Learning składników zainstalowanych w klastrze usługi Azure Kubernetes. 

Te aktualizacje można zastosować, odłączając klaster od obszaru roboczego Azure Machine Learning, a następnie dołączając ponownie klaster do obszaru roboczego. Jeśli w klastrze jest włączony protokół TLS, podczas ponownego dołączania klastra należy podać certyfikat TLS/SSL i klucz prywatny. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Jeśli nie masz już certyfikatu TLS/SSL i klucza prywatnego lub używasz certyfikatu wygenerowanego przez Azure Machine Learning, możesz pobrać pliki przed odłączeniem klastra, łącząc się z klastrem przy użyciu `kubectl` i pobierając klucz tajny `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes przechowuje wpisy tajne w zakodowanym formacie Base-64. `cert.pem` `key.pem` Przed udostępnieniem tych elementów tajnych należy oprzeć na base-64 `attach_config.enable_ssl` . 

### <a name="webservice-failures"></a>Błędy usługi WebService

Wiele błędów sieci Web w AKS można debugować, łącząc się z klastrem przy użyciu programu `kubectl` . Możesz uzyskać `kubeconfig.json` dla klastra AKS, uruchamiając

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Następne kroki

* [Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes](../aks/manage-azure-rbac.md)
* [Jak i gdzie należy wdrożyć model](how-to-deploy-and-where.md)
* [Wdrażanie modelu w klastrze usługi Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)