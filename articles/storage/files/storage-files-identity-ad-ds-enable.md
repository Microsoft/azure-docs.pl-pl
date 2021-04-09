---
title: Włącz uwierzytelnianie AD DS w udziałach plików platformy Azure
description: Dowiedz się, jak włączyć uwierzytelnianie Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń AD DS.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 5ee4481b3151e28d5d37760e486a43adbc194994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553225"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Część 1: Włączanie uwierzytelniania AD DS dla udziałów plików platformy Azure 

Przed włączeniem Active Directory Domain Services (AD DS) należy zapoznać się z [artykułem Omówienie](storage-files-identity-auth-active-directory-enable.md) , aby poznać obsługiwane scenariusze i wymagania.

W tym artykule opisano proces wymagany do włączenia uwierzytelniania Active Directory Domain Services (AD DS) na koncie magazynu. Po włączeniu tej funkcji musisz skonfigurować konto magazynu i AD DS, aby użyć poświadczeń AD DS do uwierzytelniania w udziale plików platformy Azure. Aby włączyć uwierzytelnianie AD DS za pośrednictwem protokołu SMB dla udziałów plików platformy Azure, należy zarejestrować konto magazynu za pomocą AD DS a następnie ustawić wymagane właściwości domeny na koncie magazynu.

Aby zarejestrować konto magazynu za pomocą AD DS, Utwórz konto reprezentujące je w AD DS. Ten proces można traktować tak, jakby jak utworzyć konto reprezentujące lokalny serwer plików systemu Windows w AD DS. Gdy ta funkcja jest włączona na koncie magazynu, ma zastosowanie do wszystkich nowych i istniejących udziałów plików na koncie.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Opcja jeden (zalecane): Użyj modułu AzFilesHybrid PowerShell

Polecenia cmdlet w module AzFilesHybrid PowerShell wprowadzają niezbędne modyfikacje i umożliwiają korzystanie z tej funkcji. Ponieważ niektóre części poleceń cmdlet współdziałają z lokalnym AD DS, wyjaśnimy działanie poleceń cmdlet, aby można było określić, czy zmiany są wyrównane z zasadami zgodności i zabezpieczeń, i upewnij się, że masz odpowiednie uprawnienia do wykonywania poleceń cmdlet. Chociaż zalecamy użycie modułu AzFilesHybrid, jeśli nie można tego zrobić, firma Microsoft udostępnia te kroki, aby można było wykonać je ręcznie.

### <a name="download-azfileshybrid-module"></a>Pobierz moduł AzFilesHybrid

- [Pobierz i rozpakuj moduł AzFilesHybrid (ga module: v 0.2.0 +)](https://github.com/Azure-Samples/azure-files-samples/releases) Należy pamiętać, że szyfrowanie AES 256 Kerberos jest obsługiwane w programie v 0.2.2 lub nowszym. Jeśli włączono funkcję z wersją AzFilesHybrid na 0.2.2 v i chcesz ją zaktualizować w celu obsługi szyfrowania AES 256 Kerberos, zapoznaj się z [tym artykułem](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). 
- Zainstaluj i wykonaj moduł w urządzeniu przyłączonym do lokalnego AD DS z poświadczeniami AD DS, które mają uprawnienia do tworzenia konta logowania do usługi lub konta komputera w docelowej usłudze AD.
-  Uruchom skrypt przy użyciu lokalnego poświadczenia AD DS, które jest synchronizowane z usługą Azure AD. Poświadczenia lokalnego AD DS muszą mieć właściciela konta magazynu lub uprawnienia roli współautor platformy Azure.

### <a name="run-join-azstorageaccountforauth"></a>Uruchom Join-AzStorageAccountForAuth

`Join-AzStorageAccountForAuth`Polecenie cmdlet wykonuje odpowiednik przyłączania do domeny w trybie offline w imieniu określonego konta magazynu. Skrypt używa polecenia cmdlet do utworzenia [konta komputera](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) w domenie usługi AD. Jeśli z jakiegoś powodu nie możesz użyć konta komputera, możesz zmienić skrypt, aby utworzyć [konto logowania do usługi](/windows/win32/ad/about-service-logon-accounts) . Jeśli zdecydujesz się uruchomić polecenie ręcznie, wybierz konto najlepiej dopasowane do danego środowiska.

Konto AD DS utworzone przez polecenie cmdlet reprezentuje konto magazynu. Jeśli konto AD DS jest tworzone w ramach jednostki organizacyjnej (OU), która wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym okresem ważności hasła. Nie można zaktualizować hasła konta przed tą datą spowoduje błędy uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby dowiedzieć się, jak zaktualizować hasło, zobacz [Aktualizacja hasła konta AD DS](storage-files-identity-ad-ds-update-password.md).

Zastąp wartości symboli zastępczych własnymi parametrami poniżej przed wykonaniem polecenia w programie PowerShell.
> [!IMPORTANT]
> Polecenie cmdlet Join do domeny utworzy konto usługi AD reprezentujące konto magazynu (udział plików) w usłudze AD. Możesz zarejestrować się jako konto komputera lub konto logowania do usługi, aby uzyskać szczegółowe informacje, zobacz [często zadawane pytania](./storage-files-faq.md#security-authentication-and-access-control) . W przypadku kont komputerów istnieje domyślny okres ważności hasła ustawiony w usłudze AD w ciągu 30 dni. Podobnie konto logowania do usługi może mieć ustawiony domyślny okres ważności hasła dla domeny usługi AD lub jednostki organizacyjnej (OU).
> W przypadku obu typów kont zalecamy sprawdzenie wieku ważności hasła skonfigurowanego w środowisku usługi AD i zaplanowanie [aktualizacji hasła tożsamości konta](storage-files-identity-ad-ds-update-password.md) usługi AD przed maksymalnym okresem ważności hasła. Można rozważyć [utworzenie nowej jednostki organizacyjnej (OU) usługi AD w usłudze AD](/powershell/module/addsadministration/new-adorganizationalunit) i wyłączenie zasad wygasania haseł na [kontach komputerów](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) lub kontach logowania do usługi. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
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

## <a name="option-2-manually-perform-the-enablement-actions"></a>Opcja 2: Ręczne wykonywanie akcji włączania

Jeśli skrypt został już wykonany `Join-AzStorageAccountForAuth` powyżej, przejdź do sekcji [Potwierdź, że funkcja jest włączona](#confirm-the-feature-is-enabled) . Nie musisz wykonywać następujących czynności ręcznych.

### <a name="checking-environment"></a>Sprawdzanie środowiska

Najpierw należy sprawdzić stan środowiska. W odróżnieniu od tego należy sprawdzić, czy [Active Directory PowerShell](/powershell/module/addsadministration/) jest zainstalowana i czy powłoka jest wykonywana z uprawnieniami administratora. Następnie sprawdź, czy zainstalowano [moduł Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0), i zainstaluj go, jeśli tego nie zrobiono. Po zakończeniu tych sprawdzeń Sprawdź AD DS, aby sprawdzić, czy [konto komputera](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (domyślne) lub [konto logowania do usługi](/windows/win32/ad/about-service-logon-accounts) , które zostało już utworzone przy użyciu nazwy SPN/UPN jako "CIFS/Twoje konto-Storage-Name-tutaj. plik. Core. Windows. NET". Jeśli konto nie istnieje, utwórz je zgodnie z opisem w następnej sekcji.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Ręczne tworzenie tożsamości reprezentującej konto magazynu w usłudze AD

Aby ręcznie utworzyć to konto, Utwórz nowy klucz Kerberos dla konta magazynu. Następnie użyj tego klucza Kerberos jako hasła dla konta za pomocą poleceń cmdlet programu PowerShell poniżej. Ten klucz jest używany tylko podczas instalacji i nie może być używany dla operacji kontroli ani płaszczyzny danych na koncie magazynu. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Po utworzeniu tego klucza Utwórz konto usługi lub komputera w jednostce organizacyjnej. Użyj następującej specyfikacji (Pamiętaj, aby zastąpić przykładowy tekst nazwą konta magazynu):

SPN: "CIFS/Storage-account-name-tutaj. File. Core. Windows. NET" Password: klucz Kerberos dla konta magazynu.

Jeśli jednostka organizacyjna wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym okresem ważności hasła, aby zapobiec błędom uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby uzyskać szczegółowe informacje [, zobacz Aktualizowanie hasła tożsamości konta magazynu w usłudze AD](storage-files-identity-ad-ds-update-password.md) .

Zachowaj identyfikator SID nowo utworzonej tożsamości, który będzie potrzebny do następnego kroku. Utworzona tożsamość, która reprezentuje konto magazynu, nie musi być synchronizowana z usługą Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Włącz funkcję na koncie magazynu

Teraz możesz włączyć tę funkcję na koncie magazynu. Podaj szczegóły konfiguracji właściwości domeny w poniższym poleceniu, a następnie uruchom je. Identyfikator SID konta magazynu wymagany w poniższym poleceniu jest identyfikatorem SID tożsamości utworzonej w AD DS w [poprzedniej sekcji](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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

Można uruchomić polecenie cmdlet Debug-AzStorageAccountAuth, aby przeprowadzić zestaw podstawowych sprawdzeń konfiguracji usługi AD przy użyciu zalogowanego użytkownika usługi AD. To polecenie cmdlet jest obsługiwane w wersji AzFilesHybrid 0.1.2+. Aby uzyskać więcej informacji na temat kontroli wykonanych w tym poleceniu cmdlet, zobacz [nie można zainstalować Azure Files z poświadczeniami usługi AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) w przewodniku rozwiązywania problemów dla systemu Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Potwierdź, że funkcja jest włączona

Możesz sprawdzić, czy funkcja jest włączona na koncie magazynu, za pomocą następującego skryptu:

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

Jeśli to się powiedzie, dane wyjściowe powinny wyglądać następująco:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Następne kroki

Pomyślnie włączono funkcję na koncie magazynu. Aby skorzystać z tej funkcji, należy przypisać uprawnienia na poziomie udziału. Przejdź do następnej sekcji.

[Część druga: przypisywanie uprawnień na poziomie udziału do tożsamości](storage-files-identity-ad-ds-assign-permissions.md)