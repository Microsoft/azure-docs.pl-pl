---
title: Wdrażanie usługi w chmurze (obsługa rozszerzona) — PowerShell
description: Wdrażanie usługi w chmurze (obsługa rozszerzona) przy użyciu programu PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: d6d988b4dd71fadccba056e501ba7c799b46d0d9
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508899"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Wdrażanie usługi w chmurze (obsługa rozszerzona) przy użyciu Azure PowerShell

W tym artykule pokazano, jak za pomocą `Az.CloudService` modułu PowerShell wdrożyć Cloud Services (rozszerzoną obsługę) na platformie Azure, która ma wiele ról (webrole i rola procesu roboczego) i rozszerzenie pulpitu zdalnego. 

> [!IMPORTANT]
> Cloud Services (obsługa rozszerzona) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Zapoznaj się z [wymaganiami wstępnymi](deploy-prerequisite.md) dotyczącymi wdrażania Cloud Services (obsługa rozszerzona) i Utwórz skojarzone zasoby. 

## <a name="deploy-a-cloud-services-extended-support"></a>Wdrażanie Cloud Services (obsługa rozszerzona)
1. Zainstaluj AZ. CloudService PowerShell module  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Tworzenie nowej grupy zasobów Ten krok jest opcjonalny w przypadku korzystania z istniejącej grupy zasobów.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Utwórz konto magazynu i kontener, które będą używane do przechowywania plików pakietu usługi w chmurze (. cspkg) i pliku konfiguracji usługi (. cscfg). Musisz użyć unikatowej nazwy dla nazwy konta magazynu. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “ContosoContainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Przekaż swój pakiet usługi w chmurze (cspkg) do konta magazynu.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “ContosoContainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Przekaż konfigurację usługi w chmurze (cscfg) do konta magazynu. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container ContosoContainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “ContosoContainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Utwórz sieć wirtualną i podsieć. Ten krok jest opcjonalny w przypadku korzystania z istniejącej sieci i podsieci. Ten przykład używa pojedynczej sieci wirtualnej i podsieci dla ról usługi w chmurze (webrole i rola procesu roboczego). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Utwórz publiczny adres IP i (opcjonalnie) ustaw właściwość etykieta DNS publicznego adresu IP. Jeśli używasz statycznego adresu IP, musi on być przywoływany jako Zastrzeżony adres IP w pliku konfiguracji usługi.  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Utwórz obiekt profilu sieciowego i skojarz publiczny adres IP z frontonem usługi równoważenia obciążenia utworzoną przez platformę.  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Tworzenie usługi Key Vault. Ten Key Vault będzie używany do przechowywania certyfikatów skojarzonych z rolami usługi w chmurze (obsługa rozszerzona). Upewnij się, że włączono zasady dostępu (w portalu), aby uzyskać dostęp do usługi "Azure Virtual Machines for Deployment" i "Azure Resource Manager for Template Deployment". Key Vault musi znajdować się w tym samym regionie i w ramach subskrypcji co usługa w chmurze i mieć unikatową nazwę. Aby uzyskać więcej informacji, zobacz [Korzystanie z certyfikatów przy użyciu usługi Azure Cloud Services (obsługa rozszerzona)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US” 
    ```

10. Zaktualizuj zasady dostępu Key Vault i przyznaj uprawnienia certyfikatów do konta użytkownika. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Alternatywnie możesz ustawić zasady dostępu za pomocą identyfikatora ObjectId (który można uzyskać, uruchamiając `Get-AzADUser` ). 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Na potrzeby tego przykładu dodamy certyfikat z podpisem własnym do Key Vault. Odcisk palca certyfikatu musi zostać dodany w pliku konfiguracji usługi w chmurze (cscfg) do wdrożenia w rolach usługi w chmurze. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Utwórz profil systemu operacyjnego dla obiektu w pamięci. Profil systemu operacyjnego określa certyfikaty, które są skojarzone z rolami usługi w chmurze. Będzie to ten sam certyfikat, który został utworzony w poprzednim kroku. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Utwórz profil roli w pamięci. Profil roli definiuje odpowiednie właściwości jednostki SKU, takie jak nazwa, pojemność i warstwa. W tym przykładzie zdefiniowano dwie role: frontendRole i backendRole. Informacje o profilu roli powinny być zgodne z konfiguracją roli zdefiniowaną w pliku Configuration (cscfg) i plikiem definicji usługi (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. Obowiązkowe Utwórz profil rozszerzenia w pamięci, który ma zostać dodany do usługi w chmurze. Na potrzeby tego przykładu dodamy rozszerzenie RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $extension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "ContosSA" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
15. Obowiązkowe Zdefiniuj Tagi jako tabelę skrótów programu PowerShell, którą chcesz dodać do usługi w chmurze. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Utwórz wdrożenie usługi w chmurze przy użyciu obiektów profilów & adresów URL sygnatury dostępu współdzielonego.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).
- Odwiedź [repozytorium przykładów Cloud Services (obsługa rozszerzona)](https://github.com/Azure-Samples/cloud-services-extended-support)
