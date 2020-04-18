---
title: Bezpieczne eksperymenty i wnioskowanie w sieci wirtualnej
titleSuffix: Azure Machine Learning
description: dowiedz się, jak zabezpieczyć zadania eksperymentowania/szkolenia oraz zadania wnioskowania/oceniania w usłudze Azure Machine Learning w ramach sieci wirtualnej platformy Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/17/2020
ms.openlocfilehash: 1f3c9f86072eeedbc999946d0f846fbc3b41f94d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641750"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Zabezpieczanie zadań eksperymentowania i wnioskowania usługi Azure w ramach sieci wirtualnej platformy Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zabezpieczyć zadania eksperymentowania/szkolenia i wnioskowanie/ocenianie zadań w usłudze Azure Machine Learning w sieci wirtualnej platformy Azure (vnet).

**Sieć wirtualna** działa jako granica zabezpieczeń, izolowanie zasobów platformy Azure z publicznego Internetu. Można również dołączyć do sieci wirtualnej platformy Azure do sieci lokalnej. Łącząc sieci, można bezpiecznie szkolić modele i uzyskać dostęp do wdrożonych modeli do wnioskowania.

Usługa Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych. Zasoby obliczeniowe lub [obiekty docelowe obliczeń](concept-compute-target.md)są używane do szkolenia i wdrażania modeli. Obiekty docelowe można tworzyć w sieci wirtualnej. Na przykład można użyć maszyny wirtualnej nauki o danych firmy Microsoft do uczenia modelu, a następnie wdrożyć model do usługi Azure Kubernetes Service (AKS). Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [omówienie usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Ten artykuł zawiera również szczegółowe informacje o *zaawansowanych ustawieniach zabezpieczeń,* informacje, które nie są niezbędne w przypadku użycia podstawowego lub eksperymentalnego. Niektóre sekcje tego artykułu zawierają informacje o konfiguracji dla różnych scenariuszy. Nie musisz wypełniać instrukcji w kolejności lub w całości.

> [!TIP]
> O ile nie zostanie to wyraźnie wywołane, przy użyciu zasobów, takich jak konta magazynu lub obiekty docelowe obliczeniowe wewnątrz sieci wirtualnej będzie działać zarówno z potoków uczenia maszynowego i przepływów pracy bez potoku, takich jak uruchamia skrypt.

> [!WARNING]
> Firma Microsoft nie obsługuje korzystania z funkcji usługi Azure Machine Learning Studio, takich jak zautomatyzowane ml, zestawy danych, etykiety danych, projektant i notesy, jeśli podstawowy magazyn ma włączoną sieć wirtualną.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Obszar roboczy](how-to-manage-workspace.md)usługi Azure Machine Learning .

+ Ogólna wiedza robocza zarówno usługi [Azure Virtual Network,](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) jak i [sieci IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Istniejąca wcześniej sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

## <a name="use-a-storage-account-for-your-workspace"></a>Używanie konta magazynu dla obszaru roboczego

> [!WARNING]
> Jeśli masz analityków danych, które używają projektanta usługi Azure Machine Learning, otrzymają błąd podczas wizualizacji danych z konta magazynu w sieci wirtualnej. Następujący tekst jest błędem, który otrzymują:
>
> __Błąd: nie można profilu tego zestawu danych. Może to być spowodowane tym, że dane są przechowywane za siecią wirtualną lub dane nie obsługują profilu.__

Aby użyć konta magazynu platformy Azure dla obszaru roboczego w sieci wirtualnej, należy wykonać następujące kroki:

1. Utwórz zasób obliczeniowy (na przykład wystąpienie lub klaster obliczeniowy usługi Machine Learning) za siecią wirtualną lub dołącz zasób obliczeniowy do obszaru roboczego (na przykład klaster HDInsight, maszyna wirtualna lub klaster usługi Azure Kubernetes). Zasób obliczeniowy może być do eksperymentowania lub wdrażania modelu.

   Aby uzyskać więcej informacji, zobacz [Użyj obliczeń uczenia maszynowego](#amlcompute), [Użyj maszyny wirtualnej lub klastra HDInsight](#vmorhdi)i [Użyj usługi Azure Kubernetes Service](#aksvnet) sekcje w tym artykule.

1. W witrynie Azure portal przejdź do magazynu dołączonego do obszaru roboczego.

   [![Magazyn dołączony do obszaru roboczego usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stronie **Usługi Azure Storage** wybierz pozycję __Zapory i sieci wirtualne__.

   ![Obszar "Zapory i sieci wirtualne" na stronie Usługi Azure Storage w witrynie Azure portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stronie __Zapory i sieci wirtualne__ wykonaj następujące czynności:
    - Wybierz pozycję __Wybrane sieci__.
    - W __obszarze Sieci wirtualne__wybierz łącze __Dodaj istniejącą sieć wirtualną.__ Ta akcja dodaje sieć wirtualną, w której znajdują się obliczenia (zobacz krok 1).

        > [!IMPORTANT]
        > Konto magazynu musi znajdować się w tej samej sieci wirtualnej i podsieci co wystąpienia obliczeniowe lub klastry używane do szkolenia lub wnioskowania.

    - Zaznacz pole wyboru __Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu.__

    > [!IMPORTANT]
    > Podczas pracy z zestawem SDK usługi Azure Machine Learning środowisko programistyczne musi mieć możliwość nawiązania połączenia z kontem usługi Azure Storage. Gdy konto magazynu znajduje się wewnątrz sieci wirtualnej, zapora musi zezwalać na dostęp z adresu IP środowiska programistycznego.
    >
    > Aby włączyć dostęp do konta magazynu, odwiedź __zapory i sieci wirtualne__ dla konta magazynu *z przeglądarki internetowej na kliencie dewelopera*. Następnie użyj pola wyboru __Dodaj adres IP klienta,__ aby dodać adres IP klienta do __zakresu adresów__. Można również użyć pola __ZAKRES ADRESów,__ aby ręcznie wprowadzić adres IP środowiska programistycznego. Po dodaniu adresu IP klienta można uzyskać dostęp do konta magazynu przy użyciu zestawu SDK.

   [![Okienko "Zapory i sieci wirtualne" w witrynie Azure portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> W sieci wirtualnej można umieścić zarówno _domyślne konto magazynu_ dla usługi Azure Machine Learning, jak i _konta magazynu inne niż domyślne._
>
> Domyślne konto magazynu jest automatycznie aprowizowana podczas tworzenia obszaru roboczego.
>
> W przypadku kont magazynu `storage_account` innych niż domyślne parametr w [ `Workspace.create()` funkcji](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) umożliwia określenie niestandardowego konta magazynu według identyfikatora zasobu platformy Azure.

## <a name="use-azure-data-lake-storage-gen-2"></a>Korzystanie z usługi Azure Data Lake Storage Gen 2

Usługa Azure Data Lake Storage Gen 2 to zestaw funkcji do analizy dużych zbiorów danych, zbudowanych na podstawie magazynu obiektów Blob platformy Azure. Może służyć do przechowywania danych używanych do uczenia modeli za pomocą usługi Azure Machine Learning. 

Aby użyć magazynu usługi Data Lake Gen 2 w sieci wirtualnej obszaru roboczego usługi Azure Machine Learning, należy wykonać następujące kroki:

1. Utwórz konto usługi Azure Data Lake Storage gen 2. Aby uzyskać więcej informacji, zobacz [Tworzenie konta magazynu usługi Azure Data Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Użyj kroków 2-4 w poprzedniej sekcji, [Użyj konta magazynu dla obszaru roboczego](#use-a-storage-account-for-your-workspace), aby umieścić konto w sieci wirtualnej.

Korzystając z usługi Azure Machine Learning z usługą Data Lake Storage Gen 2 w sieci wirtualnej, należy użyć następujących wskazówek:

* Jeśli używasz __zestawu SDK do utworzenia zestawu danych,__ a system uruchamiany `validate=False` kod nie znajduje się w sieci __wirtualnej,__ użyj tego parametru. Ten parametr pomija sprawdzanie poprawności, co kończy się niepowodzeniem, jeśli system nie znajduje się w tej samej sieci wirtualnej co konto magazynu. Aby uzyskać więcej informacji, zobacz [from_files().](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)

* Podczas korzystania z wystąpienia obliczeniowego usługi Azure Machine Learning lub klastra obliczeniowego do uczenia modelu przy użyciu zestawu danych, musi znajdować się w tej samej sieci wirtualnej co konto magazynu.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Używanie wystąpienia magazynu kluczy w obszarze roboczym

Wystąpienie magazynu kluczy skojarzone z obszarem roboczym jest używane przez usługę Azure Machine Learning do przechowywania następujących poświadczeń:
* Parametry połączenia skojarzonego konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączeń z magazynami danych

Aby korzystać z funkcji eksperymentowania usługi Azure Machine Learning z usługą Azure Key Vault za siecią wirtualną, należy wykonać następujące kroki:

1. Przejdź do magazynu kluczy skojarzonego z obszarem roboczym.

   [![Magazyn kluczy skojarzony z obszarem roboczym usługi Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na stronie **Przechowalnia kluczy** w lewym okienku wybierz pozycję __Zapory i sieci wirtualne__.

   ![Sekcja "Zapory i sieci wirtualne" w okienku Przechowalnia kluczy](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stronie __Zapory i sieci wirtualne__ wykonaj następujące czynności:
    - W obszarze __Zezwalaj na dostęp z__wybierz pozycję __Wybrane sieci__.
    - W __obszarze Sieci wirtualne__wybierz pozycję __Dodaj istniejące sieci wirtualne,__ aby dodać sieć wirtualną, w której znajdują się obliczenia eksperymentów.
    - W obszarze __Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory__wybierz pozycję __Tak__.

   [![Sekcja "Zapory i sieci wirtualne" w okienku Przechowalnia kluczy](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

<a id="amlcompute"></a>

## <a name="use-a-machine-learning-compute"></a><a name="compute-instance"></a>Korzystanie z obliczeń uczenia maszynowego

Aby użyć wystąpienia obliczeniowego usługi Azure Machine Learning lub klastra obliczeniowego w sieci wirtualnej, muszą być spełnione następujące wymagania sieciowe:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy usługi Azure Machine Learning.
> * Podsieć, która jest określona dla wystąpienia obliczeniowego lub klastra musi mieć wystarczającą liczbę nieprzypisanych adresów IP, aby pomieścić liczbę maszyn wirtualnych, które są kierowane. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanego adresu IP, klaster obliczeniowy zostanie częściowo przydzielony.
> * Sprawdź, czy zasady zabezpieczeń lub blokady w subskrypcji lub grupie zasobów sieci wirtualnej ograniczają uprawnienia do zarządzania siecią wirtualną. Jeśli planujesz zabezpieczyć sieć wirtualną, ograniczając ruch, pozostaw niektóre porty otwarte dla usługi obliczeniowej. Aby uzyskać więcej informacji, zobacz sekcję [Wymagane porty.](#mlcports)
> * Jeśli zamierzasz umieścić wiele wystąpień obliczeniowych lub klastrów w jednej sieci wirtualnej, może być konieczne żądanie zwiększenia przydziału dla jednego lub więcej zasobów.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co wystąpienie lub klaster obliczeniowy usługi Azure Machine Learning. 

> [!TIP]
> Wystąpienie lub klaster obliczeniowy uczenia maszynowego automatycznie przydziela dodatkowe zasoby sieciowe w grupie zasobów zawierającej sieć wirtualną. Dla każdego wystąpienia obliczeniowego lub klastra usługa przydziela następujące zasoby:
> 
> * Jedna grupa zabezpieczeń sieci
> * Jeden publiczny adres IP
> * Jeden moduł równoważenia obciążenia
> 
> Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) subskrypcji.


### <a name="required-ports"></a><a id="mlcports"></a>Wymagane porty

Machine Learning Compute obecnie używa usługi Azure Batch do aprowizowania maszyn wirtualnych w określonej sieci wirtualnej. Podsieć musi zezwalać na komunikację przychodzącą z usługi Batch. Ta komunikacja służy do planowania pracy w węzłach obliczeniowych uczenia maszynowego i do komunikowania się z usługą Azure Storage i innymi zasobami. Usługa Batch dodaje sieciowe grupy zabezpieczeń (NSG) na poziomie interfejsów sieciowych (KART SIECIOWYCH), które są podłączone do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Przychodzący ruch TCP na portach 29876 i 29877 z __tagu usługi__ __BatchNodeManagement__.

    ![Reguła ruchu przychodzącego, która używa tagu usługi BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- (Opcjonalnie) Przychodzący ruch TCP na porcie 22, aby umożliwić dostęp zdalny. Użyj tego portu tylko wtedy, gdy chcesz połączyć się przy użyciu protokołu SSH na publicznym adresie IP.

- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu.

- Dla inicjału obliczeń przychodzącego ruchu TCP na porcie 44224 z __tagu usługi__ __AzureMachineLearning__.

Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli grupa sieciowej sieciowej blokuje komunikację z węzłami obliczeniowymi, usługa obliczeniowa ustawia stan węzłów obliczeniowych na bezużyteczny.

Nie trzeba określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Azure Batch konfiguruje własne sieciowe sieciowe. Jeśli jednak określona podsieć ma skojarzone sieciowe sieciowe sieci bezpieczeństwa lub zaporę, skonfiguruj reguły zabezpieczeń przychodzących i wychodzących, jak wspomniano wcześniej.

Konfiguracja reguły sieciowej grupy zabezpieczeń w witrynie Azure portal jest wyświetlana na następujących obrazach:

[![Przychodzące reguły sieciowej sieciowej sieciowej dla obliczeń uczenia maszynowego](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png#lightbox)

![Wychodzące reguły sieciowej sieciowej sieciowej dla obliczeń uczenia maszynowego](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Ograniczanie łączności wychodzącej z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący sieci wirtualnej, wykonaj następujące czynności:

- Odmów wychodzącego połączenia internetowego przy użyciu reguł sieciowej listy adresów sieciowych.

- W przypadku __wystąpienia obliczeniowego__ lub __klastra obliczeniowego__ograniczyć ruch wychodzący do następujących elementów:
   - Usługa Azure Storage, przy użyciu __tagu usługi__ __Storage.RegionName__. Gdzie `{RegionName}` jest nazwa regionu platformy Azure.
   - Usługa Azure Container Registry, przy użyciu __tagu usługi__ __AzureContainerRegistry.RegionName__. Gdzie `{RegionName}` jest nazwa regionu platformy Azure.
   - Usługa Azure Machine Learning przy użyciu __tagu usługi__ __AzureMachineLearning__
   - Usługa Azure Resource Manager, przy użyciu __tagu usługi__ __AzureResourceManager__
   - Usługa Azure Active Directory przy użyciu __tagu usługi__ __AzureActiveDirectory__

Konfiguracja reguły sieciowej grupy zabezpieczeń w witrynie Azure portal jest wyświetlana na poniższej ilustracji:

[![Wychodzące reguły sieciowej sieciowej sieciowej dla obliczeń uczenia maszynowego](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Jeśli planujesz używać domyślnych obrazów platformy Docker dostarczonych przez firmę Microsoft i włączanie zależności zarządzanych przez użytkownika, należy również użyć __tagu usługi__ __MicrosoftContainerRegistry.Region_Name__ (na przykład MicrosoftContainerRegistry.EastUS).
>
> Ta konfiguracja jest potrzebna, gdy masz kod podobny do następujących fragmentów kodu w ramach skryptów szkoleniowych:
>
> __Szkolenie RunConfig__
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
> __Szkolenie estymatora__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

Jeśli używasz wymuszonego tunelowania za pomocą obliczeń uczenia maszynowego, dodaj [trasy zdefiniowane przez użytkownika (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsieci zawierającej zasób obliczeniowy.

* Ustanowienie UDR dla każdego adresu IP, który jest używany przez usługę Azure Batch w regionie, w którym istnieją zasoby. Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi do planowania zadań. Aby uzyskać listę adresów IP usługi Batch, należy użyć jednej z następujących metod:

    * Pobierz [zakresy adresów IP platformy Azure i tagi usług](https://www.microsoft.com/download/details.aspx?id=56519) i wyszukaj plik `BatchNodeManagement.<region>`, gdzie `<region>` jest twój region platformy Azure.

    * Użyj [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby pobrać informacje. Poniższy przykład pobiera informacje o adresie IP i odfiltruje informacje dla regionu Wschodnia stany USA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        ```

* Ruch wychodzący do usługi Azure Storage nie może być blokowany przez lokalne urządzenie sieciowe. W szczególności adresy URL są `<account>.table.core.windows.net` `<account>.queue.core.windows.net`w `<account>.blob.core.windows.net`formie , i .

Po dodaniu UDR należy zdefiniować trasę dla każdego powiązanego prefiksu adresu IP partii i ustawić __następny typ przeskoku__ na __Internet__. Na poniższej ilustracji przedstawiono przykład tego narzędzia UDR w witrynie Azure portal:

![Przykład UDR dla prefiksu adresu](./media/how-to-enable-virtual-network/user-defined-route.png)

Aby uzyskać więcej informacji, zobacz [Tworzenie puli partii Azure w sieci wirtualnej](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Tworzenie klastra obliczeniowego w sieci wirtualnej

Aby utworzyć klaster obliczeniowy uczenia maszynowego, należy wykonać następujące czynności:

1. Zaloguj się do [studia usługi Azure Machine Learning,](https://ml.azure.com/)a następnie wybierz subskrypcję i obszar roboczy.

1. Wybierz __pozycję Oblicz__ po lewej stronie.

1. Wybierz __pozycję Klastry szkoleniowe__ z __+__ centrum, a następnie wybierz pozycję .

1. W oknie dialogowym __Nowy klaster szkoleniowy__ rozwiń sekcję __Ustawienia zaawansowane.__

1. Aby skonfigurować ten zasób obliczeniowy do używania sieci wirtualnej, wykonaj następujące czynności w sekcji __Konfigurowanie sieci wirtualnej:__

    1. Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.
    1. Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną zawierającą podsieć.
    1. Z __Subnet__ listy rozwijanej Podsieć wybierz podsieć, której chcesz użyć.

   ![Ustawienia sieci wirtualnej dla uczenia maszynowego Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Można również utworzyć klaster obliczeniowy uczenia maszynowego przy użyciu zestawu SDK usługi Azure Machine Learning. Poniższy kod tworzy nowy klaster obliczeniowy uczenia maszynowego w podsieci `default` sieci wirtualnej o nazwie: `mynetwork`

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

Po zakończeniu procesu tworzenia można szkolić model przy użyciu klastra w eksperymencie. Aby uzyskać więcej informacji, zobacz [Wybieranie i używanie celu obliczeniowego do szkolenia](how-to-set-up-training-targets.md).

## <a name="use-azure-databricks"></a>Korzystanie z usługi Azure Databricks

Aby korzystać z usługi Azure Databricks w sieci wirtualnej z obszarem roboczym, muszą być spełnione następujące wymagania:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy usługi Azure Machine Learning.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co klaster usługi Azure Databricks.
> * Oprócz __podsieci databricks-private__ i __databricks-public__ używanych przez usługę Azure Databricks wymagana jest również __domyślna__ podsieć utworzona dla sieci wirtualnej.

Aby uzyskać szczegółowe informacje na temat korzystania z usługi Azure Databricks z siecią wirtualną, zobacz [Wdrażanie usługi Azure Databricks w sieci wirtualnej platformy Azure.](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)

<a id="vmorhdi"></a>

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Używanie maszyny wirtualnej lub klastra usługi HDInsight

> [!IMPORTANT]
> Usługa Azure Machine Learning obsługuje tylko maszyny wirtualne, które są uruchomione Ubuntu.

Aby użyć maszyny wirtualnej lub klastra usługi Azure HDInsight w sieci wirtualnej z obszarem roboczym, należy wykonać następujące kroki:

1. Utwórz klaster maszyny Wirtualnej lub HDInsight przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure i umieść klaster w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji zobacz następujące artykuły:
    * [Tworzenie sieci wirtualnych platformy Azure i zarządzanie nimi dla maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Rozszerzanie usługi HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Aby umożliwić usłudze Azure Machine Learning komunikowanie się z portem SSH na maszynie wirtualnej lub klastrze, skonfiguruj wpis źródłowy dla sieciowej grupy zabezpieczeń. Port SSH jest zwykle port 22. Aby zezwolić na ruch z tego źródła, wykonaj następujące czynności:

    * Z listy rozwijanej __Źródło__ wybierz pozycję __Service Tag__.

    * Z listy rozwijanej __Tag usługi źródłowej__ wybierz pozycję __AzureMachineLearning__.

    * Z listy rozwijanej __Zakresy portów źródłowych__ wybierz pozycję __*__.

    * Z listy rozwijanej __Miejsce docelowe__ wybierz pozycję __Dowolna__.

    * Z listy rozwijanej __Zakresy portów docelowych__ wybierz __pozycję 22__.

    * W obszarze __Protokół__wybierz __opcję Dowolna__.

    * W obszarze __Akcja__wybierz pozycję __Zezwalaj__.

   ![Reguły ruchu przychodzącego do wykonywania eksperymentów na maszynie Wirtualnej lub klastrze USŁUGI HDInsight w sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący sieci wirtualnej, zobacz [ogranicz łączność wychodzącą z sekcji sieć wirtualna.](#limiting-outbound-from-vnet)

1. Dołącz klaster maszyny Wirtualnej lub HDInsight do obszaru roboczego usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych dla szkolenia modelu](how-to-set-up-training-targets.md).

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Korzystanie z usługi Azure Kubernetes (AKS)

Aby dodać usługi AKS w sieci wirtualnej do obszaru roboczego, należy wykonać następujące czynności:

> [!IMPORTANT]
> Przed rozpoczęciem poniższej procedury należy postępować zgodnie z wymaganiami wstępnymi w [temacie Konfigurowanie zaawansowanej sieci w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking#prerequisites) i zaplanować adresowanie IP dla klastra.
>
> Wystąpienie usługi AKS i sieć wirtualna platformy Azure muszą znajdować się w tym samym regionie. Jeśli zabezpieczysz konta usługi Azure Storage używane przez obszar roboczy w sieci wirtualnej, muszą one znajdować się w tej samej sieci wirtualnej co wystąpienie usługi AKS.

> [!WARNING]
> Usługa Azure Machine Learning nie obsługuje korzystania z usługi Azure Kubernetes, która ma włączone łącze prywatne.

1. Zaloguj się do [studia usługi Azure Machine Learning,](https://ml.azure.com/)a następnie wybierz subskrypcję i obszar roboczy.

1. Wybierz __pozycję Oblicz__ po lewej stronie.

1. Wybierz __pozycję Klastry wnioskowania__ od __+__ środka, a następnie wybierz opcję .

1. W oknie dialogowym __Nowy klaster wnioskowania__ wybierz pozycję __Zaawansowane__ w obszarze __Konfiguracja sieci__.

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące akcje:

    1. Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.
    1. Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną zawierającą podsieć.
    1. Z __Subnet__ listy rozwijanej Podsieć wybierz podsieć.
    1. W polu __Zakres adresów usługi Kubernetes__ wprowadź zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu ip notacji cidr (Classless Inter-Domain Routing) do definiowania adresów IP dostępnych dla klastra. Nie może pokrywać się z żadnymi zakresami adresów IP podsieci (na przykład 10.0.0.0/16).
    1. W polu __Adres IP usługi DNS kubernetes__ wprowadź adres IP usługi DNS kubernetes. Ten adres IP jest przypisany do usługi DNS Kubernetes. Musi znajdować się w zakresie adresów usługi Kubernetes (na przykład 10.0.0.10).
    1. W polu __Adres mostka platformy Docker__ wprowadź adres mostka platformy Docker. Ten adres IP jest przypisany do programu Docker Bridge. Nie może znajdować się w żadnych zakresach adresów IP podsieci ani w zakresie adresów usługi Kubernetes (na przykład 172.17.0.1/16).

   ![Usługa Azure Machine Learning: ustawienia sieci wirtualnej obliczeń usługi Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Upewnij się, że grupa sieciowej grupy sieciowej grupy, która kontroluje sieć wirtualną, ma włączoną regułę zabezpieczeń przychodzących dla punktu końcowego oceniania, dzięki czemu można ją wywołać spoza sieci wirtualnej.
   > [!IMPORTANT]
   > Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej sieciowej. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Reguła zabezpieczeń ruchu przychodzącego](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Można również użyć narzędzia Azure Machine Learning SDK, aby dodać usługę Azure Kubernetes w sieci wirtualnej. Jeśli masz już klaster AKS w sieci wirtualnej, dołącz go do obszaru roboczego zgodnie z opisem w [jak wdrożyć w UA](how-to-deploy-and-where.md). Poniższy kod tworzy nowe wystąpienie `default` usługi AKS w `mynetwork`podsieci sieci wirtualnej o nazwie:

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

Po zakończeniu procesu tworzenia można uruchomić wnioskowanie lub ocenianie modelu w klastrze AKS za siecią wirtualną. Aby uzyskać więcej informacji, zobacz [Jak wdrożyć w udręki AKS](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Korzystanie z prywatnych adresów IP z usługą Azure Kubernetes

Domyślnie publiczny adres IP jest przypisywany do wdrożeń usługi AKS. Korzystając z usługi AKS w sieci wirtualnej, można użyć prywatnego adresu IP. Prywatne adresy IP są dostępne tylko z sieci wirtualnej lub połączonych sieci.

Prywatny adres IP jest włączony przez skonfigurowanie usługi AKS do używania _wewnętrznego modułu równoważenia obciążenia_. 

> [!IMPORTANT]
> Nie można włączyć prywatnego adresu IP podczas tworzenia klastra usługi Azure Kubernetes. Musi być włączona jako aktualizacja do istniejącego klastra.

Poniższy fragment kodu pokazuje, jak **utworzyć nowy klaster usługi AKS,** a następnie zaktualizować go, aby użyć prywatnego modułu równoważenia obciążenia IP/wewnętrznego:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
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
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Interfejs wiersza polecenia platformy Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Zawartość pliku, `body.json` do którego odwołuje się polecenie, jest podobna do następującego dokumentu JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> Obecnie nie można skonfigurować modułu równoważenia obciążenia podczas wykonywania operacji __dołączania__ w istniejącym klastrze. Należy najpierw dołączyć klaster, a następnie wykonać operację aktualizacji, aby zmienić moduł równoważenia obciążenia.

Aby uzyskać więcej informacji na temat korzystania z wewnętrznego modułu równoważenia obciążenia za pomocą usługi AKS, zobacz [Używanie wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes .](/azure/aks/internal-lb)

## <a name="use-azure-container-instances-aci"></a>Użyj wystąpień kontenerów platformy Azure (ACI)

Wystąpienia kontenera platformy Azure są tworzone dynamicznie podczas wdrażania modelu. Aby umożliwić usługi Azure Machine Learning tworzenie usługi ACI wewnątrz sieci wirtualnej, należy włączyć __delegowanie podsieci__ dla podsieci używanej przez wdrożenie.

Aby użyć usługi ACI w sieci wirtualnej w obszarze roboczym, należy wykonać następujące czynności:

1. Aby włączyć delegowanie podsieci w sieci wirtualnej, użyj informacji w artykule [Dodaj lub usuń delegowanie podsieci.](../virtual-network/manage-subnet-delegation.md) Delegowanie można włączyć podczas tworzenia sieci wirtualnej lub dodać ją do istniejącej sieci.

    > [!IMPORTANT]
    > Podczas włączania delegowania należy użyć `Microsoft.ContainerInstance/containerGroups` jako __delegata podsieci do__ wartości usługi.

2. Wdrażanie modelu przy użyciu [pliku AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), użyj parametrów `vnet_name` i. `subnet_name` Ustaw te parametry na nazwę sieci wirtualnej i podsieć, w której włączono delegowanie.



## <a name="use-azure-firewall"></a>Korzystanie z Zapory platformy Azure

Korzystając z Zapory platformy Azure, należy skonfigurować regułę sieciową, aby zezwalać na ruch do i z następujących adresów:

- `*.batchai.core.windows.net`
- `ml.azure.com`
- `*.azureml.ms`
- `*.experiments.azureml.net`
- `*.modelmanagement.azureml.net`
- `mlworkspace.azure.ai`
- `*.aether.ms`

Podczas dodawania reguły ustaw __protokół__ na dowolny, a porty na `*`.

Aby uzyskać więcej informacji na temat konfigurowania reguły sieciowej, zobacz [Wdrażanie i konfigurowanie Zapory platformy Azure](/azure/firewall/tutorial-firewall-deploy-portal#configure-a-network-rule).

## <a name="use-azure-container-registry"></a>Korzystanie z usługi Azure Container Registry

> [!IMPORTANT]
> Usługa Azure Container Registry (ACR) można umieścić w sieci wirtualnej, jednak należy spełnić następujące wymagania wstępne:
>
> * Obszar roboczy usługi Azure Machine Learning musi być w wersji Enterprise. Aby uzyskać informacje na temat uaktualniania, zobacz [Uaktualnianie do wersji Enterprise](how-to-manage-workspace.md#upgrade).
> * Twój rejestr kontenerów platformy Azure musi być wersją Premium . Aby uzyskać więcej informacji na temat uaktualniania, zobacz [Zmienianie jednostek SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Usługa Azure Container Registry musi znajdować się w tej samej sieci wirtualnej i podsieci, co konto magazynu i obiekty docelowe obliczeniowe używane do szkolenia lub wnioskowania.
> * Obszar roboczy usługi Azure Machine Learning musi zawierać [klaster obliczeniowy usługi Azure Machine Learning.](how-to-set-up-training-targets.md#amlcompute)
>
>     Gdy usługa ACR znajduje się za siecią wirtualną, usługa Azure Machine Learning nie może używać jej do bezpośredniego tworzenia obrazów platformy Docker. Zamiast tego klaster obliczeniowy jest używany do tworzenia obrazów.

1. Aby znaleźć nazwę rejestru kontenerów platformy Azure dla obszaru roboczego, użyj jednej z następujących metod:

    __Azure Portal__

    W sekcji omówienie obszaru roboczego łącza wartości __rejestru__ do rejestru kontenerów platformy Azure.

    ![Usługa Azure Container Registry dla obszaru roboczego](./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png)

    __Interfejs wiersza polecenia platformy Azure__

    Jeśli [zainstalowano rozszerzenie uczenia maszynowego dla interfejsu wiersza polecenia platformy Azure,](reference-azure-machine-learning-cli.md)można użyć `az ml workspace show` polecenia, aby wyświetlić informacje o obszarze roboczym.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    To polecenie zwraca wartość `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`podobną do . Ostatnia część ciągu jest nazwa rejestru kontenerów platformy Azure dla obszaru roboczego.

1. Aby ograniczyć dostęp do sieci wirtualnej, należy wykonać czynności opisane w temacie [Konfigurowanie dostępu do sieci dla rejestru](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Podczas dodawania sieci wirtualnej wybierz sieć wirtualną i podsieć dla zasobów usługi Azure Machine Learning.

1. Użyj zestawu Azure Machine Learning Python SDK, aby skonfigurować klaster obliczeniowy do tworzenia obrazów docker. Poniższy fragment kodu pokazuje, jak to zrobić:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Konto magazynu, klaster obliczeniowy i rejestr kontenerów platformy Azure muszą znajdować się w tej samej podsieci sieci wirtualnej.
    
    Aby uzyskać więcej informacji, zobacz odwołanie do metody [update().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

1. Jeśli używasz łącza prywatnego dla obszaru roboczego usługi Azure Machine Learning i umieścisz rejestr kontenerów platformy Azure dla obszaru roboczego w sieci wirtualnej, należy również zastosować następujący szablon usługi Azure Resource Manager. Ten szablon umożliwia obszarowi roboczemu komunikowanie się z acr za pomocą łącza prywatnego.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie środowisk szkoleniowych](how-to-set-up-training-targets.md)
* [Gdzie należy wdrażać modele](how-to-deploy-and-where.md)
* [Zabezpieczanie usługi sieci web za pośrednictwem usługi Azure Machine Learning za pomocą protokołu TLS](how-to-secure-web-service.md)
