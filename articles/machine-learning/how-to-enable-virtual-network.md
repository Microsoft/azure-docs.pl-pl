---
title: Izolacja sieci & prywatność
titleSuffix: Azure Machine Learning
description: Używaj izolowanych Virtual Network platformy Azure z Azure Machine Learning do zabezpieczania eksperymentów/szkoleń oraz zadań wnioskowania/oceniania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121208"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Izolacja sieci podczas szkoleń i wnioskowania z prywatnymi punktami końcowymi i sieciami wirtualnymi
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zabezpieczyć cykl życia uczenia maszynowego, izolując Azure Machine Learning zadania szkoleniowe i wnioskowania w ramach usługi Azure Virtual Network (VNET). __Sieć wirtualna__ działa jako granica zabezpieczeń, izolowanie zasobów platformy Azure od publicznego Internetu. Możesz również dołączyć do sieci wirtualnej platformy Azure do sieci lokalnej. Dzięki dołączeniu sieci można bezpiecznie uczenie modeli i uzyskać dostęp do wdrożonych modeli w celu wnioskowania.

Do obszaru roboczego Azure Machine Learning można uzyskać dostęp z sieci wirtualnej przy użyciu __prywatnego punktu końcowego__. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej, a dostęp do obszaru roboczego jest ograniczony do sieci wirtualnej. Prywatny punkt końcowy pomaga ograniczyć ryzyko związane z eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [prywatny link do platformy Azure](/azure/private-link/private-link-overview) .

> [!NOTE]
> W przypadku korzystania z przeglądarki Mozilla Firefox mogą wystąpić problemy z dostępem do obszaru roboczego za pośrednictwem prywatnego punktu końcowego. Problem może być związany z ustawieniem DNS over HTTPS w przeglądarce. Zalecamy korzystanie z przeglądarki Microsoft Edge lub Google Chrome w celu obejścia tego problemu.

Azure Machine Learning korzysta z innych usług platformy Azure na potrzeby przechowywania danych i zasobów obliczeniowych (używanych do uczenia i wdrażania modeli). Te zasoby można również utworzyć w ramach sieci wirtualnej. Na przykład można użyć obliczeń Azure Machine Learning do uczenia modelu, a następnie wdrożenia modelu w usłudze Azure Kubernetes Service (AKS). 

## <a name="prerequisites"></a>Wymagania wstępne

+ [Obszar roboczy](how-to-manage-workspace.md)Azure Machine Learning.

+ Ogólna wiedza o działaniu [usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) i [sieci IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Wstępnie istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

+ Aby można było wdrożyć zasoby w sieci wirtualnej lub podsieci, konto użytkownika musi mieć uprawnienia do następujących akcji w kontroli dostępu opartej na rolach (RBAC) na platformie Azure:

    - "Microsoft. Network/virtualNetworks/Join/Action" w zasobie sieci wirtualnej.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" w zasobie podsieci.

    Aby uzyskać więcej informacji na temat RBAC w sieci, zobacz [wbudowane role sieciowe](/azure/role-based-access-control/built-in-roles#networking) .

## <a name="secure-your-workspace"></a>Zabezpieczanie obszaru roboczego

Obszar roboczy Azure Machine Learning może mieć __publiczny punkt końcowy__ lub __prywatny punkt końcowy__. Publiczny punkt końcowy to zestaw adresów IP, które są dostępne w publicznej sieci Internet, podczas gdy prywatny punkt końcowy jest zestawem prywatnych adresów IP w ramach tej platformy. 

Aby ograniczyć dostęp do obszaru roboczego tylko za pośrednictwem prywatnych adresów IP, należy użyć prywatnego punktu końcowego.

Ponieważ komunikacja z obszarem roboczym jest dozwolona tylko w sieci wirtualnej, wszystkie środowiska deweloperskie korzystające z obszaru roboczego muszą należeć do sieci wirtualnej. Na przykład maszyna wirtualna w sieci wirtualnej.

> [!IMPORTANT]
> Prywatny punkt końcowy nie ma wpływu na płaszczyznę kontroli platformy Azure (operacje zarządzania), takie jak usuwanie obszaru roboczego lub zarządzanie zasobami obliczeniowymi. Na przykład tworzenie, aktualizowanie lub usuwanie elementu docelowego obliczeń. Te operacje są wykonywane za pośrednictwem publicznej sieci Internet jako normalne.
>
> Prywatny punkt końcowy uniemożliwia dostęp do obszaru roboczego spoza sieci wirtualnej.

Niektóre kombinacje zasobów z prywatnym punktem końcowym wymagają obszaru roboczego wersja Enterprise. Skorzystaj z poniższej tabeli, aby zrozumieć, jakie scenariusze wymagają wydania Enterprise Edition:

| Scenariusz | Przedsiębiorstwa</br>bitowych | Podstawowy</br>bitowych |
| ----- |:-----:|:-----:| 
| Brak sieci wirtualnej lub prywatnego punktu końcowego | ✔ | ✔ |
| Obszar roboczy bez prywatnego punktu końcowego. Inne zasoby (z wyjątkiem Azure Container Registry) w sieci wirtualnej | ✔ | ✔ |
| Obszar roboczy bez prywatnego punktu końcowego. Inne zasoby z prywatnym punktem końcowym | ✔ | |
| Obszar roboczy z prywatnym punktem końcowym. Inne zasoby (z wyjątkiem Azure Container Registry) w sieci wirtualnej | ✔ | ✔ |
| Obszar roboczy i inne zasoby z prywatnym punktem końcowym | ✔ | |
| Obszar roboczy z prywatnym punktem końcowym. Inne zasoby bez prywatnego punktu końcowego lub sieci wirtualnej | ✔ | ✔ |
| Azure Container Registry w sieci wirtualnej | ✔ | |
| Klucze zarządzane przez klienta dla obszaru roboczego | ✔ | |

> [!WARNING]
> 
> Wystąpienia obliczeniowe Azure Machine Learning nie są obsługiwane w obszarze roboczym, w którym włączono prywatny punkt końcowy.
>
> Azure Machine Learning nie obsługuje korzystania z usługi Azure Kubernetes, która ma włączony prywatny punkt końcowy. Zamiast tego można użyć usługi Azure Kubernetes w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie eksperymentów i zadań wnioskowania usługi Azure ml w ramach Virtual Network platformy Azure](how-to-enable-virtual-network.md).

Aby dowiedzieć się więcej o prywatnych punktach końcowych na platformie Azure, zobacz artykuł [prywatny link do platformy Azure](/azure/private-link/private-link-overview) .

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Tworzenie obszaru roboczego korzystającego z prywatnego punktu końcowego

Nowy obszar roboczy można utworzyć za pomocą prywatnego punktu końcowego przy użyciu zestawu SDK Azure Machine Learning, interfejsu wiersza polecenia, szablonu Azure Resource Manager lub Azure Portal.

__Wymagania__

* Sieć wirtualna używana z prywatnym punktem końcowym musi mieć wyłączone zasady sieciowe. Aby uzyskać więcej informacji, zobacz temat [wyłączanie zasad sieciowych dla prywatnego punktu końcowego](/azure/private-link/disable-private-endpoint-network-policy).

__Ograniczenia__

* Nie można nawiązać połączenia z obszarem roboczym za pośrednictwem publicznej sieci Internet, tylko z poziomu usługi Virtual Network.

* Jeśli wiele obszarów roboczych jest tworzonych przy użyciu prywatnych punktów końcowych i korzystają z tej samej prywatnej strefy DNS, do __łączy sieci wirtualnej__ prywatnej strefy DNS zostanie dodany tylko pierwszy obszar roboczy.

    Aby obejść to ograniczenie, należy ręcznie dodać łącze sieci wirtualnej dla dodatkowych obszarów roboczych. Aby uzyskać więcej informacji, zobacz [co to jest łącze sieci wirtualnej](/azure/dns/private-dns-virtual-network-links).

__Konfiguracja__

Aby uzyskać informacje na temat sposobu tworzenia obszaru roboczego przy użyciu sieci wirtualnej i prywatnego punktu końcowego, a także innych opcji konfiguracji, zobacz następujące artykuły:

* [Użyj szablonu Azure Resource Manager, aby utworzyć obszar roboczy dla Azure Machine Learning](how-to-create-workspace-template.md).
* [Tworzenie obszarów roboczych w portalu](how-to-manage-workspace.md).
* [Utwórz obszary robocze za pomocą interfejsu wiersza polecenia platformy Azure](how-to-manage-workspace-cli.md).
* Aby użyć zestawu SDK języka Python, zobacz dokumentację referencyjną [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) i [Workspace. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) .

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

__Wymagania__

* Aby uzyskać dostęp do danych na koncie magazynu, konto magazynu musi znajdować się w tej samej sieci wirtualnej co obszar roboczy.

* Jeśli dane są przechowywane w sieci wirtualnej, należy użyć [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) w obszarze roboczym, aby udzielić dostępu do danych w programie Studio.

    > [!IMPORTANT]
    > Chociaż większość z programu Studio współpracuje z danymi przechowywanymi w sieci wirtualnej, __nie są to__zintegrowane notesy. Zintegrowane notesy nie obsługują używania magazynu znajdującego się w sieci wirtualnej. Zamiast tego można użyć notesów Jupyter z wystąpienia obliczeniowego. Aby uzyskać więcej informacji, zobacz sekcję [& klastrów obliczeniowych](#compute-instance) .

    Jeśli nie przyznano dostępu do programu Studio, zostanie wyświetlony następujący komunikat o błędzie `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` :

    * Podgląd danych w Studio.
    * Wizualizowanie danych w projektancie.
    * Prześlij eksperyment AutoML.
    * Rozpocznij projekt etykietowania.

__Ograniczenia__

* Azure Machine Learning Studio obsługuje odczytywanie danych z następujących typów magazynów w sieci wirtualnej:

    * Obiekt bob Azure
    * Usługa Azure Data Lake Storage 1. generacji
    * Usługa Azure Data Lake Storage 2. generacji
    * Azure SQL Database

__Konfiguracja__

* __Skonfiguruj magazyny danych do korzystania z tożsamości zarządzanej__ w celu uzyskania dostępu do Twoich potrzeb. Te kroki umożliwiają dodanie tożsamości zarządzanej przez obszar roboczy jako __czytnika__ do usługi magazynu przy użyciu kontroli dostępu opartej na zasobach (RBAC) platformy Azure. Dostęp __czytnika__ umożliwia pobranie ustawień zapory przez obszar roboczy i upewnienie się, że dane nie opuszczają sieci wirtualnej.

    1. W programie Studio wybierz pozycję __magazyny__danych.

    1. Aby utworzyć nowy magazyn danych, wybierz pozycję __+ nowy magazyn__danych. Aby zaktualizować istniejący, wybierz magazyn danych i wybierz pozycję __Aktualizuj poświadczenia__.

    1. W ustawieniach magazynu danych wybierz opcję __tak__ , aby __umożliwić usłudze Azure Machine Learning dostęp do magazynu przy użyciu tożsamości zarządzanej w obszarze roboczym__.

    > [!NOTE]
    > Wprowadzenie zmian może potrwać do 10 minut.

* W przypadku __usługi Azure Blob Storage__tożsamość zarządzana w obszarze roboczym również musi zostać dodana jako [czytnik danych obiektów BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , dzięki czemu może odczytywać dane z magazynu obiektów BLOB.

* Projektant używa konta magazynu dołączonego do obszaru roboczego do przechowywania danych wyjściowych domyślnie. Można jednak określić, aby przechowywać dane wyjściowe do dowolnego magazynu danych, do którego masz dostęp. Jeśli środowisko korzysta z sieci wirtualnych, można użyć tych kontrolek, aby upewnić się, że dane pozostają bezpieczne i dostępne. Aby ustawić nowy magazyn domyślny dla potoku:

    1. W wersji roboczej potoku wybierz **ikonę koła zębatego** obok tytułu potoku.
    1. Wybierz pozycję **Wybierz domyślny magazyn** danych.
    1. Określ nowy magazyn danych.

    Możesz również zastąpić domyślny magazyn danych dla każdego modułu. Zapewnia to kontrolę nad lokalizacją przechowywania poszczególnych modułów.

    1. Wybierz moduł, którego dane wyjściowe chcesz określić.
    1. Rozwiń sekcję **Ustawienia wyjściowe** .
    1. Wybierz opcję **Zastąp domyślne ustawienia wyjściowe**.
    1. Wybierz pozycję **Ustaw ustawienia wyjściowe**.
    1. Określ nowy magazyn danych.

* W przypadku używania __Azure Data Lake Storage Gen2__można użyć list kontroli dostępu (ACL) RBAC i POSIX, aby kontrolować dostęp do danych wewnątrz sieci wirtualnej.

    Aby użyć RBAC, należy dodać tożsamość zarządzaną obszaru roboczego do roli [czytnika danych obiektów BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Aby uzyskać więcej informacji, zobacz [Kontrola dostępu na podstawie ról](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

    Aby używać list ACL, tożsamość zarządzana obszaru roboczego może być przypisana, podobnie jak jakakolwiek inna reguła zabezpieczeń. Aby uzyskać więcej informacji, zobacz [listy kontroli dostępu do plików i katalogów](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

* __Azure Data Lake Storage Gen1__ obsługuje tylko listy kontroli dostępu w stylu POSIX. Można przypisać dostęp do tożsamości zarządzanego obszaru roboczego do zasobów tak samo jak w przypadku każdej innej zasady zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

* Aby uzyskać dostęp do danych przechowywanych w __Azure SQL Database__ przy użyciu tożsamości zarządzanej, należy utworzyć użytkownika programu SQL Server, który jest mapowany na tożsamość zarządzaną. Aby uzyskać więcej informacji na temat tworzenia użytkownika od dostawcy zewnętrznego, zobacz [Tworzenie zawartych użytkowników mapowanych na tożsamości usługi Azure AD](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

    Po utworzeniu użytkownika zawartego w programie SQL Udziel uprawnień do niego przy użyciu [polecenia Udziel T-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

* Jeśli __uzyskujesz dostęp do programu Studio z zasobu w sieci wirtualnej__ (na przykład wystąpienie obliczeniowe lub maszyna wirtualna), musisz zezwolić na ruch wychodzący z sieci wirtualnej do programu Studio. 

    Na przykład, jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń) w celu ograniczenia ruchu wychodzącego, Dodaj regułę do miejsca docelowego __tagu usługi__ __AzureFrontDoor. frontonu__.

## <a name="use-datastores-and-datasets"></a>Korzystanie z magazynów danych i DataSets

> [!NOTE]
> W tej sekcji omówiono użycie magazynu danych i zestawu elementów DataSet dla środowiska zestawu SDK. Aby uzyskać więcej informacji na temat środowiska Studio, zapoznaj się z sekcją [Machine Learning Studio](#machine-learning-studio).

__Ograniczenia__

Domyślnie Azure Machine Learning sprawdza ważność danych i sprawdzanie poświadczeń podczas próby dostępu do danych za pomocą zestawu SDK. Jeśli dane znajdują się za siecią wirtualną, Azure Machine Learning nie mogą uzyskać dostępu do danych i przetworzyć ich testy. Aby obejść ten problem, należy pominąć sprawdzanie poprawności podczas tworzenia magazynów danych i zestawów DataSet.

* W przypadku korzystania z __magazynu__danych:

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

* W przypadku korzystania z __zestawu danych__:

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

## <a name="azure-storage-account"></a>Konto usługi Azure Storage

> [!IMPORTANT]
> Zarówno _domyślne konto magazynu_ dla Azure Machine Learning, jak i _inne niż domyślne konta magazynu_ można umieścić w sieci wirtualnej.

__Wymagania__

* Konto magazynu musi znajdować się w tej samej sieci wirtualnej i podsieci co wystąpienia obliczeniowe lub klastry używane do uczenia lub wnioskowania.

__Konfiguracja__

Aby zabezpieczyć konto usługi Azure Storage używane przez obszar roboczy, należy włączyć __prywatny punkt końcowy__ lub __punkt końcowy usługi__ dla konta magazynu w sieci wirtualnej.

* Aby skonfigurować konto magazynu do korzystania z __prywatnego punktu końcowego__, zobacz artykuł [Używanie prywatnych punktów końcowych](/azure/storage/common/storage-private-endpoints.md) .

* Aby skonfigurować konto magazynu do korzystania z __punktu końcowego usługi__, wykonaj następujące czynności:

    1. Aby dodać konto magazynu do sieci wirtualnej używanej przez obszar roboczy, Skorzystaj z informacji w sekcji __udzielanie dostępu z sieci wirtualnej__ w artykule [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) .
    1. Aby zezwolić na dostęp z usług firmy Microsoft w sieci wirtualnej (np. Azure Machine Learning), Skorzystaj z informacji w sekcji __wyjątki__ w artykule [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](/azure/storage/common/storage-network-security#exceptions) .
    1. Podczas pracy z zestawem SDK Azure Machine Learning środowisko programistyczne musi mieć możliwość nawiązania połączenia z kontem usługi Azure Storage. Gdy konto magazynu znajduje się w sieci wirtualnej, zapora musi zezwalać na dostęp ze swojego adresu IP środowiska deweloperskiego. Aby dodać adres IP środowiska deweloperskiego, Skorzystaj z informacji w sekcji __udzielanie dostępu z zakresu adresów IP Internetu__ w artykule [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) .

## <a name="azure-container-registry"></a>Azure Container Registry

__Wymagania__

* Obszar roboczy Azure Machine Learning musi być w wersji Enterprise Edition. Informacje o uaktualnianiu programu znajdują się w temacie [Upgrade to Enterprise Edition](how-to-manage-workspace.md#upgrade).
* Region obszaru roboczego Azure Machine Learning powinien być [regionem z włączonym linkiem prywatnym](https://docs.microsoft.com/azure/private-link/private-link-overview#availability). 
* Azure Container Registry musi być w wersji Premium. Aby uzyskać więcej informacji na temat uaktualniania, zobacz [Zmiana jednostek SKU](/azure/container-registry/container-registry-skus#changing-skus).
* Azure Container Registry musi znajdować się w tej samej sieci wirtualnej i podsieci co konto magazynu i cele obliczeniowe używane do uczenia lub wnioskowania.
* Obszar roboczy Azure Machine Learning musi zawierać [Azure Machine Learning klaster obliczeniowy](how-to-set-up-training-targets.md#amlcompute).

__Ograniczenia__

* Gdy ACR znajduje się za siecią wirtualną, Azure Machine Learning nie może użyć jej do bezpośredniego tworzenia obrazów platformy Docker. Zamiast tego klaster obliczeniowy jest używany do kompilowania obrazów.

__Konfiguracja__

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

1. Aby umożliwić obszarowi roboczemu komunikowanie się z wystąpieniem ACR, zastosuj następujący szablon Azure Resource Manager:

    > [!WARNING]
    > Ten szablon umożliwia korzystanie z prywatnego punktu końcowego dla obszaru roboczego i zmienia go w obszarze roboczym przedsiębiorstwa. Nie można cofnąć tych zmian.

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

__Wymagania__

__Ograniczenia__

__Konfiguracja__ 

Aby korzystać z funkcji eksperymentowania Azure Machine Learning z Azure Key Vault za siecią wirtualną, należy użyć artykułu [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](/azure/key-vault/general/network-security) .

> [!IMPORTANT]
> Wykonując kroki opisane w artykule, Użyj tej samej sieci wirtualnej, która jest używana przez zasoby obliczeniowe eksperymentowania. Należy również __zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory__.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Wystąpienia klastrów obliczeniowych & 

__Wymagania__

* Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
* Podsieć określona dla wystąpienia obliczeniowego lub klastra musi mieć wystarczającą liczbę nieprzypisanych adresów IP w celu uwzględnienia liczby elementów, które są przeznaczone dla maszyny wirtualnej. Jeśli podsieć nie ma wystarczającej liczby nieprzypisanych adresów IP, klaster obliczeniowy zostanie częściowo przydzielony.
* Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu, niektóre porty muszą pozostać otwarte dla usługi obliczeniowej.
* Jeśli chcesz umieścić wiele wystąpień obliczeniowych lub klastrów w jednej sieci wirtualnej, może być konieczne zażądanie zwiększenia limitu przydziału dla co najmniej jednego z zasobów.
* Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co Azure Machine Learning wystąpienie obliczeniowe lub klaster. 
* Aby funkcja Jupyter wystąpienia obliczeniowego działała, należy się upewnić, że komunikacja gniazda internetowego nie jest wyłączona.

__Ograniczenia__

* Wystąpienie obliczeniowe Machine Learning lub klaster automatycznie przydziela dodatkowe zasoby sieciowe __do grupy zasobów zawierającej sieć wirtualną__. Dla każdego wystąpienia obliczeniowego lub klastra usługa przydziela następujące zasoby:

    * Jedna sieciowa Grupa zabezpieczeń
    * Jeden publiczny adres IP
    * Jeden moduł równoważenia obciążenia
    
    W przypadku __klastrów obliczeniowych__te zasoby są usuwane (i tworzone ponownie) za każdym razem, gdy klaster jest skalowany w dół do 0 węzłów.
    
    W przypadku __wystąpienia obliczeniowego__ zasoby są przechowywane do momentu usunięcia wystąpienia (zatrzymywanie nie powoduje usunięcia zasobów).

    Te zasoby są ograniczone przez [limity zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) subskrypcji.

__Konfiguracja__

* Aby utworzyć klaster środowisko obliczeniowe usługi Machine Learning, wykonaj następujące czynności:

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

* Jeśli używasz notesów w wystąpieniu obliczeniowym platformy Azure, musisz się upewnić, że Notes jest uruchomiony w zasobie obliczeniowym za tą samą siecią wirtualną i podsiecią, co dane. 

    Skonfiguruj wystąpienie obliczeniowe jako znajdujące się w tej samej sieci wirtualnej podczas tworzenia w obszarze **Ustawienia zaawansowane**  >  **Skonfiguruj sieć wirtualną**. Nie można dodać istniejącego wystąpienia obliczeniowego do sieci wirtualnej.

* Jeśli planujesz zabezpieczenie sieci wirtualnej przez ograniczenie ruchu sieciowego do/z publicznej sieci Internet, musisz zezwolić na komunikację przychodzącą z usługi Azure Batch.

    Usługa Batch dodaje sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) na poziomie interfejsów sieciowych dołączonych do maszyn wirtualnych. Te sieciowe grupy zabezpieczeń automatycznie konfigurują reguły ruchu przychodzącego i wychodzącego, aby zezwolić na następujący ruch:

    - Ruch przychodzący TCP na portach 29876 i 29877 z __tagu usługi__ __BatchNodeManagement__.

    - Obowiązkowe Ruch przychodzący TCP na porcie 22, aby zezwolić na dostęp zdalny. Tego portu należy używać tylko w przypadku, gdy chcesz nawiązać połączenie przy użyciu protokołu SSH w publicznym adresie IP.

    - Ruch wychodzący na dowolny port do sieci wirtualnej.

    - Ruch wychodzący na dowolny port do Internetu.

    - Dla ruchu przychodzącego TCP wystąpienia obliczeniowego na porcie 44224 z __tagu usługi__ __AzureMachineLearning__.

    > [!IMPORTANT]
    > Zachowaj ostrożność przy modyfikowaniu lub dodawaniu reguły ruchu przychodzącego lub wychodzącego w sieciowych grupach zabezpieczeń skonfigurowanych za pomocą usługi Batch. Jeśli sieciowej grupy zabezpieczeń blokuje komunikację z węzłami obliczeniowymi, usługa COMPUTE ustawia stan węzłów obliczeniowych na niezdatny do użytku.
    >
    > Nie musisz określać sieciowych grup zabezpieczeń na poziomie podsieci, ponieważ usługa Azure Batch konfiguruje własny sieciowych grup zabezpieczeń. Jeśli jednak podsieć zawierająca Azure Machine Learning COMPUTE ma skojarzone sieciowych grup zabezpieczeń lub zaporę, należy również zezwolić na wymieniony wcześniej ruch.

* Jeśli nie chcesz używać domyślnych reguł ruchu wychodzącego i chcesz ograniczyć dostęp wychodzący do sieci wirtualnej, wykonaj następujące czynności:

    1. Odmowa wychodzącego połączenia internetowego przy użyciu reguł sieciowej grupy zabezpieczeń.
    1. W przypadku __wystąpienia obliczeniowego__ lub __klastra obliczeniowego__należy ograniczyć ruch wychodzący do następujących elementów:
        - Azure Storage, przy użyciu __znacznika usługi__ __Storage. RegionName__. Gdzie `{RegionName}` jest nazwą regionu świadczenia usługi Azure.
        - Azure Container Registry, przy użyciu __znacznika usługi__ __AzureContainerRegistry. RegionName__. Gdzie `{RegionName}` jest nazwą regionu świadczenia usługi Azure.
        - Azure Machine Learning, przy użyciu __tagu usługi__ __AzureMachineLearning__
        - Azure Resource Manager, przy użyciu __tagu usługi__ __AzureResourceManager__
        - Azure Active Directory, przy użyciu __tagu usługi__ __usługi azureactivedirectory__

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

* Jeśli korzystasz z [wymuszonego tunelowania](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) przy użyciu Azure Machine Learning COMPUTE, musisz zezwolić na komunikację z publiczną Internetem z podsieci zawierającej zasób obliczeniowy. Ta komunikacja służy do planowania zadań i uzyskiwania dostępu do usługi Azure Storage.

    Można to zrobić na dwa sposoby:

    * Użyj [Virtual Network translatora adresów sieciowych](../virtual-network/nat-overview.md). Brama NAT zapewnia wychodzące połączenie z Internetem dla co najmniej jednej podsieci w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [projektowanie sieci wirtualnych z użyciem zasobów bramy translatora adresów sieciowych](../virtual-network/nat-gateway-resource.md).

    * Dodaj [trasy zdefiniowane przez użytkownika (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) do podsieci zawierającej zasób obliczeniowy. Ustanów UDR dla każdego adresu IP, który jest używany przez usługę Azure Batch w regionie, w którym znajdują się zasoby. Te UDR umożliwiają usłudze Batch komunikowanie się z węzłami obliczeniowymi w celu planowania zadań. Należy również dodać adres IP dla usługi Azure Machine Learning, w której znajdują się zasoby, ponieważ jest to wymagane w celu uzyskania dostępu do wystąpień obliczeniowych. Aby uzyskać listę adresów IP usługi Batch i usługi Azure Machine Learning, należy użyć jednej z następujących metod:

        * Pobierz [zakresy adresów IP i Tagi usług platformy Azure](https://www.microsoft.com/download/details.aspx?id=56519) , a następnie wyszukaj plik dla `BatchNodeManagement.<region>` i `AzureMachineLearning.<region>` , gdzie `<region>` jest Twoim regionem świadczenia usługi Azure.

        * Użyj [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , aby pobrać te informacje. Poniższy przykład pobiera informacje o adresie IP i filtruje informacje dla regionu Wschodnie stany USA 2:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        Po dodaniu UDR, zdefiniuj trasę dla każdego powiązanego prefiksu adresu IP partii i ustaw __Typ następnego przeskoku__ na __Internet__. 

        Oprócz wszelkich zdefiniowanych UDR ruch wychodzący do usługi Azure Storage musi być dozwolony za pomocą lokalnego urządzenia sieciowego. W odniesieniu do adresów URL tego ruchu znajdują się następujące formy: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` , i `<account>.blob.core.windows.net` . 

        Aby uzyskać więcej informacji, zobacz [Tworzenie puli Azure Batch w sieci wirtualnej](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__Wymagania__

* Wystąpienie usługi Azure Kubernetes Service (AKS) i Sieć wirtualna platformy Azure muszą znajdować się w tym samym regionie. W przypadku zabezpieczenia kont usługi Azure Storage używanych przez obszar roboczy w sieci wirtualnej, muszą one znajdować się w tej samej sieci wirtualnej co wystąpienie AKS.

* Aby __zaplanować adresowanie IP__ klastra, należy postępować zgodnie z wymaganiami wstępnymi w artykule [Konfigurowanie zaawansowanej sieci w usłudze Azure KUBERNETES Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) .

* Aby ograniczyć komunikację przychodzącą i wychodzącą do klastra AKS, postępuj zgodnie ze wskazówkami w artykule [sterowanie ruchem przychodzącym węzłów klastra w usłudze Azure Kubernetes Service](/azure/aks/limit-egress-traffic) , aby upewnić się, że komunikacja _wyjściowa_ z AKS jest prawidłowo skonfigurowana. Wymagania dotyczące komunikacji _przychodzącej_ , jeśli istnieją, są wywoływane w poniższej sekcji konfiguracji.

__Ograniczenia__

* Jeśli chcesz korzystać z usługi Azure Kubernetes, która ma włączone łącze prywatne, musisz dołączyć ją do obszaru roboczego. Nie można utworzyć klastra usługi Azure Kubernetes z prywatnym linkiem z Azure Machine Learning (SDK, portalu, interfejsu wiersza polecenia itp.).

__Konfiguracja__

> [!IMPORTANT]
> Ta sekcja zawiera wiele konfiguracji. Wybierz ten, który najlepiej odpowiada Twoim potrzebom.

* Aby __używać AKS za siecią wirtualną, za pomocą publicznego modułu równoważenia obciążenia__:

    1. Utwórz lub Dołącz klaster AKS. W przypadku __tworzenia__ nowego klastra należy określić sieć wirtualną, która ma być używana przez klaster.
    
        W poniższym przykładzie pokazano, jak utworzyć nowy klaster AKS przy użyciu zestawu SDK języka Python:

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

        Jeśli masz istniejący klaster AKS, który znajduje się już za siecią wirtualną, użyj informacji zawartych w artykule [wdrażanie do usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    1. Upewnij się, że sieciowa Grupa zabezpieczeń kontrolująca sieć wirtualną ma włączoną regułę __zabezpieczeń dla__ punktu końcowego oceniania, tak aby można ją było wywołać spoza sieci wirtualnej.

* Aby __używać AKS za siecią wirtualną, za pomocą prywatnego modułu równoważenia obciążenia__:

    1. Utwórz lub Dołącz klaster AKS. W przypadku __tworzenia__ nowego klastra należy określić sieć wirtualną, która ma być używana przez klaster.
    
        W poniższym przykładzie pokazano, jak utworzyć nowy klaster AKS przy użyciu zestawu SDK języka Python:

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

        Jeśli masz istniejący klaster AKS, który znajduje się już za siecią wirtualną, użyj informacji zawartych w artykule [wdrażanie do usługi Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

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

    1. Aby zaktualizować klaster AKS w celu korzystania z __prywatnego modułu równoważenia obciążenia__, użyj zestawu SDK języka Python. Poniższy fragment kodu przedstawia sposób aktualizowania istniejącego klastra AKS, który został dodany lub dołączony do obszaru roboczego:
    
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

* Aby __dołączyć AKS przy użyciu prywatnego punktu końcowego__:

    1. Użyj poniższego polecenia platformy Azure, aby uzyskać __Identyfikator podsieci__ podsieci, która będzie używana przez klaster AKS. Na przykład domyślna podsieć dla sieci wirtualnej:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        To polecenie zwraca tablicę identyfikatorów dla podsieci w sieci wirtualnej. Poniższy kod JSON jest przykładem z sieci wirtualnej, która ma tylko jedną podsieć:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Jeśli jest zwracana wiele identyfikatorów, wybierz tę, której chcesz użyć.

    1. Aby utworzyć klaster AKS jako prywatny punkt końcowy, Skorzystaj z informacji w sekcji __Zaawansowane sieci__ w artykule [Tworzenie prywatnego klastra usługi Azure Kubernetes Service](/azure/aks/private-clusters#advanced-networking) . Podczas tworzenia klastra użyj identyfikatora podsieci z poprzedniego polecenia z `--vnet-subnet-id` parametrem.

    1. Aby dołączyć klaster, użyj informacji zawartych w artykule Wdrażanie w usłudze [Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) .

    > [!TIP]
    > W przypadku korzystania z tej konfiguracji i ograniczania komunikacji wychodzącej z klastra AKS nie trzeba zezwalać na komunikację na portach __1194__ lub __9000__. W przypadku innych portów, które powinny być dozwolone, zapoznaj się ze wskazówkami zawartymi w artykule [sterowanie ruchem wychodzącym dla węzłów klastra w usłudze Azure Kubernetes Service](/azure/aks/limit-egress-traffic) .

## <a name="use-azure-container-instances-aci"></a>Użyj Azure Container Instances (ACI)

__Wymagania__

* Azure Container Instances są tworzone dynamicznie podczas wdrażania modelu. Aby umożliwić Azure Machine Learning tworzenia ACI wewnątrz sieci wirtualnej, należy włączyć __delegowanie podsieci__ dla podsieci używanej przez wdrożenie.

__Ograniczenia__

* Sieć wirtualna musi znajdować się w tej samej grupie zasobów co obszar roboczy Azure Machine Learning.

* Azure Container Registry (ACR) dla obszaru roboczego nie może być również w sieci wirtualnej.

__Konfiguracja__

Aby użyć ACI w sieci wirtualnej z obszarem roboczym, wykonaj następujące czynności:

1. Aby włączyć delegowanie podsieci w sieci wirtualnej, Skorzystaj z informacji zawartych w artykule [Dodawanie lub usuwanie delegowania podsieci](../virtual-network/manage-subnet-delegation.md) . Delegowanie można włączyć podczas tworzenia sieci wirtualnej lub dodać je do istniejącej sieci.

    > [!IMPORTANT]
    > Podczas włączania delegowania Użyj `Microsoft.ContainerInstance/containerGroups` wartości jako __delegowanej podsieci do usługi__ .

2. Wdróż model przy użyciu [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), użyj `vnet_name` parametrów i `subnet_name` . Ustaw te parametry na nazwę sieci wirtualnej i podsieć, w której włączono delegowanie.

## <a name="azure-databricks"></a>Azure Databricks

__Wymagania__

* Sieć wirtualna musi znajdować się w tej samej subskrypcji i regionie co obszar roboczy Azure Machine Learning.
* Jeśli konta usługi Azure Storage dla obszaru roboczego są również zabezpieczone w sieci wirtualnej, muszą znajdować się w tej samej sieci wirtualnej co klaster Azure Databricks.
* Poza niektórymi __datakostki — prywatnymi__ i niestandardowymi __—__ podsieciami używanymi Azure Databricks, wymagana jest również __Domyślna__ podsieć utworzona dla sieci wirtualnej.

__Ograniczenia__

__Konfiguracja__

Aby uzyskać szczegółowe informacje na temat używania Azure Databricks z siecią wirtualną, zobacz [wdrażanie Azure Databricks w Virtual Network platformy Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Maszyna wirtualna lub klaster usługi HDInsight

__Wymagania__

* Azure Machine Learning obsługuje tylko maszyny wirtualne z systemem Ubuntu.
* Port SSH musi być włączony na maszynie wirtualnej lub w klastrze usługi HDInsight.

__Ograniczenia__

__Konfiguracja__

1. Utwórz maszynę wirtualną lub klaster usługi HDInsight przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure i umieść klaster w sieci wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz następujące artykuły:

    * [Tworzenie sieci wirtualnych platformy Azure dla maszyn wirtualnych z systemem Linux i zarządzanie nimi](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Zwiększanie usługi HDInsight przy użyciu sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. SIECIOWEJ grupy zabezpieczeń jest tworzony automatycznie dla Virtual Machines platformy Azure opartej na systemie Linux. Ta sieciowej grupy zabezpieczeń umożliwia dostęp do portu 22 z dowolnego źródła. Jeśli chcesz ograniczyć dostęp do portu SSH, musisz zezwolić na dostęp z Azure Machine Learning. Aby zachować dostęp do usługi Azure ML, musisz zezwolić na dostęp z __usługi źródłowej__ za pomocą __tagu usługi źródłowej__ __AzureMachineLearning__. Na przykład następujące polecenie interfejsu wiersza polecenia platformy Azure modyfikuje regułę SSH, aby zezwalać na dostęp tylko z Azure Machine Learning.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie grup zabezpieczeń sieci](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) w artykule Azure Virtual Networks for Linux Virtual Machines.
    
    Zachowaj domyślne reguły ruchu wychodzącego dla sieciowej grupy zabezpieczeń. Aby uzyskać więcej informacji, zobacz domyślne reguły zabezpieczeń w [grupach zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

1. Dołącz maszynę wirtualną lub klaster usługi HDInsight do obszaru roboczego Azure Machine Learning. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych na potrzeby szkolenia modeli](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Następne kroki

* [Użyj Azure Machine Learning obszaru roboczego za zaporą platformy Azure](how-to-access-azureml-behind-firewall.md).
* [Konfigurowanie środowisk szkoleniowych](how-to-set-up-training-targets.md)
* [Skonfiguruj prywatne punkty końcowe](how-to-configure-private-link.md)
* [Gdzie należy wdrażać modele](how-to-deploy-and-where.md)
* [Użyj protokołu TLS do zabezpieczenia usługi sieci Web za pomocą Azure Machine Learning](how-to-secure-web-service.md)
