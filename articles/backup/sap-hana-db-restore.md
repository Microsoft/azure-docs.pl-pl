---
title: Przywracanie SAP HANA baz danych na maszynach wirtualnych platformy Azure
description: W tym artykule opisano sposób przywracania SAP HANA baz danych uruchomionych w usłudze Azure Virtual Machines. Można również użyć przywracania między regionami w celu przywrócenia baz danych do regionu pomocniczego.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986127"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Przywracanie SAP HANA baz danych na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób przywracania SAP HANA baz danych uruchomionych na maszynie wirtualnej platformy Azure (VM), Azure Backup których kopia zapasowa została utworzona w magazynie Recovery Services. Przywracanie może służyć do tworzenia kopii danych dla scenariuszy deweloperskich/testowych lub powrotu do poprzedniego stanu.

Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych SAP HANA baz danych, zobacz [Tworzenie kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure](./backup-azure-sap-hana-database.md).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Przywracanie do punktu w czasie lub do punktu odzyskiwania

Azure Backup można przywrócić SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure w następujący sposób:

* Przywracanie do określonej daty lub czasu (w drugim) przy użyciu kopii zapasowych dziennika. Azure Backup automatycznie określa odpowiednie pełne, różnicowe kopie zapasowe i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia w oparciu o wybrany czas.

* Przywróć do konkretnej pełnej lub różnicowej kopii zapasowej, aby przywrócić do określonego punktu odzyskiwania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przywróceniem bazy danych należy zwrócić uwagę na następujące kwestie:

* Bazę danych można przywrócić tylko do wystąpienia SAP HANA znajdującego się w tym samym regionie.

* Wystąpienie docelowe musi być zarejestrowane w tym samym magazynie co źródło.

* Azure Backup nie może zidentyfikować dwóch różnych wystąpień SAP HANA na tej samej maszynie wirtualnej. Dlatego przywracanie danych z jednego wystąpienia do innego na tej samej maszynie wirtualnej nie jest możliwe.

* Aby upewnić się, że docelowe wystąpienie SAP HANA jest gotowe do przywrócenia, sprawdź stan **gotowości do utworzenia kopii zapasowej** :

  1. Otwórz magazyn, w którym zarejestrowano wystąpienie SAP HANA docelowego.

  1. Na pulpicie nawigacyjnym magazynu w obszarze **wprowadzenie**wybierz pozycję **kopia zapasowa**.

      ![Tworzenie kopii zapasowej na pulpicie nawigacyjnym magazynu](media/sap-hana-db-restore/getting-started-backup.png)

  1. W obszarze **kopia zapasowa**w obszarze **co chcesz utworzyć kopię zapasową?** wybierz **SAP HANA na maszynie wirtualnej platformy Azure**.

      ![Wybierz SAP HANA na maszynie wirtualnej platformy Azure](media/sap-hana-db-restore/sap-hana-backup.png)

  1. W obszarze **odnajdywanie baz danych na maszynach wirtualnych**wybierz pozycję **Wyświetl szczegóły**.

      ![Wyświetl szczegóły](media/sap-hana-db-restore/view-details.png)

  1. Przejrzyj **gotowość do wykonania kopii zapasowej** docelowej maszyny wirtualnej.

      ![Serwery chronione](media/sap-hana-db-restore/protected-servers.png)

* Aby dowiedzieć się więcej na temat typów przywracania obsługiwanych przez SAP HANA, zapoznaj się z artykułem SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Przywracanie bazy danych

Do przywrócenia wymagane są następujące uprawnienia:

* Uprawnienia **operatora kopii zapasowych** w magazynie, w którym wykonujesz przywracanie.
* Dostęp **współautora (zapisu)** do ŹRÓDŁOWEJ maszyny wirtualnej, której kopia zapasowa została utworzona.
* Dostęp **współautora (zapis**) do docelowej maszyny wirtualnej:
  * Jeśli przywracasz do tej samej maszyny wirtualnej, jest to źródłowa maszyna wirtualna.
  * Jeśli przywracasz do lokalizacji alternatywnej, jest to nowa docelowa maszyna wirtualna.

1. Otwórz magazyn, w którym zarejestrowano bazę danych SAP HANA, która ma zostać przywrócona

1. Na pulpicie nawigacyjnym magazynu w obszarze **chronione elementy**wybierz pozycję **elementy kopii zapasowej** .

    ![Elementy kopii zapasowej](media/sap-hana-db-restore/backup-items.png)

1. W obszarze **elementy kopii zapasowej**w obszarze **Typ zarządzania kopiami zapasowymi** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**

    ![Typ zarządzania kopiami zapasowymi](media/sap-hana-db-restore/backup-management-type.png)

1. Wybierz bazę danych do przywrócenia

    ![Baza danych do przywrócenia](media/sap-hana-db-restore/database-to-restore.png)

1. Przejrzyj menu Baza danych. Zawiera informacje o kopii zapasowej bazy danych, w tym:

    * Najstarsze i najnowsze punkty przywracania

    * Stan kopii zapasowej dziennika dla ostatnich 24 i 72 godzin dla bazy danych

    ![Menu bazy danych](media/sap-hana-db-restore/database-menu.png)

1. Wybierz pozycję **Przywróć bazę danych**

1. W obszarze **Przywracanie konfiguracji**Określ, gdzie (lub jak) przywrócić dane:

    * **Lokalizacja alternatywna**: Przywracanie bazy danych do alternatywnej lokalizacji i zachowywanie oryginalnej źródłowej bazy danych.

    * **Zastąp bazę**danych: Przywróć dane do tego samego wystąpienia SAP HANA co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych.

      ![Przywróć konfigurację](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Przywróć do lokalizacji alternatywnej

1. W menu **Przywróć konfigurację** w obszarze **gdzie należy przywrócić**wybierz pozycję **Lokalizacja alternatywna**.

    ![Przywróć do lokalizacji alternatywnej](media/sap-hana-db-restore/restore-alternate-location.png)

1. Wybierz nazwę hosta SAP HANA i nazwę wystąpienia, do którego chcesz przywrócić bazę danych.
1. Sprawdź, czy docelowe wystąpienie SAP HANA jest gotowe do przywrócenia, zapewniając **gotowość do wykonania kopii zapasowej.** Więcej informacji można znaleźć w [sekcji wymagania wstępne](#prerequisites) .
1. W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.

    > [!NOTE]
    > Przywracanie kontenerów pojedyncza baza danych (SDC) musi być zgodne z tymi [sprawdzeniami](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

1. Jeśli ma to zastosowanie, wybierz opcję **Zastąp, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu platformy Hana**.
1. Wybierz przycisk **OK**.

    ![Przywracanie konfiguracji — Ekran końcowy](media/sap-hana-db-restore/restore-configuration-last.png)

1. W obszarze **Wybierz punkt przywracania**wybierz pozycję **dzienniki (punkt w czasie)** , aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz opcję **pełna & różnicowa** , aby [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Przywróć i Zastąp

1. W menu **Przywróć konfigurację** w obszarze **gdzie można przywrócić**wybierz pozycję **Zastąp bazę danych**  >  **OK**.

    ![Zastąp bazę danych](media/sap-hana-db-restore/overwrite-db.png)

1. W obszarze **Wybierz punkt przywracania**wybierz pozycję **dzienniki (punkt w czasie)** , aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz opcję **pełna & różnicowa** , aby [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-recovery-point).

### <a name="restore-as-files"></a>Przywróć jako pliki

Aby przywrócić dane kopii zapasowej jako pliki zamiast bazy danych, wybierz polecenie **Przywróć jako pliki**. Po zrzucie plików do określonej ścieżki można wykonać te pliki na dowolnym komputerze SAP HANA, na którym chcesz je przywrócić jako bazę danych. Ponieważ można przenieść te pliki na dowolną maszynę, można teraz przywrócić dane między subskrypcjami i regionami.

1. W menu **Przywróć konfigurację** , w obszarze **gdzie i jak przywrócić**, wybierz pozycję **Przywróć jako pliki**.
1. Wybierz nazwę serwera **hosta** /platformy Hana, do którego chcesz przywrócić pliki kopii zapasowej.
1. W **ścieżce docelowej na serwerze**wprowadź ścieżkę folderu na serwerze wybranym w kroku 2. Jest to lokalizacja, w której usługa będzie zrzucać wszystkie niezbędne pliki kopii zapasowej.

    Zrzuty plików są następujące:

    * Pliki kopii zapasowej bazy danych
    * Pliki wykazu
    * Pliki metadanych JSON (dla każdego pliku kopii zapasowej, który jest powiązany)

    Zwykle ścieżka udziału sieciowego lub ścieżka zainstalowanego udziału plików platformy Azure, gdy zostanie określona jako ścieżka docelowa, zapewnia łatwiejszy dostęp do tych plików przez inne maszyny w tej samej sieci lub z tym samym udziałem plików platformy Azure.

    >[!NOTE]
    >Aby przywrócić pliki kopii zapasowej bazy danych w udziale plików platformy Azure zainstalowanym na docelowej zarejestrowanej maszynie wirtualnej, upewnij się, że konto główne ma uprawnienia do odczytu/zapisu w udziale plików platformy Azure.

    ![Wybierz ścieżkę docelową](media/sap-hana-db-restore/restore-as-files.png)

1. Wybierz **punkt przywracania** , za pomocą którego zostaną przywrócone wszystkie pliki i foldery kopii zapasowej.

    ![Wybierz punkt przywracania](media/sap-hana-db-restore/select-restore-point.png)

1. Wszystkie pliki kopii zapasowej skojarzone z wybranym punktem przywracania są zrzucane do ścieżki docelowej.
1. W oparciu o typ wybranego punktu przywracania (**punkt w czasie** lub **pełny & różnicowa**) zobaczysz co najmniej jeden folder utworzony w ścieżce docelowej. Jeden z folderów o nazwie `Data_<date and time of restore>` zawiera pełne i różnicowe kopie zapasowe, a inny folder o nazwie `Log` zawiera kopie zapasowe dziennika.
1. Przenieś te przywrócone pliki na serwer SAP HANA, na którym chcesz je przywrócić jako bazę danych.
1. Następnie wykonaj poniższe czynności:
    1. Ustaw uprawnienia do folderu/katalogu, w którym są przechowywane pliki kopii zapasowej, przy użyciu następującego polecenia:

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. Uruchom następny zestaw poleceń jako `<SID>adm`

        ```bash
        su - <sid>adm
        ```

    1. Wygeneruj plik wykazu do przywrócenia. Wyodrębnij **BackupId** z pliku metadanych JSON dla pełnej kopii zapasowej, która zostanie użyta w dalszej części operacji przywracania. Upewnij się, że kopie zapasowe pełnych i dzienników znajdują się w różnych folderach i Usuń pliki wykazu oraz pliki metadanych JSON w tych folderach.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        W powyższym poleceniu:

        * `<DataFileDir>` -folder zawierający pełne kopie zapasowe
        * `<LogFilesDir>` -folder zawierający kopie zapasowe dziennika
        * `<PathToPlaceCatalogFile>` -folder, w którym został wygenerowany plik wykazu, musi być umieszczony

    1. Przywróć przy użyciu nowo wygenerowanego pliku wykazu za pośrednictwem platformy HANA Studio lub uruchom zapytanie HDBSQL Restore z tym nowo wygenerowanym wykazem. Poniżej wymieniono zapytania HDBSQL:

    * Aby przywrócić do punktu w czasie:

        W przypadku tworzenia nowej przywróconej bazy danych Uruchom polecenie HDBSQL w celu utworzenia nowej bazy danych, `<DatabaseName>` a następnie Zatrzymaj przywracanie bazy danych. Jeśli jednak przywracasz tylko istniejącą bazę danych, uruchom polecenie HDBSQL, aby zatrzymać bazę danych.

        Następnie uruchom następujące polecenie, aby przywrócić bazę danych:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` -Nazwa nowej bazy danych lub istniejącej bazy danych, która ma zostać przywrócona
        * `<Timestamp>` -Dokładne sygnatura czasowa przywracania do punktu w czasie
        * `<DatabaseName@HostName>` -Nazwa bazy danych, której kopia zapasowa jest używana do przywracania i nazwa serwera **hosta** /SAP HANA, na którym znajduje się ta baza danych. `USING SOURCE <DatabaseName@HostName>`Opcja określa, że kopia zapasowa danych (używana do przywracania) jest bazą danych o innym identyfikatorze SID lub nazwie niż docelowa maszyna SAP HANA. W związku z tym nie trzeba określać operacji przywracania na tym samym serwerze HANA, w którym jest wykonywana kopia zapasowa.
        * `<PathToGeneratedCatalogInStep3>` -Ścieżka do pliku wykazu wygenerowanego w **kroku C**
        * `<DataFileDir>` -folder zawierający pełne kopie zapasowe
        * `<LogFilesDir>` -folder zawierający kopie zapasowe dziennika
        * `<BackupIdFromJsonFile>` - **BackupId** wyodrębniony w **kroku C**

    * Aby przywrócić do konkretnej pełnej lub różnicowej kopii zapasowej:

        W przypadku tworzenia nowej przywróconej bazy danych Uruchom polecenie HDBSQL w celu utworzenia nowej bazy danych, `<DatabaseName>` a następnie Zatrzymaj przywracanie bazy danych. Jeśli jednak przywracasz tylko istniejącą bazę danych, uruchom polecenie HDBSQL, aby zatrzymać bazę danych:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` — Nazwa nowej bazy danych lub istniejącej bazy danych, którą chcesz przywrócić.
        * `<Timestamp>` -dokładne sygnatura czasowa przywracania do punktu w czasie
        * `<DatabaseName@HostName>` — Nazwa bazy danych, której kopia zapasowa jest używana do przywracania i nazwa serwera **hosta** /SAP HANA, na którym znajduje się ta baza danych. `USING SOURCE <DatabaseName@HostName>`Opcja określa, że kopia zapasowa danych (używana do przywracania) jest bazą danych o innym identyfikatorze SID lub nazwie niż docelowa maszyna SAP HANA. Dlatego nie trzeba określać operacji przywracania na tym samym serwerze HANA, w którym jest wykonywana kopia zapasowa.
        * `<PathToGeneratedCatalogInStep3>` -ścieżka do pliku wykazu wygenerowanego w **kroku C**
        * `<DataFileDir>` -folder zawierający pełne kopie zapasowe
        * `<LogFilesDir>` -folder zawierający kopie zapasowe dziennika
        * `<BackupIdFromJsonFile>` - **BackupId** wyodrębniony w **kroku C**

### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli wybrano opcję **dzienniki (punkt w czasie)** jako typ przywracania, wykonaj następujące czynności:

1. Wybierz punkt odzyskiwania z grafu dziennika, a następnie wybierz **przycisk OK** , aby wybrać punkt przywracania.

    ![Punkt przywracania](media/sap-hana-db-restore/restore-point.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.

    ![Wybierz pozycję Przywróć](media/sap-hana-db-restore/restore-restore.png)

1. Śledź postęp przywracania w obszarze **powiadomień** lub śledź go, wybierając pozycję **Przywróć zadania** w menu Baza danych.

    ![Przywracanie zostało wyzwolone pomyślnie](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Przywracanie do określonego punktu odzyskiwania

Jeśli wybrano opcję **pełny & różnicowa** jako typ przywracania, wykonaj następujące czynności:

1. Wybierz punkt odzyskiwania z listy i wybierz pozycję **OK** , aby wybrać punkt przywracania.

    ![Przywróć określony punkt odzyskiwania](media/sap-hana-db-restore/specific-recovery-point.png)

1. W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.

    ![Uruchom zadanie przywracania](media/sap-hana-db-restore/restore-specific.png)

1. Śledź postęp przywracania w obszarze **powiadomień** lub śledź go, wybierając pozycję **Przywróć zadania** w menu Baza danych.

    ![Postęp przywracania](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > W przypadku przywracania wielu baz danych (MDC) po przywróceniu bazy danych systemowych do wystąpienia docelowego jeden musi ponownie uruchomić skrypt przed rejestracją. Kolejne Przywracanie bazy danych dzierżawy zakończy się powodzeniem. Aby dowiedzieć się więcej, zobacz [temat Rozwiązywanie problemów — przywracanie MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="cross-region-restore"></a>Przywracanie między regionami

Jako jedna z opcji przywracania, przywracanie między regionami (CRR) umożliwia przywracanie SAP HANA baz danych hostowanych na maszynach wirtualnych platformy Azure w regionie pomocniczym, który jest sparowanym regionem platformy Azure.

Aby dołączyć do funkcji w wersji zapoznawczej, zapoznaj się z [sekcją przed rozpoczęciem](./backup-create-rs-vault.md#set-cross-region-restore).

Aby sprawdzić, czy CRR jest włączona, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie przywracania między regionami](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Wyświetlanie elementów kopii zapasowej w regionie pomocniczym

Jeśli CRR jest włączona, można wyświetlić elementy kopii zapasowej w regionie pomocniczym.

1. W portalu przejdź do pozycji **Recovery Services**  >  **elementy kopii zapasowej**magazynu.
1. Wybierz **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

>[!NOTE]
>Na liście zostaną wyświetlone tylko typy zarządzania kopiami zapasowymi obsługujące funkcję CRR. Obecnie dozwolony jest tylko program obsługujący przywracanie danych regionu pomocniczego do regionu pomocniczego.

![Elementy kopii zapasowej w regionie pomocniczym](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![Bazy danych w regionie pomocniczym](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Przywróć w regionie pomocniczym

Środowisko użytkownika do przywracania regionu pomocniczego będzie podobne do środowiska użytkownika w regionie podstawowym. Podczas konfigurowania szczegółów w okienku Przywracanie konfiguracji w celu skonfigurowania przywracania zostanie wyświetlony monit o podanie tylko parametrów regionu pomocniczego.

![Miejsce i sposób przywracania](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>Sieć wirtualną w regionie pomocniczym musi być przypisana unikatowa i nie może być używana dla żadnych innych maszyn wirtualnych w tej grupie zasobów.

![Powiadomienie o wyzwoleniu przywracania w toku](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* Po wyzwoleniu przywracania i fazie transferu danych nie można anulować zadania przywracania.
>* Role platformy Azure, które muszą zostać przywrócone w regionie pomocniczym, są takie same jak w regionie podstawowym.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorowanie zadań przywracania regionu pomocniczego

1. W portalu przejdź do obszaru **Recovery Services**  >  **zadania tworzenia kopii zapasowej** magazynu
1. Wybierz **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

    ![Odfiltrowane zadania tworzenia kopii zapasowej](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak](sap-hana-db-manage.md) zarządzać bazami danych SAP HANA kopia zapasowa przy użyciu Azure Backup
