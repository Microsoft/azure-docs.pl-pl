---
title: Rozwiązywanie problemów z Azure Backup agenta
description: Z tego artykułu dowiesz się, jak rozwiązywać problemy z instalacją i rejestracją agenta Azure Backup agenta.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: c662bf8c8d9490691f45254bef01618f17bd6e2a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518188"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Rozwiązywanie problemów z agentem Microsoft Azure Recovery Services (MARS)

W tym artykule opisano sposób rozwiązywania błędów, które mogą wystąpić podczas konfigurowania, rejestrowania, tworzenia kopii zapasowej i przywracania.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Przed rozpoczęciem rozwiązywania problemów z agentem usług Azure Recovery Services (MARS) firmy Microsoft zaleca się sprawdzenie następujących danych:

- [Upewnij się, że agent MARS jest aktualny.](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że masz łączność sieciową między agentem usługi MARS i platformą Azure.](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że usługa MARS jest uruchomiona (w konsoli usługi). Jeśli to konieczne, uruchom ponownie i spróbuj ponownie wykonać operację.
- [Upewnij się, że od 5% do 10%](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)wolnego miejsca na woluminie jest dostępne w lokalizacji folderu plików scratch.
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Jeśli zadanie tworzenia kopii zapasowej zakończyło się z ostrzeżeniami, zobacz [Zadania tworzenia kopii zapasowej zakończone z ostrzeżeniem](#backup-jobs-completed-with-warning)
- Jeśli zaplanowana kopia zapasowa nie powiedzie się, ale ręczne tworzenie kopii zapasowej działa, zobacz Tworzenie kopii zapasowych nie [jest uruchamiane zgodnie z harmonogramem](#backups-dont-run-according-to-schedule).
- Upewnij się, że system operacyjny ma najnowsze aktualizacje.
- [Upewnij się, że nieobsługiwane dyski i pliki z nieobsługiwanymi atrybutami są wykluczone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Upewnij się, że zegar w chronionym systemie jest skonfigurowany do poprawnej strefy czasowej.
- Upewnij .NET Framework, że na serwerze jest zainstalowana .NET Framework [4.5.2 lub nowszy.](https://www.microsoft.com/download/details.aspx?id=30653)
- Jeśli próbujesz ponownie zarejestrować serwer w magazynie:
  - Upewnij się, że agent został odinstalowany na serwerze i usunięty z portalu.
  - Użyj tego samego hasła, które zostało początkowo użyte do zarejestrowania serwera.
- W przypadku kopii zapasowych w trybie offline upewnij się Azure PowerShell że na komputerze źródłowym i kopiowym jest zainstalowany program w wersji 3.7.0 przed rozpoczęciem tworzenia kopii zapasowej.
- Jeśli agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure, zobacz [ten artykuł.](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

**Komunikat o błędzie:** Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. (IDENTYFIKATOR: 34513)

| Przyczyna | Zalecane akcje |
| ---     | ---    |
| **Poświadczenia magazynu są nieprawidłowe** <br/> <br/> Pliki poświadczeń magazynu mogą być uszkodzone, wygasły lub mogą mieć inne rozszerzenie pliku niż *.vaultCredentials.* (Na przykład mogły one zostać pobrane ponad 10 dni przed czasem rejestracji).| [Pobierz nowe poświadczenia z](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) magazynu usługi Recovery Services na Azure Portal. Następnie w razie potrzeby należy wykonać następujące czynności: <ul><li> Jeśli już zainstalowano i zarejestrowano agenta MARS, otwórz konsolę mmc Microsoft Azure Backup Agent. Następnie wybierz **pozycję Zarejestruj serwer** w **okienku Akcje,** aby ukończyć rejestrację przy użyciu nowych poświadczeń. <br/> <li> Jeśli nowa instalacja nie powiedzie się, spróbuj ponownie zainstalować program przy użyciu nowych poświadczeń.</ul> **Uwaga:** Jeśli pobrano wiele plików poświadczeń magazynu, tylko najnowszy plik będzie ważny przez następne 10 dni. Zalecamy pobranie nowego pliku poświadczeń magazynu.
| **Serwer proxy/zapora blokuje rejestrację** <br/>lub <br/>**Brak łączności z Internetem** <br/><br/> Jeśli komputer lub serwer proxy ma ograniczoną łączność z Internetem i nie zapewniasz dostępu do niezbędnych adresów URL, rejestracja nie powiedzie się.| Należy wykonać następujące czynności:<br/> <ul><li> We współpracy z zespołem IT upewnij się, że system ma łączność z Internetem.<li> Jeśli nie masz serwera proxy, upewnij się, że opcja serwera proxy nie jest zaznaczona podczas rejestrowania agenta. [Sprawdź ustawienia serwera proxy.](#verifying-proxy-settings-for-windows)<li> Jeśli masz zaporę/serwer proxy, we współpracy z zespołem obsługi sieci upewnij się, że te adresy URL i IP mają dostęp:<br/> <br> **Adresy URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**Adresy IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Spróbuj ponownie zarejestrować się po ukończeniu poprzednich kroków rozwiązywania problemów.<br></br> Jeśli połączenie jest za pośrednictwem Azure ExpressRoute, upewnij się, że ustawienia zostały skonfigurowane zgodnie z opisem w te [Azure ExpressRoute pomocy technicznej](backup-support-matrix-mars-agent.md#azure-expressroute-support).
| **Oprogramowanie antywirusowe blokuje rejestrację** | Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, dodaj niezbędne reguły wykluczania do skanowania antywirusowego dla tych plików i folderów: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Folder plików scratch. Jego domyślna lokalizacja to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Folder bin w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Zalecenia dodatkowe

- Przejdź do katalogu C:/Windows/Temp i sprawdź, czy istnieje więcej niż 60 000 lub 65 000 plików z rozszerzeniem TMP. Jeśli istnieją, usuń te pliki.
- Upewnij się, że data i godzina maszyny są zgodne z lokalną strefą czasową.
- Upewnij [się, że](install-mars-agent.md#verify-internet-access) te witryny zostały dodane do zaufanych witryn w Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Weryfikowanie ustawień serwera proxy dla systemu Windows

1. Pobierz narzędzie PsExec ze [strony Sysinternals.](/sysinternals/downloads/psexec)
1. Uruchom `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` polecenie z wiersza polecenia z podwyższonym poziomem uprawnień.

   To polecenie spowoduje otwarcie Internet Explorer.
1. Przejdź do opcji **Narzędzia**  >  **Opcje internetowe** Ustawienia  >  **połączeń** sieci  >  **LAN.**
1. Sprawdź ustawienia serwera proxy dla konta systemowego.
1. Jeśli nie skonfigurowano żadnego serwera proxy i podano szczegóły serwera proxy, usuń szczegóły.
1. Jeśli serwer proxy jest skonfigurowany, a szczegóły serwera proxy są niepoprawne, upewnij się, że szczegóły adresu **IP serwera proxy** i portu są poprawne. 
1. Zamknij program Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Nie można pobrać pliku poświadczeń magazynu

| Błąd   | Zalecane akcje |
| ---     | ---    |
|Nie można pobrać pliku poświadczeń magazynu. (IDENTYFIKATOR: 403) | <ul><li> Spróbuj pobrać poświadczenia magazynu przy użyciu innej przeglądarki lub wykonać następujące kroki: <ul><li> Uruchom program Internet Explorer. Wybierz klawisz F12. </li><li> Przejdź do karty **Sieć i** wyczyść pamięć podręczną oraz pliki cookie. </li> <li> Odśwież stronę.<br></li></ul> <li> Sprawdź, czy subskrypcja została wyłączona/wygasła.<br></li> <li> Sprawdź, czy którakolwiek reguła zapory blokuje pobieranie. <br></li> <li> Upewnij się, że nie został wyczerpany limit magazynu (50 maszyn na magazyn).<br></li>  <li> Upewnij się, że użytkownik ma Azure Backup wymagane do pobierania poświadczeń magazynu i rejestrowania serwera w magazynie. Zobacz [Use Azure role-based access control to manage Azure Backup recovery points](backup-rbac-rs-vault.md)(Używanie kontroli dostępu opartej na rolach platformy Azure do zarządzania Azure Backup punktami odzyskiwania).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Agent usługi Microsoft Azure Recovery Service nie mógł połączyć się z usługą Microsoft Azure Backup

| Błąd  | Możliwa przyczyna | Zalecane akcje |
| ---     | ---     | ---    |
| <br /><ul><li>Agent Microsoft Azure Recovery Service nie mógł nawiązać połączenia z Microsoft Azure Backup. (IDENTYFIKATOR: 100050) Sprawdź ustawienia sieci i upewnij się, że możesz nawiązać połączenie z Internetem.<li>(407) Wymagane jest uwierzytelnianie serwera proxy. |Serwer proxy blokuje połączenie. |  <ul><li>W Internet Explorer przejdź do **opcji** Narzędzia  >  **Opcje internetowe**  >  **Zabezpieczenia**  >  **Internet.** Wybierz **pozycję Poziom niestandardowy** i przewiń w dół do sekcji **Pobieranie** pliku. Wybierz pozycję **Włącz**.<p>Może być również konieczne dodanie adresów [URL i IP](install-mars-agent.md#verify-internet-access) do zaufanych witryn w Internet Explorer.<li>Zmień ustawienia, aby używać serwera proxy. Następnie podaj szczegóły serwera proxy.<li> Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na maszynie lub serwerze proxy zezwalają [na te adresy URL i IP.](install-mars-agent.md#verify-internet-access) <li>Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, wyklucz te pliki ze skanowania antywirusowego: <ul><li>CBEngine.exe (zamiast dpmra.exe).<li>CSC.exe (powiązane z .NET Framework). Istnieje kilka CSC.exe dla każdej .NET Framework zainstalowanej na serwerze. Wyklucz CSC.exe plików dla wszystkich wersji .NET Framework na serwerze, którego dotyczy problem. <li>Folder plików scratch lub lokalizacja pamięci podręcznej. <br>Domyślna lokalizacja folderu tymczasowego lub ścieżki pamięci podręcznej to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Folder bin w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Nie można użyć określonego pliku poświadczeń magazynu, ponieważ nie został on pobrany z magazynu skojarzonego z tym serwerem

| Błąd  | Możliwa przyczyna | Zalecane akcje |
| ---     | ---     | ---    |
| Nie można użyć określonego pliku poświadczeń magazynu, ponieważ nie został on pobrany z magazynu skojarzonego z tym serwerem. (IDENTYFIKATOR: 100110) Podaj odpowiednie poświadczenia magazynu. | Plik poświadczeń magazynu pochodzi z innego magazynu niż ten, w którym ten serwer jest już zarejestrowany. | Upewnij się, że maszyna docelowa i maszyna źródłowa są zarejestrowane w tym samym magazynie usługi Recovery Services. Jeśli serwer docelowy został już zarejestrowany w innym magazynie, użyj opcji Zarejestruj **serwer,** aby zarejestrować się w prawidłowym magazynie.  

## <a name="backup-jobs-completed-with-warning"></a>Zadania tworzenia kopii zapasowej zostały ukończone z ostrzeżeniem

- Gdy agent MARS iteruje po plikach i folderach podczas tworzenia kopii zapasowej, może wystąpić wiele warunków, które mogą spowodować, że kopia zapasowa zostanie oznaczona jako ukończona z ostrzeżeniami. W tych warunkach zadanie jest wyświetlane jako ukończone z ostrzeżeniami. Jest to w porządku, ale oznacza to, że nie można było wrócić do kopii zapasowej co najmniej jednego pliku. W związku z tym zadanie pominąło ten plik, ale kopii zapasowej wszystkich pozostałych plików w źródle danych.

  ![Zadanie tworzenia kopii zapasowej zakończyło się z ostrzeżeniami](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Warunki, które mogą powodować pomijanie plików kopii zapasowych, obejmują:
  - Nieobsługiwane atrybuty pliku (na przykład: w folderze oneDrive, skompresowany strumień, punkty ponownej synchronizacji). Aby uzyskać pełną listę, zapoznaj się z [macierzą obsługi](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup).
  - Problem z systemem plików
  - Inny proces zakłócający (na przykład: oprogramowanie antywirusowe z dojściami do plików może uniemożliwić agentowi MARS dostęp do plików)
  - Pliki zablokowane przez aplikację  

- Usługa tworzenia kopii zapasowej oznaczy te pliki jako nieudane w pliku dziennika z następującą konwencją nazewnictwa: *LastBackupFailedFilesxxxx.txt* w folderze *C:\Program Files\Microsoft Azure Recovery Service Agent\temp.*
- Aby rozwiązać ten problem, przejrzyj plik dziennika, aby zrozumieć charakter problemu:

  | Kod błędu             | Powodów                                             | Zalecenia                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Plik lub katalog jest uszkodzony i nieczytelny. | Uruchom **chkdsk** na woluminie źródłowym.                             |
  | 0x80070002, 0x80070003 | System nie może odnaleźć określonego pliku.         | [Upewnij się, że folder plików scratch nie jest pełny](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)  <br><br>  Sprawdź, czy wolumin, na którym skonfigurowano miejsce na pliki scratch, istnieje (nie został usunięty)  <br><br>   [Upewnij się, że agent MARS jest wykluczony z programu antywirusowego zainstalowanego na maszynie](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Odmowa dostępu                                    | [Sprawdź, czy oprogramowanie antywirusowe lub inne oprogramowanie innej firmy blokuje dostęp](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Odmowa dostępu do pliku w chmurze.                | Pliki usługi OneDrive, Pliki Git lub inne pliki, które mogą być w stanie offline na maszynie |

- Możesz użyć funkcji [Dodaj reguły wykluczeń](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) do istniejących zasad, aby wykluczyć nieobsługiwane, brakujące lub usunięte pliki z zasad kopii zapasowych, aby zapewnić pomyślne tworzenie kopii zapasowych.

- Unikaj usuwania i ponownego tworzenia folderów chronionych o tych samych nazwach w folderze najwyższego poziomu. Może to spowodować ukończenie tworzenia kopii zapasowej z ostrzeżeniami o błędzie Wykryto krytyczną niespójność, w związku z tym nie można *replikować zmian.*  Jeśli musisz usunąć i ponownie utworzyć foldery, rozważ wykonanie tej pracy w podfolderach w chronionym folderze najwyższego poziomu.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nie można ustawić klucza szyfrowania na potrzeby bezpiecznych kopii zapasowych

| Błąd | Możliwe przyczyny | Zalecane akcje |
| ---     | ---     | ---    |
| <br />Nie można ustawić klucza szyfrowania dla bezpiecznych kopii zapasowych. Aktywacja nie powiodła się całkowicie, ale hasło szyfrowania zostało zapisane w następującym pliku. |<li>Serwer jest już zarejestrowany w innym magazynie.<li>Podczas konfiguracji hasło zostało uszkodzone.| Wyrejestruj serwer z magazynu i zarejestruj go ponownie przy użyciu nowego hasła.

## <a name="the-activation-did-not-complete-successfully"></a>Aktywacja nie została zakończona pomyślnie

| Błąd  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
|<br />Aktywacja nie została pomyślnie ukończona. Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FC07]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie nadal występować, skontaktuj się z działem pomocy technicznej firmy Microsoft.     | <li> Folder scratch znajduje się na woluminie, który nie ma wystarczającej ilości miejsca. <li> Folder plików scratch został niepoprawnie przeniesiony. <li> Brakuje pliku OnlineBackup.KEK.         | <li>Uaktualnij agenta MARS [do](https://aka.ms/azurebackup_agent) najnowszej wersji.<li>Przenieś lokalizację folderu plików scratch lub pamięci podręcznej na wolumin z wolnym obszarem, który stanowi od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, zapoznaj się z krokami w tece Typowe pytania dotyczące kopii [zapasowej plików i folderów.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>Domyślna lokalizacja folderu tymczasowego lub ścieżki pamięci podręcznej *to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.*        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Hasło szyfrowania nie zostało prawidłowo skonfigurowane

| Błąd  | Możliwe przyczyny | Zalecane akcje |
|---------|---------|---------|
| <br />Błąd 34506. Hasło szyfrowania przechowywane na tym komputerze nie jest poprawnie skonfigurowane.    | <li> Folder scratch znajduje się na woluminie, który nie ma wystarczającej ilości miejsca. <li> Folder plików scratch został niepoprawnie przeniesiony. <li> Brakuje pliku OnlineBackup.KEK.        | <li>Uaktualnij agenta MARS [do](https://aka.ms/azurebackup_agent) najnowszej wersji.<li>Przenieś folder plików scratch lub lokalizację pamięci podręcznej do woluminu z wolnym obszarem od 5% do 10% całkowitego rozmiaru danych kopii zapasowej. Aby poprawnie przenieść lokalizację pamięci podręcznej, zapoznaj się z krokami w tece Typowe pytania dotyczące kopii [zapasowej plików i folderów.](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)<li> Upewnij się, że plik OnlineBackup.KEK jest obecny. <br>Domyślna lokalizacja folderu tymczasowego lub ścieżki pamięci podręcznej *to C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.*         |

## <a name="backups-dont-run-according-to-schedule"></a>Kopie zapasowe nie są uruchamiane zgodnie z harmonogramem

Jeśli zaplanowane kopie zapasowe nie są wyzwalane automatycznie, ale ręczne kopie zapasowe działają prawidłowo, spróbuj wykonać następujące czynności:

- Upewnij się, że harmonogram tworzenia kopii zapasowych systemu Windows Server nie jest w konflikcie z harmonogramem tworzenia kopii zapasowych plików i folderów platformy Azure.

- Upewnij się, że stan kopii zapasowej online ma wartość **Włącz.** Aby sprawdzić stan, należy wykonać następujące czynności:

  1. W Harmonogram zadań rozwiń pozycję **Microsoft** i wybierz pozycję **Kopia zapasowa online.**
  1. Kliknij dwukrotnie **pozycję Microsoft-OnlineBackup** i przejdź do **karty Wyzwalacze.**
  1. Sprawdź, czy stan ma wartość **Włączone.** Jeśli tak nie jest, wybierz pozycję **Edytuj,** wybierz pozycję **Włączone,** a następnie wybierz przycisk **OK.**

- Upewnij się, że konto użytkownika wybrane do uruchomienia zadania to **systemowa** lub **lokalna grupa** administratorów na serwerze. Aby zweryfikować konto użytkownika, przejdź do **karty Ogólne** i zaznacz **opcje** zabezpieczeń.

- Upewnij się, że na serwerze jest zainstalowany program PowerShell 3.0 lub nowszy. Aby sprawdzić wersję programu PowerShell, uruchom to polecenie i sprawdź, `Major` czy numer wersji to 3 lub nowsza:

  `$PSVersionTable.PSVersion`

- Upewnij się, że ta ścieżka jest częścią `PSMODULEPATH` zmiennej środowiskowej:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Jeśli zasady wykonywania programu PowerShell dla programu są ustawione na wartość , polecenie cmdlet programu PowerShell, które wyzwala zadanie tworzenia `LocalMachine` `restricted` kopii zapasowej, może się nie powieść. Uruchom te polecenia w trybie podwyższonych uprawnień, aby sprawdzić zasady wykonywania i ustawić je na `Unrestricted` wartość lub `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Upewnij się, że nie ma brakujących lub uszkodzonych plików modułu programu PowerShell MSOnlineBackup. Jeśli brakuje plików lub są one uszkodzone, należy wykonać następujące czynności:

  1. Z dowolnej maszyny, która ma agenta MARS, który działa prawidłowo, skopiuj folder MSOnlineBackup z folderu C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Na problematycznej maszynie wklej skopiowane pliki w tej samej lokalizacji folderu (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Jeśli na maszynie istnieje już folder MSOnlineBackup, wklej do niego pliki lub zastąp wszelkie istniejące pliki.

> [!TIP]
> Aby zapewnić spójne zastosowanie zmian, uruchom ponownie serwer po wykonaniu poprzednich kroków.

## <a name="resource-not-provisioned-in-service-stamp"></a>Zasób nie aprowizowany w sygnaturze usługi

Błąd | Możliwe przyczyny | Zalecane akcje
--- | --- | ---
Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi "Zasób nie aprowizowany w sygnaturze usługi". Spróbuj ponownie wykonać operację po pewnym czasie. (IDENTYFIKATOR: 230006) | Nazwa chronionego serwera została zmieniona. | <li> Zmień nazwę serwera z powrotem na oryginalną nazwę zarejestrowaną w magazynie. <br> <li> Zarejestruj ponownie serwer w magazynie przy użyciu nowej nazwy.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Nie można rozpocząć zadania, ponieważ inne zadanie jest w toku

Jeśli w historii zadań konsoli **USŁUGI MARS** zostanie wyświetlony komunikat ostrzegawczy z informacją "Nie można uruchomić zadania, ponieważ było w toku inne zadanie", może to być spowodowane zduplikowane wystąpienie zadania wyzwolone przez  >  Harmonogram zadań.

![Nie można rozpocząć zadania, ponieważ inne zadanie jest w toku](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Aby rozwiązać ten problem:

1. Uruchom przystawkę Harmonogram zadań, wpisując *taskschd.msc* w oknie Uruchamianie
1. W okienku po lewej stronie przejdź do **Harmonogram zadań**  ->  **Microsoft**  ->  **OnlineBackup.**
1. Dla każdego zadania w tej bibliotece kliknij dwukrotnie zadanie, aby otworzyć właściwości i wykonać następujące czynności:
    1. Przejdź do **karty** Ustawienia.

         ![Karta Ustawienia](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. Zmień opcję dla **opcji Jeśli zadanie jest już uruchomione, obowiązuje następująca reguła**. Wybierz **pozycję Nie uruchamiaj nowego wystąpienia**.

         ![Zmień regułę, aby nie uruchamiać nowego wystąpienia](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Rozwiązywanie problemów z przywracaniem

Azure Backup może nie pomyślnie zainstalować woluminu odzyskiwania, nawet po kilku minutach. Podczas tego procesu mogą pojawić się komunikaty o błędach. Aby rozpocząć normalne odzyskiwanie, należy wykonać następujące czynności:

1. Anuluj proces instalacji, jeśli był uruchomiony przez kilka minut.

2. Sprawdź, czy masz najnowszą wersję agenta kopii zapasowej. Aby sprawdzić wersję, w okienku **Akcje** konsoli MARS wybierz pozycję Informacje Microsoft Azure **Recovery Services.** Upewnij się, **że numer** wersji jest równy lub wyższy niż wersja wymieniona w [tym artykule.](https://go.microsoft.com/fwlink/?linkid=229525) Wybierz ten link, [aby pobrać najnowszą wersję](https://go.microsoft.com/fwLink/?LinkID=288905).

3. Przejdź do **Menedżer urządzeń**  >  **magazynu i** znajdź **inicjator iSCSI firmy Microsoft.** Jeśli go znajdziesz, przejdź bezpośrednio do kroku 7.

4. Jeśli nie można zlokalizować usługi inicjatora iSCSI firmy Microsoft, spróbuj znaleźć wpis w obszarze **kontrolerów** usługi Menedżer urządzeń Storage o nazwie Nieznane urządzenie o identyfikatorze sprzętu  >   **ROOT\ISCSIPRT.** 

5. Kliknij prawym przyciskiem myszy **pozycję Nieznane urządzenie i** wybierz polecenie Aktualizuj oprogramowanie **sterowników.**

6. Zaktualizuj sterownik, wybierając opcję Automatycznie **wyszukaj zaktualizowane oprogramowanie sterownika.** Ta aktualizacja powinna zmienić **ustawienia Nieznane urządzenie na** **Inicjator iSCSI firmy Microsoft:**

    ![Zrzut ekranu przedstawiający Azure Backup Menedżer urządzeń z wyróżnieniami Kontrolery magazynu](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. Przejdź do **Menedżer zadań**  >  **Services (Local)** Microsoft iSCSI Initiator Service ( Usługa  >  **inicjatora iSCSI firmy Microsoft):**

    ![Zrzut ekranu Azure Backup Menedżer zadań z wyróżnionem kreśleniami Usługi (lokalne)](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Uruchom ponownie usługę inicjatora iSCSI firmy Microsoft. W tym celu kliknij prawym przyciskiem myszy usługę i wybierz polecenie **Zatrzymaj**. Następnie kliknij go ponownie prawym przyciskiem myszy i wybierz polecenie **Uruchom.**

9. Ponów próbę odzyskania przy użyciu [funkcji natychmiastowego przywracania.](backup-instant-restore-capability.md)

Jeśli odzyskiwanie nadal kończy się niepowodzeniem, uruchom ponownie serwer lub klienta. Jeśli nie chcesz ponownie uruchamiać programu lub odzyskiwanie nadal kończy się niepowodzeniem nawet po ponownym uruchomieniu serwera, spróbuj odzyskać [dane z innej maszyny.](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="troubleshoot-cache-problems"></a>Rozwiązywanie problemów z pamięcią podręczną

Operacja tworzenia kopii zapasowej może się nie powieść, jeśli folder pamięci podręcznej (nazywany również folderem podstawowym) jest niepoprawnie skonfigurowany, nie ma wymagań wstępnych lub ma ograniczony dostęp.

### <a name="prerequisites"></a>Wymagania wstępne

Aby operacje agenta MARS zakończyły się pomyślnie, folder pamięci podręcznej musi spełniać następujące wymagania:

- [Upewnij się, od 5% do 10% wolnego miejsca na woluminie jest dostępne w lokalizacji folderu plików scratch](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Upewnij się, że lokalizacja folderu plików scratch jest prawidłowa i dostępna](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [Upewnij się, że są obsługiwane atrybuty pliku w folderze pamięci podręcznej](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [Upewnij się, że przydzielone miejsce do magazynowania kopii w tle jest wystarczające dla procesu tworzenia kopii zapasowej](#increase-shadow-copy-storage)
- [Upewnij się, że nie ma żadnych innych procesów (np. oprogramowania antywirusowego) ograniczających dostęp do folderu pamięci podręcznej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Zwiększanie magazynu kopii w tle

Operacje tworzenia kopii zapasowej mogą się nie powieść, jeśli nie ma wystarczającej ilości miejsca do magazynowania kopii w tle wymaganego do ochrony źródła danych. Aby rozwiązać ten problem, zwiększ ilość miejsca do magazynowania kopii w tle na woluminie chronionym przy użyciu **narzędzia vssadmin,** jak pokazano poniżej:

- Sprawdź bieżące miejsce w tle w wierszu polecenia z podwyższonym poziomem uprawnień:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Zwiększ miejsce do magazynowania w tle za pomocą następującego polecenia:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Inny proces lub oprogramowanie antywirusowe blokujące dostęp do folderu pamięci podręcznej

Jeśli na serwerze jest zainstalowane oprogramowanie antywirusowe, dodaj niezbędne reguły wykluczania do skanowania antywirusowego dla tych plików i folderów:  

- Folder plików scratch. Jego domyślna lokalizacja to `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Folder bin w folderze `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Typowe problemy

W tej sekcji omykamy typowe błędy, które występują podczas korzystania z agenta MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Komunikat o błędzie | Zalecana akcja
--|--
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do sumy kontrolnej kopii zapasowej przechowywanej w lokalizacji tymczasowej | Aby rozwiązać ten problem, wykonaj następujące kroki i uruchom ponownie serwer <br/> - [Sprawdź, czy istnieje oprogramowanie antywirusowe lub inne procesy blokujące pliki lokalizacji tymczasowej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja na początku jest prawidłowa i dostępna dla agenta MARS.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Komunikat o błędzie | Zalecana akcja
--|--
Agent usługi Microsoft Azure Recovery Services nie mógł uzyskać dostępu do lokalizacji tymczasowej w celu zainicjowania wirtualnego dysku twardego | Aby rozwiązać ten problem, wykonaj następujące kroki i uruchom ponownie serwer <br/> - [Sprawdź, czy program antywirusowy lub inne procesy blokowały pliki lokalizacji tymczasowej](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Sprawdź, czy lokalizacja na początku jest prawidłowa i dostępna dla agenta MARS.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Komunikat o błędzie | Zalecana akcja
--|--
Tworzenie kopii zapasowej nie powiodło się z powodu niewystarczającej ilości miejsca w woluminie, w którym znajduje się folder plików scratch | Aby rozwiązać ten problem, sprawdź następujące kroki i spróbuj ponownie wykonać operację:<br/>- [Upewnij się, że agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Weryfikowanie i rozwiązywanie problemów z magazynem, które mają wpływ na miejsce na pliki zapasowe](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Komunikat o błędzie | Zalecana akcja
--|--
Nie można odnaleźć zmian w pliku. Taka sytuacja może mieć różne przyczyny. Ponów próbę wykonania operacji | Aby rozwiązać ten problem, sprawdź następujące kroki i spróbuj ponownie wykonać operację:<br/> - [Upewnij się, że agent MARS jest najnowszy](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Weryfikowanie i rozwiązywanie problemów z magazynem, które mają wpływ na miejsce na pliki zapasowe](#prerequisites)

## <a name="next-steps"></a>Następne kroki

- Uzyskaj więcej informacji na [temat sposobu kopii zapasowej systemu Windows Server za pomocą agenta Azure Backup programu](tutorial-backup-windows-server-to-azure.md).
- Jeśli musisz przywrócić kopię zapasową, zobacz [przywracanie plików na maszynie z systemem Windows.](backup-azure-restore-windows-server.md)
