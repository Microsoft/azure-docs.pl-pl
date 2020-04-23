---
title: Przekazywanie wirtualnego dysku twardego w celu utworzenia wielu maszyn wirtualnych na platformie Azure
description: Przekaż uogólniony wirtualny dysk twardy do konta usługi Azure Storage, aby utworzyć maszynę wirtualną z systemem Windows, która będzie używana z modelem wdrażania Menedżer zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: e2ecdb6f436806f93610325b4d5adf28cb3253e2
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099635"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Przekaż uogólniony wirtualny dysk twardy do platformy Azure, aby utworzyć nową maszynę wirtualną

W tym temacie omówiono przekazywanie uogólnionego dysku niezarządzanego do konta magazynu, a następnie Tworzenie nowej maszyny wirtualnej przy użyciu przekazanego dysku. Uogólniony obraz VHD miał wszystkie informacje o koncie osobistym, które zostały usunięte przy użyciu programu Sysprep. 

Jeśli chcesz utworzyć maszynę wirtualną na podstawie wyspecjalizowanego wirtualnego dysku twardego w ramach konta magazynu, zobacz [Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego wirtualnego dysku twardego](sa-create-vm-specialized.md).

W tym temacie omówiono korzystanie z kont magazynu, ale zalecamy, aby klienci przechodzą do korzystania z Managed Disks zamiast tego. Aby zapoznać się z kompletnymi krokami przygotowywania, przekazywania i tworzenia nowej maszyny wirtualnej przy użyciu dysków zarządzanych, zobacz [Tworzenie nowej maszyny wirtualnej na podstawie uogólnionego wirtualnego dysku twardego przekazanego do platformy Azure przy użyciu Managed disks](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Uogólniony wirtualny dysk twardy otrzymał wszystkie informacje o koncie osobistym, które zostały usunięte przy użyciu programu Sysprep. Jeśli zamierzasz używać dysku VHD jako obrazu do tworzenia nowych maszyn wirtualnych, należy:
  
  * [Przygotuj wirtualny dysk twardy systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md). 
  * Uogólnianie maszyny wirtualnej za pomocą programu Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Uogólnianie maszyny wirtualnej z systemem Windows przy użyciu programu Sysprep
W tej sekcji pokazano, jak uogólnić maszynę wirtualną z systemem Windows w celu wykorzystania jej jako obrazu. Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa narzędzia Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Jeśli używasz programu Sysprep przed przekazaniem wirtualnego dysku twardego do platformy Azure po raz pierwszy, upewnij się, że [maszyna wirtualna została przygotowana](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem narzędzia Sysprep. 
> 
> 

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na **%windir%\system32\sysprep**, a następnie uruchom plik `sysprep.exe`.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.
4. W obszarze **Opcje zamykania**wybierz pozycję **Zamknij**.
5. Kliknij przycisk **OK**.
   
    ![Uruchom program Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Gdy narzędzie Sysprep zakończy działanie, maszyna wirtualna zostanie wyłączona. 

> [!IMPORTANT]
> Nie uruchamiaj ponownie maszyny wirtualnej, dopóki nie ukończysz przekazywania wirtualnego dysku twardego do platformy Azure ani tworzenia obrazu z maszyny wirtualnej. Jeśli maszyna wirtualna zostanie przypadkowo ponownie uruchomiona, uruchom program Sysprep, aby ponownie uogólnić.
> 
> 


## <a name="upload-the-vhd"></a>Przekazywanie wirtualnego dysku twardego

Przekaż wirtualny dysk twardy do konta usługi Azure Storage.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Jeśli nie masz jeszcze zainstalowanego programu PowerShell w wersji 1,4 lub nowszej, zapoznaj [się z tematem Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/overview).

1. Otwórz Azure PowerShell i zaloguj się na koncie platformy Azure. Zostanie otwarte okno podręczne, w którym można wprowadzić poświadczenia konta platformy Azure.
   
    ```powershell
    Connect-AzAccount
    ```
2. Uzyskaj identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ustaw poprawną subskrypcję przy użyciu identyfikatora subskrypcji. Zamień `<subscriptionID>` na identyfikator poprawnej subskrypcji.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Pobierz konto magazynu
Do przechowywania przekazanego obrazu maszyny wirtualnej jest potrzebne konto magazynu na platformie Azure. Możesz użyć istniejącego konta magazynu lub utworzyć nowe. 

Aby wyświetlić dostępne konta magazynu, wpisz:

```powershell
Get-AzStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przechodzenie do sekcji przekazywanie obrazu maszyny wirtualnej.

Jeśli musisz utworzyć konto magazynu, wykonaj następujące czynności:

1. Wymagana jest nazwa grupy zasobów, w której ma zostać utworzone konto magazynu. Aby sprawdzić wszystkie grupy zasobów, które znajdują się w Twojej subskrypcji, wpisz:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie Moja **zasobów** w regionie **zachodnie stany USA** , wpisz:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mojekontomagazynu** w tej grupie zasobów za pomocą polecenia cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Rozpocznij przekazywanie 

Użyj polecenia cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) , aby przekazać obraz do kontenera na koncie magazynu. Ten przykład przekazuje plik **myVHD. VHD** z `"C:\Users\Public\Documents\Virtual hard disks\"` do konta magazynu o nazwie **mojekontomagazynu** w **grupie zasobów zasobu** . Plik zostanie umieszczony w kontenerze o nazwie Moja **kontener, a nowa** nazwa pliku będzie **myUploadedVHD. VHD**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Jeśli to się powiedzie, otrzymasz odpowiedź podobną do:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

W zależności od połączenia sieciowego i rozmiaru pliku VHD, wykonanie tego polecenia może potrwać trochę czasu.


## <a name="create-a-new-vm"></a>Utworzenie nowej maszyny wirtualnej. 

Możesz teraz użyć przekazanego wirtualnego dysku twardego do utworzenia nowej maszyny wirtualnej. 

### <a name="set-the-uri-of-the-vhd"></a>Ustawianie identyfikatora URI wirtualnego dysku twardego

Identyfikator URI używanego wirtualnego dysku twardego ma format: https://**mojekontomagazynu****. blob.Core.Windows.NET/er**/**MyVhdName**. VHD. W tym przykładzie wirtualny dysk twardy o nazwie **myVHD** znajduje się na koncie magazynu **mojekontomagazynu** w **kontenerze**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utworzyć sieć wirtualną i podsieć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. Poniższy przykład tworzy podsieć o nazwie Moja **podsieć** **w grupie zasobów Grupa zasobu z** prefiksem adresu **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Utwórz sieć wirtualną. Poniższy przykład tworzy sieć wirtualną o nazwie **myVnet** w lokalizacji **zachodnie stany USA** z prefiksem adresu **10.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Tworzenie publicznego adresu IP i interfejsu sieciowego
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Utwórz publiczny adres IP. Ten przykład tworzy publiczny adres IP o nazwie **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. Ten przykład tworzy kartę sieciową o nazwie **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły RDP
Aby można było zalogować się do maszyny wirtualnej przy użyciu protokołu RDP, należy mieć regułę zabezpieczeń, która zezwala na dostęp protokołu RDP na porcie 3389. 

Ten przykład tworzy sieciowej grupy zabezpieczeń o nazwie **myNsg** , który zawiera regułę o nazwie **myRdpRule** , która zezwala na ruch RDP przez port 3389. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [otwieranie portów do maszyny wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Utwórz zmienną dla sieci wirtualnej
Utwórz zmienną dla kompletnej sieci wirtualnej. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
Poniższy skrypt programu PowerShell pokazuje, jak skonfigurować konfiguracje maszyn wirtualnych i użyć przekazanego obrazu maszyny wirtualnej jako źródła nowej instalacji.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Po zakończeniu powinna zostać wyświetlona nowo utworzona maszyna wirtualna w [Azure Portal](https://portal.azure.com) w obszarze **przeglądanie** > **maszyn wirtualnych**lub przy użyciu następujących poleceń programu PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Aby zarządzać nową maszyną wirtualną za pomocą Azure PowerShell, zobacz [Zarządzanie maszynami wirtualnymi przy użyciu Azure Resource Manager i programu PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


