---
title: Rozwiązywanie problemów z kopiami zapasowymi SQL Server Database
description: Informacje dotyczące rozwiązywania problemów dotyczących tworzenia kopii zapasowych SQL Server baz danych działających na maszynach wirtualnych platformy Azure z Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: d502a4188b4f9f383188804f86abbb9a6d05d146
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429470"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Rozwiązywanie problemów z kopiami zapasowymi SQL Server Database przy użyciu Azure Backup

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z bazami danych SQL Server uruchomionymi na maszynach wirtualnych platformy Azure.

Aby uzyskać więcej informacji na temat procesu i ograniczeń tworzenia kopii zapasowej, zobacz [Informacje o SQL Server kopii zapasowych na maszynach wirtualnych platformy Azure](sql-support-matrix.md#feature-considerations-and-limitations).

## <a name="sql-server-permissions"></a>Uprawnienia SQL Server

Aby skonfigurować ochronę bazy danych SQL Server na maszynie wirtualnej, należy zainstalować rozszerzenie **AzureBackupWindowsWorkload** na tej maszynie wirtualnej. Jeśli zostanie wyświetlony błąd **UserErrorSQLNoSysadminMembership**, oznacza to, że wystąpienie SQL Server nie ma wymaganych uprawnień do tworzenia kopii zapasowych. Aby naprawić ten błąd, wykonaj kroki opisane w sekcji [Ustawianie uprawnień maszyny wirtualnej](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Rozwiązywanie problemów z odnajdywaniem i konfigurowaniem

Po utworzeniu i skonfigurowaniu magazynu Recovery Services odnajdywania baz danych i konfigurowania kopii zapasowej jest procesem dwuetapowym.<br>

![Cel kopii zapasowej — SQL Server na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql.png)

Podczas konfigurowania kopii zapasowej, jeśli maszyna wirtualna SQL i jej wystąpienia nie są widoczne na **maszynach wirtualnych baz danych** i **konfiguruje kopię zapasową** (zapoznaj się z powyższym obrazem), upewnij się, że:

### <a name="step-1-discovery-dbs-in-vms"></a>Krok 1. odnajdywanie baz danych na maszynach wirtualnych

- Jeśli maszyna wirtualna nie znajduje się na liście odnalezionych maszyn wirtualnych i nie zarejestrowano jej do tworzenia kopii zapasowych SQL w innym magazynie, postępuj zgodnie z procedurami [odnajdywania SQL Server kopii zapasowej](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) .

### <a name="step-2-configure-backup"></a>Krok 2. Konfigurowanie kopii zapasowej

- Jeśli magazyn, w którym maszyna wirtualna SQL jest zarejestrowana w tym samym magazynie używanym do ochrony baz danych, wykonaj kroki [konfigurowania kopii zapasowej](./backup-sql-server-database-azure-vms.md#configure-backup) .

Jeśli maszyna wirtualna SQL musi być zarejestrowana w nowym magazynie, należy wyrejestrować ją ze starego magazynu.  Wyrejestrowanie maszyny wirtualnej SQL z magazynu wymaga, aby wszystkie chronione źródła danych zostały zatrzymane, a następnie można usunąć dane kopii zapasowej. Usuwanie kopii zapasowej danych jest operacją niszczącą.  Po przejrzeniu i wykonaniu wszystkich środków zaradczych, aby wyrejestrować maszynę wirtualną SQL, Zarejestruj tę samą maszynę wirtualną w nowym magazynie i spróbuj ponownie wykonać operację tworzenia kopii zapasowej.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowej i odzyskiwaniem  

Czasami przypadkowe błędy mogą wystąpić podczas operacji wykonywania kopii zapasowej i przywracania albo operacje te mogą zostać zablokowane. Może to być spowodowane programami antywirusowymi na maszynie wirtualnej. Najlepszym rozwiązaniem jest zasugerowanie następujących kroków:

1. Wyklucz następujące foldery ze skanowania oprogramowania antywirusowego:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    Zamień na `C:\` literę dysku. 

1. Wyklucz następujące trzy procesy działające w ramach maszyny wirtualnej ze skanowania oprogramowania antywirusowego:

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. SQL zawiera również pewne wskazówki dotyczące pracy z programami antywirusowymi. Zobacz [ten artykuł](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) , aby uzyskać szczegółowe informacje.

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>Błędne wystąpienie w maszynie wirtualnej z wieloma wystąpieniami SQL Server

Możesz przywrócić maszynę wirtualną SQL tylko wtedy, gdy wszystkie wystąpienia SQL działające na maszynie wirtualnej są zgłaszane w dobrej kondycji. Jeśli co najmniej jedno wystąpienie jest błędne, maszyna wirtualna nie będzie wyświetlana jako element docelowy przywracania. Może to być możliwe dlatego, że maszyna wirtualna o wiele wystąpień może nie być widoczna na liście rozwijanej "serwer" podczas operacji przywracania.

Możesz sprawdzić poprawność "gotowość do tworzenia kopii zapasowych" wszystkich wystąpień SQL na maszynie wirtualnej, w obszarze **Konfigurowanie kopii zapasowej**:

![Weryfikuj gotowość do tworzenia kopii zapasowych](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

Jeśli chcesz wyzwolić przywracanie dla wystąpień programu SQL w dobrej kondycji, wykonaj następujące czynności:

1. Zaloguj się do maszyny wirtualnej SQL i przejdź do `C:\Program Files\Azure Workload Backup\bin` .
1. Utwórz plik JSON o nazwie `ExtensionSettingsOverrides.json` (jeśli nie jest jeszcze obecny). Jeśli ten plik jest już obecny na maszynie wirtualnej, Kontynuuj korzystanie z niego.
1. Dodaj następującą zawartość do pliku JSON i Zapisz plik:

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Wyzwól operację ponownego **odnajdywania baz danych** na serwerze, na którym ma wpływ Azure Portal (w tym samym miejscu, w którym można zobaczyć gotowość do tworzenia kopii zapasowych). Maszyna wirtualna zostanie uruchomiona jako element docelowy dla operacji przywracania.

    ![Ponownie odkryj baz danych](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. Po zakończeniu operacji przywracania Usuń wpis *whitelistedInstancesForInquiry* z pliku ExtensionSettingsOverrides.js.

## <a name="error-messages"></a>Komunikaty o błędach

### <a name="backup-type-unsupported"></a>Nieobsługiwany typ kopii zapasowej

| Ważność | Opis | Możliwe przyczyny | Zalecana akcja |
|---|---|---|---|
| Ostrzeżenie | Bieżące ustawienia dla tej bazy danych nie obsługują niektórych typów kopii zapasowych zawartych w skojarzonych zasadach. | <li>Na bazie danych Master można wykonać tylko operację pełnej kopii zapasowej bazy danych. Różnicowa kopia zapasowa i kopia zapasowa dziennika transakcji nie są możliwe. </li> <li>Żadna baza danych w modelu odzyskiwania prostego nie zezwala na tworzenie kopii zapasowych dzienników transakcji.</li> | Zmodyfikuj ustawienia bazy danych, tak aby wszystkie typy kopii zapasowych w ramach zasad były obsługiwane. Lub Zmień bieżące zasady tak, aby zawierały tylko obsługiwane typy kopii zapasowych. W przeciwnym razie nieobsługiwane typy kopii zapasowych zostaną pominięte podczas zaplanowanej kopii zapasowej lub zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem dla kopii zapasowej na żądanie.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Ta baza danych SQL nie obsługuje żądanego typu kopii zapasowej. | Występuje, gdy model odzyskiwania bazy danych nie zezwala na żądany typ kopii zapasowej. Ten błąd może wystąpić w następujących sytuacjach: <br/><ul><li>Baza danych, która korzysta z prostego modelu odzyskiwania, nie zezwala na tworzenie kopii zapasowej dziennika.</li><li>Różnice między kopiami zapasowymi i dziennikami nie są dozwolone dla bazy danych Master.</li></ul>Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [modeli odzyskiwania SQL Server](/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Jeśli nie można utworzyć kopii zapasowej dziennika bazy danych w modelu odzyskiwania prostego, wypróbuj jedną z następujących opcji:<ul><li>Jeśli baza danych działa w trybie odzyskiwania prostego, wyłącz kopie zapasowe dzienników.</li><li>Skorzystaj z [dokumentacji SQL Server](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) , aby zmienić model odzyskiwania bazy danych na pełny lub zbiorczo zarejestrowany. </li><li> Jeśli nie chcesz zmieniać modelu odzyskiwania i masz standardowe zasady tworzenia kopii zapasowych wielu baz danych, których nie można zmienić, zignoruj błąd. Pełne i różnicowe kopie zapasowe będą działały zgodnie z harmonogramem. Kopie zapasowe dziennika zostaną pominięte, co jest oczekiwane w tym przypadku.</li></ul>Jeśli jest to baza danych Master i została skonfigurowana różnicowa lub kopia zapasowa dziennika, wykonaj jedną z następujących czynności:<ul><li>Użyj portalu, aby zmienić harmonogram zasad tworzenia kopii zapasowej bazy danych Master na pełny.</li><li>Jeśli masz standardowe zasady tworzenia kopii zapasowych wielu baz danych, których nie można zmienić, zignoruj błąd. Pełna kopia zapasowa będzie działała zgodnie z harmonogramem. Różnicowe kopie zapasowe lub dzienniki nie będą wykonywane, co jest oczekiwane w tym przypadku.</li></ul> |
| Operacja została anulowana, ponieważ w tej samej bazie danych była już uruchomiona operacja powodująca konflikt. | Zobacz [wpis w blogu dotyczący ograniczeń kopii zapasowych i przywracania](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) , które są uruchamiane współbieżnie.| [Użyj SQL Server Management Studio (SSMS), aby monitorować zadania tworzenia kopii zapasowej](manage-monitor-sql-database-backup.md). Po niepowodzeniu operacji powodującej konflikt należy ponownie uruchomić operację.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych SQL nie istnieje. | Baza danych została usunięta lub zmieniono jej nazwę. | Sprawdź, czy baza danych została przypadkowo usunięta lub czy zmieniono jej nazwę.<br/><br/> Jeśli baza danych została przypadkowo usunięta, aby kontynuować tworzenie kopii zapasowych, Przywróć bazę danych do oryginalnej lokalizacji.<br/><br/> Jeśli baza danych została usunięta i nie są potrzebne przyszłe kopie zapasowe, w magazynie Recovery Services wybierz pozycję **Zatrzymaj tworzenie kopii** zapasowej z **zachowaniem Zachowaj dane kopii zapasowej** lub **Usuń dane kopii zapasowej**. Aby uzyskać więcej informacji, zobacz [Zarządzanie kopiami zapasowymi baz danych SQL Server i monitorowanie ich](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Łańcuch dzienników został przerwany. | Kopia zapasowa bazy danych lub maszyny wirtualnej jest tworzona za pomocą innego rozwiązania do tworzenia kopii zapasowych, która obcina łańcuch dzienników.|<ul><li>Sprawdź, czy jest używane inne rozwiązanie do tworzenia kopii zapasowej lub skrypt. Jeśli tak, Zatrzymaj inne rozwiązanie do tworzenia kopii zapasowych. </li><li>Jeśli kopia zapasowa była kopią zapasową dziennika na żądanie, wyzwól pełną kopię zapasową w celu uruchomienia nowego łańcucha dzienników. W przypadku zaplanowanych kopii zapasowych dzienników nie jest wymagana żadna akcja, ponieważ usługa Azure Backup automatycznie wyzwala pełną kopię zapasową w celu rozwiązania tego problemu.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Azure Backup nie może nawiązać połączenia z wystąpieniem programu SQL Server. | Azure Backup nie może nawiązać połączenia z wystąpieniem SQL Server. | Skorzystaj z dodatkowych szczegółów w menu błąd Azure Portal, aby zawęzić główne przyczyny. Zapoznaj się z artykułem [Rozwiązywanie problemów z usługą SQL Backup](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) , aby naprawić błąd.<br/><ul><li>Jeśli domyślne ustawienia SQL nie zezwalają na połączenia zdalne, należy zmienić ustawienia. Zapoznaj się z następującymi artykułami, aby uzyskać informacje na temat zmiany ustawień:<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Jeśli występują problemy z logowaniem, Skorzystaj z tych linków, aby je naprawić:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Brak pierwszej pełnej kopii zapasowej dla tego źródła danych. | Brak pełnej kopii zapasowej bazy danych. Dzienniki i różnicowe kopie zapasowe są nadrzędne dla pełnej kopii zapasowej, dlatego należy wykonać pełne kopie zapasowe przed zainicjowaniem różnicowej lub kopii zapasowej dziennika. | Wyzwól pełną kopię zapasową na żądanie.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można wykonać kopii zapasowej, ponieważ dziennik transakcji dla źródła danych jest pełny. | Miejsce w dzienniku transakcyjnym bazy danych jest pełne. | Aby rozwiązać ten problem, zapoznaj się z [dokumentacją SQL Server](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Baza danych o tej samej nazwie już istnieje w lokalizacji docelowej | Docelowy docelowy przywracania ma już bazę danych o tej samej nazwie.  | <ul><li>Zmień nazwę docelowej bazy danych.</li><li>Można też użyć opcji Wymuszaj zastępowanie na stronie przywracanie.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Przywracanie nie powiodło się, ponieważ bazy danych nie można było przełączyć w tryb offline. | Gdy wykonujesz przywracanie, docelowa baza danych musi zostać przełączona w tryb offline. Azure Backup nie można przenieść tych danych do trybu offline. | Skorzystaj z dodatkowych szczegółów w menu błąd Azure Portal, aby zawęzić główne przyczyny. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją programu SQL Server](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|Komunikat o błędzie |Możliwe przyczyny  |Zalecana akcja  |
|---------|---------|---------|
|Wystąpił błąd wejścia/wyjścia podczas operacji. Sprawdź, czy występują błędy typowych operacji we/wy na maszynie wirtualnej.   |   Uprawnienia dostępu lub ograniczenia dotyczące miejsca na miejscu docelowym.       |  Sprawdź, czy występują błędy typowych operacji we/wy na maszynie wirtualnej. Upewnij się, że dysk docelowy/udział sieciowy na komputerze: <li> ma uprawnienie do odczytu/zapisu dla konta NT NT\SYSTEM na komputerze. <li> ma wystarczającą ilość miejsca na pomyślne ukończenie operacji.<br> Aby uzyskać więcej informacji, zobacz [Restore as Files](restore-sql-database-azure-vm.md#restore-as-files).
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można znaleźć certyfikatu serwera z odciskiem palca w elemencie docelowym. | Baza danych Master w wystąpieniu docelowym nie ma prawidłowego odcisku palca szyfrowania. | Zaimportuj prawidłowy odcisk palca certyfikatu użyty w wystąpieniu źródłowym do wystąpienia docelowego. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Kopia zapasowa dziennika używana do odzyskiwania zawiera zmiany zarejestrowane zbiorczo. Nie można jej użyć do zatrzymania w dowolnym momencie, zgodnie z wytycznymi dotyczącymi języka SQL. | Gdy baza danych jest w trybie odzyskiwania z rejestrowaniem zbiorczym, dane między transakcją zarejestrowaną zbiorczo a następną transakcją dziennika nie mogą zostać odzyskane. | Wybierz inny punkt w czasie do odzyskania. [Dowiedz się więcej](/sql/relational-databases/backup-restore/recovery-models-sql-server).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Nie można działać zgodnie z preferencjami kopii zapasowych dla zawsze włączonej grupy dostępności SQL, ponieważ niektóre węzły grupy dostępności nie zostały zarejestrowane. | Węzły wymagane do wykonywania kopii zapasowych nie są zarejestrowane lub są nieosiągalne. | <ul><li>Upewnij się, że wszystkie węzły wymagane do wykonywania kopii zapasowych tej bazy danych są zarejestrowane w dobrej kondycji, a następnie spróbuj ponownie wykonać operację.</li><li>Zmień preferencję tworzenia kopii zapasowej SQL Server dla grupy dostępności zawsze włączone.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Maszyna wirtualna programu SQL Server jest zamykana i nie jest dostępna dla usługi Azure Backup. | Maszyna wirtualna jest wyłączona. | Upewnij się, że wystąpienie SQL Server jest uruchomione. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Usługa Azure Backup używa agenta gościa maszyny wirtualnej platformy Azure do utworzenia kopii zapasowej, ale agent gościa nie jest dostępny na serwerze docelowym. | Agent gościa nie jest włączony lub jest w złej kondycji. | Ręcznie [Zainstaluj agenta gościa maszyny wirtualnej](../virtual-machines/extensions/agent-windows.md) . |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
| Zamiar ochrony autoprotection został usunięty lub nie jest już prawidłowy. | Po włączeniu autoochrony w wystąpieniu SQL Server **Skonfiguruj zadania tworzenia kopii zapasowej** uruchomione dla wszystkich baz danych w tym wystąpieniu. Jeśli wyłączysz funkcję autoochrony podczas działania zadań, zadania **w toku** są anulowane przy użyciu tego kodu błędu. | Ponownie Włącz ochronę autoochrony, aby chronić wszystkie pozostałe bazy danych. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Operacja została zablokowana, ponieważ osiągnięto limit liczby operacji dozwolonych w ciągu 24 godzin. | Po osiągnięciu maksymalnego dopuszczalnego limitu operacji w okresie 24-godzinnym ten błąd pojawia się. <br> Na przykład: Jeśli osiągnięto limit liczby zadań konfigurowania kopii zapasowych, które mogą zostać wyzwolone dziennie, a użytkownik spróbuje skonfigurować kopię zapasową dla nowego elementu, zobaczysz ten błąd. | Zazwyczaj ponowna próba wykonania operacji po 24 godzinach rozwiązuje ten problem. Jeśli jednak problem będzie się powtarzać, możesz skontaktować się z pomocą techniczną firmy Microsoft w celu uzyskania pomocy.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Operacja została zablokowana, ponieważ magazyn osiągnął limit maksymalny dla takich operacji dozwolony w okresie 24 godzin. | Po osiągnięciu maksymalnego dopuszczalnego limitu operacji w okresie 24-godzinnym ten błąd pojawia się. Ten błąd występuje zazwyczaj, gdy istnieją operacje na skalę, takie jak modyfikacja zasad lub ochrona automatyczne. W przeciwieństwie do przypadku CloudDosAbsoluteLimitReached, nie ma dużo możliwości rozwiązania tego stanu. W rzeczywistości usługa Azure Backup ponowi próbę wykonania operacji wewnętrznie dla wszystkich elementów, których to dotyczy.<br> Na przykład: Jeśli masz dużą liczbę źródeł danych chronionych przy użyciu zasad i podjęto próbę zmodyfikowania tych zasad, zostanie wyzwolone skonfigurowanie zadań ochrony dla każdego z chronionych elementów i czasami może wystąpić maksymalny limit dozwolony dla takich operacji dziennie.| Usługa Azure Backup automatycznie ponowi próbę wykonania tej operacji po 24 godzinach.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
|---|---|---|
Maszyna wirtualna nie może nawiązać kontaktu z usługą Azure Backup ze względu na problemy z łącznością z Internetem. | Maszyna wirtualna musi mieć łączność wychodzącą z usługą Azure Backup Service, Azure Storage lub Azure Active Directory Services.| — Jeśli używasz sieciowej grupy zabezpieczeń do ograniczania łączności, Użyj znacznika usługi *AzureBackup* , aby zezwalać na dostęp wychodzący do usługi Azure Backup i podobnie dla usług Azure AD (*usługi azureactivedirectory*) i Azure Storage (*Storage*). Wykonaj następujące [kroki](./backup-sql-server-database-azure-vms.md#nsg-tags) , aby udzielić dostępu.<br>-Upewnij się, że usługa DNS rozwiązuje punkty końcowe platformy Azure.<br>-Sprawdź, czy maszyna wirtualna znajduje się za modułem równoważenia obciążenia blokującym dostęp do Internetu. Przypisując publiczny adres IP do maszyn wirtualnych, odnajdywanie będzie działało.<br>-Sprawdź, czy nie istnieje Zapora/program antywirusowy/serwer proxy, które blokują wywołania powyższych trzech usług docelowych.

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Przed wyzwoleniem operacji ponownego rejestrowania Sprawdź co najmniej jeden z następujących objawów:

- Wszystkie operacje (takie jak tworzenie kopii zapasowej, przywracanie i konfigurowanie kopii zapasowej) kończą się niepowodzeniem na maszynie wirtualnej z jednym z następujących kodów błędów: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
- Jeśli obszar **stanu kopii** zapasowej dla elementu kopii zapasowej jest wyświetlany jako **nieosiągalny**, należy wykluczyć wszystkie inne przyczyny, które mogą spowodować wystąpienie tego samego stanu:

  - Brak uprawnień do wykonywania operacji związanych z kopiami zapasowymi na maszynie wirtualnej.
  - Zamykanie maszyny wirtualnej, dlatego nie można wykonać kopii zapasowych.
  - [Problemy z siecią](#usererrorvminternetconnectivityissue)

   ![Ponowne rejestrowanie maszyny wirtualnej](./media/backup-azure-sql-database/re-register-vm.png)

- W przypadku zawsze włączonych grup dostępności kopie zapasowe kończą się niepowodzeniem po zmianie preferencji tworzenia kopii zapasowej lub po przejściu w tryb failover.

Te objawy mogą wystąpić z następujących powodów:

- Rozszerzenie zostało usunięte lub odinstalowane z portalu.
- Rozszerzenie zostało odinstalowane z **Panelu sterowania** na maszynie wirtualnej w celu **odinstalowania lub zmiany programu**.
- Maszyna wirtualna została przywrócona z powrotem w czasie za pomocą przywracania z dysku w miejscu.
- Maszyna wirtualna została zamknięta przez dłuższy czas, więc konfiguracja rozszerzenia wygasła.
- Maszyna wirtualna została usunięta, a inna maszyna wirtualna została utworzona o tej samej nazwie i w tej samej grupie zasobów co usunięta maszyna wirtualna.
- Jeden z węzłów grupy dostępności nie otrzymał pełnej konfiguracji kopii zapasowej. Taka sytuacja może wystąpić, gdy grupa dostępności zostanie zarejestrowana w magazynie lub dodany nowy węzeł.

W powyższych scenariuszach zalecamy wyzwolenie operacji ponownego zarejestrowania na maszynie wirtualnej. Zobacz [tutaj](./backup-azure-sql-automation.md#enable-backup) , aby uzyskać instrukcje dotyczące wykonywania tego zadania w programie PowerShell.

## <a name="size-limit-for-files"></a>Limit rozmiaru plików

Łączny rozmiar ciągu plików zależy nie tylko od liczby plików, ale również na ich nazwach i ścieżkach. Dla każdego pliku bazy danych Pobierz nazwę pliku logicznego i ścieżkę fizyczną. Możesz użyć tej kwerendy SQL:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Teraz Rozmieść je w następującym formacie:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Oto przykład:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Jeśli rozmiar ciągu zawartości przekracza 20 000 bajtów, pliki bazy danych są przechowywane inaczej. Podczas odzyskiwania nie będzie można ustawić docelowej ścieżki pliku dla przywracania. Pliki zostaną przywrócone do domyślnej ścieżki SQL dostarczonej przez SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Zastąp domyślną ścieżkę pliku docelowego przywracania

Docelową ścieżkę pliku przywracania można zastąpić podczas operacji przywracania, umieszczając plik JSON zawierający mapowanie pliku bazy danych do docelowej ścieżki przywracania. Utwórz `database_name.json` plik i umieść go w lokalizacji `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` .

Zawartość pliku powinna mieć następujący format:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Oto przykład:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

W poprzedniej zawartości można pobrać logiczną nazwę pliku bazy danych za pomocą następującego zapytania SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

Ten plik powinien zostać umieszczony przed wywołaniem operacji przywracania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Backup dla SQL Server maszyn wirtualnych (publiczna wersja zapoznawcza), zobacz [Azure Backup dla maszyn wirtualnych SQL](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup).
