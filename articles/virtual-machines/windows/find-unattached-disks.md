---
title: Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych platformy Azure
description: Jak znaleźć i usunąć niedołączone dyski (VHD/stronicowe obiekty blob) platformy Azure przy użyciu Azure PowerShell.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 66a54ea74fcc6d8d354f5adbffe214c34b4c20d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554382"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych platformy Azure

Po usunięciu maszyny wirtualnej na platformie Azure domyślnie wszystkie dyski dołączone do maszyny wirtualnej nie zostaną usunięte. Ta funkcja pomaga zapobiec utracie danych z powodu niezamierzonego usunięcia maszyn wirtualnych. Po usunięciu maszyny wirtualnej opłaty za dyski niedołączone będą kontynuowane. W tym artykule opisano sposób znajdowania i usuwania dowolnych niedołączonych dysków i zmniejszania niepotrzebnych kosztów.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: Znajdź i Usuń niedołączone dyski

Następujący skrypt szuka niedołączonych [dysków zarządzanych](../managed-disks-overview.md) przez sprawdzenie wartości właściwości **zarządzane** . Po dołączeniu dysku zarządzanego do maszyny wirtualnej Właściwość **zarządzane** zawiera identyfikator zasobu maszyny wirtualnej. Po odłączeniu dysku zarządzanego Właściwość **zarządzane** ma wartość null. Skrypt bada wszystkie dyski zarządzane w ramach subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk zarządzany z właściwością **zarządzane** ustawioną na wartość null, skrypt określa, że dysk nie jest dołączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedDisks** na 0. Ta akcja umożliwia znalezienie i wyświetlenie wszystkich niedołączonych dysków zarządzanych.
>
>Po przejrzeniu wszystkich niedołączonych dysków ponownie uruchom skrypt i Ustaw zmienną **deleteUnattachedDisks** na 1. Ta akcja pozwala usunąć wszystkie niedołączone dyski zarządzane.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dyski niezarządzane: Znajdź i Usuń niedołączone dyski

Dyski niezarządzane to pliki VHD, które są przechowywane jako [stronicowe obiekty blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) na [kontach usługi Azure Storage](../../storage/common/storage-account-overview.md). Następujący skrypt szuka niedołączonych dysków niezarządzanych (stronicowych obiektów BLOB) przez sprawdzenie wartości właściwości **LeaseStatus** . Gdy dysk niezarządzany jest dołączony do maszyny wirtualnej, właściwość **LeaseStatus** jest ustawiona na **zablokowaną**. Gdy dysk niezarządzany jest niedołączony, właściwość **LeaseStatus** jest ustawiona na **odblokowany**. Skrypt bada wszystkie dyski niezarządzane na wszystkich kontach usługi Azure Storage w ramach subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk niezarządzany z właściwością **LeaseStatus** ustawioną na **odblokowane**, skrypt określa, że dysk nie jest dołączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedVHDs** na `$false` . Ta akcja umożliwia znalezienie i wyświetlenie wszystkich niedołączonych niepołączonych dysków VHD.
>
>Po przejrzeniu wszystkich niedołączonych dysków ponownie uruchom skrypt i Ustaw zmienną **deleteUnattachedVHDs** na `$true` . Ta akcja pozwala usunąć wszystkie niedołączone niezarządzane wirtualne dyski twarde.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                    }
                    else{
                        $_.ICloudBlob.Uri.AbsoluteUri
                    }
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [usuwanie konta magazynu](../../storage/common/storage-account-create.md#delete-a-storage-account) i [Identyfikowanie dysków oddzielonych przy użyciu programu PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell) .