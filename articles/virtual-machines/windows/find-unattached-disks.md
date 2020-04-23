---
title: Znajdowanie i usuwanie nieprzyłączonych dysków zarządzanych i niezarządzanych platformy Azure
description: Jak znaleźć i usunąć dyski zarządzane i niezarządzane platformy Azure (VHDs/page blobs) przy użyciu programu Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cac192186c91259a5573dc27442137729816991a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869610"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Znajdowanie i usuwanie nieprzyłączonych dysków zarządzanych i niezarządzanych platformy Azure

Po usunięciu maszyny wirtualnej (VM) na platformie Azure, domyślnie wszystkie dyski, które są dołączone do maszyny wirtualnej nie są usuwane. Ta funkcja pomaga zapobiec utracie danych z powodu niezamierzonego usunięcia maszyn wirtualnych. Po usunięciu maszyny Wirtualnej nadal będziesz płacić za dyski niezłączone. W tym artykule pokazano, jak znaleźć i usunąć wszystkie niezałączone dyski i zmniejszyć niepotrzebne koszty.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: znajdowanie i usuwanie niezałączonych dysków

Poniższy skrypt wyszukuje nieprzyłączone [dyski zarządzane,](managed-disks-overview.md) sprawdzając wartość właściwości **ManagedBy.** Gdy dysk zarządzany jest dołączony do maszyny Wirtualnej, **Właściwość ManagedBy** zawiera identyfikator zasobu maszyny Wirtualnej. Gdy dysk zarządzany jest nieprzyłączony, **ManagedBy** właściwość ma wartość null. Skrypt sprawdza wszystkie dyski zarządzane w subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk zarządzany z **managedby** właściwość ustawiona na wartość null, skrypt określa, że dysk jest nieprzyłączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedDisks** na 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niezałączonych dysków zarządzanych.
>
>Po przejrzeniu wszystkich dysków nieprzyłączonych uruchom skrypt ponownie i ustaw zmienną **deleteUnattachedDisks** na 1. Ta akcja umożliwia usunięcie wszystkich niezałączonych dysków zarządzanych.

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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dyski niezarządzane: znajdowanie i usuwanie dysków niepodłączonych

Dyski niezarządzane to pliki VHD, które są przechowywane jako [obiekty blob stron](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) na [kontach magazynu platformy Azure.](../../storage/common/storage-create-storage-account.md) Poniższy skrypt wyszukuje niezarządzane dyski niezarządzane (stronicowe obiekty blob) przez sprawdzenie wartości właściwości **LeaseStatus.** Gdy dysk niezarządzany jest dołączony do maszyny Wirtualnej, właściwość **LeaseStatus** jest ustawiona na **Zablokowane**. Gdy dysk niezarządzany nie jest przyłączony, właściwość **LeaseStatus** jest ustawiona na **Odblokowany**. Skrypt sprawdza wszystkie dyski niezarządzane na wszystkich kontach magazynu platformy Azure w ramach subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk niezarządzany z właściwością **LeaseStatus** ustawioną na **Odblokowany,** skrypt określa, że dysk jest niezłączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedVHDs** na 0. Ta akcja umożliwia znajdowanie i wyświetlanie wszystkich niezałączonych niezarządzanych wirtualnych identyfikatorów wirtualnych.
>
>Po przejrzeniu wszystkich niezałączonych dysków uruchom ponownie skrypt i ustaw zmienną **deleteUnattachedVHDs** na 1. Ta akcja umożliwia usunięcie wszystkich niezałączonych niezarządzanych wirtualnych identyfikatorów wirtualnych.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
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
                    if($deleteUnattachedVHDs -eq 1){
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

Aby uzyskać więcej informacji, zobacz [Usuwanie konta magazynu](../../storage/common/storage-create-storage-account.md) i [identyfikowanie osieroconych dysków przy użyciu programu PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
