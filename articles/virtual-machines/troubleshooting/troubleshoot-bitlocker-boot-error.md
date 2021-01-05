---
title: Rozwiązywanie problemów z błędami rozruchu funkcji BitLocker na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z błędami rozruchu funkcji BitLocker na maszynie wirtualnej platformy Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 00095eed3fe6d143d9ed7a0c748c4702028f4632
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832064"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Błędy rozruchu funkcji BitLocker na maszynie wirtualnej platformy Azure

 W tym artykule opisano błędy funkcji BitLocker, które mogą wystąpić podczas uruchamiania maszyny wirtualnej z systemem Windows w Microsoft Azure.

 

## <a name="symptom"></a>Objaw

 Nie uruchomiono maszyny wirtualnej z systemem Windows. Po sprawdzeniu zrzutów ekranu w oknie [Diagnostyka rozruchu](./boot-diagnostics.md) zostanie wyświetlony jeden z następujących komunikatów o błędach:

- Podłącz sterownik USB, który ma klucz funkcji BitLocker

- Jesteś zablokowany. Wprowadź klucz odzyskiwania, aby ponownie rozpocząć (układ klawiatury: US) nieprawidłowe informacje logowania zostały zbyt wiele razy, więc komputer został zablokowany, aby chronić Twoją prywatność. Aby pobrać klucz odzyskiwania, przejdź do https://windows.microsoft.com/recoverykeyfaq innego komputera lub urządzenia przenośnego. W razie potrzeby, identyfikator klucza to XXXXXXX. Możesz też zresetować komputer.

- Wprowadź hasło, aby odblokować ten dysk [] naciśnij klawisz Insert, aby zobaczyć hasło podczas wpisywania.
- Wprowadź klucz odzyskiwania Załaduj klucz odzyskiwania z urządzenia USB.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli maszyna wirtualna nie może zlokalizować pliku klucza odzyskiwania funkcji BitLocker (klucz szyfrowania bloków) w celu odszyfrowania zaszyfrowanego dysku.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, Zatrzymaj i Cofnij przydział maszyny wirtualnej, a następnie uruchom ją. Ta operacja wymusza pobranie przez maszynę wirtualną pliku klucz szyfrowania bloków z Azure Key Vault, a następnie umieszczenie go na zaszyfrowanym dysku. 

Jeśli ta metoda nie rozwiąże problemu, wykonaj następujące kroki, aby ręcznie przywrócić plik klucz szyfrowania bloków:

1. Utwórz migawkę dysku systemowego, której dotyczy dana maszyna wirtualna, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).
2. [Dołącz dysk systemowy do maszyny wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md). Aby uruchomić polecenie [manage-bde](/windows-server/administration/windows-commands/manage-bde) w kroku 7, funkcja **szyfrowanie dysków funkcją BitLocker** musi być włączona na maszynie wirtualnej odzyskiwania.

    Po dołączeniu dysku zarządzanego może zostać wyświetlony komunikat o błędzie "zawiera ustawienia szyfrowania i w związku z tym nie można go użyć jako dysku danych". W takiej sytuacji uruchom następujący skrypt, aby spróbować ponownie dołączyć dysk:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Nie można dołączyć dysku zarządzanego do maszyny wirtualnej, która została przywrócona z obrazu obiektu BLOB.

3. Po dołączeniu dysku należy nawiązać połączenie pulpitu zdalnego z maszyną wirtualną odzyskiwania, aby można było uruchamiać niektóre skrypty Azure PowerShell. Upewnij się, że masz zainstalowaną [najnowszą wersję Azure PowerShell](/powershell/azure/) na maszynie wirtualnej odzyskiwania.

4. Otwórz sesję Azure PowerShell z podwyższonym poziomem uprawnień (Uruchom jako administrator). Uruchom następujące polecenia, aby zalogować się do subskrypcji platformy Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Uruchom następujący skrypt, aby sprawdzić nazwę pliku klucz szyfrowania bloków:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Poniżej znajduje się przykład danych wyjściowych. W tym przypadku Załóżmy, że nazwa pliku to EF7B2F5A-50C6-4637-0001-7F599C12F85C. Klucz szyfrowania bloków.

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Jeśli widzisz dwa zduplikowane woluminy, wolumin, który ma nowszą sygnaturę czasową, jest bieżącym plikiem klucz szyfrowania bloków używanym przez maszynę wirtualną odzyskiwania.

    Jeśli wartość **typu zawartości** jest **opakowana klucz szyfrowania bloków**, przejdź do [scenariuszy klucz szyfrowania klucza (KEK)](#key-encryption-key-scenario).

    Teraz, gdy masz nazwę pliku klucz szyfrowania bloków dla dysku, musisz utworzyć wpis tajny-File-Name. Plik klucz szyfrowania bloków do odblokowania dysku.

6.  Pobierz plik klucz szyfrowania bloków na dysk odzyskiwania. Poniższy przykład zapisuje plik klucz szyfrowania bloków do folderu C:\BEK. Upewnij się, że `C:\BEK\` ścieżka istnieje przed uruchomieniem skryptów.

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Aby odblokować dołączony dysk przy użyciu pliku klucz szyfrowania bloków, uruchom następujące polecenie.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    W tym przykładzie dołączony dysk systemu operacyjnego to dysk F. Upewnij się, że używasz odpowiedniej litery dysku. 

8. Po pomyślnym odblokowaniu dysku przy użyciu klucza klucz szyfrowania bloków należy odłączyć dysk od maszyny wirtualnej odzyskiwania, a następnie ponownie utworzyć maszynę wirtualną przy użyciu tego nowego dysku systemu operacyjnego.

    > [!NOTE]
    > Zamienianie dysku systemu operacyjnego nie jest obsługiwane w przypadku maszyn wirtualnych korzystających z szyfrowania dysków.

9. Jeśli nowa maszyna wirtualna nadal nie może zostać normalnie uruchomiona, spróbuj wykonać jedną z następujących czynności po odblokowaniu dysku:

    - Wstrzymaj ochronę, aby tymczasowo wyłączyć funkcję BitLocker, wykonując następujące czynności:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Całkowicie Odszyfruj dysk. Aby to zrobić, uruchom następujące polecenie:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Scenariusz klucz szyfrowania klucza

W przypadku scenariusza klucz szyfrowania klucza wykonaj następujące czynności:

1. Upewnij się, że konto zalogowanego użytkownika wymaga uprawnienia "niezapakowane" w zasadach dostępu Key Vault w ramach **użytkownika | Uprawnienia klucza | Operacje kryptograficzne | Odpakuj klucz**.
2. Zapisz Poniższy skrypt w. Plik PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Ustaw parametry. Skrypt przetworzy wpis tajny KEK w celu utworzenia klucza klucz szyfrowania bloków, a następnie zapisze go w folderze lokalnym na maszynie wirtualnej odzyskiwania. Jeśli podczas uruchamiania skryptu wystąpią błędy, zapoznaj się z sekcją [Rozwiązywanie problemów z skryptem](#script-troubleshooting) .

4. Po rozpoczęciu skryptu zobaczysz następujące dane wyjściowe:

    Lokalizacja wersji GAC                                                                              
    ---    -------        --------                                                                              
    False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..  False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..

    Po zakończeniu działania skryptu zobaczysz następujące dane wyjściowe:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. Aby odblokować dołączony dysk przy użyciu pliku klucz szyfrowania bloków, uruchom następujące polecenie:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    W tym przykładzie dołączony dysk systemu operacyjnego to dysk F. Upewnij się, że używasz odpowiedniej litery dysku. 

6. Po pomyślnym odblokowaniu dysku przy użyciu klucza klucz szyfrowania bloków należy odłączyć dysk od maszyny wirtualnej odzyskiwania, a następnie ponownie utworzyć maszynę wirtualną przy użyciu tego nowego dysku systemu operacyjnego. 

    > [!NOTE]
    > Zamienianie dysku systemu operacyjnego nie jest obsługiwane w przypadku maszyn wirtualnych korzystających z szyfrowania dysków.

7. Jeśli nowa maszyna wirtualna nadal nie może zostać normalnie uruchomiona, spróbuj wykonać jedną z następujących czynności po odblokowaniu dysku:

    - Wstrzymaj ochronę, aby tymczasowo wyłączyć funkcję BitLocker, uruchamiając następujące polecenie:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Całkowicie Odszyfruj dysk. Aby to zrobić, uruchom następujące polecenie:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Rozwiązywanie problemów z skryptami

**Błąd: nie można załadować pliku lub zestawu**

Ten błąd występuje, ponieważ ścieżki zestawów ADAL są nieprawidłowe. Możesz wyszukać `Az.Accounts` folder, aby znaleźć poprawną ścieżkę.

**Błąd: nie rozpoznano Get-AzKeyVaultSecret lub Get-AzKeyVaultSecret jako nazwy polecenia cmdlet**

Jeśli używasz starego modułu AZ PowerShell, musisz zmienić te dwa polecenia na `Get-AzureKeyVaultSecret` i `Get-AzureKeyVaultSecret` .

**Przykłady parametrów**

| Parametry  | Przykład wartości  |Komentarze   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | Nazwa magazynu kluczy, w którym jest przechowywany klucz |
|$kekName   |klucze   | Nazwa klucza, który jest używany do szyfrowania maszyny wirtualnej|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | Nazwa wpisu tajnego klucza maszyny wirtualnej|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. KLUCZ szyfrowania bloków |Ścieżka do zapisu pliku klucz szyfrowania bloków.|
|$adTenant  |contoso.onmicrosoft.com   | Nazwa FQDN lub identyfikator GUID Azure Active Directory, który hostuje Magazyn kluczy |
