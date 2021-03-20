---
title: Rozwiązywanie problemów z usługą Azure Files w systemie Windows
description: Rozwiązywanie problemów z Azure Files w systemie Windows. Zobacz typowe problemy związane z Azure Files podczas łączenia się z klientami z systemem Windows i zobaczenie możliwych rozwiązań. Tylko dla udziałów SMB
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 242c0819e916f3ea7912d4d57b7d3e338152e4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878514"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Rozwiązywanie problemów z Azure Files w systemie Windows (SMB)

W tym artykule wymieniono typowe problemy związane z Microsoft Azure plikami w przypadku łączenia się z klientami systemu Windows. Zapewnia również możliwe przyczyny i rozwiązania tych problemów. Oprócz kroków opisanych w tym artykule można także użyć programu [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) , aby upewnić się, że środowisko klienta systemu Windows ma odpowiednie wymagania wstępne. AzFileDiagnostics automatyzuje wykrywanie większości objawów wymienionych w tym artykule i ułatwia skonfigurowanie środowiska w celu uzyskania optymalnej wydajności.

> [!IMPORTANT]
> Zawartość tego artykułu dotyczy tylko udziałów SMB. Aby uzyskać szczegółowe informacje o udziałach NFS, zobacz [Rozwiązywanie problemów z udziałami plików NFS systemu Azure](storage-troubleshooting-files-nfs.md).

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Błąd 5 podczas instalowania udziału plików platformy Azure

Podczas próby zainstalowania udziału plików może zostać wyświetlony następujący błąd:

- Wystąpił błąd systemowy 5. Odmowa dostępu.

### <a name="cause-1-unencrypted-communication-channel"></a>Przyczyna 1: nieszyfrowany kanał komunikacyjny

Ze względów bezpieczeństwa połączenia z udziałami plików platformy Azure są blokowane, jeśli kanał komunikacyjny nie jest szyfrowany i jeśli próba połączenia nie pochodzi z tego samego centrum danych, w którym znajdują się udziały plików platformy Azure. Nieszyfrowane połączenia w tym samym centrum danych też mogą być blokowane, jeśli ustawienie [Wymagany bezpieczny transfer](../common/storage-require-secure-transfer.md) jest włączone na koncie magazynu. Szyfrowany kanał komunikacyjny jest udostępniany tylko wtedy, gdy system operacyjny klienta użytkownika obsługuje szyfrowanie protokołu SMB.

Systemy Windows 8, Windows Server 2012 i nowsze wersje negocjują żądania obejmujące protokół SMB 3.0, który obsługuje szyfrowanie.

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

1. Nawiąż połączenie z klientem obsługującym szyfrowanie SMB (system Windows 8, Windows Server 2012 lub nowszy) lub Połącz się z maszyną wirtualną w tym samym centrum danych co konto usługi Azure Storage, które jest używane w udziale plików platformy Azure.
2. Upewnij się, że ustawienie [wymagany bezpieczny transfer](../common/storage-require-secure-transfer.md) jest wyłączone na koncie magazynu, jeśli klient nie obsługuje szyfrowania SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 2: na koncie magazynu są włączone reguły sieci wirtualnej lub zapory 

Jeśli reguły sieci wirtualnej i zapory są skonfigurowane na koncie magazynu, dla ruchu sieciowego będzie następować odmowa dostępu, chyba że dostęp będzie dozwolony dla adresu IP klienta lub sieci wirtualnej.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../common/storage-network-security.md).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Przyczyna 3: uprawnienia na poziomie udziału są nieprawidłowe w przypadku korzystania z uwierzytelniania opartego na tożsamościach

Jeśli użytkownicy uzyskują dostęp do udziału plików platformy Azure przy użyciu uwierzytelniania Active Directory (AD) lub Azure Active Directory Domain Services (Azure AD DS), dostęp do udziału plików zakończy się niepowodzeniem z powodu błędu "odmowa dostępu", jeśli uprawnienia na poziomie udziału są nieprawidłowe. 

### <a name="solution-for-cause-3"></a>Rozwiązanie dla przyczyny 3

Sprawdź, czy uprawnienia zostały prawidłowo skonfigurowane:

- **Active Directory (AD)** zobacz [przypisywanie uprawnień na poziomie udziału do tożsamości](./storage-files-identity-ad-ds-assign-permissions.md).

    Przypisania uprawnień na poziomie udziału są obsługiwane dla grup i użytkowników, które zostały zsynchronizowane z Active Directory (AD) do Azure Active Directory (Azure AD) przy użyciu Azure AD Connect.  Upewnij się, że grupy i użytkownicy, którym przypisano uprawnienia na poziomie udziału, nie są obsługiwane grupy "tylko w chmurze".
- **Azure Active Directory Domain Services (AD DS platformy Azure)** zobacz [przypisywanie uprawnień dostępu do tożsamości](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Błąd 53, błąd 67 lub błąd 87 podczas instalowania lub odinstalowywania udziału plików platformy Azure

Podczas próby zainstalowania udziału plików z lokalnego lub innego centrum danych mogą pojawić się następujące błędy:

- Wystąpił błąd systemowy 53. Nie można znaleźć ścieżki sieciowej.
- Wystąpił błąd systemowy 67. Nie można znaleźć nazwy sieciowej.
- Wystąpił błąd systemowy 87. Parametr jest nieprawidłowy.

### <a name="cause-1-port-445-is-blocked"></a>Przyczyna 1: Port 445 jest zablokowany

Błąd systemu 53 lub błąd systemu 67 może wystąpić, jeśli port 445 wychodzący ruch do Azure Files centrum danych jest zablokowany. Aby zobaczyć podsumowanie usługodawców internetowych, którzy nie zezwalają na dostęp z portu 445, przejdź do witryny [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Aby sprawdzić, czy zapora lub usługodawca internetowy blokuje port 445, użyj narzędzia [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) lub `Test-NetConnection` polecenia cmdlet. 

Aby użyć `Test-NetConnection` polecenia cmdlet, należy zainstalować moduł Azure PowerShell, aby uzyskać więcej informacji, zobacz [Install Azure PowerShell module](/powershell/azure/install-Az-ps) . Pamiętaj, aby zastąpić wyrażenia `<your-storage-account-name>` i `<your-resource-group-name>` nazwami odpowiednimi dla konta magazynu.

   
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
Azure File Sync można przekształcić lokalny serwer systemu Windows w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego dostępnego protokołu w systemie Windows Server w celu uzyskania lokalnego dostępu do danych (w tym protokołu SMB, systemu plików NFS i protokołu FTPS). Usługa Azure File Sync działa przez port 443 i może służyć jako obejście w celu uzyskania dostępu do usługi Azure Files z klientów, którzy mają zablokowany port 445. [Dowiedz się, jak skonfigurować Azure File Sync](./storage-sync-files-extend-servers.md).

#### <a name="solution-2---use-vpn"></a>Rozwiązanie 2 — użycie sieci VPN
Skonfigurowanie sieci VPN do określonego konta magazynu spowoduje, że ruch przejdzie przez bezpieczny tunel, a nie przez Internet. Postępuj zgodnie z [instrukcjami konfigurowania sieci VPN](storage-files-configure-p2s-vpn-windows.md), aby uzyskać dostęp do usługi Azure Files z systemu Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Rozwiązanie 3 — odblokowanie portu 445 przy pomocy usługodawcy internetowego/administratora IT
Skontaktuj się z działem IT lub usługodawcą internetowym, aby otworzyć port 445 wychodzące do [zakresów adresów IP platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Rozwiązanie 4 — użycie narzędzi opartych na interfejsie API REST, takich jak Eksplorator usługi Storage/Powershell
Azure Files obsługuje również protokół REST oprócz protokołu SMB. Dostęp do funkcji REST działa przez port 443 (standardowy protokół TCP). Istnieją różne narzędzia, które są napisane przy użyciu interfejsu API REST, które oferują rozbudowany interfejs użytkownika. [Eksplorator usługi Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) jest jednym z nich. [Pobierz i zainstaluj Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/) i połącz się ze swoim udziałem plików obsługiwanym przez usługę Azure Files. Można również użyć [programu PowerShell](./storage-how-to-use-files-powershell.md) , który również jest interfejsem API REST użytkownika.

### <a name="cause-2-ntlmv1-is-enabled"></a>Przyczyna 2: NTLMv1 jest włączona

Błąd systemu 53 lub błąd systemu 87 może wystąpić, jeśli na kliencie jest włączona komunikacja NTLMv1. Usługa Azure Files obsługuje tylko uwierzytelnianie NTLMv2. Włączenie komunikacji NTLMv1 powoduje, że klient jest mniej bezpieczny. Dlatego komunikacja jest blokowana dla usługi Azure Files. 

Aby określić, czy jest to przyczyną błędu, sprawdź, czy w poniższym podkluczu rejestru nie ustawiono wartości mniejszej niż 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Aby uzyskać więcej informacji, zobacz temat [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) w witrynie TechNet.

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przywróć wartość **LmCompatibilityLevel** do wartości domyślnej równej 3 w następującym podkluczu rejestru:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>Błąd 1816 — brak wystarczającego limitu przydziału do przetworzenia tego polecenia

### <a name="cause"></a>Przyczyna

Błąd 1816 występuje po osiągnięciu górnego limitu współbieżnych otwartych dojść, które są dozwolone dla pliku lub katalogu w udziale plików platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Files scale targets (Cele skalowania usługi Azure Files)](./storage-files-scale-targets.md#azure-files-scale-targets).

### <a name="solution"></a>Rozwiązanie

Zmniejsz liczbę współbieżnych dojść otwartych przez zamknięcie niektórych uchwytów, a następnie ponów próbę. Aby uzyskać więcej informacji, zobacz [Microsoft Azure Storage listy kontrolnej wydajności i skalowalności](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Aby wyświetlić otwarte uchwyty dla udziału plików, katalogu lub pliku, należy użyć polecenia cmdlet [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) programu PowerShell.  

Aby zamknąć otwarte uchwyty dla udziału plików, katalogu lub pliku, należy użyć polecenia cmdlet programu PowerShell [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) .

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w AZ PowerShell module w wersji 2,4 lub nowszej. Aby zainstalować najnowszy moduł AZ PowerShell module, zobacz [Install the Azure PowerShell module](/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Błąd "Brak dostępu" podczas próby uzyskania dostępu do udziału plików platformy Azure lub usunięcie go  
W przypadku próby uzyskania dostępu do udziału plików platformy Azure w portalu lub usunięcia go z niego może zostać wyświetlony następujący błąd:

Brak dostępu  
Kod błędu: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Przyczyna 1: na koncie magazynu są włączone reguły sieci wirtualnej lub zapory

### <a name="solution-for-cause-1"></a>Rozwiązanie dla przyczyny 1

Sprawdź, czy reguły sieci wirtualnej i zapory są skonfigurowane poprawnie na koncie magazynu. W celu przetestowania, czy reguły sieci wirtualnej lub zapory są przyczyną problemu, tymczasowo zmień ustawienie na koncie magazynu, aby **zezwolić na dostęp ze wszystkich sieci**. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../common/storage-network-security.md).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Przyczyna 2: Twoje konto użytkownika nie ma dostępu do konta magazynu

### <a name="solution-for-cause-2"></a>Rozwiązanie dla przyczyny 2

Przejdź do konta magazynu, na którym znajduje się udział plików platformy Azure, kliknij pozycję **Kontrola dostępu (IAM)** i sprawdź, czy konto użytkownika ma dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [jak zabezpieczyć konto magazynu za pomocą kontroli dostępu opartej na rolach na platformie Azure (RBAC)](../blobs/security-recommendations.md#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>Nie można zmodyfikować, przenieść/zmienić nazwy lub usunąć pliku lub katalogu
Jeden z najważniejszych celów udziału plików polega na tym, że wielu użytkowników i aplikacji może jednocześnie współdziałać z plikami i katalogami w udziale. Aby pomóc w tej interakcji, udziały plików zapewniają kilka sposobów mediating dostępu do plików i katalogów.

Po otwarciu pliku z zainstalowanego udziału plików platformy Azure za pośrednictwem protokołu SMB aplikacja/system operacyjny żąda dojścia do pliku, który jest odwołaniem do pliku. Po zażądaniu dojścia do pliku aplikacja określa tryb udostępniania plików, który określa poziom wyłącznego dostępu do pliku wymuszonego przez Azure Files: 

- `None`: masz wyłączny dostęp. 
- `Read`: inne osoby mogą odczytywać plik, gdy jest otwarty.
- `Write`: inne osoby mogą zapisywać do pliku, gdy jest otwarty. 
- `ReadWrite`: kombinacja `Read` `Write` trybów udostępniania i.
- `Delete`: inne osoby mogą usunąć plik, gdy jest otwarty. 

Chociaż jako protokół bezstanowy protokół FileREST nie ma koncepcji dojścia do plików, zapewnia podobny mechanizm do skorygowania dostępu do plików i folderów, które mogą być używane przez skrypt, aplikację lub usługę: dzierżawy plików. Gdy plik jest dzierżawiony, jest traktowany jako równoważny z dojściem do pliku z trybem udostępniania plików `None` . 

Chociaż dojścia do plików i dzierżawy dają istotny cel, czasami dojścia do plików i dzierżawy mogą być oddzielone. W takim przypadku może to spowodować problemy z modyfikacją lub usunięciem plików. Mogą pojawić się komunikaty o błędach, takie jak:

- Proces nie może uzyskać dostępu do pliku, ponieważ jest on używany przez inny proces.
- Nie można ukończyć akcji, ponieważ plik jest otwarty w innym programie.
- Dokument jest zablokowany do edycji przez innego użytkownika.
- Określony zasób jest oznaczony do usunięcia przez klienta SMB.

Rozwiązanie tego problemu zależy od tego, czy jest to spowodowane przez oddzielone dojście do pliku, czy dzierżawę. 

### <a name="cause-1"></a>Przyczyna 1
Dojście do pliku uniemożliwia modyfikowanie lub usuwanie pliku/katalogu. Do wyświetlania otwartych dojść można użyć polecenia cmdlet programu PowerShell [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) . 

Jeśli wszyscy klienci SMB zamknęli swoje otwarte dojścia do pliku/katalogu i problem nadal wystąpi, można wymusić zamknięcie dojścia do pliku.

### <a name="solution-1"></a>Rozwiązanie 1
Aby wymusić zamknięcie dojścia do pliku, należy użyć polecenia cmdlet programu PowerShell [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) . 

> [!Note]  
> Polecenia cmdlet Get-AzStorageFileHandle i Close-AzStorageFileHandle są zawarte w AZ PowerShell module w wersji 2,4 lub nowszej. Aby zainstalować najnowszy moduł AZ PowerShell module, zobacz [Install the Azure PowerShell module](/powershell/azure/install-az-ps).

### <a name="cause-2"></a>Przyczyna 2
Dzierżawa pliku uniemożliwia zmodyfikowanie lub usunięcie pliku. Możesz sprawdzić, czy plik ma dzierżawę pliku z następującym programem PowerShell, zastępując `<resource-group>` ,, `<storage-account>` `<file-share>` i `<path-to-file>` z odpowiednimi wartościami dla danego środowiska:

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

Poniższy przykład pokazuje, jak przerwać dzierżawę dla pliku wskazanego w przyczynie 2 (w tym przykładzie jest to kontynuowane przy użyciu zmiennych programu PowerShell z przyczyny 2):

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Slow file copying to and from Azure Files in Windows (Wolne kopiowanie plików do i z usługi Azure Files w systemie Windows)

Podczas próby przetransferowania plików do usługi plików platformy Azure może być widoczna niska wydajność.

- Jeśli nie masz wymaganego minimalnego rozmiaru operacji we/wy, zalecamy użycie 1 MiB jako rozmiaru we/wy w celu uzyskania optymalnej wydajności.
-   Jeśli znasz końcowy rozmiar pliku, który jest rozszerzany przy użyciu zapisu, a oprogramowanie nie ma problemów ze zgodnością, gdy niezapisany ogon w pliku zawiera zera, a następnie ustaw rozmiar pliku z góry, zamiast wprowadzać każdy zapis rozszerzający.
-   Użyj odpowiedniej metody copy:
    -   Użyj [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) do dowolnego transferu między dwoma udziałami plików.
    -   Użyj [Robocopy](./storage-how-to-create-file-share.md) między udziałami plików na komputerze lokalnym.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Zagadnienia dotyczące Windows 8.1 lub systemu Windows Server 2012 R2

W przypadku klientów z systemem Windows 8.1 lub Windows Server 2012 R2 upewnij się, że zainstalowano poprawkę [KB3114025](https://support.microsoft.com/help/3114025) . Ta poprawka podnosi wydajność dojścia do tworzenia i zamykania.

Aby sprawdzić, czy poprawka została zainstalowana, można uruchomić następujący skrypt:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Jeśli poprawka jest zainstalowana, wyświetlane są następujące dane wyjściowe:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> Obrazy systemu Windows Server 2012 R2 w witrynie Azure Marketplace mają domyślnie zainstalowane KB3114025 poprawek, począwszy od grudnia 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Brak folderu z literą dysku w "My Computer" lub "tym komputerze"

Jeśli udział plików platformy Azure jest mapowany jako administrator przy użyciu net use, prawdopodobnie brakuje udziału.

### <a name="cause"></a>Przyczyna

Domyślnie Eksplorator plików systemu Windows nie jest uruchomiony jako administrator. Jeśli uruchomisz polecenie net use z wiersza polecenia z uprawnieniami administracyjnymi, możesz mapować dysk sieciowy jako administrator. Ze względu na to, że mapowane dyski są skoncentrowane na użytkownikach, zalogowane konto użytkownika nie wyświetla dysków, jeśli są one zainstalowane przy użyciu innego konta użytkownika.

### <a name="solution"></a>Rozwiązanie
Zainstaluj udział z wiersza polecenia bez administratora. Alternatywnie możesz skorzystać z [tego tematu TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10)) , aby skonfigurować wartość rejestru **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>Polecenie net use kończy się niepowodzeniem, jeśli konto magazynu zawiera ukośnik

### <a name="cause"></a>Przyczyna

Polecenie net use interpretuje ukośnik (/) jako opcję wiersza polecenia. Jeśli nazwa konta użytkownika zaczyna się od ukośnika, mapowanie dysku kończy się niepowodzeniem.

### <a name="solution"></a>Rozwiązanie

Aby obejść ten problem, można wykonać jedną z następujących czynności:

- Uruchom następujące polecenie programu PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  W pliku wsadowym można uruchomić polecenie w następujący sposób:

  `Echo new-smbMapping ... | powershell -command –`

- Umieść znaki podwójnego cudzysłowu otaczające klucz, aby obejść ten problem — chyba że ukośnik jest pierwszym znakiem. Jeśli tak jest, użyj trybu interaktywnego, a następnie wprowadź hasło oddzielnie lub ponownie wygeneruj klucze, aby uzyskać klucz, który nie zaczyna się od ukośnika.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>Aplikacja lub usługa nie może uzyskać dostępu do zainstalowanego dysku Azure Files

### <a name="cause"></a>Przyczyna

Dyski są instalowane na użytkownika. Jeśli aplikacja lub usługa jest uruchomiona przy użyciu innego konta użytkownika niż to, na którym zainstalowano dysk, aplikacja nie zobaczy tego dysku.

### <a name="solution"></a>Rozwiązanie

Użyj jednego z następujących rozwiązań:

-   Zainstaluj dysk z tego samego konta użytkownika, które zawiera aplikację. Możesz użyć narzędzia, takiego jak PsExec.
- Przekaż nazwę i klucz konta magazynu w parametrach nazwa użytkownika i hasło polecenia net use.
- Użyj polecenia cmdkey, aby dodać poświadczenia do Menedżera poświadczeń. Wykonaj to z wiersza polecenia w kontekście konta usługi za pośrednictwem interakcyjnego logowania lub przy użyciu `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapuj udział bezpośrednio bez użycia zmapowanej litery dysku. Niektóre aplikacje mogą nie ponownie połączyć się z literą dysku, więc użycie pełnej ścieżki UNC może być bardziej niezawodne. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Po wykonaniu tych instrukcji może zostać wyświetlony następujący komunikat o błędzie podczas uruchamiania polecenia net use dla konta usługi sieciowej/systemu: "błąd systemu 1312. Określona sesja logowania nie istnieje. Być może zostało już zakończone. " W takim przypadku upewnij się, że nazwa użytkownika, która jest przenoszona do usługi net use, zawiera informacje o domenie (na przykład: "[nazwa konta magazynu]. plik. Core. Windows. NET").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Błąd "Kopiowanie pliku do lokalizacji docelowej, która nie obsługuje szyfrowania"

Gdy plik jest kopiowany za pośrednictwem sieci, plik jest odszyfrowywany na komputerze źródłowym, przesyłany w postaci zwykłego tekstu i ponownie szyfrowany w miejscu docelowym. Jednak podczas próby skopiowania zaszyfrowanego pliku może zostać wyświetlony następujący błąd: "kopiujesz plik do lokalizacji docelowej, która nie obsługuje szyfrowania".

### <a name="cause"></a>Przyczyna
Ten problem może wystąpić, jeśli używasz system szyfrowania plików (EFS). Pliki szyfrowane przez funkcję BitLocker można kopiować do Azure Files. Jednak Azure Files nie obsługuje systemu plików NTFS.

### <a name="workaround"></a>Obejście
Aby skopiować plik za pośrednictwem sieci, należy go najpierw odszyfrować. Użyj jednej z następujących metod:

- Użyj polecenia **copy/d** . Umożliwia zapisywanie zaszyfrowanych plików jako odszyfrowanych plików w miejscu docelowym.
- Ustaw poniższy klucz rejestru:
  - Ścieżka = HKLM\Software\Policies\Microsoft\Windows\System
  - Typ wartości = DWORD
  - Nazwa: CopyFileAllowDecryptedRemoteDestination
  - Wartość: 1

Należy pamiętać, że ustawienie klucza rejestru ma wpływ na wszystkie operacje kopiowania wprowadzone do udziałów sieciowych.

## <a name="slow-enumeration-of-files-and-folders"></a>Wolne Wyliczenie plików i folderów

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli na komputerze klienckim nie ma wystarczającej ilości pamięci podręcznej dla dużych katalogów.

### <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy dostosować wartość rejestru **DirectoryCacheEntrySizeMax** , aby zezwalała na buforowanie większych list katalogów na komputerze klienckim:

- Lokalizacja: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Wartość Mane: DirectoryCacheEntrySizeMax 
- Typ wartości: DWORD
 
 
Na przykład można ustawić 0x100000 i sprawdzić, czy wydajność staje się lepsza.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Wystąpił błąd AadDsTenantNotFound podczas włączania uwierzytelniania Azure Active Directory Domain Service (Azure AD DS) dla Azure Files "nie można zlokalizować aktywnych dzierżawców z IDENTYFIKATORem dzierżawy AAD-dzierżawca"

### <a name="cause"></a>Przyczyna

AadDsTenantNotFound Wystąpił błąd podczas próby [włączenia uwierzytelniania Azure Active Directory Domain Services (azure AD DS) na Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) na koncie magazynu, w którym [usługa Azure AD Domain Service (Azure AD DS)](../../active-directory-domain-services/overview.md) nie została utworzona w dzierżawie usługi Azure AD skojarzonej subskrypcji.  

### <a name="solution"></a>Rozwiązanie

Włącz usługę Azure AD DS w dzierżawie usługi Azure AD subskrypcji, w której wdrożono konto magazynu. Do utworzenia domeny zarządzanej wymagane są uprawnienia administratora dzierżawy usługi Azure AD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby [utworzyć i skonfigurować domenę zarządzaną Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Nie można zainstalować Azure Files przy użyciu poświadczeń usługi AD 

### <a name="self-diagnostics-steps"></a>Kroki samodiagnostyki
Najpierw upewnij się, że wykonano wszystkie cztery kroki, aby [włączyć Azure Files uwierzytelnianie usługi AD](./storage-files-identity-auth-active-directory-enable.md).

Następnie spróbuj zainstalować [udział plików platformy Azure z kluczem konta magazynu](./storage-how-to-use-files-windows.md). Jeśli instalacja nie powiodła się, Pobierz [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) , aby pomóc w sprawdzeniu, czy środowisko klienta działa, Wykryj niezgodną konfigurację klienta, która spowoduje niepowodzenie dostępu do Azure Files, zawiera wskazówki dotyczące samoobsługowego rozwiązywania problemów, a następnie zbiera ślady diagnostyki.

Po trzecie można uruchomić polecenie cmdlet Debug-AzStorageAccountAuth, aby przeprowadzić zestaw podstawowych sprawdzeń konfiguracji usługi AD przy użyciu zalogowanego użytkownika usługi AD. To polecenie cmdlet jest obsługiwane w [wersji AzFilesHybrid 0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases). Należy uruchomić to polecenie cmdlet z użytkownikiem usługi AD, który ma uprawnienia właściciela do docelowego konta magazynu.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
Polecenie cmdlet wykonuje poniższe testy w sekwencji i zawiera wskazówki dotyczące niepowodzeń:
1. CheckADObjectPasswordIsCorrect: Upewnij się, że hasło skonfigurowane na tożsamości usługi AD reprezentującej konto magazynu jest zgodne z kluczem konta magazynu kerb1 lub kerb2. Jeśli hasło jest niepoprawne, można uruchomić polecenie [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) w celu zresetowania hasła. 
2. CheckADObject: Upewnij się, że w Active Directory znajduje się obiekt, który reprezentuje konto magazynu i ma poprawną nazwę SPN (główna nazwa usługi). Jeśli nazwa SPN nie została prawidłowo skonfigurowana, uruchom polecenie cmdlet Set-AD zwrócone w poleceniu cmdlet Debug, aby skonfigurować nazwę SPN.
3. CheckDomainJoined: Sprawdź, czy komputer kliencki jest przyłączony do usługi AD. Jeśli komputer nie jest przyłączony do usługi AD, zapoznaj się z tym [artykułem](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain) , aby uzyskać instrukcje dotyczące przyłączania do domeny.
4. CheckPort445Connectivity: Sprawdź, czy port 445 jest otwarty dla połączenia SMB. Jeśli wymagany port nie jest otwarty, Skorzystaj z narzędzia do rozwiązywania problemów [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) w przypadku problemów z łącznością z Azure Files.
5. CheckSidHasAadUser: Sprawdź, czy zalogowany użytkownik usługi AD jest synchronizowany z usługą Azure AD. Jeśli chcesz sprawdzić, czy określony użytkownik usługi AD jest synchronizowany z usługą Azure AD, możesz określić parametry-UserName i-Domain w parametrach wejściowych. 
6. CheckGetKerberosTicket: spróbuj uzyskać bilet protokołu Kerberos, aby nawiązać połączenie z kontem magazynu. Jeśli nie ma prawidłowego tokenu Kerberos, uruchom polecenie cmdlet Klist-CIFS/Storage-account-name. plik. Core. Windows. NET i Przeanalizuj kod błędu do katalogu głównego — spowoduje to niepowodzenie pobierania biletu.
7. CheckStorageAccountDomainJoined: Sprawdź, czy uwierzytelnianie usługi AD zostało włączone i czy są wypełniane właściwości usługi AD konta. W przeciwnym razie zapoznaj się z instrukcją w [tym miejscu](./storage-files-identity-ad-ds-enable.md) , aby włączyć uwierzytelnianie AD DS w Azure Files. 
8. CheckUserRbacAssignment: Sprawdź, czy użytkownik usługi AD ma odpowiednie przypisanie roli RBAC, aby zapewnić uprawnienia dostępu do Azure Files. W przeciwnym razie zapoznaj się z instrukcją w [tym miejscu](./storage-files-identity-ad-ds-assign-permissions.md) , aby skonfigurować uprawnienie na poziomie udziału. (Obsługiwane w programie AzFilesHybrid v 0.2.3 + version)
9. CheckUserFileAccess: Sprawdź, czy użytkownik usługi AD ma odpowiednie uprawnienie katalogu/pliku (listy ACL systemu Windows), aby uzyskać dostęp do Azure Files. W przeciwnym razie zapoznaj się z instrukcją w [tym miejscu](./storage-files-identity-ad-ds-configure-permissions.md) , aby skonfigurować uprawnienie na poziomie katalogu/pliku. (Obsługiwane w programie AzFilesHybrid v 0.2.3 + version)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Nie można skonfigurować uprawnień na poziomie katalogu/pliku (listy ACL systemu Windows) przy użyciu Eksploratora plików systemu Windows

### <a name="symptom"></a>Objaw

Podczas próby skonfigurowania list ACL systemu Windows za pomocą Eksploratora plików w zainstalowanym udziale plików mogą wystąpić poniższe objawy:
- Po kliknięciu przycisku Edytuj uprawnienia na karcie Zabezpieczenia Kreator uprawnień nie zostanie załadowany. 
- Gdy próbujesz wybrać nowego użytkownika lub grupę, w lokalizacji domeny nie jest wyświetlana odpowiednia domena AD DS. 

### <a name="solution"></a>Rozwiązanie

Zalecamy użycie [Narzędzia icacls](/windows-server/administration/windows-commands/icacls) w celu skonfigurowania uprawnień na poziomie katalogu/pliku jako obejścia. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Błędy podczas uruchamiania Join-AzStorageAccountForAuth polecenia cmdlet

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>Błąd: "usługa katalogowa nie może przydzielić identyfikatora względnego"

Ten błąd może wystąpić, Jeśli kontroler domeny z rolą FSMO głównego identyfikatora RID jest niedostępny lub został usunięty z domeny i przywrócony z kopii zapasowej.  Upewnij się, że wszystkie kontrolery domeny są uruchomione i dostępne.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>Błąd: „Nie można powiązać parametrów pozycyjnych, ponieważ nie podano nazw”

Ten błąd jest najprawdopodobniej wyzwalany przez błąd składniowy w poleceniu Join-AzStorageAccountforAuth.  Sprawdź polecenie w poszukiwaniu błędów pisowni lub składni i sprawdź, czy Najnowsza wersja modułu AzFilesHybrid ( https://github.com/Azure-Samples/azure-files-samples/releases) jest zainstalowana).  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>Azure Files obsługę lokalnego uwierzytelniania AD DS szyfrowania AES 256 Kerberos

Wprowadzono obsługę szyfrowania AES 256 Kerberos dla Azure Files uwierzytelniania przy użyciu usługi AzFilesHybrid w systemie Premium AD DS [0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases). Jeśli włączono uwierzytelnianie AD DS przy użyciu modułu w wersji niższej niż v 0.2.2, konieczne będzie pobranie najnowszego modułu AzFilesHybrid (v 0.2.2 +) i uruchomienie programu PowerShell poniżej. Jeśli nie włączono jeszcze AD DS uwierzytelniania na koncie magazynu, możesz wykonać te [wskazówki](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module) dotyczące włączania. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```


## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Skontaktuj się z pomocą techniczną.
Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać problem.
