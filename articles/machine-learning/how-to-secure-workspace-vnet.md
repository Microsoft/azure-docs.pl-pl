---
title: Zabezpieczanie obszaru roboczego Azure Machine Learning sieciami wirtualnymi
titleSuffix: Azure Machine Learning
description: Użyj izolowanej usługi Azure Virtual Network, aby zabezpieczyć obszar roboczy Azure Machine Learning i skojarzone zasoby.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 20f75580c425cee9128f9c94123dcf902642eac4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531031"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Zabezpieczanie obszaru roboczego Azure Machine Learning sieciami wirtualnymi

Z tego artykułu dowiesz się, jak zabezpieczyć obszar roboczy Azure Machine Learning i skojarzone z nim zasoby w sieci wirtualnej.

Ten artykuł jest częścią drugiej z pięciu części serii, która zawiera informacje o zabezpieczaniu przepływu Azure Machine Learning przepływu pracy. Zdecydowanie zalecamy przeczytanie części [pierwszej:](how-to-network-security-overview.md) omówienie sieci wirtualnej, aby najpierw zrozumieć ogólną architekturę. 

Zobacz inne artykuły w tej serii:

[1. Omówienie sieci wirtualnej](how-to-network-security-overview.md)  >  **2. Zabezpiecz obszar roboczy**  >  [3. Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)  >  [4. Zabezpieczanie środowiska wnioskowania](how-to-secure-inferencing-vnet.md)  >  [5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)

Z tego artykułu dowiesz się, jak włączyć następujące zasoby obszarów roboczych w sieci wirtualnej:
> [!div class="checklist"]
> - Obszar roboczy usługi Azure Machine Learning
> - Konta usługi Azure Storage
> - Azure Machine Learning magazynów danych i zestawów danych
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Wymagania wstępne

+ Przeczytaj artykuł [Omówienie zabezpieczeń sieci,](how-to-network-security-overview.md) aby poznać typowe scenariusze sieci wirtualnej i ogólną architekturę sieci wirtualnej.

+ Istniejąca sieć wirtualna i podsieć do użycia z zasobami obliczeniowymi.

+ Aby wdrożyć zasoby w sieci wirtualnej lub podsieci, konto użytkownika musi mieć uprawnienia do następujących akcji w ramach kontroli dostępu na podstawie ról (RBAC) platformy Azure:

    - "Microsoft.Network/virtualNetworks/join/action" w zasobie sieci wirtualnej.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" w zasobie podsieci.

    Aby uzyskać więcej informacji na temat kontroli RBAC platformy Azure z siecią, zobacz [Wbudowane role sieci](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>Zabezpieczanie obszaru roboczego przy użyciu prywatnego punktu końcowego

Azure Private Link umożliwia nawiązywanie połączenia z obszarem roboczym przy użyciu prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w sieci wirtualnej. Następnie możesz ograniczyć dostęp do obszaru roboczego tylko za pośrednictwem prywatnych adresów IP. Private Link zmniejsza ryzyko eksfiltracji danych.

Aby uzyskać więcej informacji na temat konfigurowania Private Link roboczego, zobacz [Jak skonfigurować Private Link](how-to-configure-private-link.md).

> [!Warning]
> Zabezpieczanie obszaru roboczego za pomocą prywatnych punktów końcowych nie zapewnia samodzielnie end-to-end zabezpieczeń. Aby zabezpieczyć poszczególne składniki rozwiązania, należy wykonać kroki opisane w dalszej części tego artykułu oraz w serii Sieć wirtualna.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Zabezpieczanie kont usługi Azure Storage przy użyciu punktów końcowych usługi

Azure Machine Learning obsługuje konta magazynu skonfigurowane do używania punktów końcowych usługi lub prywatnych punktów końcowych. W tej sekcji dowiesz się, jak zabezpieczyć konto usługi Azure Storage przy użyciu punktów końcowych usługi. W przypadku prywatnych punktów końcowych zobacz następną sekcję.

Aby użyć konta usługi Azure Storage dla obszaru roboczego w sieci wirtualnej, należy wykonać następujące kroki:

1. W Azure Portal przejdź do usługi magazynu, której chcesz użyć w obszarze roboczym.

   [![Magazyn dołączony do Azure Machine Learning roboczego](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Na stronie konta usługi magazynu wybierz pozycję __Sieć.__

   ![Obszar sieci na stronie usługi Azure Storage w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Na karcie __Zapory i sieci wirtualne__ wykonaj następujące czynności:
    1. Wybierz pozycję __Wybrane sieci__.
    1. W __obszarze Sieci wirtualne__ wybierz link Dodaj __istniejącą sieć wirtualną.__ Ta akcja powoduje dodanie sieci wirtualnej, w której znajdują się zasoby obliczeniowe (zobacz krok 1).

        > [!IMPORTANT]
        > Konto magazynu musi znajdować się w tej samej sieci wirtualnej i podsieci co wystąpienia obliczeniowe lub klastry używane do trenowania lub wnioskowania.

    1. Zaznacz pole __wyboru Zezwalaj__ usługi firmy Microsoft dostęp do tego konta magazynu. Ta zmiana nie zapewnia wszystkim usługom platformy Azure dostępu do konta magazynu.
    
        * Zasoby niektórych usług **zarejestrowanych w** ramach subskrypcji mogą uzyskać dostęp do konta magazynu w tej samej **subskrypcji dla** wybranych operacji. Na przykład pisanie dzienników lub tworzenie kopii zapasowych.
        * Zasobom niektórych usług można przyznać jawny dostęp do konta magazynu, przypisując rolę platformy __Azure__ do przypisanej przez system tożsamości zarządzanej.

        Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Podczas pracy z zestawem AZURE MACHINE LEARNING SDK środowisko projektowe musi mieć możliwość nawiązania połączenia z kontem usługi Azure Storage. Gdy konto magazynu znajduje się w sieci wirtualnej, zapora musi zezwalać na dostęp z adresu IP środowiska dewelopera.
    >
    > Aby włączyć dostęp do konta  magazynu, odwiedź stronę Zapory i sieci wirtualne dla konta magazynu w przeglądarce internetowej *na kliencie dewelopera*. Następnie użyj __pola wyboru Dodaj adres IP klienta,__ aby dodać adres IP klienta do zakresu __adresów__. Możesz również użyć pola __ZAKRES ADRESÓW,__ aby ręcznie wprowadzić adres IP środowiska projektowego. Po dodaniu adresu IP klienta może on uzyskać dostęp do konta magazynu przy użyciu zestawu SDK.

   [![Okienko "Zapory i sieci wirtualne" w Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Zabezpieczanie kont usługi Azure Storage przy użyciu prywatnych punktów końcowych

Azure Machine Learning obsługuje konta magazynu skonfigurowane do używania punktów końcowych usługi lub prywatnych punktów końcowych. Jeśli konto magazynu używa prywatnych punktów końcowych, należy skonfigurować dwa prywatne punkty końcowe dla domyślnego konta magazynu:
1. Prywatny punkt końcowy z **podzródłem** docelowym obiektu blob.
1. Prywatny punkt końcowy z **podzródłem** docelowym pliku (udział plików).

![Zrzut ekranu przedstawiający stronę konfiguracji prywatnego punktu końcowego z opcjami obiektów blob i plików](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Aby skonfigurować prywatny punkt końcowy dla  konta magazynu, które nie jest magazynem domyślnym, wybierz typ docelowego podzrzucania, który odpowiada kontu magazynu, które chcesz dodać. 

Aby uzyskać więcej informacji, zobacz [Use private endpoints for Azure Storage (Używanie prywatnych punktów końcowych dla usługi Azure Storage)](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Zabezpieczanie magazynów danych i zestawów danych

W tej sekcji dowiesz się, jak używać magazynu danych i zestawów danych w środowisku zestawu SDK w sieci wirtualnej. Aby uzyskać więcej informacji na temat środowiska studio, zobacz Use Azure Machine Learning studio in a virtual network (Korzystanie [Azure Machine Learning studio w sieci wirtualnej).](how-to-enable-studio-virtual-network.md)

Aby uzyskać dostęp do danych przy użyciu zestawu SDK, należy użyć metody uwierzytelniania wymaganej przez poszczególne usługi, w których dane są przechowywane. Jeśli na przykład zarejestrujesz magazyn danych w celu uzyskania dostępu do usługi Azure Data Lake Store Gen2, nadal musisz używać jednostki usługi zgodnie z dokumentem Łączenie z usługami [Azure Storage.](how-to-access-data.md#azure-data-lake-storage-generation-2)

### <a name="disable-data-validation"></a>Wyłączanie walidacji danych

Domyślnie program Azure Machine Learning sprawdzanie poprawności danych i poświadczeń podczas próby uzyskania dostępu do danych przy użyciu zestawu SDK. Jeśli dane są za siecią wirtualną, Azure Machine Learning nie mogą ukończyć tych testów. Aby pominąć to sprawdzenie, należy utworzyć magazyny danych i zestawy danych, które pomijają walidację.

### <a name="use-datastores"></a>Korzystanie z magazynów danych

 Usługi Azure Data Lake Store Gen1 i Azure Data Lake Store Gen2 pomijają domyślnie walidację, więc nie są wymagane żadne dalsze działania. Jednak w przypadku następujących usług można użyć podobnej składni, aby pominąć walidację magazynu danych:

- Azure Blob Storage
- Udział plików platformy Azure
- PostgreSQL
- Azure SQL Database

Poniższy przykładowy kod tworzy nowy magazyn danych obiektów blob platformy Azure i ustawia obiekt `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Korzystanie z zestawów danych

Składnia pomijania walidacji zestawu danych jest podobna dla następujących typów zestawów danych:
- Plik rozdzielany
- JSON 
- Parquet
- SQL
- Plik

Poniższy kod tworzy nowy zestaw danych JSON i ustawia element `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Bezpieczne Azure Key Vault

Azure Machine Learning używa skojarzonego Key Vault do przechowywania następujących poświadczeń:
* Skojarzone ciągi połączenia konta magazynu
* Hasła do wystąpień usługi Azure Container Repository
* Parametry połączenia z magazynami danych

Aby korzystać Azure Machine Learning eksperymentowania z Azure Key Vault za siecią wirtualną, należy wykonać następujące czynności:

1. Przejdź do Key Vault, która jest skojarzona z obszarem roboczym.

1. Na stronie __Key Vault__ w okienku po lewej stronie wybierz pozycję __Sieć__.

1. Na karcie __Zapory i sieci wirtualne__ wykonaj następujące czynności:
    1. W __obszarze Zezwalaj na dostęp z__ usługi wybierz pozycję Prywatny punkt końcowy i wybrane __sieci.__
    1. W __obszarze Sieci wirtualne__ wybierz pozycję Dodaj istniejące sieci __wirtualne,__ aby dodać sieć wirtualną, w której znajdują się obliczenia eksperymentowania.
    1. W obszarze Allow trusted usługi firmy Microsoft to bypass this firewall (Zezwalaj zaufanym użytkownikom na __obejście tej zapory)__ wybierz pozycję __Yes (Tak).__

   [![Sekcja "Zapory i sieci wirtualne" w okienku Key Vault sieci](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Włączanie Azure Container Registry (ACR)

Aby używać Azure Container Registry sieci wirtualnej, należy spełnić następujące wymagania:

* Twoja Azure Container Registry musi być w wersji Premium. Aby uzyskać więcej informacji na temat uaktualniania, zobacz [Zmienianie jednostki SKU.](../container-registry/container-registry-skus.md#changing-tiers)

* Twoja Azure Container Registry musi znajdować się w tej samej sieci wirtualnej i podsieci co konto magazynu i docelowe obiekty obliczeniowe używane do trenowania lub wnioskowania.

* Twój Azure Machine Learning roboczy musi zawierać [Azure Machine Learning obliczeniowy](how-to-create-attach-compute-cluster.md).

    Jeśli za siecią wirtualną znajduje się Azure Machine Learning ACR, nie można jej użyć do bezpośredniego kompilowania obrazów platformy Docker. Zamiast tego klaster obliczeniowy jest używany do kompilowania obrazów.

    > [!IMPORTANT]
    > Klaster obliczeniowy używany do kompilowania obrazów platformy Docker musi mieć dostęp do repozytoriów pakietów, które są używane do trenowania i wdrażania modeli. Może być konieczne dodanie reguł zabezpieczeń sieci, które zezwalają na dostęp do publicznych repos, korzystanie z prywatnych pakietów języka [Python](how-to-use-private-python-packages.md)lub używanie niestandardowych obrazów [platformy Docker,](how-to-train-with-custom-image.md) które już zawierają te pakiety.

Po spełnieniu tych wymagań należy wykonać następujące kroki, aby włączyć Azure Container Registry.

> [!TIP]
> Jeśli podczas tworzenia obszaru roboczego nie Azure Container Registry istniejącego obszaru roboczego, może on nie istnieć. Domyślnie obszar roboczy nie utworzy wystąpienia usługi ACR, dopóki nie będzie go wymagać. Aby wymusić utworzenie modelu, wytrenuj lub wdróć model przy użyciu obszaru roboczego przed rozpoczęciem kroków w tej sekcji.

1. Znajdź nazwę obszaru Azure Container Registry, korzystając z jednej z następujących metod:

    __Witryna Azure Portal__

    W sekcji przeglądu obszaru roboczego wartość Rejestr __zawiera__ link do Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry obszaru roboczego" border="true":::

    __Interfejs wiersza polecenia platformy Azure__

    Jeśli zainstalowano [rozszerzenie Machine Learning interfejsu](reference-azure-machine-learning-cli.md)wiersza polecenia platformy Azure, możesz użyć `az ml workspace show` polecenia , aby wyświetlić informacje o obszarze roboczym.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    To polecenie zwraca wartość podobną do `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Ostatnia część ciągu to nazwa Azure Container Registry obszaru roboczego.

1. Ogranicz dostęp do sieci wirtualnej, korzystając z procedury opisanej w te [stronie Configure network access for registry (Konfigurowanie dostępu sieciowego dla rejestru).](../container-registry/container-registry-vnet.md#configure-network-access-for-registry) Podczas dodawania sieci wirtualnej wybierz sieć wirtualną i podsieć dla zasobów Azure Machine Learning wirtualnej.

1. Skonfiguruj usługę ACR dla obszaru roboczego, aby [zezwolić na dostęp przez zaufane usługi.](../container-registry/allow-access-trusted-services.md)

1. Użyj zestawu SDK Azure Machine Learning Python, aby skonfigurować klaster obliczeniowy do kompilowania obrazów platformy Docker. Poniższy fragment kodu pokazuje, jak to zrobić:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > Konto magazynu, klaster obliczeniowy i Azure Container Registry muszą znajdować się w tej samej podsieci sieci wirtualnej.
    
    Aby uzyskać więcej informacji, zobacz odwołanie do metody [update().](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-)

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest częścią drugiej z pięciu części serii sieci wirtualnych. Zobacz pozostałe artykuły, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 1. Omówienie sieci wirtualnej](how-to-network-security-overview.md)
* [Część 3. Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)
* [Część 4. Zabezpieczanie środowiska wnioskowania](how-to-secure-inferencing-vnet.md)
* [Część 5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)

Zobacz również artykuł na temat używania [niestandardowego systemu DNS](how-to-custom-dns.md) do rozpoznawania nazw.
