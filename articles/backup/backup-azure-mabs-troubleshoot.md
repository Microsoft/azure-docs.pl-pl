---
title: Rozwiązywanie problemów ze składnikiem Azure Backup Server
description: Rozwiązywanie problemów z instalacją, rejestracją Azure Backup Server oraz tworzeniem kopii zapasowych i przywracaniem obciążeń aplikacji.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 644946ca90c2893ba3d87f9d2ff8bfd8325f4715
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514754"
---
# <a name="troubleshoot-azure-backup-server"></a>Rozwiązywanie problemów ze składnikiem Azure Backup Server

Skorzystaj z informacji w poniższych tabelach, aby rozwiązać problemy z błędami, które występują podczas korzystania z Azure Backup Server.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Zalecamy wykonanie następującej weryfikacji przed rozpoczęciem rozwiązywania problemów z Microsoft Azure Backup Server (MABS):

- [Upewnij Microsoft Azure agent usługi Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje łączność sieciowa między agentem usługi MARS i platformą Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). W razie potrzeby uruchom ponownie i spróbuj ponownie wykonać operację
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- Jeśli rejestracja nie działa, upewnij się, że serwer, na którym próbujesz zainstalować pakiet Azure Backup Server nie został jeszcze zarejestrowany w innym magazynie
- Jeśli instalacja wypychana zakończy się niepowodzeniem, sprawdź, czy agent programu DPM jest już obecny. Jeśli tak, odinstaluj agenta i ponów próbę instalacji
- [Upewnij się, że żaden inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).<br>
- Upewnij się, że usługa SQL Agent jest uruchomiona i ustawiona na automatyczną na serwerze USŁUGI MABS<br>

## <a name="configure-antivirus-for-mabs-server"></a>Konfigurowanie oprogramowania antywirusowego dla serwera USŁUGI MABS

Rozwiązanie MABS jest zgodne z najpopularniejszymi produktami antywirusowymi. Zalecamy następujące kroki, aby uniknąć konfliktów:

1. **Wyłącz monitorowanie w czasie rzeczywistym** — wyłącz monitorowanie w czasie rzeczywistym przez oprogramowanie antywirusowe dla następujących funkcji:
    - `C:\Program Files<MABS Installation path>\XSD` Folder
    - `C:\Program Files<MABS Installation path>\Temp` Folder
    - Litera dysku Nowoczesny magazyn kopii zapasowych woluminu
    - Dzienniki repliki i transferu: aby to zrobić, wyłącz monitorowanie w czasie rzeczywistymdpmra.exe **,** który znajduje się w folderze `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Monitorowanie w czasie rzeczywistym obniża wydajność, ponieważ oprogramowanie antywirusowe skanuje repliki za każdym razem, gdy serwer MABS synchronizuje się z chronionym serwerem, i skanuje wszystkie pliki, których to dotyczy, za każdym razem, gdy mabs stosuje zmiany do replik.
    - Konsola administratora: aby uniknąć wpływu na wydajność, wyłącz monitorowanie w czasie rzeczywistymcsc.exe **procesu.** Proces **csc.exe** jest kompilatorem języka C, a monitorowanie w czasie rzeczywistym może obniżyć wydajność, ponieważ oprogramowanie antywirusowe skanuje pliki emitowane przez procescsc.exepodczas generowania komunikatów \# XML.  **CSC.exe** znajduje się w następujących ścieżkach:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - W przypadku agenta MARS zainstalowanego na serwerze USŁUGI MABS zaleca się wykluczenie następujących plików i lokalizacji:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` jako proces
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Lokalizacja na początku (jeśli nie używasz lokalizacji standardowej)
2. **Wyłącz monitorowanie w czasie rzeczywistym** na chronionym serwerze: wyłącz monitorowanie w czasie rzeczywistym programu **dpmra.exe,** który znajduje się w folderze `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` , na serwerze chronionym.
3. Konfigurowanie oprogramowania antywirusowego w celu usuwania zainfekowanych plików na chronionych serwerach i serwerze **MABS:** aby zapobiec uszkodzeniem danych replik i punktów odzyskiwania, skonfiguruj oprogramowanie antywirusowe tak, aby usuwało zainfekowane pliki, zamiast automatycznego czyszczenia lub kwarantanny. Automatyczne czyszczenie i poddanie kwarantannie może spowodować, że oprogramowanie antywirusowe zmodyfikuje pliki, wprowadzając zmiany, których mabs nie może wykryć.

Należy uruchomić synchronizację ręczną ze spójnością. Sprawdź zadanie za każdym razem, gdy oprogramowanie antywirusowe usuwa plik z repliki, mimo że replika jest oznaczona jako niespójna.

### <a name="mabs-installation-folders"></a>Foldery instalacji mabs

Domyślne foldery instalacji programu DPM są następujące:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Możesz również uruchomić następujące polecenie, aby znaleźć ścieżkę folderu instalacji:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Rejestrowanie w magazynie | Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. | Zalecana akcja: <br> <ul><li> Pobierz najnowszy plik poświadczeń z magazynu i spróbuj ponownie. <br>(OR)</li> <li> Jeśli poprzednia akcja nie zadziałała, spróbuj pobrać poświadczenia do innego katalogu lokalnego lub utworzyć nowy magazyn. <br>(OR)</li> <li> Spróbuj zaktualizować ustawienia daty i czasu zgodnie z opisem w [tym artykule.](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided) <br>(OR)</li> <li> Sprawdź, czy plik c:\windows\temp ma więcej niż 65000 plików. Przenieś nieodświeżone pliki do innej lokalizacji lub usuń elementy w folderze Temp. <br>(OR)</li> <li> Sprawdź stan certyfikatów. <br> a. Otwórz **okno Zarządzanie certyfikatami komputerów** (w Panel sterowania). <br> b. Rozwiń **węzeł Osobiste** i jego węzeł **podrzędny Certyfikaty**.<br> c.  Usuń certyfikat **Narzędzia platformy Windows Azure.** <br> d. Ponów próbę rejestracji w Azure Backup klienta. <br> (OR) </li> <li> Sprawdź, czy są jakieś zasady grupy. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replika jest niespójna

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Replika jest niespójna | Sprawdź, czy opcja automatycznego sprawdzania spójności w Kreatorze grupy ochrony jest włączona. Aby uzyskać więcej informacji na temat opcji replikacji i sprawdzania spójności, zobacz [ten artykuł.](/system-center/dpm/create-dpm-protection-groups)<br> <ol><li> W przypadku kopii zapasowej stanu systemu/kopii zapasowej BMR sprawdź, Kopia zapasowa systemu Windows Server jest zainstalowany na chronionym serwerze.</li><li> Sprawdź problemy związane z przestrzenią w puli magazynów programu DPM na serwerze DPM/Microsoft Azure Backup i przydziel magazyn zgodnie z potrzebami.</li><li> Sprawdź stan serwera Usługa kopiowania woluminów w tle chronionym serwerze. Jeśli jest w stanie wyłączonym, ustaw go tak, aby uruchamiał się ręcznie. Uruchom usługę na serwerze. Następnie wróć do konsoli programu DPM/Microsoft Azure Backup Server i uruchom synchronizację z zadaniem sprawdzania spójności.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Tworzenie punktu odzyskiwania w trybie online nie powiodło się

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Tworzenie punktu odzyskiwania w trybie online nie powiodło się | **Komunikat o** błędzie: Agent Azure Backup Systemu Windows nie mógł utworzyć migawki wybranego woluminu. <br> **Obejście:** Spróbuj zwiększyć miejsce w woluminie repliki i punktu odzyskiwania.<br> <br> **Komunikat o błędzie:** Agent Azure Backup Windows nie może nawiązać połączenia z usługą OBEngine <br> **Obejście:** sprawdź, czy obiekt OBEngine istnieje na liście uruchomionych usług na komputerze. Jeśli usługa OBEngine nie jest uruchomiona, użyj polecenia "net start OBEngine", aby uruchomić usługę OBEngine. <br> <br> **Komunikat o** błędzie: Hasło szyfrowania dla tego serwera nie jest ustawione. Skonfiguruj hasło szyfrowania. <br> **Obejście:** Spróbuj skonfigurować hasło szyfrowania. Jeśli to się nie powiedzie, należy wykonać następujące czynności: <br> <ol><li>Sprawdź, czy lokalizacja na początku znajduje się. Jest to lokalizacja wymieniona w kluczu **rejestru,** HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Configo nazwie **ScratchLocation** powinna istnieć.</li><li> Jeśli lokalizacja na początku istnieje, spróbuj ponownie zarejestrować się przy użyciu starego hasła. *Zawsze, gdy skonfigurujesz hasło szyfrowania, zapisz je w bezpiecznej lokalizacji.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Oryginalne i zewnętrzne serwery programu DPM muszą być zarejestrowane w tym samym magazynie

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Przywracanie | **Kod błędu:** CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Komunikat o błędzie:** Oryginalne i zewnętrzne serwery programu DPM muszą być zarejestrowane w tym samym magazynie | **Przyczyna:** Ten problem występuje, gdy próbujesz przywrócić pliki na alternatywny serwer z oryginalnego serwera przy użyciu opcji odzyskiwania zewnętrznego programu DPM, a odzyskiwany serwer i oryginalny serwer, z którego tworzyć kopię zapasową danych, nie są skojarzone z tym samym magazynem usługi Recovery Services.<br/> <br/>**Obejście problemu** Aby rozwiązać ten problem, upewnij się, że zarówno oryginalny, jak i alternatywny serwer są zarejestrowane w tym samym magazynie.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Zadania tworzenia punktu odzyskiwania online dla maszyny wirtualnej VMware nie powiodły się

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Zadania tworzenia punktu odzyskiwania online dla maszyny wirtualnej VMware nie powiodły się. Program DPM napotkał błąd z programu VMware podczas próby uzyskania informacji z rozwiązania ChangeTracking. ErrorCode — FileFaultFault (identyfikator 33621) |  <ol><li> Zresetuj CTK w programie VMware dla maszyn wirtualnych, których dotyczy problem.</li> <li>Sprawdź, czy w programie VMware nie ma dysku niezależnego.</li> <li>Zatrzymaj ochronę maszyn wirtualnych, których dotyczy problem, i ponownie wychronij je za pomocą **przycisku Odśwież.** </li><li>Uruchom cc dla maszyn wirtualnych, których dotyczy problem.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynatora agenta programu DPM na serwerze

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Wypychanie agentów do serwerów chronionych | Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynator agenta programu DPM na \<ServerName> platformie . | **Jeśli zalecana akcja pokazana w produkcie nie działa, wykonaj następujące kroki:** <ul><li> Jeśli dołączasz komputer z niezaufanej domeny, wykonaj [następujące kroki.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br> (OR) </li><li> Jeśli dołączasz komputer z zaufanej domeny, rozwiąż problemy, korzystając z kroków opisanych w [tym blogu.](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726) <br>(OR)</li><li> Spróbuj wyłączyć oprogramowanie antywirusowe w kroku rozwiązywania problemów. Jeśli problem zostanie rozwiązany, zmodyfikuj ustawienia programu antywirusowego zgodnie z sugestią w [tym artykule.](/system-center/dpm/run-antivirus-server)</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Instalator nie może zaktualizować metadanych rejestru

| Operacja | Szczegóły błędu | Obejście |
|-----------|---------------|------------|
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do przesyt użycia magazynu. Aby tego uniknąć, należy zaktualizować wpis rejestru przycinania systemu plików ReFS. | Dostosuj klucz rejestru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim.** Ustaw wartość Dword na 1. |
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do przesytego użycia magazynu. Aby tego uniknąć, zaktualizuj wpis rejestru SnapOptimization woluminu. | Utwórz klucz rejestru **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** z pustą wartością ciągu. |

## <a name="registration-and-agent-related-issues"></a>Problemy związane z rejestracją i agentem

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Wypychanie agentów do chronionych serwerów | Poświadczenia określone dla serwera są nieprawidłowe. | **Jeśli zalecana akcja wyświetlana w produkcie nie działa, należy wykonać następujące czynności:** <br> Spróbuj zainstalować agenta ochrony ręcznie na serwerze produkcyjnym, jak określono w [tym artykule.](/system-center/dpm/deploy-dpm-protection-agent)|
| Azure Backup agent nie mógł nawiązać połączenia z usługą Azure Backup (identyfikator: 100050) | Agent Azure Backup nie mógł nawiązać połączenia z Azure Backup usługą. | **Jeśli zalecana akcja wyświetlana w produkcie nie działa, należy wykonać następujące czynności:** <br>1. Uruchom następujące polecenie w wierszu z podwyższonym poziomem uprawnień: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Spowoduje to otwarcie Internet Explorer aplikacji. <br/> 2. Przejdź do menu **Narzędzia Opcje**  >  **internetowe Połączenia**  >  **ustawienia** sieci  >  **LAN.** <br/> 3. Zmień ustawienia, aby używać serwera proxy. Następnie podaj szczegóły serwera proxy.<br/> 4. Jeśli komputer ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na maszynie lub serwerze proxy zezwalają na te [adresy](install-mars-agent.md#verify-internet-access) [URL i IP.](install-mars-agent.md#verify-internet-access)|
| Azure Backup instalacja agenta nie powiodła się | Instalacja Microsoft Azure Recovery Services nie powiodła się. Wszystkie zmiany wprowadzone w systemie przez usługę Microsoft Azure Recovery Services zostały wycofane. (IDENTYFIKATOR: 4024) | Ręcznie zainstaluj agenta platformy Azure.

## <a name="configuring-protection-group"></a>Konfigurowanie grupy ochrony

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie grup ochrony | Program DPM nie mógł wyliczyć składnika aplikacji na komputerze chronionym (nazwa komputera chronionego). | Wybierz **pozycję Odśwież** na ekranie interfejsu użytkownika konfigurowania grupy ochrony na odpowiednim poziomie źródła danych/składnika. |
| Konfigurowanie grup ochrony | Nie można skonfigurować ochrony | Jeśli chroniony serwer jest serwerem SQL, sprawdź, czy na koncie systemowym (NTAuthority\System) na chronionym komputerze zostały podane uprawnienia roli sysadmin, zgodnie z opisem w [tym artykule.](/system-center/dpm/back-up-sql-server)
| Konfigurowanie grup ochrony | W puli magazynów dla tej grupy ochrony jest za mało wolnego miejsca. | Dyski dodawane do puli magazynów nie [powinny zawierać partycji](/system-center/dpm/create-dpm-protection-groups). Usuń wszystkie istniejące woluminy na dyskach. Następnie dodaj je do puli magazynów.|
| Zmiana zasad |Nie można zmodyfikować zasad tworzenia kopii zapasowych. Błąd: Bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x29834]. Spróbuj ponownie wykonać operację po upływie pewnego czasu. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. | **Przyczyna:**<br/>Ten błąd występuje w trzech warunkach: gdy ustawienia zabezpieczeń są włączone, gdy próbujesz zmniejszyć zakres przechowywania poniżej wartości minimalnych określonych wcześniej i gdy używasz nieobsługiwanej wersji. (Nieobsługiwane wersje są niższe niż Microsoft Azure Backup Server w wersji 2.0.9052 i Azure Backup Server update 1). <br/>**Zalecana akcja:**<br/> Aby kontynuować aktualizacje związane z zasadami, ustaw okres przechowywania powyżej określonego minimalnego okresu przechowywania. (Minimalny okres przechowywania to siedem dni dziennie, cztery tygodnie co tydzień, trzy tygodnie co miesiąc lub jeden rok dla roku). <br><br>Opcjonalnie innym preferowanym podejściem jest zaktualizowanie agenta kopii zapasowej i Azure Backup Server wykorzystania wszystkich aktualizacji zabezpieczeń. |

## <a name="backup"></a>Backup

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Wystąpił nieoczekiwany błąd podczas uruchamiania zadania. Urządzenie nie jest gotowe. | **Jeśli zalecana akcja wyświetlana w produkcie nie działa, należy wykonać następujące czynności:** <br> <ul><li>Ustaw pozycję Miejsce do magazynowania kopii w tle na nieograniczoną liczbę elementów w grupie ochrony, a następnie uruchom sprawdzanie spójności.<br></li> (OR) <li>Spróbuj usunąć istniejącą grupę ochrony i utworzyć wiele nowych grup. Każda nowa grupa ochrony powinna mieć pojedynczy element.</li></ul> |
| Backup | W przypadku tworzenia kopii zapasowej tylko stanu systemu sprawdź, czy na chronionym komputerze jest wystarczająca ilość wolnego miejsca do przechowywania kopii zapasowej stanu systemu. | <ol><li>Sprawdź, Kopia zapasowa systemu Windows Server jest zainstalowany na chronionej maszynie.</li><li>Sprawdź, czy na chronionym komputerze jest wystarczająca ilość miejsca dla stanu systemu. Najprostszym sposobem sprawdzenia tego jest wybranie komputera chronionego, otwarcie komputera Kopia zapasowa systemu Windows Server kliknięciem wybranych opcji, a następnie wybraniem opcji BMR. Następnie interfejs użytkownika informuje o tym, ile miejsca jest wymagane. Otwórz harmonogram tworzenia kopii zapasowych lokalnych kopii zapasowych programu **WSB**  >    >    >  **Wybierz konfigurację kopii**  >  **zapasowej Pełny serwer** (wyświetlany jest rozmiar). Użyj tego rozmiaru do weryfikacji.</li></ol>
| Backup | Niepowodzenie kopii zapasowej dla BMR | Jeśli rozmiar BMR jest duży, przenieś niektóre pliki aplikacji na dysk systemu operacyjnego i spróbuj ponownie. |
| Backup | Opcja ponownego włączania ochrony maszyny wirtualnej VMware na nowym Microsoft Azure Backup Server nie jest dostępna do dodania. | Właściwości programu VMware wskazują na stare, wycofane wystąpienie Microsoft Azure Backup Server. Aby rozwiązać ten problem:<br><ol><li>W programie VCenter (odpowiednik programu SC-VMM) przejdź do karty **Podsumowanie,** a następnie do opcji **Atrybuty niestandardowe.**</li>  <li>Usuń starą Microsoft Azure Backup serwera z wartości **DPMServer.**</li>  <li>Wstecz do nowego serwera Microsoft Azure Backup i zmodyfikuj pg.  Po wybraniu przycisku **Odśwież** maszyna wirtualna zostanie wyświetlona z polem wyboru dostępnym do dodania do ochrony.</li></ol> |
| Backup | Błąd podczas uzyskiwania dostępu do plików/folderów udostępnionych | Spróbuj zmodyfikować ustawienia ochrony antywirusowej zgodnie z sugestią w tym artykule Uruchamianie oprogramowania [antywirusowego na serwerze DPM.](/system-center/dpm/run-antivirus-server)|

## <a name="change-passphrase"></a>Zmienianie hasła

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Zmienianie hasła |Wprowadzony numer PIN zabezpieczeń jest nieprawidłowy. Podaj prawidłowy numer PIN zabezpieczeń, aby ukończyć tę operację. |**Przyczyna:**<br/> Ten błąd występuje, gdy wprowadzasz nieprawidłowy lub wygasły numer PIN zabezpieczeń podczas wykonywania operacji krytycznej (takiej jak zmiana hasła). <br/>**Zalecana akcja:**<br/> Aby ukończyć operację, należy wprowadzić prawidłowy numer PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do Azure Portal i przejdź do magazynu usługi Recovery Services. Następnie przejdź do strony **Właściwości**  >  **ustawień**  >  **Generuj numer PIN zabezpieczeń.** Użyj tego numeru PIN, aby zmienić hasło. |
| Zmienianie hasła |Operacja nie powiodła się. Identyfikator: 120002 |**Przyczyna:**<br/>Ten błąd występuje, gdy ustawienia zabezpieczeń są włączone lub gdy próbujesz zmienić hasło, gdy używasz nieobsługiwanej wersji.<br/>**Zalecana akcja:**<br/> Aby zmienić hasło, należy najpierw zaktualizować agenta kopii zapasowej do minimalnej wersji, czyli 2.0.9052. Należy również zaktualizować Azure Backup Server do wersji Update 1, a następnie wprowadzić prawidłowy numer PIN zabezpieczeń. Aby uzyskać numer PIN, zaloguj się do Azure Portal i przejdź do magazynu usługi Recovery Services. Następnie przejdź do strony **Właściwości**  >  **ustawień**  >  **Generuj numer PIN zabezpieczeń.** Użyj tego numeru PIN, aby zmienić hasło. |

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie powiadomień e-mail przy użyciu konta służbowego |Identyfikator błędu: 2013| **Przyczyna:**<br> Próba użycia konta służbowego <br>**Zalecana akcja:**<ol><li> Pierwszą rzeczą, którą należy zapewnić, jest skonfigurowanie w programie Exchange ustawienia "Zezwalaj na anonimowe przekaźniki na łączniku odbierania" dla serwera DPM. Aby uzyskać więcej informacji na temat konfigurowania tej metody, zobacz Zezwalaj na [anonimowe przekaźniki w łączniku odbierania](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Jeśli nie możesz użyć wewnętrznego przekazywania SMTP i musisz skonfigurować go przy użyciu serwera usługi Office 365, możesz skonfigurować usługi IIS jako przekaźnik. Skonfiguruj serwer programu DPM do [przekazywania protokołu SMTP do usługi Office 365 przy użyciu usług IIS.](/exchange/mail-flow/test-smtp-with-telnet)<br><br>  Pamiętaj, aby użyć formatu \@ domain.com, a *nie* domeny\użytkownika.<br><br><li>Wskaż programowi DPM, aby użyć nazwy serwera lokalnego jako serwera SMTP i portu 587. Następnie wskaż adres e-mail użytkownika, z który powinny pochodzić wiadomości e-mail.<li> Nazwa użytkownika i hasło na stronie konfiguracji SMTP programu DPM powinny być dla konta domeny w domenie, w których znajduje się program DPM. </li><br> Podczas zmieniania adresu serwera SMTP wprowadź zmianę nowych ustawień, zamknij pole ustawień, a następnie otwórz go ponownie, aby upewnić się, że odzwierciedla nową wartość.  Samo zmiana i testowanie nie zawsze może spowodować, że nowe ustawienia zajdą w życie, więc testowanie ich w ten sposób jest najlepszym rozwiązaniem.<br><br>W dowolnym momencie tego procesu można wyczyścić te ustawienia, zamykając konsolę programu DPM i edytując następujące klucze rejestru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ Usuń klucze <br/> SMTPPassword i SMTPUserName.** Możesz dodać je z powrotem do interfejsu użytkownika po jego uruchomieniu ponownie.

## <a name="common-issues"></a>Typowe problemy

W tej sekcji omykamy typowe błędy, które mogą wystąpić podczas korzystania z Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Komunikat o błędzie | Zalecana akcja |
-- | --
Tworzenie kopii zapasowej nie powiodło się, ponieważ replika kopii zapasowej na dysku jest nieprawidłowa lub nie istnieje. | Aby rozwiązać ten problem, sprawdź poniższe kroki i spróbuj ponownie wykonać operację: <br/> 1. Tworzenie punktu odzyskiwania dysku<br/> 2. Uruchamianie sprawdzania spójności źródła danych <br/> 3. Zatrzymaj ochronę źródła danych, a następnie skonfiguruj ponownie ochronę dla tego źródła danych

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Komunikat o błędzie | Zalecana akcja |
-- | --
Migawka woluminu źródłowego nie powiodła się, ponieważ metadane repliki są nieprawidłowe. | Utwórz punkt odzyskiwania dysku tego źródła danych i ponów próbę wykonania kopii zapasowej online

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Komunikat o błędzie | Zalecana akcja |
-- | --
Migawka woluminu źródłowego nie powiodła się z powodu niespójnej repliki źródła danych. | Uruchom sprawdzanie spójności dla tego źródła danych i spróbuj ponownie

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Komunikat o błędzie | Zalecana akcja |
-- | --
Próba utworzenia kopii zapasowej nie powiodła się, ponieważ nie można sklonować repliki kopii zapasowej na dysku.| Upewnij się, że wszystkie poprzednie pliki repliki kopii zapasowej dysku (vhdx) są odinstalowane i podczas tworzenia kopii zapasowych w trybie online nie jest w toku żadne kopie zapasowe dysku na dysku
