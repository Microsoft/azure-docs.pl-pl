---
title: Bezpieczne środowiska inferencing z sieciami wirtualnymi
titleSuffix: Azure Machine Learning
description: Użyj izolowanego Virtual Network platformy Azure, aby zabezpieczyć środowisko Azure Machine Learning inferencing.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 10/23/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 5d029f4328f955d64c5ce479e553769f8a107e45
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518572"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Zabezpieczanie środowiska Azure Machine Learning inferencing z sieciami wirtualnymi

W tym artykule dowiesz się, jak zabezpieczyć środowiska inferencing przy użyciu sieci wirtualnej w Azure Machine Learning.

Ten artykuł jest czwartą częścią serii składającej się z pięciu części, która przeprowadzi Cię przez proces zabezpieczania przepływów pracy Azure Machine Learning. Zdecydowanie zalecamy zapoznanie się z [częścią poniżej: Omówienie sieci wirtualnej](how-to-network-security-overview.md) , aby zrozumieć ogólną architekturę. 

Zapoznaj się z innymi artykułami w tej serii:

[1. Omówienie sieci wirtualnej](how-to-network-security-overview.md)  >  [Zabezpiecz obszar roboczy](how-to-secure-workspace-vnet.md)  >  [3. Zabezpiecz środowisko szkoleniowe](how-to-secure-training-vnet.md)  >  **4. Zabezpiecz środowisko inferencing**  >  [5. Włącz funkcje programu Studio](how-to-enable-studio-virtual-network.md)

W tym artykule dowiesz się, jak zabezpieczyć następujące zasoby inferencing w sieci wirtualnej:
> [!div class="checklist"]
> - Domyślny klaster usługi Azure Kubernetes Service (AKS)
> - Prywatny klaster AKS
> - Klaster AKS z linkiem prywatnym
> - Azure Container Instances (ACI)

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z artykułem [Omówienie zabezpieczeń sieci](how-to-network-security-overview.md) , aby poznać typowe scenariusze sieci wirtualnej i ogólną architekturę sieci wirtualnej.

+ Istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

+ Aby można było wdrożyć zasoby w sieci wirtualnej lub podsieci, konto użytkownika musi mieć uprawnienia do następujących akcji w kontroli dostępu opartej na rolach (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/Join/Action" w zasobie sieci wirtualnej.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" w zasobie podsieci.

    Aby uzyskać więcej informacji na temat usługi Azure RBAC z obsługą sieci, zobacz [wbudowane role sieciowe](../role-based-access-control/built-in-roles.md#networking) .

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Aby można było użyć klastra AKS w sieci wirtualnej, należy spełnić następujące wymagania dotyczące sieci:

> [!div class="checklist"]
> * Postępuj zgodnie z wymaganiami wstępnymi w temacie [Konfigurowanie zaawansowanej sieci w usłudze Azure Kubernetes Service (AKS)](../aks/configure-azure-cni.md#prerequisites).
> * Wystąpienie AKS i Sieć wirtualna muszą znajdować się w tym samym regionie. W przypadku zabezpieczenia kont usługi Azure Storage używanych przez obszar roboczy w sieci wirtualnej, muszą one znajdować się w tej samej sieci wirtualnej co wystąpienie AKS.

Aby dodać AKS w sieci wirtualnej do obszaru roboczego, wykonaj następujące czynności:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/), a następnie wybierz swoją subskrypcję i obszar roboczy.

1. Wybierz pozycję __obliczenia__ po lewej stronie.

1. Wybierz z centrum pozycję __klastry wnioskowania__ , a następnie wybierz pozycję __+__ .

1. W oknie dialogowym __nowy klaster wnioskowania__ wybierz pozycję __Zaawansowane__ w obszarze __Konfiguracja sieci__.

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności:

    1. Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.
    1. Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną, która zawiera podsieć.
    1. Z listy rozwijanej __podsieć__ wybierz podsieć.
    1. W polu __zakres adresów usługi Kubernetes__ Wprowadź zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP notacji CIDR (Classless Inter-Domain Routing), aby zdefiniować adresy IP, które są dostępne dla klastra. Nie może się nakładać na żadne zakresy adresów IP podsieci (na przykład 10.0.0.0/16).
    1. W polu __adres IP usługi KUBERNETES DNS__ wprowadź adres IP usługi DNS Kubernetes. Ten adres IP jest przypisywany do usługi DNS Kubernetes. Musi ona należeć do zakresu adresów usługi Kubernetes (na przykład 10.0.0.10).
    1. W polu __adres mostka platformy Docker__ wprowadź adres mostka platformy Docker. Ten adres IP jest przypisany do mostka platformy Docker. Nie może być w żadnym z zakresów adresów IP podsieci lub zakres adresów usługi Kubernetes (na przykład 172.17.0.1/16).

   ![Azure Machine Learning: środowisko obliczeniowe usługi Machine Learning ustawień sieci wirtualnej](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. W przypadku wdrażania modelu jako usługi sieci Web AKS punkt końcowy oceniania jest tworzony w celu obsługi żądań inferencing. Upewnij się, że grupa sieciowej grupy zabezpieczeń kontrolująca sieć wirtualną ma włączoną regułę zabezpieczeń dla ruchu przychodzącego dla adresu IP punktu końcowego oceniania, jeśli chcesz wywołać go spoza sieci wirtualnej.

    Aby znaleźć adres IP punktu końcowego oceniania, należy zapoznać się z identyfikatorem URI oceny wdrożonej usługi. Aby uzyskać informacje na temat wyświetlania identyfikatora URI oceniania, zobacz temat [Korzystanie z modelu wdrożonego jako usługa sieci Web](how-to-consume-web-service.md#connection-information).

   > [!IMPORTANT]
   > Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](../virtual-network/network-security-groups-overview.md#default-security-rules).

   [![Reguła zabezpieczeń dla ruchu przychodzącego](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > Adres IP wyświetlany w obrazie punktu końcowego oceniania będzie różny dla wdrożeń. Chociaż ten sam adres IP jest współużytkowany przez wszystkie wdrożenia do jednego klastra AKS, każdy klaster AKS będzie miał inny adres IP.

Możesz również użyć zestawu SDK Azure Machine Learning, aby dodać usługę Azure Kubernetes w sieci wirtualnej. Jeśli masz już klaster AKS w sieci wirtualnej, dołącz go do obszaru roboczego, zgodnie z opisem w artykule [wdrażanie w AKS](how-to-deploy-and-where.md). Poniższy kod tworzy nowe wystąpienie AKS w `default` podsieci sieci wirtualnej o nazwie `mynetwork` :

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

Po zakończeniu procesu tworzenia można uruchomić wnioskowanie lub ocenianie modelu w klastrze AKS za siecią wirtualną. Aby uzyskać więcej informacji, zobacz [How to Deploy to AKS](how-to-deploy-and-where.md).

Aby uzyskać więcej informacji na temat korzystania z Role-Based Access Control z usługą Kubernetes, zobacz [Korzystanie z usługi Azure RBAC na potrzeby autoryzacji Kubernetes](../aks/manage-azure-rbac.md).

## <a name="network-contributor-role"></a>Rola współautor sieci

> [!IMPORTANT]
> Jeśli utworzysz lub dołączysz klaster AKS, dostarczając wcześniej utworzoną sieć wirtualną, należy przyznać jednostce usługi (SP) lub tożsamość zarządzaną dla klastra AKS rolę _współautor sieci_ do grupy zasobów zawierającej sieć wirtualną.
>
> Aby dodać tożsamość jako współautor sieci, wykonaj następujące czynności:

1. Aby znaleźć nazwę główną usługi lub identyfikator tożsamości zarządzanej dla AKS, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure. Zamień `<aks-cluster-name>` na nazwę klastra. Zamień `<resource-group-name>` na nazwę grupy zasobów zawierającej _klaster AKS_:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Jeśli to polecenie zwróci wartość `msi` , użyj następującego polecenia, aby zidentyfikować Identyfikator podmiotu zabezpieczeń dla tożsamości zarządzanej:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Aby znaleźć identyfikator grupy zasobów zawierającej daną sieć wirtualną, użyj następującego polecenia. Zamień `<resource-group-name>` na nazwę grupy zasobów zawierającej _sieć wirtualną_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Aby dodać nazwę główną usługi lub tożsamość zarządzaną jako współautor sieci, użyj następującego polecenia. Zamień na `<SP-or-managed-identity>` Identyfikator zwrócony dla jednostki usługi lub tożsamości zarządzanej. Zamień na `<resource-group-id>` Identyfikator zwrócony dla grupy zasobów zawierającej sieć wirtualną:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Aby uzyskać więcej informacji na temat używania wewnętrznego modułu równoważenia obciążenia z programem AKS, zobacz [Korzystanie z wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service](../aks/internal-lb.md).

## <a name="secure-vnet-traffic"></a>Bezpieczny ruch sieci wirtualnej

Istnieją dwa podejścia do izolowania ruchu do i z klastra AKS do sieci wirtualnej:

* __Prywatny klaster AKS__: to podejście używa prywatnego linku platformy Azure do zabezpieczania komunikacji z klastrem na potrzeby operacji wdrażania i zarządzania.
* __Wewnętrzny moduł równoważenia obciążenia AKS__: to podejście służy do konfigurowania punktu końcowego dla wdrożeń do AKS w celu korzystania z prywatnego adresu IP w ramach sieci wirtualnej.

> [!WARNING]
> Wewnętrzny moduł równoważenia obciążenia nie działa z klastrem AKS, który korzysta z korzystającą wtyczki kubenet. Jeśli chcesz użyć wewnętrznego modułu równoważenia obciążenia i prywatnego klastra AKS w tym samym czasie, skonfiguruj prywatny klaster AKS za pomocą interfejsu Azure Container Network Interface (CNI). Aby uzyskać więcej informacji, zobacz [Konfigurowanie sieci Azure CNI w usłudze Azure Kubernetes Service](../aks/configure-azure-cni.md).

### <a name="private-aks-cluster"></a>Prywatny klaster AKS

Domyślnie klastry AKS mają płaszczyznę kontroli lub serwer interfejsu API z publicznymi adresami IP. Można skonfigurować AKS do korzystania z prywatnej płaszczyzny kontroli, tworząc prywatny klaster AKS. Aby uzyskać więcej informacji, zobacz [Tworzenie prywatnego klastra usługi Azure Kubernetes Service](../aks/private-clusters.md).

Po utworzeniu prywatnego klastra AKS [Dołącz klaster do sieci wirtualnej](how-to-create-attach-kubernetes.md) , aby używać go z Azure Machine Learning.

> [!IMPORTANT]
> Przed użyciem linku prywatnego z włączonym klastrem AKS z Azure Machine Learning należy otworzyć zdarzenie obsługi, aby włączyć tę funkcję. Aby uzyskać więcej informacji, zobacz [Zarządzanie i zwiększanie limitów przydziału](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

### <a name="internal-aks-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia AKS

Domyślnie wdrożenia AKS używają [publicznego modułu równoważenia obciążenia](../aks/load-balancer-standard.md). W tej sekcji dowiesz się, jak skonfigurować AKS do korzystania z wewnętrznego modułu równoważenia obciążenia. Używany jest wewnętrzny (lub prywatny) moduł równoważenia obciążenia, w przypadku którego jako frontonu można używać tylko prywatnych adresów IP. Wewnętrzne moduły równoważenia obciążenia są używane do równoważenia obciążenia ruchu w sieci wirtualnej

Prywatny moduł równoważenia obciążenia jest włączony przez skonfigurowanie AKS do korzystania z _wewnętrznego modułu równoważenia obciążenia_. 

#### <a name="enable-private-load-balancer"></a>Włączanie prywatnego modułu równoważenia obciążenia

> [!IMPORTANT]
> Nie można włączyć prywatnego adresu IP podczas tworzenia klastra usługi Azure Kubernetes w programie Azure Machine Learning Studio. Możesz utworzyć jeden z wewnętrznym modułem równoważenia obciążenia, korzystając z zestawu SDK języka Python lub rozszerzenia interfejsu wiersza polecenia platformy Azure do uczenia maszynowego.

W poniższych przykładach pokazano, jak __utworzyć nowy klaster AKS z prywatnym adresem IP/wewnętrznym modułem równoważenia obciążenia__ za pomocą zestawu SDK i interfejsu wiersza polecenia:

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
> Korzystając z interfejsu wiersza polecenia, można utworzyć klaster AKS tylko z wewnętrznym modułem równoważenia obciążenia. Nie ma polecenia AZ ml, aby uaktualnić istniejący klaster do korzystania z wewnętrznego modułu równoważenia obciążenia.

Aby uzyskać więcej informacji, zobacz [AZ ml computetarget Create AKS](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

---

W przypadku __dołączania istniejącego klastra__ do obszaru roboczego należy poczekać, aż po zakończeniu operacji dołączania usługa równoważenia obciążenia zostanie skonfigurowana. Aby uzyskać informacje na temat dołączania klastra, zobacz [dołączanie istniejącego klastra AKS](how-to-create-attach-kubernetes.md).

Po dołączeniu istniejącego klastra można zaktualizować klaster tak, aby korzystał z wewnętrznego modułu równoważenia obciążenia/prywatnego adresu IP:

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

## <a name="enable-azure-container-instances-aci"></a>Włącz Azure Container Instances (ACI)

Azure Container Instances są tworzone dynamicznie podczas wdrażania modelu. Aby umożliwić Azure Machine Learning tworzenia ACI wewnątrz sieci wirtualnej, należy włączyć __delegowanie podsieci__ dla podsieci używanej przez wdrożenie.

> [!WARNING]
> W przypadku korzystania z Azure Container Instances w sieci wirtualnej Sieć wirtualna musi być:
> * W tej samej grupie zasobów co obszar roboczy Azure Machine Learning.
> * Jeśli obszar roboczy ma __prywatny punkt końcowy__, Sieć wirtualna używana na potrzeby Azure Container Instances musi być taka sama jak używana przez prywatny punkt końcowy obszaru roboczego.
>
> W przypadku korzystania z Azure Container Instances wewnątrz sieci wirtualnej Azure Container Registry (ACR) obszaru roboczego nie może znajdować się w sieci wirtualnej.

Aby użyć ACI w sieci wirtualnej z obszarem roboczym, wykonaj następujące czynności:

1. Aby włączyć delegowanie podsieci w sieci wirtualnej, Skorzystaj z informacji zawartych w artykule [Dodawanie lub usuwanie delegowania podsieci](../virtual-network/manage-subnet-delegation.md) . Delegowanie można włączyć podczas tworzenia sieci wirtualnej lub dodać je do istniejącej sieci.

    > [!IMPORTANT]
    > Podczas włączania delegowania Użyj `Microsoft.ContainerInstance/containerGroups` wartości jako __delegowanej podsieci do usługi__ .

2. Wdróż model przy użyciu [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), użyj `vnet_name` parametrów i `subnet_name` . Ustaw te parametry na nazwę sieci wirtualnej i podsieć, w której włączono delegowanie.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>Ograniczanie łączności wychodzącej z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, musisz zezwolić na dostęp do Azure Container Registry. Na przykład upewnij się, że sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń) zawierają regułę umożliwiającą dostęp do tagu usługi __AzureContainerRegistry. RegionName__ , gdzie "{RegionName}" jest nazwą regionu platformy Azure.

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest czwartą częścią serii sieci wirtualnych z pięcioma częściami. Zapoznaj się z pozostałymi artykułami, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 1: Omówienie usługi Virtual Network](how-to-network-security-overview.md)
* [Część 2: Zabezpieczanie zasobów obszaru roboczego](how-to-secure-workspace-vnet.md)
* [Część 3: Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)
* [Część 5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)