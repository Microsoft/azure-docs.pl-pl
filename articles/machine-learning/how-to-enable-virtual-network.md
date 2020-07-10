---
title: Izolacja sieci & prywatność
titleSuffix: Azure Machine Learning
description: Używaj izolowanych Virtual Network platformy Azure z Azure Machine Learning do zabezpieczania eksperymentów/szkoleń oraz zadań wnioskowania/oceniania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 06/30/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 35938ca3b9d8f3aedd0892740a3dbfa0fb5b036a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186864"
---
# <a name="network-isolation-during-training--inference-with-private-virtual-networks"></a>Izolacja sieci podczas uczenia & wnioskowania z prywatnymi sieciami wirtualnymi
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zabezpieczyć cykl życia uczenia maszynowego, izolując Azure Machine Learning zadania szkoleniowe i wnioskowania w ramach usługi Azure Virtual Network (VNET). Azure Machine Learning opiera się na innych usługach platformy Azure dla zasobów obliczeniowych, nazywanych również [celami obliczeniowymi](concept-compute-target.md), do uczenia i wdrażania modeli. Obiekty docelowe można tworzyć w ramach sieci wirtualnej. Na przykład można użyć obliczeń Azure Machine Learning do uczenia modelu, a następnie wdrożenia modelu w usłudze Azure Kubernetes Service (AKS). 

__Sieć wirtualna__ działa jako granica zabezpieczeń, izolowanie zasobów platformy Azure od publicznego Internetu. Możesz również dołączyć do sieci wirtualnej platformy Azure do sieci lokalnej. Dzięki dołączeniu sieci można bezpiecznie uczenie modeli i uzyskać dostęp do wdrożonych modeli w celu wnioskowania.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Obszar roboczy](how-to-manage-workspace.md)Azure Machine Learning.

+ Ogólna wiedza o działaniu [usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i [sieci IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Wstępnie istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

## <a name="private-endpoints"></a>Prywatne punkty końcowe

Możesz również [włączyć link prywatny platformy Azure](how-to-configure-private-link.md) , aby połączyć się z obszarem roboczym przy użyciu prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej. [Dowiedz się, jak skonfigurować ten prywatny punkt końcowy.](how-to-configure-private-link.md)



> [!TIP]
> Możesz połączyć sieć wirtualną i połączenie prywatne, aby chronić komunikację między obszarem roboczym i innymi zasobami platformy Azure. Jednak niektóre kombinacje wymagają obszaru roboczego wersja Enterprise. Skorzystaj z poniższej tabeli, aby zrozumieć, jakie scenariusze wymagają wydania Enterprise Edition:
>
> | Scenariusz | Enterprise</br>bitowych | Podstawowa</br>bitowych |
> | ----- |:-----:|:-----:| 
> | Brak sieci wirtualnej lub prywatnego linku | ✔ | ✔ |
> | Obszar roboczy bez linku prywatnego. Inne zasoby (z wyjątkiem Azure Container Registry) w sieci wirtualnej | ✔ | ✔ |
> | Obszar roboczy bez linku prywatnego. Inne zasoby z linkiem prywatnym | ✔ | |
> | Obszar roboczy z linkiem prywatnym. Inne zasoby (z wyjątkiem Azure Container Registry) w sieci wirtualnej | ✔ | ✔ |
> | Obszar roboczy i wszystkie inne zasoby z linkiem prywatnym | ✔ | |
> | Obszar roboczy z linkiem prywatnym. Inne zasoby bez linku prywatnego lub sieci wirtualnej | ✔ | ✔ |
> | Azure Container Registry w sieci wirtualnej | ✔ | |
> | Klucze zarządzane przez klienta dla obszaru roboczego | ✔ | |
> 

> [!WARNING]
> 
> Azure Machine Learning wystąpienia obliczeniowe w wersji zapoznawczej nie są obsługiwane w obszarze roboczym, w którym włączono link prywatny.
>
> Azure Machine Learning nie obsługuje korzystania z usługi Azure Kubernetes, która ma włączone łącze prywatne. Zamiast tego można użyć usługi Azure Kubernetes w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie eksperymentów i zadań wnioskowania usługi Azure ml w ramach Virtual Network platformy Azure](how-to-enable-virtual-network.md).


<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

Jeśli dane są przechowywane w sieci wirtualnej, należy użyć [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) w obszarze roboczym, aby udzielić dostępu do danych w programie Studio.

> [!IMPORTANT]
> Chociaż większość z programu Studio współpracuje z danymi przechowywanymi w sieci wirtualnej, __nie są to__zintegrowane notesy. Zintegrowane notesy nie obsługują używania magazynu znajdującego się w sieci wirtualnej. Zamiast tego można użyć notesów Jupyter z wystąpienia obliczeniowego. Aby uzyskać więcej informacji, zobacz sekcję [dostęp do danych w notesie wystąpienia obliczeniowego](#access-data-in-a-compute-instance-notebook) .

Jeśli uzyskanie dostępu do programu Studio nie powiedzie się, zostanie wyświetlony `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` następujący błąd i wyłączono następujące operacje:

* Podgląd danych w Studio.
* Wizualizowanie danych w projektancie.
* Prześlij eksperyment AutoML.
* Rozpocznij projekt etykietowania.

Program Virtual Machines obsługuje odczytywanie danych z następujących typów magazynów w sieci wirtualnej:

* Obiekt bob Azure
* Usługa Azure Data Lake Storage 1. generacji
* Usługa Azure Data Lake Storage 2. generacji
* Azure SQL Database

### <a name="add-resources-to-the-virtual-network"></a>Dodawanie zasobów do sieci wirtualnej 

Dodaj obszar roboczy i konto magazynu do tej samej sieci wirtualnej, aby umożliwić im dostęp do siebie nawzajem.

1. Aby połączyć obszar roboczy z siecią wirtualną, [Włącz łącze prywatne platformy Azure](how-to-configure-private-link.md). Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a w regionach Południowo-środkowe stany USA 2, Stany USA — Europa Zachodnia.

1. Aby połączyć konto magazynu z siecią wirtualną, [Skonfiguruj ustawienia zapory i sieci wirtualnych](#use-a-storage-account-for-your-workspace).

### <a name="configure-a-datastore-to-use-managed-identity"></a>Konfigurowanie magazynu danych do korzystania z tożsamości zarządzanej

Po dodaniu obszaru roboczego i konta usługi magazynu do sieci wirtualnej należy skonfigurować magazyny danych do korzystania z tożsamości zarządzanej w celu uzyskania dostępu do Twoich potrzeb. Te kroki umożliwiają dodanie tożsamości zarządzanej przez obszar roboczy jako __czytnika__ do usługi magazynu przy użyciu kontroli dostępu opartej na zasobach (RBAC) platformy Azure. Dostęp __czytnika__ umożliwia pobranie ustawień zapory przez obszar roboczy i upewnienie się, że dane nie opuszczają sieci wirtualnej.

1. W programie Studio wybierz pozycję __magazyny__danych.

1. Aby utworzyć nowy magazyn danych, wybierz pozycję __+ nowy magazyn__danych. Aby zaktualizować istniejący, wybierz magazyn danych i wybierz pozycję __Aktualizuj poświadczenia__.

1. W ustawieniach magazynu danych wybierz opcję __tak__ , aby __umożliwić usłudze Azure Machine Learning dostęp do magazynu przy użyciu tożsamości zarządzanej w obszarze roboczym__.

> [!NOTE]
> Wprowadzenie zmian może potrwać do 10 minut.

### <a name="azure-blob-storage-blob-data-reader"></a>Czytnik danych obiektów blob magazynu obiektów blob platformy Azure

W przypadku __usługi Azure Blob Storage__tożsamość zarządzana w obszarze roboczym jest również dodawana jako [czytnik danych obiektów BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , dzięki czemu może odczytywać dane z magazynu obiektów BLOB.


### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 kontroli dostępu

Do kontrolowania dostępu do danych wewnątrz sieci wirtualnej można użyć list kontroli dostępu (ACL) typu RBAC i POSIX.

Aby użyć RBAC, należy dodać tożsamość zarządzaną obszaru roboczego do roli [czytnika danych obiektów BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Aby uzyskać więcej informacji, zobacz [Kontrola dostępu na podstawie ról](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

Aby używać list ACL, tożsamość zarządzana obszaru roboczego może być przypisana, podobnie jak jakakolwiek inna reguła zabezpieczeń. Aby uzyskać więcej informacji, zobacz [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).


### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 kontroli dostępu

Azure Data Lake Storage Gen1 obsługuje tylko listy kontroli dostępu w stylu POSIX. Można przypisać dostęp do tożsamości zarządzanego obszaru roboczego do zasobów tak samo jak w przypadku każdej innej zasady zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).


### <a name="azure-sql-database-contained-user"></a>Azure SQL Database zawarty użytkownik

Aby uzyskać dostęp do danych przechowywanych w Azure SQL Database przy użyciu tożsamości zarządzanej, należy utworzyć użytkownika programu SQL Server, który jest mapowany na tożsamość zarządzaną. Aby uzyskać więcej informacji na temat tworzenia użytkownika od dostawcy zewnętrznego, zobacz [Tworzenie zawartych użytkowników mapowanych na tożsamości usługi Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Po utworzeniu użytkownika zawartego w programie SQL Udziel uprawnień do niego przy użyciu [polecenia Udziel T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="connect-to-the-studio"></a>Nawiązywanie połączenia z programem Studio

Jeśli uzyskujesz dostęp do programu Studio z zasobu w sieci wirtualnej (na przykład wystąpienie obliczeniowe lub maszyna wirtualna), musisz zezwolić na ruch wychodzący z sieci wirtualnej do programu Studio. 

Na przykład, jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia ruchu wychodzącego, Dodaj regułę do miejsca docelowego __tagu usługi__ __AzureFrontDoor. frontonu__.

## <a name="use-a-storage-account-for-your-workspace"></a>Korzystanie z konta magazynu dla obszaru roboczego

> [!IMPORTANT]
> _Konto magazynu domyślnego_ można umieścić dla Azure Machine Learning lub _kont magazynu innych niż domyślne_ w sieci wirtualnej.
>
> Domyślne konto magazynu jest automatycznie inicjowane podczas tworzenia obszaru roboczego.
>
> W przypadku kont magazynu innych niż domyślne `storage_account` parametr w [ `Workspace.create()` funkcji](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) umożliwia określenie NIESTANDARDOWEGO konta magazynu według identyfikatora zasobu platformy Azure.

Aby użyć usługi Azure Storage dla obszaru roboczego w sieci wirtualnej, wykonaj następujące czynności:

1. Utwórz zasób obliczeniowy (na przykład wystąpienie obliczeniowe Machine Learning lub klaster) za siecią wirtualną lub Dołącz zasób obliczeniowy do obszaru roboczego (na przykład klaster usługi HDInsight, maszyna wirtualna lub klaster usług Azure Kubernetes). Zasób obliczeniowy może być przeznaczony do eksperymentowania lub wdrażania modelu.

   Aby uzyskać więcej informacji, zobacz sekcję [Korzystanie z Machine Learning obliczeń](#amlcompute), [Używanie maszyny wirtualnej lub klastra usługi HDInsight](#vmorhdi)oraz korzystanie z sekcji [Azure Kubernetes Service](#aksvnet) w tym artykule.

1. W Azure Portal przejdź do usługi magazynu, która ma być używana w obszarze roboczym.

   [![Magazyn połączony z obszarem roboczym Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stronie konto usługi magazynu wybierz pozycję __zapory i sieci wirtualne__.

   ![Obszar "zapory i sieci wirtualne" na stronie usługi Azure Storage w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na stronie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    - Wybierz pozycję __Wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz łącze __Dodaj istniejące sieci wirtualne__ . Ta akcja powoduje dodanie sieci wirtualnej, w której znajdują się obliczenia (zobacz krok 1).

        > [!IMPORTANT]
        > Konto magazynu musi znajdować się w tej samej sieci wirtualnej i podsieci co wystąpienia obliczeniowe lub klastry używane do uczenia lub wnioskowania.

    - Zaznacz pole wyboru __Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu__ .

    > [!IMPORTANT]
    > Podczas pracy z zestawem SDK Azure Machine Learning środowisko programistyczne musi mieć możliwość nawiązania połączenia z kontem usługi Azure Storage. Gdy konto magazynu znajduje się w sieci wirtualnej, zapora musi zezwalać na dostęp ze swojego adresu IP środowiska deweloperskiego.
    >
    > Aby włączyć dostęp do konta magazynu, odwiedź __zapory i sieci wirtualne__ dla konta magazynu *z przeglądarki sieci Web na kliencie deweloperskim*. Następnie użyj pola wyboru __Dodaj adres IP klienta__ , aby dodać adres IP klienta do __zakresu adresów__. Możesz również użyć pola __zakres adresów__ , aby ręcznie wprowadzić adres IP środowiska deweloperskiego. Po dodaniu adresu IP klienta może on uzyskać dostęp do konta magazynu przy użyciu zestawu SDK.

   [![Okienko "zapory i sieci wirtualne" w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="use-datastores-and-datasets"></a>Korzystanie z magazynów danych i DataSets

W tej sekcji omówiono użycie magazynu danych i zestawu elementów DataSet dla środowiska zestawu SDK. Aby uzyskać więcej informacji na temat środowiska Studio, zapoznaj się z sekcją [Machine Learning Studio](#machine-learning-studio).

Domyślnie Azure Machine Learning sprawdza ważność danych i sprawdzanie poświadczeń podczas próby dostępu do danych za pomocą zestawu SDK. Jeśli dane znajdują się za siecią wirtualną, Azure Machine Learning nie mogą uzyskać dostępu do danych i przetworzyć ich testy. Aby tego uniknąć, należy utworzyć magazyny danych i zestawy DataSet, które pomijają weryfikację.

### <a name="use-a-datastore"></a>Korzystanie z magazynu danych

 Azure Data Lake Store Gen1 i Azure Data Lake Store Gen2 Pomijaj walidację domyślnie, więc nie są wymagane żadne dalsze działania. Jednak dla następujących usług można użyć podobnej składni, aby pominąć walidację magazynu danych:

- Azure Blob Storage
- Udział plików platformy Azure
- PostgreSQL
- Azure SQL Database

Poniższy przykład kodu tworzy nowy magazyn danych obiektów blob platformy Azure `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-a-dataset"></a>Korzystanie z zestawu danych

Składnia do pomijania walidacji zestawu danych jest podobna do następujących typów zestawów danych:
- Rozdzielany plik
- JSON 
- Parquet
- SQL
- Plik

Poniższy kod tworzy nowy zestaw danych JSON i zestawy `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Wystąpienia klastrów obliczeniowych & 

Aby można było użyć [zarządzanego __obiektu docelowego obliczeń__ Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) lub [ __wystąpienia__ obliczeniowego Azure Machine Learning](concept-compute-instance.md) w sieci wirtualnej, należy spełnić następujące wymagania dotyczące sieci:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
> * Podsieć określona dla wystąpienia obliczeniowego lub klastra musi mieć wystarczającą liczbę nieprzypisanych adresów IP w celu uwzględnienia liczby elementów, które są przeznaczone dla maszyny wirtualnej. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster obliczeniowy zostanie częściowo przydzielony.
> * Sprawdź, czy zasady zabezpieczeń lub blokady w ramach subskrypcji lub grupy zasobów sieci wirtualnej ograniczają uprawnienia do zarządzania siecią wirtualną. Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu, pozostaw kilka otwartych portów dla usługi obliczeniowej. Aby uzyskać więcej informacji, zobacz sekcję [wymagane porty](#mlcports) .
> * Jeśli chcesz umieścić wiele wystąpień obliczeniowych lub klastrów w jednej sieci wirtualnej, może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z zasobów.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co Azure Machine Learning wystąpienie obliczeniowe lub klaster. 
> * Aby funkcja Jupyter wystąpienia obliczeniowego działała, należy się upewnić, że komunikacja gniazda internetowego nie jest wyłączona.

> [!TIP]
> Wystąpienie obliczeniowe Machine Learning lub klaster automatycznie przydziela dodatkowe zasoby sieciowe __do grupy zasobów zawierającej sieć wirtualną__. Dla każdego wystąpienia obliczeniowego lub klastra usługa przydziela następujące zasoby:
> 
> * Jedna sieciowa Grupa zabezpieczeń
> * Jeden publiczny adres IP
> * Jeden moduł równoważenia obciążenia
> 
> W przypadku klastrów te zasoby są usuwane (i tworzone ponownie) za każdym razem, gdy klaster jest skalowany w dół do 0 węzłów, jednak w przypadku wystąpienia, w którym znajdują się zasoby, do momentu całkowitego usunięcia wystąpienia. zatrzymywanie nie powoduje usunięcia zasobów. 
> Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) subskrypcji.


### <a name="required-ports"></a><a id="mlcports"></a>Wymagane porty

Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu sieciowego do/z publicznej sieci Internet, musisz zezwolić na komunikację przychodzącą z usługi Azure Batch.

Usługa Batch dodaje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) na poziomie interfejsów sieciowych dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

- Ruch przychodzący TCP na portach 29876 i 29877 z __tagu usługi__ __BatchNodeManagement__.

    ![Reguła przychodząca korzystająca z tagu usługi BatchNodeManagement](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Obowiązkowe Ruch przychodzący TCP na porcie 22, aby zezwolić na dostęp zdalny. Tego portu należy używać tylko w przypadku, gdy chcesz nawiązać połączenie przy użyciu protokołu SSH w publicznym adresie IP.

- Ruch wychodzący na dowolny port do sieci wirtualnej.

- Ruch wychodzący na dowolny port do Internetu.

- Dla ruchu przychodzącego TCP wystąpienia obliczeniowego na porcie 44224 z __tagu usługi__ __AzureMachineLearning__.

> [!IMPORTANT]
> Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli sieciowej grupy zabezpieczeń blokuje komunikację z węzłami obliczeniowymi, usługa COMPUTE ustawia stan węzłów obliczeniowych na niezdatny do użytku.
>
> Nie musisz określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Azure Batch konfiguruje własny sieciowych grup zabezpieczeń. Jeśli jednak podsieć zawierająca Azure Machine Learning COMPUTE ma skojarzone sieciowych grup zabezpieczeń lub zaporę, należy również zezwolić na wymieniony wcześniej ruch.

Konfiguracja reguły sieciowej grupy zabezpieczeń w Azure Portal przedstawiono na następujących obrazach:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego dla środowisko obliczeniowe usługi Machine Learning" border="true":::



![Wychodzące reguły sieciowej grupy zabezpieczeń dla środowisko obliczeniowe usługi Machine Learning](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Ogranicz łączność wychodzącą z sieci wirtualnej

Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, wykonaj następujące czynności:

- Odmowa wychodzącego połączenia internetowego przy użyciu reguł sieciowej grupy zabezpieczeń.

- W przypadku __wystąpienia obliczeniowego__ lub __klastra obliczeniowego__należy ograniczyć ruch wychodzący do następujących elementów:
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

### <a name="user-defined-routes-for-forced-tunneling"></a>Trasy zdefiniowane przez użytkownika dla tunelowania wymuszonego

Jeśli używasz wymuszonego tunelowania przy użyciu środowisko obliczeniowe usługi Machine Learning, Dodaj [trasy zdefiniowane przez użytkownika (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsieci zawierającej zasób obliczeniowy.

* Ustanów UDR dla każdego adresu IP, który jest używany przez usługę Azure Batch w regionie, w którym znajdują się zasoby. Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi w celu planowania zadań. Należy również dodać adres IP dla usługi Azure Machine Learning, w której znajdują się zasoby, ponieważ jest to wymagane w celu uzyskania dostępu do wystąpień obliczeniowych. Aby uzyskać listę adresów IP usługi Batch i usługi Azure Machine Learning, należy użyć jednej z następujących metod:

    * Pobierz [zakresy adresów IP i Tagi usług platformy Azure](https://www.microsoft.com/download/details.aspx?id=56519) , a następnie wyszukaj plik dla `BatchNodeManagement.<region>` i `AzureMachineLearning.<region>` , gdzie `<region>` jest Twoim regionem świadczenia usługi Azure.

    * Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , aby pobrać te informacje. Poniższy przykład pobiera informacje o adresie IP i filtruje informacje dla regionu Wschodnie stany USA 2:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* Ruch wychodzący do usługi Azure Storage nie może być blokowany przez lokalne urządzenie sieciowe. W odróżnieniu od tego adresy URL mają postać `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` i `<account>.blob.core.windows.net` .

Po dodaniu UDR, zdefiniuj trasę dla każdego powiązanego prefiksu adresu IP partii i ustaw __Typ następnego przeskoku__ na __Internet__. Na poniższej ilustracji przedstawiono przykład tego UDR w Azure Portal:

![Przykład UDR dla prefiksu adresu](./media/how-to-enable-virtual-network/user-defined-route.png)

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

### <a name="access-data-in-a-compute-instance-notebook"></a>Dostęp do danych w notesie wystąpienia obliczeniowego

Jeśli używasz notesów w wystąpieniu obliczeniowym platformy Azure, musisz się upewnić, że Notes jest uruchomiony w zasobie obliczeniowym za tą samą siecią wirtualną i podsiecią, co dane. 

Wystąpienie obliczeniowe należy skonfigurować tak, aby znajdować się w tej samej sieci wirtualnej podczas tworzenia w obszarze **Ustawienia zaawansowane**  >  **Skonfiguruj sieć wirtualną**. Nie można dodać istniejącego wystąpienia obliczeniowego do sieci wirtualnej.

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Aby dodać usługę Azure Kubernetes Service (AKS) do obszaru roboczego w sieci wirtualnej, wykonaj następujące czynności:

> [!IMPORTANT]
> Przed rozpoczęciem poniższej procedury postępuj zgodnie z wymaganiami wstępnymi w temacie [Konfigurowanie zaawansowanej sieci w usłudze Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) How-to i planowanie adresowania IP klastra.
>
> Wystąpienie AKS i Sieć wirtualna platformy Azure muszą znajdować się w tym samym regionie. W przypadku zabezpieczenia kont usługi Azure Storage używanych przez obszar roboczy w sieci wirtualnej, muszą one znajdować się w tej samej sieci wirtualnej co wystąpienie AKS.

> [!WARNING]
> Azure Machine Learning nie obsługuje korzystania z usługi Azure Kubernetes, która ma włączone łącze prywatne.

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/), a następnie wybierz swoją subskrypcję i obszar roboczy.

1. Wybierz pozycję __obliczenia__ po lewej stronie.

1. Wybierz z centrum pozycję __klastry wnioskowania__ , a następnie wybierz pozycję __+__ .

1. W oknie dialogowym __nowy klaster wnioskowania__ wybierz pozycję __Zaawansowane__ w obszarze __Konfiguracja sieci__.

1. Aby skonfigurować ten zasób obliczeniowy do korzystania z sieci wirtualnej, wykonaj następujące czynności:

    1. Z listy rozwijanej __Grupa zasobów__ wybierz grupę zasobów zawierającą sieć wirtualną.
    1. Z listy rozwijanej __Sieć wirtualna__ wybierz sieć wirtualną, która zawiera podsieć.
    1. Z listy rozwijanej __podsieć__ wybierz podsieć.
    1. W polu __zakres adresów usługi Kubernetes__ Wprowadź zakres adresów usługi Kubernetes. Ten zakres adresów używa zakresu adresów IP notacji CIDR (Classless Inter-Domain Routing) do definiowania adresów IP, które są dostępne dla klastra. Nie może się nakładać na żadne zakresy adresów IP podsieci (na przykład 10.0.0.0/16).
    1. W polu __adres IP usługi KUBERNETES DNS__ wprowadź adres IP usługi DNS Kubernetes. Ten adres IP jest przypisywany do usługi DNS Kubernetes. Musi ona należeć do zakresu adresów usługi Kubernetes (na przykład 10.0.0.10).
    1. W polu __adres mostka platformy Docker__ wprowadź adres mostka platformy Docker. Ten adres IP jest przypisany do mostka platformy Docker. Nie może być w żadnym z zakresów adresów IP podsieci lub zakres adresów usługi Kubernetes (na przykład 172.17.0.1/16).

   ![Azure Machine Learning: środowisko obliczeniowe usługi Machine Learning ustawień sieci wirtualnej](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Upewnij się, że grupa sieciowej grupy zabezpieczeń kontrolująca sieć wirtualną ma włączoną regułę zabezpieczeń dla punktu końcowego oceniania, tak aby można ją było wywołać spoza sieci wirtualnej.
   > [!IMPORTANT]
   > Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Reguła zabezpieczeń dla ruchu przychodzącego](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

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

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Używanie prywatnych adresów IP w usłudze Azure Kubernetes Service

Domyślnie publiczny adres IP jest przypisywany do wdrożeń AKS. W przypadku używania AKS wewnątrz sieci wirtualnej można zamiast tego użyć prywatnego adresu IP. Prywatne adresy IP są dostępne tylko z wewnątrz sieci wirtualnej lub połączonych sieci.

Prywatny adres IP jest włączony przez skonfigurowanie AKS do korzystania z _wewnętrznego modułu równoważenia obciążenia_. 

> [!IMPORTANT]
> Nie można włączyć prywatnego adresu IP podczas tworzenia klastra usługi Azure Kubernetes. Musi być włączona jako aktualizacja istniejącego klastra.

Poniższy fragment kodu przedstawia sposób __tworzenia nowego klastra AKS__, a następnie aktualizowania go do korzystania z prywatnego modułu równoważenia obciążenia IP/wewnętrznego:

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
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

Zawartość `body.json` pliku, do którego odwołuje się polecenie, jest podobna do następującego dokumentu JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
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

W przypadku __dołączania istniejącego klastra__ do obszaru roboczego należy poczekać, aż po zakończeniu operacji dołączania usługa równoważenia obciążenia zostanie skonfigurowana.

Aby uzyskać informacje na temat dołączania klastra, zobacz [dołączanie istniejącego klastra AKS](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster).

Po dołączeniu istniejącego klastra można zaktualizować klaster tak, aby korzystał z prywatnego adresu IP.

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

__Rola współautor sieci__

> [!IMPORTANT]
> Jeśli utworzysz lub dołączysz klaster AKS, dostarczając wcześniej utworzoną sieć wirtualną, należy przyznać jednostce usługi (SP) lub tożsamość zarządzaną dla klastra AKS rolę _współautor sieci_ do grupy zasobów zawierającej sieć wirtualną. Należy to zrobić przed podjęciem próby zmiany wewnętrznego modułu równoważenia obciążenia na prywatny adres IP.
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
Aby uzyskać więcej informacji na temat używania wewnętrznego modułu równoważenia obciążenia z programem AKS, zobacz [Korzystanie z wewnętrznego modułu równoważenia obciążenia z usługą Azure Kubernetes Service](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Użyj Azure Container Instances (ACI)

Azure Container Instances są tworzone dynamicznie podczas wdrażania modelu. Aby umożliwić Azure Machine Learning tworzenia ACI wewnątrz sieci wirtualnej, należy włączyć __delegowanie podsieci__ dla podsieci używanej przez wdrożenie.

> [!WARNING]
> W przypadku korzystania z Azure Container Instances w sieci wirtualnej Sieć wirtualna musi znajdować się w tej samej grupie zasobów co obszar roboczy Azure Machine Learning.
>
> W przypadku korzystania z Azure Container Instances wewnątrz sieci wirtualnej Azure Container Registry (ACR) dla obszaru roboczego nie może być również w sieci wirtualnej.

Aby użyć ACI w sieci wirtualnej z obszarem roboczym, wykonaj następujące czynności:

1. Aby włączyć delegowanie podsieci w sieci wirtualnej, Skorzystaj z informacji zawartych w artykule [Dodawanie lub usuwanie delegowania podsieci](../virtual-network/manage-subnet-delegation.md) . Delegowanie można włączyć podczas tworzenia sieci wirtualnej lub dodać je do istniejącej sieci.

    > [!IMPORTANT]
    > Podczas włączania delegowania Użyj `Microsoft.ContainerInstance/containerGroups` wartości jako __delegowanej podsieci do usługi__ .

2. Wdróż model przy użyciu [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), użyj `vnet_name` parametrów i `subnet_name` . Ustaw te parametry na nazwę sieci wirtualnej i podsieć, w której włączono delegowanie.

## <a name="azure-firewall"></a>Azure Firewall

Aby uzyskać informacje na temat używania Azure Machine Learning z zaporą platformy Azure, zobacz [Korzystanie z Azure Machine Learning obszaru roboczego za zaporą platformy Azure](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Azure Container Registry

> [!IMPORTANT]
> Azure Container Registry (ACR) można umieścić wewnątrz sieci wirtualnej, jednak muszą być spełnione następujące wymagania wstępne:
>
> * Obszar roboczy Azure Machine Learning musi być w wersji Enterprise Edition. Informacje o uaktualnianiu programu znajdują się w temacie [Upgrade to Enterprise Edition](how-to-manage-workspace.md#upgrade).
> * Azure Container Registry musi być w wersji Premium. Aby uzyskać więcej informacji na temat uaktualniania, zobacz [Zmiana jednostek SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Azure Container Registry musi znajdować się w tej samej sieci wirtualnej i podsieci co konto magazynu i cele obliczeniowe używane do uczenia lub wnioskowania.
> * Obszar roboczy Azure Machine Learning musi zawierać [Azure Machine Learning klaster obliczeniowy](how-to-set-up-training-targets.md#amlcompute).
>
>     Gdy ACR znajduje się za siecią wirtualną, Azure Machine Learning nie może użyć jej do bezpośredniego tworzenia obrazów platformy Docker. Zamiast tego klaster obliczeniowy jest używany do kompilowania obrazów.

1. Aby znaleźć nazwę Azure Container Registry obszaru roboczego, użyj jednej z następujących metod:

    __Witryna Azure Portal__

    W sekcji przegląd obszaru roboczego wartość __rejestru__ łączy się z Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry obszaru roboczego" border="true":::

    __Interfejs wiersza polecenia platformy Azure__

    Jeśli [zainstalowano rozszerzenie Machine Learning dla interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md), możesz użyć `az ml workspace show` polecenie, aby wyświetlić informacje o obszarze roboczym.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    To polecenie zwraca wartość podobną do `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Ostatnia część ciągu jest nazwą Azure Container Registry obszaru roboczego.

1. Aby ograniczyć dostęp do sieci wirtualnej, wykonaj kroki opisane w temacie [Konfigurowanie dostępu do sieci dla rejestru](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Podczas dodawania sieci wirtualnej wybierz sieć wirtualną i podsieć dla zasobów Azure Machine Learning.

1. Użyj Azure Machine Learning Python SDK, aby skonfigurować klaster obliczeniowy do tworzenia obrazów platformy Docker. Poniższy fragment kodu ilustruje, jak to zrobić:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Twoje konto magazynu, klaster obliczeniowy i Azure Container Registry muszą znajdować się w tej samej podsieci sieci wirtualnej.
    
    Aby uzyskać więcej informacji, zobacz informacje o metodzie [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

1. Należy zastosować następujący szablon Azure Resource Manager. Ten szablon umożliwia obszarowi roboczemu komunikowanie się z ACR.

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

## <a name="key-vault-instance"></a>Wystąpienie magazynu kluczy 

Wystąpienie magazynu kluczy skojarzone z obszarem roboczym jest używane przez Azure Machine Learning do przechowywania następujących poświadczeń:
* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia do magazynów danych

Aby korzystać z funkcji eksperymentowania Azure Machine Learning z Azure Key Vault za siecią wirtualną, wykonaj następujące czynności:

1. Przejdź do magazynu kluczy skojarzonego z obszarem roboczym.

   [![Magazyn kluczy skojarzony z obszarem roboczym Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. Na stronie __Key Vault__ w lewym okienku wybierz pozycję __zapory i sieci wirtualne__.

   ![Sekcja "zapory i sieci wirtualne" w okienku Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Na stronie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    - W obszarze __Zezwalaj na dostęp z__, wybierz opcję __wybrane sieci__.
    - W obszarze __sieci wirtualne__wybierz pozycję __Dodaj istniejące sieci wirtualne__ , aby dodać sieć wirtualną, w której znajduje się obliczenie eksperymentu.
    - W obszarze __Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory__wybierz pozycję __tak__.

   [![Sekcja "zapory i sieci wirtualne" w okienku Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Aby używać Azure Databricks w sieci wirtualnej z obszarem roboczym, muszą zostać spełnione następujące wymagania:

> [!div class="checklist"]
> * Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
> * Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co klaster Azure Databricks.
> * Poza niektórymi __datakostki — prywatnymi__ i niestandardowymi __—__ podsieciami używanymi Azure Databricks, wymagana jest również __Domyślna__ podsieć utworzona dla sieci wirtualnej.

Aby uzyskać szczegółowe informacje na temat używania Azure Databricks z siecią wirtualną, zobacz [wdrażanie Azure Databricks w Virtual Network platformy Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Maszyna wirtualna lub klaster usługi HDInsight

> [!IMPORTANT]
> Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu.

Aby użyć maszyny wirtualnej lub klastra usługi Azure HDInsight w sieci wirtualnej z obszarem roboczym, wykonaj następujące czynności:

1. Utwórz maszynę wirtualną lub klaster usługi HDInsight przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure i umieść klaster w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz następujące artykuły:
    * [Tworzenie sieci wirtualnych platformy Azure dla maszyn wirtualnych z systemem Linux i zarządzanie nimi](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Zwiększanie usługi HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Aby zezwolić Azure Machine Learning na komunikowanie się z portem SSH na maszynie wirtualnej lub w klastrze, skonfiguruj wpis źródła dla sieciowej grupy zabezpieczeń. Port SSH zazwyczaj jest portem 22. Aby zezwolić na ruch z tego źródła, wykonaj następujące czynności:

    * Z listy rozwijanej __Źródło__ wybierz pozycję __tag usługi__.

    * Z listy rozwijanej __tag usługi źródłowej__ wybierz pozycję __AzureMachineLearning__.

    * Z listy rozwijanej __zakresy portów źródłowych__ wybierz pozycję __*__ .

    * Z listy rozwijanej __Lokalizacja docelowa__ wybierz __dowolne__.

    * Z listy rozwijanej __zakresy portów docelowych__ wybierz pozycję __22__.

    * W obszarze __Protokół__wybierz opcję __dowolny__.

    * W obszarze __Akcja__wybierz pozycję __Zezwalaj__.

   ![Reguły ruchu przychodzącego na potrzeby przeprowadzania eksperymentów na maszynie wirtualnej lub w klastrze usługi HDInsight w sieci wirtualnej](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, zobacz sekcję [ograniczanie łączności wychodzącej z sieci wirtualnej](#limiting-outbound-from-vnet) .

1. Dołącz maszynę wirtualną lub klaster usługi HDInsight do obszaru roboczego Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie środowisk szkoleniowych](how-to-set-up-training-targets.md)
* [Skonfiguruj prywatne punkty końcowe](how-to-configure-private-link.md)
* [Gdzie należy wdrażać modele](how-to-deploy-and-where.md)
* [Użyj protokołu TLS do zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](how-to-secure-web-service.md)
