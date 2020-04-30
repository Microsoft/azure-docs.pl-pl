---
title: Korzystanie z udziału plików platformy Azure w systemie Windows | Microsoft Docs
description: Dowiedz się, jak używać udziału plików platformy Azure w systemach Windows i Windows Server.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2694e0c1536064267faad10517ae58d0709ad1c8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231768"
---
# <a name="use-an-azure-file-share-with-windows"></a>Korzystanie z udziału plików platformy Azure w systemie Windows
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziałów plików platformy Azure można bezproblemowo używać w systemach Windows i Windows Server. W tym artykule omówiono zagadnienia dotyczące korzystania z udziału plików platformy Azure w systemach Windows i Windows Server.

Aby móc korzystać z udziału plików platformy Azure poza regionem świadczenia usługi Azure, w którym jest on hostowany, na przykład lokalnie lub w innym regionie świadczenia usługi Azure, system operacyjny musi obsługiwać protokół SMB 3.0. 

Z udziałów plików platformy Azure można korzystać w instalacji systemu Windows działającej na maszynie wirtualnej platformy Azure lub lokalnie. W poniższej tabeli pokazano, które wersje systemów operacyjnych obsługują dostęp do udziałów plików w poszczególnych środowiskach:

| Wersja systemu Windows        | Wersja protokołu SMB | Możliwa instalacja na maszynie wirtualnej platformy Azure | Instalacja lokalna |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Tak | Tak |
| Windows 10<sup>1</sup> | SMB 3.0 | Tak | Tak |
| Windows Server semi-annual channel<sup>2</sup> | SMB 3.0 | Tak | Tak |
| Windows Server 2016 | SMB 3.0 | Tak | Tak |
| Windows 8.1 | SMB 3.0 | Tak | Tak |
| Windows Server 2012 R2 | SMB 3.0 | Tak | Tak |
| Windows Server 2012 | SMB 3.0 | Tak | Tak |
| Windows 7<sup>3</sup> | SMB 2.1 | Tak | Nie |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Tak | Nie |

<sup>1</sup> Windows 10, wersje 1507, 1607, 1709, 1803, 1809, 1903 i 1909.  
<sup>2</sup> System Windows Server, wersje 1809, 1903 i 1909.  
<sup>3</sup> Zakończono regularne wsparcie firmy Microsoft dla systemów Windows 7 i Windows Server 2008 R2. Dodatkowe wsparcie dla aktualizacji zabezpieczeń można zakupić tylko za pomocą [programu Extended Security Update (ESU)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). Zdecydowanie zalecamy Migrowanie z tych systemów operacyjnych.

> [!Note]  
> Zawsze zalecamy pobranie najnowszej aktualizacji KB dla danej wersji systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne 
* **Nazwa konta magazynu**: Aby zainstalować udział plików platformy Azure, potrzebna będzie nazwa konta magazynu.

* **Klucz konta magazynu**: Aby zainstalować udział plików platformy Azure, konieczne będzie posiadanie podstawowego (lub dodatkowego) klucza magazynu. Klucze sygnatur dostępu współdzielonego nie są aktualnie obsługiwane na potrzeby instalowania.

* **Otwarty port 445**: protokół SMB wymaga otwartego portu TCP 445; połączenia zakończą się niepowodzeniem, jeśli port 445 będzie zablokowany. Aby sprawdzić, czy zapora blokuje port 445, można użyć polecenia cmdlet `Test-NetConnection`. Można dowiedzieć się więcej [na temat różnych sposobów obejścia zablokowanego portu 445 w tym miejscu](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked).

    W poniższym kodzie programu PowerShell założono, że zainstalowano moduł Azure PowerShell, zobacz [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) , aby uzyskać więcej informacji. Pamiętaj, aby zastąpić wyrażenia `<your-storage-account-name>` i `<your-resource-group-name>` nazwami odpowiednimi dla konta magazynu.

    ```powershell
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

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > Powyższe polecenie zwraca bieżący adres IP konta magazynu. Nie ma żadnej gwarancji, że ten adres IP pozostanie niezmieniony. Może on ulec zmianie w dowolnym momencie. Nie umieszczaj tego adresu IP w żadnym kodzie skryptu ani konfiguracji zapory. 

## <a name="using-an-azure-file-share-with-windows"></a>Korzystanie z udziału plików platformy Azure w systemie Windows
Aby używać udziału plików platformy Azure w systemie Windows, musisz go zainstalować, czyli przypisać do niego literę dysku bądź ścieżkę do punktu instalacji, lub uzyskiwać do niego dostęp za pośrednictwem jego [ścieżki UNC](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

W przeciwieństwie do innych udziałów SMB, z którymi możesz wchodzić w interakcje, na przykład takich jak te hostowane w systemie Windows Server, na serwerze Samba w systemie Linux lub na urządzeniu NAS, udziały plików platformy Azure aktualnie nie obsługują uwierzytelniania Kerberos za pomocą tożsamości usługi Active Directory (AD) lub Azure Active Directory (AAD), ale [pracujemy nad](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles) włączeniem takiej możliwości. Zamiast tego dostęp do udziału plików platformy Azure musi być uzyskiwany przy użyciu klucza konta magazynu zawierającego ten udział plików platformy Azure. Klucz konta magazynu to klucz administratora dla konta magazynu, w tym uprawnienia administratora do wszystkich plików i folderów w udziale plików, do którego uzyskujesz dostęp, a także dla wszystkich udziałów plików i innych zasobów magazynu (obiektów blob, kolejek, tabel itp.) zawartych w ramach konta magazynu. Jeśli te uprawnienia nie są wystarczające na potrzeby danego obciążenia, [usługa Azure File Sync](storage-sync-files-planning.md) może tymczasowo zastąpić brak obsługi uwierzytelniania Kerberos i list kontroli dostępu, dopóki obsługa uwierzytelniania Kerberos opartego na usłudze AAD i list kontroli dostępu zostanie publicznie udostępniona.

Typowym sposobem na przeniesienie na platformę Azure aplikacji biznesowych (LOB), które oczekują obsługi udziału plików SMB, jest użycie udziału plików platformy Azure jako alternatywy do uruchamiania dedykowanego serwera plików Windows na maszynie wirtualnej platformy Azure. Ważnym zagadnieniem warunkującym pomyślną migrację aplikacji biznesowej do korzystania z udziału plików platformy Azure jest to, że wiele aplikacji biznesowych działa w kontekście dedykowanego konta usługi z ograniczonymi uprawnieniami systemowymi, a nie w kontekście konta administracyjnego maszyny wirtualnej. W związku z tym należy się upewnić, że poświadczenia dla udziału plików platformy Azure zostały zainstalowane/zapisane w kontekście konta usługi, a nie konta administracyjnego.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Utrwalanie poświadczeń udziału plików platformy Azure w systemie Windows  
Narzędzie [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) umożliwia przechowywanie poświadczeń konta magazynu w systemie Windows. Oznacza to, że podczas próby dostępu do udziału plików platformy Azure za pośrednictwem ścieżki UNC lub próby zainstalowania udziału plików platformy Azure nie będzie konieczne podawanie poświadczeń. Aby zapisać poświadczenia konta magazynu, uruchom następujące polecenia programu PowerShell, zastępując elementy `<your-storage-account-name>` i `<your-resource-group-name>` zgodnie z wymaganiami.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

Aby sprawdzić, czy narzędzie cmdkey zapisało poświadczenia dla konta magazynu, możesz użyć parametru „list”:

```powershell
cmdkey /list
```

Jeśli poświadczenia dla udziału plików platformy Azure zostały pomyślnie zapisane, dane wyjściowe powinny wyglądać w następujący sposób (na liście mogą być zapisane dodatkowe klucze):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Teraz powinno być możliwe zainstalowanie udziału lub uzyskanie do niego dostępu bez konieczności podawania dodatkowych poświadczeń.

#### <a name="advanced-cmdkey-scenarios"></a>Zaawansowane scenariusze narzędzia cmdkey
Istnieją dwa dodatkowe scenariusze użycia narzędzia cmdkey, które warto poznać: przechowywanie poświadczeń dla innego użytkownika na tej samej maszynie, na przykład za pośrednictwem konta usługi, i przechowywanie poświadczeń na maszynie zdalnej za pomocą komunikacji zdalnej programu PowerShell.

Przechowywanie poświadczeń dla innego użytkownika na komputerze jest proste: po zalogowaniu się na koncie wystarczy wykonać następujące polecenie programu PowerShell:

```powershell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Zostanie otwarte nowe okno programu PowerShell w kontekście użytkownika konta usługi (lub konta użytkownika). Następnie można użyć narzędzia cmdkey zgodnie z opisem [powyżej](#persisting-azure-file-share-credentials-in-windows).

Przechowywanie poświadczeń na maszynie zdalnej za pomocą komunikacji zdalnej programu PowerShell nie jest możliwe, ponieważ narzędzie cmdkey nie zezwala na dostęp, nawet dla operacji dodawania, do jego magazynu poświadczeń, gdy użytkownik jest zalogowany przy użyciu komunikacji zdalnej programu PowerShell. Firma Microsoft zaleca zalogowanie się do maszyny za pomocą [pulpitu zdalnego](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows).

### <a name="mount-the-azure-file-share-with-powershell"></a>Instalowanie udziału plików platformy Azure za pomocą programu PowerShell
Aby zainstalować udział plików platformy Azure, uruchom następujące polecenia z zwykłej (nie z podwyższonym poziomem uprawnień). Pamiętaj, aby zastąpić elementy `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` i `<desired-drive-letter>` odpowiednimi informacjami.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> Użycie opcji `-Persist` polecenia cmdlet `New-PSDrive` spowoduje, że ponowne zainstalowanie udziału plików podczas rozruchu będzie możliwe tylko wtedy, gdy poświadczenia zostały zapisane. Poświadczenia można zapisać za pomocą narzędzia cmdkey, postępując zgodnie z [wcześniejszym opisem](#persisting-azure-file-share-credentials-in-windows). 

Jeśli to konieczne, możesz odinstalować udział plików platformy Azure przy użyciu następującego polecenia cmdlet programu PowerShell.

```powershell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Instalowanie udziału plików platformy Azure za pomocą Eksploratora plików
> [!Note]  
> Należy pamiętać, że następujące instrukcje dotyczą systemu Windows 10 i mogą być nieco inne w starszych wersjach. 

1. Otwórz Eksploratora plików. Można to zrobić przy użyciu menu Start lub przez naciśnięcie skrótu Win+E.

1. Przejdź do **tego elementu komputera** po lewej stronie okna. Spowoduje to zmianę menu dostępnego na wstążce. W menu komputer wybierz pozycję **Mapuj dysk sieciowy**.
    
    ![Zrzut ekranu przedstawiający menu rozwijane „Mapuj dysk sieciowy”](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Wybierz literę dysku, a następnie wprowadź ścieżkę UNC Format ścieżki UNC `<storageAccountName>.file.core.windows.net/<fileShareName>`. Na przykład: `anexampleaccountname.file.core.windows.net/example-share-name`.
    
    ![Zrzut ekranu przedstawiający okno dialogowe „Mapowanie dysku sieciowego”](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Jako nazwy użytkownika użyj nazwy konta magazynu poprzedzonej ciągiem `AZURE\`, a jako hasła użyj klucza konta magazynu.
    
    ![Zrzut ekranu okna dialogowego poświadczeń sieciowych](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Użyj udziału plików platformy Azure zgodnie z potrzebami.
    
    ![Udział plików platformy Azure jest teraz zainstalowany](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Gdy zajdzie potrzeba odinstalowania udziału plików platformy Azure, możesz to zrobić przez kliknięcie prawym przyciskiem myszy wpisu dla udziału w obszarze **Lokalizacje sieciowe** w Eksploratorze plików i wybranie polecenia **Odłącz**.

### <a name="accessing-share-snapshots-from-windows"></a>Dostęp do migawek udziałów z systemu Windows
Jeśli migawkę udziału utworzono ręcznie lub automatycznie za pomocą skryptu bądź usługi, takiej jak Azure Backup, możesz wyświetlić poprzednie wersje udziału, katalogu lub konkretnego pliku z udziału plików w systemie Windows. Migawkę udziału można wykonać za pomocą [Azure Portal](storage-how-to-use-files-portal.md), [Azure PowerShell](storage-how-to-use-files-powershell.md)i [interfejsu wiersza polecenia platformy Azure](storage-how-to-use-files-cli.md).

#### <a name="list-previous-versions"></a>Wyświetlanie listy poprzednich wersji
Przejdź do elementu lub elementu nadrzędnego, który należy przywrócić. Kliknij dwukrotnie, aby przejść do żądanego katalogu. Kliknij prawym przyciskiem myszy i wybierz z menu pozycję **Właściwości**.

![Menu dla wybranego katalogu wyświetlane po kliknięciu prawym przyciskiem myszy](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Wybierz pozycję **Poprzednie wersje**, aby wyświetlić listę migawek udziału dla tego katalogu. Załadowanie listy może potrwać kilka sekund w zależności od szybkości sieci i liczby migawek udziałów w katalogu.

![Karta Poprzednie wersje](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Możesz wybrać pozycję **Otwórz**, aby otworzyć określoną migawkę. 

![Otwarta migawka](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Uaktualnianie z poprzedniej wersji
Wybierz pozycję **Przywróć**, aby rekursywnie skopiować zawartość całego katalogu podczas tworzenia migawki udziału do oryginalnej lokalizacji.

 ![Przycisk Przywróć w komunikacie ostrzegawczym](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Zabezpieczanie systemu Windows lub Windows Server
Aby móc zainstalować udział plików platformy Azure w systemie Windows, musi być dostępny port 445. Wiele organizacji blokuje port 445 z powodu zagrożeń bezpieczeństwa związanych z protokołem SMB 1. SMB 1, znany także jako CIFS (Common Internet File System), to starsza wersja protokołu systemu plików dołączona do systemów Windows i Windows Server. Protokół SMB 1 jest nieaktualny, nieefektywny i, co najważniejsze, niezabezpieczony. Na szczęście usługa Azure Files nie obsługuje protokołu SMB 1, a we wszystkich obsługiwanych wersjach systemu Windows i Windows Server ten protokół można usunąć lub wyłączyć. Firma Microsoft zawsze [zdecydowanie zaleca](https://aka.ms/stopusingsmb1) usunięcie lub wyłączenie klienta i serwera protokołu SMB 1 w systemie Windows przed rozpoczęciem korzystania z udziałów plików platformy Azure w środowisku produkcyjnym.

W poniższej tabeli zebrano szczegółowe informacje dotyczące stanu protokołu SMB 1 w poszczególnych wersjach systemu Windows:

| Wersja systemu Windows                           | Domyślny stan protokołu SMB 1 | Metoda wyłączenia lub usunięcia       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Disabled (Wyłączony)             | Usunięcie za pomocą funkcji systemu Windows |
| Windows Server w wersjach 1709+            | Disabled (Wyłączony)             | Usunięcie za pomocą funkcji systemu Windows |
| Windows 10 w wersjach 1709+                | Disabled (Wyłączony)             | Usunięcie za pomocą funkcji systemu Windows |
| Windows Server 2016                       | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows |
| Windows 10 w wersjach 1507, 1607 i 1703 | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows |
| Windows Server 2012 R2                    | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows | 
| Windows 8.1                               | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows | 
| Windows Server 2012                       | Enabled (Włączony)              | Wyłączenie za pomocą rejestru       | 
| Windows Server 2008 R2                    | Enabled (Włączony)              | Wyłączenie za pomocą rejestru       |
| Windows 7                                 | Enabled (Włączony)              | Wyłączenie za pomocą rejestru       | 

### <a name="auditing-smb-1-usage"></a>Inspekcja użycia protokołu SMB 1
> Dotyczy systemu Windows Server 2019, półrocznego kanału z systemem Windows Server (wersje 1709 i 1803), Windows Server 2016, Windows 10 (wersje 1507, 1607, 1703, 1709 i 1803), Windows Server 2012 R2 i Windows 8.1

Przed usunięciem protokołu SMB 1 ze środowiska można przeprowadzić inspekcję użycia tego protokołu, aby sprawdzić, czy ta zmiana będzie miała negatywny wpływ na działanie jakichś klientów. W przypadku wystąpienia jakichkolwiek żądań względem udziałów SMB za pośrednictwem protokołu SMB 1 zdarzenia inspekcji będą rejestrowane w dzienniku zdarzeń w ścieżce `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Aby włączyć obsługę inspekcji w systemach Windows Server 2012 R2 i Windows 8.1, należy zainstalować co najmniej aktualizację [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Aby włączyć inspekcję, wykonaj następujące polecenie cmdlet w sesji programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Usuwanie protokołu SMB 1 z systemu Windows Server
> Dotyczy systemu Windows Server 2019, półrocznego kanału z systemem Windows Server (wersje 1709 i 1803), Windows Server 2016, Windows Server 2012 R2

Aby usunąć protokół SMB 1 z wystąpienia systemu Windows Server, wykonaj następujące polecenie cmdlet w sesji programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Aby ukończyć proces usuwania, ponownie uruchom serwer. 

> [!Note]  
> Począwszy od systemów Windows 10 i Windows Server w wersji 1709, protokół SMB 1 nie jest już instalowany domyślnie i ma oddzielne funkcje systemu Windows dla klienta i serwera protokołu SMB 1. Firma Microsoft zawsze zaleca pozostawienie odinstalowanego zarówno serwera protokołu SMB 1 (`FS-SMB1-SERVER`), jak i klienta (`FS-SMB1-CLIENT`).

### <a name="removing-smb-1-from-windows-client"></a>Usuwanie protokołu SMB 1 z klienta systemu Windows
> Dotyczy systemów Windows 10 (w wersjach 1507, 1607, 1703, 1709 i 1803) oraz Windows 8.1

Aby usunąć protokół SMB 1 z klienta systemu Windows, wykonaj następujące polecenie cmdlet w sesji programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Aby ukończyć proces usuwania, ponownie uruchom komputer.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Wyłączanie protokołu SMB 1 w starszych wersjach systemu Windows lub Windows Server
> Dotyczy systemów Windows Server 2012, Windows Server 2008 R2 i Windows 7

Protokołu SMB 1 nie można całkowicie usunąć ze starszych wersji systemu Windows lub Windows Server, ale można go wyłączyć za pomocą rejestru. Aby wyłączyć protokół SMB 1, utwórz nowy klucz rejestru `SMB1` typu `DWORD` o wartości `0` w ścieżce `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Jest to łatwe do wykonania za pomocą następującego polecenia cmdlet programu PowerShell:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Po utworzeniu tego klucza rejestru należy ponownie uruchomić serwer, aby wyłączyć protokół SMB 1.

### <a name="smb-resources"></a>Zasoby dotyczące protokołu SMB
- [Stop using SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/) (Przestań używać protokołu SMB 1)
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/) (Informacje o produktach korzystających z protokołu SMB 1)
- [Discover SMB 1 in your environment with DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/) (Wykrywanie protokołu SMB 1 w środowisku za pomocą modułu DSCEA)
- [Disabling SMB 1 through Group Policy](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/) (Wyłączanie protokołu SMB 1 za pomocą zasad grupy)

## <a name="next-steps"></a>Następne kroki
Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files:
- [Planowanie wdrażania usługi Pliki Azure](storage-files-planning.md)
- [Najczęściej zadawane pytania](../storage-files-faq.md)
- [Rozwiązywanie problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)      
