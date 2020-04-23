---
title: Przykładowe skrypty usługi Azure Disk Encryption
description: Ten artykuł jest dodatkiem do szyfrowania dysków platformy Microsoft Azure dla maszyn wirtualnych systemu Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e5e0a970df680df43a7bd303636b3d81bda3e141
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085709"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Przykładowe skrypty usługi Azure Disk Encryption 

Ten artykuł zawiera przykładowe skrypty do przygotowywania wstępnie zaszyfrowanych dysków VHD i innych zadań.

 

## <a name="list-vms-and-secrets"></a>Lista maszyn wirtualnych i wpisów tajnych

Wyświetl listę wszystkich zaszyfrowanych maszyn wirtualnych w ramach subskrypcji:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
Wyświetl listę wszystkich wpisów tajnych szyfrowania dysków używanych do szyfrowania maszyn wirtualnych w magazynie kluczy:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Skrypty wymagań wstępnych szyfrowania dysków platformy Azure
Jeśli znasz już wymagania wstępne dotyczące szyfrowania dysków platformy Azure, możesz użyć [skryptu wstępnego powershell wymagań wstępnych szyfrowania dysków platformy Azure.](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ) Na przykład użycia tego skryptu programu PowerShell zobacz [Szybki start szyfrowania maszyny Wirtualnej](disk-encryption-powershell-quickstart.md). Można usunąć komentarze z sekcji skryptu, począwszy od wiersza 211, aby zaszyfrować wszystkie dyski dla istniejących maszyn wirtualnych w istniejącej grupie zasobów. 

W poniższej tabeli przedstawiono, które parametry mogą być używane w skrypcie programu PowerShell: 

|Parametr|Opis|Obowiązkowe?|
|------|------|------|
|$resourceGroupName| Nazwa grupy zasobów, do której należy keyvault.  Nowa grupa zasobów o tej nazwie zostanie utworzona, jeśli jej nie istnieje.| True|
|$keyVaultName|Nazwa keyvault, w którym mają być umieszczone klucze szyfrowania. Nowy magazyn o tej nazwie zostanie utworzony, jeśli nie istnieje.| True|
|$location|Lokalizacja keyvault. Upewnij się, że keyvault i maszyny wirtualne, które mają być zaszyfrowane są w tej samej lokalizacji. Pobierz listę lokalizacji za pomocą polecenia `Get-AzLocation`.|True|
|$subscriptionId|Identyfikator subskrypcji platformy Azure, który ma być używany.  Możesz pobrać identyfikator subskrypcji za pomocą polecenia `Get-AzSubscription`.|True|
|$aadAppName|Nazwa aplikacji usługi Azure AD, która będzie używana do zapisywania wpisów tajnych do usługi KeyVault. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli ta aplikacja już istnieje, przekaż aadClientSecret parametr do skryptu.|False|
|$aadClientSecret|Klucz tajny klienta aplikacji usługi Azure AD, który został utworzony wcześniej.|False|
|$keyEncryptionKeyName|Nazwa opcjonalnego klucza szyfrowania klucza w keyvault. Nowy klucz o tej nazwie zostanie utworzony, jeśli nie istnieje.|False|

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych bez aplikacji usługi Azure AD

- [Włączanie szyfrowania dysku na istniejącej lub uruchomionej maszynie Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Wyłączanie szyfrowania na uruchomionej maszynie Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Szyfrowanie lub odszyfrowywanie maszyn wirtualnych za pomocą aplikacji usługi Azure AD (poprzednia wersja) 
 
- [Włączanie szyfrowania dysku na istniejącej lub uruchomionej maszynie Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Wyłączanie szyfrowania na uruchomionej maszynie Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Tworzenie nowego zaszyfrowanego dysku zarządzanego ze wstępnie zaszyfrowanego obiektu blob VHD/storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Tworzy nowy zaszyfrowany dysk zarządzany pod warunkiem wstępnie zaszyfrowanego dysku VHD i odpowiadających mu ustawień szyfrowania

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Przygotowywanie wstępnie zaszyfrowanego dysku VHD systemu Windows
Następujące sekcje są niezbędne do przygotowania wstępnie zaszyfrowanego dysku VHD systemu Windows do wdrożenia jako zaszyfrowanego dysku wirtualnego w usłudze Azure IaaS. Użyj tych informacji, aby przygotować i uruchomić nową maszynę wirtualną systemu Windows (VHD) na usłudze Azure Site Recovery lub platformie Azure. Aby uzyskać więcej informacji na temat przygotowania i przekazania dysku wirtualnego, zobacz [Przekazywanie uogólnionego dysku wirtualnego i używanie go do tworzenia nowych maszyn wirtualnych na platformie Azure.](upload-generalized-managed.md)

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizowanie zasad grupy w celu zezwalania na ochronę systemu operacyjnego innych niż TPM
Skonfiguruj ustawienie zasad grupy funkcji **BitLocker,** które znajdziesz w obszarze > **Szablony** >  > **administracyjne**konfiguracji komputera **komputera lokalnego****Składniki systemu Windows**. Zmień to ustawienie na **Dyski** > systemu operacyjnego**Wymagaj dodatkowego uwierzytelniania podczas uruchamiania** > Zezwalaj na**funkcje BitLocker bez zgodnego modułu TPM,** jak pokazano na poniższym rysunku:

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Install BitLocker feature components
W systemie Windows Server 2012 lub nowszym należy użyć następującego polecenia:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

W systemie Windows Server 2008 R2 należy użyć następującego polecenia:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Przygotowanie woluminu systemu operacyjnego dla funkcji BitLocker za pomocą`bdehdcfg`
Aby skompresować partycję systemu operacyjnego i przygotować urządzenie do funkcji BitLocker, w razie potrzeby wykonaj [bdehdcfg:](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment)

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrona woluminu systemu operacyjnego za pomocą funkcji BitLocker
Użyj [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) polecenia, aby włączyć szyfrowanie na woluminie rozruchowym przy użyciu zewnętrznego ochrony kluczy. Umieść również klucz zewnętrzny (plik bek) na dysku zewnętrznym lub woluminie. Szyfrowanie jest włączone na woluminie systemowym/rozruchowym po następnym ponownym uruchomieniu komputera.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Przygotuj maszynę wirtualną z oddzielnym dyskiem wirtualnym danych/zasobu do uzyskania klucza zewnętrznego przy użyciu funkcji BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Przekazywanie zaszyfrowanego dysku twardego VHD na konto magazynu platformy Azure
Po włączeniu szyfrowania DM-Crypt, lokalny zaszyfrowany dysk wirtualny musi zostać przekazany na twoje konto magazynu.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Przekazywanie klucza tajnego wstępnie zaszyfrowanej maszyny Wirtualnej do magazynu kluczy
Klucz tajny szyfrowania dysku, który został wcześniej uzyskany, musi zostać przekazany jako klucz tajny w magazynie kluczy.  Wymaga to udzielenia uprawnienia klucza tajnego zestawu i uprawnienie wrapkey do konta, które przekaże wpisy tajne.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Klucz tajny szyfrowania dysku nie zaszyfrowany za pomocą klucza KEK
Aby skonfigurować klucz tajny w magazynie kluczy, użyj [funkcji Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Hasło jest zakodowane jako ciąg base64, a następnie przekazywane do magazynu kluczy. Ponadto upewnij się, że następujące tagi są ustawiane podczas tworzenia klucza tajnego w magazynie kluczy.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Użyj `$secretUrl` w następnym kroku do [podłączenia dysku systemu operacyjnego bez użycia KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Klucz tajny szyfrowania dysku zaszyfrowany za pomocą klucza KEK
Przed przekazaniem klucza tajnego do magazynu kluczy można go opcjonalnie zaszyfrować przy użyciu klucza szyfrowania klucza. Użyj [interfejsu API](https://msdn.microsoft.com/library/azure/dn878066.aspx) zawijania, aby najpierw zaszyfrować klucz tajny przy użyciu klucza szyfrowania klucza. Dane wyjściowe tej operacji zawijania jest base64 adres URL zakodowany ciąg, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) który można następnie przekazać jako klucz tajny przy użyciu polecenia cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Użyj `$KeyEncryptionKey` `$secretUrl` i w następnym kroku [do podłączenia dysku systemu operacyjnego za pomocą KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Określanie tajnego adresu URL podczas dołączania dysku systemu operacyjnego

###  <a name="without-using-a-kek"></a>Bez użycia KEK
Podczas podłączania dysku systemu operacyjnego należy przejść `$secretUrl`. Adres URL został wygenerowany w sekcji "Klucz tajny szyfrowania dysku nie zaszyfrowany za pomocą KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Korzystanie z KEK
Po podłączeniu dysku systemu `$KeyEncryptionKey` operacyjnego przekaż i `$secretUrl`. Adres URL został wygenerowany w sekcji "Klucz tajny szyfrowania dysku zaszyfrowany za pomocą KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
