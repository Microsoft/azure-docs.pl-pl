---
title: Tworzenie niezarządzanego obrazu uogólnionej maszyny Wirtualnej na platformie Azure
description: Utwórz niezarządzany obraz uogólnionej maszyny Wirtualnej systemu Windows, która będzie używana do tworzenia wielu kopii maszyny wirtualnej na platformie Azure.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 1f44f30666ead84b2bd2fc63d8a8eb624f70c85c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458083"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Jak utworzyć niezarządzany obraz maszyny Wirtualnej z maszyny Wirtualnej platformy Azure

W tym artykule omówiono przy użyciu kont magazynu. Zaleca się używanie dysków zarządzanych i obrazów zarządzanych zamiast konta magazynu. Aby uzyskać więcej informacji, zobacz [Przechwytywanie zarządzanego obrazu uogólnionej maszyny Wirtualnej na platformie Azure.](capture-image-resource.md)

W tym artykule pokazano, jak używać programu Azure PowerShell do tworzenia obrazu uogólnionej maszyny Wirtualnej platformy Azure przy użyciu konta magazynu. Następnie można użyć obrazu do utworzenia innej maszyny Wirtualnej. Obraz zawiera dysk systemu operacyjnego i dyski danych, które są dołączone do maszyny wirtualnej. Obraz nie zawiera zasobów sieci wirtualnej, więc należy skonfigurować te zasoby podczas tworzenia nowej maszyny Wirtualnej. 

 

## <a name="generalize-the-vm"></a>Uogólnianie maszyny Wirtualnej 
W tej sekcji pokazano, jak uogólniać maszynę wirtualną systemu Windows do użycia jako obraz. Generalizacja maszyny Wirtualnej powoduje między innymi usunięcie wszystkich informacji o koncie osobistym i przygotowanie urządzenia do użycia jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez sysprep. Aby uzyskać więcej informacji, zobacz Obsługa ról [serwera Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Jeśli przekazujesz dysk VHD na platformę Azure po raz pierwszy, przed uruchomieniem narzędzia Sysprep upewnij się, że [maszyna wirtualna została przygotowana.](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
> 
> 

Można również uogólnić `sudo waagent -deprovision+user` maszynę wirtualną z systemem Linux przy użyciu, a następnie użyć programu PowerShell do przechwytywania maszyny Wirtualnej. Aby uzyskać informacje dotyczące używania interfejsu wiersza polecenia do przechwytywania maszyny wirtualnej, zobacz [Jak uogólniać i przechwytywać maszynę wirtualną systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Logowanie do programu Azure PowerShell
1. Otwórz program Azure PowerShell i zaloguj się na swoje konto platformy Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Zostanie otwarte okno podręczne, w które można wprowadzić poświadczenia konta platformy Azure.
2. Pobierz identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ustaw poprawną subskrypcję przy użyciu identyfikatora subskrypcji.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Zdemionetuj maszynę wirtualną i ustaw stan na uogólniony

> [!IMPORTANT] 
> Nie można dodawać, edytować ani usuwać znaczników z maszyny Wirtualnej, gdy zostanie oznaczona jako uogólniona. Jeśli chcesz dodać znacznik do maszyny Wirtualnej, upewnij się, że tagi zostały dodane przed oznaczeniem go jako uogólnione.
> 

1. Zdeokuj zasoby maszyny Wirtualnej.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stan* maszyny Wirtualnej w witrynie Azure portal zmienia się z **Zatrzymane** na **zatrzymane (cofnięte)**.
2. Ustaw stan maszyny wirtualnej na **Uogólniony**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Sprawdź stan maszyny Wirtualnej. Sekcja **OSState/uogólniona** dla maszyny Wirtualnej powinna mieć status **displayu** ustawiony na **maszynę wirtualną uogólnioną**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Tworzenie obrazu

Utwórz niezarządzany obraz maszyny wirtualnej w kontenerze magazynu docelowego za pomocą tego polecenia. Obraz jest tworzony na tym samym koncie magazynu co oryginalna maszyna wirtualna. Parametr `-Path` zapisuje kopię szablonu JSON dla źródłowej maszyny Wirtualnej na komputerze lokalnym. Parametr `-DestinationContainerName` jest nazwą kontenera, który ma być przechowywany obrazów. Jeśli kontener nie istnieje, jest tworzony dla Ciebie.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Adres URL obrazu można uzyskać z szablonu pliku JSON. Przejdź do magazynu > **zasobówProfile** > **osDisk** > **obraz** > **uri** sekcji dla pełnej ścieżki obrazu. **resources** Adres URL obrazu wygląda `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`następująco: .
   
Można również zweryfikować identyfikator URI w portalu. Obraz jest kopiowany do kontenera o nazwie **system** na koncie magazynu. 

## <a name="create-a-vm-from-the-image"></a>Utwórz maszynę wirtualną na podstawie obrazu

Teraz można utworzyć jedną lub więcej maszyn wirtualnych z obrazu niezarządzanego.

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
    $location = "West US"
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
Poniższy program PowerShell kończy konfiguracje maszyn wirtualnych i używa obrazu niezarządzanego jako źródła nowej instalacji.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Po zakończeniu powinna zostać wyświetlona nowo utworzona maszyna wirtualna w [portalu Azure](https://portal.azure.com) w obszarze Przeglądaj maszyny **wirtualne** > **Virtual machines**lub przy użyciu następujących poleceń programu PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Aby zarządzać nową maszyną wirtualną za pomocą programu Azure PowerShell, zobacz [Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i powershell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


