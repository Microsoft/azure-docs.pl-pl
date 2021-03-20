---
title: Przewinięcie certyfikatu klastra Service Fabric platformy Azure
description: Dowiedz się, jak przetworzyć certyfikat klastra Service Fabric identyfikowany przy użyciu nazwy pospolitej certyfikatu.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 65ea4f463073c472ac6a31e62dcfdfd11cb28cc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88853353"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Ręczne przewinięcie certyfikatu klastra Service Fabric
Gdy certyfikat klastra Service Fabric jest bliski wygaśnięcia, należy zaktualizować certyfikat.  Przerzucanie certyfikatów jest proste, Jeśli klaster został [skonfigurowany do używania certyfikatów na podstawie nazwy pospolitej](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (zamiast odcisku palca).  Pobierz nowy certyfikat z urzędu certyfikacji z nową datą wygaśnięcia.  Certyfikaty z podpisem własnym nie obsługują klastrów Service Fabric produkcyjnych w celu uwzględnienia certyfikatów wygenerowanych podczas przepływu pracy tworzenia klastra Azure Portal. Nowy certyfikat musi mieć taką samą nazwę pospolitą jak certyfikat starszej wersji. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Klaster Service Fabric automatycznie będzie używać zadeklarowanego certyfikatu z dalszą datą wygaśnięcia w przyszłości. Jeśli na hoście jest zainstalowany więcej niż jeden certyfikat weryfikacji. Najlepszym rozwiązaniem jest użycie szablonu Menedżer zasobów w celu udostępnienia zasobów platformy Azure. W przypadku środowiska nieprodukcyjnego można użyć następującego skryptu do przekazania nowego certyfikatu do magazynu kluczy, a następnie zainstalowania certyfikatu na zestawie skalowania maszyn wirtualnych: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($certConfig)

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Oblicza wpisy tajne zestawu skalowania maszyn wirtualnych nie obsługują tego samego identyfikatora zasobu dla dwóch oddzielnych wpisów tajnych, ponieważ każdy klucz tajny jest unikatowym zasobem w wersji. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zabezpieczeniach klastra](service-fabric-cluster-security.md).
* [Aktualizowanie certyfikatów klastra i zarządzanie nimi](service-fabric-cluster-security-update-certs-azure.md)
