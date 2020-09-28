---
title: Rozwiązywanie problemów ze składnikiem Azure Backup Server
description: Rozwiązywanie problemów z instalacją, rejestracją Azure Backup Server oraz wykonywaniem kopii zapasowych i przywracaniem obciążeń aplikacji.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 09e5fe5da7e316257cbbdcb89074fe8a4bc692c0
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403011"
---
# <a name="troubleshoot-azure-backup-server"></a>Rozwiązywanie problemów ze składnikiem Azure Backup Server

Skorzystaj z informacji podanych w poniższych tabelach, aby rozwiązać problemy występujące podczas korzystania z Azure Backup Server.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Przed rozpoczęciem rozwiązywania problemów Microsoft Azure Backup Server (serwera usługi MAB) zalecamy wykonanie następującej weryfikacji:

- [Upewnij się, że agent Microsoft Azure Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zapewnianie łączności sieciowej między agentem MARS i platformą Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). W razie potrzeby uruchom ponownie, a następnie spróbuj ponownie wykonać operację.
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- Jeśli rejestracja kończy się niepowodzeniem, upewnij się, że serwer, na którym próbujesz zainstalować Azure Backup Server nie jest już zarejestrowany w innym magazynie
- Jeśli instalacja wypychana zakończy się niepowodzeniem, sprawdź, czy agent programu DPM jest już obecny. Jeśli tak, odinstaluj agenta i ponów próbę instalacji
- [Upewnij się, że żaden inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).<br>
- Upewnij się, że usługa agenta SQL jest uruchomiona i ma ustawioną wartość automatyczne na serwerze serwera usługi MAB<br>

## <a name="configure-antivirus-for-mabs-server"></a>Konfigurowanie oprogramowania antywirusowego dla serwera serwera usługi MAB

SERWERA usługi MAB jest zgodna z najpopularniejszymi programami antywirusowymi. Aby uniknąć konfliktów, zalecamy wykonanie następujących czynności:

1. **Wyłącz monitorowanie** w czasie rzeczywistym — Wyłącz monitorowanie w czasie rzeczywistym przez oprogramowanie antywirusowe w następujący sposób:
    - `C:\Program Files<MABS Installation path>\XSD` system32\drivers\etc
    - `C:\Program Files<MABS Installation path>\Temp` system32\drivers\etc
    - Litera dysku Nowoczesny magazyn kopii zapasowych
    - Pliki replik i transferów: w tym celu Wyłącz monitorowanie w czasie rzeczywistym **dpmra.exe**, które znajdują się w folderze `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Monitorowanie w czasie rzeczywistym powoduje spadek wydajności, ponieważ oprogramowanie antywirusowe skanuje repliki za każdym razem, gdy serwera usługi MAB synchronizuje się z chronionym serwerem i skanuje wszystkie pliki, których to dotyczy, za każdym razem, gdy serwera usługi MAB stosuje zmiany w replikach.
    - Konsola administratora: aby uniknąć wpływu na wydajność, wyłącz monitorowanie w czasie rzeczywistym procesu **csc.exe** . Proces **csc.exe** to kompilator języka C \# , a monitorowanie w czasie rzeczywistym może obniżyć wydajność, ponieważ oprogramowanie antywirusowe skanuje pliki emitowane przez proces **csc.exe** podczas generowania komunikatów XML. **CSC.exe** znajduje się w następujących ścieżkach:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - W przypadku agenta MARS zainstalowanego na serwerze serwera usługi MAB zaleca się wykluczenie następujących plików i lokalizacji:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` jako proces
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Lokalizacja zapasowa (jeśli nie używasz lokalizacji standardowej)
2. **Wyłącz monitorowanie w czasie rzeczywistym na chronionym serwerze**: Wyłącz monitorowanie w czasie rzeczywistym **dpmra.exe**, które znajduje się w folderze `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` na serwerze chronionym.
3. **Skonfiguruj oprogramowanie antywirusowe do usuwania zainfekowanych plików na serwerach chronionych i serwerze serwera usługi MAB**: aby zapobiec uszkodzeniu danych replik i punktów odzyskiwania, należy skonfigurować oprogramowanie antywirusowe do usuwania zainfekowanych plików, zamiast ich automatycznego czyszczenia lub poddawania kwarantannie. Automatyczne czyszczenie i poddawania kwarantannie może spowodować, że oprogramowanie antywirusowe modyfikuje pliki, wprowadzając zmiany, które serwera usługi MAB nie mogą wykryć.

Należy uruchomić ręczną synchronizację ze spójnością. Sprawdź zadanie przy każdym usunięciu pliku z repliki przez oprogramowanie antywirusowe, nawet jeśli replika jest oznaczona jako niespójna.

### <a name="mabs-installation-folders"></a>Foldery instalacji serwera usługi MAB

Domyślne foldery instalacji programu DPM są następujące:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Możesz również uruchomić następujące polecenie, aby znaleźć ścieżkę folderu instalacji:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Podano nieprawidłowe poświadczenia magazynu

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Rejestrowanie w magazynie | Podano nieprawidłowe poświadczenia magazynu. Plik jest uszkodzony lub nie ma najnowszych poświadczeń skojarzonych z usługą odzyskiwania. | Zalecana akcja: <br> <ul><li> Pobierz najnowszy plik poświadczeń z magazynu i spróbuj ponownie. <br>ORAZ</li> <li> Jeśli poprzednia akcja nie zadziałała, spróbuj pobrać poświadczenia do innego katalogu lokalnego lub utworzyć nowy magazyn. <br>ORAZ</li> <li> Spróbuj zaktualizować ustawienia daty i godziny zgodnie z opisem w [tym artykule](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided). <br>ORAZ</li> <li> Sprawdź, czy c:\Windows\Temp. ma więcej niż 65000 plików. Przenieś stare pliki do innej lokalizacji lub Usuń elementy z folderu temp. <br>ORAZ</li> <li> Sprawdź stan certyfikatów. <br> a. Otwórz przystawkę **Zarządzanie certyfikatami komputerów** (w panelu sterowania). <br> b. Rozwiń węzeł **osobisty** i jego **Certyfikaty**węzła podrzędnego.<br> c.  Usuń certyfikat **Narzędzia platformy Microsoft Azure**. <br> d. Ponów próbę rejestracji w kliencie Azure Backup. <br> ORAZ </li> <li> Sprawdź, czy istnieją zasady grupy. </li></ul> |

## <a name="replica-is-inconsistent"></a>Replika jest niespójna

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Replika jest niespójna | Sprawdź, czy opcja automatycznego sprawdzania spójności w Kreatorze grupy ochrony jest włączona. Aby uzyskać więcej informacji na temat opcji replikacji i sprawdzania spójności, zobacz [ten artykuł](/system-center/dpm/create-dpm-protection-groups) .<br> <ol><li> W przypadku kopii zapasowej stanu systemu/BMR upewnij się, że na chronionym serwerze zainstalowano Kopia zapasowa systemu Windows Server.</li><li> Sprawdź problemy związane z miejscem w puli magazynów programu DPM na serwerze DPM/Microsoft Azure Backup i Przydziel magazyn zgodnie z potrzebami.</li><li> Sprawdź stan Usługa kopiowania woluminów w tle na chronionym serwerze. Jeśli jest w stanie wyłączonym, ustaw go jako uruchomiony ręcznie. Uruchom usługę na serwerze programu. Następnie wróć do konsoli serwera programu DPM/Microsoft Azure Backup i uruchom zadanie Synchronizuj ze sprawdzaniem spójności.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Tworzenie punktu odzyskiwania w trybie online nie powiodło się

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Tworzenie punktu odzyskiwania w trybie online nie powiodło się | **Komunikat o błędzie**: Agent Azure Backup systemu Windows nie mógł utworzyć migawki wybranego woluminu. <br> **Obejście**: Spróbuj zwiększyć ilość miejsca w woluminie repliki i punktu odzyskiwania.<br> <br> **Komunikat o błędzie**: Agent Azure Backup systemu Windows nie może nawiązać połączenia z usługą usługą obengine <br> **Obejście**: Sprawdź, czy na liście uruchomionych usług na komputerze znajduje się usługą obengine. Jeśli usługa usługą obengine nie jest uruchomiona, użyj polecenia "net start usługą obengine", aby uruchomić usługę usługą obengine. <br> <br> **Komunikat o błędzie**: nie ustawiono hasła szyfrowania dla tego serwera. Skonfiguruj hasło szyfrowania. <br> **Obejście**: spróbuj skonfigurować hasło szyfrowania. Jeśli to się nie powiedzie, wykonaj następujące czynności: <br> <ol><li>Upewnij się, że istnieje lokalizacja tymczasowa. Jest to lokalizacja wymieniona w rejestrze **HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config**, o nazwie **ScratchLocation** .</li><li> Jeśli lokalizacja tymczasowa istnieje, spróbuj ponownie zarejestrować się przy użyciu starego hasła. *Za każdym razem, gdy konfigurujesz hasło szyfrowania, Zapisz je w bezpiecznej lokalizacji.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Oryginalne i zewnętrzne serwery DPM muszą być zarejestrowane w tym samym magazynie

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Przywracanie | **Kod błędu**: błąd CBPServerRegisteredVaultDontMatchWithCurrent/poświadczeń magazynu: 100110 <br/> <br/>**Komunikat o błędzie**: oryginalne i zewnętrzne serwery DPM muszą być zarejestrowane w tym samym magazynie | **Przyczyna**: ten problem występuje, gdy próbujesz przywrócić pliki na alternatywny serwer z oryginalnego serwera przy użyciu opcji odzyskiwania zewnętrznego programu DPM, a serwer, który jest odzyskiwany, oraz oryginalny serwer, z którego są tworzone kopie zapasowe danych, nie są skojarzone z tym samym magazynem Recovery Services.<br/> <br/>**Obejście problemu** Aby rozwiązać ten problem, upewnij się, że zarówno oryginalny, jak i alternatywny serwer są zarejestrowani do tego samego magazynu.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Zadania tworzenia punktu odzyskiwania w trybie online dla maszyny wirtualnej VMware kończą się niepowodzeniem

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Zadania tworzenia punktu odzyskiwania w trybie online dla maszyny wirtualnej VMware kończą się niepowodzeniem. Program DPM napotkał błąd z programu VMware podczas próby uzyskania informacji o śledzenia zmian. ErrorCode-FileFaultFault (identyfikator 33621) |  <ol><li> Zresetuj CTK w oprogramowaniu VMware dla maszyn wirtualnych, których to dotyczy.</li> <li>Sprawdź, czy dysk niezależny nie jest umieszczony w programie VMware.</li> <li>Zatrzymaj ochronę odpowiednich maszyn wirtualnych i ponownie Włącz ochronę za pomocą przycisku **Odśwież** . </li><li>Uruchom DW dla maszyn wirtualnych, których to dotyczy.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynatora agenta programu DPM na serwerze

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Wypychanie agentów do serwerów chronionych | Operacja agenta nie powiodła się z powodu błędu komunikacji z usługą koordynator agenta programu DPM w systemie \<ServerName> . | **Jeśli zalecana akcja wyświetlana w produkcie nie działa, wykonaj następujące czynności**: <ul><li> W przypadku dołączania komputera z niezaufanej domeny wykonaj [następujące kroki](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains). <br> ORAZ </li><li> Jeśli komputer jest dołączany do zaufanej domeny, należy wykonać czynności opisane w [tym blogu](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>ORAZ</li><li> Spróbuj wyłączyć program antywirusowy jako krok rozwiązywania problemów. Jeśli problem nie zostanie rozwiązany, zmodyfikuj ustawienia programu antywirusowego zgodnie z opisem w [tym artykule](/system-center/dpm/run-antivirus-server).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Instalator nie może zaktualizować metadanych rejestru

| Operacja | Szczegóły błędu | Obejście |
|-----------|---------------|------------|
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do nadużycia magazynu. Aby tego uniknąć, zaktualizuj wpis rejestru przycinania plików ReFS. | Dostosuj Klucz rejestru **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ustaw wartość DWORD na 1. |
|Instalacja | Instalator nie może zaktualizować metadanych rejestru. Ten błąd aktualizacji może prowadzić do nadużycia magazynu. Aby tego uniknąć, zaktualizuj wpis rejestru SnapOptimization woluminu. | Utwórz klucz rejestru **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** z pustą wartością ciągu. |

## <a name="registration-and-agent-related-issues"></a>Rejestracja i problemy związane z agentem

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Wypychanie agentów do serwerów chronionych | Poświadczenia określone dla serwera są nieprawidłowe. | **Jeśli zalecana akcja wyświetlana w produkcie nie działa, wykonaj następujące czynności**: <br> Spróbuj zainstalować agenta ochrony ręcznie na serwerze produkcyjnym zgodnie z opisem w [tym artykule](/system-center/dpm/deploy-dpm-protection-agent).|
| Agent Azure Backup nie mógł nawiązać połączenia z usługą Azure Backup (Identyfikator: 100050) | Agent Azure Backup nie mógł nawiązać połączenia z usługą Azure Backup. | **Jeśli zalecana akcja wyświetlana w produkcie nie działa, wykonaj następujące czynności**: <br>1. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień: **PsExec-i-s "C:\Program Files\Internet Explorer\iexplore.exe**. Spowoduje to otwarcie okna programu Internet Explorer. <br/> 2. Przejdź do pozycji **Narzędzia**  >  **Opcje internetowe**  >  **połączenia**  >  **sieci LAN**. <br/> 3. Zmień ustawienia tak, aby korzystały z serwera proxy. Następnie podaj szczegóły serwera proxy.<br/> 4. Jeśli maszyna ma ograniczony dostęp do Internetu, upewnij się, że ustawienia zapory na komputerze lub serwerze proxy zezwalają na te [adresy URL](install-mars-agent.md#verify-internet-access) i [adres IP](install-mars-agent.md#verify-internet-access).|
| Niepowodzenie instalacji agenta Azure Backup | Instalacja Microsoft Azure Recovery Services nie powiodła się. Wszystkie zmiany wprowadzone w systemie przez instalację Microsoft Azure Recovery Services zostały wycofane. (IDENTYFIKATOR: 4024) | Ręcznie Zainstaluj agenta platformy Azure.

## <a name="configuring-protection-group"></a>Konfigurowanie grupy ochrony

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie grup ochrony | Program DPM nie mógł wyliczyć składnika aplikacji na chronionym komputerze (nazwa komputera chronionego). | Wybierz pozycję **Odśwież** na ekranie Konfigurowanie interfejsu użytkownika grupy ochrony na odpowiednim poziomie źródła danych/składnika. |
| Konfigurowanie grup ochrony | Nie można skonfigurować ochrony | Jeśli chroniony serwer jest serwerem SQL, upewnij się, że uprawnienia roli sysadmin zostały przekazane do konta systemowego (systemowe NTAUTHORITY\SYSTEM) na komputerze chronionym, zgodnie z opisem w [tym artykule](/system-center/dpm/back-up-sql-server).
| Konfigurowanie grup ochrony | Brak wystarczającej ilości wolnego miejsca w puli magazynów dla tej grupy ochrony. | Dyski dodane do puli magazynów [nie powinny zawierać partycji](/system-center/dpm/create-dpm-protection-groups). Usuń wszystkie woluminy istniejące na dyskach. Następnie dodaj je do puli magazynów.|
| Zmiana zasad |Nie można zmodyfikować zasad tworzenia kopii zapasowych. Błąd: bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x29834]. Spróbuj ponownie wykonać operację po upływie pewnego czasu. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną firmy Microsoft. | **Przyczyna:**<br/>Ten błąd występuje w trzech warunkach: gdy ustawienia zabezpieczeń są włączone, przy próbie zmniejszenia zakresu przechowywania poniżej wartości minimalnej określonych wcześniej, a jeśli korzystasz z nieobsługiwanej wersji. (Wersje nieobsługiwane są takie same jak Microsoft Azure Backup Server w wersji: 2.0.9052 i Azure Backup Server Update 1). <br/>**Zalecana akcja:**<br/> Aby kontynuować aktualizacje związane z zasadami, należy ustawić okres przechowywania powyżej minimalnego określonego okresu przechowywania. (Minimalny okres przechowywania wynosi siedem dni dziennie, cztery tygodnie przez tydzień, trzy tygodnie dla co miesiąc lub jeden rok przez rok). <br><br>Opcjonalnie należy zaktualizować agenta kopii zapasowej i Azure Backup Server, aby wykorzystać wszystkie aktualizacje zabezpieczeń. |

## <a name="backup"></a>Backup

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Backup | Wystąpił nieoczekiwany błąd podczas wykonywania zadania. Urządzenie nie jest gotowe. | **Jeśli zalecana akcja wyświetlana w produkcie nie działa, wykonaj następujące czynności:** <br> <ul><li>Ustaw w obszarze magazyn kopii w tle opcję nieograniczone na elementy w grupie ochrony, a następnie Uruchom sprawdzanie spójności.<br></li> ORAZ <li>Spróbuj usunąć istniejącą grupę ochrony i utworzyć wiele nowych grup. Każda nowa grupa ochrony powinna mieć poszczególne elementy.</li></ul> |
| Backup | Jeśli wykonujesz kopię zapasową tylko stanu systemu, sprawdź, czy na chronionym komputerze jest wystarczająca ilość wolnego miejsca do przechowywania kopii zapasowej stanu systemu. | <ol><li>Sprawdź, czy na chronionej maszynie jest zainstalowany Kopia zapasowa systemu Windows Server.</li><li>Sprawdź, czy na chronionym komputerze jest wystarczająca ilość miejsca na potrzeby stanu systemu. Najprostszym sposobem sprawdzenia, czy jest przejście do komputera chronionego, otwarcie Kopia zapasowa systemu Windows Server, kliknięcie opcji, a następnie wybranie opcji BMR. Interfejs użytkownika wskazuje, ile miejsca jest wymagane. Otwórz Harmonogram kopii zapasowych lokalnych kopii zapasowych **WSB**  >  **Local backup**  >  **Backup schedule**  >  **Wybierz kolejno pozycje Konfiguracja kopii zapasowej**  >  **pełny serwer** (rozmiar jest wyświetlany). Użyj tego rozmiaru do weryfikacji.</li></ol>
| Backup | Niepowodzenie tworzenia kopii zapasowej dla BMR | Jeśli rozmiar BMR jest duży, przenieś niektóre pliki aplikacji na dysk systemu operacyjnego i spróbuj ponownie. |
| Backup | Opcja ponownego włączania ochrony maszyny wirtualnej VMware na nowym serwerze Microsoft Azure Backup nie jest wyświetlana jako dostępna do dodania. | Właściwości VMware są wskazywane w starym, wycofanym wystąpieniu Microsoft Azure Backup Server. Aby rozwiązać ten problem:<br><ol><li>W programie VCenter (odpowiednik w programie SC-VMM) przejdź do karty **Podsumowanie** , a następnie do pozycji **atrybuty niestandardowe**.</li>  <li>Usuń starą nazwę serwera Microsoft Azure Backup z wartości **DPMServer** .</li>  <li>Wróć do nowego serwera Microsoft Azure Backup i zmodyfikuj PG.  Po wybraniu przycisku **Odśwież** , maszyna wirtualna zostanie wyświetlona z polem wyboru, które jest dostępne do dodania do ochrony.</li></ol> |
| Backup | Wystąpił błąd podczas uzyskiwania dostępu do plików/folderów udostępnionych | Spróbuj zmodyfikować ustawienia oprogramowania antywirusowego zgodnie z opisem w tym artykule [Uruchom oprogramowanie antywirusowe na serwerze DPM](/system-center/dpm/run-antivirus-server).|

## <a name="change-passphrase"></a>Zmień hasło

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Zmień hasło |Wprowadzony numer PIN zabezpieczeń jest nieprawidłowy. Podaj poprawny zabezpieczający numer PIN, aby ukończyć tę operację. |**Przyczyna:**<br/> Ten błąd występuje po wprowadzeniu nieprawidłowego lub wygasłego numeru PIN zabezpieczeń podczas wykonywania operacji krytycznej (na przykład zmiany hasła). <br/>**Zalecana akcja:**<br/> Aby ukończyć tę operację, musisz wprowadzić prawidłowy zabezpieczający kod PIN. Aby uzyskać numer PIN, zaloguj się do Azure Portal i przejdź do magazynu Recovery Services. Następnie przejdź do pozycji **Ustawienia**  >  **Właściwości**  >  **Generuj zabezpieczający numer PIN**. Użyj tego numeru PIN, aby zmienić hasło. |
| Zmień hasło |Operacja nie powiodła się. IDENTYFIKATOR: 120002 |**Przyczyna:**<br/>Ten błąd występuje, gdy ustawienia zabezpieczeń są włączone, lub podczas próby zmiany hasła podczas korzystania z nieobsługiwanej wersji.<br/>**Zalecana akcja:**<br/> Aby zmienić hasło, należy najpierw zaktualizować agenta kopii zapasowej do wersji minimalnej, która jest: 2.0.9052. Należy również zaktualizować Azure Backup Server do minimum Update 1, a następnie wprowadzić prawidłowy zabezpieczający numer PIN. Aby uzyskać numer PIN, zaloguj się do Azure Portal i przejdź do magazynu Recovery Services. Następnie przejdź do pozycji **Ustawienia**  >  **Właściwości**  >  **Generuj zabezpieczający numer PIN**. Użyj tego numeru PIN, aby zmienić hasło. |

## <a name="configure-email-notifications"></a>Konfigurowanie powiadomień e-mail

| Operacja | Szczegóły błędu | Obejście |
| --- | --- | --- |
| Konfigurowanie powiadomień e-mail przy użyciu konta służbowego |Identyfikator błędu: 2013| **Przyczyna:**<br> Próba użycia konta służbowego <br>**Zalecana akcja:**<ol><li> Najpierw należy upewnić się, że na serwerze DPM skonfigurowano opcję "Zezwalaj na anonimowe przekazywanie na łączniku odbierającym". Aby uzyskać więcej informacji na temat sposobu konfigurowania tego elementu, zobacz [Zezwalanie na anonimowe przekazywanie w odniesieniu do łącznika odbioru](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Jeśli nie możesz użyć wewnętrznego przekaźnika SMTP i skonfigurować go przy użyciu serwera pakietu Office 365, możesz skonfigurować usługi IIS jako przekaźnik. Skonfiguruj serwer programu DPM do [przekazywania protokołu SMTP do pakietu Office 365 przy użyciu usług IIS](/exchange/mail-flow/test-smtp-with-telnet).<br><br>  Upewnij się, że używasz \@ formatu Domain.com użytkownika, a *nie* domena \ użytkownik<br><br><li>Wskaż program DPM, aby używał nazwy serwera lokalnego jako serwera SMTP, port 587. Następnie wskaż adres e-mail użytkownika, z którego powinny pochodzić wiadomości e-mail.<li> Nazwa użytkownika i hasło na stronie Instalatora SMTP programu DPM powinny dotyczyć konta domeny w domenie, w której znajduje się program DPM. </li><br> Gdy zmieniasz adres serwera SMTP, wprowadź zmiany w nowych ustawieniach, Zamknij pole ustawienia, a następnie otwórz je ponownie, aby upewnić się, że odzwierciedla nową wartość.  Po prostu zmiana i testowanie może nie zawsze spowodować, że nowe ustawienia zaczęły obowiązywać, dlatego testowanie w ten sposób jest najlepszym rozwiązaniem.<br><br>W dowolnym momencie w trakcie tego procesu można wyczyścić te ustawienia, zamykając konsolę programu DPM i edytując następujące klucze rejestru: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> delete SMTPPassword i SMTPUserName Keys**. Można je dodać z powrotem do interfejsu użytkownika po jego ponownym uruchomieniu.

## <a name="common-issues"></a>Typowe problemy

W tej sekcji opisano typowe błędy, które mogą wystąpić podczas korzystania z Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Komunikat o błędzie | Zalecana akcja |
-- | --
Tworzenie kopii zapasowej nie powiodło się, ponieważ replika kopii zapasowej na dysku jest nieprawidłowa lub nie istnieje. | Aby rozwiązać ten problem, sprawdź poniższe kroki i spróbuj ponownie wykonać operację: <br/> 1. Utwórz punkt odzyskiwania dysku<br/> 2. Uruchom sprawdzanie spójności dla źródła danych <br/> 3. Zatrzymaj ochronę źródła danych, a następnie Zmień konfigurację ochrony tego źródła.

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Komunikat o błędzie | Zalecana akcja |
-- | --
Nie można utworzyć migawki woluminu źródłowego, ponieważ metadane w replice są nieprawidłowe. | Utwórz punkt odzyskiwania dysku tego źródła danych i spróbuj ponownie wykonać kopię zapasową online

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Komunikat o błędzie | Zalecana akcja |
-- | --
Nie można utworzyć migawki woluminu źródłowego z powodu niespójnej repliki źródła danych. | Uruchom sprawdzanie spójności dla tego źródła danych i spróbuj ponownie

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Komunikat o błędzie | Zalecana akcja |
-- | --
Próba utworzenia kopii zapasowej nie powiodła się, ponieważ nie można sklonować repliki kopii zapasowej na dysku.| Upewnij się, że wszystkie wcześniejsze pliki repliki kopii zapasowej (VHDX) są odinstalowane i nie są wykonywane żadne kopie zapasowe dysku w trakcie wykonywania kopii zapasowych online
