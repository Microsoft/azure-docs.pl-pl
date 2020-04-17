---
title: Przekazywanie uogólnionej dysku VHD w celu utworzenia wielu maszyn wirtualnych na platformie Azure
description: Przekaż uogólniony dysk VHD na konto magazynu platformy Azure, aby utworzyć maszynę wirtualną systemu Windows do użycia z modelem wdrażania Menedżera zasobów.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 7a5aa05a9045548e15aba667fdcdbd14fc8990e6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460311"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Przekazywanie uogólnionego dysku twardego na platformę Azure w celu utworzenia nowej maszyny Wirtualnej

W tym temacie opisano przekazywanie uogólnionego dysku niezarządzanego do konta magazynu, a następnie tworzenie nowej maszyny Wirtualnej przy użyciu przekazanego dysku. Uogólniony obraz VHD miał wszystkie dane osobowe konta usunięte za pomocą Sysprep. 

Jeśli chcesz utworzyć maszynę wirtualną ze specjalistycznego dysku VHD na koncie magazynu, zobacz [Tworzenie maszyny Wirtualnej ze specjalistycznego dysku VHD](sa-create-vm-specialized.md).

W tym temacie omówiono przy użyciu kont magazynu, ale zaleca się klientom przejście do korzystania z dysków zarządzanych zamiast. Aby uzyskać pełną informacje na temat przygotowania, przekazywania i tworzenia nowej maszyny wirtualnej przy użyciu dysków zarządzanych, zobacz [Tworzenie nowej maszyny Wirtualnej z uogólnionej dysków wirtualnych przekazanych na platformę Azure przy użyciu dysków zarządzanych.](upload-generalized-managed.md)

 

## <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej

Uogólniony VHD miał wszystkie dane osobowe konta usunięte za pomocą Sysprep. Jeśli zamierzasz użyć dysku wirtualnego jako obrazu do tworzenia nowych maszyn wirtualnych z, należy:
  
  * [Przygotuj dysk VHD systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md). 
  * Uogólnianie maszyny wirtualnej przy użyciu programu Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Uogólnianie maszyny wirtualnej systemu Windows przy użyciu programu Sysprep
W tej sekcji pokazano, jak uogólniać maszynę wirtualną systemu Windows do użycia jako obraz. Narzędzie Sysprep między innymi usuwa wszystkie informacje osobiste związane z kontem i przygotowuje maszynę do używania jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez sysprep. Aby uzyskać więcej informacji, zobacz Obsługa ról [serwera Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Jeśli używasz narzędzia Sysprep przed przekazaniem dysku VHD na platformę Azure po raz pierwszy, przed uruchomieniem narzędzia Sysprep upewnij się, że [maszyna wirtualna została przygotowana.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
2. Otwórz okno wiersza polecenia jako administrator. Zmień katalog na **%windir%\system32\sysprep**, a następnie uruchom plik `sysprep.exe`.
3. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.
4. W **obszarze Opcje zamykania**wybierz pozycję **Zamknięcie**.
5. Kliknij przycisk **OK**.
   
    ![Uruchamianie sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Gdy narzędzie Sysprep zakończy działanie, maszyna wirtualna zostanie wyłączona. 

> [!IMPORTANT]
> Nie należy ponownie uruchamiać maszyny Wirtualnej, dopóki nie zostanie wykonane przekazywanie dysku twardego na platformę Azure lub tworzenie obrazu z maszyny Wirtualnej. Jeśli maszyna wirtualna przypadkowo zostanie ponownie uruchomiona, uruchom program Sysprep, aby uogólnić ją ponownie.
> 
> 


## <a name="upload-the-vhd"></a>Prześlij dysk VHD

Przekaż dysk VHD na konto magazynu platformy Azure.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Jeśli nie masz jeszcze zainstalowanej wersji 1.4 lub wyższej programu PowerShell, przeczytaj [artykuł Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

1. Otwórz program Azure PowerShell i zaloguj się na swoje konto platformy Azure. Zostanie otwarte okno podręczne, w które można wprowadzić poświadczenia konta platformy Azure.
   
    ```powershell
    Connect-AzAccount
    ```
2. Pobierz identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ustaw poprawną subskrypcję przy użyciu identyfikatora subskrypcji. Zamień `<subscriptionID>` na identyfikator poprawnej subskrypcji.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Pobierz konto magazynu
Do przechowywania przekazanego obrazu maszyny Wirtualnej potrzebne jest konto magazynu na platformie Azure. Można użyć istniejącego konta magazynu lub utworzyć nowe. 

Aby wyświetlić dostępne konta magazynu, wpisz:

```powershell
Get-AzStorageAccount
```

Jeśli chcesz użyć istniejącego konta magazynu, przejdź do sekcji Przekaż obraz maszyny Wirtualnej.

Jeśli chcesz utworzyć konto magazynu, wykonaj następujące czynności:

1. Potrzebna jest nazwa grupy zasobów, w której należy utworzyć konto magazynu. Aby dowiedzieć się, że wszystkie grupy zasobów znajdują się w subskrypcji, wpisz:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Aby utworzyć grupę zasobów o nazwie **myResourceGroup** w regionie **Zachodnie stany USA,** należy wpisać:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Utwórz konto magazynu o nazwie **mystorageaccount** w tej grupie zasobów przy użyciu polecenia cmdlet [New-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Rozpoczynanie przesyłania 

Użyj polecenia cmdlet [Add-AzVhd,](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) aby przesłać obraz do kontenera na koncie magazynu. W tym przykładzie plik **myVHD.vhd** z `"C:\Users\Public\Documents\Virtual hard disks\"` konta magazynu o nazwie **mystorageaccount** w grupie zasobów **myResourceGroup.** Plik zostanie umieszczony w kontenerze o nazwie **mycontainer,** a nowa nazwa pliku będzie **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Jeśli się powiedzie, otrzymasz odpowiedź, która wygląda podobnie do tego:

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

W zależności od połączenia sieciowego i rozmiaru pliku VHD to polecenie może trochę potrwać.


## <a name="create-a-new-vm"></a>Utworzenie nowej maszyny wirtualnej. 

Teraz możesz użyć przekazanego dysku VHD, aby utworzyć nową maszynę wirtualną. 

### <a name="set-the-uri-of-the-vhd"></a>Ustawianie identyfikatora URI dysku VHD

Identyfikator URI dla VHD do użycia jest w formacie: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. W tym przykładzie VHD o nazwie **myVHD** znajduje się na koncie pamięci masowej **mystorageaccount** w kontenerze **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
Utwórz sieć wirtualną i podsieć [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Utwórz podsieć. Poniższy przykład tworzy podsieć o nazwie **mySubnet** w grupie zasobów **myResourceGroup** z prefiksem adresu **10.0.0.0/24**.  
   
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

1. Utwórz publiczny adres IP. W tym przykładzie utworzono publiczny adres IP o nazwie **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Utwórz kartę sieciową. W tym przykładzie utworzy się kartę sieciową o nazwie **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły RDP
Aby móc zalogować się do maszyny Wirtualnej przy użyciu protokołu RDP, musisz mieć regułę zabezpieczeń, która umożliwia dostęp do protokołu RDP na porcie 3389. 

W tym przykładzie tworzy nsg o nazwie **myNsg,** który zawiera regułę o nazwie **myRdpRule,** która umożliwia ruch RDP przez port 3389. Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Otwieranie portów na maszynę wirtualną na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Tworzenie zmiennej dla sieci wirtualnej
Utwórz zmienną dla ukończonej sieci wirtualnej. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej
W poniższym skrypcie programu PowerShell pokazano, jak skonfigurować konfiguracje maszyny wirtualnej i użyć przekazanego obrazu maszyny wirtualnej jako źródła nowej instalacji.



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
Po zakończeniu powinna zostać wyświetlona nowo utworzona maszyna wirtualna w [portalu Azure](https://portal.azure.com) w obszarze Przeglądaj maszyny **wirtualne** > **Virtual machines**lub przy użyciu następujących poleceń programu PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Aby zarządzać nową maszyną wirtualną za pomocą programu Azure PowerShell, zobacz [Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i powershell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


