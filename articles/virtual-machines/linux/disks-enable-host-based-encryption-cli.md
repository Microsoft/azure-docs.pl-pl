---
title: Włączanie szyfrowania end-to-end przy użyciu szyfrowania na hoście — interfejs wiersza polecenia platformy Azure — dyski zarządzane
description: Użyj szyfrowania na hoście, aby włączyć szyfrowanie end-to-end na dyskach zarządzanych platformy Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 45cdb9217eebf6e3129718a96d9f7b72a3ab62b3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533611"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Włączanie szyfrowania end-to-end przy użyciu szyfrowania na hoście za pomocą interfejsu wiersza polecenia platformy Azure

Po włączeniu szyfrowania na hoście dane przechowywane na hoście maszyny wirtualnej są szyfrowane podczas magazynowania i szyfrowane przepływy do usługi Storage. Aby uzyskać koncepcyjne informacje na temat szyfrowania na hoście, a także innych typów szyfrowania dysków zarządzanych, zobacz Szyfrowanie na hoście — [end-to-end encryption for your VM data](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)(Szyfrowanie na hoście — szyfrowanie danych maszyny wirtualnej).

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Pełną listę obsługiwanych rozmiarów maszyn wirtualnych można ściągnąć programowo. Aby dowiedzieć się, jak pobrać je programowo, zapoznaj się z [sekcją Znajdowanie obsługiwanych rozmiarów maszyn](#finding-supported-vm-sizes) wirtualnych.
Uaktualnienie rozmiaru maszyny wirtualnej spowoduje weryfikację w celu sprawdzenia, czy nowy rozmiar maszyny wirtualnej obsługuje funkcję EncryptionAtHost.

## <a name="prerequisites"></a>Wymagania wstępne

Należy włączyć tę funkcję dla subskrypcji przed użyciem właściwości EncryptionAtHost dla maszyny wirtualnej/zestawu usług VMSS. Wykonaj poniższe kroki, aby włączyć funkcję dla subskrypcji:

1.  Wykonaj następujące polecenie, aby zarejestrować funkcję dla subskrypcji

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Przed wypróbowaniem funkcji sprawdź, czy stan rejestracji to Zarejestrowano (trwa kilka minut) przy użyciu poniższego polecenia.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Tworzenie zestawu Azure Key Vault DiskEncryptionSet

Po włączeniu tej funkcji należy skonfigurować zestaw Azure Key Vault DiskEncryptionSet, jeśli jeszcze tego nie zrobiliśmy.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Przykłady

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Utwórz maszynę wirtualną z włączonym szyfrowaniem na hoście przy użyciu kluczy zarządzanych przez klienta. 

Utwórz maszynę wirtualną z dyskami zarządzanymi przy użyciu utworzonego wcześniej klucza URI zasobu DiskEncryptionSet w celu zaszyfrowania pamięci podręcznej dysków systemu operacyjnego i danych przy użyciu kluczy zarządzanych przez klienta. Dyski tymczasowe są szyfrowane przy użyciu kluczy zarządzanych przez platformę. 

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

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Utwórz maszynę wirtualną z włączonym szyfrowaniem na hoście przy użyciu kluczy zarządzanych przez platformę. 

Utwórz maszynę wirtualną z włączonym szyfrowaniem na hoście, aby zaszyfrować pamięć podręczną dysków systemu operacyjnego/danych i dysków tymczasowych przy użyciu kluczy zarządzanych przez platformę. 

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

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Sprawdzanie stanu szyfrowania na hoście dla maszyny wirtualnej

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Utwórz zestaw skalowania maszyn wirtualnych z włączonym szyfrowaniem na hoście przy użyciu kluczy zarządzanych przez klienta. 

Utwórz zestaw skalowania maszyn wirtualnych z dyskami zarządzanymi przy użyciu utworzonego wcześniej klucza URI zasobu DiskEncryptionSet w celu zaszyfrowania pamięci podręcznej dysków systemu operacyjnego i danych przy użyciu kluczy zarządzanych przez klienta. Dyski tymczasowe są szyfrowane przy użyciu kluczy zarządzanych przez platformę. 

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

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Utwórz zestaw skalowania maszyn wirtualnych z włączonym szyfrowaniem na hoście przy użyciu kluczy zarządzanych przez platformę. 

Utwórz zestaw skalowania maszyn wirtualnych z włączonym szyfrowaniem na hoście w celu szyfrowania pamięci podręcznej dysków systemu operacyjnego/danych i dysków tymczasowych przy użyciu kluczy zarządzanych przez platformę. 

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

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Aktualizowanie zestawu skalowania maszyn wirtualnych w celu włączenia szyfrowania na hoście. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Sprawdzanie stanu szyfrowania na hoście dla zestawu skalowania maszyn wirtualnych

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Znajdowanie obsługiwanych rozmiarów maszyn wirtualnych

Starsze rozmiary maszyn wirtualnych nie są obsługiwane. Listę obsługiwanych rozmiarów maszyn wirtualnych można znaleźć w jednym z tych elementów:

Wywołanie [interfejsu API jednostki SKU zasobów](/rest/api/compute/resourceskus/list) i sprawdzenie, czy `EncryptionAtHostSupported` dla możliwości ustawiono wartość **True.**

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

Lub wywołanie polecenia cmdlet [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) programu PowerShell.

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

Teraz, po utworzeniu i skonfigurowaniu tych zasobów, możesz użyć ich do zabezpieczenia dysków zarządzanych. Poniższy link zawiera przykładowe skrypty, z których każdy ma odpowiedni scenariusz, których można użyć do zabezpieczenia dysków zarządzanych.

[Przykłady szablonów usługi Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
