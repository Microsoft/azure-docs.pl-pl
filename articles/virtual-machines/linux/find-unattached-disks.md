---
title: Interfejs wiersza polecenia platformy Azure — Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych
description: Jak znaleźć i usunąć niedołączone dyski (VHD/stronicowe obiekty blob) platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0c3e8bb2ff6f3313e851a4253a95a5ad923a8f70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016220"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Znajdowanie i usuwanie niedołączonych dysków zarządzanych i niezarządzanych platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
Po usunięciu maszyny wirtualnej na platformie Azure domyślnie wszystkie dyski dołączone do maszyny wirtualnej nie zostaną usunięte. Ta funkcja pomaga zapobiec utracie danych z powodu niezamierzonego usunięcia maszyn wirtualnych. Po usunięciu maszyny wirtualnej opłaty za dyski niedołączone będą kontynuowane. W tym artykule opisano sposób znajdowania i usuwania dowolnych niedołączonych dysków i zmniejszania niepotrzebnych kosztów. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Dyski zarządzane: Znajdź i Usuń niedołączone dyski 

Następujący skrypt szuka niedołączonych [dysków zarządzanych](../managed-disks-overview.md) przez sprawdzenie wartości właściwości **zarządzane** . Po dołączeniu dysku zarządzanego do maszyny wirtualnej Właściwość **zarządzane** zawiera identyfikator zasobu maszyny wirtualnej. Po odłączeniu dysku zarządzanego Właściwość **zarządzane** ma wartość null. Skrypt bada wszystkie dyski zarządzane w ramach subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk zarządzany z właściwością **zarządzane** ustawioną na wartość null, skrypt określa, że dysk nie jest dołączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedDisks** na 0. Ta akcja umożliwia znalezienie i wyświetlenie wszystkich niedołączonych dysków zarządzanych.
>
>Po przejrzeniu wszystkich niedołączonych dysków ponownie uruchom skrypt i Ustaw zmienną **deleteUnattachedDisks** na 1. Ta akcja pozwala usunąć wszystkie niedołączone dyski zarządzane.
>

```azurecli
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0
unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Dyski niezarządzane: Znajdź i Usuń niedołączone dyski 

Dyski niezarządzane to pliki VHD, które są przechowywane jako [stronicowe obiekty blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) na [kontach usługi Azure Storage](../../storage/common/storage-account-overview.md). Następujący skrypt szuka niedołączonych dysków niezarządzanych (stronicowych obiektów BLOB) przez sprawdzenie wartości właściwości **LeaseStatus** . Gdy dysk niezarządzany jest dołączony do maszyny wirtualnej, właściwość **LeaseStatus** jest ustawiona na **zablokowaną**. Gdy dysk niezarządzany jest niedołączony, właściwość **LeaseStatus** jest ustawiona na **odblokowany**. Skrypt bada wszystkie dyski niezarządzane na wszystkich kontach usługi Azure Storage w ramach subskrypcji platformy Azure. Gdy skrypt lokalizuje dysk niezarządzany z właściwością **LeaseStatus** ustawioną na **odblokowane**, skrypt określa, że dysk nie jest dołączony.

>[!IMPORTANT]
>Najpierw uruchom skrypt, ustawiając zmienną **deleteUnattachedVHDs** na 0. Ta akcja umożliwia znalezienie i wyświetlenie wszystkich niedołączonych niepołączonych dysków VHD.
>
>Po przejrzeniu wszystkich niedołączonych dysków ponownie uruchom skrypt i Ustaw zmienną **deleteUnattachedVHDs** na 1. Ta akcja pozwala usunąć wszystkie niedołączone niezarządzane wirtualne dyski twarde.
>

```azurecli
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0
storageAccountIds=$(az storage account list --query [].[id] -o tsv)
for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [usuwanie konta magazynu](../../storage/common/storage-account-create.md#delete-a-storage-account).