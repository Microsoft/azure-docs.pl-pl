---
title: Zabezpieczanie obszaru roboczego Azure Machine Learning przy użyciu sieci wirtualnych
titleSuffix: Azure Machine Learning
description: Użyj izolowanego Virtual Network platformy Azure do zabezpieczenia obszaru roboczego Azure Machine Learning i skojarzonych zasobów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: a923f65e5c6183d045f4b7455e0a01edda75d499
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584354"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Zabezpieczanie obszaru roboczego Azure Machine Learning przy użyciu sieci wirtualnych

Ten artykuł zawiera informacje na temat zabezpieczania obszaru roboczego Azure Machine Learning i skojarzonych z nim zasobów w sieci wirtualnej.

Ten artykuł jest drugą częścią serii składającej się z pięciu części, która przeprowadzi Cię przez proces zabezpieczania przepływów pracy Azure Machine Learning. Zdecydowanie zalecamy zapoznanie się z [częścią poniżej: Omówienie sieci wirtualnej](how-to-network-security-overview.md) , aby zrozumieć ogólną architekturę. 

Zapoznaj się z innymi artykułami w tej serii:

[1. Sieć wirtualna — Omówienie](how-to-network-security-overview.md)  >  **2. Zabezpiecz obszar roboczy**  >  [3. Zabezpiecz środowisko szkoleniowe](how-to-secure-training-vnet.md)  >  [4. Zabezpiecz środowisko inferencing](how-to-secure-inferencing-vnet.md)  >  [5. Włącz funkcje programu Studio](how-to-enable-studio-virtual-network.md)

W tym artykule dowiesz się, jak włączyć następujące zasoby obszarów roboczych w sieci wirtualnej:
> [!div class="checklist"]
> - Obszar roboczy usługi Azure Machine Learning
> - Konta usługi Azure Storage
> - Azure Machine Learning magazyny i zestawy danych
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Wymagania wstępne

+ Zapoznaj się z artykułem [Omówienie zabezpieczeń sieci](how-to-network-security-overview.md) , aby poznać typowe scenariusze sieci wirtualnej i ogólną architekturę sieci wirtualnej.

+ Istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

+ Aby można było wdrożyć zasoby w sieci wirtualnej lub podsieci, konto użytkownika musi mieć uprawnienia do następujących akcji w kontroli dostępu opartej na rolach (Azure RBAC):

    - "Microsoft. Network/virtualNetworks/Join/Action" w zasobie sieci wirtualnej.
    - "Microsoft. Network/virtualNetworks/Subnet/Join/Action" w zasobie podsieci.

    Aby uzyskać więcej informacji na temat usługi Azure RBAC z obsługą sieci, zobacz [wbudowane role sieciowe](../role-based-access-control/built-in-roles.md#networking) .


## <a name="secure-the-workspace-with-private-endpoint"></a>Zabezpieczanie obszaru roboczego za pomocą prywatnego punktu końcowego

Link prywatny platformy Azure umożliwia nawiązanie połączenia z obszarem roboczym przy użyciu prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej. Następnie można ograniczyć dostęp do obszaru roboczego tylko w przypadku prywatnych adresów IP. Link prywatny pomaga ograniczyć ryzyko związane z eksfiltracji danych.

Aby uzyskać więcej informacji na temat konfigurowania prywatnego obszaru roboczego łącza, zobacz [jak skonfigurować link prywatny](how-to-configure-private-link.md).

> [!Warning]
> Zabezpieczanie obszaru roboczego za pomocą prywatnych punktów końcowych nie gwarantuje pełnego zabezpieczenia. Aby zabezpieczyć poszczególne składniki rozwiązania, należy wykonać kroki opisane w dalszej części tego artykułu oraz serię sieci wirtualnej.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Zabezpieczanie kont magazynu platformy Azure za pomocą punktów końcowych usługi

Azure Machine Learning obsługuje konta magazynu skonfigurowane do korzystania z punktów końcowych usługi lub prywatnych punktów końcowych. W tej sekcji dowiesz się, jak zabezpieczyć konto usługi Azure Storage za pomocą punktów końcowych usług. W przypadku prywatnych punktów końcowych Zobacz następną sekcję.

Aby użyć konta usługi Azure Storage dla obszaru roboczego w sieci wirtualnej, wykonaj następujące czynności:

1. W Azure Portal przejdź do usługi magazynu, która ma być używana w obszarze roboczym.

   [![Magazyn połączony z obszarem roboczym Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stronie konto usługi magazynu wybierz pozycję __Sieć__.

   ![Obszar sieci na stronie usługi Azure Storage w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na karcie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    1. Wybierz pozycję __Wybrane sieci__.
    1. W obszarze __sieci wirtualne__ wybierz łącze __Dodaj istniejące sieci wirtualne__ . Ta akcja powoduje dodanie sieci wirtualnej, w której znajdują się obliczenia (zobacz krok 1).

        > [!IMPORTANT]
        > Konto magazynu musi znajdować się w tej samej sieci wirtualnej i podsieci co wystąpienia obliczeniowe lub klastry używane do uczenia lub wnioskowania.

    1. Zaznacz pole wyboru __Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu__ . Ta zmiana nie zapewnia dostępu do konta magazynu wszystkim usługom platformy Azure.
    
        * Zasoby niektórych usług, **zarejestrowane w ramach subskrypcji**, mogą uzyskiwać dostęp do konta magazynu **w ramach tej samej subskrypcji** dla operacji wyboru. Na przykład zapisywanie dzienników lub tworzenie kopii zapasowych.
        * Do zasobów niektórych usług można uzyskać jawny dostęp do konta magazynu, __przypisując rolę platformy Azure__ do zarządzanej tożsamości przypisanej do systemu.

        Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Podczas pracy z zestawem SDK Azure Machine Learning środowisko programistyczne musi mieć możliwość nawiązania połączenia z kontem usługi Azure Storage. Gdy konto magazynu znajduje się w sieci wirtualnej, zapora musi zezwalać na dostęp ze swojego adresu IP środowiska deweloperskiego.
    >
    > Aby włączyć dostęp do konta magazynu, odwiedź __zapory i sieci wirtualne__ dla konta magazynu *z przeglądarki sieci Web na kliencie deweloperskim*. Następnie użyj pola wyboru __Dodaj adres IP klienta__ , aby dodać adres IP klienta do __zakresu adresów__. Możesz również użyć pola __zakres adresów__ , aby ręcznie wprowadzić adres IP środowiska deweloperskiego. Po dodaniu adresu IP klienta może on uzyskać dostęp do konta magazynu przy użyciu zestawu SDK.

   [![Okienko "zapory i sieci wirtualne" w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Zabezpieczanie kont usługi Azure Storage za pomocą prywatnych punktów końcowych

Azure Machine Learning obsługuje konta magazynu skonfigurowane do korzystania z punktów końcowych usługi lub prywatnych punktów końcowych. Jeśli konto magazynu używa prywatnych punktów końcowych, należy skonfigurować dwa prywatne punkty końcowe dla domyślnego konta magazynu:
1. Prywatny punkt końcowy z podzasobem obiektu **BLOB** Target.
1. Prywatny punkt końcowy z obiektem docelowym **pliku** (dataudziały).

![Zrzut ekranu przedstawiający stronę konfiguracji prywatnego punktu końcowego z użyciem obiektów blob i opcji plików](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Aby skonfigurować prywatny punkt końcowy dla konta magazynu, które **nie** jest domyślnym magazynem, wybierz **docelowy typ zasobu** , który odpowiada kontu magazynu, które chcesz dodać.

Aby uzyskać więcej informacji, zobacz [Używanie prywatnych punktów końcowych usługi Azure Storage](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Zabezpieczanie magazynów danych

W tej sekcji dowiesz się, jak za pomocą sieci wirtualnej używać magazynu elementów i zestawów danych w środowisku zestawu SDK. Aby uzyskać więcej informacji na temat środowiska Studio, zobacz Korzystanie z programu [Azure Machine Learning Studio w sieci wirtualnej](how-to-enable-studio-virtual-network.md).

Aby uzyskać dostęp do danych przy użyciu zestawu SDK, należy użyć metody uwierzytelniania wymaganej przez poszczególne usługi, w których są przechowywane dane. Jeśli na przykład zarejestrujesz magazyn danych w celu uzyskania dostępu do Azure Data Lake Store Gen2, nadal musisz używać nazwy głównej usługi zgodnie z opisem w temacie [Connect to Azure Storage Services](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Wyłącz weryfikację danych

Domyślnie Azure Machine Learning sprawdza ważność danych i sprawdzanie poświadczeń podczas próby dostępu do danych za pomocą zestawu SDK. Jeśli dane znajdują się za siecią wirtualną, Azure Machine Learning nie mogą zakończyć tych kontroli. Aby obejść to sprawdzenie, należy utworzyć magazyny danych i zestawy DataSet, które pomijają weryfikację.

### <a name="use-datastores"></a>Korzystanie z magazynów danych

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

### <a name="use-datasets"></a>Korzystanie z zestawów danych

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

## <a name="secure-azure-key-vault"></a>Bezpieczny Azure Key Vault

Azure Machine Learning używa skojarzonego wystąpienia Key Vault do przechowywania następujących poświadczeń:
* Powiązane parametry połączenia konta magazynu
* Hasła do wystąpień repozytorium kontenerów platformy Azure
* Parametry połączenia do magazynów danych

Aby korzystać z funkcji eksperymentowania Azure Machine Learning z Azure Key Vault za siecią wirtualną, wykonaj następujące czynności:

1. Przejdź do Key Vault, która jest skojarzona z obszarem roboczym.

1. Na stronie __Key Vault__ w lewym okienku wybierz pozycję __Sieć__.

1. Na karcie __zapory i sieci wirtualne__ wykonaj następujące czynności:
    1. W obszarze __Zezwalaj na dostęp z__, wybierz pozycję __prywatny punkt końcowy i wybrane sieci__.
    1. W obszarze __sieci wirtualne__ wybierz pozycję __Dodaj istniejące sieci wirtualne__ , aby dodać sieć wirtualną, w której znajduje się obliczenie eksperymentu.
    1. W obszarze __Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory__ wybierz pozycję __tak__.

   [![Sekcja "zapory i sieci wirtualne" w okienku Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Włącz Azure Container Registry (ACR)

Aby używać Azure Container Registry wewnątrz sieci wirtualnej, należy spełnić następujące wymagania:

* Azure Container Registry musi być w wersji Premium. Aby uzyskać więcej informacji na temat uaktualniania, zobacz [Zmiana jednostek SKU](../container-registry/container-registry-skus.md#changing-tiers).

* Azure Container Registry musi znajdować się w tej samej sieci wirtualnej i podsieci co konto magazynu i cele obliczeniowe używane do uczenia lub wnioskowania.

* Obszar roboczy Azure Machine Learning musi zawierać [Azure Machine Learning klaster obliczeniowy](how-to-create-attach-compute-cluster.md).

    Gdy ACR znajduje się za siecią wirtualną, Azure Machine Learning nie może użyć jej do bezpośredniego tworzenia obrazów platformy Docker. Zamiast tego klaster obliczeniowy jest używany do kompilowania obrazów.

    > [!IMPORTANT]
    > Klaster obliczeniowy służący do tworzenia obrazów platformy Docker musi mieć możliwość uzyskania dostępu do repozytoriów pakietów, które są używane do uczenia i wdrożenia modeli. Może być konieczne dodanie reguł zabezpieczeń sieci, które zezwalają na dostęp do repozytoriów publicznych, [Używanie prywatnych pakietów języka Python](how-to-use-private-python-packages.md)lub używanie [niestandardowych obrazów platformy Docker](how-to-train-with-custom-image.md) , które zawierają już pakiety.

Po spełnieniu tych wymagań wykonaj następujące kroki, aby włączyć Azure Container Registry.

> [!TIP]
> Jeśli nie korzystasz z istniejącego Azure Container Registry podczas tworzenia obszaru roboczego, jeden z nich może nie istnieć. Domyślnie obszar roboczy nie utworzy wystąpienia ACR, dopóki nie będzie potrzebne. Aby wymusić utworzenie jednego z nich, nauczenie lub wdrożenie modelu przy użyciu obszaru roboczego przed wykonaniem kroków opisanych w tej sekcji.

1. Znajdź nazwę Azure Container Registry w obszarze roboczym, korzystając z jednej z następujących metod:

    __Witryna Azure Portal__

    W sekcji przegląd obszaru roboczego wartość __rejestru__ łączy się z Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry obszaru roboczego" border="true":::

    __Interfejs wiersza polecenia platformy Azure__

    Jeśli [zainstalowano rozszerzenie Machine Learning dla interfejsu wiersza polecenia platformy Azure](reference-azure-machine-learning-cli.md), możesz użyć `az ml workspace show` polecenie, aby wyświetlić informacje o obszarze roboczym.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    To polecenie zwraca wartość podobną do `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Ostatnia część ciągu jest nazwą Azure Container Registry obszaru roboczego.

1. Ogranicz dostęp do sieci wirtualnej, wykonując czynności opisane w sekcji [Konfigurowanie dostępu do sieci dla rejestru](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Podczas dodawania sieci wirtualnej wybierz sieć wirtualną i podsieć dla zasobów Azure Machine Learning.

1. Skonfiguruj ACR dla obszaru roboczego, aby [zezwolić na dostęp za pomocą zaufanych usług](../container-registry/allow-access-trusted-services.md).

1. Użyj Azure Machine Learning Python SDK, aby skonfigurować klaster obliczeniowy do tworzenia obrazów platformy Docker. Poniższy fragment kodu ilustruje, jak to zrobić:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = None)
    ```

    > [!IMPORTANT]
    > Twoje konto magazynu, klaster obliczeniowy i Azure Container Registry muszą znajdować się w tej samej podsieci sieci wirtualnej.
    
    Aby uzyskać więcej informacji, zobacz informacje o metodzie [Update ()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest drugą częścią serii sieci wirtualnych z pięcioma częściami. Zapoznaj się z pozostałymi artykułami, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 1: Omówienie usługi Virtual Network](how-to-network-security-overview.md)
* [Część 3: Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)
* [Część 4: Zabezpieczanie środowiska inferencing](how-to-secure-inferencing-vnet.md)
* [Część 5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)

Zobacz również artykuł dotyczący używania [niestandardowego systemu DNS](how-to-custom-dns.md) do rozpoznawania nazw.