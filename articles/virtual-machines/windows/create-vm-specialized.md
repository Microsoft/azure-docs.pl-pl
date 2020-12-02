---
title: Tworzenie maszyny wirtualnej z systemem Windows na podstawie wyspecjalizowanego wirtualnego dysku twardego na platformie Azure
description: Utwórz nową maszynę wirtualną z systemem Windows przez dołączenie wyspecjalizowanego dysku zarządzanego jako dysku systemu operacyjnego przy użyciu modelu wdrażania Menedżer zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: cddc7f4f453f22b0cb36b1d3a1e9c2fba2dcabaf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455094"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Tworzenie maszyny wirtualnej z systemem Windows na podstawie wyspecjalizowanego dysku za pomocą programu PowerShell

Utwórz nową maszynę wirtualną przez dołączenie wyspecjalizowanego dysku zarządzanego jako dysku systemu operacyjnego. Wyspecjalizowany dysk to kopia wirtualnego dysku twardego (VHD) z istniejącej maszyny wirtualnej, która zawiera konta użytkowników, aplikacje i inne dane stanu z oryginalnej maszyny wirtualnej. 

Istnieje kilka rozwiązań:
* [Użyj istniejącego dysku zarządzanego](#option-1-use-an-existing-disk). Ta opcja jest przydatna, jeśli masz maszynę wirtualną, która nie działa prawidłowo. Możesz usunąć maszynę wirtualną, a następnie ponownie użyć dysku zarządzanego, aby utworzyć nową maszynę wirtualną. 
* [Przekazywanie wirtualnego dysku twardego](#option-2-upload-a-specialized-vhd) 
* [Kopiowanie istniejącej maszyny wirtualnej platformy Azure przy użyciu migawek](#option-3-copy-an-existing-azure-vm)

Możesz również użyć Azure Portal, aby [utworzyć nową maszynę wirtualną z wyspecjalizowanego wirtualnego dysku twardego](create-vm-specialized-portal.md).

W tym artykule pokazano, jak używać dysków zarządzanych. Jeśli masz starsze wdrożenie wymagające użycia konta magazynu, zobacz [Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego wirtualnego dysku twardego na koncie magazynu](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized).

> [!IMPORTANT]
> 
> W przypadku tworzenia nowej maszyny wirtualnej przy użyciu wyspecjalizowanego dysku Nowa maszyna wirtualna zachowuje nazwę komputera oryginalnej maszyny wirtualnej. Inne informacje specyficzne dla komputera (np. Identyfikator CMID) również są przechowywane i w niektórych przypadkach te zduplikowane informacje mogą powodować problemy. Podczas kopiowania maszyny wirtualnej należy wiedzieć o typach informacji specyficznych dla komputera, na których zależą Twoje aplikacje.  
> W tym celu nie należy używać wyspecjalizowanego dysku, jeśli chcesz utworzyć wiele maszyn wirtualnych. Zamiast tego, w przypadku większych wdrożeń [Utwórz obraz](capture-image-resource.md) , a następnie [Użyj tego obrazu do utworzenia wielu maszyn wirtualnych](create-vm-generalized-managed.md).

Zalecamy ograniczenie liczby współbieżnych wdrożeń do 20 maszyn wirtualnych z jednego wirtualnego dysku twardego lub migawki. 

## <a name="option-1-use-an-existing-disk"></a>Opcja 1: Użyj istniejącego dysku

Jeśli masz usuniętą maszynę wirtualną i chcesz ponownie użyć dysku systemu operacyjnego, aby utworzyć nową maszynę wirtualną, użyj polecenie [Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Teraz możesz dołączyć ten dysk jako dysk systemu operacyjnego do [nowej maszyny wirtualnej](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opcja 2: przekazywanie wyspecjalizowanego wirtualnego dysku twardego

Wirtualny dysk twardy można przekazać z wyspecjalizowanej maszyny wirtualnej utworzonej przy użyciu lokalnego narzędzia do wirtualizacji, takiego jak funkcja Hyper-V, lub maszyny wirtualnej wyeksportowanej z innej chmury.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Aby utworzyć nową maszynę wirtualną, użyj wirtualnego dysku twardego. 
  
  * [Przygotuj wirtualny dysk twardy systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie należy** UOGÓLNIAĆ maszyny wirtualnej przy użyciu programu Sysprep.
  * Usuń wszystkie narzędzia i agenci wirtualizacji gościa, które są zainstalowane na maszynie wirtualnej (na przykład narzędzia VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana do pobierania adresu IP i ustawień DNS z protokołu DHCP. Dzięki temu serwer uzyskuje adres IP w ramach sieci wirtualnej podczas uruchamiania. 


### <a name="upload-the-vhd"></a>Przekazywanie wirtualnego dysku twardego

Możesz teraz przekazać dysk VHD bezpośrednio do dysku zarządzanego. Aby uzyskać instrukcje, zobacz [przekazywanie wirtualnego dysku twardego do platformy Azure przy użyciu Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Opcja 3: kopiowanie istniejącej maszyny wirtualnej platformy Azure

Można utworzyć kopię maszyny wirtualnej korzystającej z dysków zarządzanych, pobierając migawkę maszyny wirtualnej, a następnie używając tej migawki do utworzenia nowego dysku zarządzanego i nowej maszyny wirtualnej.

Jeśli chcesz skopiować istniejącą maszynę wirtualną do innego regionu, możesz użyć AzCopy, aby [utworzyć kopię dysku w innym regionie](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Utwórz migawkę dysku systemu operacyjnego

Można wykonać migawkę całej maszyny wirtualnej (w tym wszystkich dysków) lub tylko jednego dysku. Poniższe kroki pokazują, jak wykonać migawkę tylko dysku systemu operacyjnego maszyny wirtualnej za pomocą polecenia cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) . 

Najpierw ustaw niektóre parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Pobierz obiekt maszyny wirtualnej.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Pobierz nazwę dysku systemu operacyjnego.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Utwórz konfigurację migawki. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Zrób migawkę.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Aby użyć tej migawki do utworzenia maszyny wirtualnej wymagającej wysokiej wydajności, Dodaj parametr `-AccountType Premium_LRS` do polecenia New-AzSnapshotConfig. Ten parametr tworzy migawkę, tak aby była przechowywana jako dysk zarządzany w warstwie Premium. Managed Disks w warstwie Premium są droższe niż w warstwie Standardowa, dlatego przed użyciem tego parametru należy pamiętać o wersji Premium.

### <a name="create-a-new-disk-from-the-snapshot"></a>Tworzenie nowego dysku na podstawie migawki

Utwórz dysk zarządzany na podstawie migawki przy użyciu polecenia [New-AzDisk](/powershell/module/az.compute/new-azdisk). W tym przykładzie w nazwie dysku są stosowane *myOSDisk* .

Utwórz nową grupę zasobów dla nowej maszyny wirtualnej.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Ustaw nazwę dysku systemu operacyjnego. 

```powershell
$osDiskName = 'myOsDisk'
```

Utwórz dysk zarządzany.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Utwórz nową maszynę wirtualną 

Tworzenie sieci i innych zasobów maszyn wirtualnych, które mają być używane przez nową maszynę wirtualną.

### <a name="create-the-subnet-and-virtual-network"></a>Tworzenie podsieci i sieci wirtualnej

Utwórz [sieć wirtualną](../../virtual-network/virtual-networks-overview.md) i podsieć dla maszyny wirtualnej.

1. Utwórz podsieć. Ten przykład tworzy podsieć o nazwie Moja *podsieć*, w grupie zasobów *myDestinationResourceGroup* i ustawia prefiks adresu podsieci na *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Utwórz sieć wirtualną. Ten przykład ustawia nazwę sieci wirtualnej na *myVnetName*, lokalizację na *zachodnie stany USA* oraz prefiks adresu sieci wirtualnej na *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły RDP
Aby można było zalogować się do maszyny wirtualnej za pomocą protokołu RDP (Remote Desktop Protocol), musisz mieć regułę zabezpieczeń, która zezwala na dostęp RDP na porcie 3389. W naszym przykładzie wirtualny dysk twardy nowej maszyny wirtualnej został utworzony na podstawie istniejącej wyspecjalizowanej maszyny wirtualnej, dlatego można użyć konta, które istniało na źródłowej maszynie wirtualnej dla protokołu RDP.

Ten przykład ustawia nazwę sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) na *myNsg* i nazwę reguły RDP na *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Aby uzyskać więcej informacji na temat punktów końcowych i reguł sieciowej grupy zabezpieczeń, zobacz [otwieranie portów do maszyny wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty sieciowej
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebny jest [publiczny adres IP](../../virtual-network/public-ip-addresses.md) i interfejs sieciowy.

1. Utwórz publiczny adres IP. W tym przykładzie nazwa publicznego adresu IP jest ustawiona na *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Utwórz kartę sieciową. W tym przykładzie nazwa karty sieciowej jest ustawiona na *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Ustaw nazwę i rozmiar maszyny wirtualnej

Ten przykład służy do ustawiania nazwy maszyny wirtualnej *myVM* i rozmiaru maszyny wirtualnej do *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodaj kartę sieciową
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Dodawanie dysku systemu operacyjnego 

Dodaj dysk systemu operacyjnego do konfiguracji za pomocą polecenia [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk). Ten przykład ustawia rozmiar dysku na *128 GB* i dołącza dysk zarządzany jako dysk *systemu operacyjnego Windows* .
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Ukończ maszynę wirtualną 

Utwórz maszynę wirtualną przy użyciu polecenia [New-AzVM](/powershell/module/az.compute/new-azvm) z utworzonymi właśnie konfiguracjami.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Jeśli to polecenie zakończy się pomyślnie, zobaczysz dane wyjściowe podobne do tego:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Nowo utworzona maszyna wirtualna powinna zostać wyświetlona w [Azure Portal](https://portal.azure.com) w obszarze **przeglądanie**  >  **maszyn wirtualnych** lub przy użyciu następujących poleceń programu PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [jak nawiązać połączenie i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md).