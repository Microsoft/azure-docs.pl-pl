---
title: Wdrażanie usługi w chmurze (rozszerzona obsługa) — PowerShell
description: Wdrażanie usługi w chmurze (rozszerzona obsługa) przy użyciu programu PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a7e4e76aa0619d78a91d766a9a43c0b1a02a48d3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717391"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Wdrażanie usługi w chmurze (rozszerzona pomoc techniczna) przy użyciu Azure PowerShell

W tym artykule pokazano, jak za pomocą modułu programu PowerShell wdrożyć usługę Cloud Services (rozszerzona obsługa) na platformie Azure, która ma wiele ról (WebRole i WorkerRole) i rozszerzenie pulpitu `Az.CloudService` zdalnego. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przejrzyj wymagania [wstępne dotyczące wdrożenia](deploy-prerequisite.md) Cloud Services (rozszerzona pomoc techniczna) i utwórz skojarzone zasoby. 

## <a name="deploy-a-cloud-services-extended-support"></a>Wdrażanie aplikacji Cloud Services (rozszerzona pomoc techniczna)
1. Instalowanie modułu Az.CloudService programu PowerShell  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Tworzenie nowej grupy zasobów Ten krok jest opcjonalny w przypadku korzystania z istniejącej grupy zasobów.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Utwórz konto magazynu i kontener, które będą używane do przechowywania plików pakietu usługi w chmurze (cspkg) i konfiguracji usługi (cscfg). Jako nazwy konta magazynu należy użyć unikatowej nazwy. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Przekaż pakiet usługi w chmurze (cspkg) na konto magazynu.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Przekaż konfigurację usługi w chmurze (cscfg) na konto magazynu. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Utwórz sieć wirtualną i podsieć. Ten krok jest opcjonalny w przypadku korzystania z istniejącej sieci i podsieci. W tym przykładzie użyto jednej sieci wirtualnej i podsieci dla obu ról usługi w chmurze (WebRole i WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Utwórz publiczny adres IP i ustaw właściwość etykiety DNS publicznego adresu IP. Cloud Services (rozszerzona obsługa) obsługuje tylko [publiczne](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) adresy IP podstawowej wersji SKU. Publiczne ip w standardowych sku nie działają z Cloud Services.
Jeśli używasz statycznego adresu IP, musisz odwołać się do niego jako Zastrzeżony adres IP w pliku konfiguracji usługi (cscfg) 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Utwórz obiekt profilu sieciowego i skojarz publiczny adres IP z frontoną usługi równoważenia obciążenia. Platforma Azure automatycznie tworzy zasób usługi równoważenia obciążenia "klasycznej" wersji SKU w tej samej subskrypcji co zasób usługi w chmurze. Zasób równoważenia obciążenia jest zasobem tylko do odczytu w ujmie arm. Wszelkie aktualizacje zasobu są obsługiwane tylko za pośrednictwem plików wdrażania usługi w chmurze (cscfg & .csdef)

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Tworzenie usługi Key Vault. Ta Key Vault będzie używana do przechowywania certyfikatów skojarzonych z rolami usługi w chmurze (rozszerzona obsługa). Adres Key Vault musi znajdować się w tym samym regionie i subskrypcji co usługa w chmurze i mieć unikatową nazwę. Aby uzyskać więcej informacji, [zobacz Używanie certyfikatów z Azure Cloud Services (rozszerzona obsługa).](certificates-and-key-vault.md)

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Zaktualizuj zasady Key Vault dostępu i przyznaj uprawnienia certyfikatu do konta użytkownika. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Alternatywnie można ustawić zasady dostępu za pomocą objectId (który można uzyskać, uruchamiając `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. Na potrzeby tego przykładu dodamy certyfikat z podpisem własnym do Key Vault. Odcisk palca certyfikatu należy dodać w pliku konfiguracji usługi w chmurze (cscfg) w celu wdrożenia w rolach usługi w chmurze. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Utwórz obiekt profilu systemu operacyjnego w pamięci. Profil systemu operacyjnego określa certyfikaty, które są skojarzone z rolami usługi w chmurze. Będzie to ten sam certyfikat, który został utworzony w poprzednim kroku. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Utwórz obiekt profilu roli w pamięci. Profil roli definiuje specyficzne dla sku ról właściwości, takie jak nazwa, pojemność i warstwa. W tym przykładzie zdefiniowano dwie role: frontendRole i backendRole. Informacje o profilu roli powinny być zgodne z konfiguracją roli zdefiniowaną w pliku konfiguracji (cscfg) i pliku definicji usługi (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Opcjonalnie) Utwórz obiekt w pamięci profilu rozszerzenia, który chcesz dodać do usługi w chmurze. W tym przykładzie dodamy rozszerzenie RDP. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Należy pamiętać, że plik configFile powinien mieć tylko tagi PublicConfig i powinien zawierać przestrzeń nazw w następujący sposób:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Opcjonalnie) Zdefiniuj tagi jako tabelę skrótów programu PowerShell, którą chcesz dodać do usługi w chmurze. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Tworzenie wdrożenia usługi w chmurze przy użyciu obiektów & adresów URL SAS.

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
- Przejrzyj [często zadawane pytania dotyczące](faq.md) Cloud Services (rozszerzona pomoc techniczna).
- Wdrażanie usługi w chmurze (rozszerzona obsługa) przy [użyciu Azure Portal,](deploy-portal.md) [programu PowerShell,](deploy-powershell.md) [szablonu](deploy-template.md) [lub Visual Studio](deploy-visual-studio.md).
- Odwiedź [repozytorium przykładów Cloud Services (rozszerzona pomoc techniczna)](https://github.com/Azure-Samples/cloud-services-extended-support)
