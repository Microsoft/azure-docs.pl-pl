---
title: Włączanie AD DS uwierzytelniania w udziałach plików platformy Azure
description: Dowiedz się, jak włączyć uwierzytelnianie Active Directory Domain Services przez SMB dla udziałów plików platformy Azure. Maszyny wirtualne z systemem Windows przyłączone do domeny mogą następnie uzyskać dostęp do udziałów plików platformy Azure przy użyciu AD DS poświadczeń.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 7187563824fd7e371d6352510b9ab71c920fc1ef
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835114"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Część 1: włączanie AD DS uwierzytelniania dla udziałów plików platformy Azure 

Przed włączeniem Active Directory Domain Services (AD DS) zapoznaj się z artykułem z [](storage-files-identity-auth-active-directory-enable.md) omówieniem, aby poznać obsługiwane scenariusze i wymagania.

W tym artykule opisano proces wymagany do włączenia Active Directory Domain Services (AD DS) na koncie magazynu. Po włączeniu tej funkcji należy skonfigurować konto magazynu i konto AD DS, aby używać poświadczeń AD DS do uwierzytelniania w udziałach plików platformy Azure. Aby włączyć AD DS za pośrednictwem protokołu SMB dla udziałów plików platformy Azure, należy zarejestrować konto magazynu w usłudze AD DS, a następnie ustawić wymagane właściwości domeny na koncie magazynu.

Aby zarejestrować konto magazynu w AD DS, utwórz konto reprezentujące je w AD DS. Ten proces można myśleć tak, jakby przypominał tworzenie konta reprezentującego lokalnego serwera plików systemu Windows w AD DS. Po włączeniu tej funkcji na koncie magazynu ma ona zastosowanie do wszystkich nowych i istniejących udziałów plików na koncie.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Opcja 1 (zalecana): użyj modułu AzFilesHybrid programu PowerShell

Polecenia cmdlet w module AzFilesHybrid programu PowerShell umożliwiają wprowadzenie niezbędnych modyfikacji i włączenie tej funkcji. Ponieważ niektóre części poleceń cmdlet współdziałają z lokalną usługą AD DS, wyjaśniamy, do czego te polecenia cmdlet mają dostęp, aby określić, czy zmiany są zgodne z zasadami zgodności i zasadami zabezpieczeń, i upewnić się, że masz odpowiednie uprawnienia do wykonywania poleceń cmdlet. Mimo że zalecamy używanie modułu AzFilesHybrid, jeśli nie możesz tego zrobić, zapewniamy kroki, aby można było wykonać je ręcznie.

### <a name="download-azfileshybrid-module"></a>Pobieranie modułu AzFilesHybrid

- [Pobierz i rozpakować moduł AzFilesHybrid (moduł ga ga: v0.2.0+)](https://github.com/Azure-Samples/azure-files-samples/releases) Należy pamiętać, że szyfrowanie Kerberos AES 256 jest obsługiwane w wersji 0.2.2 lub wersji powyżej. Jeśli funkcja została włączona przy użyciu pliku AzFilesHybrid w wersji nej niż 0.2.2 i chcesz zaktualizować usługę do obsługi szyfrowania AES 256 Kerberos, zapoznaj się z [tym artykułem.](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption) 
- Zainstaluj i wykonaj moduł na urządzeniu, które jest przyłączone do domeny lokalnej w usłudze AD DS przy użyciu poświadczeń usługi AD DS, które mają uprawnienia do tworzenia konta logowania do usługi lub konta komputera w docelowej usłudze AD.
-  Uruchom skrypt przy użyciu lokalnego poświadczenia AD DS zsynchronizowanego z usługą Azure AD. Poświadczenie lokalnego AD DS musi mieć uprawnienia właściciela konta magazynu lub współautora roli platformy Azure.

### <a name="run-join-azstorageaccountforauth"></a>Uruchamianie Join-AzStorageAccountForAuth

Polecenie cmdlet wykonuje odpowiednik przyłączenia do domeny w trybie `Join-AzStorageAccountForAuth` offline w imieniu określonego konta magazynu. Skrypt używa polecenia cmdlet do utworzenia konta [komputera w](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) domenie usługi AD. Jeśli z jakiegoś powodu nie można użyć konta komputera, można zmienić skrypt w celu utworzenia konta [logowania do usługi.](/windows/win32/ad/about-service-logon-accounts) Jeśli zdecydujesz się uruchomić polecenie ręcznie, wybierz konto, które najlepiej odpowiada Twojemu środowisku.

Konto AD DS utworzone przez polecenie cmdlet reprezentuje konto magazynu. Jeśli konto AD DS zostało utworzone w ramach jednostki organizacyjnej(OU), która wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym okresem ważności hasła. Niepowodzenie aktualizacji hasła konta przed tym dniem powoduje niepowodzenia uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby dowiedzieć się, jak zaktualizować hasło, zobacz [Aktualizowanie AD DS hasła konta.](storage-files-identity-ad-ds-update-password.md)

Zastąp wartości symboli zastępczych własnymi wartościami w poniższych parametrach przed ich wykonaniem w programie PowerShell.
> [!IMPORTANT]
> Polecenie cmdlet przyłączania do domeny utworzy konto usługi AD do reprezentowania konta magazynu (udziału plików) w u usługi AD. Możesz zarejestrować się jako konto komputera lub konto logowania do usługi. Aby uzyskać szczegółowe informacje, zobacz [Często](./storage-files-faq.md#security-authentication-and-access-control) zadawane pytania. W przypadku kont komputerów domyślny wiek wygaśnięcia hasła w u usługi AD wynosi 30 dni. Podobnie konto logowania do usługi może mieć ustawiony domyślny wiek wygaśnięcia hasła w domenie usługi AD lub jednostce organizacyjnej ( OU).
> W przypadku obu typów kont zalecamy sprawdzenie wieku wygaśnięcia hasła skonfigurowanego [](storage-files-identity-ad-ds-update-password.md) w środowisku usługi AD i zaplanowanie aktualizacji hasła tożsamości konta magazynu dla konta usługi AD przed maksymalnym okresem ważności hasła. Możesz rozważyć utworzenie [nowej jednostki](/powershell/module/addsadministration/new-adorganizationalunit) organizacyjnej usługi AD w usłudze [](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) AD i odpowiednie wyłączenie zasad wygasania haseł na kontach komputerów lub kontach logowania do usługi. 

```PowerShell
# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

# Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
# If you are logging into an Azure environment other than Public (ex. AzureUSGovernment) you will need to specify that.
# See https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-ps
# for more information.
Connect-AzAccount

# Define parameters, $StorageAccountName currently has a maximum limit of 15 characters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

# Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Opcja 2. Ręczne wykonywanie akcji włączania

Jeśli powyższy skrypt został już pomyślnie `Join-AzStorageAccountForAuth` wykonany, przejdź do sekcji [Potwierdzanie, że funkcja jest włączona.](#confirm-the-feature-is-enabled) Nie musisz wykonywać następujących czynności ręcznych.

### <a name="checking-environment"></a>Sprawdzanie środowiska

Najpierw należy sprawdzić stan środowiska. W szczególności należy sprawdzić, czy program [PowerShell](/powershell/module/addsadministration/) usługi Active Directory jest zainstalowany i czy powłoka jest wykonywana z uprawnieniami administratora. Następnie sprawdź, czy zainstalowano [moduł Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0), i zainstaluj go, jeśli tego nie zrobiono. Po zakończeniu tych testów sprawdź, AD DS, czy istnieje konto komputera [](/windows/win32/ad/about-service-logon-accounts) [(domyślne)](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) lub konto logowania do usługi, które zostało już utworzone przy użyciu nazwy SPN/UPN jako "cifs/your-storage-account-name-here.file.core.windows.net". Jeśli konto nie istnieje, utwórz je zgodnie z opisem w poniższej sekcji.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Ręczne tworzenie tożsamości reprezentującej konto magazynu w u usługi AD

Aby ręcznie utworzyć to konto, utwórz nowy klucz protokołu Kerberos dla konta magazynu. Następnie użyj tego klucza protokołu Kerberos jako hasła dla konta przy użyciu poniższych poleceń cmdlet programu PowerShell. Ten klucz jest używany tylko podczas instalacji i nie może być używany do żadnych operacji kontroli ani płaszczyzny danych na koncie magazynu. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Po utworzeniu tego klucza utwórz konto usługi lub komputera w ramach swojej aplikacji OU. Użyj następującej specyfikacji (pamiętaj, aby zastąpić przykładowy tekst nazwą konta magazynu):

NAZWA SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Hasło: klucz protokołu Kerberos dla konta magazynu.

Jeśli twoja nazwa OU wymusza wygaśnięcie hasła, musisz zaktualizować hasło przed maksymalnym okresem ważności hasła, aby zapobiec niepowodzeniu uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby uzyskać szczegółowe informacje, zobacz Aktualizowanie [hasła tożsamości konta magazynu w u usługi AD.](storage-files-identity-ad-ds-update-password.md)

Zachowaj identyfikator SID nowo utworzonej tożsamości. Będzie on potrzebny w następnym kroku. Utworzona tożsamość reprezentująca konto magazynu nie musi być synchronizowana z usługą Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Włączanie funkcji na koncie magazynu

Teraz możesz włączyć tę funkcję na koncie magazynu. Podaj szczegóły konfiguracji właściwości domeny w poniższym poleceniu, a następnie uruchom ją. Identyfikator SID konta magazynu wymagany w poniższym poleceniu to identyfikator SID tożsamości utworzonej w AD DS [w poprzedniej sekcji](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Debugowanie

Możesz uruchomić polecenie cmdlet Debug-AzStorageAccountAuth, aby przeprowadzić zestaw podstawowych kontroli konfiguracji usługi AD z zalogowanym użytkownikiem usługi AD. To polecenie cmdlet jest obsługiwane w wersji AzFilesHybrid 0.1.2+. Aby uzyskać więcej informacji na temat kontroli wykonywanych w tym polecenie cmdlet, zobacz nie można zainstalować Azure Files przy użyciu poświadczeń [usługi AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) w przewodniku rozwiązywania problemów dla systemu Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Potwierdzanie, że funkcja jest włączona

Aby sprawdzić, czy funkcja jest włączona na koncie magazynu, możesz użyć następującego skryptu:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Jeśli to się powiedzie, dane wyjściowe powinny wyglądać tak:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Następne kroki

Funkcja została pomyślnie włączona na koncie magazynu. Aby korzystać z tej funkcji, musisz przypisać uprawnienia na poziomie udziału. Przejdź do następnej sekcji.

[Część 2. Przypisywanie uprawnień na poziomie udziału do tożsamości](storage-files-identity-ad-ds-assign-permissions.md)