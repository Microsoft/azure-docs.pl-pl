---
title: Bezpieczne środowiska wnioskowania z sieciami wirtualnymi
titleSuffix: Azure Machine Learning
description: Użyj izolowanej usługi Azure Virtual Network, aby zabezpieczyć Azure Machine Learning środowiska wnioskowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 610ab82bfc4665fbb30aa3d3bc0448fa9338689c
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872555"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Zabezpieczanie Azure Machine Learning wnioskowania za pomocą sieci wirtualnych

Z tego artykułu dowiesz się, jak zabezpieczyć środowiska wnioskowania za pomocą sieci wirtualnej w Azure Machine Learning.

Ten artykuł jest czwartym z pięciu części serii, który zawiera 5-częściowe informacje na temat zabezpieczania przepływu Azure Machine Learning przepływu pracy. Zdecydowanie zalecamy przeczytanie części [pierwszej:](how-to-network-security-overview.md) omówienie sieci wirtualnej, aby najpierw zrozumieć ogólną architekturę. 

Zobacz inne artykuły w tej serii:

[1. Omówienie sieci wirtualnej](how-to-network-security-overview.md)  >  [Zabezpiecz obszar roboczy](how-to-secure-workspace-vnet.md)  >  [3. Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)  >  **4. Zabezpieczanie środowiska wnioskowania**  >  [5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)

W tym artykule opisano sposób zabezpieczania następujących zasobów wnioskowania w sieci wirtualnej:
> [!div class="checklist"]
> - Domyślny Azure Kubernetes Service klastra usługi (AKS)
> - Prywatny klaster AKS
> - Klaster AKS z łączem prywatnym
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Wymagania wstępne

+ Przeczytaj artykuł [Omówienie zabezpieczeń sieci,](how-to-network-security-overview.md) aby poznać typowe scenariusze sieci wirtualnej i ogólną architekturę sieci wirtualnej.

+ Istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

+ Aby wdrożyć zasoby w sieci wirtualnej lub podsieci, konto użytkownika musi mieć uprawnienia do następujących akcji w ramach kontroli dostępu na podstawie ról (RBAC) platformy Azure:

    - "Microsoft.Network/virtualNetworks/join/action" w zasobie sieci wirtualnej.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" w zasobie podsieci.

    Aby uzyskać więcej informacji na temat kontroli RBAC platformy Azure z siecią, zobacz [Wbudowane role sieci](../role-based-access-control/built-in-roles.md#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Aby można było korzystać z klastra usługi AKS w sieci wirtualnej, muszą zostać spełnione następujące wymagania dotyczące sieci:

> [!div class="checklist"]
> * Postępuj zgodnie z wymaganiami wstępnymi z tematu Konfigurowanie sieci [zaawansowanej w Azure Kubernetes Service (AKS).](../aks/configure-azure-cni.md#prerequisites)
> * Wystąpienie usługi AKS i sieć wirtualna muszą znajdować się w tym samym regionie. Jeśli zabezpieczasz konta usługi Azure Storage używane przez obszar roboczy w sieci wirtualnej, muszą one również znajdować się w tej samej sieci wirtualnej co wystąpienie usługi AKS.

Aby dodać usługę AKS w sieci wirtualnej do obszaru roboczego, należy wykonać następujące czynności:

1. Zaloguj się do [Azure Machine Learning studio](https://ml.azure.com/), a następnie wybierz swoją subskrypcję i obszar roboczy.

1. Wybierz __pozycję Obliczenia__ po lewej stronie.

1. Wybierz __pozycję Klastry wnioskowania__ w środku, a następnie wybierz pozycję __+__ .

1. W __oknie dialogowym Nowy klaster wnioskowania__ wybierz pozycję __Zaawansowane w__ __obszarze Konfiguracja sieci.__

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności:

    1. Z __listy rozwijanej__ Grupa zasobów wybierz grupę zasobów zawierającą sieć wirtualną.
    1. Z __listy rozwijanej__ Sieć wirtualna wybierz sieć wirtualną zawierającą podsieć.
    1. Z __listy rozwijanej__ Podsieć wybierz podsieć.
    1. W polu __Zakres adresów usługi Kubernetes Service__ wprowadź zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP Inter-Domain routingu bez klas (CIDR) do definiowania adresów IP dostępnych dla klastra. Nie może pokrywać się z żadnymi zakresami adresów IP podsieci (na przykład 10.0.0.0/16).
    1. W polu __Adres IP usługi DNS kubernetes__ wprowadź adres IP usługi DNS kubernetes. Ten adres IP jest przypisywany do usługi DNS Kubernetes. Musi ona znajdować się w zakresie adresów usługi Kubernetes (na przykład 10.0.0.10).
    1. W polu __Adres mostka platformy Docker__ wprowadź adres mostka platformy Docker. Ten adres IP jest przypisany do mostka platformy Docker. Nie może znajdować się w żadnym zakresie adresów IP podsieci ani w zakresie adresów usługi Kubernetes (na przykład 172.17.0.1/16).

   ![Azure Machine Learning: środowisko obliczeniowe usługi Machine Learning ustawień sieci wirtualnej](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Podczas wdrażania modelu jako usługi internetowej w usłudze AKS jest tworzony punkt końcowy oceniania w celu obsługi żądań wnioskowania. Upewnij się, że grupa sieciowej grupy zabezpieczeń, która kontroluje sieć wirtualną, ma włączoną regułę zabezpieczeń dla ruchu przychodzącego dla adresu IP punktu końcowego oceniania, jeśli chcesz ją wywołać spoza sieci wirtualnej.

    Aby znaleźć adres IP punktu końcowego oceniania, zobacz scoring URI for the deployed service (Adres URI oceniania dla wdrożonej usługi). Aby uzyskać informacje na temat wyświetlania URI oceniania, zobacz Consume a model deployed as a web service (Wykorzystanie [modelu wdrożonego jako usługa internetowa).](how-to-consume-web-service.md#connection-information)

   > [!IMPORTANT]
   > Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy bezpieczeństwa. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [tece Grupy zabezpieczeń.](../virtual-network/network-security-groups-overview.md#default-security-rules)

   [![Reguła zabezpieczeń dla ruchu przychodzącego](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > Adres IP wyświetlany na obrazie punktu końcowego oceniania będzie inny dla wdrożeń. Chociaż ten sam adres IP jest współużytkowany przez wszystkie wdrożenia w jednym klastrze usługi AKS, każdy klaster usługi AKS będzie miał inny adres IP.

Możesz również użyć zestawu SDK Azure Machine Learning, aby dodać Azure Kubernetes Service w sieci wirtualnej. Jeśli masz już klaster usługi AKS w sieci wirtualnej, dołącz go do obszaru roboczego zgodnie z opisem w teksie How to deploy to AKS (Jak [wdrożyć do usługi AKS).](how-to-deploy-and-where.md) Poniższy kod tworzy nowe wystąpienie usługi AKS w `default` podsieci sieci wirtualnej o nazwie `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Po zakończeniu procesu tworzenia można uruchomić wnioskowanie lub ocenianie modelu w klastrze usługi AKS za siecią wirtualną. Aby uzyskać więcej informacji, zobacz [Jak wdrożyć usługę AKS.](how-to-deploy-and-where.md)

Aby uzyskać więcej informacji na temat używania Role-Based Access Control z platformą Kubernetes, zobacz Use Azure RBAC for Kubernetes authorization (Używanie kontroli dostępu na podstawie ról na platformie Azure do [autoryzacji platformy Kubernetes).](../aks/manage-azure-rbac.md)

## <a name="network-contributor-role"></a>Rola współautora sieci

> [!IMPORTANT]
> Jeśli tworzysz lub dołączasz klaster usługi AKS, podając wcześniej utworzoną sieć wirtualną, musisz przyznać  roli Współautor sieci grupie zasobów zawierającej sieć wirtualną dla jednostki usługi lub tożsamości zarządzanej dla klastra usługi AKS rolę Współautor sieci.
>
> Aby dodać tożsamość jako współautora sieci, należy wykonać następujące czynności:

1. Aby znaleźć jednostkę usługi lub identyfikator tożsamości zarządzanej dla usługi AKS, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure. Zastąp `<aks-cluster-name>` nazwą klastra. Zastąp `<resource-group-name>` nazwą grupy zasobów, która zawiera _klaster usługi AKS:_

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Jeśli to polecenie zwraca wartość , użyj następującego polecenia, aby zidentyfikować identyfikator podmiotu zabezpieczeń `msi` tożsamości zarządzanej:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Aby znaleźć identyfikator grupy zasobów zawierającej sieć wirtualną, użyj następującego polecenia. Zastąp `<resource-group-name>` nazwą grupy zasobów zawierającej sieć _wirtualną_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Aby dodać jednostkę usługi lub tożsamość zarządzaną jako współautora sieci, użyj następującego polecenia. Zastąp `<SP-or-managed-identity>` identyfikatorem zwróconym dla jednostki usługi lub tożsamości zarządzanej. Zastąp `<resource-group-id>` identyfikatorem zwróconym dla grupy zasobów zawierającej sieć wirtualną:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Aby uzyskać więcej informacji na temat używania wewnętrznego równoważenia obciążenia z usługą AKS, zobacz Use internal load balancer with Azure Kubernetes Service (Używanie wewnętrznego równoważenia obciążenia z [usługą Azure Kubernetes Service).](../aks/internal-lb.md)

## <a name="secure-vnet-traffic"></a>Zabezpieczanie ruchu sieci wirtualnej

Istnieją dwa podejścia do izolowania ruchu do i z klastra AKS do sieci wirtualnej:

* __Prywatny klaster AKS:__ to podejście używa Azure Private Link do zabezpieczania komunikacji z klastrem na czas operacji wdrażania/zarządzania.
* __Wewnętrzny równoważenie obciążenia usługi AKS:__ to podejście umożliwia skonfigurowanie punktu końcowego dla wdrożeń usługi AKS w celu używania prywatnego adresu IP w sieci wirtualnej.

> [!WARNING]
> Wewnętrzny równoważenie obciążenia nie działa z klastrem usługi AKS, który korzysta z usługi kubenet. Jeśli chcesz jednocześnie używać wewnętrznego i prywatnego klastra usługi AKS, skonfiguruj prywatny klaster usługi AKS przy użyciu Azure Container Networking Interface (CNI). Aby uzyskać więcej informacji, zobacz [Configure Azure CNI networking in Azure Kubernetes Service (Konfigurowanie](../aks/configure-azure-cni.md)sieci Azure Kubernetes Service ).

### <a name="private-aks-cluster"></a>Prywatny klaster usługi AKS

Domyślnie klastry usługi AKS mają płaszczyznę sterowania lub serwer interfejsu API z publicznymi adresami IP. Usługę AKS można skonfigurować do używania prywatnej płaszczyzny sterowania, tworząc prywatny klaster usługi AKS. Aby uzyskać więcej informacji, zobacz [Create a private Azure Kubernetes Service cluster (Tworzenie klastra Azure Kubernetes Service prywatnego).](../aks/private-clusters.md)

Po utworzeniu prywatnego klastra usługi AKS dołącz [klaster](how-to-create-attach-kubernetes.md) do sieci wirtualnej, która ma być Azure Machine Learning.

> [!IMPORTANT]
> Przed użyciem klastra usługi AKS z włączonym łączem prywatnym Azure Machine Learning należy otworzyć zdarzenie pomocy technicznej, aby włączyć tę funkcję. Aby uzyskać więcej informacji, zobacz [Zarządzanie przydziałami i ich zwiększanie.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

### <a name="internal-aks-load-balancer"></a>Wewnętrzny równoważenie obciążenia AKS

Domyślnie wdrożenia usługi AKS używają publicznego [równoważenia obciążenia.](../aks/load-balancer-standard.md) W tej sekcji dowiesz się, jak skonfigurować usługę AKS do korzystania z wewnętrznego równoważenia obciążenia. Używany jest wewnętrzny (lub prywatny) równoważenie obciążenia, w którym jako frontend są dozwolone tylko prywatne ip. Wewnętrzne usługi równoważenia obciążenia służą do równoważenia obciążenia ruchem wewnątrz sieci wirtualnej

Prywatny równoważenie obciążenia jest włączany przez skonfigurowanie usługi AKS do korzystania z _wewnętrznego równoważenia obciążenia._ 

#### <a name="enable-private-load-balancer"></a>Włączanie prywatnego równoważenia obciążenia

> [!IMPORTANT]
> Nie można włączyć prywatnego adresu IP podczas tworzenia Azure Kubernetes Service klastra w Azure Machine Learning studio. Możesz go utworzyć za pomocą wewnętrznego równoważenia obciążenia, korzystając z zestawu SDK dla języka Python lub rozszerzenia interfejsu wiersza polecenia platformy Azure do uczenia maszynowego.

W poniższych przykładach pokazano, jak utworzyć nowy __klaster usługi AKS__ z prywatnym adresem IP/wewnętrznym usługą równoważenia obciążenia przy użyciu zestawu SDK i interfejsu wiersza polecenia:

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

> [!IMPORTANT]
> Za pomocą interfejsu wiersza polecenia można utworzyć klaster usługi AKS tylko za pomocą wewnętrznego równoważenia obciążenia. Nie ma polecenia az ml służącego do uaktualniania istniejącego klastra w celu korzystania z wewnętrznego równoważenia obciążenia.

Aby uzyskać więcej informacji, zobacz [az ml computetarget create aks reference (Az ml computetarget create aks](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_aks) reference).

---

Podczas __dołączania istniejącego klastra do__ obszaru roboczego musisz poczekać, aż po operacji dołączania skonfigurujesz usługę równoważenia obciążenia. Aby uzyskać informacje na temat dołączania klastra, zobacz [Dołączanie istniejącego klastra usługi AKS.](how-to-create-attach-kubernetes.md)

Po dołączeniu istniejącego klastra można zaktualizować klaster, aby używać wewnętrznego usługi równoważenia obciążenia/prywatnego adresu IP:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Włączanie Azure Container Instances (ACI)

Azure Container Instances są tworzone dynamicznie podczas wdrażania modelu. Aby umożliwić Azure Machine Learning ACI w sieci wirtualnej, należy  włączyć delegowanie podsieci dla podsieci używanej przez wdrożenie.

> [!WARNING]
> W przypadku Azure Container Instances w sieci wirtualnej sieć wirtualna musi być:
> * W tej samej grupie zasobów co Azure Machine Learning obszaru roboczego.
> * Jeśli obszar roboczy ma prywatny __punkt końcowy__, sieć wirtualna używana na Azure Container Instances musi być taka sama jak sieć używana przez prywatny punkt końcowy obszaru roboczego.
>
> W przypadku Azure Container Instances sieci wirtualnej Azure Container Registry (ACR) dla obszaru roboczego nie może być w sieci wirtualnej.

Aby użyć usługi ACI w sieci wirtualnej w obszarze roboczym, należy wykonać następujące kroki:

1. Aby włączyć delegowanie podsieci w sieci wirtualnej, skorzystaj z informacji w artykule Dodawanie lub usuwanie [delegowania podsieci.](../virtual-network/manage-subnet-delegation.md) Delegowanie można włączyć podczas tworzenia sieci wirtualnej lub dodać do istniejącej sieci.

    > [!IMPORTANT]
    > Podczas włączania delegowania użyj `Microsoft.ContainerInstance/containerGroups` wartości jako __delegowania podsieci do__ usługi.

2. Wdrożenie modelu przy [użyciu AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), użycie parametrów `vnet_name` i `subnet_name` . Ustaw te parametry na nazwę sieci wirtualnej i podsieć, w której włączono delegowanie.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>Ograniczanie łączności wychodzącej z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, musisz zezwolić na dostęp do Azure Container Registry. Na przykład upewnij się, że sieciowe grupy zabezpieczeń zawierają regułę, która zezwala na dostęp do tagu usługi __AzureContainerRegistry.RegionName,__ gdzie "{RegionName} jest nazwą regionu platformy Azure.

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest częścią 4 z pięciu części serii sieci wirtualnych. Zobacz pozostałe artykuły, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 1. Omówienie sieci wirtualnej](how-to-network-security-overview.md)
* [Część 2. Zabezpieczanie zasobów obszaru roboczego](how-to-secure-workspace-vnet.md)
* [Część 3. Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)
* [Część 5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)

Zobacz również artykuł na temat używania [niestandardowego systemu DNS](how-to-custom-dns.md) do rozpoznawania nazw.