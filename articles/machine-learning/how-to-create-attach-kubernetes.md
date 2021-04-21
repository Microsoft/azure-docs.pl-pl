---
title: Tworzenie i dołączanie Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć nowy klaster Azure Kubernetes Service za pośrednictwem usługi Azure Machine Learning lub jak dołączyć istniejący klaster usługi AKS do obszaru roboczego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 04/08/2021
ms.openlocfilehash: 1c9434d137114560b5585b081961497412dfbf69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770259"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Tworzenie i dołączanie Azure Kubernetes Service klastra

Azure Machine Learning wdrożyć wytrenowane modele uczenia maszynowego w Azure Kubernetes Service. Należy jednak najpierw __utworzyć__ klaster usługi Azure Kubernetes Service (AKS) z obszaru  roboczego usługi Azure ML lub dołączyć istniejący klaster usługi AKS. Ten artykuł zawiera informacje dotyczące tworzenia i dołączania klastra.

## <a name="prerequisites"></a>Wymagania wstępne

- Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Tworzenie Azure Machine Learning roboczego.](how-to-manage-workspace.md)

- Rozszerzenie [interfejsu wiersza polecenia](reference-azure-machine-learning-cli.md)platformy Azure Machine Learning service , Azure Machine Learning python [SDK](/python/api/overview/azure/ml/intro)lub rozszerzenie [Azure Machine Learning Visual Studio Code .](tutorial-setup-vscode-extension.md)

- Jeśli planujesz używanie usługi Azure Virtual Network do zabezpieczania komunikacji między obszarem roboczym usługi Azure ML i klastrem usługi AKS, przeczytaj artykuł Izolacja sieci podczas trenowania & [wnioskowania.](./how-to-network-security-overview.md)

## <a name="limitations"></a>Ograniczenia

- Jeśli potrzebujesz usługi **usługa Load Balancer w warstwie Standardowa (SLB)** wdrożonej w klastrze zamiast podstawowego zestawu Load Balancer (BLB), utwórz klaster w portalu  usługi AKS,interfejsie wiersza polecenia/zestawie SDK, a następnie dołącz go do obszaru roboczego usługi AML.

- Jeśli masz skoja Azure Policy, która ogranicza tworzenie publicznych adresów IP, tworzenie klastra usługi AKS nie powiedzie się. Dla ruchu wychodzącego aks jest [wymagany publiczny adres IP.](../aks/limit-egress-traffic.md) Artykuł na temat ruchu wychodzącego zawiera również wskazówki dotyczące blokowania ruchu wychodzącego z klastra za pośrednictwem publicznego adresu IP, z wyjątkiem kilku w pełni kwalifikowanych nazw domen. Istnieją 2 sposoby włączenia publicznego adresu IP:
    - Klaster może używać publicznego adresu IP utworzonego domyślnie z usługą BLB lub SLB albo
    - Klaster można utworzyć bez publicznego adresu IP, a następnie publiczny adres IP jest konfigurowany przy użyciu zapory z trasą zdefiniowaną przez użytkownika. Aby uzyskać więcej informacji, zobacz [Customize cluster egress with a user-defined-route](../aks/egress-outboundtype.md)(Dostosowywanie ruchu wychodzącego klastra za pomocą trasy zdefiniowanej przez użytkownika).
    
    Płaszczyzna sterowania AML nie rozmawia z tym publicznym adresem IP. Zawiera on rozmowy z płaszczyzną sterowania usługi AKS na temat wdrożeń. 

- W przypadku **dołączania** klastra usługi AKS z włączonym autoryzowanym zakresem [adresów IP](../aks/api-server-authorized-ip-ranges.md)umożliwiającym dostęp do serwera interfejsu API włącz zakresy adresów IP płaszczyzny sterowania AML dla klastra usługi AKS. Płaszczyzna sterowania AML jest wdrażana w sparowanych regionach i wdraża zasobniki wnioskowania w klastrze usługi AKS. Bez dostępu do serwera interfejsu API nie można wdrożyć zasobników wnioskowania. Zakresów [adresów IP należy używać](https://www.microsoft.com/download/confirmation.aspx?id=56519) w obu [sparowanych regionach](../best-practices-availability-paired-regions.md) podczas włączania zakresów adresów IP w klastrze usługi AKS.

    Autoryzowane zakresy adresów IP współpracuje tylko z usługa Load Balancer w warstwie Standardowa.

- Podczas **dołączania** klastra usługi AKS musi on znajdować się w tej samej subskrypcji platformy Azure, co Azure Machine Learning obszaru roboczego.

- Jeśli chcesz użyć prywatnego klastra usługi AKS (przy użyciu usługi Azure Private Link), musisz najpierw utworzyć klaster, a następnie dołączyć **go** do obszaru roboczego. Aby uzyskać więcej informacji, zobacz [Tworzenie klastra Azure Kubernetes Service prywatnego.](../aks/private-clusters.md)

- Nazwa obliczeniowa klastra usługi AKS MUSI być unikatowa w obszarze roboczym usługi Azure ML. Może zawierać litery, cyfry i łączniki. Musi zaczynać się literą, kończyć literą lub cyfrą i mieć długość od 3 do 24 znaków.
 
 - Jeśli chcesz wdrożyć modele w węzłach **procesora GPU** lub węzłach **FPGA** (lub dowolnej określonej sku), musisz utworzyć klaster z określoną sku. Nie ma obsługi tworzenia puli węzłów pomocniczych w istniejącym klastrze i wdrażania modeli w puli węzłów pomocniczych.
 
- Podczas tworzenia lub dołączania klastra możesz wybrać, czy chcesz utworzyć klaster do tworzenia __i testowania,__ czy __do produkcji.__ Jeśli chcesz utworzyć klaster usługi AKS na  potrzeby tworzenia, walidacji i testowania zamiast produkcji, ustaw cel klastra na __tworzenie i testowanie.__    Jeśli nie określisz celu klastra, __zostanie__ utworzony klaster produkcyjny. 

    > [!IMPORTANT]
    > Klaster __dewelopersko-testowy__ nie jest odpowiedni dla ruchu na poziomie produkcyjnym i może zwiększyć czas wnioskowania. Klastry dewelopersko-testowe również nie gwarantują odporności na uszkodzenia.

- Jeśli podczas tworzenia lub dołączania klastra będzie on używany w środowisku __produkcyjnym,__ musi zawierać co najmniej 12 __wirtualnych procesorów CPU.__ Liczbę procesorów wirtualnych można obliczyć przez  pomnożenie liczby węzłów w klastrze przez liczbę rdzeni zapewnianych przez wybrany rozmiar maszyny wirtualnej.  Jeśli na przykład używasz rozmiaru maszyny wirtualnej "Standard_D3_v2", który ma 4 rdzenie wirtualne, wybierz co najmniej 3 jako liczbę węzłów.

    W przypadku __klastra dewelopersko-testowego__ zalecamy co najmniej 2 wirtualne procesory CPU.

- Zestaw Azure Machine Learning SDK nie zapewnia obsługi skalowania klastra usługi AKS. Aby skalować węzły w klastrze, użyj interfejsu użytkownika klastra usługi AKS w Azure Machine Learning studio. Możesz zmienić tylko liczbę węzłów, a nie rozmiar maszyny wirtualnej klastra. Aby uzyskać więcej informacji na temat skalowania węzłów w klastrze usługi AKS, zobacz następujące artykuły:

    - [Ręczne skalowanie liczby węzłów w klastrze usługi AKS](../aks/scale-cluster.md)
    - [Konfigurowanie automatycznego skalowania klastra w u usługi AKS](../aks/cluster-autoscaler.md)

- __Nie należy bezpośrednio aktualizować klastra przy użyciu konfiguracji YAML.__ Chociaż usługi Azure Kubernetes Services obsługują aktualizacje za pośrednictwem konfiguracji YAML, Azure Machine Learning wdrożenia zastąpią wprowadzone zmiany. Jedynymi dwoma polami YAML, które nie zostaną zastąpione, są __limity__ żądań oraz __procesor i pamięć__.

- Tworzenie klastra AKS przy użyciu interfejsu Azure Machine Learning studio, zestawu SDK lub rozszerzenia interfejsu wiersza polecenia nie __jest__ idempotentne. Próba utworzenia zasobu ponownie spowoduje błąd, że klaster o takiej samej nazwie już istnieje.
    
    - Użycie szablonu Azure Resource Manager zasobów [Microsoft.MachineLearningServices/workspaces/computes](/azure/templates/microsoft.machinelearningservices/2019-11-01/workspaces/computes) do utworzenia klastra usługi AKS również nie __jest__ idempotentne. Jeśli spróbujemy ponownie użyć szablonu w celu zaktualizowania istniejącego zasobu, zostanie wyświetlony ten sam błąd.

## <a name="azure-kubernetes-service-version"></a>Wersja usługi Azure Kubernetes Service

Azure Kubernetes Service umożliwia utworzenie klastra przy użyciu różnych wersji usługi Kubernetes. Aby uzyskać więcej informacji na temat dostępnych wersji, zobacz [obsługiwane wersje kubernetes w Azure Kubernetes Service](../aks/supported-kubernetes-versions.md).

Podczas **tworzenia** Azure Kubernetes Service klastra przy użyciu jednej z  następujących metod nie ma możliwości wyboru w tworzonej wersji klastra:

* Azure Machine Learning studio lub Azure Machine Learning sekcji Azure Portal.
* Machine Learning dla interfejsu wiersza polecenia platformy Azure.
* Azure Machine Learning SDK.

Te metody tworzenia klastra usługi AKS używają __domyślnej__ wersji klastra. *Wersja domyślna zmienia się wraz z* czasem, gdy staną się dostępne nowe wersje kubernetes.

Podczas **dołączania istniejącego** klastra usługi AKS obsługiwane są wszystkie obecnie obsługiwane wersje usługi AKS.

> [!NOTE]
> Mogą wystąpić przypadki brzegowe, w których masz starszy klaster, który nie jest już obsługiwany. W takim przypadku operacja dołączania zwróci błąd i zwróci listę obecnie obsługiwanych wersji.
>
> Możesz dołączyć wersje **zapoznawcze.** Funkcja w wersji zapoznawczej jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Obsługa korzystania z wersji zapoznawczych może być ograniczona. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="available-and-default-versions"></a>Dostępne i domyślne wersje

Aby znaleźć dostępne i domyślne wersje usługi AKS, użyj polecenia [az aks get-versions](/cli/azure/aks#az_aks_get_versions)interfejsu wiersza polecenia platformy [Azure.](/cli/azure/install-azure-cli) Na przykład następujące polecenie zwraca wersje dostępne w regionie Zachodnie stany USA:

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

Aby znaleźć wersję domyślną  używaną podczas tworzenia klastra za pośrednictwem Azure Machine Learning, możesz użyć parametru , `--query` aby wybrać wersję domyślną:

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

Dane wyjściowe tego polecenia są podobne do następującego tekstu:

```text
Result
--------
1.16.13
```

Jeśli chcesz programowo **sprawdzić** dostępne wersje, użyj interfejsu API REST [Container Service Client - List Orchestrators.](/rest/api/container-service/container%20service%20client/listorchestrators) Aby znaleźć dostępne wersje, przyjrzyj się wpisom, w których `orchestratorType` znajduje się . `Kubernetes` Skojarzone wpisy `orchestrationVersion` zawierają dostępne wersje, które można **dołączyć do** obszaru roboczego.

Aby znaleźć domyślną wersję  używaną podczas tworzenia klastra za pośrednictwem usługi Azure Machine Learning, znajdź wpis , gdzie to , `orchestratorType` a to `Kubernetes` `default` `true` . `orchestratorVersion`Skojarzona wartość to wersja domyślna. Poniższy fragment kodu JSON przedstawia przykładowy wpis:

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

## <a name="create-a-new-aks-cluster"></a>Tworzenie nowego klastra usługi AKS

**Szacowany czas:** około 10 minut.

Tworzenie lub dołączanie klastra usługi AKS jest procesem tylko raz dla obszaru roboczego. Ten klaster można ponownie wykorzystać w wielu wdrożeniach. Jeśli usuniesz klaster lub grupę zasobów, która go zawiera, musisz utworzyć nowy klaster przy następnym wdrożeniu. Do obszaru roboczego może być dołączonych wiele klastrów usługi AKS.

W poniższym przykładzie pokazano, jak utworzyć nowy klaster usługi AKS przy użyciu zestawu SDK i interfejsu wiersza polecenia:

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
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Aby uzyskać więcej informacji, zobacz [az ml computetarget create aks reference (Az ml computetarget create aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać informacje na temat tworzenia klastra usługi AKS w portalu, zobacz [Create compute targets in Azure Machine Learning studio](how-to-create-attach-compute-studio.md#inference-clusters)(Tworzenie docelowych obiektów obliczeniowych w u Azure Machine Learning studio .

---

## <a name="attach-an-existing-aks-cluster"></a>Dołączanie istniejącego klastra usługi AKS

**Szacowany czas:** Około 5 minut.

Jeśli masz już klaster usługi AKS w subskrypcji platformy Azure, możesz go używać z obszarem roboczym.

> [!TIP]
> Istniejący klaster usługi AKS może być w regionie świadczenia usługi Azure innym Azure Machine Learning roboczym.


> [!WARNING]
> Nie należy tworzyć wielu równoczesnych załączników w tym samym klastrze usługi AKS z obszaru roboczego. Na przykład dołączanie jednego klastra usługi AKS do obszaru roboczego przy użyciu dwóch różnych nazw. Każdy nowy załącznik spowoduje przerwę w poprzednich istniejących załącznikach.
>
> Jeśli chcesz ponownie dołączyć klaster AKS, na przykład w celu zmiany ustawienia konfiguracji TLS lub innego klastra, musisz najpierw usunąć istniejący załącznik przy użyciu [funkcji AksCompute.detach().](/python/api/azureml-core/azureml.core.compute.akscompute#detach--)

Aby uzyskać więcej informacji na temat tworzenia klastra usługi AKS przy użyciu interfejsu wiersza polecenia platformy Azure lub portalu, zobacz następujące artykuły:

* [Tworzenie klastra AKS (interfejs wiersza polecenia)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az_aks_create)
* [Tworzenie klastra usługi AKS (portal)](../aks/kubernetes-walkthrough-portal.md)
* [Tworzenie klastra AKS (szablon usługi ARM w szablonach szybkiego startu platformy Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

W poniższym przykładzie pokazano, jak dołączyć istniejący klaster usługi AKS do obszaru roboczego:

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

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby dołączyć istniejący klaster przy użyciu interfejsu wiersza polecenia, musisz uzyskać identyfikator zasobu istniejącego klastra. Aby uzyskać tę wartość, użyj następującego polecenia. Zastąp `myexistingcluster` nazwą klastra usługi AKS. `myresourcegroup`Zamień na grupę zasobów zawierającą klaster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

To polecenie zwraca wartość podobną do następującego tekstu:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Aby dołączyć istniejący klaster do obszaru roboczego, użyj następującego polecenia. Zastąp `aksresourceid` wartość wartością zwróconą przez poprzednie polecenie. Zastąp `myresourcegroup` grupę zasobów zawierającą obszar roboczy. Zastąp `myworkspace` nazwą swojego obszaru roboczego.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Aby uzyskać więcej informacji, zobacz [az ml computetarget attach aks reference (Az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby uzyskać informacje na temat dołączania klastra AKS w portalu, zobacz [Create compute targets in Azure Machine Learning studio](how-to-create-attach-compute-studio.md#inference-clusters).

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>Tworzenie lub dołączanie klastra usługi AKS z zakończeniem działania TLS
Podczas tworzenia [lub dołączania](how-to-create-attach-kubernetes.md)klastra usługi AKS można włączyć zakończenie TLS za pomocą **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** **[i AksCompute.attach_configuration().](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** Obie metody zwracają obiekt konfiguracji, który ma **enable_ssl,** i można użyć enable_ssl **metody** do włączenia zabezpieczeń TLS.

Poniższy przykład pokazuje, jak włączyć zakończenie TLS przy użyciu automatycznego generowania i konfigurowania certyfikatów TLS przy użyciu certyfikatu firmy Microsoft.
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
Poniższy przykład pokazuje, jak włączyć zakończenie TLS przy użyciu niestandardowego certyfikatu i niestandardowej nazwy domeny. W przypadku niestandardowej domeny i certyfikatu należy zaktualizować rekord [DNS,](how-to-secure-web-service.md#update-your-dns) aby wskazać adres IP punktu końcowego oceniania. Zobacz Aktualizowanie systemu DNS

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
> Aby uzyskać więcej informacji na temat zabezpieczania wdrożenia modelu w klastrze usługi AKS, zobacz zabezpieczanie usługi internetowej za pomocą usługi [TLS za](how-to-secure-web-service.md) pośrednictwem usługi Azure Machine Learning

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>Tworzenie lub dołączanie klastra usługi AKS w celu używania adresów Load Balancer z prywatnym adresem IP
Podczas tworzenia lub dołączania klastra usługi AKS można skonfigurować klaster do używania wewnętrznego Load Balancer. W przypadku wewnętrznej Load Balancer punkty końcowe oceniania wdrożeń w u usługi AKS będą używać prywatnego adresu IP w sieci wirtualnej. Poniższe fragmenty kodu pokazują, jak skonfigurować wewnętrzną Load Balancer klastra usługi AKS.
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
> Azure Machine Learning nie obsługuje zakończenia TLS z wewnętrznymi Load Balancer. Adres Load Balancer ma prywatny adres IP, a prywatny adres IP może być w innej sieci, a certyfikat może zostać odmówiony. 

>[!NOTE]
> Aby uzyskać więcej informacji na temat zabezpieczania środowiska wnioskowania, zobacz [Secure an Azure Machine Learning Inferencing Environment](how-to-secure-inferencing-vnet.md) (Zabezpieczanie środowiska wnioskowania wnioskowania)

## <a name="detach-an-aks-cluster"></a>Odłączanie klastra usługi AKS

Aby odłączyć klaster od obszaru roboczego, użyj jednej z następujących metod:

> [!WARNING]
> Odłączenie klastra usługi AKS przy użyciu Azure Machine Learning studio, zestawu SDK lub rozszerzenia interfejsu wiersza polecenia platformy Azure w celu odłączenia klastra usługi AKS nie powoduje **usunięcia klastra usługi AKS.** Aby usunąć klaster, zobacz Use the Azure CLI with AKS (Używanie [interfejsu wiersza polecenia platformy Azure z usługą AKS).](../aks/kubernetes-walkthrough.md#delete-the-cluster)

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby odłączyć istniejący klaster od obszaru roboczego, użyj następującego polecenia. Zastąp `myaks` nazwą, jako że klaster usługi AKS jest dołączony do obszaru roboczego. Zastąp `myresourcegroup` grupę zasobów zawierającą obszar roboczy. Zastąp `myworkspace` nazwą swojego obszaru roboczego.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

W Azure Machine Learning studio wybierz __pozycję Obliczenia,__ __Klastry wnioskowania__ i klaster, który chcesz usunąć. Użyj __linku Odłącz,__ aby odłączyć klaster.

---

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="update-the-cluster"></a>Aktualizowanie klastra

Aktualizacje Azure Machine Learning zainstalowanych w klastrze Azure Kubernetes Service muszą być stosowane ręcznie. 

Te aktualizacje można zastosować, odłączając klaster od Azure Machine Learning roboczego, a następnie ponownie dołączyć klaster do obszaru roboczego. Jeśli protokół TLS jest włączony w klastrze, podczas ponownego dołączania klastra należy podać certyfikat TLS/SSL i klucz prywatny. 

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

Jeśli nie masz już certyfikatu TLS/SSL i klucza prywatnego lub używasz certyfikatu wygenerowanego przez usługę Azure Machine Learning, możesz pobrać pliki przed odłączeniu klastra, łącząc się z klastrem przy użyciu narzędzia i pobierając klucz `kubectl` tajny `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes przechowuje wpisy tajne w formacie zakodowanym w formacie Base-64. Przed dostarczeniem wpisów tajnych do usługi należy zdekodować składniki i wpisów `cert.pem` `key.pem` tajnych w `attach_config.enable_ssl` bazie-64. 

### <a name="webservice-failures"></a>Błędy usługi internetowej

Wiele błędów usługi sieci Web w u usługi AKS można debugować, łącząc się z klastrem przy użyciu polecenia `kubectl` . Klaster usługi AKS można `kubeconfig.json` uzyskać, uruchamiając

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>Następne kroki

* [Używanie kontroli RBAC platformy Azure na użytek autoryzacji platformy Kubernetes](../aks/manage-azure-rbac.md)
* [Jak i gdzie wdrożyć model](how-to-deploy-and-where.md)
* [Wdrażanie modelu w klastrze Azure Kubernetes Service klastra](how-to-deploy-azure-kubernetes-service.md)