---
title: Dołączanie dysku danych do maszyny wirtualnej z systemem Windows na platformie Azure przy użyciu programu PowerShell
description: Jak dołączyć nowy lub istniejący dysk danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell z modelem wdrażania Menedżer zasobów.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: da4f9fd9fd4305029f42dbe63326c8782d22d907
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87825450"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Dołączanie dysku danych do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell

W tym artykule opisano sposób dołączania nowych i istniejących dysków do maszyny wirtualnej z systemem Windows przy użyciu programu PowerShell. 

Najpierw zapoznaj się z następującymi wskazówkami:

* Rozmiar maszyny wirtualnej kontroluje liczbę dysków z danymi, które można dołączyć. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aby korzystać z dysków SSD Premium, musisz mieć [Typ maszyny wirtualnej z obsługą magazynu w warstwie Premium](../sizes-memory.md), np. maszynę wirtualną z serii DS lub GS.

W tym artykule jest używany program PowerShell w [Azure Cloud Shell](../../cloud-shell/overview.md), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Dodawanie pustego dysku danych do maszyny wirtualnej

Ten przykład pokazuje, jak dodać pusty dysk danych do istniejącej maszyny wirtualnej.

### <a name="using-managed-disks"></a>Korzystanie z usługi Managed disks

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Używanie dysków zarządzanych w strefie dostępności

Aby utworzyć dysk w strefie dostępności, użyj polecenie [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) z `-Zone` parametrem. Poniższy przykład tworzy dysk w strefie *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Inicjowanie dysku

Po dodaniu pustego dysku musisz go zainicjować. Aby zainicjować dysk, możesz zalogować się do maszyny wirtualnej i użyć przystawki Zarządzanie dyskami. Jeśli włączono usługę [WinRM](/windows/desktop/winrm/portal) i certyfikat na maszynie wirtualnej podczas jej tworzenia, możesz użyć zdalnego programu PowerShell, aby zainicjować dysk. Można również użyć niestandardowego rozszerzenia skryptu:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Plik skryptu może zawierać kod umożliwiający zainicjowanie dysków, na przykład:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Dołączanie istniejącego dysku z danymi do maszyny wirtualnej

Istniejący dysk zarządzany można dołączyć do maszyny wirtualnej jako dysk danych.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Następne kroki

Dyski zarządzane można także wdrażać przy użyciu szablonów. Aby uzyskać więcej informacji, zobacz [używanie Managed disks w szablonach Azure Resource Manager](using-managed-disks-template-deployments.md) lub [szablon szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) do wdrażania wielu dysków z danymi.