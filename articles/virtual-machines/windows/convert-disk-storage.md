---
title: Konwertowanie magazynu dysków zarządzanych między różnymi typami dysków przy użyciu Azure PowerShell
description: Jak przekonwertować dyski zarządzane przez platformę Azure między różnymi typami dysków przy użyciu Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 1d1c191c746d6853f922302d74c6eefcba547f80
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519749"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizowanie typu magazynu na dysku zarządzanym

Istnieją cztery typy dysków zarządzanych przez platformę Azure: Azure Ultra disks, dysk SSD Premium, standardowy dysk SSD i standardowy dysk twardy. Możesz przełączać się między dyskiem SSD Premium, standardowym dyskiem SSD i standardowym dyskiem twardym w zależności od potrzeb dotyczących wydajności. Nie można już przełączyć z programu ani na dysk typu Ultra, należy wdrożyć nowy.

Ta funkcja nie jest obsługiwana w przypadku dysków niezarządzanych. Można jednak łatwo [skonwertować dysk niezarządzany na dysk zarządzany](convert-unmanaged-to-managed-disks.md) , aby można było przełączać się między typami dysków.



## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Ponieważ konwersja wymaga ponownego uruchomienia maszyny wirtualnej, należy zaplanować migrację magazynu dyskowego w ramach istniejącego wcześniej okna obsługi.
* Jeśli dysk jest niezarządzany, najpierw [przekonwertuj go na dysk zarządzany](convert-unmanaged-to-managed-disks.md) , aby można było przełączać się między opcjami magazynu.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Przełączanie wszystkich dysków zarządzanych z maszyny wirtualnej między różnymi kontami

Ten przykład pokazuje, jak przekonwertować wszystkie dyski maszyny wirtualnej do magazynu w warstwie Premium. Jednak zmieniając zmienną $storageType w tym przykładzie, można skonwertować typ dysków maszyny wirtualnej na standardowy dysk SSD lub standardowy dysk twardy. Aby można było korzystać z dysków zarządzanych w warstwie Premium, maszyna wirtualna musi używać [rozmiaru maszyny wirtualnej](../sizes.md) obsługującego usługę Premium Storage. Ten przykład umożliwia przełączenie na rozmiar obsługujący usługę Premium Storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSDD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Przełączanie poszczególnych dysków zarządzanych między warstwami standardowa i Premium

W przypadku obciążeń związanych z tworzeniem i testowaniem możesz potrzebować różnych dysków w warstwach Standardowa i Premium, aby zmniejszyć koszty. Można uaktualnić tylko te dyski, które potrzebują lepszej wydajności. Ten przykład pokazuje, jak przekonwertować pojedynczy dysk maszyny wirtualnej ze standardu do Premium Storage. Jednak zmieniając zmienną $storageType w tym przykładzie, można skonwertować typ dysków maszyny wirtualnej na standardowy dysk SSD lub standardowy dysk twardy. Aby można było korzystać z dysków zarządzanych w warstwie Premium, maszyna wirtualna musi używać [rozmiaru maszyny wirtualnej](../sizes.md) obsługującego usługę Premium Storage. Ten przykład pokazuje również, jak przełączyć się do rozmiaru, który obsługuje usługę Premium Storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Przełącz dyski zarządzane z jednego typu dysku na inny

Wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz MASZYNę wirtualną z listy **maszyn wirtualnych**.
3. Jeśli maszyna wirtualna nie jest zatrzymana, wybierz pozycję **Zatrzymaj** w górnej części okienka **Przegląd** maszyny wirtualnej i poczekaj na zatrzymanie maszyny wirtualnej.
4. W okienku dla maszyny wirtualnej wybierz pozycję **dyski** z menu.
5. Wybierz dysk, który chcesz skonwertować.
6. Z menu wybierz pozycję **Konfiguracja** .
7. Zmień **Typ konta** z oryginalnego typu dysku na żądany typ dysku.
8. Wybierz pozycję **Zapisz**, a następnie zamknij okienko dysk.

Konwersja typu dysku jest chwilowo. Po konwersji można uruchomić maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

Przekształć kopię maszyny wirtualnej w trybie tylko do odczytu przy użyciu [migawki](snapshot-copy-managed-disk.md).