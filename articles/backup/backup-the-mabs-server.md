---
title: Tworzenie kopii zapasowej serwera MABS
description: Dowiedz się, jak wrócić do Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: fbd3d1f2d7cb24c21962dbe5c88acebf73652a04
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519123"
---
# <a name="back-up-the-mabs-server"></a>Tworzenie kopii zapasowej serwera MABS

Aby zapewnić możliwość odzyskania danych w przypadku awarii serwera Microsoft Azure Backup Server (MABS), potrzebna będzie strategia kopii zapasowej serwera USŁUGI MABS. Jeśli nie ma kopii zapasowej, należy ponownie skompilować ją ręcznie po awarii, a punktów odzyskiwania opartych na dyskach nie będzie można odzyskać. Kopię zapasową serwerów MABS można utworzyć, kopię zapasową bazy danych mabs.

## <a name="back-up-the-mabs-database"></a>Back up the MABS database (Kopię zapasową bazy danych mabs)

W ramach strategii tworzenia kopii zapasowych usługi MABS należy utworzyć kopię zapasową bazy danych usługi MABS. Baza danych ma nazwę DPMDB. Ta baza danych zawiera konfigurację usługi MABS wraz z danymi o kopiach zapasowych usługi MABS. W przypadku awarii można ponownie skompilować większość funkcji serwera USŁUGI MABS przy użyciu najnowszej kopii zapasowej bazy danych. Przy założeniu, że można przywrócić bazę danych, kopie zapasowe oparte na taśmach są dostępne, a wszystkie ustawienia grup ochrony i harmonogramy kopii zapasowych są zachowywane. Jeśli awarii nie ma wpływu na dyski puli magazynów USŁUGI MABS, kopie zapasowe oparte na dyskach mogą być również używane po odbudowie. Istnieje kilka różnych metod wykonywania kopii zapasowych bazy danych.

|Metoda tworzenia kopii zapasowej bazy danych|Zalety|Wady|
|--------------------------|--------------|-----------------|
|[Tworzenie kopii zapasowej w systemie Azure](#back-up-to-azure)|<li>Łatwe konfigurowanie i monitorowanie w u usługi MABS.<li>Wiele lokalizacji plików kopii zapasowej bazy danych.<li>Magazyn w chmurze zapewnia niezawodne rozwiązanie odzyskiwania po awarii.<li>Bardzo bezpieczny magazyn na bazę danych.<li>Obsługuje 120 punktów odzyskiwania online.|<li>Wymaga konta platformy Azure i dodatkowej konfiguracji usługi MABS. Generuje pewien koszt związany z magazynem systemu Azure.<li> Wymaga obsługiwanej wersji systemu Windows Server z agentem platformy Azure w celu uzyskania dostępu do kopii zapasowych usługi MABS przechowywanych w magazynie usługi Azure Backup. Nie może to być inny serwer USŁUGI MABS.<li>Opcja ta nie jest możliwa, jeśli baza danych jest przechowywana lokalnie i chcesz włączyć dodatkową ochronę. <li>Wymaga pewnego dodatkowego czasu na przygotowanie i odzyskiwanie.|
|[Back up the database by backing the MABS storage pool (Kopię zapasową bazy danych przez kopię zapasową puli magazynów USŁUGI MABS)](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Prosta do konfigurowania i monitorowania.<li>Kopia zapasowa jest przechowywana na dyskach puli magazynów USŁUGI MABS i jest łatwa do uzyskania dostępu lokalnie.<li>Zaplanowane kopie zapasowe usługi MABS obsługują 512 ekspresowych pełnych kopii zapasowych. W przypadku kopii zapasowej co godzinę będziesz mieć pełną ochronę przez 21 dni.|<li>Nie jest to dobra opcja w przypadku odzyskiwania po awarii. Jest w trybie online, a odzyskiwanie może nie działać zgodnie z oczekiwaniami w przypadku awarii serwera usługi MABS lub dysku puli magazynów.<li>Opcja ta nie jest możliwa, jeśli baza danych jest przechowywana lokalnie i chcesz włączyć dodatkową ochronę. <li>Jeśli usługa MABS lub konsola nie jest uruchomiona lub nie działa, wymagane są pewne przygotowania i specjalne kroki w celu uzyskania dostępu do punktów odzyskiwania.|
|[Tworzenie kopii zapasowej za pomocą natywnego wykonywania kopii zapasowych programu SQL Server na dysku lokalnym](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Wbudowana w program SQL Server.<li>Kopia zapasowa jest przechowywana na dysku lokalnym, który jest łatwo dostępny.<li>Można ją uruchamiać zgodnie z harmonogramem, z dowolną częstotliwością.<li>Całkowicie niezależna od mabs.<li>Można zaplanować oczyszczanie pliku kopii zapasowej.|<li>Nie jest dobrą opcją odzyskiwania po awarii, chyba że wykonane kopie zapasowe są kopiowane do lokalizacji zdalnej.<li>Wymaga magazynu lokalnego dla kopii zapasowych, co może ograniczać przechowywanie i częstotliwość.|
|[Tworzenie kopii zapasowej za pomocą natywnej kopii zapasowej SQL i ochrony usługi MABS w udziałach chronionych przez usługę MABS](#back-up-to-a-share-protected-by-mabs)|<li>Łatwe monitorowanie w uchu MABS.<li>Wiele lokalizacji plików kopii zapasowej bazy danych.<li>Łatwo dostępna z dowolnego komputera z systemem Windows w sieci.<li>Potencjalnie najszybsza metoda odzyskiwania.|<li>Obsługuje tylko 64 punkty odzyskiwania.<li>Nie jest to dobra opcja w przypadku odzyskiwania lokalizacji po awarii. Awaria dysku serwera MABS lub puli magazynów MABS może utrudnić odzyskiwanie.<li>Nie jest to opcja, jeśli baza danych MABS jest hostowana lokalnie i chcesz włączyć ochronę pomocniczą. <li>Aby ją skonfigurować i przetestować, konieczne są pewne dodatkowe przygotowania.<li>Jeśli serwer MABS nie działa, ale dyski puli magazynów usługi MABS są w porządku, wymagany jest dodatkowy czas przygotowania i odzyskiwania.|

- W przypadku kopii zapasowej przy użyciu grupy ochrony usługi MABS zaleca się użycie unikatowej grupy ochrony dla bazy danych.

    > [!NOTE]
    > Na potrzeby przywracania instalacja serwera MABS, którą chcesz przywrócić za pomocą bazy danych mabs, musi być dopasowana do wersji samej bazy danych mabs.  Jeśli na przykład baza danych, którą chcesz odzyskać, pochodzi z instalacji pakietu zbiorczego aktualizacji 1 usługi MABS w wersji 3, na serwerze USŁUGI MABS musi być uruchomiona ta sama wersja z pakietu zbiorczym aktualizacji 1. Oznacza to, że przed przywróceniem bazy danych może być konieczne odinstalowanie i ponowne zainstalowanie usługi MABS ze zgodną wersją.  Aby można było wyświetlić wersję bazy danych, może być konieczne ręczne zainstalowanie jej pod tymczasową nazwą, a następnie uruchomienie na tej bazie zapytania SQL sprawdzającego wersję główną i pomocniczą ostatnio zainstalowanego pakietu aktualizacji.

- Aby sprawdzić wersję bazy danych mabs, wykonaj następujące kroki:

    1. Aby uruchomić zapytanie, otwórz program SQL Management Studio, a następnie połącz się z wystąpieniem programu SQL, na których działa baza danych USŁUGI MABS.

    2. Wybierz bazę danych MABS, a następnie uruchom nowe zapytanie.

    3. Wklej następujący kod zapytania SQL w okienku zapytania i uruchom go:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Jeśli żadne dane nie zostaną zwrócone w wynikach zapytania lub jeśli serwer USŁUGI MABS został uaktualniony z poprzednich wersji, ale od tego czasu nie zainstalowano żadnego nowego pakietu zbiorczego aktualizacji, nie będzie wpisu dla wersji podstawowej, pomocniczej dla podstawowej instalacji usługi MABS. Aby sprawdzić wersje usługi MABS skojarzone z zbiorczymi aktualizacjami, zobacz List of Build Numbers for MABS (Lista numerów [kompilacji dla usługi MABS).](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)

### <a name="back-up-to-azure"></a>Tworzenie kopii zapasowej w systemie Azure

1. Przed rozpoczęciem należy uruchomić skrypt w celu pobrania ścieżki punktu instalacji woluminu repliki MABS, aby wiedzieć, który punkt odzyskiwania zawiera kopię zapasową usługi MABS. Zrób to po zakończeniu replikacji początkowej za pomocą usługi Kopia zapasowa Azure. W skrypcie zastąp nazwą wystąpienia SQL Server `dplsqlservername%` hostującym bazę danych USŁUGI MABS.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Upewnij się, że masz kod dostępu określony podczas instalacji agenta usług Azure Recovery Services, a serwer MABS został zarejestrowany w Azure Backup magazynu. Ten kod dostępu jest potrzebny do przywrócenia kopii zapasowej.

2. Utwórz magazyn usługi Kopia zapasowa Azure, a następnie pobierz plik instalacyjny agenta kopii zapasowej platformy Azure i poświadczenia magazynu. Uruchom plik instalacyjny, aby zainstalować agenta na serwerze USŁUGI MABS i użyć poświadczeń magazynu do zarejestrowania serwera USŁUGI MABS w magazynie. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md).

3. Po skonfigurowaniu magazynu skonfiguruj grupę ochrony usługi MABS, która zawiera bazę danych usługi MABS. Wybierz, aby wrócić do kopii zapasowej na dysku i na platformie Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Odzyskiwanie bazy danych MABS z platformy Azure

Bazę danych można odzyskać z platformy Azure przy użyciu dowolnego serwera USŁUGI MABS zarejestrowanego w magazynie Azure Backup w następujący sposób:

1. W konsoli usługi MABS wybierz pozycję **Odzyskiwanie** Dodaj  >  **zewnętrzną usługę MABS.**

2. Podaj poświadczenia magazynu (pobierz z magazynu Azure Backup magazynu). Weź pod uwagę, że poświadczenia są ważne tylko przez dwa dni.

3. Na **stronie Wybierz zewnętrzną** usługę MABS do odzyskiwania wybierz serwer USŁUGI MABS, dla którego chcesz odzyskać bazę danych, wpisz hasło szyfrowania i wybierz przycisk **OK.**

4. Wybierz punkt odzyskiwania, którego chcesz używać, z listy dostępnych punktów. Wybierz **pozycję Wyczyść zewnętrzną usługę MABS,** aby wrócić do lokalnego widoku usługi MABS.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Back up the MABS database to MABS storage pool (Kopię zapasową bazy danych USŁUGI MABS w puli magazynów usługi MABS)

> [!NOTE]  
> Ta opcja ma zastosowanie w przypadku mabs z Nowoczesny magazyn kopii zapasowych.

1. W konsoli usługi MABS wybierz pozycję **Ochrona**  >  **Utwórz grupę ochrony.**
2. Na stronie **Wybierz typ grupy ochrony** zaznacz opcję **Serwery**.
3. Na stronie **Wybieranie członków grupy** wybierz pozycję **DPM database**. Rozwiń serwer MABS i wybierz pozycję DPMDB.
4. Na stronie **Wybierz metodę ochrony danych** wybierz pozycję Chcę uzyskać **krótkoterminową ochronę przy użyciu dysku**. Określ opcje zasady ochrony krótkoterminowej.
5. Po początkowej replikacji bazy danych USŁUGI MABS uruchom następujący skrypt SQL:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>Odzyskiwanie bazy danych mabs

Aby odtworzyć usługę MABS przy użyciu tej samej bazy danych, należy najpierw odzyskać bazę danych MABS i zsynchronizować ją z nowo zainstalowanym programem MABS.

#### <a name="use-the-following-steps"></a>Skorzystaj z poniższych kroków

1. Otwórz administracyjny wiersz polecenia i uruchom polecenie `psexec.exe -s powershell.exe` , aby uruchomić okno programu PowerShell w kontekście systemu.
2. Określ lokalizację, z której chcesz odzyskać bazę danych:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Aby skopiować bazę danych z ostatniej kopii zapasowej

1. Przejdź do ścieżki wirtualnego dysku twardego repliki `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Zainstaluj **dysk0.vhdx obecny** w nim za pomocą `mount-vhd disk0.vhdx` polecenia .
3. Po zamontowaniu repliki wirtualnego dysku twardego użyj polecenia , aby przypisać literę dysku do woluminu repliki przy użyciu identyfikatora repliki fizycznej `mountvol.exe` z danych wyjściowych skryptu SQL. Na przykład: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Aby skopiować bazę danych z poprzedniego punktu odzyskiwania

1. Przejdź do katalogu kontenera  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` DPMDB. Zobaczysz wiele katalogów z pewnymi unikatowymi identyfikatorami GUID pod odpowiednimi punktami odzyskiwania dla bazy danych MABS. Inne katalogi reprezentują punkt PIT/punkt odzyskiwania.
2. Przejdź do dowolnej ścieżki wirtualnego dysku twardego pit, na przykład i `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` zainstaluj **dysk0.vhdx** obecny w nim za pomocą `mount-vhd disk0.vhdx` polecenia .
3. Po zamontowaniu repliki wirtualnego dysku twardego użyj polecenia , aby przypisać literę dysku do woluminu repliki przy użyciu identyfikatora repliki fizycznej z danych `mountvol.exe` wyjściowych skryptu SQL. Na przykład: `mountvol X: \?\Volume{}\`

   Wszystkie terminy, które pojawiają się w nawiasach klamrowych angular w powyższych krokach, są posiadaczami miejsca. Zastąp je odpowiednimi wartościami w następujący sposób:
   - **ReFSVolume** — ścieżka dostępu z danych wyjściowych skryptu SQL
   - **NAZWA FQDN serwera MABSServer** — w pełni kwalifikowana nazwa serwera USŁUGI MABS
   - **PhysicalReplicaId** — identyfikator repliki fizycznej ze skryptu SQL
   - **PITId** — identyfikator GUID inny niż identyfikator repliki fizycznej w katalogu kontenera.
4. Otwórz inny administracyjny wiersz polecenia i uruchom polecenie `psexec.exe -s cmd.exe` , aby uruchomić wiersz polecenia w kontekście systemu.
5. Zmień katalog na dysk X: i przejdź do lokalizacji plików bazy danych usługi MABS.
6. Skopiuj pliki do lokalizacji, z której będzie łatwo je przywrócić. Zamknij okno polecenia psexec po skopiowaniu.
7. Przejdź do okna programu PowerShell psexec otwartego w kroku 1, przejdź do ścieżki VHDX i odinstaluj dysk VHDX za pomocą polecenia `dismount-vhd disk0.vhdx` .
8. Po ponownym zainstalowaniu serwera MABS możesz użyć przywróconej bazy danych DPMDB, aby dołączyć ją do serwera MABS, uruchamiając `DPMSYNC-RESTOREDB` polecenie .
9. Uruchom `DPMSYNC-SYNC` po `DPMSYNC-RESTOREDB` zakończeniu.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Back up the database by backing the MABS storage pool (Kopię zapasową bazy danych przez kopię zapasową puli magazynów USŁUGI MABS)

> [!NOTE]
> Ta opcja ma zastosowanie w przypadku usługi MABS ze starszym magazynem.

Przed rozpoczęciem należy uruchomić skrypt w celu pobrania ścieżki punktu instalacji woluminu repliki MABS, aby wiedzieć, który punkt odzyskiwania zawiera kopię zapasową usługi MABS. Zrób to po zakończeniu replikacji początkowej za pomocą usługi Kopia zapasowa Azure. W skrypcie zastąp nazwą wystąpienia SQL Server `dplsqlservername%` hostującym bazę danych USŁUGI MABS.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. W konsoli usługi MABS wybierz pozycję **Ochrona**  >  **Utwórz grupę ochrony.**

2. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję **Serwery.**

3. Na stronie **Wybieranie członków grupy** wybierz bazę danych MABS. Rozwiń element serwera MABS i wybierz pozycję **DPMDB.**

4. Na stronie  **Wybierz metodę ochrony danych** wybierz pozycję Chcę uzyskać **krótkoterminową ochronę przy użyciu dysku**. Określ opcje zasady ochrony krótkoterminowej. Zalecamy zakres przechowywania dwóch tygodni dla baz danych mabs.

#### <a name="recover-the-database"></a>Odzyskiwanie bazy danych

Jeśli serwer MABS nadal działa i pula magazynów jest nienaruszona (na przykład problemy z usługą MABS lub konsolą), skopiuj bazę danych z woluminu repliki lub kopii w tle w następujący sposób:

1. Zdecyduj, z jakiego czasu chcesz odzyskać bazę danych.

    - Jeśli chcesz skopiować bazę danych z ostatniej kopii zapasowej wykonanej bezpośrednio z woluminu repliki USŁUGI MABS, użyj programu **mountvol.exe,** aby przypisać literę dysku do woluminu repliki przy użyciu identyfikatora GUID z danych wyjściowych skryptu SQL. Na przykład: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Jeśli chcesz skopiować bazę danych z poprzedniego punktu odzyskiwania (kopii w tle), musisz wyświetlić listę wszystkich kopii w tle repliki przy użyciu identyfikatora GUID woluminu z danych wyjściowych skryptu SQL. To polecenie wyświetla listę kopii w tle dla tego woluminu: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Zanotuj czas tworzenia i identyfikator kopii w tle, z których chcesz odzyskać dane.

2. Następnie użyj **diskshadow.exe,** aby zainstalować kopię w tle na nieużywanej literze dysku X: przy użyciu identyfikatora kopii w tle, aby skopiować pliki bazy danych.

3. Otwórz administracyjny wiersz polecenia i uruchom polecenie , aby uruchomić wiersz polecenia w kontekście systemu, dzięki czemu masz uprawnienia do przechodzenia do woluminu `psexec.exe -s cmd.exe` repliki (X:) i skopiuj pliki.

4. Przejdź do dysku X: i przejdź do lokalizacji plików bazy danych usługi MABS. Skopiuj pliki do lokalizacji, z której będzie łatwo je przywrócić. Po zakończeniu kopiowania istnieje okno polecenia psexec, a następnie uruchom polecenie **diskshadow.exe** i wyeksponuj wolumin X:.

5. Teraz możesz przywrócić pliki bazy danych przy użyciu programu SQL Management Studio lub uruchamiając program **DPMSYNC \- RESTOREDB.**

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Tworzenie kopii zapasowej za pomocą natywnego wykonywania kopii zapasowych programu SQL Server na dysku lokalnym

Możesz utworzyć kopię zapasową bazy danych usługi MABS na dysku lokalnym przy użyciu natywnej kopii SQL Server zapasowej, niezależnie od usługi MABS.

- Zapoznaj się z [opisem](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) kopii zapasowych programu SQL Server.

- [Dowiedz się więcej](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) o tworzeniu kopii zapasowych programu SQL Server w chmurze.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Back up to a share protected by MABS

Ta opcja tworzenia kopii zapasowej używa natywnego programu SQL do tworzenia kopii zapasowej bazy danych USŁUGI MABS w udziału, chroni udział za pomocą usługi MABS i używa wcześniejszych wersji usługi VSS systemu Windows w celu ułatwienia przywracania.

### <a name="before-you-start"></a>Przed rozpoczęciem

1. Na SQL Server folderu na dysku z wystarczającą ilość wolnego miejsca do przechowywania pojedynczej kopii kopii zapasowej. Na przykład: `C:\MABSBACKUP`.

1. Udostępnij ten folder. Na przykład udostępnij `C:\MABSBACKUP` folder jako *DPMBACKUP.*

1. Skopiuj i wklej poniższe polecenie OSQL do Notatnika i zapisz je w pliku o nazwie `C:\MABSACKUP\bkupdb.cmd` . Upewnij się, że nie ma rozszerzenia txt. Zmodyfikuj SQL_Instance_name i DPMDB_NAME, aby dopasować wystąpienie i nazwę bazy danych DPMDB używane przez serwer USŁUGI MABS.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Za pomocą Notatnika otwórz **ScriptingConfig.xml** znajdujący się `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` w folderze na serwerze USŁUGI MABS.

1. **Zmodyfikuj** ScriptingConfig.xml **i zmień wartość DataSourceName=** na literę dysku zawierającą folder/udział DPMDBBACKUP. Zmień wpis PreBackupScript na pełną ścieżkę i nazwę **pliku bkupdb.cmd** zapisanego w kroku 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Zapisz zmiany w **ScriptingConfig.xml**.

1. Chroń folder C:\MABSBACKUP lub udział przy użyciu usługi MABS i poczekaj na tworzenie `\sqlservername\MABSBACKUP` repliki początkowej. W folderze C:\MABSBACKUP powinien być folder **dpmdb.bak** w wyniku uruchomienia skryptu przed kopią zapasową, który z kolei został skopiowany do repliki usługi MABS.

1. Jeśli nie włączysz odzyskiwania samoobsługowego, musisz wykonać kilka dodatkowych czynności, aby udostępnić folder MABSBACKUP w replice:

    1. W konsoli usługi MABS > **Protection** znajdź źródło danych MABSBACKUP i wybierz je. W sekcji szczegółów wybierz pozycję Kliknij, aby **wyświetlić szczegóły** w linku do ścieżki repliki i skopiować ścieżkę do Notatnika. Usuń ścieżkę źródłową i zachowaj ścieżkę docelową. Ścieżka powinna wyglądać podobnie do następującej: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Udostępnij do tej ścieżki przy użyciu nazwy udziału **MABSSERVERNAME-DPMDB.** Możesz użyć poniższego polecenia Net Share w wierszu polecenia z uprawnieniami administracyjnymi.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Konfigurowanie kopii zapasowej

Kopię zapasową bazy danych USŁUGI MABS można tworzyć tak samo jak każdą inną SQL Server przy użyciu SQL Server kopii zapasowej.

- Zapoznaj się z [opisem](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) kopii zapasowych programu SQL Server.

- [Dowiedz się więcej](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) o tworzeniu kopii zapasowych programu SQL Server w chmurze.

### <a name="recover-the-mabs-database"></a>Odzyskiwanie bazy danych mabs

1. Połącz się z `\\MABSServer\MABSSERVERNAME-dpmdb` udziałem za pomocą Eksploratora z dowolnego komputera z systemem Windows.

2. Kliknij prawym przyciskiem myszy plik **dpmdb.bak,** aby wyświetlić właściwości. Na karcie **Poprzednie wersje** znajdują się wszystkie kopie zapasowe, które można wybrać i skopiować. Ponadto ostatnia kopia zapasowa nadal znajduje się w folderze C:\MABSBACKUP, który jest również łatwo dostępny.

3. Jeśli musisz przenieść dysk puli magazynów MABS dołączony do sieci SAN na inny serwer, aby móc odczytywać dane z woluminu repliki, lub ponownie zainstalować system Windows w celu odczytu lokalnie dołączonych dysków, musisz wcześniej znać ścieżkę punktu instalacji woluminu repliki MABS lub identyfikator GUID woluminu, aby wiedzieć, który wolumin przechowuje kopię zapasową bazy danych. Aby wyodrębnić te informacje w dowolnej chwili po rozpoczęciu ochrony, ale zanim zajdzie potrzeba przywrócenia, możesz użyć poniższego skryptu SQL. Zastąp `%dpmsqlservername%` nazwę nazwą serwera SQL Server hostującym bazę danych.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Jeśli musisz odzyskać dane po przeniesieniu dysków puli magazynów USŁUGI MABS lub ponownego skompilowania serwera USŁUGI MABS:

    1. Masz identyfikator GUID woluminu, więc jeśli wolumin musi być zainstalowany na innym serwerze z systemem Windows lub po odbudowie serwera MABS, użyj polecenia **mountvol.exe,** aby przypisać mu literę dysku przy użyciu identyfikatora GUID woluminu z danych wyjściowych skryptu SQL: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Udostępnij ponownie folder MABSBACKUP na woluminie repliki przy użyciu litery dysku i części ścieżki repliki reprezentującej strukturę folderów.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Połącz się z `\\SERVERNAME\MABSSERVERNAME-dpmdb` udziałem za pomocą Eksploratora z dowolnego komputera z systemem Windows.

    4. Kliknij prawym przyciskiem myszy plik **dpmdb.bak,** aby wyświetlić właściwości. Na karcie **Poprzednie wersje** znajdują się wszystkie kopie zapasowe, które można wybrać i skopiować.

## <a name="using-dpmsync"></a>Korzystanie z programu DPMSync

**DpmSync** to narzędzie wiersza polecenia, które umożliwia synchronizowanie bazy danych MABS ze stanem dysków w puli magazynów i z zainstalowanymi agentami ochrony. Program DpmSync przywraca bazę danych usługi MABS, synchronizuje bazę danych USŁUGI MABS z replikami w puli magazynów, przywraca bazę danych raportów i ponownie lokalizuje brakujące repliki.

### <a name="parameters"></a>Parametry

| Parametr      | Opis    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Przywraca bazę danych USŁUGI MABS z określonej lokalizacji.|
| **- Synchronizacja**                            | Synchronizuje przywrócone bazy danych. Po przywróceniu baz danych należy uruchomić program **DpmSync –Sync.** Po uruchomieniu **funkcji DpmSync –Sync** niektóre repliki mogą być nadal oznaczone jako brakujące. |
| **Lokalizacja -DbLoc**                 | Określa lokalizację kopii zapasowej bazy danych usługi MABS.|
| **-InstanceName (Nazwa wystąpienia)** <br/>*serwer\wystąpienie*     | Wystąpienie, do którego należy przywrócić program DPMDB.|
| **-ReallocateReplica**         | Lokalizuje wszystkie brakujące woluminy repliki bez synchronizacji. |
| **-DataCopied**                      | Wskazuje, że zakończono ładowanie danych do nowo przydzielonych woluminów repliki. Dotyczy to tylko komputerów klienckich. |

**Przykład 1:** Aby przywrócić bazę danych USŁUGI MABS z lokalnego nośnika kopii zapasowej na serwerze USŁUGI MABS, uruchom następujące polecenie:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Po przywróceniu bazy danych MABS, aby zsynchronizować bazy danych, uruchom następujące polecenie:

```cmd
DpmSync -Sync
```

Po przywróceniu i zsynchronizowaniu bazy danych usługi MABS i przed przywróceniem repliki uruchom następujące polecenie, aby ponownie przydzielić miejsce na dysku repliki:

```cmd
DpmSync -ReallocateReplica
```

**Przykład 2:** Aby przywrócić bazę danych usługi MABS ze zdalnej bazy danych, uruchom następujące polecenie na komputerze zdalnym:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Po przywróceniu bazy danych usługi MABS, aby zsynchronizować bazy danych, uruchom następujące polecenie na serwerze USŁUGI MABS:

```cmd
DpmSync -Sync
```

Po przywróceniu i zsynchronizowaniu bazy danych usługi MABS i przed przywróceniem repliki uruchom następujące polecenie na serwerze USŁUGI MABS, aby ponownie przydzielić miejsce na dysku dla repliki:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Następne kroki

- [Macierz obsługi mabs](backup-support-matrix-mabs-dpm.md)
- [CZĘSTO ZADAWANE PYTANIA DOTYCZĄCE USŁUGI MABS](backup-azure-dpm-azure-server-faq.yml)