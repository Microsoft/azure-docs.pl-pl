---
title: Przywracanie SQL Server baz danych na maszynie wirtualnej platformy Azure
description: W tym artykule opisano sposób przywracania SQL Server baz danych, które są uruchomione na maszynie wirtualnej platformy Azure i których kopia zapasowa została utworzona przy użyciu Azure Backup. Można również użyć przywracania między regionami w celu przywrócenia baz danych do regionu pomocniczego.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 7dd8d8d54fa7d33bb4a0935357597d19dd2368c5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734406"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Przywracanie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób przywracania bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure (VM), w której utworzono kopię zapasową usługi [Azure Backup](backup-overview.md) do magazynu Azure Backup Recovery Services.

W tym artykule opisano sposób przywracania SQL Server baz danych. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowych baz danych SQL Server na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Przywracanie do czasu lub punktu odzyskiwania

Azure Backup można przywrócić SQL Server baz danych uruchomionych na maszynach wirtualnych platformy Azure w następujący sposób:

- Przywracanie do określonej daty lub czasu (w drugim) przy użyciu kopii zapasowych dziennika transakcji. Azure Backup automatycznie określa odpowiednią pełną, różnicową kopię zapasową i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia w oparciu o wybrany czas.
- Przywróć określoną pełną lub różnicową kopię zapasową w celu przywrócenia do określonego punktu odzyskiwania.

## <a name="restore-prerequisites"></a>Przywróć wymagania wstępne

Przed przystąpieniem do przywracania bazy danych należy zwrócić uwagę na następujące kwestie:

- Można przywrócić bazę danych do wystąpienia programu SQL Server w tym samym regionie platformy Azure.
- Serwer docelowy musi być zarejestrowany w tym samym magazynie co źródło.
- Jeśli na serwerze jest uruchomionych wiele wystąpień, wszystkie wystąpienia powinny zostać uruchomione. W przeciwnym razie serwer nie będzie wyświetlany na liście serwerów docelowych, aby przywrócić bazę danych. Aby uzyskać więcej informacji, zapoznaj się z [procedurą rozwiązywania problemów](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances).
- Aby przywrócić bazę danych zaszyfrowaną TDE na inny SQL Server, należy najpierw [przywrócić certyfikat na serwerze docelowym](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- Bazy danych z włączoną funkcją [przechwytywania](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) zmian należy przywrócić przy użyciu opcji [Przywróć jako pliki](#restore-as-files) .
- Przed przystąpieniem do przywracania bazy danych "Master" uruchom wystąpienie SQL Server w trybie jednego użytkownika przy użyciu opcji Start **-m AzureWorkloadBackup**.
  - Wartość parametru **-m** to nazwa klienta.
  - Połączenie może otworzyć tylko określona nazwa klienta.
- Dla wszystkich systemowych baz danych (model, Master, msdb) Zatrzymaj usługę agenta SQL Server przed wywołaniem przywracania.
- Zamknij wszystkie aplikacje, które mogą próbować nawiązać połączenie z dowolną z tych baz danych.

## <a name="restore-a-database"></a>Przywracanie bazy danych

Do przywrócenia wymagane są następujące uprawnienia:

- Uprawnienia **operatora kopii zapasowych** w magazynie, w którym wykonujesz przywracanie.
- Dostęp **współautora (zapisu)** do ŹRÓDŁOWEJ maszyny wirtualnej, której kopia zapasowa została utworzona.
- Dostęp **współautora (zapis)** do docelowej maszyny wirtualnej:
  - Jeśli przywracasz do tej samej maszyny wirtualnej, jest to źródłowa maszyna wirtualna.
  - Jeśli przywracasz do lokalizacji alternatywnej, jest to nowa docelowa maszyna wirtualna.

Przywróć w następujący sposób:

1. Otwórz magazyn, w którym zarejestrowano SQL Server maszynę wirtualną.
2. Na pulpicie nawigacyjnym magazynu w obszarze **użycie** wybierz pozycję **elementy kopii zapasowej**.
3. W obszarze **elementy kopii zapasowej** w obszarze **Typ zarządzania kopiami zapasowymi** wybierz pozycję **SQL na maszynie wirtualnej platformy Azure**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Wybierz bazę danych do przywrócenia.

    ![Wybieranie bazy danych do przywrócenia](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Przejrzyj menu Baza danych. Zawiera informacje o kopii zapasowej bazy danych, w tym:

    - Najstarsze i najnowsze punkty przywracania.
    - Stan kopii zapasowej dziennika dla ostatnich 24 godzin dla baz danych, które są w pełni i w trybie odzyskiwania zbiorczego i są skonfigurowane do tworzenia kopii zapasowych dzienników transakcyjnych.

6. Wybierz pozycję **Przywróć**.

    ![Wybierz pozycję Przywróć](./media/backup-azure-sql-database/restore-db.png)

7. W obszarze **Konfiguracja przywracania** Określ, gdzie (lub jak) przywrócić dane:
   - **Lokalizacja alternatywna**: Przywracanie bazy danych do alternatywnej lokalizacji i zachowywanie oryginalnej źródłowej bazy danych.
   - **Zastąp bazę** danych: Przywróć dane do tego samego wystąpienia SQL Server co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych.

        > [!IMPORTANT]
        > Jeśli wybrana baza danych należy do grupy dostępności Always On, SQL Server nie zezwala na zastąpienie bazy danych. Dostępna jest tylko **Lokalizacja alternatywna** .
        >
   - **Przywróć jako pliki**: zamiast przywracać jako bazę danych, Przywróć pliki kopii zapasowej, które mogą być odzyskiwane jako baza danych później na dowolnym komputerze, na którym znajdują się pliki, przy użyciu SQL Server Management Studio.
     ![Menu Konfiguracja przywracania](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

1. W menu **Przywróć konfigurację** w obszarze **gdzie należy przywrócić** wybierz pozycję **Lokalizacja alternatywna**.
1. Wybierz nazwę SQL Server i wystąpienie, do którego chcesz przywrócić bazę danych.
1. W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.
1. Jeśli ma to zastosowanie, wybierz opcję **Zastąp, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu programu SQL**.
1. Wybierz pozycję **punkt przywracania**, a następnie wybierz, czy chcesz [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time) , czy [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-restore-point).

    ![Wybierz punkt przywracania](./media/backup-azure-sql-database/select-restore-point.png)

    ![Przywracanie do punktu w czasie](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. W menu **Konfiguracja zaawansowana**:

    - Jeśli chcesz zachować bazę danych bez działania po przywróceniu, Włącz opcję **Przywróć z opcją NORECOVERY**.
    - Jeśli chcesz zmienić lokalizację przywracania na serwerze docelowym, wprowadź nowe ścieżki docelowe.

        ![Wprowadź ścieżki docelowe](./media/backup-azure-sql-database/target-paths.png)

1. Wybierz **przycisk OK** , aby wyzwolić przywracanie. Śledź postęp przywracania w obszarze **powiadomień** lub śledź go w widoku **zadania tworzenia kopii zapasowej** w magazynie.

    > [!NOTE]
    > Przywracanie do punktu w czasie jest dostępne tylko dla kopii zapasowych dzienników dla baz danych, które są w trybie odzyskiwania pełnym i zbiorczym.

### <a name="restore-and-overwrite"></a>Przywróć i Zastąp

1. W menu **Przywróć konfigurację** w obszarze **gdzie można przywrócić** wybierz pozycję **Zastąp bazę danych**  >  **OK**.

    ![Wybieranie pozycji Zastąp bazę danych](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. W obszarze **Wybierz punkt przywracania** wybierz pozycję **dzienniki (punkt w czasie)** , aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz opcję **pełna & różnicowa** , aby przywrócić do [określonego punktu odzyskiwania](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Przywracanie do punktu w czasie jest dostępne tylko dla kopii zapasowych dzienników dla baz danych, które są w trybie odzyskiwania pełnym i zbiorczym.

### <a name="restore-as-files"></a>Przywróć jako pliki

Aby przywrócić dane kopii zapasowej jako pliki. bak zamiast bazy danych, wybierz polecenie **Przywróć jako pliki**. Po zrzucie plików do określonej ścieżki można wykonać te pliki na dowolnym komputerze, na którym chcesz je przywrócić jako bazę danych. Ponieważ można przenieść te pliki na dowolną maszynę, można teraz przywrócić dane między subskrypcjami i regionami.

1. W obszarze **gdzie i jak przywrócić**, wybierz pozycję **Przywróć jako pliki**.
1. Wybierz nazwę SQL Server, do której chcesz przywrócić pliki kopii zapasowej.
1. W **ścieżce docelowej na serwerze** wprowadź ścieżkę folderu na serwerze wybranym w kroku 2. Jest to lokalizacja, w której usługa będzie zrzucać wszystkie niezbędne pliki kopii zapasowej. Zwykle ścieżka udziału sieciowego lub ścieżka zainstalowanego udziału plików platformy Azure, gdy zostanie określona jako ścieżka docelowa, zapewnia łatwiejszy dostęp do tych plików przez inne maszyny w tej samej sieci lub z tym samym udziałem plików platformy Azure.<BR>

    >Aby przywrócić pliki kopii zapasowej bazy danych w udziale plików platformy Azure zainstalowanym na docelowej zarejestrowanych maszynach wirtualnych, upewnij się, że usługa NT NT\SYSTEM ma dostęp do udziału plików. Aby udzielić uprawnień do odczytu/zapisu dla procesu AFS zainstalowanego na maszynie wirtualnej, można wykonać kroki podane poniżej:
    >
    >- Uruchom `PsExec -s cmd` , aby wejść do powłoki NT NT\SYSTEM
    >   - Wykonaj polecenie `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Weryfikowanie dostępu za pomocą `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Rozpoczęcie przywracania jako plików z magazynu kopii zapasowych `\\<storageacct>.file.core.windows.net\<filesharename>` jako ścieżki<BR>
    PsExec można pobrać ze strony [Sysinternals](/sysinternals/downloads/psexec) .

1. Wybierz pozycję **OK**.

    ![Wybierz pozycję Przywróć jako pliki](./media/backup-azure-sql-database/restore-as-files.png)

1. Wybierz pozycję **punkt przywracania**, a następnie wybierz, czy chcesz [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time) , czy [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-restore-point).

1. Wszystkie pliki kopii zapasowej skojarzone z wybranym punktem odzyskiwania są zrzucane do ścieżki docelowej. Pliki można przywrócić jako bazę danych na dowolnym komputerze, na którym znajdują się one przy użyciu SQL Server Management Studio.

    ![Przywrócono pliki kopii zapasowej w ścieżce docelowej](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli wybrano opcję **dzienniki (punkt w czasie)** jako typ przywracania, wykonaj następujące czynności:

1. W obszarze **Przywróć datę/godzinę** Otwórz Kalendarz. W kalendarzu daty z punktami odzyskiwania są wyświetlane pogrubioną czcionką, a bieżąca data jest wyróżniona.
1. Wybierz datę mającą punkty odzyskiwania. Nie można wybrać dat, które nie mają punktów odzyskiwania.

    ![Otwórz Kalendarz](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Po wybraniu daty wykres osi czasu zawiera dostępne punkty odzyskiwania w ciągłym zakresie.
1. Określ czas odzyskiwania na wykresie osi czasu lub wybierz godzinę. Następnie wybierz przycisk **OK**.

### <a name="restore-to-a-specific-restore-point"></a>Przywróć do określonego punktu przywracania

Jeśli wybrano opcję **pełny & różnicowa** jako typ przywracania, wykonaj następujące czynności:

1. Wybierz punkt odzyskiwania z listy, a następnie wybierz pozycję **OK**, aby ukończyć procedurę punktu przywracania.

    ![Wybieranie punktu odzyskiwania pełnego](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Domyślnie wyświetlane są punkty odzyskiwania z ostatnich 30 dni. Punkty odzyskiwania można wyświetlić w starszej wersji niż 30 dni, wybierając pozycję **Filtruj** i wybierając zakres niestandardowy.

### <a name="restore-databases-with-large-number-of-files"></a>Przywracanie baz danych o dużej liczbie plików

Jeśli całkowity rozmiar ciągu plików w bazie danych jest większy niż [określony limit](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), Azure Backup przechowuje listę plików bazy danych w innym składniku Pit, dlatego nie można ustawić docelowej ścieżki przywracania podczas operacji przywracania. Pliki zostaną przywrócone do domyślnej ścieżki SQL.

  ![Przywracanie bazy danych z dużym plikiem](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>Przywracanie między regionami

Ponieważ jedna z opcji przywracania, przywracanie między regionami (CRR) umożliwia przywracanie baz danych SQL hostowanych na maszynach wirtualnych platformy Azure w regionie pomocniczym, który jest sparowanym regionem platformy Azure.

Aby dołączyć do funkcji w wersji zapoznawczej, zapoznaj się z [sekcją przed rozpoczęciem](./backup-create-rs-vault.md#set-cross-region-restore).

Aby sprawdzić, czy CRR jest włączona, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie przywracania między regionami](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Wyświetlanie elementów kopii zapasowej w regionie pomocniczym

Jeśli CRR jest włączona, można wyświetlić elementy kopii zapasowej w regionie pomocniczym.

1. W portalu przejdź do pozycji **Recovery Services**  >  **elementy kopii zapasowej** magazynu.
1. Wybierz **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

>[!NOTE]
>Na liście zostaną wyświetlone tylko typy zarządzania kopiami zapasowymi obsługujące funkcję CRR. Obecnie dozwolony jest tylko program obsługujący przywracanie danych regionu pomocniczego do regionu pomocniczego.

![Elementy kopii zapasowej w regionie pomocniczym](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![Bazy danych w regionie pomocniczym](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>Przywróć w regionie pomocniczym

Środowisko użytkownika do przywracania regionu pomocniczego będzie podobne do środowiska użytkownika w regionie podstawowym. Podczas konfigurowania szczegółów w okienku Przywracanie konfiguracji w celu skonfigurowania przywracania zostanie wyświetlony monit o podanie tylko parametrów regionu pomocniczego.

![Miejsce i sposób przywracania](./media/backup-azure-sql-database/restore-secondary-region.png)

>[!NOTE]
>Sieć wirtualną w regionie pomocniczym musi być przypisana unikatowa i nie może być używana dla żadnych innych maszyn wirtualnych w tej grupie zasobów.

![Powiadomienie o wyzwoleniu przywracania w toku](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>- Po wyzwoleniu przywracania i fazie transferu danych nie można anulować zadania przywracania.
>- Role platformy Azure, które muszą zostać przywrócone w regionie pomocniczym, są takie same jak w regionie podstawowym.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitorowanie zadań przywracania regionu pomocniczego

1. W portalu przejdź do obszaru **Recovery Services**  >  **zadania tworzenia kopii zapasowej** magazynu
1. Wybierz **region pomocniczy** , aby wyświetlić elementy w regionie pomocniczym.

    ![Odfiltrowane zadania tworzenia kopii zapasowej](./media/backup-azure-sql-database/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Następne kroki

[Zarządzanie i monitorowanie](manage-monitor-sql-database-backup.md) SQL Server baz danych, których kopię zapasową utworzono przy użyciu Azure Backup.
