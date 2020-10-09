---
title: Dodawanie certyfikatu aplikacji do klastra w programie PowerShell
description: Przykładowy skrypt programu Azure PowerShell — dodawanie certyfikatu aplikacji do klastra usługi Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0f5a7d49200c7dec08f71f9cb0256630728e4711
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090146"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Dodawanie certyfikatu aplikacji do klastra usługi Service Fabric

Ten przykładowy skrypt przedstawia sposób tworzenia certyfikatu w programie Key Vault, a następnie wdrażania go w jednym z zestawów skalowania maszyn wirtualnych, na których działa klaster. Ten scenariusz nie używa bezpośrednio Service Fabric, ale raczej zależy od Key Vault i w zestawach skalowania maszyn wirtualnych.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/), a następnie uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure. 

## <a name="create-a-certificate-in-key-vault"></a>Tworzenie certyfikatu w Key Vault

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Lub Przekaż istniejący certyfikat do Key Vault

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$bytes = [System.IO.File]::ReadAllBytes($PathToPFX)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Aktualizowanie profilu zestawu skalowania maszyn wirtualnych przy użyciu certyfikatu

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

# If you have added your certificate to the keyvault certificates, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore

# Otherwise, if you have added your certificate to the keyvault secrets, use
$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultSecret -VaultName $VaultName -Name $CertName).Id -CertificateStore $CertStore

$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($CertConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>Aktualizowanie zestawu skalowania maszyn wirtualnych
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Jeśli chcesz, aby certyfikat został umieszczony na wielu typach węzłów w klastrze, należy powtórzyć drugą i trzecią część tego skryptu dla każdego typu węzła, który powinien mieć certyfikat.

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Tworzy zasady w pamięci reprezentujące certyfikat |
| [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Wdraża zasady w celu Key Vault certyfikatów |
| [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/Set-AzKeyVaultSecret)| Wdraża zasady w celu Key Vault wpisów tajnych |
| [New-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Tworzy konfigurację w pamięci reprezentującą certyfikat w maszynie wirtualnej |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Dodaje certyfikat do definicji w pamięci zestawu skalowania maszyn wirtualnych |
| [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Wdraża nową definicję zestawu skalowania maszyn wirtualnych |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładów programu Azure PowerShell dla usługi Azure Service Fabric można znaleźć w [przykładach programu Azure PowerShell](../service-fabric-powershell-samples.md).
