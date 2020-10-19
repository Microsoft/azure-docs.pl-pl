---
title: Tworzenie kopii zapasowej serwera MABS
description: Dowiedz się, jak utworzyć kopię zapasową serwera Microsoft Azure Backup (serwera usługi MAB).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172118"
---
# <a name="back-up-the-mabs-server"></a>Tworzenie kopii zapasowej serwera MABS

Aby zapewnić, że dane mogą zostać odzyskane w przypadku awarii serwera Microsoft Azure Backup (serwera usługi MAB), potrzebna będzie strategia tworzenia kopii zapasowej serwera serwera usługi MAB. Jeśli kopia zapasowa nie zostanie utworzona, należy ponownie skompilować ją ręcznie po awarii, a punkty odzyskiwania oparte na dyskach nie będą odzyskiwalne. Można utworzyć kopię zapasową serwerów serwera usługi MAB, wykonując kopie zapasowe bazy danych serwera usługi MAB.

## <a name="back-up-the-mabs-database"></a>Tworzenie kopii zapasowej bazy danych serwera usługi MAB

W ramach strategii tworzenia kopii zapasowych serwera usługi MAB należy wykonać kopię zapasową bazy danych serwera usługi MAB. Baza danych serwera usługi MAB ma nazwę DPMDB. Ta baza danych zawiera konfigurację serwera usługi MAB wraz z danymi dotyczącymi kopii zapasowych serwera usługi MAB. Jeśli wystąpi awaria, można ponownie skompilować większość funkcji serwera serwera usługi MAB, używając najnowszej kopii zapasowej bazy danych. Przy założeniu, że można przywrócić bazę danych, kopie zapasowe oparte na taśmach są dostępne, a wszystkie ustawienia grupy ochrony i harmonogramy kopii zapasowych są zachowywane. Jeśli awaria nie wpłynęła na dyski puli magazynu serwera usługi MAB, kopie zapasowe oparte na dyskach są również użyteczne po odbudowie. Istnieje kilka różnych metod wykonywania kopii zapasowych bazy danych.

|Metoda tworzenia kopii zapasowej bazy danych|Zalety|Wady|
|--------------------------|--------------|-----------------|
|[Tworzenie kopii zapasowej w systemie Azure](#back-up-to-azure)|<li>Łatwa konfiguracja i monitorowanie w serwera usługi MAB.<li>Wiele lokalizacji plików kopii zapasowej bazy danych.<li>Magazyn w chmurze zapewnia niezawodne rozwiązanie odzyskiwania po awarii.<li>Bardzo bezpieczny magazyn na bazę danych.<li>Obsługuje 120 punktów odzyskiwania online.|<li>Wymaga konta platformy Azure i dodatkowej konfiguracji serwera usługi MAB. Generuje pewien koszt związany z magazynem systemu Azure.<li> Wymaga obsługiwanej wersji systemu Windows Server z agentem platformy Azure, aby uzyskać dostęp do kopii zapasowych serwera usługi MAB przechowywanych w magazynie usługi Azure Backup. Nie może to być inny serwer serwera usługi MAB.<li>Opcja ta nie jest możliwa, jeśli baza danych jest przechowywana lokalnie i chcesz włączyć dodatkową ochronę. <li>Wymaga pewnego dodatkowego czasu na przygotowanie i odzyskiwanie.|
|[Tworzenie kopii zapasowej bazy danych przez utworzenie kopii zapasowej puli magazynu serwera usługi MAB](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Prosta do konfigurowania i monitorowania.<li>Kopia zapasowa jest przechowywana na dyskach puli magazynu serwera usługi MAB i łatwo uzyskać do niej dostęp lokalnie.<li>SERWERA usługi MAB zaplanowane kopie zapasowe obsługują 512 ekspresowych pełnych kopii zapasowych. W przypadku tworzenia kopii zapasowej co godzinę masz 21 dni pełnej ochrony.|<li>Nie jest to dobra opcja w przypadku odzyskiwania po awarii. Jest w trybie online, a odzyskiwanie może nie działać zgodnie z oczekiwaniami, jeśli serwer serwera usługi MAB lub dysk puli magazynów ulegnie awarii.<li>Opcja ta nie jest możliwa, jeśli baza danych jest przechowywana lokalnie i chcesz włączyć dodatkową ochronę. <li>Niektóre przygotowania i specjalne kroki są wymagane w celu uzyskania dostępu do punktów odzyskiwania, jeśli usługa serwera usługi MAB lub konsola nie działają lub nie działają.|
|[Tworzenie kopii zapasowej za pomocą natywnego wykonywania kopii zapasowych programu SQL Server na dysku lokalnym](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Wbudowana w program SQL Server.<li>Kopia zapasowa jest przechowywana na dysku lokalnym, który jest łatwo dostępny.<li>Można ją uruchamiać zgodnie z harmonogramem, z dowolną częstotliwością.<li>Całkowicie niezależna od serwera usługi MAB.<li>Można zaplanować oczyszczanie pliku kopii zapasowej.|<li>Nie jest dobrą opcją odzyskiwania po awarii, chyba że wykonane kopie zapasowe są kopiowane do lokalizacji zdalnej.<li>Wymaga lokalnego magazynu dla kopii zapasowych, co może ograniczyć czas przechowywania i częstotliwość.|
|[Tworzenie kopii zapasowej za pomocą natywnej kopii zapasowej SQL i serwera usługi MAB ochronę w udziale chronionym przez serwera usługi MAB](#back-up-to-a-share-protected-by-mabs)|<li>Łatwe monitorowanie w serwera usługi MAB.<li>Wiele lokalizacji plików kopii zapasowej bazy danych.<li>Łatwo dostępna z dowolnego komputera z systemem Windows w sieci.<li>Potencjalnie najszybsza metoda odzyskiwania.|<li>Obsługuje tylko 64 punkty odzyskiwania.<li>Nie jest to dobra opcja w przypadku odzyskiwania lokalizacji po awarii. Awaria dysku puli magazynów serwera usługi MAB Server lub serwera usługi MAB może utrudnić odzyskiwanie.<li>Opcja nie jest dostępna, jeśli baza danych serwera usługi MAB jest hostowana lokalnie i chcesz włączyć dodatkową ochronę. <li>Aby ją skonfigurować i przetestować, konieczne są pewne dodatkowe przygotowania.<li>Konieczny jest jakiś dodatkowy czas przygotowania i odzyskiwania w przypadku, gdy serwer serwera usługi MAB sam nie działa, ale serwera usługi MAB dyski puli magazynów są bardzo precyzyjne.|

- W przypadku tworzenia kopii zapasowej przy użyciu grupy ochrony serwera usługi MAB zalecamy użycie unikatowej grupy ochrony dla bazy danych.

    > [!NOTE]
    > W celu przywrócenia instalacji serwera usługi MAB, która ma zostać przywrócona z bazą danych serwera usługi MAB, musi odpowiadać wersji samej bazy danych serwera usługi MAB.  Na przykład jeśli baza danych, którą chcesz odzyskać, pochodzi z programu serwera usługi MAB v3 z instalacją Update Rollup 1, na serwerze serwera usługi MAB musi być uruchomiona ta sama wersja z pakietem zbiorczym aktualizacji 1. Oznacza to, że przed przywróceniem bazy danych może być konieczne odinstalowanie i ponowne zainstalowanie serwera usługi MAB z zgodną wersją.  Aby można było wyświetlić wersję bazy danych, może być konieczne ręczne zainstalowanie jej pod tymczasową nazwą, a następnie uruchomienie na tej bazie zapytania SQL sprawdzającego wersję główną i pomocniczą ostatnio zainstalowanego pakietu aktualizacji.

- Aby sprawdzić wersję bazy danych serwera usługi MAB, wykonaj następujące kroki:

    1. Aby uruchomić zapytanie, Otwórz program SQL Management Studio, a następnie połącz się z wystąpieniem programu SQL, w którym działa baza danych serwera usługi MAB.

    2. Wybierz bazę danych serwera usługi MAB, a następnie rozpocznij nowe zapytanie.

    3. Wklej następujący kod zapytania SQL w okienku zapytania i uruchom go:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Jeśli nic nie zostanie zwrócone w wynikach zapytania lub serwer serwera usługi MAB został uaktualniony z poprzednich wersji, ale żaden nowy pakiet zbiorczy aktualizacji nie został zainstalowany od tego momentu, nie istnieje wpis dla głównej, pomocniczej instalacji podstawowej programu serwera usługi MAB. Aby sprawdzić wersje serwera usługi MAB skojarzone z pakietami zbiorczymi aktualizacji, zobacz [listę numerów kompilacji dla serwera usługi MAB](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Tworzenie kopii zapasowej w systemie Azure

1. Przed rozpoczęciem należy uruchomić skrypt w celu pobrania ścieżki punktu instalacji woluminu repliki serwera usługi MAB, aby wiedzieć, który punkt odzyskiwania zawiera kopię zapasową serwera usługi MAB. Zrób to po zakończeniu replikacji początkowej za pomocą usługi Kopia zapasowa Azure. W skrypcie Zastąp `dplsqlservername%` nazwą wystąpienia SQL Server hostujący bazę danych serwera usługi MAB.

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

    Upewnij się, że masz kod dostępu, który został określony podczas instalacji agenta Recovery Services platformy Azure, a serwer serwera usługi MAB został zarejestrowany w magazynie Azure Backup. Ten kod dostępu jest potrzebny do przywrócenia kopii zapasowej.

2. Utwórz magazyn usługi Kopia zapasowa Azure, a następnie pobierz plik instalacyjny agenta kopii zapasowej platformy Azure i poświadczenia magazynu. Uruchom plik instalacyjny, aby zainstalować agenta na serwerze serwera usługi MAB i użyć poświadczeń magazynu, aby zarejestrować serwer serwera usługi MAB w magazynie. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md).

3. Po skonfigurowaniu magazynu Skonfiguruj grupę ochrony serwera usługi MAB, która zawiera bazę danych serwera usługi MAB. Wybierz, aby utworzyć kopię zapasową na dysku i na platformie Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Odzyskiwanie bazy danych serwera usługi MAB z platformy Azure

Bazę danych można odzyskać z platformy Azure przy użyciu dowolnego serwera serwera usługi MAB, który jest zarejestrowany w magazynie Azure Backup w następujący sposób:

1. W konsoli serwera usługi MAB wybierz pozycję **odzyskiwanie**  >  **Dodawanie zewnętrznego serwera usługi MAB**.

2. Podaj poświadczenia magazynu (Pobierz z magazynu Azure Backup). Weź pod uwagę, że poświadczenia są ważne tylko przez dwa dni.

3. W obszarze **Wybierz zewnętrzne serwera usługi MAB na potrzeby odzyskiwania**wybierz serwer serwera usługi MAB, dla którego chcesz odzyskać bazę danych, wpisz hasło szyfrowania, a następnie wybierz **przycisk OK.**

4. Wybierz punkt odzyskiwania, którego chcesz używać, z listy dostępnych punktów. Wybierz pozycję **Wyczyść zewnętrzne serwera usługi MAB** , aby powrócić do lokalnego widoku serwera usługi MAB.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Tworzenie kopii zapasowej bazy danych serwera usługi MAB w puli magazynu serwera usługi MAB

> [!NOTE]  
> Ta opcja ma zastosowanie do serwera usługi MAB z Nowoczesny magazyn kopii zapasowych.

1. W konsoli serwera usługi MAB wybierz pozycję **Ochrona**  >  **Utwórz grupę ochrony**.
2. Na stronie **Wybierz typ grupy ochrony** zaznacz opcję **Serwery**.
3. Na stronie **Wybierz członków grupy** wybierz pozycję **DPM Database**. Rozwiń serwer serwera usługi MAB i wybierz pozycję DPMDB.
4. Na stronie **Wybierz metodę ochrony danych** wybierz opcję **Chcę uzyskać krótkoterminową ochronę za pomocą dysku**. Określ opcje zasady ochrony krótkoterminowej.
5. Po początkowej replikacji bazy danych serwera usługi MAB uruchom następujący skrypt SQL:

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

### <a name="recover-mabs-database"></a>Odzyskaj bazę danych serwera usługi MAB

Aby odtworzyć serwera usługi MAB z tą samą bazą danych, należy najpierw odzyskać bazę danych serwera usługi MAB i zsynchronizować ją z świeżo zainstalowaną serwera usługi MAB.

#### <a name="use-the-following-steps"></a>Wykonaj następujące kroki

1. Otwórz wiersz polecenia z uprawnieniami administracyjnymi i uruchom polecenie, `psexec.exe -s powershell.exe` Aby uruchomić okno programu PowerShell w kontekście systemu.
2. Wybierz lokalizację, w której chcesz odzyskać bazę danych:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Aby skopiować bazę danych z ostatniej kopii zapasowej

1. Przejdź do ścieżki wirtualnego dysku twardego `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Zainstaluj plik **disk0. vhdx** znajdujący się w nim przy użyciu `mount-vhd disk0.vhdx` polecenia.
3. Po zainstalowaniu wirtualnego dysku twardego repliki Użyj polecenia `mountvol.exe` , aby przypisać literę dysku do woluminu repliki przy użyciu identyfikatora repliki fizycznej z danych wyjściowych skryptu SQL. Na przykład: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Aby skopiować bazę danych z poprzedniego punktu odzyskiwania

1. Przejdź do katalogu kontenera DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . Zobaczysz wiele katalogów z unikatowymi identyfikatorami GUID w ramach odpowiednich punktów odzyskiwania wykonanych dla bazy danych serwera usługi MAB DB. Inne katalogi reprezentują punkt PIT/odzyskiwanie.
2. Przejdź do dowolnej ścieżki wirtualnego dysku twardego, na przykład, `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` i Zainstaluj plik **disk0. vhdx** znajdujący się w nim przy użyciu `mount-vhd disk0.vhdx` polecenia.
3. Po zainstalowaniu wirtualnego dysku twardego repliki Użyj polecenia `mountvol.exe` , aby przypisać literę dysku do woluminu repliki przy użyciu identyfikatora repliki fizycznej z danych wyjściowych skryptu SQL. Na przykład: `mountvol X: \?\Volume{}\`

   Wszystkie warunki, które pojawiają się w nawiasach kątowych w powyższych krokach, są umieszczane jako posiadacze. Zastąp je odpowiednimi wartościami w następujący sposób:
   - **ReFSVolume** — ścieżka dostępu do danych wyjściowych skryptu SQL
   - **MABSSERVER FQDN** — w pełni kwalifikowana nazwa serwera serwera usługi MAB
   - **PhysicalReplicaId** — identyfikator repliki fizycznej ze skryptu SQL out
   - **PITId** — identyfikator GUID inny niż identyfikator repliki fizycznej w katalogu kontenerów.
4. Otwórz inny wiersz polecenia z uprawnieniami administracyjnymi i uruchom `psexec.exe -s cmd.exe` polecenie, aby uruchomić wiersz polecenia w kontekście systemu.
5. Zmień katalog na dysk X: i przejdź do lokalizacji plików bazy danych serwera usługi MAB.
6. Skopiuj pliki do lokalizacji, z której będzie łatwo je przywrócić. Zamknij okno CMD PsExec po skopiowaniu.
7. Przejdź do okna programu PowerShell PsExec otwartego w kroku 1, przejdź do ścieżki VHDX i Odinstaluj plik VHDX za pomocą polecenia `dismount-vhd disk0.vhdx` .
8. Po ponownym zainstalowaniu serwera serwera usługi MAB można użyć przywróconego DPMDB do dołączenia do serwera serwera usługi MAB, uruchamiając `DPMSYNC-RESTOREDB` polecenie.
9. Uruchomienie `DPMSYNC-SYNC` jednokrotne `DPMSYNC-RESTOREDB` zostało zakończone.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Tworzenie kopii zapasowej bazy danych przez utworzenie kopii zapasowej puli magazynu serwera usługi MAB

> [!NOTE]
> Ta opcja ma zastosowanie do serwera usługi MAB z starszym magazynem.

Przed rozpoczęciem należy uruchomić skrypt w celu pobrania ścieżki punktu instalacji woluminu repliki serwera usługi MAB, aby wiedzieć, który punkt odzyskiwania zawiera kopię zapasową serwera usługi MAB. Zrób to po zakończeniu replikacji początkowej za pomocą usługi Kopia zapasowa Azure. W skrypcie Zastąp `dplsqlservername%` nazwą wystąpienia SQL Server hostujący bazę danych serwera usługi MAB.

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

1. W konsoli serwera usługi MAB wybierz pozycję **Ochrona**  >  **Utwórz grupę ochrony**.

2. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję  **serwery**.

3. Na stronie **Wybierz członków grupy** wybierz bazę danych serwera usługi MAB. Rozwiń element serwer serwera usługi MAB i wybierz pozycję **DPMDB**.

4. Na stronie  **Wybierz metodę ochrony danych** wybierz opcję **Chcę uzyskać krótkoterminową ochronę za pomocą dysku**. Określ opcje zasady ochrony krótkoterminowej. Zalecamy zakres przechowywania równy dwa tygodnie dla baz danych serwera usługi MAB.

#### <a name="recover-the-database"></a>Odzyskiwanie bazy danych

Jeśli serwer serwera usługi MAB nadal działa, a pula magazynów jest nienaruszona (na przykład w przypadku problemów z usługą lub konsolą serwera usługi MAB), skopiuj bazę danych z woluminu repliki lub kopii w tle w następujący sposób:

1. Zdecyduj, z jakiego czasu chcesz odzyskać bazę danych.

    - Jeśli chcesz skopiować bazę danych z ostatniej kopii zapasowej wykonanej bezpośrednio z woluminu repliki serwera usługi MAB, użyj **mountvol.exe** , aby przypisać literę dysku do woluminu repliki przy użyciu identyfikatora GUID z danych wyjściowych skryptu SQL. Na przykład: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Jeśli chcesz skopiować bazę danych z poprzedniego punktu odzyskiwania (kopii w tle), należy wyświetlić listę wszystkich kopii w tle repliki przy użyciu identyfikatora GUID woluminu z danych wyjściowych skryptu SQL. To polecenie wyświetla listę kopii w tle dla tego woluminu: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Zanotuj czas utworzenia i identyfikator kopii w tle, z której chcesz odzyskać.

2. Następnie użyj **diskshadow.exe** , aby zainstalować kopię w tle na nieużywanej literze dysku X: za pomocą identyfikatora kopii w tle, aby można było skopiować pliki bazy danych.

3. Otwórz wiersz polecenia z uprawnieniami administracyjnymi i uruchom `psexec.exe -s cmd.exe` polecenie, aby uruchomić wiersz polecenia w kontekście systemu, więc masz uprawnienia do przejścia do woluminu repliki (X:) i skopiuj pliki.

4. Dysk CD z dyskiem X: i przejdź do lokalizacji plików bazy danych serwera usługi MAB. Skopiuj pliki do lokalizacji, z której będzie łatwo je przywrócić. Po zakończeniu kopiowania istnieje okno PsExec cmd i uruchom **diskshadow.exe** i rozuwidocznić wolumin X:.

5. Teraz można przywrócić pliki bazy danych za pomocą programu SQL Management Studio lub przez uruchomienie polecenia **DPMSYNC \- RESTOREDB**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Tworzenie kopii zapasowej za pomocą natywnego wykonywania kopii zapasowych programu SQL Server na dysku lokalnym

Można utworzyć kopię zapasową bazy danych serwera usługi MAB na dysku lokalnym z natywną kopią zapasową SQL Server, niezależnie od serwera usługi MAB.

- Zapoznaj się z [opisem](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) kopii zapasowych programu SQL Server.

- [Dowiedz się więcej](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) o tworzeniu kopii zapasowych programu SQL Server w chmurze.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Tworzenie kopii zapasowej w udziale chronionym przez serwera usługi MAB

Ta opcja tworzenia kopii zapasowej używa natywnego języka SQL do tworzenia kopii zapasowej bazy danych serwera usługi MAB w udziale, chroni udział za pomocą serwera usługi MAB i korzysta z wcześniejszych wersji usługi VSS systemu Windows w celu ułatwienia przywracania.

### <a name="before-you-start"></a>Przed rozpoczęciem

1. Na SQL Server Utwórz folder na dysku z wystarczającą ilością wolnego miejsca do przechowywania pojedynczej kopii kopii zapasowej. Na przykład: `C:\MABSBACKUP`.

1. Udostępnij ten folder. Na przykład Udostępnij `C:\MABSBACKUP` folder jako *DPMBACKUP*.

1. Skopiuj i wklej poniższe polecenie OSQL do Notatnika i Zapisz je w pliku o nazwie `C:\MABSACKUP\bkupdb.cmd` . Upewnij się, że nie ma rozszerzenia. txt. Zmodyfikuj SQL_Instance_name i DPMDB_NAME, aby pasowały do wystąpienia i nazwy DPMDB używanej przez serwer serwera usługi MAB.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Za pomocą Notatnika otwórz plik **ScriptingConfig.xml** znajdujący się w `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` folderze na serwerze serwera usługi MAB.

1. Zmodyfikuj **ScriptingConfig.xml** i Zmień **DataSourceName =** na literę dysku, która zawiera folder/udział folder dpmdbbackup. Zmień wpis PreBackupScript na pełną ścieżkę i nazwę **BkUpDB. cmd** zapisanego w kroku 3.

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

1. Chroń folder C:\MABSBACKUP lub `\sqlservername\MABSBACKUP` udział przy użyciu serwera usługi MAB i poczekaj na utworzenie repliki początkowej. W folderze C:\MABSBACKUP powinien znajdować się plik **DPMDB. bak** w wyniku uruchomienia skryptu poprzedzającego utworzenie kopii zapasowej, który został skopiowany do repliki serwera usługi MAB.

1. Jeśli odzyskiwanie samoobsługowe nie zostanie włączone, należy wykonać kilka dodatkowych czynności w celu udostępnienia folderu MABSBACKUP w replice:

    1. W konsoli programu serwera usługi MAB > **Ochrona**Znajdź źródło danych MABSBACKUP i wybierz je. W sekcji szczegółów wybierz **pozycję kliknij, aby wyświetlić szczegóły** dotyczące linku do ścieżki repliki i skopiuj ścieżkę do Notatnika. Usuń ścieżkę źródłową i zachowaj ścieżkę docelową. Ścieżka powinna wyglądać podobnie do następującej: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Utwórz udział w tej ścieżce przy użyciu nazwy udziału **MABSSERVERNAME-DPMDB**. Możesz użyć poniższego polecenia Net Share w wierszu polecenia z uprawnieniami administracyjnymi.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Konfigurowanie kopii zapasowej

Można utworzyć kopię zapasową bazy danych serwera usługi MAB, tak jak w przypadku każdej innej bazy danych SQL Server przy użyciu SQL Server natywnej kopii zapasowej.

- Zapoznaj się z [opisem](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) kopii zapasowych programu SQL Server.

- [Dowiedz się więcej](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) o tworzeniu kopii zapasowych programu SQL Server w chmurze.

### <a name="recover-the-mabs-database"></a>Odzyskaj bazę danych serwera usługi MAB

1. Połącz się z `\\MABSServer\MABSSERVERNAME-dpmdb` udziałem przy użyciu Eksploratora z dowolnego komputera z systemem Windows.

2. Kliknij prawym przyciskiem myszy plik **DPMDB. bak** , aby wyświetlić właściwości. Na karcie **poprzednie wersje** znajdują się wszystkie kopie zapasowe, które można wybrać i skopiować. Istnieje również bardzo Ostatnia kopia zapasowa nadal znajdująca się w folderze C:\MABSBACKUP, który również jest łatwo dostępny.

3. Jeśli potrzebujesz przenieść dysk puli magazynów serwera usługi MAB dołączony do sieci SAN do innego serwera, aby był możliwy odczyt z woluminu repliki, lub ponownie zainstalować system Windows w celu odczytania lokalnie dołączonych dysków, musisz znać ścieżkę punktu instalacji woluminu repliki serwera usługi MAB lub wcześniej identyfikator GUID woluminu, aby wiedzieć, który wolumin przechowuje kopię zapasową bazy danych. Aby wyodrębnić te informacje w dowolnej chwili po rozpoczęciu ochrony, ale zanim zajdzie potrzeba przywrócenia, możesz użyć poniższego skryptu SQL. Zastąp wartość `%dpmsqlservername%` nazwą SQL Server hostujący bazę danych.

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

4. Jeśli konieczne jest odzyskanie po przeniesieniu dysków puli magazynu serwera usługi MAB lub odbudowywania serwera serwera usługi MAB:

    1. Istnieje identyfikator GUID woluminu, dlatego należy zainstalować wolumin na innym serwerze z systemem Windows lub po odbudowie serwera serwera usługi MAB, użyj **mountvol.exe** , aby przypisać literę dysku przy użyciu identyfikatora GUID woluminu z danych wyjściowych skryptu SQL: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Udostępnij ponownie folder MABSBACKUP na woluminie repliki przy użyciu litery dysku i części ścieżki repliki reprezentującej strukturę folderów.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Połącz się z `\\SERVERNAME\MABSSERVERNAME-dpmdb` udziałem przy użyciu Eksploratora z dowolnego komputera z systemem Windows.

    4. Kliknij prawym przyciskiem myszy plik **DPMDB. bak** , aby wyświetlić właściwości. Na karcie **poprzednie wersje** znajdują się wszystkie kopie zapasowe, które można wybrać i skopiować.

## <a name="using-dpmsync"></a>Korzystanie z narzędzia DPMSync

**DpmSync** to narzędzie wiersza polecenia, które umożliwia synchronizowanie bazy danych serwera usługi MAB ze stanem dysków w puli magazynów oraz z zainstalowanymi agentami ochrony. Narzędzie DpmSync przywraca bazę danych serwera usługi MAB, synchronizuje bazę danych serwera usługi MAB z replikami w puli magazynów, przywraca bazę danych raportów i ponownie przydziela brakujące repliki.

### <a name="parameters"></a>Parametry

| Parametr      | Opis    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Przywraca bazę danych serwera usługi MAB z określonej lokalizacji.|
| **-Synchronizuj**                            | Synchronizuje przywrócone bazy danych. Po przywróceniu baz danych należy uruchomić polecenie **DpmSync-Sync** . Po uruchomieniu polecenia **DpmSync-Sync**niektóre repliki mogą nadal być oznaczone jako brakujące. |
| **-DbLoc** *lokalizacji*                | Określa lokalizację kopii zapasowej bazy danych serwera usługi MAB.|
| **-InstanceName** <br/>*server\instance*     | Wystąpienie, do którego należy przywrócić DPMDB.|
| **-ReallocateReplica**         | Ponownie przydziela wszystkie brakujące woluminy repliki bez synchronizacji. |
| **-Kopiowane**                      | Wskazuje, że zakończono ładowanie danych do nowo przydzielonego woluminu repliki. Ma to zastosowanie tylko w przypadku komputerów klienckich. |

**Przykład 1:** Aby przywrócić bazę danych serwera usługi MAB z lokalnego nośnika kopii zapasowej na serwerze serwera usługi MAB, uruchom następujące polecenie:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

Po przywróceniu bazy danych serwera usługi MAB w celu zsynchronizowania baz danych Uruchom następujące polecenie:

```cmd
DpmSync -Sync
```

Po przywróceniu i zsynchronizowaniu bazy danych serwera usługi MAB oraz przed przystąpieniem do przywracania repliki Uruchom następujące polecenie, aby ponownie przydzielić miejsce na dysku dla repliki:

```cmd
DpmSync -ReallocateReplica
```

**Przykład 2:** Aby przywrócić bazę danych serwera usługi MAB ze zdalnej bazy danych, uruchom następujące polecenie na komputerze zdalnym:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

Po przywróceniu bazy danych serwera usługi MAB w celu zsynchronizowania baz danych Uruchom następujące polecenie na serwerze serwera usługi MAB:

```cmd
DpmSync -Sync
```

Po przywróceniu i zsynchronizowaniu bazy danych serwera usługi MAB oraz przed przystąpieniem do przywracania repliki Uruchom następujące polecenie na serwerze serwera usługi MAB, aby ponownie przydzielić miejsce na dysku dla repliki:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Następne kroki

- [Macierz obsługi serwera usługi MAB](backup-support-matrix-mabs-dpm.md)
- [SERWERA USŁUGI MAB — CZĘSTO ZADAWANE PYTANIA](backup-azure-dpm-azure-server-faq.md)