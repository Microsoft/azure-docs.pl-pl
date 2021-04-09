---
title: Bezpieczne środowiska szkoleniowe z sieciami wirtualnymi
titleSuffix: Azure Machine Learning
description: Aby zabezpieczyć środowisko szkoleniowe Azure Machine Learning, użyj izolowanego Virtual Network platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 81a6a6aeb826ba4a61fd4f0a4abdb63fe3674bc4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573731"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Zabezpiecz środowisko szkoleniowe Azure Machine Learning z sieciami wirtualnymi

W tym artykule dowiesz się, jak zabezpieczyć środowiska szkoleniowe za pomocą sieci wirtualnej w Azure Machine Learning.

Ten artykuł jest trzecią częścią serii składającej się z pięciu części, która przeprowadzi Cię przez proces zabezpieczania przepływów pracy Azure Machine Learning. Zdecydowanie zalecamy zapoznanie się z [częścią poniżej: Omówienie sieci wirtualnej](how-to-network-security-overview.md) , aby zrozumieć ogólną architekturę. 

Zapoznaj się z innymi artykułami w tej serii:

[1. Sieć wirtualna — Omówienie](how-to-network-security-overview.md)  >  [2. Zabezpiecz obszar roboczy](how-to-secure-workspace-vnet.md)  >  **3. Zabezpiecz środowisko szkoleniowe**  >  [4. Zabezpiecz środowisko inferencing](how-to-secure-inferencing-vnet.md)   >  [5. Włącz funkcje programu Studio](how-to-enable-studio-virtual-network.md)

W tym artykule opisano sposób zabezpieczania następujących zasobów obliczeniowych szkoleniowych w sieci wirtualnej:
> [!div class="checklist"]
> - Azure Machine Learning klaster obliczeniowy
> - Wystąpienie obliczeniowe usługi Azure Machine Learning
> - Azure Databricks
> - Maszyna wirtualna
> - Klaster usługi HDInsight

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z artykułem [Omówienie zabezpieczeń sieci](how-to-network-security-overview.md) , aby poznać typowe scenariusze sieci wirtualnej i ogólną architekturę sieci wirtualnej.

+ Istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

+ Aby można było wdrożyć zasoby w sieci wirtualnej lub podsieci, konto użytkownika musi mieć uprawnienia do następujących akcji w kontroli dostępu opartej na rolach (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/Join/Action" w zasobie sieci wirtualnej.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" w zasobie podsieci.

    Aby uzyskać więcej informacji na temat usługi Azure RBAC z obsługą sieci, zobacz [wbudowane role sieciowe](../role-based-access-control/built-in-roles.md#networking) .


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Wystąpienia klastrów obliczeniowych & 

Aby można było użyć [zarządzanego __obiektu docelowego obliczeń__ Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) lub [ __wystąpienia__ obliczeniowego Azure Machine Learning](concept-compute-instance.md) w sieci wirtualnej, należy spełnić następujące wymagania dotyczące sieci:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
> * Podsieć określona dla wystąpienia obliczeniowego lub klastra musi mieć wystarczającą liczbę nieprzypisanych adresów IP w celu uwzględnienia liczby elementów, które są przeznaczone dla maszyny wirtualnej. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster obliczeniowy zostanie częściowo przydzielony.
> * Sprawdź, czy zasady zabezpieczeń lub blokady w ramach subskrypcji lub grupy zasobów sieci wirtualnej ograniczają uprawnienia do zarządzania siecią wirtualną. Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu, pozostaw kilka otwartych portów dla usługi obliczeniowej. Aby uzyskać więcej informacji, zobacz sekcję [wymagane porty](#mlcports) .
> * Jeśli chcesz umieścić wiele wystąpień obliczeniowych lub klastrów w jednej sieci wirtualnej, może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z zasobów.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co Azure Machine Learning wystąpienie obliczeniowe lub klaster. 
> * Aby funkcja Jupyter wystąpienia obliczeniowego działała, należy się upewnić, że komunikacja gniazda internetowego nie jest wyłączona. Upewnij się, że sieć zezwala na połączenia protokołu WebSocket z *. instances.azureml.net i *. instances.azureml.ms. 
> * Gdy wystąpienie obliczeniowe zostanie wdrożone w prywatnym obszarze roboczym łącza, można uzyskać do niego dostęp tylko z poziomu sieci wirtualnej. Jeśli używasz niestandardowego pliku DNS lub hosta, Dodaj wpis dla prywatnego `<instance-name>.<region>.instances.azureml.ms` adresu IP obszaru roboczego prywatnego punktu końcowego. Aby uzyskać więcej informacji, zobacz artykuł [niestandardowy DNS](./how-to-custom-dns.md) .
> * Podsieć użyta do wdrożenia klastra/wystąpienia obliczeniowego nie powinna być delegowana do żadnej innej usługi, takiej jak ACI
> * Zasady punktu końcowego usługi sieci wirtualnej nie działają w przypadku kont magazynu klastra obliczeniowego/wystąpienia systemu

    
> [!TIP]
> Wystąpienie obliczeniowe Machine Learning lub klaster automatycznie przydziela dodatkowe zasoby sieciowe __do grupy zasobów zawierającej sieć wirtualną__. Dla każdego wystąpienia obliczeniowego lub klastra usługa przydziela następujące zasoby:
> 
> * Jedna sieciowa Grupa zabezpieczeń
> * Jeden publiczny adres IP. Jeśli zasady platformy Azure zabraniają tworzenia publicznego adresu IP, wdrożenie klastra/wystąpień zakończy się niepowodzeniem
> * Jeden moduł równoważenia obciążenia
> 
> W przypadku klastrów te zasoby są usuwane (i tworzone ponownie) za każdym razem, gdy klaster jest skalowany w dół do 0 węzłów, jednak w przypadku wystąpienia, w którym znajdują się zasoby, do momentu całkowitego usunięcia wystąpienia. zatrzymywanie nie powoduje usunięcia zasobów. 
> Te zasoby są ograniczone przez [limity zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md) subskrypcji. Jeśli grupa zasobów sieci wirtualnej jest zablokowana, usunięcie klastra i wystąpienia obliczeniowego zakończy się niepowodzeniem. Nie można usunąć modułu równoważenia obciążenia, dopóki klaster obliczeniowy/wystąpienie nie zostanie usunięte. Upewnij się również, że nie ma żadnych zasad platformy Azure, które uniemożliwiają tworzenie sieciowych grup zabezpieczeń.


### <a name="required-ports"></a><a id="mlcports"></a> Wymagane porty

Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu sieciowego do/z publicznej sieci Internet, musisz zezwolić na komunikację przychodzącą z usługi Azure Batch.

Usługa Batch dodaje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) na poziomie interfejsów sieciowych dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Ruch przychodzący TCP na portach 29876 i 29877 z __tagu usługi__ __BatchNodeManagement__. Ruch na tych portach jest szyfrowany i używany przez Azure Batch na potrzeby komunikacji między harmonogramem i węzłem.

    ![Reguła przychodząca korzystająca z tagu usługi BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Obowiązkowe Ruch przychodzący TCP na porcie 22, aby zezwolić na dostęp zdalny. Tego portu należy używać tylko w przypadku, gdy chcesz nawiązać połączenie przy użyciu protokołu SSH w publicznym adresie IP.

- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu.

- Dla ruchu przychodzącego TCP wystąpienia obliczeniowego na porcie 44224 z __tagu usługi__ __AzureMachineLearning__. Ruch przez ten port jest szyfrowany i używany przez Azure Machine Learning do komunikacji z aplikacjami uruchomionymi w wystąpieniach obliczeniowych.

> [!IMPORTANT]
> Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli sieciowej grupy zabezpieczeń blokuje komunikację z węzłami obliczeniowymi, usługa COMPUTE ustawia stan węzłów obliczeniowych na niezdatny do użytku.
>
> Nie musisz określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Azure Batch konfiguruje własny sieciowych grup zabezpieczeń. Jeśli jednak podsieć zawierająca Azure Machine Learning COMPUTE ma skojarzone sieciowych grup zabezpieczeń lub zaporę, należy również zezwolić na wymieniony wcześniej ruch.

Konfiguracja reguły sieciowej grupy zabezpieczeń w Azure Portal przedstawiono na następujących obrazach:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla środowisko obliczeniowe usługi Machine Learning" border="true":::



![Reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> Ogranicz łączność wychodzącą z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, wykonaj następujące czynności:

- Odmowa wychodzącego połączenia internetowego przy użyciu reguł sieciowej grupy zabezpieczeń.

- W przypadku __wystąpienia obliczeniowego__ lub __klastra obliczeniowego__ należy ograniczyć ruch wychodzący do następujących elementów:
   - Azure Storage, przy użyciu __znacznika usługi__ __Storage. RegionName__. Gdzie `{RegionName}` jest nazwą regionu świadczenia usługi Azure.
   - Azure Container Registry, przy użyciu __znacznika usługi__ __AzureContainerRegistry. RegionName__. Gdzie `{RegionName}` jest nazwą regionu świadczenia usługi Azure.
   - Azure Machine Learning, przy użyciu __tagu usługi__ __AzureMachineLearning__
   - Azure Resource Manager, przy użyciu __tagu usługi__ __AzureResourceManager__
   - Azure Active Directory, przy użyciu __tagu usługi__ __usługi azureactivedirectory__

Konfiguracja reguły sieciowej grupy zabezpieczeń w Azure Portal jest pokazana na poniższym obrazie:

[![Wychodzące reguły sieciowej grupy zabezpieczeń dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Jeśli planujesz użycie domyślnych obrazów platformy Docker dostarczonych przez firmę Microsoft i włączenie zależności zarządzanych przez użytkownika, należy również użyć następujących __tagów usługi__:
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Ta konfiguracja jest konieczna, gdy kod jest podobny do następującego fragmentu kodu w ramach skryptów szkoleniowych:
>
> __Szkolenia RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Szkolenia szacowania__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Jeśli korzystasz z [wymuszonego tunelowania](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) przy użyciu Azure Machine Learning COMPUTE, musisz zezwolić na komunikację z publiczną Internetem z podsieci zawierającej zasób obliczeniowy. Ta komunikacja służy do planowania zadań i uzyskiwania dostępu do usługi Azure Storage.

Można to zrobić na dwa sposoby:

* Użyj [Virtual Network translatora adresów sieciowych](../virtual-network/nat-overview.md). Brama NAT zapewnia wychodzące połączenie z Internetem dla co najmniej jednej podsieci w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [projektowanie sieci wirtualnych z użyciem zasobów bramy translatora adresów sieciowych](../virtual-network/nat-gateway-resource.md).

* Dodaj [trasy zdefiniowane przez użytkownika (UDR)](../virtual-network/virtual-networks-udr-overview.md) do podsieci zawierającej zasób obliczeniowy. Ustanów UDR dla każdego adresu IP, który jest używany przez usługę Azure Batch w regionie, w którym znajdują się zasoby. Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi w celu planowania zadań. Należy również dodać adres IP dla usługi Azure Machine Learning, ponieważ jest to wymagane w celu uzyskania dostępu do wystąpień obliczeniowych. Podczas dodawania adresu IP dla usługi Azure Machine Learning należy dodać adres IP zarówno dla __podstawowego, jak i pomocniczego__ regionu platformy Azure. Region podstawowy, w którym znajduje się obszar roboczy.

    Aby znaleźć region pomocniczy, zobacz [zapewnianie ciągłości działania & odzyskiwania po awarii przy użyciu sparowanych regionów platformy Azure](../best-practices-availability-paired-regions.md#azure-regional-pairs). Na przykład jeśli usługa Azure Machine Learning jest w regionie Wschodnie stany USA 2, region pomocniczy to środkowe stany USA. 

    Aby uzyskać listę adresów IP usługi Batch i usługi Azure Machine Learning, należy użyć jednej z następujących metod:

    * Pobierz [zakresy adresów IP i Tagi usług platformy Azure](https://www.microsoft.com/download/details.aspx?id=56519) , a następnie wyszukaj plik dla `BatchNodeManagement.<region>` i `AzureMachineLearning.<region>` , gdzie `<region>` jest Twoim regionem świadczenia usługi Azure.

    * Użyj [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , aby pobrać te informacje. Poniższy przykład pobiera informacje o adresie IP i filtruje informacje dla regionu Wschodnie stany USA 2 (podstawowy) i regionu środkowe stany USA (pomocniczy):

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        # Get primary region IPs
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        # Get secondary region IPs
        az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
        ```

        > [!TIP]
        > Jeśli używasz regionów US-Wirginia, US-Arizona lub Chiny-wschód-2, te polecenia nie zwracają adresów IP. Zamiast tego użyj jednego z poniższych linków w celu pobrania listy adresów IP:
        >
        > * [Zakresy adresów IP platformy Azure i Tagi usług dla Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Zakresy adresów IP platformy Azure i Tagi usług dla Chin platformy Azure](https://www.microsoft.com//download/details.aspx?id=57062)
    
    Po dodaniu UDR, zdefiniuj trasę dla każdego powiązanego prefiksu adresu IP partii i ustaw __Typ następnego przeskoku__ na __Internet__. Na poniższej ilustracji przedstawiono przykład tego UDR w Azure Portal:

    ![Przykład UDR dla prefiksu adresu](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > Adresy IP mogą ulec zmianie z upływem czasu.

    Oprócz wszelkich zdefiniowanych UDR ruch wychodzący do usługi Azure Storage musi być dozwolony za pomocą lokalnego urządzenia sieciowego. W odniesieniu do adresów URL tego ruchu znajdują się następujące formy: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` , i `<account>.blob.core.windows.net` . 

    Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch w sieci wirtualnej](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Tworzenie klastra obliczeniowego w sieci wirtualnej

Aby utworzyć klaster środowisko obliczeniowe usługi Machine Learning, wykonaj następujące czynności:

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/), a następnie wybierz swoją subskrypcję i obszar roboczy.

1. Wybierz pozycję __obliczenia__ po lewej stronie.

1. Wybierz pozycję __klastry szkoleniowe__ z centrum, a następnie wybierz pozycję __+__ .

1. W oknie dialogowym __nowy klaster szkoleniowy__ rozwiń sekcję __Ustawienia zaawansowane__ .

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności w sekcji __Konfigurowanie sieci wirtualnej__ :

    1. Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.
    1. Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną, która zawiera podsieć.
    1. Z listy rozwijanej __podsieć__ wybierz podsieć, która ma zostać użyta.

   ![Ustawienia sieci wirtualnej dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Klaster środowisko obliczeniowe usługi Machine Learning można również utworzyć za pomocą zestawu Azure Machine Learning SDK. Poniższy kod tworzy nowy klaster środowisko obliczeniowe usługi Machine Learning w `default` podsieci sieci wirtualnej o nazwie `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Po zakończeniu procesu tworzenia nauczysz model przy użyciu klastra w eksperymentie. Aby uzyskać więcej informacji, zobacz [Wybieranie i używanie elementu docelowego obliczeń do szkoleń](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Dostęp do danych w notesie wystąpienia obliczeniowego

Jeśli używasz notesów w wystąpieniu obliczeniowym platformy Azure, musisz się upewnić, że Notes jest uruchomiony w zasobie obliczeniowym za tą samą siecią wirtualną i podsiecią, co dane. 

Wystąpienie obliczeniowe należy skonfigurować tak, aby znajdować się w tej samej sieci wirtualnej podczas tworzenia w obszarze **Ustawienia zaawansowane**  >  **Skonfiguruj sieć wirtualną**. Nie można dodać istniejącego wystąpienia obliczeniowego do sieci wirtualnej.

## <a name="azure-databricks"></a>Azure Databricks

Aby używać Azure Databricks w sieci wirtualnej z obszarem roboczym, muszą zostać spełnione następujące wymagania:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co klaster Azure Databricks.
> * Poza niektórymi __datakostki — prywatnymi__ i niestandardowymi __—__ podsieciami używanymi Azure Databricks, wymagana jest również __Domyślna__ podsieć utworzona dla sieci wirtualnej.

Aby uzyskać szczegółowe informacje na temat używania Azure Databricks z siecią wirtualną, zobacz [wdrażanie Azure Databricks w Virtual Network platformy Azure](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Maszyna wirtualna lub klaster usługi HDInsight

> [!IMPORTANT]
> Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu.

W tej sekcji dowiesz się, jak używać maszyny wirtualnej lub klastra usługi Azure HDInsight w sieci wirtualnej z Twoim obszarem roboczym.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Tworzenie maszyny wirtualnej lub klastra usługi HDInsight

Utwórz maszynę wirtualną lub klaster usługi HDInsight przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure i umieść klaster w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz następujące artykuły:
* [Tworzenie sieci wirtualnych platformy Azure dla maszyn wirtualnych z systemem Linux i zarządzanie nimi](../virtual-machines/linux/tutorial-virtual-network.md)

* [Zwiększanie usługi HDInsight przy użyciu sieci wirtualnej platformy Azure](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Konfigurowanie portów sieciowych 

Zezwól Azure Machine Learning na komunikowanie się z portem SSH na maszynie wirtualnej lub w klastrze, skonfiguruj wpis źródła dla sieciowej grupy zabezpieczeń. Port SSH zazwyczaj jest portem 22. Aby zezwolić na ruch z tego źródła, wykonaj następujące czynności:

1. Z listy rozwijanej __Źródło__ wybierz pozycję __tag usługi__.

1. Z listy rozwijanej __tag usługi źródłowej__ wybierz pozycję __AzureMachineLearning__.

    ![Reguły ruchu przychodzącego na potrzeby przeprowadzania eksperymentów na maszynie wirtualnej lub w klastrze usługi HDInsight w sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Z listy rozwijanej __zakresy portów źródłowych__ wybierz pozycję __*__ .

1. Z listy rozwijanej __Lokalizacja docelowa__ wybierz __dowolne__.

1. Z listy rozwijanej __zakresy portów docelowych__ wybierz pozycję __22__.

1. W obszarze __Protokół__ wybierz opcję __dowolny__.

1. W obszarze __Akcja__ wybierz pozycję __Zezwalaj__.

Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](../virtual-network/network-security-groups-overview.md#default-security-rules).

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, zobacz sekcję [ograniczanie łączności wychodzącej z sieci wirtualnej](#limiting-outbound-from-vnet) .

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Dołącz maszynę wirtualną lub klaster usługi HDInsight

Dołącz maszynę wirtualną lub klaster usługi HDInsight do obszaru roboczego Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest trzecią częścią serii sieci wirtualnych z pięcioma częściami. Zapoznaj się z pozostałymi artykułami, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 1: Omówienie usługi Virtual Network](how-to-network-security-overview.md)
* [Część 2: Zabezpieczanie zasobów obszaru roboczego](how-to-secure-workspace-vnet.md)
* [Część 4: Zabezpieczanie środowiska inferencing](how-to-secure-inferencing-vnet.md)
* [Część 5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)

Zobacz również artykuł dotyczący używania [niestandardowego systemu DNS](how-to-custom-dns.md) do rozpoznawania nazw.