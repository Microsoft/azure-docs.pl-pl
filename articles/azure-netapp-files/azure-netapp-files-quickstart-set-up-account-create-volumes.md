---
title: 'Szybki start: konfigurowanie woluminów Azure NetApp Files i NFS'
description: Szybki start — opis sposobu szybkiego Azure NetApp Files i tworzenia woluminu.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: 0b48963fa6cb28c836c57de8b46861ef83752231
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388566"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Szybki start: konfigurowanie Azure NetApp Files i tworzenie woluminu NFS

W tym artykule pokazano, jak szybko skonfigurować Azure NetApp Files i utworzyć wolumin NFS. 

W tym przewodniku Szybki start skonfigurujemy następujące elementy:

- Rejestracja dla Azure NetApp Files i dostawcy zasobów NetApp
- Konto netapp
- Pula pojemności
- Wolumin NFS dla Azure NetApp Files

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Aby wyświetlić wszystkie funkcje, które można włączyć dla woluminu NFS i istotne zagadnienia, zobacz [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md). 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

> [!IMPORTANT]
> Musisz mieć dostęp do usługi Azure NetApp Files usługi. Aby zażądać dostępu do usługi, zobacz stronę przesyłania [Azure NetApp Files listy oczekiwania.](https://aka.ms/azurenetappfiles)  Przed kontynuowaniem musisz poczekać na oficjalną wiadomość e-mail Azure NetApp Files e-mail z potwierdzeniem.

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Rejestrowanie się w Azure NetApp Files i dostawcy zasobów NetApp

> [!NOTE]
> Proces rejestracji może trochę potrwać.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby wykonać kroki rejestracji przy użyciu portalu, otwórz sesję Cloud Shell, jak podano powyżej, i wykonaj następujące kroki interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Ten artykuł z 2.6.0 wymaga modułu Azure PowerShell Az w wersji 2.6.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli wolisz, możesz użyć konsoli Cloud Shell w sesji programu PowerShell.

1. W wierszu polecenia programu PowerShell (lub w sesji Cloud Shell PowerShell) określ subskrypcję, która została zatwierdzona dla Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Zarejestruj dostawcę zasobów platformy Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Przygotowywanie środowiska dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Szablon](#tab/template)

Brak.

Użyj interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby zarejestrować się Azure NetApp Files usługi i dostawcy zasobów NetApp.

Aby [uzyskać więcej informacji, Azure NetApp Files](azure-netapp-files-register.md) register for Azure NetApp Files register for Azure NetApp Files (Rejestrowanie się w celu zarejestrowania się).

---

## <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W polu Azure Portal pola wyszukiwania wpisz **Azure NetApp Files** a następnie wybierz **Azure NetApp Files** z wyświetlonej listy.

      ![Wybierz Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknij pozycję **+ Dodaj**, aby utworzyć nowe konto usługi NetApp.

     ![Tworzenie nowego konta netapp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. W oknie Nowe konto usługi NetApp podaj następujące informacje:
   1. Wprowadź **myaccount1** jako nazwę konta.
   2. Wybierz subskrypcję.
   3. Wybierz **pozycję Utwórz nową,** aby utworzyć nową grupę zasobów. Wprowadź **myRG1** jako nazwę grupy zasobów. Kliknij przycisk **OK**.
   4. Wybierz lokalizację konta.

      ![Okno nowego konta netapp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)

      ![Okno Grupy zasobów](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kliknij **pozycję Utwórz,** aby utworzyć nowe konto netapp.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zdefiniuj pewne zmienne, aby można było odwoływać się do nich w pozostałych przykładach:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ```

    > [!NOTE]
    > Aby uzyskać listę [obsługiwanych regionów, zapoznaj](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) się z tematem Products available by region (Dostępne produkty według regionów).
    > Aby uzyskać nazwę regionu obsługiwaną przez nasze narzędzia wiersza polecenia, użyj polecenia `Get-AzLocation | select Location`
    >

1. Utwórz nową grupę zasobów za pomocą [polecenia New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Utwórz Azure NetApp Files za pomocą [polecenia New-AzNetAppFilesAccount:](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount)

    ```powershell-interactive
    New-AzNetAppFilesAccount -ResourceGroupName $resourceGroup -Location $location -Name $anfAccountName
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Zdefiniuj pewne zmienne, aby można było odwoływać się do nich w pozostałej części przykładów:

    ```azurecli-interactive
    RESOURCE_GROUP="myRG1"
    LOCATION="eastus"
    ANF_ACCOUNT_NAME="myaccount1"
    ```

    > [!NOTE]
    > Aby uzyskać listę [obsługiwanych regionów, zapoznaj](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) się z tematem Products available by region (Dostępne produkty według regionów).
    > Aby uzyskać nazwę regionu obsługiwaną przez nasze narzędzia wiersza polecenia, użyj polecenia `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Utwórz nową grupę zasobów za pomocą [polecenia az group create:](/cli/azure/group#az-group-create)

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Utwórz Azure NetApp Files za pomocą [polecenia az netappfiles account create:](/cli/azure/netappfiles/account#az-netappfiles-account-create)

    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Szablon](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Poniższy fragment kodu przedstawia sposób tworzenia konta usługi NetApp w szablonie usługi Azure Resource Manager (szablon arm) przy użyciu [zasobu Microsoft.NetApp/netAppAccounts.](/azure/templates/microsoft.netapp/netappaccounts) Aby uruchomić kod, pobierz pełny [szablon usługi ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z naszego repozytorium GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W bloku Azure NetApp Files zarządzania wybierz swoje konto netapp **(myaccount1).**

    ![Wybieranie konta netapp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)

2. W bloku Azure NetApp Files zarządzania konta netApp kliknij pozycję **Pule pojemności.**

    ![Kliknij pozycję Pule pojemności](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)

3. Kliknij **pozycję + Dodaj pule.**

    ![Kliknij pozycję Dodaj pule](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Podaj informacje dotyczące puli pojemności:
    * Wprowadź **mypool1** jako nazwę puli.
    * Wybierz **pozycję Premium** jako poziom usługi.
    * Określ **rozmiar puli 4 (TiB).**
    * Użyj typu **Automatycznego** QoS.

5. Kliknij pozycję **Utwórz**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Definiowanie niektórych nowych zmiennych do przyszłego odwołania

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Tworzenie nowej puli pojemności przy użyciu [new-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Definiowanie niektórych nowych zmiennych do przyszłego odwołania

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Tworzenie nowej puli pojemności przy użyciu narzędzia [az netappfiles pool create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create)

    ```azurecli-interactive
    az netappfiles pool create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --size $POOL_SIZE_TiB \
        --service-level $SERVICE_LEVEL
    ```

# <a name="template"></a>[Szablon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Poniższy fragment kodu przedstawia sposób tworzenia puli pojemności w szablonie usługi Azure Resource Manager (arm) przy użyciu zasobu [Microsoft.NetApp/netAppAccounts/capacityPools.](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) Aby uruchomić kod, pobierz pełny [szablon usługi ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z repozytorium GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Tworzenie woluminu NFS dla usługi Azure NetApp Files

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W bloku Azure NetApp Files zarządzania usługą NetApp kliknij pozycję **Woluminy**.

    ![Klikanie pozycji Woluminy](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)

2. Kliknij pozycję **+ Dodaj wolumin**.

    ![Kliknij pozycję Dodaj woluminy](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)

3. W oknie Tworzenie woluminu podaj informacje dotyczące woluminu:
   1. Wprowadź **myvol1** jako nazwę woluminu.
   2. Wybierz pulę pojemności **(mypool1).**
   3. Użyj wartości domyślnej dla limitu przydziału.
   4. W obszarze sieć wirtualna kliknij pozycję **Utwórz nową,** aby utworzyć nową sieć wirtualną platformy Azure.  Następnie wprowadź następujące informacje:
       * Wprowadź **myvnet1** jako nazwę sieci wirtualnej.
       * Określ przestrzeń adresową dla ustawienia, na przykład 10.7.0.0/16
       * Wprowadź **myANFsubnet** jako nazwę podsieci.
       * Określ zakres adresów podsieci, na przykład 10.7.0.0/24. Dedykowanej podsieci nie można udostępniać innym zasobom.
       * Wybierz **pozycję Microsoft.NetApp/volumes w** celu delegowania podsieci.
       * Kliknij **przycisk OK,** aby utworzyć sieć wirtualną.
   5. W podsieci wybierz nowo utworzoną sieć wirtualną **(myvnet1)** jako podsieć delegata.

      ![Okno tworzenia woluminu](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)

      ![Okno Tworzenie sieci wirtualnej](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)

4. Kliknij **pozycję Protokół**, a następnie wykonaj następujące czynności:
    * Wybierz **NFS** jako typ protokołu dla woluminu.
    * Wprowadź **myfilepath1** jako ścieżkę pliku, która zostanie użyta do utworzenia ścieżki eksportu dla woluminu.
    * Wybierz wersję systemu plików **NFS (NFSv3** lub **NFSv4.1)** dla woluminu.
      Zapoznaj [się z zagadnieniami](azure-netapp-files-create-volumes.md#considerations) [i najlepszymi rozwiązaniami w](azure-netapp-files-create-volumes.md#best-practice) zakresie wersji systemu plików NFS.

    ![Określanie protokołu NFS na platformie Szybki start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Kliknij pozycję **Przejrzyj i utwórz**.

    ![Okno Przeglądania i tworzenia](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)

6. Przejrzyj informacje o woluminie, a następnie kliknij pozycję **Utwórz**.
    Utworzony wolumin zostanie wyświetlony w bloku Woluminy.

    ![Utworzony wolumin](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz delegowanie podsieci do "Microsoft.NetApp/volumes" za [pomocą polecenia New-AzDelegation.](/powershell/module/az.network/new-azdelegation)

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Utwórz konfigurację podsieci za pomocą [polecenia New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Utwórz sieć wirtualną za pomocą [polecenia New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork)

    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Utwórz wolumin za pomocą [polecenia New-AzNetAppFilesVolume.](/powershell/module/az.netappfiles/new-aznetappfilesvolume)

    ```powershell-interactive
    $volumeSizeBytes = 1099511627776 # 100GiB
    $subnetId = $vnet.Subnets[0].Id

    New-AzNetAppFilesVolume -ResourceGroupName $resourceGroup `
        -Location $location `
        -AccountName $anfAccountName `
        -PoolName $poolName `
        -Name "myvol1" `
        -UsageThreshold $volumeSizeBytes `
        -SubnetId $subnetId `
        -CreationToken "myfilepath1" `
        -ServiceLevel $serviceLevel `
        -ProtocolType NFSv3
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Definiowanie niektórych zmiennych do późniejszego użycia.

    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Utwórz sieć wirtualną bez podsieci za pomocą [polecenia az network vnet create.](/cli/azure/network/vnet#az-network-vnet-create)

    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Utwórz delegowaną podsieć za pomocą [polecenia az network vnet subnet create.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Utwórz wolumin za pomocą [polecenia az netappfiles volume create.](/cli/azure/netappfiles/volume#az-netappfiles-volume-create)

    ```azurecli-interactive
    VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
    SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
    VOLUME_SIZE_GiB=100 # 100 GiB
    UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within subscription and region

    az netappfiles volume create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME \
        --pool-name $POOL_NAME \
        --name "myvol1" \
        --service-level $SERVICE_LEVEL \
        --vnet $VNET_ID \
        --subnet $SUBNET_ID \
        --usage-threshold $VOLUME_SIZE_GiB \
        --file-path $UNIQUE_FILE_PATH \
        --protocol-types "NFSv3"
    ```

# <a name="template"></a>[Szablon](#tab/template)

<!-- [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)] -->

Poniższe fragmenty kodu pokazują, jak skonfigurować sieć wirtualną i utworzyć wolumin Azure NetApp Files w szablonie Azure Resource Manager (szablon USŁUGI ARM). Konfiguracja sieci wirtualnej używa [zasobu Microsoft.Network/virtualNetworks.](/azure/templates/Microsoft.Network/virtualNetworks) Podczas tworzenia woluminu [jest używany zasób Microsoft.NetApp/netAppAccounts/capacityPools/volumes.](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) Aby uruchomić kod, pobierz pełny [szablon usługi ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z repozytorium GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

# <a name="portal"></a>[Portal](#tab/azure-portal)

Gdy wszystko będzie gotowe, a jeśli chcesz, możesz usunąć grupę zasobów. Akcja usunięcia grupy zasobów jest nieodwracalna.

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie będzie można ich cofnąć.

1. W polu Azure Portal pola wyszukiwania wpisz **Azure NetApp Files** a następnie wybierz **Azure NetApp Files** z wyświetlonej listy.

2. Na liście subskrypcji kliknij grupę zasobów (myRG1), którą chcesz usunąć.

    ![Przechodzenie do grup zasobów](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stronie grupy zasobów kliknij pozycję **Usuń grupę zasobów.**

    ![Zrzut ekranu przedstawiający przycisk Usuń grupę zasobów.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png)

    Zostanie otwarte okno zawierające ostrzeżenie dotyczące zasobów, które zostaną usunięte razem z grupą zasobów.

4. Wprowadź nazwę grupy zasobów (myRG1), aby potwierdzić trwałe usunięcie grupy zasobów i wszystkich jej zasobów, a następnie kliknij przycisk **Usuń.**

    ![Potwierdzanie usunięcia grupy zasobów](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png )

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Gdy wszystko będzie gotowe, a jeśli chcesz, możesz usunąć grupę zasobów. Akcja usunięcia grupy zasobów jest nieodwracalna.

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie będzie można ich cofnąć.

1. Usuń grupę zasobów za pomocą [polecenia Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Gdy wszystko będzie gotowe, a jeśli chcesz, możesz usunąć grupę zasobów. Akcja usunięcia grupy zasobów jest nieodwracalna.

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie będzie można ich cofnąć.

1. Usuń grupę zasobów za pomocą [polecenia az group delete.](/cli/azure/group#az-group-delete)

    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Szablon](#tab/template)

Brak.

Użyj interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby usunąć grupę zasobów.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Hierarchia magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)

> [!div class="nextstepaction"]
> [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)

> [!div class="nextstepaction"]
> [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md)
