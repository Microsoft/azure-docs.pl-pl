---
title: Rozwiązywanie problemów z usługą Azure Files w systemie Windows
description: Rozwiązywanie Azure Files problemów w systemie Windows. Zobacz typowe problemy związane z Azure Files podczas nawiązywania połączenia z klientów systemu Windows i zobacz możliwe rozwiązania. Tylko w przypadku udziałów SMB
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 115c083a75adab96e416fc200bf7db287a99ff4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788425"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Rozwiązywanie Azure Files problemów w systemie Windows (SMB)

W tym artykule wymieniono typowe problemy związane z usługą Microsoft Azure Files podczas nawiązywania połączenia z klientów systemu Windows. Udostępnia również możliwe przyczyny i rozwiązania tych problemów. Oprócz kroków rozwiązywania problemów w tym artykule można również użyć narzędzia [AzFileDiagnostics,](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) aby upewnić się, że środowisko klienta systemu Windows ma poprawne wymagania wstępne. Diagnostyka AzFileDiagnostics automatyzuje wykrywanie większości objawów wymienionych w tym artykule i pomaga skonfigurować środowisko w celu uzyskania optymalnej wydajności.

> [!IMPORTANT]
> Zawartość tego artykułu dotyczy tylko udziałów SMB. Aby uzyskać szczegółowe informacje na temat udziałów NFS, zobacz Troubleshoot Azure NFS file shares (Rozwiązywanie problemów z udziałami [plików NFS platformy Azure).](storage-troubleshooting-files-nfs.md)

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Błąd 5 podczas instalacji udziału plików platformy Azure

Podczas próby instalacji udziału plików może zostać wyświetlony następujący błąd:

- Wystąpił błąd systemowy 5. Odmowa dostępu.

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyna 1. Nieszyfrowany kanał komunikacyjny

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli kanał komunikacyjny nie jest szyfrowany i jeśli próba połączenia nie pochodzi z tego samego centrum danych, w którym znajdują się udziały plików platformy Azure. Nieszyfrowane połączenia w tym samym centrum danych też mogą być blokowane, jeśli ustawienie [Wymagany bezpieczny transfer](../common/storage-require-secure-transfer.md) jest włączone na koncie magazynu. Szyfrowany kanał komunikacyjny jest udostępniany tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Systemy Windows 8, Windows Server 2012 i nowsze wersje negocjują żądania obejmujące protokół SMB 3.0, który obsługuje szyfrowanie.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Nawiąż połączenie z klienta, który obsługuje szyfrowanie SMB (Windows 8, Windows Server 2012 lub nowszy) lub połącz się z maszyny wirtualnej w tym samym centrum danych co konto usługi Azure Storage używane na dla udziału plików platformy Azure.
2. Sprawdź, [czy ustawienie Wymagany bezpieczny transfer](../common/storage-require-secure-transfer.md) jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 2. Reguły sieci wirtualnej lub zapory są włączone na koncie magazynu 

Jeśli reguły sieci wirtualnej i zapory są skonfigurowane na koncie magazynu, dla ruchu sieciowego będzie następować odmowa dostępu, chyba że dostęp będzie dozwolony dla adresu IP klienta lub sieci wirtualnej.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Przyczyna 3: Uprawnienia na poziomie udziału są nieprawidłowe w przypadku korzystania z uwierzytelniania opartego na tożsamościach

Jeśli użytkownicy mają dostęp do udziału plików platformy Azure przy użyciu uwierzytelniania usługi Active Directory (AD) lub Azure Active Directory Domain Services (Azure AD DS), dostęp do udziału plików nie powiedzie się z błędem "Odmowa dostępu", jeśli uprawnienia na poziomie udziału są nieprawidłowe. 

### <a name="solution-for-cause-3"></a>Rozwiązanie dotyczące przyczyny 3

Sprawdź, czy uprawnienia są prawidłowo skonfigurowane:

- **Active Directory (AD) — zobacz** [Przypisywanie uprawnień na poziomie udziału do tożsamości](./storage-files-identity-ad-ds-assign-permissions.md).

    Przypisania uprawnień na poziomie udziału są obsługiwane w przypadku grup i użytkowników, które zostały zsynchronizowane z usługi Active Directory (AD) do usługi Azure Active Directory (Azure AD) przy użyciu Azure AD Connect.  Upewnij się, że grupy i użytkownicy, do których przypisano uprawnienia na poziomie udziału, nie są nieobsługiwanymi grupami "tylko w chmurze".
- **Azure Active Directory Domain Services (Azure AD DS) zobacz** [Przypisywanie uprawnień dostępu do tożsamości.](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity)

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Błąd 53, błąd 67 lub błąd 87 podczas instalowania lub odinstalowania udziału plików platformy Azure

Podczas próby instalacji udziału plików ze źródła lokalnego lub z innego centrum danych mogą wystąpić następujące błędy:

- Wystąpił błąd systemowy 53. Nie można znaleźć ścieżki sieciowej.
- Wystąpił błąd systemowy 67. Nie można znaleźć nazwy sieciowej.
- Wystąpił błąd systemowy 87. Parametr jest nieprawidłowy.

### <a name="cause-1-port-445-is-blocked"></a>Przyczyna 1: Port 445 jest zablokowany

Błąd systemowy 53 lub błąd systemu 67 może wystąpić, jeśli komunikacja wychodząca na porcie 445 Azure Files centrum danych jest zablokowana. Aby zobaczyć podsumowanie usługodawców internetowych, którzy nie zezwalają na dostęp z portu 445, przejdź do witryny [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby sprawdzić, czy zapora lub isP blokuje port 445, użyj [narzędzia AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) lub `Test-NetConnection` polecenia cmdlet. 

Aby można było użyć polecenia cmdlet , Azure PowerShell moduł musi być zainstalowany. Aby uzyskać więcej informacji, Azure PowerShell `Test-NetConnection` [zainstalować](/powershell/azure/install-Az-ps) moduł. Pamiętaj, aby zastąpić wyrażenia `<your-storage-account-name>` i `<your-resource-group-name>` nazwami odpowiednimi dla konta magazynu.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
Jeśli połączenie zostało pomyślnie nawiązane, powinny pojawić się następujące dane wyjściowe:
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> Powyższe polecenie zwraca bieżący adres IP konta magazynu. Nie ma żadnej gwarancji, że ten adres IP pozostanie niezmieniony. Może on ulec zmianie w dowolnym momencie. Nie umieszczaj tego adresu IP w żadnym kodzie skryptu ani konfiguracji zapory.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

#### <a name="solution-1---use-azure-file-sync"></a>Rozwiązanie 1 — użycie usługi Azure File Sync
Azure File Sync przekształcić lokalną platformę Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Usługa Azure File Sync działa przez port 443 i może służyć jako obejście w celu uzyskania dostępu do usługi Azure Files z klientów, którzy mają zablokowany port 445. [Dowiedz się, jak skonfigurować Azure File Sync](../file-sync/file-sync-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Rozwiązanie 2 — użycie sieci VPN
Po skonfigurowaniu sieci VPN na określonym koncie magazynu ruch będzie przechodzić przez bezpieczny tunel, a nie przez Internet. Postępuj zgodnie z [instrukcjami konfigurowania sieci VPN](storage-files-configure-p2s-vpn-windows.md), aby uzyskać dostęp do usługi Azure Files z systemu Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Rozwiązanie 3 — odblokowanie portu 445 przy pomocy usługodawcy internetowego/administratora IT
We współpracy z działem IT lub isP otwórz port 445 dla ruchu wychodzącego do [zakresów adresów IP platformy Azure.](https://www.microsoft.com/download/details.aspx?id=41653)

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Rozwiązanie 4 — użycie narzędzi opartych na interfejsie API REST, takich jak Eksplorator usługi Storage/Powershell
Azure Files obsługuje również rest (rest) oprócz SMB. Dostęp do funkcji REST działa przez port 443 (standardowy protokół TCP). Istnieją różne narzędzia, które są napisane przy użyciu interfejsu API REST, które oferują rozbudowany interfejs użytkownika. [Eksplorator usługi Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) jest jedną z nich. [Pobierz i zainstaluj Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) i połącz się ze swoim udziałem plików obsługiwanym przez usługę Azure Files. Możesz również użyć programu [PowerShell, który](./storage-how-to-use-files-powershell.md) również korzysta z interfejsu API REST użytkownika.

### <a name="cause-2-ntlmv1-is-enabled"></a>Przyczyna 2: Włączono NTLMv1

Błąd systemowy 53 lub błąd systemu 87 może wystąpić, jeśli komunikacja NTLMv1 jest włączona na kliencie. Usługa Azure Files obsługuje tylko uwierzytelnianie NTLMv2. Włączenie komunikacji NTLMv1 powoduje, że klient jest mniej bezpieczny. Dlatego komunikacja jest blokowana dla usługi Azure Files. 

Aby ustalić, czy jest to przyczyna błędu, sprawdź, czy następujący podklucz rejestru nie jest ustawiony na wartość mniejszą niż 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Aby uzyskać więcej informacji, zobacz temat [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) w witrynie TechNet.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przywróć wartość **LmCompatibilityLevel** do wartości domyślnej równej 3 w następującym podkluczu rejestru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Błąd 1816 — Za mało dostępnego limitu przydziału do przetwarzania tego polecenia

### <a name="cause"></a>Przyczyna

Błąd 1816 występuje, gdy osiągniesz górny limit współbieżnych otwartych dojść, które są dozwolone dla pliku lub katalogu w udziałach plików platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Files scale targets (Cele skalowania usługi Azure Files)](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę współbieżnych otwartych dojść, zamykając niektóre dojścia, a następnie ponów próbę. Aby uzyskać więcej informacji, [zobacz Microsoft Azure Storage listy kontrolnej dotyczącej wydajności i skalowalności.](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

Aby wyświetlić otwarte dojścia do udziału plików, katalogu lub pliku, użyj polecenia cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) programu PowerShell.  

Aby zamknąć otwarte dojścia do udziału plików, katalogu lub pliku, użyj polecenia cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) programu PowerShell.

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w module Az programu PowerShell w wersji 2.4 lub nowszej. Aby zainstalować najnowszy moduł Az programu PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Błąd "Brak dostępu" podczas próby uzyskania dostępu do udziału plików platformy Azure lub usunięcia go  
Podczas próby uzyskania dostępu do udziału plików platformy Azure lub usunięcia go w portalu może zostać wyświetlony następujący błąd:

Brak dostępu  
Kod błędu: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 1: Reguły sieci wirtualnej lub zapory są włączone na koncie magazynu

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Przyczyna 2: Twoje konto użytkownika nie ma dostępu do konta magazynu

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przejdź do konta magazynu, na którym znajduje się udział plików platformy Azure, kliknij pozycję Kontrola dostępu **(IAM)** i sprawdź, czy twoje konto użytkownika ma dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [How to secure your storage account with Azure role-based access control (Azure RBAC) (Jak](../blobs/security-recommendations.md#data-protection)zabezpieczyć konto magazynu przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure).

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Nie można zmodyfikować, przenieść/zmienić nazwy lub usunąć pliku lub katalogu
Jednym z kluczowych celów udziału plików jest to, że wielu użytkowników i aplikacje mogą jednocześnie wchodzić w interakcje z plikami i katalogami w tym udziału. Aby pomóc w tej interakcji, udziały plików zapewniają kilka sposobów uzyskiwania dostępu do plików i katalogów.

Po otwarciu pliku z zainstalowanego udziału plików platformy Azure za pośrednictwem SMB aplikacja/system operacyjny żąda dojścia do pliku, które jest odwołaniem do pliku. Między innymi aplikacja określa tryb udostępniania plików, gdy żąda dojścia do pliku, który określa poziom wyłączności dostępu do pliku wymuszany przez Azure Files: 

- `None`: masz wyłączny dostęp. 
- `Read`: inne osoby mogą odczytać plik po jego otwarciu.
- `Write`: inne osoby mogą zapisywać w pliku, gdy jest otwarty. 
- `ReadWrite`: kombinacja trybów udostępniania `Read` `Write` i .
- `Delete`: inne osoby mogą usunąć plik po jego otwarciu. 

Mimo że jako protokół bez stanowy, protokół FileREST nie ma koncepcji dojść do plików, zapewnia podobny mechanizm do obsługi dostępu do plików i folderów, których skrypt, aplikacja lub usługa może używać: dzierżawy plików. Gdy plik jest dzierżawiony, jest traktowany jako odpowiednik dojścia do pliku z trybem udostępniania plików `None` . 

Chociaż dojścia do plików i dzierżawy pełnią ważną rolę, czasami dojścia plików i dzierżawy mogą być oddzielone. W takim przypadku może to spowodować problemy z modyfikowaniem lub usuwaniem plików. Mogą pojawić się komunikaty o błędach, takie jak:

- Proces nie może uzyskać dostępu do pliku, ponieważ jest on używany przez inny proces.
- Nie można ukończyć akcji, ponieważ plik jest otwarty w innym programie.
- Dokument jest zablokowany do edycji przez innego użytkownika.
- Określony zasób jest oznaczony do usunięcia przez klienta SMB.

Rozwiązanie tego problemu zależy od tego, czy jest to spowodowane przez oddzielone dojście do pliku, czy dzierżawę. 

### <a name="cause-1"></a>Przyczyna 1
Dojście do pliku uniemożliwia zmodyfikowanie lub usunięcie pliku/katalogu. Aby wyświetlić otwarte dojścia, możesz użyć polecenia cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) programu PowerShell. 

Jeśli wszyscy klienci SMB zamknęli otwarte dojścia do pliku/katalogu, a problem nadal występuje, można wymusić zamknięcie dojścia do pliku.

### <a name="solution-1"></a>Rozwiązanie 1
Aby wymusić zamknięcie dojścia do pliku, użyj polecenia cmdlet [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) programu PowerShell. 

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w module Az programu PowerShell w wersji 2.4 lub nowszej. Aby zainstalować najnowszy moduł Az programu PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>Przyczyna 2
Dzierżawa pliku uniemożliwia zmodyfikowanie lub usunięcie pliku. Możesz sprawdzić, czy plik ma dzierżawę pliku przy użyciu następującego programu PowerShell, zastępując wartości , , i odpowiednimi `<resource-group>` `<storage-account>` `<file-share>` `<path-to-file>` wartościami dla środowiska:

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

Jeśli plik ma dzierżawę, zwracany obiekt powinien zawierać następujące właściwości:

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>Rozwiązanie 2
Aby usunąć dzierżawę z pliku, możesz zwolnić dzierżawę lub ją przerwać. Aby zwolnić dzierżawę, potrzebny jest identyfikator LeaseId dzierżawy, który jest ustawiany podczas jej tworzenia. Nie potrzebujesz identyfikatora LeaseId, aby przerwać dzierżawę.

W poniższym przykładzie pokazano, jak przerwać dzierżawę dla pliku wskazanego w przyczynie 2 (w tym przykładzie są kontynuowane zmienne programu PowerShell z przyczyny 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Slow file copying to and from Azure Files in Windows (Wolne kopiowanie plików do i z usługi Azure Files w systemie Windows)

Podczas próby transferu plików do usługi plików platformy Azure wydajność może być niska.

- Jeśli nie masz określonego minimalnego rozmiaru we/wy, zalecamy użycie rozmiaru 1 MiB jako rozmiaru we/wy w celu uzyskania optymalnej wydajności.
-   Jeśli znasz ostateczny rozmiar pliku, który rozszerzasz za pomocą zapisu, a twoje oprogramowanie nie ma problemów ze zgodnością, gdy niepisany koniec pliku zawiera zera, ustaw rozmiar pliku z wyprzedzeniem, zamiast każdego zapisu rozszerzać zapis.
-   Użyj odpowiedniej metody kopiowania:
    -   Użyj [programu AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) do dowolnego transferu między dwoma udziałami plików.
    -   Używanie [programu Robocopy](./storage-how-to-create-file-share.md) między udziałami plików na komputerze lokalnym.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Zagadnienia dotyczące Windows 8.1 systemu Windows Server 2012 R2

W przypadku klientów z systemem Windows 8.1 lub Windows Server 2012 R2 upewnij się, że zainstalowano poprawkę [KB3114025.](https://support.microsoft.com/help/3114025) Ta poprawka zwiększa wydajność tworzenia i zamykania dojść.

Możesz uruchomić następujący skrypt, aby sprawdzić, czy poprawka została zainstalowana:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Jeśli jest zainstalowana poprawka, zostaną wyświetlone następujące dane wyjściowe:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Obrazy systemu Windows Server 2012 R2 w programie Azure Marketplace mają domyślnie zainstalowaną poprawkę KB3114025, począwszy od grudnia 2015 r.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Brak folderu z literą dysku w folderze "Mój komputer" lub "Ten komputer"

Jeśli zamapujesz udział plików platformy Azure jako administrator przy użyciu narzędzia net use, udział wydaje się brakować.

### <a name="cause"></a>Przyczyna

Domyślnie system Windows Eksplorator plików nie działa jako administrator. Jeśli polecenie net use jest uruchamiane z administracyjnego wiersza polecenia, dysk sieciowy jest mapowy jako administrator. Ponieważ zamapowane dyski są zorientowane na użytkownika, zalogowane konto użytkownika nie wyświetla dysków, jeśli są one zainstalowane w ramach innego konta użytkownika.

### <a name="solution"></a>Rozwiązanie
Zainstaluj udział z wiersza polecenia bez uprawnień administratora. Alternatywnie możesz użyć tego [tematu w witrynie TechNet,](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) aby skonfigurować wartość rejestru **EnableLinkedConnections.**

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Polecenie Net Use kończy się niepowodzeniem, jeśli konto magazynu zawiera ukośnik

### <a name="cause"></a>Przyczyna

Net Use polecenie interpretuje ukośnik (/) jako opcję wiersza polecenia. Jeśli nazwa konta użytkownika rozpoczyna się ukośnikiem, mapowanie dysku kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, możesz wykonać jedną z następujących czynności:

- Uruchom następujące polecenie programu PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  Z pliku wsadowego można uruchomić polecenie w ten sposób:

  `Echo new-smbMapping ... | powershell -command –`

- Umieść podwójne cudzysłowy wokół klucza, aby omiń ten problem — chyba że ukośnik jest pierwszym znakiem. Jeśli tak jest, użyj trybu interaktywnego i wprowadź hasło oddzielnie lub ponownie wygeneruj klucze, aby uzyskać klucz, który nie rozpoczyna się ukośnikiem.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikacja lub usługa nie może uzyskać dostępu do Azure Files dysku

### <a name="cause"></a>Przyczyna

Dyski są instalowane na użytkownika. Jeśli aplikacja lub usługa jest uruchomiona na innym koncie użytkownika niż konto, na które został zainstalowany dysk, aplikacja nie będzie widzieć dysku.

### <a name="solution"></a>Rozwiązanie

Użyj jednego z następujących rozwiązań:

-   Zainstaluj dysk z tego samego konta użytkownika, które zawiera aplikację. Możesz użyć narzędzia, takiego jak PsExec.
- Przekaż nazwę i klucz konta magazynu w parametrach nazwy użytkownika i hasła polecenia net use.
- Użyj polecenia cmdkey, aby dodać poświadczenia do Menedżer poświadczeń. Wykonaj tę czynności z wiersza polecenia w kontekście konta usługi za pomocą logowania interakcyjnego lub za pomocą polecenia `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapuj udział bezpośrednio bez użycia litery dysku zmapowanych. Niektóre aplikacje mogą nie ponownie nawiązać prawidłowego połączenia z literą dysku, więc użycie pełnej ścieżki UNC może być bardziej niezawodne. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po zakończeniu tych instrukcji podczas uruchamiania konta usługi systemowej/sieciowej może zostać wyświetlony następujący komunikat o błędzie: "Wystąpił błąd systemu 1312. Określona sesja logowania nie istnieje. Być może została ona już zakończona". W takim przypadku upewnij się, że nazwa użytkownika przekazywana do net use zawiera informacje o domenie (na przykład: "[nazwa konta magazynu].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Błąd "Kopiujesz plik do miejsca docelowego, które nie obsługuje szyfrowania"

Gdy plik jest kopiowany przez sieć, plik jest odszyfrowywany na komputerze źródłowym, przesyłany w postaci zwykłego tekstu i ponownie szyfrowany w miejscu docelowym. Jednak podczas próby skopiowania zaszyfrowanego pliku może zostać wyświetlony następujący błąd: "Kopiujesz plik do miejsca docelowego, które nie obsługuje szyfrowania".

### <a name="cause"></a>Przyczyna
Ten problem może wystąpić, jeśli używasz systemu system szyfrowania plików (EFS). Pliki zaszyfrowane za pomocą funkcji BitLocker można kopiować do Azure Files. Jednak Azure Files systemu plików NTFS EFS.

### <a name="workaround"></a>Obejście
Aby skopiować plik za pośrednictwem sieci, należy najpierw go odszyfrować. Użyj jednej z następujących metod:

- Użyj polecenia **copy /d.** Umożliwia zapisanie zaszyfrowanych plików jako odszyfrowanych plików w miejscu docelowym.
- Ustaw poniższy klucz rejestru:
  - Ścieżka = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ wartości = DWORD
  - Nazwa: CopyFileAllowDecryptedRemoteDestination
  - Wartość: 1

Należy pamiętać, że ustawienie klucza rejestru ma wpływ na wszystkie operacje kopiowania, które są wykonywane w udziałach sieciowych.

## <a name="slow-enumeration-of-files-and-folders"></a>Powolne wyliczanie plików i folderów

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli na komputerze klienckim nie ma wystarczającej pamięci podręcznej dla dużych katalogów.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy dostosować wartość rejestru **DirectoryCacheEntrySizeMax** w celu umożliwienia buforowania większych list katalogów na komputerze klienckim:

- Lokalizacja: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Mane wartości: DirectoryCacheEntrySizeMax 
- Typ wartości:DWORD
 
 
Można na przykład ustawić ją na wartość 0x100000 sprawdzić, czy wydajność się poprawi.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Błąd AadDsTenantNotFound podczas włączania uwierzytelniania usługi Azure Active Directory Domain Service (Azure AD DS) dla usługi Azure Files "Nie można zlokalizować aktywnych dzierżaw z identyfikatorem dzierżawy aad-tenant-id"

### <a name="cause"></a>Przyczyna

Błąd AadDsTenantNotFound występuje podczas próby włączenia uwierzytelniania usługi [Azure Active Directory Domain Services (Azure AD DS Azure Files)](storage-files-identity-auth-active-directory-domain-service-enable.md) na koncie magazynu, na którym usługa [Azure AD Domain Service(Azure AD DS)](../../active-directory-domain-services/overview.md) nie jest tworzona w dzierżawie usługi Azure AD skojarzonej subskrypcji.  

### <a name="solution"></a>Rozwiązanie

Włącz Azure AD DS dzierżawie usługi Azure AD subskrypcji, w ramach których wdrożono konto magazynu. Aby utworzyć domenę zarządzaną, potrzebne są uprawnienia administratora dzierżawy usługi Azure AD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby utworzyć i skonfigurować Azure Active Directory Domain Services [zarządzaną.](../../active-directory-domain-services/tutorial-create-instance.md)

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Nie można zainstalować Azure Files przy użyciu poświadczeń usługi AD 

### <a name="self-diagnostics-steps"></a>Kroki samodzielnej diagnostyki
Najpierw upewnij się, że zostały one opisane we wszystkich czterech krokach, aby włączyć uwierzytelnianie [Azure Files AD.](./storage-files-identity-auth-active-directory-enable.md)

Następnie spróbuj ponownie [schować udział plików platformy Azure przy użyciu klucza konta magazynu.](./storage-how-to-use-files-windows.md) Jeśli nie można zainstalować programu , pobierz program [AzFileDiagnostics.ps1, ](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) aby ułatwić weryfikację uruchomionego środowiska klienta, wykrywanie niezgodnej konfiguracji klienta, która może spowodować niepowodzenie dostępu dla programu Azure Files, zapewnia nakazowe wskazówki dotyczące samodzielnej naprawy i zbierania śladów diagnostycznych.

Po trzecie można uruchomić polecenie cmdlet Debug-AzStorageAccountAuth, aby przeprowadzić zestaw podstawowych kontroli konfiguracji usługi AD z zalogowanym użytkownikiem usługi AD. To polecenie cmdlet jest obsługiwane w [wersji AzFilesHybrid 0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases). Należy uruchomić to polecenie cmdlet z użytkownikiem usługi AD, który ma uprawnienia właściciela do docelowego konta magazynu.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Polecenie cmdlet wykonuje poniższe testy kolejno i zapewnia wskazówki dotyczące błędów:
1. CheckADObjectPasswordIsCorrect: upewnij się, że hasło skonfigurowane w tożsamości usługi AD reprezentującej konto magazynu jest zgodne z hasłem konta magazynu kerb1 lub kerb2. Jeśli hasło jest nieprawidłowe, możesz uruchomić program [Update-AzStorageAccountADObjectPassword,](./storage-files-identity-ad-ds-update-password.md) aby zresetować hasło. 
2. CheckADObject: Upewnij się, że w usłudze Active Directory znajduje się obiekt reprezentujący konto magazynu i ma poprawną nazwę SPN (główną nazwę usługi). Jeśli nie skonfigurowano poprawnie tej usługi, uruchom polecenie cmdlet Set-AD zwrócone w poleceniach cmdlet debugowania, aby skonfigurować jej.
3. CheckDomainJoined: sprawdź, czy komputer kliencki jest przyłączony do domeny usługi AD. Jeśli komputer nie jest przyłączony do domeny usługi AD, zapoznaj się z tym artykułem, [aby](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) uzyskać instrukcje dotyczące dołączania do domeny.
4. CheckPort445Connectivity: Sprawdź, czy port 445 jest otwarty dla połączenia SMB. Jeśli wymagany port nie jest otwarty, zapoznaj się z narzędziem do rozwiązywania problemów [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) problemów z łącznością z Azure Files.
5. CheckSidHasAadUser: sprawdź, czy zalogowany użytkownik usługi AD jest zsynchronizowany z usługą Azure AD. Jeśli chcesz sprawdzić, czy określony użytkownik usługi AD jest synchronizowany z usługą Azure AD, możesz określić parametry -UserName i -Domain w parametrach wejściowych. 
6. CheckGetKerberosTicket: spróbuj uzyskać bilet protokołu Kerberos w celu nawiązania połączenia z kontem magazynu. Jeśli nie ma prawidłowego tokenu protokołu Kerberos, uruchom polecenie cmdlet cifs/storage-account-name.file.core.windows.net i sprawdź kod błędu, aby uzyskać główną przyczynę niepowodzenia pobierania biletu.
7. CheckStorageAccountDomainJoined: sprawdź, czy uwierzytelnianie usługi AD zostało włączone, a właściwości usługi AD konta są wypełnione. Jeśli nie, zapoznaj się z instrukcjami w tym [miejscu,](./storage-files-identity-ad-ds-enable.md) aby włączyć AD DS na Azure Files. 
8. CheckUserRbacAssignment: sprawdź, czy użytkownik usługi AD ma odpowiednie przypisanie roli RBAC w celu zapewnienia uprawnień na poziomie udziału w celu uzyskania dostępu do Azure Files. Jeśli nie, zapoznaj się z instrukcjami [w tym miejscu,](./storage-files-identity-ad-ds-assign-permissions.md) aby skonfigurować uprawnienia na poziomie udziału. (Obsługiwane w programie AzFilesHybrid w wersji 0.2.3 lub nowsza)
9. CheckUserFileAccess: sprawdź, czy użytkownik usługi AD ma odpowiednie uprawnienia katalogu/pliku (ACL) systemu Windows, aby uzyskać dostęp do Azure Files. Jeśli nie, zapoznaj się z instrukcjami w tym [miejscu,](./storage-files-identity-ad-ds-configure-permissions.md) aby skonfigurować uprawnienia na poziomie katalogu/pliku. (Obsługiwane w programie AzFilesHybrid w wersji 0.2.3 lub nowsza)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Nie można skonfigurować uprawnień na poziomie katalogu/pliku (listy ACL systemu Windows) w systemie Windows Eksplorator plików

### <a name="symptom"></a>Objaw

Podczas próby skonfigurowania listy ACL systemu Windows przy użyciu Eksplorator plików zainstalowanego udziału plików mogą wystąpić objawy opisane poniżej:
- Po kliknięciu uprawnienia Edytuj w obszarze karta Zabezpieczenia nie zostanie załadowany kreator uprawnień. 
- Podczas próby wybrania nowego użytkownika lub grupy lokalizacja domeny nie wyświetla właściwej AD DS domeny. 

### <a name="solution"></a>Rozwiązanie

Zalecamy użycie narzędzia [icacls w](/windows-server/administration/windows-commands/icacls) celu skonfigurowania uprawnień na poziomie katalogu/pliku jako obejścia. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Błędy podczas uruchamiania Join-AzStorageAccountForAuth cmdlet

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Błąd: "Usługa katalogowa nie mogła przydzielić identyfikatora względnego"

Ten błąd może wystąpić, jeśli kontroler domeny, który zawiera rolę FSMO głównego serwera RID, jest niedostępny lub został usunięty z domeny i przywrócony z kopii zapasowej.  Upewnij się, że wszystkie kontrolery domeny są uruchomione i dostępne.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Błąd: „Nie można powiązać parametrów pozycyjnych, ponieważ nie podano nazw”

Ten błąd jest najprawdopodobniej wyzwalany przez błąd składniowy w poleceniu Join-AzStorageAccountforAuth.  Sprawdź, czy w poleceniu występują błędy pisowni lub błędy składni i czy zainstalowano najnowszą wersję modułu AzFilesHybrid ( https://github.com/Azure-Samples/azure-files-samples/releases) .  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files lokalnego uwierzytelniania AD DS dla szyfrowania Kerberos AES 256

Wprowadziliśmy obsługę szyfrowania Kerberos AES 256 na Azure Files uwierzytelniania AD DS przy użyciu modułu [AzFilesHybrid w wersji 0.2.2.](https://github.com/Azure-Samples/azure-files-samples/releases) Jeśli włączono uwierzytelnianie usługi AD DS przy użyciu modułu w wersji niższej niż 0.2.2, musisz pobrać najnowszy moduł AzFilesHybrid (wersja 0.2.2 lub nowsza) i uruchomić poniższy program PowerShell. Jeśli nie włączono jeszcze AD DS na koncie magazynu, możesz wykonać te [wskazówki](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) dotyczące włączania. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, skontaktuj [się z pomocą techniczną,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) aby szybko rozwiązać problem.
