---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 62645e6252256079e27792b1905d60a073c1fa3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080232"
---
## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

Dyski udostępnione obsługują kilka systemów operacyjnych. Zapoznaj się z sekcjami [systemu Windows](../articles/virtual-machines/windows/disks-shared.md#windows) i [Linux](../articles/virtual-machines/linux/disks-shared.md#linux) artykułu koncepcyjnego dla obsługiwanych systemów operacyjnych.

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Wdrażanie dysków udostępnionych

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Wdrażanie dysków SSD Premium jako dysku udostępnionego

Aby wdrożyć dysk zarządzany z włączoną funkcją udostępnionego dysku, użyj nowej właściwości `maxShares` i Zdefiniuj wartość większą od 1. Dzięki temu dysk jest współużytkowany na wielu maszynach wirtualnych.

> [!IMPORTANT]
> Wartość `maxShares` można ustawić lub zmienić tylko wtedy, gdy dysk zostanie odinstalowany ze wszystkich maszyn wirtualnych. Sprawdź [rozmiary dysków](#disk-sizes) dla dozwolonych wartości dla `maxShares` .

#### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli

az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku PremiumSSD_LRS --max-shares 2

```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType PremiumSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager
Przed użyciem następującego szablonu Zastąp `[parameters('dataDiskName')]` wartości, `[resourceGroup().location]` , `[parameters('dataDiskSizeGB')]` i `[parameters('maxShares')]` własnymi wartościami.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Wdróż dysk typu Ultra jako dysk udostępniony

Aby wdrożyć dysk zarządzany z włączoną funkcją udostępnionego dysku, należy zmienić `maxShares` parametr na wartość większą niż 1. Dzięki temu dysk jest współużytkowany na wielu maszynach wirtualnych.

> [!IMPORTANT]
> Wartość `maxShares` można ustawić lub zmienić tylko wtedy, gdy dysk zostanie odinstalowany ze wszystkich maszyn wirtualnych. Sprawdź [rozmiary dysków](#disk-sizes) dla dozwolonych wartości dla `maxShares` .

#### <a name="cli"></a>Interfejs wiersza polecenia
```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Aby wdrożyć dysk zarządzany z włączoną funkcją udostępnionego dysku, użyj właściwości `maxShares` i Zdefiniuj wartość większą od 1. Dzięki temu dysk jest współużytkowany na wielu maszynach wirtualnych.

> [!IMPORTANT]
> Wartość `maxShares` można ustawić lub zmienić tylko wtedy, gdy dysk zostanie odinstalowany ze wszystkich maszyn wirtualnych. Sprawdź [rozmiary dysków](#disk-sizes) dla dozwolonych wartości dla `maxShares` .

Przed użyciem następującego szablonu Zastąp wartości,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` i `[parameters('diskMBpsReadOnly')]` własnymi wartościami.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Używanie dysków udostępnionych platformy Azure z maszynami wirtualnymi

Po wdrożeniu udostępnionego dysku za pomocą programu możesz `maxShares>1` zainstalować dysk na co najmniej jednej z maszyn wirtualnych.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Obsługiwane polecenia SCSI PR

Po zainstalowaniu dysku udostępnionego na maszynach wirtualnych w klastrze można ustanowić kworum i odczyt/zapis na dysku przy użyciu żądania ściągnięcia SCSI. Następujące polecenia żądania ściągnięcia są dostępne w przypadku korzystania z dysków udostępnionych platformy Azure:

Aby współdziałać z dyskiem, należy zacząć od listy Akcja zastrzeżenia trwałego:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

W przypadku korzystania z PR_RESERVE, PR_PREEMPT_RESERVATION lub PR_RELEASE_RESERVATION Podaj jeden z następujących typów trwałych:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

W przypadku korzystania z PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION lub PR_RELEASE-rezerwacji należy również podać klucz trwały.


## <a name="next-steps"></a>Następne kroki
