---
title: 'Szybki Start: Konfigurowanie Azure NetApp Files i woluminu NFS'
description: Przewodnik Szybki start — opis sposobu szybkiego konfigurowania Azure NetApp Files i tworzenia woluminu.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-azurecli, subject-armqs
ms.openlocfilehash: 088961d58cee2f1bb0c6ac042d9d8ee2ad50ba14
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931363"
---
# <a name="quickstart-set-up-azure-netapp-files-and-create-an-nfs-volume"></a>Szybki Start: Konfigurowanie Azure NetApp Files i Tworzenie woluminu NFS 

W tym artykule pokazano, jak szybko skonfigurować Azure NetApp Files i utworzyć wolumin. 

W tym przewodniku szybki start skonfigurujesz następujące elementy:

- Rejestracja dla Azure NetApp Files i dostawcy zasobów NetApp
- Konto NetApp
- Pula pojemności
- Wolumin systemu plików NFS dla Azure NetApp Files

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Zanim rozpoczniesz 

> [!IMPORTANT] 
> Musisz mieć udzielony dostęp do usługi Azure NetApp Files.  Aby zażądać dostępu do usługi, zobacz [stronę Azure NetApp Files waitlist](https://aka.ms/azurenetappfiles).  Przed kontynuowaniem musisz poczekać na oficjalną wiadomość e-mail z potwierdzeniem z zespołu Azure NetApp Files. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

---

## <a name="register-for-azure-netapp-files-and-netapp-resource-provider"></a>Zarejestruj się, aby uzyskać Azure NetApp Files i dostawcę zasobów NetApp

> [!NOTE]
> Proces rejestracji może trochę potrwać.
>

# <a name="portal"></a>[Portal](#tab/azure-portal)

W przypadku kroków rejestracji przy użyciu portalu Otwórz sesję Cloud Shell, jak wskazano powyżej, i wykonaj następujące kroki interfejsu wiersza polecenia platformy Azure:

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Ten artykuł instruktażowy wymaga modułu Azure PowerShell AZ Version 2.6.0 lub nowszego. Uruchom polecenie `Get-Module -ListAvailable Az`, aby określić bieżącą wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli wolisz, możesz zamiast tego użyć konsoli Cloud Shell w sesji programu PowerShell.

1. W wierszu polecenia programu PowerShell (lub w sesji Cloud Shell programu PowerShell) Określ subskrypcję, która została zatwierdzona dla Azure NetApp Files:
    ```powershell-interactive
    Select-AzSubscription -Subscription <subscriptionId>
    ```

2. Zarejestruj dostawcę zasobów platformy Azure:
    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.NetApp
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

[!INCLUDE [azure-netapp-files-cloudshell-include](../../includes/azure-netapp-files-azure-cloud-shell-window.md)]

# <a name="template"></a>[Szablon](#tab/template)

Brak.  

Użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, aby zarejestrować Azure NetApp Files i dostawcę zasobów NetApp.  

Aby uzyskać więcej informacji, zobacz artykuł [Rejestrowanie na Azure NetApp Files](azure-netapp-files-register.md) . 

---

## <a name="create-a-netapp-account"></a>Tworzenie konta usługi NetApp

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W polu wyszukiwania Azure Portal wprowadź **Azure NetApp Files** a następnie wybierz pozycję **Azure NetApp Files** z wyświetlonej listy.

      ![Wybierz Azure NetApp Files](../media/azure-netapp-files/azure-netapp-files-select-azure-netapp-files.png)

2. Kliknij pozycję **+ Dodaj**, aby utworzyć nowe konto usługi NetApp.

     ![Utwórz nowe konto NetApp](../media/azure-netapp-files/azure-netapp-files-create-new-netapp-account.png)

3. W oknie nowe konto NetApp podaj następujące informacje: 
   1. Wprowadź **myaccount1** dla nazwy konta. 
   2. Wybierz subskrypcję.
   3. Wybierz pozycję **Utwórz nowy** , aby utworzyć nową grupę zasobów. Wprowadź **myRG1** dla nazwy grupy zasobów. Kliknij przycisk **OK**. 
   4. Wybierz lokalizację konta.  

      ![Nowe okno konta NetApp](../media/azure-netapp-files/azure-netapp-files-new-account-window.png)  

      ![Okno grupy zasobów](../media/azure-netapp-files/azure-netapp-files-resource-group-window.png)

4. Kliknij przycisk **Utwórz** , aby utworzyć nowe konto NetApp.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Zdefiniuj pewne zmienne, aby można było odwoływać się do nich w pozostałej części przykładów:

    ```powershell-interactive
    $resourceGroup = "myRG1"
    $location = "eastus"
    $anfAccountName = "myaccount1"
    ``` 

    > [!NOTE]
    > Listę obsługiwanych regionów można znaleźć w [obszarze produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > Aby uzyskać nazwę regionu obsługiwaną przez nasze narzędzia wiersza polecenia, użyj polecenia `Get-AzLocation | select Location`
    >

1. Utwórz nową grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

    ```powershell-interactive
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

2. Utwórz konto Azure NetApp Files za pomocą polecenia [New-AzNetAppFilesAccount](/powershell/module/az.netappfiles/New-AzNetAppFilesAccount) :
   
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
    > Listę obsługiwanych regionów można znaleźć w [obszarze produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) .
    > Aby uzyskać nazwę regionu obsługiwaną przez nasze narzędzia wiersza polecenia, użyj polecenia `az account list-locations --query "[].{Region:name}" --out table`
    >

2. Utwórz nową grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az-group-create) :

    ```azurecli-interactive
    az group create \
        --name $RESOURCE_GROUP \
        --location $LOCATION
    ```

3. Utwórz konto Azure NetApp Files za pomocą polecenia [AZ netappfiles Account Create](/cli/azure/netappfiles/account#az-netappfiles-account-create) :
   
    ```azurecli-interactive
    az netappfiles account create \
        --resource-group $RESOURCE_GROUP \
        --location $LOCATION \
        --account-name $ANF_ACCOUNT_NAME
    ```

# <a name="template"></a>[Szablon](#tab/template)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Poniższy fragment kodu przedstawia sposób tworzenia konta NetApp w szablonie Azure Resource Manager (szablon ARM) przy użyciu zasobu [Microsoft. NetApp/netAppAccounts](/azure/templates/microsoft.netapp/netappaccounts) . Aby uruchomić kod, Pobierz [pełny szablon usługi ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z repozytorium GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="177-183":::

<!-- Block begins with "type": "Microsoft.NetApp/netAppAccounts", -->

---

## <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W bloku zarządzanie Azure NetApp Files wybierz konto NetApp (**myaccount1**).

    ![Wybierz konto NetApp](../media/azure-netapp-files/azure-netapp-files-select-netapp-account.png)  

2. W bloku zarządzanie Azure NetApp Files konta NetApp kliknij pozycję **Pule pojemności**.

    ![Kliknij pozycję Pule pojemności](../media/azure-netapp-files/azure-netapp-files-click-capacity-pools.png)  

3. Kliknij pozycję **+ Dodaj pule**. 

    ![Kliknij pozycję Dodaj pule](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)  

4. Podaj informacje dotyczące puli pojemności: 
    * Wprowadź **mypool1** jako nazwę puli.
    * Wybierz pozycję **Premium** dla poziomu usługi. 
    * Określ **4 (TIB)** jako rozmiar puli. 
    * Użyj typu **autoqos.**

5. Kliknij przycisk **Utwórz**.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Definiowanie nowych zmiennych do użytku w przyszłości

    ```powershell-interactive
    $poolName = "mypool1"
    $poolSizeBytes = 4398046511104 # 4TiB
    $serviceLevel = "Premium" # Valid values are Standard, Premium and Ultra
    ```

1. Utwórz nową pulę pojemności przy użyciu polecenia [New-AzNetAppFilesPool](/powershell/module/az.netappfiles/new-aznetappfilespool)

    ```powershell-interactive
    New-AzNetAppFilesPool -ResourceGroupName $resourceGroup -Location $location -AccountName $anfAccountName -Name $poolName -PoolSize $poolSizeBytes -ServiceLevel $serviceLevel
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

1. Definiowanie nowych zmiennych do użytku w przyszłości

    ```azurecli-interactive
    POOL_NAME="mypool1"
    POOL_SIZE_TiB=4 # Size in Azure CLI needs to be in TiB unit (minimum 4 TiB)
    SERVICE_LEVEL="Premium" # Valid values are Standard, Premium and Ultra
    ```

2. Utwórz nową pulę pojemności przy użyciu polecenia [AZ netappfiles Pool Create](/cli/azure/netappfiles/pool#az-netappfiles-pool-create) 

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

Poniższy fragment kodu przedstawia sposób tworzenia puli pojemności w szablonie Azure Resource Manager (szablon ARM) przy użyciu zasobu [Microsoft. NetApp/netAppAccounts/capacityPools](/azure/templates/microsoft.netapp/netappaccounts/capacitypools) . Aby uruchomić kod, Pobierz [pełny szablon usługi ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z repozytorium GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="184-196":::

<!-- LN 185, block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools", -->

---

## <a name="create-nfs-volume-for-azure-netapp-files"></a>Utwórz wolumin systemu plików NFS dla Azure NetApp Files

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W bloku zarządzanie Azure NetApp Files konta NetApp kliknij pozycję **woluminy**.

    ![Klikanie pozycji Woluminy](../media/azure-netapp-files/azure-netapp-files-click-volumes.png)  

2. Kliknij pozycję **+ Dodaj wolumin**.

    ![Kliknij pozycję Dodaj woluminy](../media/azure-netapp-files/azure-netapp-files-click-add-volumes.png)  

3. W oknie Tworzenie woluminu podaj informacje dotyczące woluminu: 
   1. Wprowadź **myvol1** jako nazwę woluminu. 
   2. Wybierz swoją pulę pojemności (**mypool1**).
   3. Użyj wartości domyślnej dla limitu przydziału. 
   4. W obszarze Sieć wirtualna kliknij pozycję **Utwórz nową** , aby utworzyć nową sieć wirtualną platformy Azure.  Następnie uzupełnij następujące informacje:
       * Wprowadź **myvnet1** jako nazwę sieci wirtualnej.
       * Określ przestrzeń adresową dla ustawienia, na przykład 10.7.0.0/16
       * Wprowadź **myANFsubnet** jako nazwę podsieci.
       * Określ zakres adresów podsieci, na przykład 10.7.0.0/24. Nie można udostępnić dedykowanej podsieci innym zasobom.
       * Wybierz pozycję **Microsoft. NetApp/Volumes** dla delegowania podsieci.
       * Kliknij przycisk **OK** , aby utworzyć sieć wirtualną.
   5. W obszarze podsieć wybierz nowo utworzoną sieć wirtualną (**myvnet1**) jako podsiecię delegata.

      ![Utwórz okno woluminu](../media/azure-netapp-files/azure-netapp-files-create-volume-window.png)  

      ![Utwórz okno sieci wirtualnej](../media/azure-netapp-files/azure-netapp-files-create-virtual-network-window.png)  

4. Kliknij pozycję **Protokół**, a następnie wykonaj następujące czynności: 
    * Wybierz system **plików NFS** jako typ protokołu dla woluminu.  
    * Wprowadź **myfilepath1** jako ścieżkę pliku, która zostanie użyta do utworzenia ścieżki eksportu dla woluminu.  
    * Wybierz wersję systemu plików NFS (**NFSv3** lub **nfsv 4.1**) dla woluminu.  
      Zapoznaj się z [zagadnieniami](azure-netapp-files-create-volumes.md#considerations) i [najlepszym rozwiązaniem](azure-netapp-files-create-volumes.md#best-practice) dotyczącym wersji systemu plików NFS. 
      
  ![Określanie protokołu NFS dla przewodnika Szybki Start](../media/azure-netapp-files/azure-netapp-files-quickstart-protocol-nfs.png)

5. Kliknij pozycję **Przejrzyj i utwórz**.

    ![Przeglądanie i tworzenie okna](../media/azure-netapp-files/azure-netapp-files-review-and-create-window.png)  

6. Przejrzyj informacje o woluminie, a następnie kliknij przycisk **Utwórz**.  
    Utworzony wolumin zostanie wyświetlony w bloku woluminy.

    ![Utworzono wolumin](../media/azure-netapp-files/azure-netapp-files-create-volume-created.png)  

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz delegowanie podsieci do "Microsoft. NetApp/Volumes" przy użyciu polecenia [New-AzDelegation](/powershell/module/az.network/new-azdelegation) .

    ```powershell-interactive
    $anfDelegation = New-AzDelegation -Name ([guid]::NewGuid().Guid) -ServiceName "Microsoft.NetApp/volumes"
    ```

2. Utwórz konfigurację podsieci przy użyciu polecenia [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

    ```powershell-interactive
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "myANFSubnet" -AddressPrefix "10.7.0.0/24" -Delegation $anfDelegation
    ```

3. Utwórz sieć wirtualną za pomocą polecenia [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) .
    
    ```powershell-interactive
    $vnet = New-AzVirtualNetwork -Name "myvnet1" -ResourceGroupName $resourceGroup -Location $location -AddressPrefix "10.7.0.0/16" -Subnet $subnet
    ```

4. Utwórz wolumin za pomocą polecenia [New-AzNetAppFilesVolume](/powershell/module/az.netappfiles/new-aznetappfilesvolume) .
   
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

1. Definiowanie zmiennych do późniejszego użycia.
    
    ```azurecli-interactive
    VNET_NAME="myvnet1"
    SUBNET_NAME="myANFSubnet"
    ```

1. Utwórz sieć wirtualną bez podsieci przy użyciu polecenia [AZ Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create) .
    
    ```azurecli-interactive
    az network vnet create \
        --resource-group $RESOURCE_GROUP \
        --name $VNET_NAME \
        --location $LOCATION \
        --address-prefix "10.7.0.0/16"

    ```

2. Utwórz podsieć delegowaną za pomocą polecenia [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) .

    ```azurecli-interactive
    az network vnet subnet create \
        --resource-group $RESOURCE_GROUP \
        --vnet-name $VNET_NAME \
        --name $SUBNET_NAME \
        --address-prefixes "10.7.0.0/24" \
        --delegations "Microsoft.NetApp/volumes"
    ```

3. Utwórz wolumin za pomocą polecenia [AZ netappfiles Volume Create](/cli/azure/netappfiles/volume#az-netappfiles-volume-create) .
   
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

Poniższe fragmenty kodu pokazują, jak skonfigurować sieć wirtualną i utworzyć wolumin Azure NetApp Files w szablonie Azure Resource Manager (szablon ARM). Konfiguracja sieci wirtualnej używa zasobu [Microsoft. Network/virtualNetworks](/azure/templates/Microsoft.Network/virtualNetworks) . Tworzenie woluminu używa zasobu [Microsoft. NetApp/netAppAccounts/capacityPools/Volumes](/azure/templates/microsoft.netapp/netappaccounts/capacitypools/volumes) . Aby uruchomić kod, Pobierz [pełny szablon usługi ARM](https://github.com/Azure/azure-quickstart-templates/blob/master/101-anf-nfs-volume/azuredeploy.json) z repozytorium GitHub.

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="148-176":::

<!-- Block begins with  "type": "Microsoft.Network/virtualNetworks", -->

:::code language="json" source="~/quickstart-templates/101-anf-nfs-volume/azuredeploy.json" range="197-229":::

<!-- Block begins with  "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes", -->

---

## <a name="clean-up-resources"></a>Czyszczenie zasobów

# <a name="portal"></a>[Portal](#tab/azure-portal)

Gdy wszystko będzie gotowe, a jeśli chcesz, możesz usunąć grupę zasobów. Akcja usuwania grupy zasobów jest nieodwracalna.  

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie można ich cofnąć. 

1. W polu wyszukiwania Azure Portal wprowadź **Azure NetApp Files** a następnie wybierz pozycję **Azure NetApp Files** z wyświetlonej listy.

2. Na liście subskrypcji kliknij grupę zasobów (myRG1), którą chcesz usunąć. 

    ![Przejdź do grup zasobów](../media/azure-netapp-files/azure-netapp-files-azure-navigate-to-resource-groups.png)


3. Na stronie Grupa zasobów kliknij pozycję **Usuń grupę zasobów**.

    ![Zrzut ekranu, który podświetla przycisk Usuń grupę zasobów.](../media/azure-netapp-files/azure-netapp-files-azure-delete-resource-group.png) 

    Zostanie otwarte okno zawierające ostrzeżenie dotyczące zasobów, które zostaną usunięte razem z grupą zasobów.

4. Wprowadź nazwę grupy zasobów (myRG1), aby potwierdzić, że chcesz trwale usunąć grupę zasobów i wszystkie znajdujące się w niej zasoby, a następnie kliknij przycisk **Usuń**.

    ![Potwierdzenie usunięcia grupy zasobów](../media/azure-netapp-files/azure-netapp-files-azure-confirm-resource-group-deletion.png ) 

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Gdy wszystko będzie gotowe, a jeśli chcesz, możesz usunąć grupę zasobów. Akcja usuwania grupy zasobów jest nieodwracalna.  

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie można ich cofnąć.

1. Usuń grupę zasobów za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .
   
    ```powershell-interactive
    Remove-AzResourceGroup -Name $resourceGroup
    ```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Gdy wszystko będzie gotowe, a jeśli chcesz, możesz usunąć grupę zasobów. Akcja usuwania grupy zasobów jest nieodwracalna.  

> [!IMPORTANT]
> Wszystkie zasoby w grupach zasobów zostaną trwale usunięte i nie można ich cofnąć.

1. Usuń grupę zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .
   
    ```azurecli-interactive
    az group delete \
        --name $RESOURCE_GROUP
    ```

# <a name="template"></a>[Szablon](#tab/template)

Brak.

Aby usunąć grupę zasobów, użyj Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.   

---

## <a name="next-steps"></a>Następne kroki  

> [!div class="nextstepaction"]
> [Hierarchia magazynu Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)   
> [Poziomy usługi dla Azure NetApp Files](azure-netapp-files-service-levels.md)   
> [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md)