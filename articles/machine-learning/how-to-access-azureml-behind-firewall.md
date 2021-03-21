---
title: Korzystanie z zapory
titleSuffix: Azure Machine Learning
description: Kontroluj dostęp do obszarów roboczych Azure Machine Learning przy użyciu zapór platformy Azure. Dowiedz się więcej o hostach, które muszą być dozwolone przez zaporę.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 295228e9eaa3529b05055869bd46f9aefc938a6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212777"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Używanie obszaru roboczego za zaporą dla Azure Machine Learning

W tym artykule dowiesz się, jak skonfigurować zaporę platformy Azure w celu kontrolowania dostępu do obszaru roboczego Azure Machine Learning i publicznego Internetu. Aby dowiedzieć się więcej na temat zabezpieczania Azure Machine Learning, zobacz [zabezpieczenia przedsiębiorstwa dla Azure Machine Learning](concept-enterprise-security.md).

> [!WARNING]
> Dostęp do magazynu danych za zaporą jest obsługiwany tylko w pierwszym działaniu kodu. Używanie [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) do uzyskiwania dostępu do danych za zaporą nie jest obsługiwane. Aby można było korzystać z magazynu danych w sieci prywatnej przy użyciu programu Studio, należy najpierw [skonfigurować sieć wirtualną](../virtual-network/quick-create-portal.md) i [udostępnić programowi Studio dostęp do danych przechowywanych w sieci wirtualnej](how-to-enable-studio-virtual-network.md).

## <a name="azure-firewall"></a>Azure Firewall

W przypadku korzystania z zapory platformy Azure należy używać funkcji __translacji adresów sieciowych (DNAT)__ do tworzenia reguł NAT dla ruchu przychodzącego. Dla ruchu wychodzącego Utwórz reguły __sieci__ i/lub __aplikacji__ . Te kolekcje reguł są szczegółowo opisane w [artykule Co to są niektóre pojęcia związane z zaporą platformy Azure](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Konfiguracja przychodząca

Jeśli używasz __wystąpienia obliczeniowego__ Azure Machine Learning lub __klastra obliczeniowego__, Dodaj [zdefiniowane przez użytkownika trasy (UDR)](../virtual-network/virtual-networks-udr-overview.md) dla podsieci, która zawiera zasoby Azure Machine Learning. Ta trasa wymusza ruch __z__ adresów IP `BatchNodeManagement` i `AzureMachineLearning` zasobów do publicznego adresu IP wystąpienia obliczeniowego i klastra obliczeniowego.

Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi w celu planowania zadań. Należy również dodać adres IP dla usługi Azure Machine Learning, ponieważ jest to wymagane w celu uzyskania dostępu do wystąpień obliczeniowych. Podczas dodawania adresu IP dla usługi Azure Machine Learning należy dodać adres IP zarówno dla __podstawowego, jak i pomocniczego__ regionu platformy Azure. Region podstawowy, w którym znajduje się obszar roboczy.

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

Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch w sieci wirtualnej](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Konfiguracja wychodząca

1. Dodaj __reguły sieci__, zezwalając __na ruch do__ i __z__ następujących tagów usługi:

    * Usługi azureactivedirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage. region
    * Magazyn kluczy. region
    * ContainerRegistry. region

    Jeśli planujesz użycie domyślnych obrazów platformy Docker dostarczonych przez firmę Microsoft i włączenie zależności zarządzanych przez użytkownika, należy również dodać następujące znaczniki usługi:

    * MicrosoftContainerRegistry. region
    * AzureFrontDoor.FirstParty

    W przypadku wpisów zawierających `region` , Zamień na region platformy Azure, którego używasz. Na przykład `keyvault.westus`.

    W polu __Protokół__ wybierz opcję `TCP` . W polu __porty__ źródłowe i docelowe wybierz opcję `*` .

1. Dodaj __reguły aplikacji__ dla następujących hostów:

    > [!NOTE]
    > Nie jest to pełna lista hostów wymaganych dla wszystkich zasobów w języku Python w Internecie, które są najczęściej używane. Na przykład jeśli potrzebujesz dostępu do repozytorium GitHub lub innego hosta, musisz zidentyfikować i dodać wymagane hosty dla tego scenariusza.

    | **Nazwa hosta** | **Cel** |
    | ---- | ---- |
    | **graph.windows.net** | Używany przez Azure Machine Learning wystąpienie/klaster obliczeniowy. |
    | **anaconda.com**</br>**\*. anaconda.com** | Służy do instalowania pakietów domyślnych. |
    | **\*. anaconda.org** | Służy do pobierania danych repozytorium. |
    | **pypi.org** | Służy do wyświetlania listy zależności od domyślnego indeksu (jeśli istnieje), a indeks nie jest zastępowany przez ustawienia użytkownika. Jeśli indeks jest zastępowany, należy również zezwolić na **\* . pythonhosted.org**. |
    | **cloud.r-project.org** | Używane podczas instalowania pakietów CRAN na potrzeby programowania w języku R. |
    | **\*pytorch.org** | Używane przez kilka przykładów w oparciu o PyTorch. |
    | **\*. tensorflow.org** | Używane przez kilka przykładów w oparciu o Tensorflow. |

    W przypadku __protokołu: Port__ wybierz opcję Użyj __protokołu HTTP i https__.

    Aby uzyskać więcej informacji na temat konfigurowania reguł aplikacji, zobacz [wdrażanie i Konfigurowanie zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Aby ograniczyć dostęp do modeli wdrożonych w usłudze Azure Kubernetes Service (AKS), zobacz [ograniczanie ruchu wychodzącego w usłudze Azure Kubernetes](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Inne zapory

Wskazówki zawarte w tej sekcji są ogólne, ponieważ każda Zapora ma własną terminologię i konkretne konfiguracje. Jeśli masz pytania dotyczące sposobu zezwalania na komunikację za pośrednictwem zapory, zapoznaj się z dokumentacją używanej zapory.

Jeśli nie skonfigurowano go prawidłowo, Zapora może spowodować problemy z używaniem obszaru roboczego. Istnieją różne nazwy hostów, które są używane w obszarze roboczym Azure Machine Learning. W poniższych sekcjach znajdują się hosty, które są wymagane do Azure Machine Learning.

### <a name="microsoft-hosts"></a>Hosty firmy Microsoft

Hosty w tej sekcji należą do firmy Microsoft i zapewniają usługi wymagane do prawidłowego działania obszaru roboczego. W poniższych tabelach wymieniono nazwy hostów dla publicznych, Azure Government i Chin platformy Azure w regionach 21Vianet.

**Ogólne hosty platformy Azure**

| **Wymagane przez** | **Azure — publiczna** | **Azure Government** | **Azure w Chinach — 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure Portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Hosty Azure Machine Learning**

| **Wymagane przez** | **Azure — publiczna** | **Azure Government** | **Azure w Chinach — 21Vianet** |
| ----- | ----- | ----- | ----- |
| Studio uczenia maszynowego Azure | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| Interfejs API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Eksperymentowanie, historia, moje napędy, etykietowanie | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Zarządzanie modelem | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Potok | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Projektant (usługa Studio) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Zintegrowany Notes | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Zintegrowany Notes | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Zintegrowany Notes | \*. dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Zintegrowany Notes | \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Zintegrowany Notes | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Zintegrowany Notes | \*. aznbcontent.net |  | |

**Azure Machine Learning wystąpienia obliczeniowe i hosty klastra obliczeniowego**

| **Wymagane przez** | **Azure — publiczna** | **Azure Government** | **Azure w Chinach — 21Vianet** |
| ----- | ----- | ----- | ----- |
| Klaster obliczeniowy/wystąpienie | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| Klaster obliczeniowy/wystąpienie | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Wystąpienie obliczeniowe | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| Wystąpienie obliczeniowe | \*. instances.azureml.ms |  |  |

**Skojarzone zasoby używane przez Azure Machine Learning**

| **Wymagane przez** | **Azure — publiczna** | **Azure Government** | **Azure w Chinach — 21Vianet** |
| ----- | ----- | ----- | ----- |
| Konto usługi Azure Storage | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Container Registry firmy Microsoft | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Jeśli planujesz korzystanie z tożsamości federacyjnych, postępuj zgodnie z [najlepszymi rozwiązaniami dotyczącymi zabezpieczania Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) artykułu.

Ponadto należy użyć informacji z [wymuszonego tunelowania](how-to-secure-training-vnet.md#forced-tunneling) , aby dodać adresy IP dla `BatchNodeManagement` i `AzureMachineLearning` .

Aby uzyskać informacje na temat ograniczania dostępu do modeli wdrożonych w usłudze Azure Kubernetes Service (AKS), zobacz [ograniczanie ruchu wychodzącego w usłudze Azure Kubernetes](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Hosty języka Python

Hosty w tej sekcji służą do instalowania pakietów języka Python. Są one wymagane podczas opracowywania, uczenia i wdrażania. 

> [!NOTE]
> Nie jest to pełna lista hostów wymaganych dla wszystkich zasobów w języku Python w Internecie, które są najczęściej używane. Na przykład jeśli potrzebujesz dostępu do repozytorium GitHub lub innego hosta, musisz zidentyfikować i dodać wymagane hosty dla tego scenariusza.

| **Nazwa hosta** | **Cel** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Służy do instalowania pakietów domyślnych. |
| **\*. anaconda.org** | Służy do pobierania danych repozytorium. |
| **pypi.org** | Służy do wyświetlania listy zależności od domyślnego indeksu (jeśli istnieje), a indeks nie jest zastępowany przez ustawienia użytkownika. Jeśli indeks jest zastępowany, należy również zezwolić na **\* . pythonhosted.org**. |
| **\*pytorch.org** | Używane przez kilka przykładów w oparciu o PyTorch. |
| **\*. tensorflow.org** | Używane przez kilka przykładów w oparciu o Tensorflow. |

### <a name="r-hosts"></a>Hosty języka R

Hosty w tej sekcji służą do instalowania pakietów języka R. Są one wymagane podczas opracowywania, uczenia i wdrażania.

> [!NOTE]
> Nie jest to kompletna lista hostów wymaganych dla wszystkich zasobów R w Internecie, które są najczęściej używane. Na przykład jeśli potrzebujesz dostępu do repozytorium GitHub lub innego hosta, musisz zidentyfikować i dodać wymagane hosty dla tego scenariusza.

| **Nazwa hosta** | **Cel** |
| ---- | ---- |
| **cloud.r-project.org** | Używane podczas instalowania pakietów CRAN. |

> [!IMPORTANT]
> Wewnętrznie zestaw R SDK dla Azure Machine Learning używa pakietów języka Python. W związku z tym należy również zezwolić hostom języka Python przez zaporę.
## <a name="next-steps"></a>Następne kroki

* [Samouczek: wdrażanie i konfigurowanie usługi Azure Firewall w witrynie Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Zabezpieczanie eksperymentów usługi Azure Machine Learning i zadań wnioskowania w ramach sieci wirtualnej platformy Azure](how-to-network-security-overview.md)
