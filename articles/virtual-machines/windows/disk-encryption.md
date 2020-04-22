---
title: Szyfrowanie po stronie serwera dysków zarządzanych platformy Azure — program PowerShell
description: Usługa Azure Storage chroni dane, szyfrując je w spoczynku przed utrwaleniem ich w klastrach magazynu. Szyfrowanie dysków zarządzanych przez firmę Microsoft można polegać na kluczach zarządzanych lub za pomocą kluczy zarządzanych przez klienta do zarządzania szyfrowaniem za pomocą własnych kluczy.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 3f6039382b970ca3b0d77042c60cb720f96466d2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759197"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Szyfrowanie po stronie serwera dysków zarządzanych platformy Azure

Dyski zarządzane platformy Azure domyślnie automatycznie szyfrują dane podczas utrwalania ich w chmurze. Szyfrowanie po stronie serwera chroni dane i pomaga spełnić zobowiązania dotyczące zabezpieczeń i zgodności z przepisami organizacji. Dane na dyskach zarządzanych platformy Azure są szyfrowane w sposób przezroczysty przy użyciu 256-bitowego [szyfrowania AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), jednego z najsilniejszych dostępnych szyfrów blokowych i zgodnego ze standardem FIPS 140-2.

Szyfrowanie nie wpływa na wydajność dysków zarządzanych. Szyfrowanie nie wiąże się z żadnymi dodatkowymi kosztami.

Aby uzyskać więcej informacji na temat modułów kryptograficznych leżących u podstaw dysków zarządzanych platformy Azure, zobacz [Interfejs API kryptografii: następna generacja](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Zarządzanie kluczami szyfrowania — informacje

Szyfrowanie można polegać na kluczach zarządzanych przez platformę do szyfrowania dysku zarządzanego lub zarządzać szyfrowaniem przy użyciu własnych kluczy. Jeśli zdecydujesz się zarządzać szyfrowaniem za pomocą własnych kluczy, możesz określić *klucz zarządzany przez klienta* do szyfrowania i odszyfrowywania wszystkich danych na dyskach zarządzanych. 

W poniższych sekcjach opisano wszystkie opcje zarządzania kluczami bardziej szczegółowo.

## <a name="platform-managed-keys"></a>Klucze zarządzane przez platformę

Domyślnie dyski zarządzane używają kluczy szyfrowania zarządzanych przez platformę. Od 10 czerwca 2017 r. wszystkie nowe dyski zarządzane, migawki, obrazy i nowe dane zapisywane na istniejących dyskach zarządzanych są automatycznie szyfrowane w spoczynku za pomocą kluczy zarządzanych przez platformę.

## <a name="customer-managed-keys"></a>Klucze zarządzane przez klienta

Można zarządzać szyfrowaniem na poziomie każdego dysku zarządzanego za pomocą własnych kluczy. Szyfrowanie po stronie serwera dla dysków zarządzanych za pomocą kluczy zarządzanych przez klienta oferuje zintegrowane środowisko z usługą Azure Key Vault. Klucze [RSA](../../key-vault/keys/hsm-protected-keys.md) można zaimportować do magazynu kluczy lub wygenerować nowe klucze RSA w usłudze Azure Key Vault. 

Dyski zarządzane platformy Azure obsługują szyfrowanie i odszyfrowywanie w pełni przejrzysty sposób przy użyciu [szyfrowania kopert.](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Szyfruje dane przy użyciu klucza szyfrowania danych opartego na [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), który z kolei jest chroniony za pomocą kluczy. Usługa Storage generuje klucze szyfrowania danych i szyfruje je za pomocą kluczy zarządzanych przez klienta przy użyciu szyfrowania RSA. Szyfrowanie kopert umożliwia okresowe obracanie (zmienianie) kluczy zgodnie z zasadami zgodności bez wpływu na maszyny wirtualne. Po obróceniu kluczy usługa Storage ponownie szyfruje klucze szyfrowania danych za pomocą nowych kluczy zarządzanych przez klienta. 

Musisz udzielić dostępu do dysków zarządzanych w magazynie kluczy, aby używać kluczy do szyfrowania i odszyfrowywania pliku DEK. Pozwala to na pełną kontrolę nad danymi i kluczami. Klucze można wyłączyć lub odwołać dostęp do dysków zarządzanych w dowolnym momencie. Można również przeprowadzić inspekcję użycia klucza szyfrowania za pomocą monitorowania usługi Azure Key Vault, aby upewnić się, że tylko dyski zarządzane lub inne zaufane usługi platformy Azure uzyskują dostęp do kluczy.

W przypadku dysków SSD premium, standardowych dysków SSD i standardowych dysków twardych: po wyłączeniu lub usunięciu klucza wszystkie maszyny wirtualne z dyskami używającymi tego klucza zostaną automatycznie zamknięte. Po tym, maszyny wirtualne nie będzie można było ować, chyba że klucz jest włączony ponownie lub przypisać nowy klucz.

W przypadku dysków ultra po wyłączeniu lub usunięciu klucza wszystkie maszyny wirtualne z dyskami ultra przy użyciu klucza nie zostaną automatycznie zamknięte. Po przydzieleniu i ponownym uruchomieniu maszyn wirtualnych dyski przestaną używać klucza, a następnie maszyny wirtualne nie wrócą do trybu online. Aby przywrócić maszyny wirtualne do trybu online, należy przypisać nowy klucz lub włączyć istniejący klucz.

Na poniższym diagramie pokazano, jak dyski zarządzane używają usługi Azure Active Directory i usługi Azure Key Vault do składania żądań przy użyciu klucza zarządzanego przez klienta:

![Przepływ pracy zarządzanych kluczy zarządzanych i kluczy zarządzanych przez klienta. Administrator tworzy usługę Azure Key Vault, a następnie tworzy zestaw szyfrowania dysku i konfiguruje zestaw szyfrowania dysku. Zestaw jest skojarzony z maszyną wirtualną, która umożliwia dyskowi korzystanie z usługi Azure AD do uwierzytelniania](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Poniższa lista wyjaśnia schemat bardziej szczegółowo:

1. Administrator usługi Azure Key Vault tworzy zasoby magazynu kluczy.
1. Administrator magazynu kluczy importuje klucze RSA do magazynu kluczy lub generuje nowe klucze RSA w przechowalni kluczy.
1. Ten administrator tworzy wystąpienie zasobu Zestawu szyfrowania dysku, określając identyfikator usługi Azure Key Vault i kluczowy adres URL. Zestaw szyfrowania dysku to nowy zasób wprowadzony w celu uproszczenia zarządzania kluczami dla dysków zarządzanych. 
1. Podczas tworzenia zestawu szyfrowania dysku w usłudze Azure Active Directory (AD) tworzona jest [tożsamość zarządzana przypisana przez system](../../active-directory/managed-identities-azure-resources/overview.md) i skojarzona z zestawem szyfrowania dysku. 
1. Administrator magazynu kluczy platformy Azure następnie udziela uprawnień tożsamości zarządzanej do wykonywania operacji w magazynie kluczy.
1. Użytkownik maszyny Wirtualnej tworzy dyski, kojarząc je z zestawem szyfrowania dysku. Użytkownik maszyny Wirtualnej może również włączyć szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta dla istniejących zasobów, kojarząc je z zestawem szyfrowania dysku. 
1. Dyski zarządzane używają tożsamości zarządzanej do wysyłania żądań do usługi Azure Key Vault.
1. Do odczytywania lub zapisywania danych dyski zarządzane wysyłają żądania do usługi Azure Key Vault w celu zaszyfrowania (zawijania) i odszyfrowania (rozpakowania) klucza szyfrowania danych w celu przeprowadzenia szyfrowania i odszyfrowywania danych. 

Aby odwołać dostęp do kluczy zarządzanych przez klienta, zobacz [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) i [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Odwołując dostęp skutecznie blokuje dostęp do wszystkich danych na koncie magazynu, ponieważ klucz szyfrowania jest niedostępny przez usługę Azure Storage.

### <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Ograniczenia

Na razie klucze zarządzane przez klienta mają następujące ograniczenia:

- Jeśli ta funkcja jest włączona dla dysku, nie można jej wyłączyć.
    Jeśli chcesz obejść ten problem, należy [skopiować wszystkie dane na](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) zupełnie inny dysk zarządzany, który nie używa kluczy zarządzanych przez klienta.
- Obsługiwane są tylko ["miękkie" i "twarde" klucze RSA](../../key-vault/keys/about-keys.md) o rozmiarze 2080, bez innych klawiszy lub rozmiarów.
- Dyski utworzone na podstawie obrazów niestandardowych, które są szyfrowane przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta, muszą być szyfrowane przy użyciu tych samych kluczy zarządzanych przez klienta i muszą być w tej samej subskrypcji.
- Migawki utworzone z dysków zaszyfrowanych za pomocą szyfrowania po stronie serwera i kluczy zarządzanych przez klienta muszą być szyfrowane przy tym samym kluczu zarządzanym przez klienta.
- W galerii obrazów udostępnionych nie można używać obrazów niestandardowych zaszyfrowanych przy użyciu szyfrowania po stronie serwera i kluczy zarządzanych przez klienta.
- Wszystkie zasoby związane z kluczami zarządzanymi przez klienta (usługi Azure Key Vaults, zestawy szyfrowania dysków, maszyny wirtualne, dyski i migawki) muszą znajdować się w tej samej subskrypcji i regionie.
- Dyski, migawki i obrazy zaszyfrowane za pomocą kluczy zarządzanych przez klienta nie mogą przejść do innej subskrypcji.
- Jeśli używasz witryny Azure portal do tworzenia zestawu szyfrowania dysku, nie można na razie używać migawek.
- Dyski zarządzane szyfrowane przy użyciu kluczy zarządzanych przez klienta nie mogą być również szyfrowane za pomocą szyfrowania dysków platformy Azure.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Konfigurowanie usługi Azure Key Vault i DiskEncryptionSet

1. Upewnij się, że zainstalowano [najnowszą wersję programu Azure PowerShell](/powershell/azure/install-az-ps)i zalogowano się do konta platformy Azure za pomocą connect-AzAccount

1. Utwórz wystąpienie usługi Azure Key Vault i klucz szyfrowania.

    Podczas tworzenia wystąpienia magazynu kluczy należy włączyć ochronę usuwania nietrwałego i oczyszczania. Usuwanie nietrwałe zapewnia, że magazyn kluczy przechowuje usunięty klucz dla danego okresu przechowywania (domyślnie 90 dni). Ochrona przed przeczyszczaniem gwarantuje, że usunięty klucz nie może zostać trwale usunięty, dopóki nie upłynie okres przechowywania. Te ustawienia chronią przed utratą danych z powodu przypadkowego usunięcia. Te ustawienia są obowiązkowe w przypadku używania usługi Key Vault do szyfrowania dysków zarządzanych.

    > [!IMPORTANT]
    > Nie przypadku wielbłąda regionu, jeśli to zrobisz, mogą wystąpić problemy podczas przypisywania dodatkowych dysków do zasobu w witrynie Azure portal.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Utwórz wystąpienie zestawu DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Udziel zasobu DiskEncryptionSet dostępu do magazynu kluczy.

        > [!NOTE]
        > Może upłynąć kilka minut, aby platforma Azure utworzyła tożsamość zestawu DiskEncryptionSet w usłudze Azure Active Directory. Jeśli podczas uruchamiania następującego polecenia pojawia się błąd ,,Nie można odnaleźć obiektu usługi Active Directory", odczekaj kilka minut i spróbuj ponownie.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Tworzenie maszyny wirtualnej przy użyciu obrazu w portalu Marketplace, szyfrowanie systemu operacyjnego i dysków z danymi za pomocą kluczy zarządzanych przez klienta

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Tworzenie pustego dysku zaszyfrowanego przy użyciu szyfrowania po stronie serwera za pomocą kluczy zarządzanych przez klienta i dołączanie go do maszyny Wirtualnej

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

#### <a name="encrypt-existing-unattached-managed-disks"></a>Szyfruj istniejące niezałączone dyski zarządzane 

Istniejące dyski nie mogą być podłączone do uruchomionej maszyny Wirtualnej w celu zaszyfrowania ich przy użyciu następującego skryptu:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Tworzenie zestawu skalowania maszyny wirtualnej przy użyciu obrazu marketplace, szyfrowanie systemu operacyjnego i dysków danych za pomocą kluczy zarządzanych przez klienta

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

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Zmienianie klucza zestawu DiskEncryptionSet w celu obracania klucza dla wszystkich zasobów odwołujących się do zestawu DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Znajdowanie stanu szyfrowania po stronie serwera dysku

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> Klucze zarządzane przez klienta opierają się na zarządzanych tożsamościach zasobów platformy Azure, funkcja usługi Azure Active Directory (Azure AD). Podczas konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli następnie przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z dyskami zarządzanymi nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji między katalogami usługi Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Klucze zarządzane przez klienta opierają się na zarządzanych tożsamościach zasobów platformy Azure, funkcja usługi Azure Active Directory (Azure AD). Podczas konfigurowania kluczy zarządzanych przez klienta tożsamość zarządzana jest automatycznie przypisywana do zasobów w ramach okładek. Jeśli następnie przeniesiesz subskrypcję, grupę zasobów lub dysk zarządzany z jednego katalogu usługi Azure AD do innego, tożsamość zarządzana skojarzona z dyskami zarządzanymi nie zostanie przeniesiona do nowej dzierżawy, więc klucze zarządzane przez klienta mogą przestać działać. Aby uzyskać więcej informacji, zobacz [Przenoszenie subskrypcji między katalogami usługi Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Szyfrowanie po stronie serwera a szyfrowanie dysków platformy Azure

[Usługa Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) wykorzystuje funkcję [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systemu Windows i funkcję [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) systemu Linux do szyfrowania dysków zarządzanych za pomocą kluczy zarządzanych przez klienta w ramach maszyny Wirtualnej gościa.  Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta poprawia działanie usługi ADE, umożliwiając używanie dowolnych typów systemu operacyjnego i obrazów dla maszyn wirtualnych przez szyfrowanie danych w usłudze Magazynowanie.

## <a name="next-steps"></a>Następne kroki

- [Eksplorowanie szablonów usługi Azure Resource Manager do tworzenia zaszyfrowanych dysków za pomocą kluczy zarządzanych przez klienta](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)
- [Replikowanie maszyn z dyskami z włączonymi kluczami zarządzanymi przez klienta](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Konfigurowanie odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure za pomocą programu PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Konfigurowanie odzyskiwania po awarii na platformie Azure dla maszyn wirtualnych z programem Hyper-V przy użyciu programów PowerShell i usługi Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
