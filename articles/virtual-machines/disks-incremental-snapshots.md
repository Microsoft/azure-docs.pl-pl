---
title: Tworzenie migawki przyrostowej
description: Dowiedz się więcej na temat migawek przyrostowych dla dysków zarządzanych, w tym sposobu ich tworzenia za pomocą Azure Portal, Azure PowerShell module i Azure Resource Manager.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/15/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a1cfabf6ccbb5d580bcad36694cd2e1a74155714
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540644"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Tworzenie przyrostowej migawki dla dysków zarządzanych

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]


# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Za pomocą Azure PowerShell można utworzyć przyrostową migawkę. Potrzebna będzie Najnowsza wersja Azure PowerShell, następujące polecenie zainstaluje je lub zaktualizuje istniejącą instalację do najnowszej wersji:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Po zakończeniu instalacji zaloguj się do sesji programu PowerShell przy użyciu polecenia `Connect-AzAccount` .

Aby utworzyć przyrostową migawkę z Azure PowerShell, należy ustawić konfigurację przy użyciu parametru [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) z `-Incremental` parametrem, a następnie przekazać ją jako zmienną do [nowego-AzSnapshot](/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) za pomocą `-Snapshot` parametru.

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

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Szablon Menedżer zasobów](#tab/azure-resource-manager)

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
---

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz zobaczyć przykładowy kod pokazujący różnicowe możliwości migawek przyrostowych, korzystając z platformy .NET, zobacz [Kopiuj kopie zapasowe platformy Azure Managed disks do innego regionu z różnicową możliwością migawek przyrostowych](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).