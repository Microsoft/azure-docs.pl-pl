---
title: Włączanie serii dysków na żądanie
description: Włącz na dysku zarządzanym możliwość serii dysków na żądanie.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5110e580bada7bb1090b17d6df22a9354622e8e4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483150"
---
# <a name="enable-on-demand-bursting"></a>Włączanie funkcji bursting na żądanie

Dyski półprzewodnikowe (SSD) w warstwie Premium mają dwa dostępne modele serii: wzrosty na podstawie środków i wzrosty na żądanie. W tym artykule opisano sposób przełączania się na wzrosty na żądanie. Dyski, które korzystają z modelu na żądanie, mogą przekroczyć swoje pierwotne aprowizowane cele. Wzrosty na żądanie występują tak często, jak jest to wymagane przez obciążenie, aż do maksymalnej wartości docelowej serii. Wzrost na żądanie wiąże się z dodatkowymi opłatami.

Aby uzyskać szczegółowe informacje na temat serii dysków, zobacz Managed disk bursting (Seria [serii dysków zarządzanych).](disk-bursting.md)

> [!IMPORTANT]
> Nie musisz postępować zgodnie z instrukcjami w tym artykule, aby użyć funkcji bursting na podstawie środków. Domyślnie na wszystkich kwalifikujących się dyskach jest włączone bursting oparte na środków.

Przed włączeniem funkcji bursting na żądanie należy zrozumieć następujące kwestie:

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>Dostępność w regionach

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Funkcję bursting na żądanie można włączyć za pomocą modułu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Resource Manager szablonów. W poniższych przykładach opisano sposób tworzenia nowego dysku z włączonym trybem bursting na żądanie i włączaniem funkcji bursting na żądanie na istniejących dyskach.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Polecenia cmdlet z serii na żądanie są dostępne w wersji 5.5.0 i nowszej modułu Az. Alternatywnie możesz użyć Azure Cloud Shell [.](https://shell.azure.com/)
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>Tworzenie pustego dysku danych z serii na żądanie

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić skalowanie na żądanie. Zastąp parametry i , a następnie uruchom następujący skrypt, aby utworzyć dysk SSD w warstwie Premium z serii na `<myResourceGroupDisk>` `<myDataDisk>` żądanie:

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>Włączanie funkcji bursting na żądanie na istniejącym dysku

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić skalowanie na żądanie. Zastąp parametry , i uruchom to polecenie, aby włączyć na żądanie serii `<myResourceGroupDisk>` `<myDataDisk>` na istniejącym dysku:

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet z serii na żądanie są dostępne w wersji 2.19.0 i nowszej modułu interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli) Alternatywnie możesz użyć Azure Cloud Shell [.](https://shell.azure.com/)

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>Tworzenie i dołączanie dysku danych z serii na żądanie

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić skalowanie na żądanie. Zastąp parametry , i , a następnie uruchom następujące polecenia, aby utworzyć dysk SSD w warstwie Premium z serii na `<yourDiskName>` `<yourResourceGroup>` `<yourVMName>` żądanie:

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>Włączanie serii na żądanie na istniejącym dysku — interfejs wiersza polecenia

Dysk zarządzany musi być większy niż 512 GiB, aby umożliwić skalowanie na żądanie. Zastąp parametry `<myResourceGroupDisk>` i i uruchom to polecenie, aby włączyć na żądanie serii `<yourDiskName>` na istniejącym dysku:

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

Za pomocą interfejsu API dysku można włączyć skalowanie na żądanie na nowo utworzonych lub istniejących dyskach SSD Premium o rozmiarze większym `2020-09-30` niż 512 GiB. Interfejs `2020-09-30` API wprowadził nową właściwość , `burstingEnabled` . Domyślnie ta właściwość jest ustawiona na wartość false. Poniższy przykładowy szablon tworzy dysk SSD w warstwie Premium 1TiB w zachodnio-środkowych stany USA z włączoną możliwością serii dysków:

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

Aby dowiedzieć się, jak uzyskać wgląd w zasoby, których ilość się rozsyła, zobacz [Metryki dotyczące serii dysków.](disks-metrics.md)
