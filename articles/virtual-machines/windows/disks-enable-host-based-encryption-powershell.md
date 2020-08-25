---
title: Azure PowerShell — Włącz kompleksowe szyfrowanie na hoście maszyny wirtualnej
description: Jak włączyć kompleksowe szyfrowanie dla maszyn wirtualnych platformy Azure przy użyciu szyfrowania na hoście.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6a352ecc2d2b02f03e2b55f7c5896ac905077921
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814797"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Użyj modułu Azure PowerShell, aby umożliwić kompleksowe szyfrowanie przy użyciu szyfrowania na hoście

Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane w stanie spoczynku i są zaszyfrowane w usłudze Storage. Aby uzyskać informacje koncepcyjne na temat szyfrowania na hoście, a także inne typy szyfrowania dysków zarządzanych, zobacz [szyfrowanie na hoście-end-to-end dla danych maszyny wirtualnej](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Rozmiary maszyn wirtualnych można również wyszukać programowo. Aby dowiedzieć się, jak je pobrać programowo, zapoznaj się z sekcją [Znajdowanie obsługiwanych rozmiarów maszyn wirtualnych](#finding-supported-vm-sizes) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać szyfrowania na hoście dla maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych, należy włączyć tę funkcję w ramach subskrypcji. Wyślij wiadomość e-mail do encryptionAtHost@microsoft . com z identyfikatorami subskrypcji, aby włączyć funkcję dla subskrypcji.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Tworzenie Azure Key Vault i DiskEncryptionSet

Po włączeniu tej funkcji musisz skonfigurować Azure Key Vault i DiskEncryptionSet, jeśli jeszcze tego nie zrobiono.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Włącz szyfrowanie na hoście dla dysków podłączonych do maszyn wirtualnych i zestawów skalowania maszyn wirtualnych

Możesz włączyć szyfrowanie na hoście, ustawiając nową właściwość EncryptionAtHost w obszarze securityProfile maszyn wirtualnych lub zestawów skalowania maszyn wirtualnych przy użyciu interfejsu API w wersji **2020-06-01** lub nowszej.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Przykładowe skrypty

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Włącz szyfrowanie na hoście dla dysków dołączonych do maszyny wirtualnej z kluczami zarządzanymi przez klienta

Utwórz maszynę wirtualną z dyskami zarządzanymi przy użyciu identyfikatora URI zasobu utworzonego wcześniej przez DiskEncryptionSet.

Zastąp `<yourPassword>` , `<yourVMName>` ,,,, `<yourVMSize>` `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` , i `<yourRegion>` , a następnie uruchom skrypt.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Włącz szyfrowanie na hoście dla dysków dołączonych do maszyny wirtualnej przy użyciu kluczy zarządzanych przez platformę

Zastąp `<yourPassword>` , `<yourVMName>` ,, `<yourVMSize>` `<yourResourceGroupName>` , i `<yourRegion>` , a następnie uruchom skrypt.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
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
