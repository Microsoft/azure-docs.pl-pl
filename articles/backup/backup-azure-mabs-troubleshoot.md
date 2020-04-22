---
title: Rozwiązywanie problemów ze składnikiem Azure Backup Server
description: Rozwiązywanie problemów z instalacją, rejestracją serwera kopii zapasowych platformy Azure oraz tworzeniem kopii zapasowych i przywracania obciążeń aplikacji.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 7a1cac63ba6497b8580c83fe2b666b020701283a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688047"
---
# <a name="troubleshoot-azure-backup-server"></a>Rozwiązywanie problemów ze składnikiem Azure Backup Server

Użyj informacji zawartych w poniższych tabelach, aby rozwiązać problemy z błędami, które można napotkać podczas korzystania z usługi Azure Backup Server.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zaleca się wykonanie poniższej weryfikacji przed rozpoczęciem rozwiązywania problemów z programem Microsoft Azure Backup Server (MABS):

- [Upewnij się, że agent usług odzyskiwania platformy Microsoft Azure (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje połączenie sieciowe między agentem MARS i platformą Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). W razie potrzeby uruchom ponownie i ponów próbę wykonania operacji
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- Jeśli rejestracja nie powiódł się, upewnij się, że serwer, na którym próbujesz zainstalować usługę Azure Backup Server, nie jest jeszcze zarejestrowany w innym magazynie
- Jeśli instalacja wypychana zakończy się niepowodzeniem, sprawdź, czy agent programu DPM jest już obecny. Jeśli tak, odinstaluj agenta i ponów próbę instalacji
- [Upewnij się, że żaden inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup).<br>
- Upewnij się, że usługa SQL Agent jest uruchomiona i ustawiona na automatyczną na serwerze MABS<br>

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Rejestrowanie się w magazynie | Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. | Zalecane działanie: <br> <ul><li> Pobierz najnowszy plik poświadczeń z repozytorium i spróbuj ponownie. <br>(LUB)</li> <li> Jeśli poprzednia akcja nie zadziałała, spróbuj pobrać poświadczenia do innego katalogu lokalnego lub utworzyć nowy magazyn. <br>(LUB)</li> <li> Spróbuj zaktualizować ustawienia daty i godziny zgodnie z opisem w [tym artykule](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#invalid-vault-credentials-provided). <br>(LUB)</li> <li> Sprawdź, czy c:\windows\temp ma więcej niż 65000 plików. Przenieś nieaktualne pliki do innej lokalizacji lub usuń elementy w folderze Temp. <br>(LUB)</li> <li> Sprawdź stan certyfikatów. <br> a. Otwórz **pozycję Zarządzaj certyfikatami komputera** (w Panelu sterowania). <br> b. Rozwiń węzeł **osobisty** i jego węzeł **podrzędny Certyfikaty**.<br> d.  Usuń certyfikat **Narzędzia systemu Windows Azure**. <br> d. Ponów próbę rejestracji w kliencie usługi Azure Backup. <br> (LUB) </li> <li> Sprawdź, czy obowiązują zasady grupy. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replika jest niespójna

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Replika jest niespójna | Sprawdź, czy opcja automatycznego sprawdzania spójności w Kreatorze grupy ochrony jest włączona. Aby uzyskać więcej informacji na temat przyczyn niespójności repliki i odpowiednich sugestii, zobacz artykuł [Replika jest niespójna](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2006/cc161593(v=technet.10)).<br> <ol><li> W przypadku kopii zapasowej stanu systemu/BMR sprawdź, czy kopia zapasowa systemu Windows Server jest zainstalowana na chronionym serwerze.</li><li> Sprawdź, czy nie występują problemy związane z miejscem w puli magazynu programu DPM na serwerze kopii zapasowych programu DPM/Microsoft Azure i przydziel magazyn zgodnie z wymaganiami.</li><li> Sprawdź stan usługi kopiowania woluminów w tle na chronionym serwerze. Jeśli jest w stanie wyłączonym, ustaw go, aby uruchomić ręcznie. Uruchom usługę na serwerze. Następnie wróć do konsoli programu DPM/Microsoft Azure Backup Server i uruchom synchronizację z zadaniem sprawdzania spójności.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Tworzenie punktu odzyskiwania w trybie online nie powiodło się

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Tworzenie punktu odzyskiwania w trybie online nie powiodło się | **Komunikat o błędzie:** Agent kopii zapasowej systemu Windows Azure nie może utworzyć migawki wybranego woluminu. <br> **Obejście:** Spróbuj zwiększyć miejsce w woluminie repliki i punktu odzyskiwania.<br> <br> **Komunikat o błędzie:** Agent kopii zapasowej systemu Windows Azure nie może połączyć się z usługą OBEngine <br> **Obejście**: sprawdź, czy obEngine istnieje na liście uruchomionych usług na komputerze. Jeśli usługa OBEngine nie jest uruchomiona, użyj polecenia "net start OBEngine", aby uruchomić usługę OBEngine. <br> <br> **Komunikat o błędzie:** Hasło szyfrowania dla tego serwera nie jest ustawione. Skonfiguruj hasło szyfrowania. <br> **Obejście**: Spróbuj skonfigurować hasło szyfrowania. Jeśli to się nie powiedzie, należy wykonać następujące kroki: <br> <ol><li>Sprawdź, czy lokalizacja zarysowania istnieje. Jest to lokalizacja, o których mowa w rejestrze **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, o nazwie **ScratchLocation** powinna istnieć.</li><li> Jeśli istnieje lokalizacja zarysowania, spróbuj ponownie zarejestrować się przy użyciu starego hasła. *Za każdym razem, gdy konfigurujesz hasło szyfrowania, zapisz go w bezpiecznej lokalizacji.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Oryginalne i zewnętrzne serwery DPM muszą być zarejestrowane w tym samym magazynie

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Przywracanie | **Kod błędu**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Komunikat o błędzie:** Oryginalne i zewnętrzne serwery programu DPM muszą być zarejestrowane w tym samym magazynie | **Przyczyna:** Ten problem występuje, gdy próbujesz przywrócić pliki na serwerze alternatywnym z oryginalnego serwera przy użyciu opcji odzyskiwania programu External DPM i jeśli serwer, który jest odzyskiwany, a oryginalny serwer, z którego kopia zapasowa danych jest archiwiza, nie są skojarzone z tym samym magazynem usługi odzyskiwania.<br/> <br/>**Obejście problemu** Aby rozwiązać ten problem upewnij się, że zarówno oryginalny, jak i alternatywny serwer jest zarejestrowany w tym samym magazynie.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Zadania tworzenia punktów odzyskiwania online dla maszyny wirtualnej VMware nie powiodą się

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Zadania tworzenia punktów odzyskiwania online dla maszyny wirtualnej VMware nie powiodą się. Program DPM napotkał błąd z VMware podczas próby uzyskania informacji ChangeTracking. Kod błędu — filefaultfault (iD 33621) |  <ol><li> Zresetuj CTK na VMware dla maszyn wirtualnych, których dotyczy problem.</li> <li>Sprawdź, czy niezależny dysk nie jest na miejscu vmware.</li> <li>Zatrzymaj ochronę dla dotkniętych maszyn wirtualnych i ponownie przestań ą za pomocą przycisku **Odśwież.** </li><li>Uruchom CC dla maszyn wirtualnych, których dotyczy problem.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynatora agenta programu DPM na serwerze

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Pchanie agentów do chronionych serwerów | Operacja agenta nie powiodła się z powodu błędu \<komunikacji z usługą koordynatora agenta programu DPM w> ServerName. | **Jeśli zalecane działanie pokazane w produkcie nie działa, wykonaj następujące czynności:** <ul><li> Jeśli komputer jest dołączany z niezaufanej domeny, wykonaj [następujące kroki](https://docs.microsoft.com/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019). <br> (LUB) </li><li> Jeśli komputer jest dołączany z zaufanej domeny, należy rozwiązać problemy z instrukcjami opisanymi w [tym blogu](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>(LUB)</li><li> Spróbuj wyłączyć program antywirusowy jako krok rozwiązywania problemów. Jeśli problem zostanie rozwiązany, zmodyfikuj ustawienia antywirusowe zgodnie z sugestią w [tym artykule](https://docs.microsoft.com/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Instalator nie może zaktualizować metadanych rejestru

| Operacja | Szczegóły błędu | Obejście |
|-----------|---------------|------------|
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do nadmiernego użycia magazynu. Aby uniknąć tej aktualizacji, wpis rejestru przycinanie plikówFS. | Dostosuj klucz rejestru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ustaw wartość Dword na 1. |
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do nadmiernego użycia magazynu. Aby tego uniknąć, zaktualizuj wpis rejestru SnapOptimization. | Utwórz klucz rejestru **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** z pustą wartością ciągu. |

## <a name="registration-and-agent-related-issues"></a>Rejestracja i kwestie związane z agentem

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Pchanie agentów do chronionych serwerów | Poświadczenia, które są określone dla serwera są nieprawidłowe. | **Jeśli zalecane działanie wyświetlane w produkcie nie działa, należy wykonać następujące czynności:** <br> Spróbuj zainstalować agenta ochrony ręcznie na serwerze produkcyjnym, jak określono w [tym artykule](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).|
| Agent kopii zapasowej platformy Azure nie może połączyć się z usługą Azure Backup service (identyfikator: 100050) | Agent kopii zapasowej platformy Azure nie może połączyć się z usługą Azure Backup. | **Jeśli zalecane działanie wyświetlane w produkcie nie działa, należy wykonać następujące czynności:** <br>1. Uruchom następujące polecenie z podwyższonego poziomu wiersza: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Spowoduje to otwarcie okna programu Internet Explorer. <br/> 2. Przejdź do **Narzędzia** > **Opcje internetowe Połączenia** > **ustawienia** > **SIECI LAN**. <br/> 3. Zmień ustawienia, aby użyć serwera proxy. Następnie podaj szczegóły serwera proxy.<br/> 4. Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na te [adresy URL](install-mars-agent.md#verify-internet-access) i [adres IP.](install-mars-agent.md#verify-internet-access)|
| Instalacja usługi Azure Backup Agent nie powiodła się | Instalacja usług odzyskiwania platformy Microsoft Azure nie powiodła się. Wszystkie zmiany wprowadzone w systemie przez instalację usług Microsoft Azure Recovery Services zostały wycofane. (ID: 4024) | Ręcznie zainstaluj usługę Azure Agent.

## <a name="configuring-protection-group"></a>Konfigurowanie grupy ochrony

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie grup ochrony | Program DPM nie może wyliczyć składnika aplikacji na chronionym komputerze (nazwa komputera chronionego). | Wybierz **odśwież** na ekranie interfejsu użytkownika grupy zabezpieczeń konfiguruj na odpowiednim poziomie źródła danych/składnika. |
| Konfigurowanie grup ochrony | Nie można skonfigurować ochrony | Jeśli serwer chroniony jest serwerem SQL, sprawdź, czy uprawnienia roli sysadmin zostały dostarczone do konta systemowego (NTAuthority\System) na chronionym komputerze, jak opisano w [tym artykule](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh757977(v=sc.12)).
| Konfigurowanie grup ochrony | W puli magazynów nie ma wystarczającej ilości wolnego miejsca dla tej grupy ochrony. | Dyski dodane do puli magazynu [nie powinny zawierać partycji](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)). Usuń wszystkie istniejące woluminy na dyskach. Następnie dodaj je do puli magazynu.|
| Zmiana zasad |Nie można zmodyfikować zasad tworzenia kopii zapasowej. Błąd: bieżąca operacja nie powiodła się z powodu błędu usługi wewnętrznej [0x29834]. Ponów próbę wykonania operacji po pewnym czasie. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. | **Spowodować:**<br/>Ten błąd występuje w trzech warunkach: gdy ustawienia zabezpieczeń są włączone, podczas próby zmniejszenia zakresu przechowywania poniżej wartości minimalnych określonych wcześniej i gdy jesteś w wersji nieobsługiwani. (Nieobsługiwały wersje są wersjami poniżej wersji 2.0.9052 programu Microsoft Azure Backup Server i usługi Azure Backup Server 1). <br/>**Zalecane działanie:**<br/> Aby kontynuować aktualizacje związane z zasadami, ustaw okres przechowywania powyżej określonego minimalnego okresu przechowywania. (Minimalny okres przechowywania wynosi siedem dni w przypadku dziennego, cztery tygodnie w tygodniu, trzy tygodnie w ujęciu miesięcznym lub jeden rok w skali roku). <br><br>Opcjonalnie innym preferowanym podejściem jest zaktualizowanie agenta kopii zapasowej i serwera kopii zapasowej platformy Azure w celu wykorzystania wszystkich aktualizacji zabezpieczeń. |

## <a name="backup"></a>Backup

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Wystąpił nieoczekiwany błąd podczas pracy zadania. Urządzenie nie jest gotowe. | **Jeśli zalecane działanie wyświetlane w produkcie nie działa, należy wykonać następujące czynności:** <br> <ul><li>Ustaw przestrzeń magazynu kopii w tle na nieograniczoną liczbę elementów w grupie ochrony, a następnie uruchom sprawdzanie spójności.<br></li> (LUB) <li>Spróbuj usunąć istniejącą grupę ochrony i utworzyć wiele nowych grup. Każda nowa grupa ochrony powinna mieć w sobie pojedynczy element.</li></ul> |
| Backup | Jeśli kopię zapasową tylko stan systemu, sprawdź, czy jest wystarczająco dużo wolnego miejsca na chronionym komputerze do przechowywania kopii zapasowej stanu systemu. | <ol><li>Sprawdź, czy kopia zapasowa systemu Windows Server jest zainstalowana na chronionym komputerze.</li><li>Sprawdź, czy na chronionym komputerze jest wystarczająco dużo miejsca dla stanu systemu. Najprostszym sposobem sprawdzenia tego jest przejście do chronionego komputera, otwarcie programu Kopia zapasowa systemu Windows Server, kliknięcie wybranych opcji, a następnie wybranie narzędzia BMR. Następnie interfejs użytkownika informuje, ile miejsca jest wymagane. Otwórz harmonogram > **tworzenia kopii zapasowych w lokalnej kopii** > **zapasowej** **WSB** > **Wybierz pełną konfigurację kopii** > zapasowej **(wyświetlany** jest rozmiar). Użyj tego rozmiaru do weryfikacji.</li></ol>
| Backup | Awaria kopii zapasowej dla BMR | Jeśli rozmiar BMR jest duży, przenieś niektóre pliki aplikacji na dysk systemu operacyjnego i ponów próbę. |
| Backup | Opcja ponownego ceł maszyny Wirtualnej VMware na nowym serwerze kopii zapasowej platformy Microsoft Azure nie jest ana jako dostępna do dodania. | Właściwości VMware są wskazywały na stare, wycofane wystąpienie serwera kopii zapasowej platformy Microsoft Azure. Aby rozwiązać ten problem:<br><ol><li>W obszarze VCenter (odpowiednik SC-VMM) przejdź do karty **Podsumowanie,** a następnie do **atrybutów niestandardowych**.</li>  <li>Usuń starą nazwę serwera kopii zapasowych platformy Microsoft Azure z wartości **serwera DPMServer.**</li>  <li>Wróć do nowego serwera kopii zapasowych platformy Microsoft Azure i zmodyfikuj PG.  Po zaznaczeniu przycisku **Odśwież** zostanie wyświetlone pole wyboru z polem wyboru, które jest dostępne do dodania do ochrony.</li></ol> |
| Backup | Błąd podczas uzyskiwania dostępu do plików/folderów udostępnionych | Spróbuj zmodyfikować ustawienia antywirusowe zgodnie z sugestią w tym artykule [Uruchom oprogramowanie antywirusowe na serwerze programu DPM](https://docs.microsoft.com/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).|

## <a name="change-passphrase"></a>Zmienianie hasła

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Zmienianie hasła |Wprowadzony kod PIN zabezpieczeń jest niepoprawny. Podaj poprawny kod PIN zabezpieczeń, aby wykonać tę operację. |**Spowodować:**<br/> Ten błąd występuje po wprowadzeniu nieprawidłowego lub wygasłego kodu PIN zabezpieczeń podczas wykonywania operacji krytycznej (takiej jak zmiana hasła). <br/>**Zalecane działanie:**<br/> Aby wykonać operację, należy wprowadzić prawidłowy kod PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do magazynu usług odzyskiwania. Następnie przejdź do pozycji Właściwości **ustawień** > **Generowanie** > **numeru PIN zabezpieczeń**. Użyj tego numeru PIN, aby zmienić hasło. |
| Zmienianie hasła |Operacja nie powiodła się. Identyfikator: 120002 |**Spowodować:**<br/>Ten błąd występuje, gdy ustawienia zabezpieczeń są włączone lub podczas próby zmiany hasła podczas korzystania z nieobsługiwałej wersji.<br/>**Zalecane działanie:**<br/> Aby zmienić hasło, należy najpierw zaktualizować agenta kopii zapasowej do wersji minimalnej, która wynosi 2.0.9052. Należy również zaktualizować usługę Azure Backup Server do minimum aktualizacji 1, a następnie wprowadzić prawidłowy kod PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do witryny Azure portal i przejdź do magazynu usług odzyskiwania. Następnie przejdź do pozycji Właściwości **ustawień** > **Generowanie** > **numeru PIN zabezpieczeń**. Użyj tego numeru PIN, aby zmienić hasło. |

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie powiadomień e-mail przy użyciu konta usługi Office 365 |Identyfikator błędu: 2013| **Spowodować:**<br> Próba użycia konta usługi Office 365 <br>**Zalecane działanie:**<ol><li> Pierwszą rzeczą, aby upewnić się, że "Zezwalaj na przekaźnik anonimowy na łączniku odbierania" dla serwera programu DPM jest skonfigurowany w programie Exchange. Aby uzyskać więcej informacji na temat konfigurowania tego sposobu, zobacz [Zezwalanie na przekazywanie anonimowe na łączniku odbierania](https://docs.microsoft.com/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019).</li> <li> Jeśli nie możesz użyć wewnętrznego przekaźnika SMTP i musisz skonfigurować go przy użyciu serwera usługi Office 365, możesz skonfigurować usługi IIS jako przekaźnik. Skonfiguruj serwer programu DPM do [przekazywania protokołu SMTP do usługi O365 przy użyciu usług IIS](https://docs.microsoft.com/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)).<br><br>  Pamiętaj, aby użyć\@domain.com formatu użytkownika, a *nie* domeny\user.<br><br><li>Program DPM wskazuje na używanie nazwy serwera lokalnego jako serwera SMTP, port 587. Następnie wskaż go na wiadomość e-mail użytkownika, z których powinny pochodzić wiadomości e-mail.<li> Nazwa użytkownika i hasło na stronie konfiguracji SMTP programu DPM powinny dotyczyć konta domeny w domenie, w którą jest włączony program DPM. </li><br> Podczas zmiany adresu serwera SMTP należy wprowadzić zmiany w nowych ustawieniach, zamknąć pole ustawień, a następnie ponownie otworzyć je, aby upewnić się, że odzwierciedla nową wartość.  Po prostu zmiana i testowanie nie zawsze może spowodować, że nowe ustawienia zajmą się efektem, więc testowanie go w ten sposób jest najlepszym rozwiązaniem.<br><br>W dowolnym momencie tego procesu można wyczyścić te ustawienia, zamykając konsolę programu DPM i edytując następujące klucze rejestru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName keys**. Możesz dodać je z powrotem do interfejsu użytkownika po ponownym uruchomieniu.

## <a name="common-issues"></a>Typowe problemy

W tej sekcji opisano typowe błędy, które mogą wystąpić podczas korzystania z usługi Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Komunikat o błędzie | Zalecana akcja |
-- | --
Tworzenie kopii zapasowej nie powiodło się, ponieważ replika kopii zapasowej na dysku jest nieprawidłowa lub nie istnieje. | Aby rozwiązać ten problem, sprawdź poniższe kroki i ponów próbę wykonania operacji: <br/> 1. Tworzenie punktu odzyskiwania dysku<br/> 2. Uruchom sprawdzanie spójności w źródle danych <br/> 3. Zatrzymaj ochronę źródła danych, a następnie ponownie skonfiguruj ochronę tego źródła danych

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Komunikat o błędzie | Zalecana akcja |
-- | --
Migawka woluminu źródłowego nie powiodła się, ponieważ metadane w replice są nieprawidłowe. | Utwórz punkt odzyskiwania dysku tego źródła danych i ponów próbę utworzenia kopii zapasowej online ponownie

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Komunikat o błędzie | Zalecana akcja |
-- | --
Migawka woluminu źródłowego nie powiodła się z powodu niespójnej repliki źródła danych. | Uruchom sprawdzanie spójności w tym źródle danych i spróbuj ponownie

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Komunikat o błędzie | Zalecana akcja |
-- | --
Próba utworzenia kopii zapasowej nie powiodła się, ponieważ nie można sklonować repliki kopii zapasowej na dysku.| Upewnij się, że wszystkie poprzednie pliki replik kopii zapasowej dysku (vhdx) są odinstalowane i podczas tworzenia kopii zapasowych na dysku nie jest w toku.
