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
ms.openlocfilehash: 4bdeef537556db94338ed50fcfa6e9d88431f25a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016265"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>Interfejs wiersza polecenia

Można utworzyć przyrostową migawkę przy użyciu interfejsu wiersza polecenia platformy Azure, która będzie potrzebna w najnowszej wersji interfejsu wiersza polecenia platformy Azure. 

W systemie Windows następujące polecenie zainstaluje lub zaktualizuje istniejącą instalację do najnowszej wersji:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
W systemie Linux instalacja interfejsu wiersza polecenia różni się w zależności od wersji systemu operacyjnego.  Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) dla konkretnej wersji systemu Linux.

Aby utworzyć migawkę przyrostową, użyj [AZ Snapshot Create](/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) z `--incremental` parametrem.

Poniższy przykład obejmuje tworzenie przyrostowych migawek, zastępowanie `<yourDesiredSnapShotNameHere>` , `<yourResourceGroupNameHere>` , `<exampleDiskName>` , i `<exampleLocation>` przy użyciu własnych wartości, a następnie uruchomienie przykładu:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Można zidentyfikować przyrostowe migawki z tego samego dysku przy użyciu `SourceResourceId` i `SourceUniqueId` Właściwości migawek. `SourceResourceId` jest Azure Resource Manager IDENTYFIKATORem zasobu dysku nadrzędnego. `SourceUniqueId` jest wartością dziedziczoną z `UniqueId` właściwości dysku. Jeśli chcesz usunąć dysk, a następnie utworzyć nowy dysk o tej samej nazwie, wartość `UniqueId` właściwości zostanie zmieniona.

Za pomocą programu `SourceResourceId` i można `SourceUniqueId` utworzyć listę wszystkich migawek skojarzonych z określonym dyskiem. Poniższy przykład wyświetla listę wszystkich migawek przyrostowych skojarzonych z określonym dyskiem, ale wymaga pewnego Instalatora.

Ten przykład używa JQ do wykonywania zapytań dotyczących danych. Aby uruchomić ten przykład, należy [zainstalować JQ](https://stedolan.github.io/jq/download/).

Zastąp `<yourResourceGroupNameHere>` `<exampleDiskName>` wartości i wartościami, a następnie użyj poniższego przykładu, aby wyświetlić listę istniejących migawek przyrostowych, o ile również zainstalowano JQ:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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