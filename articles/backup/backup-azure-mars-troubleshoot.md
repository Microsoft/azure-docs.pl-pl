---
title: Rozwiązywanie problemów z agentem Azure Backup
description: W tym artykule dowiesz się, jak rozwiązywać problemy z instalacją i rejestracją agenta Azure Backup.
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: a15f8a4531bc31dab5b99e125454b0d9c4fd4521
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80421272"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Rozwiązywanie problemów z agentem Microsoft Azure Recovery Services (MARS)

W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić podczas konfigurowania, rejestracji, tworzenia kopii zapasowej i przywracania.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zalecamy sprawdzenie następujących danych przed rozpoczęciem rozwiązywania problemów z usługą Microsoft Azure Recovery Services (MARS) Agent:

- [Upewnij się, że Agent Mars jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Upewnij się, że masz połączenie sieciowe między agentem Mars i platformą Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Upewnij się, że Usługa MARS jest uruchomiona (w konsoli usługi). Jeśli chcesz, uruchom ponownie, a następnie spróbuj ponownie wykonać operację.
- [Zapewnij 5% do 10% wolnego miejsca na woluminie w lokalizacji folderu](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe przeszkadza w Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Jeśli zaplanowane tworzenie kopii zapasowej zakończy się niepowodzeniem, ale ręczne wykonywanie kopii zapasowych, zobacz [kopie zapasowe nie są uruchamiane według harmonogramu](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Upewnij się, że system operacyjny ma najnowsze aktualizacje.
- [Upewnij się, że nieobsługiwane dyski i pliki z nieobsługiwanymi atrybutami są wyłączone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Upewnij się, że zegar w chronionym systemie jest skonfigurowany do poprawnej strefy czasowej.
- [Upewnij się, że na serwerze zainstalowano .NET Framework 4.5.2 lub nowszy](https://www.microsoft.com/download/details.aspx?id=30653).
- Jeśli próbujesz ponownie zarejestrować serwer w magazynie:
  - Upewnij się, że agent zostanie odinstalowany na serwerze i usunięty z portalu.
  - Użyj tego samego hasła, które zostało początkowo użyte do zarejestrowania serwera.
- W przypadku kopii zapasowych w trybie offline przed rozpoczęciem tworzenia kopii zapasowej upewnij się, że na komputerze źródłowym i skopiuj jest zainstalowany program Azure PowerShell 3.7.0.
- Jeśli Agent kopii zapasowych jest uruchomiony na maszynie wirtualnej platformy Azure, zobacz [ten artykuł](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

**Komunikat o błędzie**: podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. (IDENTYFIKATOR: 34513)

| Przyczyna | Zalecane akcje |
| ---     | ---    |
| **Poświadczenia magazynu są nieprawidłowe** <br/> <br/> Pliki poświadczeń magazynu mogą być uszkodzone lub mogły wygasnąć. (Na przykład mogły zostać pobrane ponad 48 godzin przed upływem czasu rejestracji).| Pobierz nowe poświadczenia z magazynu Recovery Services w Azure Portal. (Zobacz krok 6 w sekcji [pobieranie agenta Mars](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) ). Następnie wykonaj następujące kroki: <ul><li> Jeśli zainstalowano już i zarejestrowano usługę MARS, Otwórz konsolę MMC agent Microsoft Azure Backup, a następnie wybierz pozycję **zarejestruj serwer** w okienku **Akcje** , aby zakończyć rejestrację przy użyciu nowych poświadczeń. <br/> <li> Jeśli nowa instalacja nie powiedzie się, spróbuj zainstalować ją ponownie przy użyciu nowych poświadczeń.</ul> **Uwaga**: Jeśli pobrano wiele plików poświadczeń magazynu, tylko najnowszy plik jest ważny przez następne 48 godzin. Zalecamy pobranie nowego pliku poświadczeń magazynu.
| **Serwer proxy/zapora blokuje rejestrację** <br/>lub <br/>**Brak łączności z Internetem** <br/><br/> Jeśli komputer lub serwer proxy ma ograniczoną łączność z Internetem i nie masz dostępu do wymaganych adresów URL, rejestracja zakończy się niepowodzeniem.| Wykonaj następujące kroki:<br/> <ul><li> Pracuj z zespołem IT, aby upewnić się, że system ma łączność z Internetem.<li> Jeśli nie masz serwera proxy, upewnij się, że opcja proxy nie jest zaznaczona podczas rejestrowania agenta. [Sprawdź ustawienia serwera proxy](#verifying-proxy-settings-for-windows).<li> Jeśli masz zaporę/serwer proxy, skontaktuj się z zespołem sieci, aby upewnić się, że te adresy URL i adresy IP mają dostęp:<br/> <br> **Adresy URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Adresy IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Spróbuj zarejestrować się ponownie po wykonaniu powyższych kroków rozwiązywania problemów.<br></br> Jeśli nawiązano połączenie za pośrednictwem usługi Azure ExpressRoute, upewnij się, że ustawienia zostały skonfigurowane zgodnie z opisem w [pomocy technicznej usługi Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Oprogramowanie antywirusowe blokuje rejestrację** | Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, należy dodać niezbędne reguły wykluczania do skanowania oprogramowania antywirusowego dla tych plików i folderów: <br/><ul> <li> Pliku cbengine. exe <li> CSC. exe<li> Folder tymczasowy. Domyślna lokalizacja to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Folder bin w katalogu C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Zalecenia dodatkowe

- Przejdź do C:/Windows/Temp i sprawdź, czy istnieje więcej plików niż 60 000 lub 65 000 z rozszerzeniem. tmp. Jeśli istnieją, usuń te pliki.
- Upewnij się, że data i godzina komputera są zgodne z lokalną strefą czasową.
- Upewnij się, że [te lokacje](install-mars-agent.md#verify-internet-access) są dodawane do zaufanych witryn programu Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Weryfikowanie ustawień serwera proxy dla systemu Windows

1. Pobierz PsExec ze strony [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) .
1. Uruchom `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` polecenie w wierszu polecenia z podwyższonym poziomem uprawnień.

   To polecenie spowoduje otwarcie programu Internet Explorer.
1. Przejdź do pozycji **Narzędzia** > **Opcje** > internetowe**połączenia** > **sieci LAN**.
1. Sprawdź ustawienia serwera proxy dla konta System.
1. Jeśli nie skonfigurowano serwera proxy i podano szczegóły serwera proxy, Usuń szczegóły.
1. Jeśli skonfigurowano serwer proxy, a szczegóły serwera proxy są niepoprawne, upewnij się, że **adres IP serwera proxy** i szczegóły **portu** są poprawne.
1. Zamknij program Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nie można pobrać pliku poświadczeń magazynu

| Error   | Zalecane akcje |
| ---     | ---    |
|Nie można pobrać pliku poświadczeń magazynu. (IDENTYFIKATOR: 403) | <ul><li> Spróbuj pobrać poświadczenia magazynu za pomocą innej przeglądarki lub wykonaj następujące czynności: <ul><li> Uruchom program Internet Explorer. Wybierz klawisz F12. </li><li> Przejdź do karty **Sieć** i wyczyść pamięć podręczną i pliki cookie. </li> <li> Odśwież stronę.<br></li></ul> <li> Sprawdź, czy subskrypcja jest wyłączona/wygasła.<br></li> <li> Sprawdź, czy żadna Reguła zapory blokuje pobieranie. <br></li> <li> Upewnij się, że limit magazynu (50 maszyn na magazyn) nie został wyczerpany.<br></li>  <li> Upewnij się, że użytkownik ma uprawnienia Azure Backup wymagane do pobrania poświadczeń magazynu i zarejestrowania serwera w magazynie. [Aby zarządzać Azure Backup punktów odzyskiwania, zobacz temat używanie Access Control opartych na rolach](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent usługi Microsoft Azure Recovery Service nie mógł połączyć się z usługą Microsoft Azure Backup

| Error  | Możliwa przyczyna | Zalecane akcje |
| ---     | ---     | ---    |
| <br /><ul><li>Agent usługi odzyskiwania Microsoft Azure nie mógł nawiązać połączenia z Microsoft Azure Backup. (IDENTYFIKATOR: 100050) Sprawdź ustawienia sieci i upewnij się, że możesz nawiązać połączenie z Internetem.<li>(407) wymagane jest uwierzytelnianie serwera proxy. |Serwer proxy blokuje połączenie. |  <ul><li>W programie Internet Explorer przejdź do pozycji **Narzędzia** > **Opcje** > internetowe**zabezpieczenia** > **internetowe Internet**. Wybierz pozycję **Poziom niestandardowy** i przewiń w dół do sekcji **Pobieranie pliku** . Wybierz pozycję **Włącz**.<p>Może być również konieczne dodanie adresów [URL i adresów IP](install-mars-agent.md#verify-internet-access) do zaufanych witryn w programie Internet Explorer.<li>Zmień ustawienia tak, aby korzystały z serwera proxy. Następnie podaj szczegóły serwera proxy.<li> Jeśli maszyna ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na te [adresy URL i adresy IP](install-mars-agent.md#verify-internet-access). <li>Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, Wyklucz te pliki ze skanowania antywirusowego: <ul><li>Pliku cbengine. exe (zamiast DPMRA. exe).<li>CSC. exe (powiązane z .NET Framework). Dla każdej wersji .NET Framework zainstalowanej na serwerze istnieje plik CSC. exe. Wyklucz pliki CSC. exe dla wszystkich wersji .NET Framework na serwerze, którego to dotyczy. <li>Folder tymczasowy lub lokalizacja pamięci podręcznej. <br>Domyślna lokalizacja folderu tymczasowego lub ścieżki pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Folder bin w katalogu C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych

| Error | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| <br />Nie można ustawić klucza szyfrowania dla bezpiecznych kopii zapasowych. Aktywacja nie powiodła się w całości, ale hasło szyfrowania zostało zapisane w następującym pliku. |<li>Serwer jest już zarejestrowany w innym magazynie.<li>Podczas konfiguracji hasło zostało uszkodzone.| Wyrejestruj serwer z magazynu i zarejestruj go ponownie, podając nowe hasło.

## <a name="the-activation-did-not-complete-successfully"></a>Aktywacja nie została pomyślnie ukończona

| Error  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|<br />Aktywacja nie została pomyślnie ukończona. Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FC07]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej firmy Microsoft.     | <li> Folder tymczasowy znajduje się na woluminie, na którym nie ma wystarczającej ilości miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony. <li> Brak pliku OnlineBackup. KEK.         | <li>Uaktualnij do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta Mars.<li>Przenieś folder tymczasowy lub lokalizację pamięci podręcznej na wolumin z ilością wolnego miejsca wynoszącą od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby prawidłowo przenieść lokalizację pamięci podręcznej, zapoznaj się z instrukcjami w [temacie typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Upewnij się, że plik OnlineBackup. KEK jest obecny. <br>*Domyślną lokalizacją folderu tymczasowego lub ścieżką pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Hasło szyfrowania nie zostało prawidłowo skonfigurowane

| Error  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
| <br />Błąd 34506. Hasło szyfrowania zapisane na tym komputerze nie jest prawidłowo skonfigurowane.    | <li> Folder tymczasowy znajduje się na woluminie, na którym nie ma wystarczającej ilości miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony. <li> Brak pliku OnlineBackup. KEK.        | <li>Uaktualnij do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta Mars.<li>Przenieś folder tymczasowy lub lokalizację pamięci podręcznej na wolumin z ilością wolnego miejsca wynoszącą od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby prawidłowo przenieść lokalizację pamięci podręcznej, zapoznaj się z instrukcjami w [temacie typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Upewnij się, że plik OnlineBackup. KEK jest obecny. <br>*Domyślną lokalizacją folderu tymczasowego lub ścieżką pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Kopie zapasowe nie są uruchamiane zgodnie z harmonogramem

Jeśli zaplanowane kopie zapasowe nie są wyzwalane automatycznie, ale ręczne kopie zapasowe działają prawidłowo, spróbuj wykonać następujące czynności:

- Upewnij się, że harmonogram kopii zapasowych systemu Windows Server nie powoduje konfliktu z harmonogramem tworzenia kopii zapasowych plików i folderów platformy Azure.

- Upewnij się, że stan kopii zapasowej online to **enable**. Aby sprawdzić stan, wykonaj następujące kroki:

  1. W Harmonogram zadań rozwiń pozycję **Microsoft** i wybierz pozycję **kopia zapasowa online**.
  1. Kliknij dwukrotnie pozycję **Microsoft-OnlineBackup** i przejdź do karty **wyzwalacze** .
  1. Sprawdź, czy stan jest ustawiony na **włączone**. Jeśli tak nie jest, wybierz opcję **Edytuj**, wybierz opcję **włączone**, a następnie wybierz przycisk **OK**.

- Upewnij się, że konto użytkownika wybrane do uruchamiania zadania jest grupą **system** lub **Administratorzy lokalni** na serwerze. Aby sprawdzić konto użytkownika, przejdź do karty **Ogólne** i Sprawdź opcje **zabezpieczeń** .

- Upewnij się, że na serwerze jest zainstalowany program PowerShell 3,0 lub nowszy. Aby sprawdzić wersję programu PowerShell, Uruchom to polecenie i sprawdź, czy `Major` numer wersji to 3 lub nowszy:

  `$PSVersionTable.PSVersion`

- Upewnij się, że `PSMODULEPATH` Ta ścieżka jest częścią zmiennej środowiskowej:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Jeśli zasady wykonywania programu PowerShell dla `LocalMachine` programu mają `restricted`ustawioną wartość, polecenie cmdlet programu PowerShell wyzwalające zadanie tworzenia kopii zapasowej może zakończyć się niepowodzeniem. Uruchom te polecenia w trybie podniesionych uprawnień, aby sprawdzić i ustawić zasady wykonywania `Unrestricted` na `RemoteSigned`albo:

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- Upewnij się, że nie brakuje plików MSOnlineBackup modułu programu PowerShell lub nie są one uszkodzone. W przypadku brakujących lub uszkodzonych plików wykonaj następujące kroki:

  1. Z dowolnego komputera z agentem MARS, który działa prawidłowo, skopiuj folder MSOnlineBackup z katalogu C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na problematycznej maszynie wklej skopiowane pliki w tej samej lokalizacji folderu (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Jeśli na maszynie istnieje już folder MSOnlineBackup, wklej do niego pliki lub Zastąp wszystkie istniejące pliki.

> [!TIP]
> Aby zapewnić spójność zmian, należy ponownie uruchomić serwer po wykonaniu powyższych kroków.

## <a name="resource-not-provisioned-in-service-stamp"></a>Zasób nie został zainicjowany w sygnaturze usługi

Error | Możliwe przyczyny | Zalecane akcje
--- | --- | ---
Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi "zasób nie został zainicjowany w sygnaturze usługi". Spróbuj ponownie wykonać operację po pewnym czasie. (IDENTYFIKATOR: 230006) | Zmieniono nazwę chronionego serwera. | <li> Zmień nazwę serwera z powrotem na oryginalną nazwę zarejestrowanego w magazynie. <br> <li> Zarejestruj ponownie serwer w magazynie przy użyciu nowej nazwy.


## <a name="troubleshoot-restore-problems"></a>Rozwiązywanie problemów z przywracaniem

Azure Backup może nie pomyślnie zainstalować woluminu odzyskiwania, nawet po kilku minutach. Podczas procesu mogą pojawić się komunikaty o błędach. Aby rozpocząć odzyskiwanie normalnie, wykonaj następujące czynności:

1. Anuluj proces instalacji, jeśli jest uruchomiony przez kilka minut.

2. Sprawdź, czy masz najnowszą wersję agenta kopii zapasowej. Aby sprawdzić wersję, w okienku **Akcje** konsoli Mars wybierz pozycję **Informacje o Microsoft Azure Recovery Services agencie**. Upewnij się, że numer **wersji** jest równy lub większy niż wersja wymieniona w [tym artykule](https://go.microsoft.com/fwlink/?linkid=229525). Wybierz ten link [, aby pobrać najnowszą wersję](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Przejdź do pozycji **Menedżer urządzeń** > **Storage controllers** i Znajdź **inicjatora iSCSI firmy Microsoft**. Jeśli go wyszukasz, przejdź bezpośrednio do kroku 7.

4. Jeśli nie możesz znaleźć usługi inicjatora iSCSI firmy Microsoft, spróbuj znaleźć wpis w obszarze **Menedżer urządzeń** > **Kontrolery magazynu** o nazwie **nieznane urządzenie** z identyfikatorem sprzętu **ROOT\ISCSIPRT**.

5. Kliknij prawym przyciskiem myszy pozycję **nieznane urządzenie** i wybierz polecenie **Aktualizuj oprogramowanie sterownika**.

6. Zaktualizuj sterownik, wybierając opcję **automatycznego wyszukiwania zaktualizowanego oprogramowania sterownika**. Ta aktualizacja powinna zmienić **nieznane urządzenie** na **inicjatora iSCSI firmy Microsoft**:

    ![Zrzut ekranu przedstawiający Azure Backup Menedżer urządzeń z wyróżnionymi kontrolerami magazynu](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Przejdź do usługi **Menedżera** > zadań **(lokalnego)** > **Usługa inicjatora iSCSI firmy Microsoft**:

    ![Zrzut ekranu Azure Backup Menedżera zadań z wyróżnionymi usługami (lokalnymi)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Uruchom ponownie usługę inicjatora iSCSI firmy Microsoft. Aby to zrobić, kliknij prawym przyciskiem myszy usługę i wybierz polecenie **Zatrzymaj**. Następnie kliknij ponownie prawym przyciskiem myszy i wybierz polecenie **Uruchom**.

9. Ponów próbę odzyskiwania przy użyciu funkcji [natychmiastowego przywracania](backup-instant-restore-capability.md).

Jeśli odzyskiwanie nadal kończy się niepowodzeniem, należy ponownie uruchomić serwer lub klienta. Jeśli nie chcesz ponownie uruchamiać programu lub jeśli odzyskiwanie nadal kończy się niepowodzeniem nawet po ponownym uruchomieniu serwera, spróbuj wykonać [odzyskiwanie z innego komputera](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Rozwiązywanie problemów z pamięcią podręczną

Operacja tworzenia kopii zapasowej może zakończyć się niepowodzeniem, jeśli folder pamięci podręcznej (również określony jako folder tymczasowy) jest niepoprawnie skonfigurowany, brakujące wymagania wstępne lub ma ograniczony dostęp.

### <a name="prerequisites"></a>Wymagania wstępne

W przypadku operacji agenta MARS do pomyślnego przeprowadzenia w folderze pamięci podręcznej musi być zgodna z poniższymi wymaganiami:

- [Upewnij się, że 5% do 10% wolnego miejsca na woluminie jest dostępne w lokalizacji folderu tymczasowego](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Upewnij się, że lokalizacja folderu tymczasowego jest prawidłowa i dostępna](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Upewnij się, że atrybuty pliku w folderze pamięci podręcznej są obsługiwane](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Upewnij się, że zajęte miejsce do magazynowania kopii w tle jest wystarczające dla procesu tworzenia kopii zapasowej](#increase-shadow-copy-storage)
- [Upewnij się, że nie istnieją żadne inne procesy (np. oprogramowanie antywirusowe) ograniczające dostęp do folderu pamięci podręcznej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Zwiększ magazyn kopii w tle

Operacje tworzenia kopii zapasowej mogą zakończyć się niepowodzeniem, jeśli brakuje miejsca do magazynowania kopii w tle wymaganego do ochrony źródła danych. Aby rozwiązać ten problem, Zwiększ ilość miejsca do magazynowania kopii w tle na chronionym woluminie, korzystając z usługi vssadmin, jak pokazano poniżej:

- Sprawdź bieżące miejsce do magazynowania w tle z wiersza polecenia z podwyższonym poziomem uprawnień:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Zwiększ ilość miejsca w magazynie w tle przy użyciu poniższego polecenia:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Inny proces lub program antywirusowy blokujący dostęp do folderu pamięci podręcznej

Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, należy dodać niezbędne reguły wykluczania do skanowania oprogramowania antywirusowego dla tych plików i folderów:  

- Folder tymczasowy. Domyślna lokalizacja to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- Folder bin w katalogu C:\Program Files\Microsoft Azure Recovery Services Agent\Bin
- Pliku cbengine. exe
- CSC. exe

## <a name="common-issues"></a>Typowe problemy

W tej sekcji opisano typowe błędy występujące podczas korzystania z agenta MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Komunikat o błędzie | Zalecana akcja |
-- | --
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do sumy kontrolnej kopii zapasowej przechowywanej w lokalizacji tymczasowej | Aby rozwiązać ten problem, wykonaj poniższe czynności i ponownie uruchom serwer <br/> - [Sprawdź, czy istnieje program antywirusowy lub inne procesy blokujące pliki lokalizacji tymczasowej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja tymczasowa jest prawidłowa i dostępna dla agenta Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Komunikat o błędzie | Zalecana akcja |
-- | --
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do lokalizacji tymczasowej w celu zainicjowania wirtualnego dysku twardego | Aby rozwiązać ten problem, wykonaj poniższe czynności i ponownie uruchom serwer <br/> - [Sprawdź, czy istnieje program antywirusowy lub inne procesy blokujące pliki lokalizacji tymczasowej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja tymczasowa jest prawidłowa i dostępna dla agenta Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Komunikat o błędzie | Zalecana akcja |
-- | --
Tworzenie kopii zapasowej nie powiodło się z powodu niewystarczającej ilości miejsca w magazynie, w której znajduje się folder | Aby rozwiązać ten problem, sprawdź poniższe kroki i spróbuj ponownie wykonać operację:<br/>- [Upewnij się, że Agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Weryfikowanie i rozwiązywanie problemów z magazynem, które wpływają na miejsce na kopie zapasowe](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Komunikat o błędzie | Zalecana akcja |
-- | --
Nie można odnaleźć zmian w pliku. Taka sytuacja może mieć różne przyczyny. Ponów próbę wykonania operacji | Aby rozwiązać ten problem, sprawdź poniższe kroki i spróbuj ponownie wykonać operację:<br/> - [Upewnij się, że Agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Weryfikowanie i rozwiązywanie problemów z magazynem, które wpływają na miejsce na kopie zapasowe](#prerequisites)

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [tworzenia kopii zapasowej systemu Windows Server z agentem Azure Backup](tutorial-backup-windows-server-to-azure.md).
- Jeśli chcesz przywrócić kopię zapasową, zobacz [Przywracanie plików do maszyny z systemem Windows](backup-azure-restore-windows-server.md).
