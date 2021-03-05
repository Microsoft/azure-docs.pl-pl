---
title: Włącz szeregowanie dysków na żądanie
description: Włącz obsługę obciążeń dysków na żądanie na dysku zarządzanym.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f5865646200a783e7139bb5e22576ea404f58203
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216653"
---
# <a name="enable-on-demand-bursting"></a>Włącz nadużyenie na żądanie

Dyski półprzewodnikowe w warstwie Premium (SSD) mają dwa dostępne modele rozłożenia: Korzystanie z rozliczeń opartych na kredycie i na żądanie. W tym artykule opisano, jak przełączyć się na tworzenie serii na żądanie. Dyski korzystające z modelu na żądanie mogą przekroczyć pierwotne zainicjowane obiekty docelowe. Ruch na żądanie odbywa się tak często, jak jest to konieczne w przypadku obciążenia, do maksymalnego miejsca docelowego. Naliczanie na żądanie wiąże się z dodatkowymi opłatami.

Aby uzyskać szczegółowe informacje na temat rozszeregowania dysków, zobacz [Zarządzanie dyskami zarządzanymi](disk-bursting.md).

> [!IMPORTANT]
> Nie musisz wykonywać kroków opisanych w tym artykule, aby korzystać z rozliczeń opartych na kredycie. Domyślnie w przypadku wszystkich kwalifikujących się dysków jest włączona funkcja rozliczeń opartych na kredycie.

Przed włączeniem serii na żądanie należy zrozumieć następujące kwestie:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Dostępność regionalna

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Na żądanie można włączyć funkcję, korzystając z modułu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonów Azure Resource Manager. W poniższych przykładach opisano sposób tworzenia nowego dysku z włączonym rozruchem na żądanie i włączenia tworzenia na żądanie obciążeń na istniejących dyskach.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenia cmdlet do obsługi serii na żądanie są dostępne w wersji 5.5.0 i nowszych modułu AZ module. Alternatywnie możesz użyć [Azure Cloud Shell](https://shell.azure.com/).
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Tworzenie pustego dysku danych z funkcją tworzenia serii na żądanie

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić korzystanie z serii na żądanie. Zastąp `<myResourceGroupDisk>` Parametry i, `<myDataDisk>` a następnie uruchom następujący skrypt, aby utworzyć dysk SSD w warstwie Premium przy użyciu serii na żądanie:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Włączanie obciążeń na żądanie na istniejącym dysku

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić korzystanie z serii na żądanie. Zastąp `<myResourceGroupDisk>` `<myDataDisk>` Parametry, i Uruchom to polecenie, aby włączyć obsługę obciążeń na żądanie na istniejącym dysku:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet dotyczące szeregowania na żądanie są dostępne w wersji 2.19.0 i nowszej [modułu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Alternatywnie możesz użyć [Azure Cloud Shell](https://shell.azure.com/).

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Tworzenie i dołączanie dysku z danymi na żądanie

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić korzystanie z serii na żądanie. Zastąp `<yourDiskName>` `<yourResourceGroup>` Parametry,, i `<yourVMName>` , a następnie uruchom następujące polecenia, aby utworzyć dysk SSD w warstwie Premium przy użyciu serii na żądanie:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Włączanie obsługi obciążeń na żądanie na istniejącym dysku — interfejs wiersza polecenia

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić korzystanie z serii na żądanie. Zastąp `<myResourceGroupDisk>` Parametry i i `<yourDiskName>` Uruchom to polecenie, aby włączyć obsługę obciążeń na żądanie na istniejącym dysku:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Za pomocą `2020-09-30` interfejsu API dysku można włączyć funkcję tworzenia dużych ilości na żądanie na nowo utworzonym lub istniejącym poziomie Premium dysków SSD większym niż 512 GIB. `2020-09-30`Interfejs API wprowadził nową właściwość `burstingEnabled` . Domyślnie ta właściwość ma wartość FAŁSZ. Poniższy przykładowy szablon tworzy dysk SSD 1TiB Premium w regionie Zachodnio-środkowe stany USA z włączonym rozruchem dysku:

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak uzyskać wgląd w zasoby dotyczące zasobów, zobacz [metryki](disks-metrics.md)dotyczące tworzenia dysków.