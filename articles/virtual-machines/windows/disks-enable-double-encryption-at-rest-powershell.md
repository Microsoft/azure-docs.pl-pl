---
title: Azure PowerShell — Włącz podwójne szyfrowanie na dyskach z zarządzaną resztą
description: Włącz podwójne szyfrowanie dla danych dysku zarządzanego przy użyciu Azure PowerShell.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0f386e4ba4a1835b88b753574bde23e93f7f8d17
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86236022"
---
# <a name="azure-powershell---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure PowerShell — Włącz podwójne szyfrowanie na dyskach zarządzanych

Azure Disk Storage obsługuje podwójne szyfrowanie dla dysków zarządzanych. Aby uzyskać informacje koncepcyjne na temat podwójnego szyfrowania w stanie spoczynku, a także innych typów szyfrowania dysków zarządzanych, zobacz sekcję [podwójne szyfrowanie w spoczynku](disk-encryption.md#double-encryption-at-rest) artykułu szyfrowanie dysków.

## <a name="supported-regions"></a>Obsługiwane regiony

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj najnowszą [wersję Azure PowerShell](/powershell/azure/install-az-ps)i zaloguj się do konta platformy Azure przy użyciu polecenia [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-4.3.0).

## <a name="getting-started"></a>Wprowadzenie

1. Utwórz wystąpienie Azure Key Vault i klucza szyfrowania.

    Podczas tworzenia wystąpienia Key Vault należy włączyć opcję trwałe usuwanie i przeczyszczanie ochrony. Usuwanie nietrwałe gwarantuje, że Key Vault przechowuje usunięty klucz dla danego okresu przechowywania (wartość domyślna 90). Ochrona przed przeczyszczeniem gwarantuje, że usunięty klucz nie może zostać trwale usunięty, dopóki nie upłynie okres przechowywania. Te ustawienia chronią przed utratą danych z powodu przypadkowego usunięcia. Te ustawienia są obowiązkowe w przypadku używania Key Vault do szyfrowania dysków zarządzanych.
    
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

1.  Utwórz element DiskEncryptionSet z atrybutem EncryptionType ustawiony jako EncryptionAtRestWithPlatformAndCustomerKeys. Użyj interfejsu API w wersji **2020-05-01** w szablonie Azure Resource Manager (ARM). 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName "yourDESForDoubleEncryption" `
    -keyVaultId "subscriptions/dd80b94e-0463-4a65-8d04-c94f403879dc/resourceGroups/yourResourceGroupName/providers/Microsoft.KeyVault/vaults/yourKeyVaultName" `
    -keyVaultKeyUrl "https://yourKeyVaultName.vault.azure.net/keys/yourKeyName/403445136dee4a57af7068cab08f7d42" `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region "CentralUSEUAP"
    ```

1. Przyznaj zasobowi DiskEncryptionSet dostęp do magazynu kluczy.

    > [!NOTE]
    > Utworzenie tożsamości DiskEncryptionSet w Azure Active Directory przez platformę Azure może potrwać kilka minut. Jeśli zostanie wyświetlony błąd "nie można odnaleźć Active Directory obiektu" podczas uruchamiania poniższego polecenia, poczekaj kilka minut i spróbuj ponownie.

    ```powershell  
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu i skonfigurowaniu tych zasobów można użyć ich do zabezpieczenia dysków zarządzanych. Poniższe linki zawierają przykładowe skrypty, z których każdy może być używany do zabezpieczania dysków zarządzanych.

[Azure PowerShell — Włącz klucze zarządzane przez klienta za pomocą dysków](disks-enable-customer-managed-keys-powershell.md) 
 zarządzanych przez szyfrowanie po stronie serwera [Przykłady szablonów Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)