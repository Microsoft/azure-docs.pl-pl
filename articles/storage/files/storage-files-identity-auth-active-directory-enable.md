---
title: Włącz uwierzytelnianie Active Directory za pośrednictwem protokołu SMB dla Azure Files
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB dla udziałów plików platformy Azure za pośrednictwem Active Directory. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: b2dd501344e1ea799db58ea749395aaed05d05f8
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106558"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Włącz lokalne uwierzytelnianie Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure

[Azure Files](storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) przez dwa typy usług domenowych: Azure Active Directory Domain Services (Azure AD DS) i lokalnych Active Directory Domain Services (AD DS) (wersja zapoznawcza). Ten artykuł koncentruje się na nowo wprowadzonej (w wersji zapoznawczej) obsłudze usługi domena usługi Active Directory na potrzeby uwierzytelniania w udziałach plików platformy Azure. Jeśli interesuje Cię włączenie uwierzytelniania za pomocą usługi Azure AD DS (GA) dla udziałów plików platformy Azure, zapoznaj się z [naszym artykułem w temacie](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Udziały plików platformy Azure obsługują tylko uwierzytelnianie dla jednej usługi domeny, Azure Active Directory usługi domeny (Azure AD DS) lub lokalnego Active Directory Domain Services (AD DS). 
>
> Tożsamości AD DS używane do uwierzytelniania udziałów plików platformy Azure muszą być synchronizowane z usługą Azure AD. Synchronizacja skrótów haseł jest opcjonalna. 
> 
> Uwierzytelnianie lokalne AD DS nie obsługuje uwierzytelniania na kontach komputerów utworzonych w programie AD DS. 
> 
> Lokalne uwierzytelnianie AD DS może być obsługiwane tylko dla jednego lasu usługi AD, w którym zarejestrowano konto magazynu. Można uzyskać dostęp tylko do udziałów plików platformy Azure z poświadczeniami AD DS z pojedynczego lasu. Jeśli potrzebujesz dostępu do udziału plików platformy Azure z innego lasu, upewnij się, że skonfigurowano odpowiednie zaufanie lasu, aby uzyskać szczegółowe informacje, zobacz [często zadawane pytania](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) .  
> 
> W przypadku udziałów plików platformy Azure zarządzanych przez Azure File Sync jest obsługiwane uwierzytelnianie AD DS w przypadku dostępu do protokołu SMB oraz trwałości listy ACL.
>
> Azure Files obsługuje uwierzytelnianie Kerberos za pomocą usługi AD z szyfrowaniem RC4-HMAC. Szyfrowanie AES Kerberos nie jest jeszcze obsługiwane.

Po włączeniu AD DS dla udziałów plików platformy Azure za pośrednictwem protokołu SMB komputery przyłączone do AD DS mogą instalować udziały plików platformy Azure przy użyciu istniejących poświadczeń usługi AD. Tę możliwość można włączyć za pomocą środowiska AD DS hostowanego na maszynach Premium lub hostowanych na platformie Azure.

Tożsamości używane do uzyskiwania dostępu do udziałów plików platformy Azure muszą zostać zsynchronizowane z usługą Azure AD w celu wymuszenia uprawnień do pliku na poziomie udziału za pośrednictwem modelu [kontroli dostępu opartego na rolach (RBAC)](../../role-based-access-control/overview.md) . [Listy DACL w stylu systemu Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) dotyczące plików/katalogów przeprowadzonych z istniejących serwerów plików zostaną zachowane i wymuszone. Ta funkcja zapewnia bezproblemową integrację ze środowiskiem AD DS przedsiębiorstwa. Gdy zastąpisz Premium serwery plików z udziałami plików platformy Azure, istniejący użytkownicy mogą uzyskiwać dostęp do udziałów plików platformy Azure od ich bieżących klientów przy użyciu funkcji logowania jednokrotnego, bez wprowadzania żadnych zmian w poświadczeniach w użyciu.  

> [!NOTE]
> Aby ułatwić konfigurację Azure Files uwierzytelniania usługi AD w przypadku niektórych typowych przypadków użycia, opublikowano [dwa filmy wideo](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) z przewodnikiem krok po kroku:
> - Zastępowanie lokalnych serwerów plików Azure Files (łącznie z konfiguracją prywatnego linku do plików i uwierzytelniania w usłudze AD)
> - Używanie Azure Files jako kontenera profilu dla pulpitu wirtualnego systemu Windows (w tym Instalatora uwierzytelniania usługi AD i konfiguracji FsLogix)

## <a name="prerequisites"></a>Wymagania wstępne 

Przed włączeniem AD DS uwierzytelniania dla udziałów plików platformy Azure upewnij się, że zostały spełnione następujące wymagania wstępne: 

- Wybierz lub Utwórz środowisko AD DS i [zsynchronizuj je z usługą Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Tę funkcję można włączyć w nowym lub istniejącym lokalnym środowisku AD DS. Tożsamości używane na potrzeby dostępu muszą być synchronizowane z usługą Azure AD. Dzierżawa usługi Azure AD i udział plików, do których uzyskujesz dostęp, muszą być skojarzone z tą samą subskrypcją. 

    Aby skonfigurować środowisko domeny usługi AD, zapoznaj się z [omówieniem Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Jeśli nie zsynchronizujesz usługi AD z usługą Azure AD, postępuj zgodnie ze wskazówkami w temacie [Azure AD Connect i Azure AD Connect Health plan instalacji](../../active-directory/hybrid/how-to-connect-install-roadmap.md) , aby skonfigurować i skonfigurować Azure AD Connect. 

- Przyłącz do domeny maszynę lokalną lub maszynę wirtualną platformy Azure do AD DS lokalnych. 

    Aby uzyskać dostęp do udziału plików przy użyciu poświadczeń usługi AD z komputera lub maszyny wirtualnej, urządzenie musi zostać przyłączone do domeny, aby AD DS. Aby uzyskać informacje o sposobie przyłączania do domeny, zapoznaj się z tematem aby [przyłączyć komputer do domeny](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Wybierz lub Utwórz konto usługi Azure Storage w [obsługiwanym regionie](#regional-availability). 

    Upewnij się, że konto magazynu zawierające Twoje udziały plików nie zostało już skonfigurowane do uwierzytelniania za pomocą usługi Azure AD DS. Jeśli na koncie magazynu jest włączone uwierzytelnianie Azure Files Azure AD DS, należy je wyłączyć przed zmianą lokalnego AD DS. Oznacza to, że istniejące listy ACL skonfigurowane w środowisku usługi Azure AD DS należy ponownie skonfigurować do prawidłowego wymuszania uprawnień.
    
    Aby uzyskać informacje na temat tworzenia nowego udziału plików, zobacz [Tworzenie udziału plików w Azure Files](storage-how-to-create-file-share.md).
    
    W celu uzyskania optymalnej wydajności zalecamy wdrożenie konta magazynu w tym samym regionie, w którym znajduje się maszyna wirtualna, z której ma być uzyskiwany dostęp do udziału. 

- Sprawdź łączność, instalując udziały plików platformy Azure przy użyciu klucza konta magazynu. 

    Aby sprawdzić, czy Twoje urządzenie i udział plików są prawidłowo skonfigurowane, spróbuj zainstalować [udział plików](storage-how-to-use-files-windows.md) przy użyciu klucza konta magazynu. Jeśli występują problemy z nawiązywaniem połączenia z usługą Azure Files, zapoznaj się z [narzędziem do rozwiązywania problemów opublikowanym pod kątem Azure Files instalowania błędów w systemie Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Udostępniamy również [wskazówki](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) ułatwiające obejście tego problemu, gdy port 445 jest zablokowany. 

## <a name="regional-availability"></a>Dostępność regionalna

Uwierzytelnianie Azure Files z AD DS (wersja zapoznawcza) jest dostępne we [wszystkich regionach publicznych i regionach usługi Azure gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Przed włączeniem AD DS uwierzytelniania za pośrednictwem protokołu SMB dla udziałów plików platformy Azure zaleca się przeczytanie i zakończenie sekcji [wymagania wstępne](#prerequisites) . Wymagania wstępne sprawdzają, czy Twoje środowiska usługi AD, usługi Azure AD i usługi Azure Storage są prawidłowo skonfigurowane. 

Jeśli planujesz włączyć każdą konfigurację sieciową w udziale plików, zalecamy ocenę [zagadnienia](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) dotyczącego sieci i zakończenie pokrewnej konfiguracji przed włączeniem AD DS uwierzytelniania.

Następnie wykonaj poniższe kroki, aby skonfigurować Azure Files na potrzeby uwierzytelniania w usłudze AD: 

1. Włącz uwierzytelnianie Azure Files AD DS na koncie magazynu. 

2. Przypisywanie uprawnień dostępu dla udziału do tożsamości usługi Azure AD (użytkownika, grupy lub nazwy głównej usługi), która jest zsynchronizowana z docelową tożsamością usługi AD. 

3. Skonfiguruj listy ACL za pośrednictwem protokołu SMB dla katalogów i plików. 
 
4. Zainstaluj udział plików platformy Azure na maszynie wirtualnej przyłączonej do AD DS. 

5. Zaktualizuj hasło tożsamości konta magazynu w AD DS.

Na poniższym diagramie przedstawiono kompleksowy przepływ pracy służący do włączania uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB dla udziałów plików platformy Azure. 

![Diagram przepływu pracy usługi AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Uwierzytelnianie AD DS za pośrednictwem protokołu SMB dla udziałów plików platformy Azure jest obsługiwane tylko na maszynach lub maszynach wirtualnych działających w systemach operacyjnych starszych niż Windows 7 lub Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. Włącz uwierzytelnianie usługi AD dla Twojego konta 

Aby włączyć uwierzytelnianie AD DS za pośrednictwem protokołu SMB dla udziałów plików platformy Azure, należy najpierw zarejestrować konto magazynu za pomocą AD DS a następnie ustawić wymagane właściwości domeny na koncie magazynu. Gdy ta funkcja jest włączona na koncie magazynu, ma zastosowanie do wszystkich nowych i istniejących udziałów plików na koncie. Użyj `join-AzStorageAccountForAuth` , aby włączyć tę funkcję. Szczegółowy opis przepływu pracy typu end-to-end można znaleźć w skrypcie w tej sekcji. 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth` Polecenie cmdlet wprowadzi modyfikacje w środowisku usługi AD. Przeczytaj poniższe wyjaśnienie, aby lepiej zrozumieć, co robi, aby upewnić się, że masz odpowiednie uprawnienia do wykonywania polecenia i czy zastosowane zmiany są dostosowane do zasad zgodności i zabezpieczeń. 

`Join-AzStorageAccountForAuth` Polecenie cmdlet przeprowadzi odpowiednik przyłączania do domeny w trybie offline w imieniu wskazanego konta magazynu. Skrypt używa polecenia cmdlet w celu utworzenia konta w domenie usługi AD, [konta komputera](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (domyślnego) lub [konta logowania do usługi](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Jeśli zdecydujesz się to zrobić ręcznie, wybierz konto najlepiej dopasowane do danego środowiska.

Konto AD DS utworzone przez polecenie cmdlet reprezentuje konto magazynu w domenie usługi AD. Jeśli konto AD DS jest tworzone w ramach jednostki organizacyjnej (OU), która wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym okresem ważności hasła. Niepowodzenie aktualizacji hasła konta spowoduje błędy uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby dowiedzieć się, jak zaktualizować hasło, zobacz [Aktualizacja hasła konta AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Za pomocą następującego skryptu można przeprowadzić rejestrację i włączyć funkcję lub, Alternatywnie można wykonać operacje wykonywane ręcznie przez skrypt. Te operacje są opisane w sekcji po skrypcie. Nie musisz wykonywać obu tych czynności.

### <a name="11-script-prerequisites"></a>wymagania wstępne dotyczące skryptu 1,1
- [Pobierz i rozpakuj moduł AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Zainstaluj i wykonaj moduł w urządzeniu przyłączonym do lokalnego AD DS z poświadczeniami AD DS, które mają uprawnienia do tworzenia konta logowania do usługi lub konta komputera w docelowej usłudze AD.
-  Uruchom skrypt przy użyciu lokalnego poświadczenia AD DS, które jest synchronizowane z usługą Azure AD. Poświadczenia lokalnego AD DS muszą mieć uprawnienia właściciela konta magazynu lub roli RBAC współautora.
- Upewnij się, że konto magazynu jest w [obsługiwanym regionie](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1,2 domena dołączania do konta magazynu
Pamiętaj, aby zastąpić wartości symboli zastępczych własnymi parametrami, przed wykonaniem polecenia w programie PowerShell.
> [!IMPORTANT]
> Polecenie cmdlet Join do domeny utworzy konto usługi AD reprezentujące konto magazynu (udział plików) w usłudze AD. Możesz zarejestrować się jako konto komputera lub konto logowania do usługi, aby uzyskać szczegółowe informacje, zobacz [często zadawane pytania](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) . W przypadku kont komputerów istnieje domyślny okres ważności hasła ustawiony w usłudze AD w ciągu 30 dni. Podobnie konto logowania do usługi może mieć ustawiony domyślny okres ważności hasła dla domeny usługi AD lub jednostki organizacyjnej (OU).
> W przypadku obu typów kont zdecydowanie zalecamy sprawdzenie wieku ważności hasła skonfigurowanego w środowisku usługi AD i zaplanowanie [aktualizacji hasła tożsamości konta magazynu w usłudze AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) poniższego konta usługi AD przed maksymalnym okresem ważności hasła. Niepowodzenie aktualizacji hasła konta usługi AD spowoduje błędy uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Można rozważyć [utworzenie nowej jednostki organizacyjnej (OU) usługi AD w usłudze AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) i wyłączenie zasad wygasania haseł na [kontach komputerów](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) lub kontach logowania do usługi. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

Poniższy opis podsumowuje wszystkie akcje wykonywane, gdy polecenie `Join-AzStorageAccountForAuth` cmdlet zostanie wykonane. Te kroki można wykonać ręcznie, jeśli wolisz nie używać polecenia:

> [!NOTE]
> Jeśli `Join-AzStorageAccountForAuth` skrypt został już wykonany powyżej, przejdź do następnej sekcji "1,3 Upewnij się, że funkcja jest włączona". Nie trzeba ponownie wykonywać operacji poniżej.

#### <a name="a-checking-environment"></a>a. Sprawdzanie środowiska

Najpierw skrypt sprawdza Twoje środowisko. W każdym przypadku sprawdza, czy [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) jest zainstalowana i czy powłoka jest wykonywana z uprawnieniami administratora. Następnie sprawdza, czy jest zainstalowany [moduł AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , i instaluje go, jeśli nie jest. Jeśli te testy zakończyły się powodzeniem, sprawdzimy AD DS, aby sprawdzić, czy istnieje [konto komputera](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (domyślne) lub [konto logowania do usługi](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , które zostało już utworzone przy użyciu nazwy SPN/UPN jako "CIFS/konto-magazynu-nazwa-tutaj. plik. Core. Windows. NET". Jeśli konto nie istnieje, zostanie utworzone zgodnie z opisem w sekcji b poniżej.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Ręczne tworzenie tożsamości reprezentującej konto magazynu w usłudze AD

Aby ręcznie utworzyć to konto, Utwórz nowy klucz Kerberos dla konta magazynu za pomocą programu `New-AzStorageAccountKey -KeyName kerb1`. Następnie użyj tego klucza Kerberos jako hasła do konta. Ten klucz jest używany tylko podczas konfiguracji i nie może być używany dla operacji kontroli ani płaszczyzny danych na koncie magazynu.

Po utworzeniu tego klucza Utwórz konto usługi lub komputera w jednostce organizacyjnej. Użyj następującej specyfikacji: SPN: "CIFS/Storage-account-name-tutaj. File. Core. Windows. NET" Password: klucz Kerberos dla konta magazynu.

Jeśli jednostka organizacyjna wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym okresem ważności hasła, aby zapobiec błędom uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby uzyskać szczegółowe informacje [, zobacz Aktualizacja hasła tożsamości konta magazynu w AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) .

Zachowaj identyfikator SID nowo utworzonego konta, który będzie potrzebny do następnego kroku. Utworzona tożsamość, która reprezentuje konto magazynu, nie musi być synchronizowana z usługą Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>d. Włącz funkcję na koncie magazynu

Następnie skrypt włączy tę funkcję na koncie magazynu. Aby ręcznie wykonać tę konfigurację, podaj szczegóły konfiguracji właściwości domeny w poniższym poleceniu, a następnie uruchom je. Identyfikator SID konta magazynu wymagany w poniższym poleceniu jest identyfikatorem SID tożsamości utworzonej w AD DS w [poprzedniej sekcji](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3 Upewnij się, że funkcja jest włączona

Możesz sprawdzić, czy funkcja jest włączona na koncie magazynu, można użyć następującego skryptu:

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

Pomyślnie włączono funkcję na koncie magazynu. Po włączeniu funkcji należy wykonać więcej czynności, aby użyć tej funkcji.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Pomyślnie włączono uwierzytelnianie AD DS za pośrednictwem protokołu SMB i przypisano rolę niestandardową, która zapewnia dostęp do udziału plików platformy Azure z tożsamością AD DS. Aby udzielić dodatkowych użytkownikom dostępu do udziału plików, postępuj zgodnie z instrukcjami podanymi w sekcji [przypisywanie uprawnień dostępu](#2-assign-access-permissions-to-an-identity) do używania tożsamości i [konfigurowania uprawnień NTFS dla protokołu SMB](#3-configure-ntfs-permissions-over-smb) .

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. Zaktualizuj hasło tożsamości konta magazynu w AD DS

Jeśli zarejestrowano AD DS tożsamość/konto reprezentujące Twoje konto magazynu w jednostce organizacyjnej, która wymusza czas wygaśnięcia hasła, należy je obrócić przed maksymalnym okresem ważności hasła. Niepowodzenie aktualizacji hasła konta AD DS spowoduje niepowodzenia uwierzytelniania w celu uzyskania dostępu do udziałów plików platformy Azure.  

Aby wyzwolić rotację haseł, można uruchomić `Update-AzStorageAccountADObjectPassword` polecenie z modułu AzFilesHybrid. Polecenie cmdlet wykonuje akcje podobne do rotacji kluczy konta magazynu. Pobiera drugi klucz Kerberos konta magazynu i używa go do aktualizowania hasła zarejestrowanego konta w AD DS. Następnie generuje ponownie docelowy klucz Kerberos konta magazynu i aktualizuje hasło zarejestrowanego konta w AD DS. To polecenie cmdlet należy uruchomić w lokalnym środowisku przyłączonym do domeny AD DS.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Files i sposobu korzystania z usługi AD za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Omówienie usługi Azure Files uwierzytelniania opartego na tożsamościach na potrzeby dostępu do protokołu SMB](storage-files-active-directory-overview.md)
- [Często zadawane pytania](storage-files-faq.md)
