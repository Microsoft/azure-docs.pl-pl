---
title: Rozwiązywanie problemów z agentem Azure Backup
description: W tym artykule dowiesz się, jak rozwiązywać problemy z instalacją i rejestracją agenta Azure Backup.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 3203d5604f1bd5db9cf579af01b2ae6f34032d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467616"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Rozwiązywanie problemów z agentem Microsoft Azure Recovery Services (MARS)

W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić podczas konfigurowania, rejestracji, tworzenia kopii zapasowej i przywracania.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zalecamy sprawdzenie następujących danych przed rozpoczęciem rozwiązywania problemów z usługą Microsoft Azure Recovery Services (MARS) Agent:

- [Upewnij się, że Agent Mars jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Upewnij się, że masz połączenie sieciowe między agentem Mars i platformą Azure](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- Upewnij się, że Usługa MARS jest uruchomiona (w konsoli usługi). Jeśli chcesz, uruchom ponownie, a następnie spróbuj ponownie wykonać operację.
- [Zapewnij 5% do 10% wolnego miejsca na woluminie w lokalizacji folderu](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder).
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Jeśli zadanie tworzenia kopii zapasowej zostało ukończone z ostrzeżeniami, zobacz [zadania tworzenia kopii zapasowej zakończone z ostrzeżeniem](#backup-jobs-completed-with-warning)
- Jeśli zaplanowane tworzenie kopii zapasowej zakończy się niepowodzeniem, ale ręczne wykonywanie kopii zapasowych, zobacz [kopie zapasowe nie są uruchamiane według harmonogramu](#backups-dont-run-according-to-schedule)
- Upewnij się, że system operacyjny ma najnowsze aktualizacje.
- [Upewnij się, że nieobsługiwane dyski i pliki z nieobsługiwanymi atrybutami są wyłączone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Upewnij się, że zegar w chronionym systemie jest skonfigurowany do poprawnej strefy czasowej.
- [Upewnij się, że na serwerze zainstalowano .NET Framework 4.5.2 lub nowszy](https://www.microsoft.com/download/details.aspx?id=30653).
- Jeśli próbujesz ponownie zarejestrować serwer w magazynie:
  - Upewnij się, że agent zostanie odinstalowany na serwerze i usunięty z portalu.
  - Użyj tego samego hasła, które zostało początkowo użyte do zarejestrowania serwera.
- W przypadku kopii zapasowych w trybie offline przed rozpoczęciem tworzenia kopii zapasowej upewnij się, że na komputerze źródłowym i skopiuj jest zainstalowany program Azure PowerShell 3.7.0.
- Jeśli Agent kopii zapasowych jest uruchomiony na maszynie wirtualnej platformy Azure, zobacz [ten artykuł](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine).

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

**Komunikat o błędzie**: podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. (IDENTYFIKATOR: 34513)

| Przyczyna | Zalecane akcje |
| ---     | ---    |
| **Poświadczenia magazynu są nieprawidłowe** <br/> <br/> Pliki poświadczeń magazynu mogą być uszkodzone lub mogły wygasnąć lub mieć inne rozszerzenie pliku niż *. vaultCredentials*. (Na przykład mogły zostać pobrane więcej niż 10 dni przed czasem rejestracji).| [Pobierz nowe poświadczenia](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file) z magazynu Recovery Services w Azure Portal. Następnie wykonaj następujące kroki: <ul><li> Jeśli zainstalowano już i zarejestrowano usługę MARS, Otwórz konsolę MMC agent Microsoft Azure Backup. Następnie wybierz pozycję **zarejestruj serwer** w okienku **Akcje** , aby zakończyć rejestrację przy użyciu nowych poświadczeń. <br/> <li> Jeśli nowa instalacja nie powiedzie się, spróbuj zainstalować ją ponownie przy użyciu nowych poświadczeń.</ul> **Uwaga**: Jeśli pobrano wiele plików poświadczeń magazynu, tylko najnowszy plik jest ważny przez następne 10 dni. Zalecamy pobranie nowego pliku poświadczeń magazynu.
| **Serwer proxy/zapora blokuje rejestrację** <br/>lub <br/>**Brak łączności z Internetem** <br/><br/> Jeśli komputer lub serwer proxy ma ograniczoną łączność z Internetem i nie masz dostępu do wymaganych adresów URL, rejestracja zakończy się niepowodzeniem.| Wykonaj następujące kroki:<br/> <ul><li> Pracuj z zespołem IT, aby upewnić się, że system ma łączność z Internetem.<li> Jeśli nie masz serwera proxy, upewnij się, że opcja proxy nie jest zaznaczona podczas rejestrowania agenta. [Sprawdź ustawienia serwera proxy](#verifying-proxy-settings-for-windows).<li> Jeśli masz zaporę/serwer proxy, skontaktuj się z zespołem sieci, aby upewnić się, że te adresy URL i adresy IP mają dostęp:<br/> <br> **Adresy URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**Adresy IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Spróbuj zarejestrować się ponownie po wykonaniu powyższych kroków rozwiązywania problemów.<br></br> Jeśli połączenie jest realizowane za pośrednictwem usługi Azure ExpressRoute, upewnij się, że ustawienia zostały skonfigurowane zgodnie z opisem w [pomocy technicznej usługi Azure ExpressRoute](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Oprogramowanie antywirusowe blokuje rejestrację** | Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, należy dodać niezbędne reguły wykluczania do skanowania oprogramowania antywirusowego dla tych plików i folderów: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Folder tymczasowy. Domyślna lokalizacja to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Folder bin w katalogu C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Zalecenia dodatkowe

- Przejdź do C:/Windows/Temp i sprawdź, czy istnieje więcej plików niż 60 000 lub 65 000 z rozszerzeniem. tmp. Jeśli istnieją, usuń te pliki.
- Upewnij się, że data i godzina komputera są zgodne z lokalną strefą czasową.
- Upewnij się, że [te lokacje](install-mars-agent.md#verify-internet-access) są dodawane do zaufanych witryn programu Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Weryfikowanie ustawień serwera proxy dla systemu Windows

1. Pobierz PsExec ze strony [Sysinternals](/sysinternals/downloads/psexec) .
1. Uruchom `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` polecenie w wierszu polecenia z podwyższonym poziomem uprawnień.

   To polecenie spowoduje otwarcie programu Internet Explorer.
1. Przejdź do pozycji **Narzędzia**  >  **Opcje internetowe**  >  **połączenia**  >  **sieci LAN**.
1. Sprawdź ustawienia serwera proxy dla konta System.
1. Jeśli nie skonfigurowano serwera proxy i podano szczegóły serwera proxy, Usuń szczegóły.
1. Jeśli skonfigurowano serwer proxy, a szczegóły serwera proxy są niepoprawne, upewnij się, że **adres IP serwera proxy** i szczegóły **portu** są poprawne.
1. Zamknij program Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nie można pobrać pliku poświadczeń magazynu

| Błąd   | Zalecane akcje |
| ---     | ---    |
|Nie można pobrać pliku poświadczeń magazynu. (IDENTYFIKATOR: 403) | <ul><li> Spróbuj pobrać poświadczenia magazynu za pomocą innej przeglądarki lub wykonaj następujące czynności: <ul><li> Uruchom program Internet Explorer. Wybierz klawisz F12. </li><li> Przejdź do karty **Sieć** i wyczyść pamięć podręczną i pliki cookie. </li> <li> Odśwież stronę.<br></li></ul> <li> Sprawdź, czy subskrypcja jest wyłączona/wygasła.<br></li> <li> Sprawdź, czy żadna Reguła zapory blokuje pobieranie. <br></li> <li> Upewnij się, że limit magazynu (50 maszyn na magazyn) nie został wyczerpany.<br></li>  <li> Upewnij się, że użytkownik ma uprawnienia Azure Backup wymagane do pobrania poświadczeń magazynu i zarejestrowania serwera w magazynie. [Aby zarządzać Azure Backup punktami odzyskiwania, zobacz Używanie kontroli dostępu opartej na rolach na platformie Azure](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent usługi Microsoft Azure Recovery Service nie mógł połączyć się z usługą Microsoft Azure Backup

| Błąd  | Możliwa przyczyna | Zalecane akcje |
| ---     | ---     | ---    |
| <br /><ul><li>Agent usługi odzyskiwania Microsoft Azure nie mógł nawiązać połączenia z Microsoft Azure Backup. (IDENTYFIKATOR: 100050) Sprawdź ustawienia sieci i upewnij się, że możesz nawiązać połączenie z Internetem.<li>(407) wymagane jest uwierzytelnianie serwera proxy. |Serwer proxy blokuje połączenie. |  <ul><li>W programie Internet Explorer przejdź do pozycji **Narzędzia**  >  **Opcje internetowe**  >  **zabezpieczenia**  >  **internetowe Internet**. Wybierz pozycję **Poziom niestandardowy** i przewiń w dół do sekcji **Pobieranie pliku** . Wybierz pozycję **Włącz**.<p>Może być również konieczne dodanie adresów [URL i adresów IP](install-mars-agent.md#verify-internet-access) do zaufanych witryn w programie Internet Explorer.<li>Zmień ustawienia tak, aby korzystały z serwera proxy. Następnie podaj szczegóły serwera proxy.<li> Jeśli maszyna ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na te [adresy URL i adresy IP](install-mars-agent.md#verify-internet-access). <li>Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, Wyklucz te pliki ze skanowania antywirusowego: <ul><li>CBEngine.exe (zamiast dpmra.exe).<li>CSC.exe (powiązane z .NET Framework). Istnieje CSC.exe dla każdej wersji .NET Framework zainstalowanej na serwerze. Wyklucz CSC.exe pliki dla wszystkich wersji .NET Framework na serwerze, którego to dotyczy. <li>Folder tymczasowy lub lokalizacja pamięci podręcznej. <br>Domyślna lokalizacja folderu tymczasowego lub ścieżki pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Folder bin w katalogu C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Nie można użyć określonego pliku poświadczeń magazynu, ponieważ nie jest on pobrany z magazynu skojarzonego z tym serwerem.

| Błąd  | Możliwa przyczyna | Zalecane akcje |
| ---     | ---     | ---    |
| Nie można użyć określonego pliku poświadczeń magazynu, ponieważ nie jest on pobrany z magazynu skojarzonego z tym serwerem. (IDENTYFIKATOR: 100110) Podaj odpowiednie poświadczenia magazynu. | Plik poświadczeń magazynu pochodzi z innego magazynu niż ten, na którym jest już zarejestrowany ten serwer. | Upewnij się, że maszyna docelowa i maszyna źródłowa są zarejestrowani do tego samego magazynu Recovery Services. Jeśli serwer docelowy został już zarejestrowany w innym magazynie, użyj opcji **zarejestruj serwer** , aby zarejestrować się w prawidłowym magazynie.  

## <a name="backup-jobs-completed-with-warning"></a>Zadania tworzenia kopii zapasowej zakończone z ostrzeżeniem

- Gdy Agent MARS wykonuje iterację plików i folderów podczas tworzenia kopii zapasowej, może wystąpić różne warunki, które mogą spowodować, że kopia zapasowa ma być oznaczona jako ukończona z ostrzeżeniami. W tych warunkach zadanie pokazuje jako zakończone z ostrzeżeniami. Jest to dokładne, ale oznacza to, że nie można utworzyć kopii zapasowej co najmniej jednego pliku. Oznacza to, że zadanie pominął ten plik, ale utworzono kopię zapasową wszystkich innych plików w źródle danych.

  ![Zadanie tworzenia kopii zapasowej zostało ukończone z ostrzeżeniami](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Warunki, które mogą spowodować, że kopie zapasowe pomijają pliki:
  - Nieobsługiwane atrybuty plików (na przykład: w folderze usługi OneDrive, skompresowanym strumieniu punktów ponownej analizy). Aby uzyskać pełną listę, zapoznaj się z [matrycą pomocy technicznej](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup).
  - Problem z systemem plików
  - Inny proces zakłóca działanie (na przykład: oprogramowanie antywirusowe, które obsługuje pliki, może uniemożliwić uzyskiwanie dostępu do plików przez agenta MARS)
  - Pliki zablokowane przez aplikację  

- Usługa Backup oznaczy te pliki jako nieudane w pliku dziennika, korzystając z następującej konwencji nazewnictwa: *LastBackupFailedFilesxxxx.txt* w folderze *C:\Program Files\Microsoft Azure Recovery Service Agent\temp* .
- Aby rozwiązać ten problem, przejrzyj plik dziennika, aby poznać jego charakter:

  | Kod błędu             | Powodów                                             | Zalecenia                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Plik lub katalog jest uszkodzony i nie można go odczytać. | Uruchom **program CHKDSK** na woluminie źródłowym.                             |
  | 0x80070002, 0x80070003 | System nie może znaleźć określonego pliku.         | [Upewnij się, że folder tymczasowy nie jest pełny](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  Sprawdź, czy wolumin, na którym jest skonfigurowane miejsce na pliki tymczasowe, istnieje (nie został usunięty)  <br><br>   [Upewnij się, że Agent MARS jest wykluczony z programu antywirusowego zainstalowanego na komputerze](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Odmowa dostępu                                    | [Sprawdź, czy program antywirusowy lub inne oprogramowanie innych firm blokuje dostęp](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Odmowa dostępu do pliku w chmurze.                | Pliki usługi OneDrive, pliki Git lub inne pliki, które mogą znajdować się w stanie offline na komputerze |

- Do [istniejących zasad można użyć dodawania reguł wykluczeń](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) w celu wykluczenia nieobsługiwanych, brakujących lub usuniętych plików z zasad tworzenia kopii zapasowych, aby zapewnić pomyślne tworzenie kopii zapasowych.

- Należy unikać usuwania i ponownego tworzenia chronionych folderów o tych samych nazwach w folderze najwyższego poziomu. Wykonanie tej operacji może spowodować ukończenie tworzenia kopii zapasowej z ostrzeżeniami z błędem *, ponieważ wykryto krytyczną niespójność, dlatego zmiany nie mogą być replikowane.*  Jeśli konieczne jest usunięcie i ponowne utworzenie folderów, należy rozważyć wykonanie tej operacji w podfolderach w chronionym folderze najwyższego poziomu.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych

| Błąd | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| <br />Nie można ustawić klucza szyfrowania dla bezpiecznych kopii zapasowych. Aktywacja nie powiodła się w całości, ale hasło szyfrowania zostało zapisane w następującym pliku. |<li>Serwer jest już zarejestrowany w innym magazynie.<li>Podczas konfiguracji hasło zostało uszkodzone.| Wyrejestruj serwer z magazynu i zarejestruj go ponownie, podając nowe hasło.

## <a name="the-activation-did-not-complete-successfully"></a>Aktywacja nie została pomyślnie ukończona

| Błąd  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|<br />Aktywacja nie została pomyślnie ukończona. Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FC07]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej firmy Microsoft.     | <li> Folder tymczasowy znajduje się na woluminie, na którym nie ma wystarczającej ilości miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony. <li> Brak pliku OnlineBackup. KEK.         | <li>Uaktualnij do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta Mars.<li>Przenieś folder tymczasowy lub lokalizację pamięci podręcznej na wolumin z ilością wolnego miejsca wynoszącą od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby prawidłowo przenieść lokalizację pamięci podręcznej, zapoznaj się z instrukcjami w [temacie typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder).<li> Upewnij się, że plik OnlineBackup. KEK jest obecny. <br>*Domyślną lokalizacją folderu tymczasowego lub ścieżką pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Hasło szyfrowania nie zostało prawidłowo skonfigurowane

| Błąd  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
| <br />Błąd 34506. Hasło szyfrowania zapisane na tym komputerze nie jest prawidłowo skonfigurowane.    | <li> Folder tymczasowy znajduje się na woluminie, na którym nie ma wystarczającej ilości miejsca. <li> Folder tymczasowy został niepoprawnie przeniesiony. <li> Brak pliku OnlineBackup. KEK.        | <li>Uaktualnij do [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta Mars.<li>Przenieś folder tymczasowy lub lokalizację pamięci podręcznej na wolumin z ilością wolnego miejsca wynoszącą od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby prawidłowo przenieść lokalizację pamięci podręcznej, zapoznaj się z instrukcjami w [temacie typowe pytania dotyczące tworzenia kopii zapasowych plików i folderów](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder).<li> Upewnij się, że plik OnlineBackup. KEK jest obecny. <br>*Domyślną lokalizacją folderu tymczasowego lub ścieżką pamięci podręcznej jest C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

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

- Upewnij się, że ta ścieżka jest częścią `PSMODULEPATH` zmiennej środowiskowej:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Jeśli zasady wykonywania programu PowerShell dla programu `LocalMachine` mają ustawioną wartość `restricted` , polecenie cmdlet programu PowerShell wyzwalające zadanie tworzenia kopii zapasowej może zakończyć się niepowodzeniem. Uruchom te polecenia w trybie podniesionych uprawnień, aby sprawdzić i ustawić zasady wykonywania na albo `Unrestricted` `RemoteSigned` :

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

Błąd | Możliwe przyczyny | Zalecane akcje
--- | --- | ---
Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi "zasób nie został zainicjowany w sygnaturze usługi". Spróbuj ponownie wykonać operację po pewnym czasie. (IDENTYFIKATOR: 230006) | Zmieniono nazwę chronionego serwera. | <li> Zmień nazwę serwera z powrotem na oryginalną nazwę zarejestrowanego w magazynie. <br> <li> Zarejestruj ponownie serwer w magazynie przy użyciu nowej nazwy.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Nie można uruchomić zadania, ponieważ inne zadanie było w toku

Jeśli zauważysz komunikat ostrzegawczy w   >  **historii zadań** konsoli Mars, mówiąc "nie można uruchomić zadania, ponieważ inne zadanie było w toku", może to być spowodowane zduplikowanym wystąpieniem zadania wyzwalanym przez harmonogram zadań.

![Nie można uruchomić zadania, ponieważ inne zadanie było w toku](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Aby rozwiązać ten problem:

1. Uruchom przystawkę Harmonogram zadań, wpisując *taskschd. msc* w oknie uruchamiania
1. W lewym okienku przejdź do **harmonogram zadań bibliotece**  ->  **Microsoft**  ->  **OnlineBackup**.
1. Dla każdego zadania w tej bibliotece kliknij dwukrotnie zadanie, aby otworzyć właściwości, i wykonaj następujące czynności:
    1. Przejdź do karty **Ustawienia** .

         ![Karta Ustawienia](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Zmień opcję **, jeśli zadanie jest już uruchomione, a następnie zastosowana zostanie następująca reguła**. Wybierz pozycję nie **uruchamiaj nowego wystąpienia**.

         ![Zmień regułę, aby nie uruchamiać nowego wystąpienia](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Rozwiązywanie problemów z przywracaniem

Azure Backup może nie pomyślnie zainstalować woluminu odzyskiwania, nawet po kilku minutach. Podczas procesu mogą pojawić się komunikaty o błędach. Aby rozpocząć odzyskiwanie normalnie, wykonaj następujące czynności:

1. Anuluj proces instalacji, jeśli jest uruchomiony przez kilka minut.

2. Sprawdź, czy masz najnowszą wersję agenta kopii zapasowej. Aby sprawdzić wersję, w okienku **Akcje** konsoli Mars wybierz pozycję **Informacje o Microsoft Azure Recovery Services agencie**. Upewnij się, że numer **wersji** jest równy lub większy niż wersja wymieniona w [tym artykule](https://go.microsoft.com/fwlink/?linkid=229525). Wybierz ten link [, aby pobrać najnowszą wersję](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Przejdź do pozycji **Device Manager**  >  **Storage controllers** i Znajdź **inicjatora iSCSI firmy Microsoft**. Jeśli go wyszukasz, przejdź bezpośrednio do kroku 7.

4. Jeśli nie możesz znaleźć usługi inicjatora iSCSI firmy Microsoft, spróbuj znaleźć wpis w obszarze **Device Manager**  >  **Kontrolery magazynu** o nazwie **nieznane urządzenie** z identyfikatorem sprzętu **ROOT\ISCSIPRT**.

5. Kliknij prawym przyciskiem myszy pozycję **nieznane urządzenie** i wybierz polecenie **Aktualizuj oprogramowanie sterownika**.

6. Zaktualizuj sterownik, wybierając opcję  **automatycznego wyszukiwania zaktualizowanego oprogramowania sterownika**. Ta aktualizacja powinna zmienić **nieznane urządzenie** na **inicjatora iSCSI firmy Microsoft**:

    ![Zrzut ekranu przedstawiający Azure Backup Device Manager z wyróżnionymi kontrolerami magazynu](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Przejdź do usługi **Menedżera zadań**  >  **(lokalnego)**  >  **Usługa inicjatora iSCSI firmy Microsoft**:

    ![Zrzut ekranu Azure Backup Menedżera zadań z wyróżnionymi usługami (lokalnymi)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Uruchom ponownie usługę inicjatora iSCSI firmy Microsoft. Aby to zrobić, kliknij prawym przyciskiem myszy usługę i wybierz polecenie **Zatrzymaj**. Następnie kliknij ponownie prawym przyciskiem myszy i wybierz polecenie **Uruchom**.

9. Ponów próbę odzyskiwania przy użyciu funkcji [natychmiastowego przywracania](backup-instant-restore-capability.md).

Jeśli odzyskiwanie nadal kończy się niepowodzeniem, należy ponownie uruchomić serwer lub klienta. Jeśli nie chcesz ponownie uruchamiać programu lub jeśli odzyskiwanie nadal kończy się niepowodzeniem nawet po ponownym uruchomieniu serwera, spróbuj wykonać [odzyskiwanie z innego komputera](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="troubleshoot-cache-problems"></a>Rozwiązywanie problemów z pamięcią podręczną

Operacja tworzenia kopii zapasowej może zakończyć się niepowodzeniem, jeśli folder pamięci podręcznej (również określony jako folder tymczasowy) jest niepoprawnie skonfigurowany, brakujące wymagania wstępne lub ma ograniczony dostęp.

### <a name="prerequisites"></a>Wymagania wstępne

W przypadku operacji agenta MARS do pomyślnego przeprowadzenia w folderze pamięci podręcznej musi być zgodna z następującymi wymaganiami:

- [Upewnij się, że 5% do 10% wolnego miejsca na woluminie jest dostępne w lokalizacji folderu tymczasowego](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Upewnij się, że lokalizacja folderu tymczasowego jest prawidłowa i dostępna](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Upewnij się, że atrybuty pliku w folderze pamięci podręcznej są obsługiwane](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Upewnij się, że zajęte miejsce do magazynowania kopii w tle jest wystarczające dla procesu tworzenia kopii zapasowej](#increase-shadow-copy-storage)
- [Upewnij się, że nie istnieją żadne inne procesy (np. oprogramowanie antywirusowe) ograniczające dostęp do folderu pamięci podręcznej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Zwiększ magazyn kopii w tle

Operacje tworzenia kopii zapasowej mogą zakończyć się niepowodzeniem, jeśli nie ma wystarczającej ilości miejsca w magazynie kopii w tle wymaganej do ochrony źródła danych. Aby rozwiązać ten problem, Zwiększ ilość miejsca do magazynowania kopii w tle na chronionym woluminie, korzystając z usługi **vssadmin** , jak pokazano poniżej:

- Sprawdź bieżące miejsce do magazynowania w tle z wiersza polecenia z podwyższonym poziomem uprawnień:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Zwiększ ilość miejsca w magazynie w tle przy użyciu następującego polecenia:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Inny proces lub program antywirusowy blokujący dostęp do folderu pamięci podręcznej

Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, należy dodać niezbędne reguły wykluczania do skanowania oprogramowania antywirusowego dla tych plików i folderów:  

- Folder tymczasowy. Domyślna lokalizacja to `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Folder bin w `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Typowe problemy

W tej sekcji opisano typowe błędy występujące podczas korzystania z agenta MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Komunikat o błędzie | Zalecana akcja
--|--
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do sumy kontrolnej kopii zapasowej przechowywanej w lokalizacji tymczasowej | Aby rozwiązać ten problem, wykonaj następujące kroki i ponownie uruchom serwer <br/> - [Sprawdź, czy istnieje program antywirusowy lub inne procesy blokujące pliki lokalizacji tymczasowej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja tymczasowa jest prawidłowa i dostępna dla agenta MARS.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Komunikat o błędzie | Zalecana akcja
--|--
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do lokalizacji tymczasowej w celu zainicjowania wirtualnego dysku twardego | Aby rozwiązać ten problem, wykonaj następujące kroki i ponownie uruchom serwer <br/> - [Sprawdź, czy program antywirusowy lub inne procesy blokują pliki lokalizacji tymczasowej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja tymczasowa jest prawidłowa i dostępna dla agenta MARS.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Komunikat o błędzie | Zalecana akcja
--|--
Tworzenie kopii zapasowej nie powiodło się z powodu niewystarczającej ilości miejsca w magazynie, w której znajduje się folder | Aby rozwiązać ten problem, sprawdź następujące kroki i spróbuj ponownie wykonać operację:<br/>- [Upewnij się, że Agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Weryfikowanie i rozwiązywanie problemów z magazynem, które wpływają na miejsce na kopie zapasowe](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Komunikat o błędzie | Zalecana akcja
--|--
Nie można odnaleźć zmian w pliku. Taka sytuacja może mieć różne przyczyny. Ponów próbę wykonania operacji | Aby rozwiązać ten problem, sprawdź następujące kroki i spróbuj ponownie wykonać operację:<br/> - [Upewnij się, że Agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Weryfikowanie i rozwiązywanie problemów z magazynem, które wpływają na miejsce na kopie zapasowe](#prerequisites)

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na temat [tworzenia kopii zapasowej systemu Windows Server z agentem Azure Backup](tutorial-backup-windows-server-to-azure.md).
- Jeśli chcesz przywrócić kopię zapasową, zobacz [Przywracanie plików do maszyny z systemem Windows](backup-azure-restore-windows-server.md).
