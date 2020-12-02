---
title: Włącz kompleksowe szyfrowanie przy użyciu szyfrowania w usłudze Host-dyski zarządzane w interfejsie wiersza polecenia platformy Azure
description: Użyj szyfrowania na hoście, aby umożliwić kompleksowe szyfrowanie na dyskach zarządzanych platformy Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 5c0336b80bee1cd5eb76d0ce3d5f99c7296a8467
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499768"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure w celu umożliwienia kompleksowego szyfrowania przy użyciu szyfrowania na hoście

Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. Aby uzyskać informacje koncepcyjne na temat szyfrowania na hoście, a także inne typy szyfrowania dysków zarządzanych, zobacz [szyfrowanie na hoście-end-to-end dla danych maszyny wirtualnej](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Rozmiary maszyn wirtualnych można również wyszukać programowo. Aby dowiedzieć się, jak je pobrać programowo, zapoznaj się z sekcją [Znajdowanie obsługiwanych rozmiarów maszyn wirtualnych](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać szyfrowania na hoście dla maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych, należy włączyć tę funkcję w ramach subskrypcji. Wyślij wiadomość e-mail na adres encryptionAtHost@microsoft.com z identyfikatorami subskrypcji, aby włączyć funkcję dla subskrypcji.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Tworzenie Azure Key Vault i DiskEncryptionSet

Po włączeniu tej funkcji musisz skonfigurować Azure Key Vault i DiskEncryptionSet, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Przykłady

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Utwórz maszynę wirtualną z szyfrowaniem na hoście z włączonymi kluczami zarządzanymi przez klienta. 

Utwórz maszynę wirtualną z dyskami zarządzanymi przy użyciu identyfikatora URI zasobu DiskEncryptionSet utworzonego wcześniej w celu szyfrowania pamięci podręcznej dysków systemu operacyjnego i danych z kluczami zarządzanymi przez klienta. Dyski tymczasowe są szyfrowane za pomocą kluczy zarządzanych przez platformę. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Utwórz maszynę wirtualną z szyfrowaniem na hoście z włączonymi kluczami zarządzanymi na platformie. 

Tworzenie maszyny wirtualnej z szyfrowaniem na hoście z włączoną obsługą szyfrowania pamięci podręcznej dysków systemu operacyjnego/danych i dysków tymczasowych przy użyciu kluczy zarządzanych przez platformę. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Zaktualizuj maszynę wirtualną, aby włączyć szyfrowanie na hoście. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Sprawdź stan szyfrowania na hoście dla maszyny wirtualnej

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Utwórz zestaw skalowania maszyn wirtualnych z szyfrowaniem na hoście z włączonymi kluczami zarządzanymi przez klienta. 

Utwórz zestaw skalowania maszyn wirtualnych z dyskami zarządzanymi przy użyciu identyfikatora URI zasobu DiskEncryptionSet utworzonego wcześniej w celu szyfrowania pamięci podręcznej dysków systemu operacyjnego i danych z kluczami zarządzanymi przez klienta. Dyski tymczasowe są szyfrowane za pomocą kluczy zarządzanych przez platformę. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Utwórz zestaw skalowania maszyn wirtualnych z szyfrowaniem na hoście z włączonymi kluczami zarządzanymi na platformie. 

Utwórz zestaw skalowania maszyn wirtualnych z szyfrowaniem na hoście włączonym do szyfrowania pamięci podręcznej dysków systemu operacyjnego/danych i dysków tymczasowych przy użyciu kluczy zarządzanych przez platformę. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Zaktualizuj zestaw skalowania maszyn wirtualnych, aby włączyć szyfrowanie na hoście. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Sprawdź stan szyfrowania na hoście dla zestawu skalowania maszyn wirtualnych

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Znajdowanie obsługiwanych rozmiarów maszyn wirtualnych

Starsze rozmiary maszyn wirtualnych nie są obsługiwane. Listę obsługiwanych rozmiarów maszyn wirtualnych można znaleźć w:

Wywoływanie [interfejsu API zasobów SKU](/rest/api/compute/resourceskus/list) i sprawdzanie, czy `EncryptionAtHostSupported` możliwość jest ustawiona na **wartość true**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Lub wywołując polecenie cmdlet [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) programu PowerShell.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu i skonfigurowaniu tych zasobów można użyć ich do zabezpieczenia dysków zarządzanych. Poniższy link zawiera przykładowe skrypty, każdy z odpowiednim scenariuszem, który służy do zabezpieczania dysków zarządzanych.

[Przykłady szablonów usługi Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)