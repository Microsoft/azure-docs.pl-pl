---
title: Azure PowerShell — Włącz klucze zarządzane przez klienta przy użyciu dysków zarządzanych przez funkcję SSE
description: Włącz szyfrowanie po stronie serwera przy użyciu kluczy zarządzanych przez klienta na dyskach zarządzanych przy użyciu Azure PowerShell.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: f967d061ece5887457375b4e93469809f6a575f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88814777"
---
# <a name="azure-powershell---enable-customer-managed-keys-with-server-side-encryption---managed-disks"></a>Azure PowerShell — Włącz klucze zarządzane przez klienta za pomocą dysków zarządzanych przez szyfrowanie po stronie serwera

Azure Disk Storage pozwala zarządzać własnymi kluczami podczas korzystania z szyfrowania po stronie serwera (SSE) dla dysków zarządzanych, jeśli wybierzesz opcję. Aby uzyskać informacje koncepcyjne na temat instrukcji SSE z kluczami zarządzanymi przez klienta, a także innych typów szyfrowania dysków zarządzanych, zobacz sekcję [klucze zarządzane przez klienta](disk-encryption.md#customer-managed-keys) w artykule dotyczącym szyfrowania dysku.

## <a name="restrictions"></a>Ograniczenia

Na razie klucze zarządzane przez klienta mają następujące ograniczenia:

- Jeśli ta funkcja jest włączona dla danego dysku, nie można jej wyłączyć.
    Jeśli zachodzi potrzeba obejścia tego problemu, należy [skopiować wszystkie dane](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) na całkowicie inny dysk zarządzany, który nie korzysta z kluczy zarządzanych przez klienta.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="set-up-your-azure-key-vault-and-diskencryptionset"></a>Konfigurowanie Azure Key Vault i DiskEncryptionSet

Aby używać kluczy zarządzanych przez klienta z funkcją SSE, należy skonfigurować Azure Key Vault i zasób DiskEncryptionSet.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="examples"></a>Przykłady

Po utworzeniu i skonfigurowaniu tych zasobów można użyć ich do zabezpieczenia dysków zarządzanych. Poniżej przedstawiono przykładowe skrypty, z których każdy może być używany do zabezpieczania dysków zarządzanych.

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Tworzenie maszyny wirtualnej przy użyciu obrazu z portalu Marketplace, szyfrowanie dysków systemu operacyjnego i danych za pomocą kluczy zarządzanych przez klienta

Skopiuj skrypt, Zastąp wszystkie przykładowe wartości własnymi parametrami, a następnie uruchom go.

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "yourRegion"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "yourVMSize"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Utwórz pusty dysk zaszyfrowany przy użyciu szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta i dołącz go do maszyny wirtualnej

Skopiuj skrypt, Zastąp wszystkie przykładowe wartości własnymi parametrami, a następnie uruchom go.

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

### <a name="encrypt-existing-managed-disks"></a>Szyfrowanie istniejących dysków zarządzanych 

Istniejące dyski nie mogą być podłączone do uruchomionej maszyny wirtualnej, aby można było je zaszyfrować przy użyciu następującego skryptu:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu obrazu z witryny Marketplace, szyfrowanie dysków systemu operacyjnego i danych za pomocą kluczy zarządzanych przez klienta

Skopiuj skrypt, Zastąp wszystkie przykładowe wartości własnymi parametrami, a następnie uruchom go.

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Zmień klucz DiskEncryptionSet, aby obrócić klucz dla wszystkich zasobów odwołujących się do DiskEncryptionSet

Skopiuj skrypt, Zastąp wszystkie przykładowe wartości własnymi parametrami, a następnie uruchom go.

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Znajdowanie stanu szyfrowania po stronie serwera

[!INCLUDE [virtual-machines-disks-encryption-status-powershell](../../../includes/virtual-machines-disks-encryption-status-powershell.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta korzystają z zarządzanych tożsamości dla zasobów platformy Azure, funkcji Azure Active Directory (Azure AD). W przypadku konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli później przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, zarządzana tożsamość skojarzona z dyskami zarządzanymi nie zostanie przetransferowana do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [transfer subskrypcji między katalogami usługi Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Następne kroki

- [Poznaj szablony Azure Resource Manager tworzenia szyfrowanych dysków przy użyciu kluczy zarządzanych przez klienta](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Replikowanie maszyn z włączonymi kluczami zarządzanymi przez klienta](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Skonfiguruj odzyskiwanie po awarii maszyn wirtualnych VMware na platformę Azure za pomocą programu PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Skonfiguruj odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych funkcji Hyper-V przy użyciu programu PowerShell i Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
