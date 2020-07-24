---
title: Tworzenie niezarządzanego obrazu uogólnionej maszyny wirtualnej na platformie Azure
description: Tworzenie niezarządzanego obrazu uogólnionej maszyny wirtualnej z systemem Windows w celu utworzenia wielu kopii maszyny wirtualnej na platformie Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.custom: storage-accounts
ms.openlocfilehash: 8077689bce988124da34428842df8fd2ef757bf0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020189"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Jak utworzyć obraz niezarządzany maszyny wirtualnej przy użyciu maszyny wirtualnej platformy Azure

W tym artykule omówiono korzystanie z kont magazynu. Zalecamy używanie dysków zarządzanych i zarządzanych obrazów zamiast konta magazynu. Aby uzyskać więcej informacji, zobacz [przechwytywanie zarządzanego obrazu z uogólnionej maszyny wirtualnej na platformie Azure](capture-image-resource.md).

W tym artykule pokazano, jak za pomocą usługi Azure PowerShell utworzyć obraz uogólnionej maszyny wirtualnej platformy Azure przy użyciu konta magazynu. Następnie możesz użyć obrazu, aby utworzyć kolejną maszynę wirtualną. Obraz zawiera dysk systemu operacyjnego i dyski z danymi, które są dołączone do maszyny wirtualnej. Obraz nie obejmuje zasobów sieci wirtualnej, dlatego należy skonfigurować te zasoby podczas tworzenia nowej maszyny wirtualnej. 

 

## <a name="generalize-the-vm"></a>Uogólnianie maszyny wirtualnej 
W tej sekcji pokazano, jak uogólnić maszynę wirtualną z systemem Windows w celu wykorzystania jej jako obrazu. Uogólnianie maszyny wirtualnej eliminuje wszystkie informacje o koncie osobistym, między innymi i przygotowuje maszynę do użycia jako obraz. Aby uzyskać więcej informacji na temat narzędzia Sysprep, zobacz [Używanie narzędzia Sysprep: wprowadzenie](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Upewnij się, że role serwera uruchomione na komputerze są obsługiwane przez program Sysprep. Aby uzyskać więcej informacji, zobacz [Obsługa narzędzia Sysprep dla ról serwera](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> W przypadku przekazywania wirtualnego dysku twardego do platformy Azure po raz pierwszy upewnij się, że [maszyna wirtualna została przygotowana](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przed uruchomieniem narzędzia Sysprep. 
> 
> 

Możesz również uogólnić maszynę wirtualną z systemem Linux przy użyciu `sudo waagent -deprovision+user` programu, a następnie przechwycić maszynę wirtualną za pomocą środowiska PowerShell. Aby uzyskać informacje o używaniu interfejsu wiersza polecenia do przechwytywania maszyny wirtualnej, zobacz [jak uogólniać i przechwytywać maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Logowanie do programu Azure PowerShell
1. Otwórz Azure PowerShell i zaloguj się na koncie platformy Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Zostanie otwarte okno podręczne, w którym można wprowadzić poświadczenia konta platformy Azure.
2. Uzyskaj identyfikatory subskrypcji dla dostępnych subskrypcji.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ustaw poprawną subskrypcję przy użyciu identyfikatora subskrypcji.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Cofnij przydział maszyny wirtualnej i Ustaw stan na uogólniony

> [!IMPORTANT] 
> Nie można dodawać, edytować ani usuwać tagów z maszyny wirtualnej, gdy jest ona oznaczona jako uogólniona. Jeśli chcesz dodać tag do maszyny wirtualnej, pamiętaj, aby dodać znaczniki przed oznaczeniem go jako uogólnione.
> 

1. Cofnij przydział zasobów maszyny wirtualnej.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Stan* maszyny wirtualnej w Azure Portal zmiany z **zatrzymane** na **zatrzymane (cofnięto przydział)**.
2. Ustaw stan maszyny wirtualnej na **uogólnione**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Sprawdź stan maszyny wirtualnej. Sekcja **OSState/uogólniona** maszyny wirtualnej powinna mieć ustawioną **maszynę wirtualną** **DisplayStatus** .  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Tworzenie obrazu

Utwórz niezarządzany obraz maszyny wirtualnej w docelowym kontenerze magazynu za pomocą tego polecenia. Obraz jest tworzony na tym samym koncie magazynu co oryginalna maszyna wirtualna. `-Path`Parametr zapisuje kopię szablonu JSON dla źródłowej maszyny wirtualnej na komputerze lokalnym. `-DestinationContainerName`Parametr jest nazwą kontenera, w którym chcesz przechowywać obrazy. Jeśli kontener nie istnieje, zostanie on utworzony.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Możesz uzyskać adres URL obrazu z szablonu pliku JSON. Aby uzyskać pełną ścieżkę obrazu, przejdź do sekcji **resources**  >  **obszarze storageprofile**  >  **osDisk**  >  **Image**  >  **URI** . Adres URL obrazu wygląda następująco: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd` .
   
Możesz również zweryfikować identyfikator URI w portalu. Obraz jest kopiowany do kontenera o nazwie **system** na koncie magazynu. 

## <a name="create-a-vm-from-the-image"></a>Utwórz maszynę wirtualną na podstawie obrazu

Teraz można utworzyć co najmniej jedną maszynę wirtualną z obrazu niezarządzanego.

### <a name="set-the-uri-of-the-vhd"></a>Ustawianie identyfikatora URI wirtualnego dysku twardego

Identyfikator URI używanego wirtualnego dysku twardego ma format: https://**mojekontomagazynu****. blob.Core.Windows.NET/er** / **MyVhdName**. VHD. W tym przykładzie wirtualny dysk twardy o nazwie **myVHD** znajduje się na koncie magazynu **mojekontomagazynu** w **kontenerze**.

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
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Tworzenie publicznego adresu IP i interfejsu sieciowego
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../../virtual-network/public-ip-addresses.md) i interfejsu sieciowego.

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
Poniższy program PowerShell uzupełnia konfiguracje maszyn wirtualnych i używa obrazu niezarządzanego jako źródła nowej instalacji.

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
Po zakończeniu powinna zostać wyświetlona nowo utworzona maszyna wirtualna w [Azure Portal](https://portal.azure.com) w obszarze **przeglądanie**  >  **maszyn wirtualnych**lub przy użyciu następujących poleceń programu PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Aby zarządzać nową maszyną wirtualną za pomocą Azure PowerShell, zobacz [Zarządzanie maszynami wirtualnymi przy użyciu Azure Resource Manager i programu PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
