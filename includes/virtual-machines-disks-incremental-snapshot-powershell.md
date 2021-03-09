---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b4af7c8a02a1059e56bb2f709e3a4d1a9924662e
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510755"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Obsługiwane regiony
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Za pomocą Azure PowerShell można utworzyć przyrostową migawkę. Potrzebna będzie Najnowsza wersja Azure PowerShell, następujące polecenie zainstaluje je lub zaktualizuje istniejącą instalację do najnowszej wersji:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po zakończeniu instalacji zaloguj się do sesji programu PowerShell przy użyciu polecenia `Connect-AzAccount` .

Aby utworzyć przyrostową migawkę z Azure PowerShell, należy ustawić konfigurację przy użyciu parametru [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) z `-Incremental` parametrem, a następnie przekazać ją jako zmienną do [nowego-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) za pomocą `-Snapshot` parametru.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Można zidentyfikować przyrostowe migawki z tego samego dysku przy użyciu `SourceResourceId` i `SourceUniqueId` Właściwości migawek. `SourceResourceId` jest Azure Resource Manager IDENTYFIKATORem zasobu dysku nadrzędnego. `SourceUniqueId` jest wartością dziedziczoną z `UniqueId` właściwości dysku. Jeśli chcesz usunąć dysk, a następnie utworzyć nowy dysk o tej samej nazwie, wartość `UniqueId` właściwości zostanie zmieniona.

Za pomocą programu `SourceResourceId` i można `SourceUniqueId` utworzyć listę wszystkich migawek skojarzonych z określonym dyskiem. Zamień `<yourResourceGroupNameHere>` na wartość, a następnie użyj poniższego przykładu, aby wyświetlić listę istniejących migawek przyrostowych:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Za pomocą szablonów Azure Resource Manager można także utworzyć przyrostową migawkę. Należy upewnić się, że apiVersion jest ustawiona na **2019-03-01** i że właściwość przyrostowa jest również ustawiona na wartość true. Poniższy fragment kodu stanowi przykład tworzenia przyrostowej migawki z szablonami Menedżer zasobów:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zobaczyć przykładowy kod pokazujący różnicowe możliwości migawek przyrostowych, korzystając z platformy .NET, zobacz [Kopiuj kopie zapasowe platformy Azure Managed disks do innego regionu z różnicową możliwością migawek przyrostowych](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).