---
title: Tworzenie maszyny Wirtualnej systemu Windows na podstawie wyspecjalizowanej sieci VHD na platformie Azure
description: Utwórz nową maszynę wirtualną systemu Windows, dołączając wyspecjalizowany dysk zarządzany jako dysk systemu operacyjnego przy użyciu modelu wdrażania Menedżera zasobów.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 2939726898abc2abc0e62d0e36feedbfe7ba3645
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086406"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Tworzenie maszyny wirtualnej z systemem Windows na podstawie wyspecjalizowanego dysku za pomocą programu PowerShell

Utwórz nową maszynę wirtualną, dołączając wyspecjalizowany dysk zarządzany jako dysk systemu operacyjnego. Dysk specjalistyczny jest kopią wirtualnego dysku twardego (VHD) z istniejącej maszyny Wirtualnej, która zawiera konta użytkowników, aplikacje i inne dane o stanie z oryginalnej maszyny Wirtualnej. 

Podczas tworzenia nowej maszyny Wirtualnej przy użyciu wyspecjalizowanego dysku twardego nowa maszyna wirtualna zachowuje nazwę komputera oryginalnej maszyny Wirtualnej. Przechowywane są również inne informacje specyficzne dla komputera, a w niektórych przypadkach te zduplikowane informacje mogą powodować problemy. Podczas kopiowania maszyny Wirtualnej należy pamiętać o typach informacji specyficznych dla komputera, na którym opierają się aplikacje.

Istnieje kilka rozwiązań:
* [Użyj istniejącego dysku zarządzanego](#option-1-use-an-existing-disk). Ta opcja jest przydatna, jeśli masz maszynę wirtualną, która nie działa poprawnie. Można usunąć maszynę wirtualną, a następnie ponownie użyć dysku zarządzanego, aby utworzyć nową maszynę wirtualną. 
* [Przekazywanie wirtualnego dysku twardego](#option-2-upload-a-specialized-vhd) 
* [Kopiowanie istniejącej maszyny wirtualnej platformy Azure przy użyciu migawek](#option-3-copy-an-existing-azure-vm)

Za pomocą portalu Azure można również [utworzyć nową maszynę wirtualną ze specjalistycznego dysku VHD.](create-vm-specialized-portal.md)

W tym artykule pokazano, jak używać dysków zarządzanych. Jeśli masz starsze wdrożenie, które wymaga użycia konta magazynu, zobacz [Tworzenie maszyny Wirtualnej ze specjalistycznej maszyny Wirtualnej na koncie magazynu](sa-create-vm-specialized.md).

Zaleca się ograniczenie liczby równoczesnych wdrożeń do 20 maszyn wirtualnych z jednego dysku wirtualnego lub migawki. 

## <a name="option-1-use-an-existing-disk"></a>Opcja 1: Użyj istniejącego dysku

Jeśli usunięto maszynę wirtualną i chcesz ponownie użyć dysku systemu operacyjnego do utworzenia nowej maszyny Wirtualnej, użyj programu [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Teraz można dołączyć ten dysk jako dysk systemu operacyjnego do [nowej maszyny Wirtualnej](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opcja 2: Prześlij wyspecjalizowany VHD

Dysk VHD można przekazać ze specjalistycznej maszyny Wirtualnej utworzonej za pomocą lokalnego narzędzia do wirtualizacji, takiego jak funkcja Hyper-V, lub maszyny wirtualnej eksportowanej z innej chmury.

### <a name="prepare-the-vm"></a>Przygotowywanie maszyny wirtualnej
Użyj dysku VHD w stanie as-is, aby utworzyć nową maszynę wirtualną. 
  
  * [Przygotuj dysk VHD systemu Windows do przekazania na platformę Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Nie** uogólniaj maszyny Wirtualnej przy użyciu sysprep.
  * Usuń wszystkie narzędzia wirtualizacji gości i agentów zainstalowanych na maszynie wirtualnej (takich jak narzędzia VMware).
  * Upewnij się, że maszyna wirtualna jest skonfigurowana do uzyskania adresu IP i ustawień DNS z usługi DHCP. Gwarantuje to, że serwer uzyskuje adres IP w sieci wirtualnej podczas uruchamiania. 


### <a name="upload-the-vhd"></a>Prześlij dysk VHD

Teraz możesz przesłać dysk VHD bezpośrednio na dysk zarządzany. Aby uzyskać instrukcje, zobacz [Przekazywanie dysku VHD na platformę Azure przy użyciu programu Azure PowerShell.](disks-upload-vhd-to-managed-disk-powershell.md)

## <a name="option-3-copy-an-existing-azure-vm"></a>Opcja 3: Kopiowanie istniejącej maszyny Wirtualnej platformy Azure

Można utworzyć kopię maszyny Wirtualnej, która używa dysków zarządzanych, wykonując migawkę maszyny Wirtualnej, a następnie używając tej migawki do utworzenia nowego dysku zarządzanego i nowej maszyny Wirtualnej.

Jeśli chcesz skopiować istniejącą maszynę wirtualną do innego regionu, możesz użyć azcopy do [utworzenia kopii dysku w innym regionie](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Tworzenie migawki dysku systemu operacyjnego

Można zrobić migawkę całej maszyny Wirtualnej (w tym wszystkich dysków) lub tylko jednego dysku. Poniższe kroki pokazują, jak wykonać migawkę tylko dysku systemu operacyjnego maszyny Wirtualnej z polecenia cmdlet [New-AzSnapshot.](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) 

Najpierw ustaw niektóre parametry. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Pobierz obiekt maszyny Wirtualnej.

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


Aby użyć tej migawki, aby utworzyć maszynę wirtualną, która musi być wydajna, dodaj parametr `-AccountType Premium_LRS` do polecenia New-AzSnapshotConfig. Ten parametr tworzy migawkę, dzięki czemu jest przechowywany jako dysk zarządzany w wersji Premium. Dyski zarządzane w wersji Premium są droższe niż standardowe, więc upewnij się, że będziesz potrzebować usługi Premium przed użyciem tego parametru.

### <a name="create-a-new-disk-from-the-snapshot"></a>Tworzenie nowego dysku z migawki

Utwórz dysk zarządzany z migawki przy użyciu [programu New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk). W tym przykładzie użyto *programu myOSDisk* dla nazwy dysku.

Utwórz nową grupę zasobów dla nowej maszyny Wirtualnej.

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


## <a name="create-the-new-vm"></a>Tworzenie nowej maszyny Wirtualnej 

Tworzenie sieci i innych zasobów maszyn wirtualnych, które mają być używane przez nową maszynę wirtualną.

### <a name="create-the-subnet-and-virtual-network"></a>Tworzenie podsieci i sieci wirtualnej

Utwórz [sieć wirtualną](../../virtual-network/virtual-networks-overview.md) i podsieć dla maszyny Wirtualnej.

1. Utwórz podsieć. W tym przykładzie utworzysz podsieć o nazwie *mySubNet*w grupie zasobów *myDestinationResourceGroup*i ustawia prefiks adresu podsieci na *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Utwórz sieć wirtualną. W tym przykładzie ustawia nazwę sieci wirtualnej na *myVnetName*, lokalizację na *zachodnie stany USA,* a prefiks adresu sieci wirtualnej na *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Tworzenie sieciowej grupy zabezpieczeń i reguły RDP
Aby móc zalogować się do maszyny Wirtualnej za pomocą protokołu RDP (Remote desktop Protocol), musisz mieć regułę zabezpieczeń umożliwiającą dostęp do protokołu RDP na porcie 3389. W naszym przykładzie dysk wirtualny dla nowej maszyny Wirtualnej został utworzony na podstawie istniejącej wyspecjalizowanej maszyny Wirtualnej, dzięki czemu można użyć konta, które istniało na źródłowej maszynie wirtualnej dla protokołu RDP.

W tym przykładzie ustawia nazwę sieciowej grupy zabezpieczeń (NSG) na *myNsg,* a nazwę reguły RDP na *myRdpRule*.

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

Aby uzyskać więcej informacji na temat punktów końcowych i reguł sieciowej sieciowej, zobacz [Otwieranie portów na maszynie wirtualnej na platformie Azure przy użyciu programu PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Tworzenie publicznego adresu IP i karty sieciowej
Aby włączyć komunikację z maszyną wirtualną w sieci wirtualnej, potrzebny jest [publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejs sieciowy.

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
    


### <a name="set-the-vm-name-and-size"></a>Ustawianie nazwy i rozmiaru maszyny Wirtualnej

W tym przykładzie nazwa maszyny Wirtualnej jest ustawiana na *myVM,* a rozmiar maszyny Wirtualnej na *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Dodawanie karty sieciowej
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Dodawanie dysku systemu operacyjnego 

Dodaj dysk systemu operacyjnego do konfiguracji za pomocą [set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk). W tym przykładzie ustawia rozmiar dysku na *128 GB* i dołącza dysk zarządzany jako dysk systemu *operacyjnego Windows.*
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Ukończ maszynę wirtualną 

Utwórz maszynę wirtualną przy użyciu [new-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) z konfiguracjami, które właśnie utworzyliśmy.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Jeśli to polecenie zakończy się pomyślnie, zobaczysz dane wyjściowe w ten sposób:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Sprawdź, czy maszyna wirtualna została utworzona
Nowo utworzona maszyna wirtualna powinna zostać wyświetlona w [portalu Azure](https://portal.azure.com) w obszarze Przeglądaj maszyny **wirtualne** > **Virtual machines**lub przy użyciu następujących poleceń programu PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Następne kroki
Zaloguj się do nowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Jak połączyć się i zalogować się do maszyny wirtualnej platformy Azure z systemem Windows](connect-logon.md).

