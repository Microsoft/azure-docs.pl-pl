---
title: Zmiana zestawu dostępności maszyn wirtualnych
description: Dowiedz się, jak zmienić zestaw dostępności dla maszyny wirtualnej przy użyciu Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 54f59a052132826897cfbc8dda59bc73fb6ad8d9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200570"
---
# <a name="change-the-availability-set-for-a-vm"></a>Zmienianie zestawu dostępności dla maszyny wirtualnej
W poniższych krokach opisano, jak zmienić zestaw dostępności maszyny wirtualnej przy użyciu Azure PowerShell. Maszynę wirtualną można dodać do zestawu dostępności tylko podczas jego tworzenia. Aby zmienić zestaw dostępności, należy usunąć i ponownie utworzyć maszynę wirtualną. 

Ten artykuł dotyczy maszyn wirtualnych z systemami Linux i Windows.

Ten artykuł został ostatnio przetestowany w dniu 2/12/2019 przy użyciu [Azure Cloud Shell](https://shell.azure.com/powershell) i [AZ PowerShell module](/powershell/azure/install-az-ps) Version 1.2.0.

Ten przykład nie sprawdza, czy maszyna wirtualna jest dołączona do modułu równoważenia obciążenia. Jeśli maszyna wirtualna jest dołączona do modułu równoważenia obciążenia, należy zaktualizować skrypt w taki sposób, aby obsługiwał ten przypadek. 


## <a name="change-the-availability-set"></a>Zmień zestaw dostępności 

Poniższy skrypt zawiera przykład zbierania wymaganych informacji, usuwania oryginalnej maszyny wirtualnej, a następnie tworzenia jej ponownie w nowym zestawie dostępności.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Następne kroki

Dodaj dodatkowy magazyn do maszyny wirtualnej, dodając dodatkowy [dysk danych](attach-managed-disk-portal.md).
