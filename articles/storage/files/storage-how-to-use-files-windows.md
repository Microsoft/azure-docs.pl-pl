---
title: Korzystanie z udziału plików platformy Azure w systemie Windows | Microsoft Docs
description: Dowiedz się, jak używać udziałów plików platformy Azure w systemach Windows i Windows Server. Używaj udziałów plików platformy Azure z protokołem SMB 3,0 w instalacjach systemu Windows działających lokalnie lub na maszynach wirtualnych platformy Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94626898"
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
| Windows Server 2012 z dodatkiem R2 | SMB 3.0 | Tak | Tak |
| Windows Server 2012 | SMB 3.0 | Tak | Tak |
| Windows 7<sup>3</sup> | SMB 2.1 | Tak | Nie |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Tak | Nie |

<sup>1</sup> Windows 10, wersje 1507, 1607, 1803, 1809, 1903, 1909 i 2004.  
<sup>2</sup> Windows Server, wersje 1809, 1903, 1909, 2004.  
<sup>3</sup> Zakończono regularne wsparcie firmy Microsoft dla systemów Windows 7 i Windows Server 2008 R2. Dodatkowe wsparcie dla aktualizacji zabezpieczeń można zakupić tylko za pomocą [programu Extended Security Update (ESU)](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). Zdecydowanie zalecamy Migrowanie z tych systemów operacyjnych.

> [!Note]  
> Zawsze zalecamy pobranie najnowszej aktualizacji KB dla danej wersji systemu Windows.

## <a name="prerequisites"></a>Wymagania wstępne 

Otwarty port 445: protokół SMB wymaga otwartego portu TCP 445; połączenia zakończą się niepowodzeniem, jeśli port 445 będzie zablokowany. Możesz sprawdzić, czy Zapora blokuje port 445 przy użyciu `Test-NetConnection` polecenia cmdlet. Aby dowiedzieć się, jak obejść zablokowany port 445, zobacz sekcję [Przyczyna 1: port 445 jest zablokowany](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) w przewodniku rozwiązywania problemów systemu Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Korzystanie z udziału plików platformy Azure w systemie Windows
Aby używać udziału plików platformy Azure w systemie Windows, musisz go zainstalować, czyli przypisać do niego literę dysku bądź ścieżkę do punktu instalacji, lub uzyskiwać do niego dostęp za pośrednictwem jego [ścieżki UNC](/windows/win32/fileio/naming-a-file). 

W tym artykule jest wykorzystywany klucz konta magazynu w celu uzyskania dostępu do udziału plików. Klucz konta magazynu to klucz administratora dla konta magazynu, w tym uprawnienia administratora do wszystkich plików i folderów w udziale plików, do którego uzyskujesz dostęp, a także dla wszystkich udziałów plików i innych zasobów magazynu (obiektów blob, kolejek, tabel itp.) zawartych w ramach konta magazynu. Jeśli nie jest to wystarczające dla obciążenia, [Azure File Sync](storage-sync-files-planning.md) mogą być używane lub można użyć [uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB](storage-files-active-directory-overview.md).

Typowym sposobem na przeniesienie na platformę Azure aplikacji biznesowych (LOB), które oczekują obsługi udziału plików SMB, jest użycie udziału plików platformy Azure jako alternatywy do uruchamiania dedykowanego serwera plików Windows na maszynie wirtualnej platformy Azure. Ważnym zagadnieniem warunkującym pomyślną migrację aplikacji biznesowej do korzystania z udziału plików platformy Azure jest to, że wiele aplikacji biznesowych działa w kontekście dedykowanego konta usługi z ograniczonymi uprawnieniami systemowymi, a nie w kontekście konta administracyjnego maszyny wirtualnej. W związku z tym należy się upewnić, że poświadczenia dla udziału plików platformy Azure zostały zainstalowane/zapisane w kontekście konta usługi, a nie konta administracyjnego.

### <a name="mount-the-azure-file-share"></a>Instalowanie udziału plików platformy Azure

Azure Portal zapewnia skrypt, którego można użyć do zainstalowania udziału plików bezpośrednio na hoście. Zalecamy korzystanie z tego dostarczonego skryptu.

Aby uzyskać ten skrypt:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Przejdź do konta magazynu zawierającego udział plików, który chcesz zainstalować.
1. Wybierz pozycję **Udziały plików**.
1. Wybierz udział plików, który chcesz zainstalować.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="przyklad":::

1. Wybierz pozycję **Połącz**.

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Zrzut ekranu przedstawiający ikonę połączenia dla udziału plików.":::

1. Wybierz literę dysku, w której ma zostać zainstalowany udział.
1. Skopiuj podany skrypt.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Przykładowy tekst":::

1. Wklej skrypt do powłoki na hoście, na którym chcesz zainstalować udział plików, a następnie uruchom go.

Udział plików platformy Azure został już zainstalowany.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Instalowanie udziału plików platformy Azure za pomocą Eksploratora plików
> [!Note]  
> Należy pamiętać, że następujące instrukcje dotyczą systemu Windows 10 i mogą być nieco inne w starszych wersjach. 

1. Otwórz Eksploratora plików. Można to zrobić przy użyciu menu Start lub przez naciśnięcie skrótu Win+E.

1. Przejdź do **tego komputera** po lewej stronie okna. Spowoduje to zmianę menu dostępnego na wstążce. W menu komputer wybierz pozycję **Mapuj dysk sieciowy**.
    
    ![Zrzut ekranu przedstawiający menu rozwijane „Mapuj dysk sieciowy”](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Wybierz literę dysku, a następnie wprowadź ścieżkę UNC Format ścieżki UNC `\\<storageAccountName>.file.core.windows.net\<fileShareName>` . Na przykład: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Zrzut ekranu przedstawiający okno dialogowe „Mapowanie dysku sieciowego”](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Jako nazwy użytkownika użyj nazwy konta magazynu poprzedzonej ciągiem `AZURE\`, a jako hasła użyj klucza konta magazynu.
    
    ![Zrzut ekranu okna dialogowego poświadczeń sieciowych](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Użyj udziału plików platformy Azure zgodnie z potrzebami.
    
    ![Udział plików platformy Azure jest teraz zainstalowany](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Gdy zajdzie potrzeba odinstalowania udziału plików platformy Azure, możesz to zrobić przez kliknięcie prawym przyciskiem myszy wpisu dla udziału w obszarze **Lokalizacje sieciowe** w Eksploratorze plików i wybranie polecenia **Odłącz**.

### <a name="accessing-share-snapshots-from-windows"></a>Dostęp do migawek udziałów z systemu Windows
Jeśli migawkę udziału utworzono ręcznie lub automatycznie za pomocą skryptu bądź usługi, takiej jak Azure Backup, możesz wyświetlić poprzednie wersje udziału, katalogu lub konkretnego pliku z udziału plików w systemie Windows. Migawkę udziału można wykonać przy użyciu [Azure PowerShell](storage-how-to-use-files-powershell.md), [interfejsu wiersza polecenia platformy Azure](storage-how-to-use-files-cli.md)lub [Azure Portal](storage-how-to-use-files-portal.md).

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
| Windows Server 2019                       | Disabled             | Usunięcie za pomocą funkcji systemu Windows |
| Windows Server w wersjach 1709+            | Disabled             | Usunięcie za pomocą funkcji systemu Windows |
| Windows 10 w wersjach 1709+                | Disabled             | Usunięcie za pomocą funkcji systemu Windows |
| Windows Server 2016                       | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows |
| Windows 10 w wersjach 1507, 1607 i 1703 | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows |
| Windows Server 2012 z dodatkiem R2                    | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows | 
| Windows 8.1                               | Enabled (Włączony)              | Usunięcie za pomocą funkcji systemu Windows | 
| Windows Server 2012                       | Enabled (Włączony)              | Wyłączenie za pomocą rejestru       | 
| Windows Server 2008 z dodatkiem R2                    | Enabled (Włączony)              | Wyłączenie za pomocą rejestru       |
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
- [Discover SMB 1 in your environment with DSCEA](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea) (Wykrywanie protokołu SMB 1 w środowisku za pomocą modułu DSCEA)
- [Disabling SMB 1 through Group Policy](/archive/blogs/secguide/disabling-smbv1-through-group-policy) (Wyłączanie protokołu SMB 1 za pomocą zasad grupy)

## <a name="next-steps"></a>Następne kroki
Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files:
- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Często zadawane pytania](./storage-files-faq.md)
- [Rozwiązywanie problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)