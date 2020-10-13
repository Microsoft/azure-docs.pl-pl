---
title: Tworzenie kopii zapasowej SQL Server przy użyciu Azure Backup Server
description: W tym artykule poznasz konfigurację tworzenia kopii zapasowych SQL Server baz danych przy użyciu serwera Microsoft Azure Backup Server (serwera usługi MAB).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 29813741e88ad5f2bc5109be87939abf7cc11502
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316923"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Tworzenie kopii zapasowych SQL Server na platformie Azure przy użyciu Azure Backup Server

W tym artykule opisano sposób konfigurowania kopii zapasowych baz danych SQL Server przy użyciu programu Microsoft Azure Backup Server (serwera usługi MAB).

Aby utworzyć kopię zapasową bazy danych SQL Server i odzyskać ją z platformy Azure:

1. Tworzenie zasad tworzenia kopii zapasowych w celu ochrony baz danych SQL Server na platformie Azure.
1. Tworzenie kopii zapasowych na żądanie na platformie Azure.
1. Odzyskaj bazę danych na platformie Azure.

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

* Jeśli Twoja baza danych ma pliki w zdalnym udziale plików, ochrona zakończy się niepowodzeniem z błędem o identyfikatorze 104. SERWERA usługi MAB nie obsługuje ochrony danych SQL Server w zdalnym udziale plików.
* SERWERA usługi MAB nie chroni baz danych przechowywanych w zdalnych udziałach SMB.
* Upewnij się, że [repliki grupy dostępności są skonfigurowane jako tylko do odczytu](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server).
* Należy jawnie dodać konto System **systemowe NTAUTHORITY\SYSTEM** do grupy sysadmin na SQL Server.
* W przypadku przeprowadzania odzyskiwania do lokalizacji alternatywnej dla częściowo zawartej bazy danych należy upewnić się, że docelowe wystąpienie programu SQL Server ma włączoną funkcję [zawarte bazy danych](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) .
* W przypadku przeprowadzania odzyskiwania alternatywnej lokalizacji bazy danych strumieni plików należy upewnić się, że docelowe wystąpienie programu SQL Server ma włączoną funkcję [bazy danych strumienia plików](/sql/relational-databases/blob/enable-and-configure-filestream) .
* Ochrona programu SQL Server z włączoną funkcją AlwaysOn:
  * SERWERA usługi MAB wykrywa grupy dostępności podczas uruchamiania zapytania podczas tworzenia grupy ochrony.
  * SERWERA usługi MAB wykrywa tryb failover i kontynuuje ochronę bazy danych.
  * SERWERA usługi MAB obsługuje konfiguracje klastra wielolokacjowego dla wystąpienia SQL Server.
* W przypadku ochrony baz danych używających funkcji AlwaysOn serwera usługi MAB mają następujące ograniczenia:
  * SERWERA usługi MAB będzie przestrzegać zasad tworzenia kopii zapasowych dla grup dostępności ustawionych w SQL Server na podstawie preferencji dotyczących kopii zapasowych w następujący sposób:
    * Preferuj pomocniczą — kopie zapasowe będą stosowane tylko do repliki pomocniczej z wyjątkiem sytuacji, gdy replika podstawowa jest jedyną repliką online. Jeśli jest dostępnych wiele replik pomocniczych, do kopii zapasowej zostanie wybrany węzeł o najwyższym priorytecie kopii zapasowej. Jeśli dostępna jest tylko replika podstawowa, kopia zapasowa powinna być wykonywana w replice podstawowej.
    * Tylko pomocnicza — kopia zapasowa nie powinna być wykonywana dla repliki podstawowej. Jeśli replika podstawowa jest jedyną repliką online, kopia zapasowa nie powinna być wykonywana.
    * Podstawowa — kopie zapasowe powinny być zawsze wykonywane dla repliki podstawowej.
    * Dowolna replika — kopie zapasowe powinny być wykonywane dla dowolnych replik w grupie dostępności. Węzeł, z którego będzie wykonywana kopia zapasowa, powinien być oparty na priorytetach kopii zapasowej dla każdego z węzłów.
  * . Weź pod uwagę następujące kwestie:
    * Kopie zapasowe mogą być wykonywane z dowolnej możliwej do odczytu repliki — to jest, podstawowa, synchroniczna pomocnicza, asynchroniczna pomocnicza.
    * Jeśli dowolna replika jest wykluczona z kopii zapasowej, na przykład funkcja **wykluczania repliki** jest włączona lub oznaczona jako nieczytelna, ta replika nie zostanie wybrana do wykonania kopii zapasowej w ramach żadnej z opcji.
    * Jeśli jest dostępnych i możliwych do odczytu wiele replik, do kopii zapasowej zostanie wybrany węzeł o najwyższym priorytecie kopii zapasowej.
    * Jeśli kopia zapasowa nie powiedzie się w wybranym węźle, operacja tworzenia kopii zapasowej zakończy się niepowodzeniem.
    * Odzyskiwanie do oryginalnej lokalizacji nie jest obsługiwane.
* SQL Server 2014 lub więcej problemów z kopią zapasową:
  * Program SQL Server 2014 dodał nową funkcję w celu utworzenia [bazy danych dla SQL Server lokalnych w usłudze Windows Azure Blob Storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Nie można użyć serwera usługi MAB do ochrony tej konfiguracji.
  * Istnieją znane problemy z preferencjami "Preferuj pomocnicze" kopii zapasowych dla opcji SQL AlwaysOn. SERWERA usługi MAB zawsze wykonuje kopię zapasową z elementu pomocniczego. Jeśli nie można znaleźć żadnych dodatkowych, kopia zapasowa kończy się niepowodzeniem.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem upewnij się, że [zainstalowano i przygotowano Azure Backup Server](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Aby chronić SQL Server bazy danych na platformie Azure, najpierw utwórz zasady tworzenia kopii zapasowych:

1. W Azure Backup Server wybierz obszar roboczy **Ochrona** .
1. Wybierz pozycję **Nowy** , aby utworzyć grupę ochrony.

    ![Tworzenie grupy ochrony w Azure Backup Server](./media/backup-azure-backup-sql/protection-group.png)
1. Na stronie startowej zapoznaj się ze wskazówkami dotyczącymi tworzenia grupy ochrony. Następnie wybierz pozycję **Dalej**.
1. W polu Typ grupy ochrony wybierz pozycję **serwery**.

    ![Wybierz typ grupy ochrony serwerów](./media/backup-azure-backup-sql/pg-servers.png)
1. Rozwiń wystąpienie SQL Server, w którym znajdują się bazy danych, których kopię zapasową chcesz utworzyć. Zostaną wyświetlone źródła danych, których kopię zapasową można utworzyć z tego serwera. Rozwiń **wszystkie udziały SQL** , a następnie wybierz bazy danych, dla których chcesz utworzyć kopię zapasową. W tym przykładzie wybieramy polecenie ReportServer $ MSDPM2012 i ReportServer $ MSDPM2012TempDB. Wybierz opcję **Dalej**.

    ![Wybierz bazę danych SQL Server](./media/backup-azure-backup-sql/pg-databases.png)
1. Nadaj nazwę grupie ochrony, a następnie wybierz opcję **Chcę chronić w trybie online**.

    ![Wybierz metodę ochrony danych — krótkoterminowa ochrona dysku lub ochrona online platformy Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Na stronie **Określ cele Short-Term** Uwzględnij niezbędne dane wejściowe, aby utworzyć punkty kopii zapasowej na dysku.

    W tym przykładzie **Zakres przechowywania** jest ustawiony na *5 dni*. **Częstotliwość synchronizacji** kopii zapasowych jest ustawiona na co *15 minut*. **Ekspresowa pełna kopia zapasowa** jest ustawiona na *8:00 PM*.

    ![Konfigurowanie celów krótkoterminowych na potrzeby ochrony kopii zapasowych](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > W tym przykładzie punkt kopii zapasowej jest tworzony codziennie o godzinie 8:00. Dane, które zostały zmodyfikowane od ostatniego dnia 8:00 PM punktu kopii zapasowej. Ten proces jest nazywany **ekspresową pełną kopią zapasową**. Mimo że dzienniki transakcji są synchronizowane co 15 minut, jeśli konieczne jest odzyskanie bazy danych o 9:00 PM, wówczas punkt jest tworzony przez ponowne odtwarzanie dzienników z ostatniego ekspresowej pełnej kopii zapasowej, czyli 8:00 PM w tym przykładzie.
   >
   >

1. Wybierz opcję **Dalej**. SERWERA usługi MAB pokazuje ogólne dostępne miejsce do magazynowania. Pokazuje również potencjalne użycie miejsca na dysku.

    ![Konfigurowanie przydziału dysku w serwera usługi MAB](./media/backup-azure-backup-sql/pg-storage.png)

    Domyślnie serwera usługi MAB tworzy jeden wolumin na źródło danych (SQL Server Database). Wolumin jest używany na potrzeby początkowej kopii zapasowej. W tej konfiguracji Menedżer dysków logicznych (LDM) ogranicza ochronę serwera usługi MAB do źródeł danych 300 (bazy danych SQL Server). Aby obejść to ograniczenie, wybierz pozycję **Umieść dane w puli magazynów programu DPM**. W przypadku użycia tej opcji serwera usługi MAB korzysta z pojedynczego woluminu dla wielu źródeł danych. Ta konfiguracja umożliwia serwera usługi MAB ochronę do SQL Server 2 000 baz danych.

    W przypadku wybrania opcji **automatycznie Zwiększ woluminy**program serwera usługi MAB może obsłużyć zwiększony wolumin kopii zapasowej w miarę rozwoju danych produkcyjnych. Jeśli nie wybrano opcji **automatycznie Zwiększ woluminy**, program serwera usługi MAB ogranicza magazyn kopii zapasowych do źródeł danych w grupie ochrony.
1. Jeśli jesteś administratorem, możesz zdecydować się na automatyczne przeniesienie tej początkowej kopii zapasowej **za pośrednictwem sieci** i wybranie czasu transferu. Lub **ręcznie** przetransferować kopię zapasową. Następnie wybierz pozycję **Dalej**.

    ![Wybierz metodę tworzenia repliki w serwera usługi MAB](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga przeniesienia całego źródła danych (SQL Server Database). Dane kopii zapasowej są przenoszone z serwera produkcyjnego (SQL Server komputera) do serwera usługi MAB. Jeśli ta kopia zapasowa jest duża, przeniesienie danych przez sieć może spowodować Przeciążenie przepustowości. Z tego powodu Administratorzy mogą zdecydować się na **Ręczne**przetransferowanie początkowej kopii zapasowej za pomocą nośnika wymiennego. Lub mogą automatycznie przesyłać dane **przez sieć** w określonym czasie.

    Po zakończeniu początkowej kopii zapasowej kopie zapasowe będą wykonywane przyrostowo na początkowej kopii zapasowej. Przyrostowe kopie zapasowe mają być małe i łatwo przesyłane przez sieć.
1. Wybierz, kiedy ma zostać uruchomione sprawdzanie spójności. Następnie wybierz pozycję **Dalej**.

    ![Wybierz, kiedy ma zostać uruchomione sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    SERWERA usługi MAB może uruchomić sprawdzanie spójności na integralność punktu kopii zapasowej. Oblicza sumę kontrolną pliku kopii zapasowej na serwerze produkcyjnym (w tym przykładzie komputer SQL Server) oraz dane kopii zapasowej dla tego pliku w serwera usługi MAB. Jeśli zaznaczenie spowoduje znalezienie konfliktu, zakłada się, że plik kopii zapasowej w serwera usługi MAB jest uszkodzony. SERWERA usługi MAB naprawia kopię zapasową danych przez wysłanie bloków, które odpowiadają niezgodności sumy kontrolnej. Ze względu na to, że sprawdzanie spójności jest operacją intensywnie wykorzystującą wydajność, Administratorzy mogą zdecydować się na zaplanowanie kontroli spójności lub uruchomić ją automatycznie.
1. Wybierz źródła danych, które mają być chronione na platformie Azure. Następnie wybierz pozycję **Dalej**.

    ![Wybierz źródła danych do ochrony na platformie Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Jeśli jesteś administratorem, możesz wybrać harmonogramy kopii zapasowych i zasady przechowywania odpowiednie dla zasad organizacji.

    ![Wybieranie harmonogramów i zasad przechowywania](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są wykonywane codziennie o godzinie 12:00 PM i 8:00 PM.

    > [!TIP]
    > Na potrzeby szybkiego odzyskiwania należy zachować kilka krótkoterminowych punktów odzyskiwania na dysku. Te punkty odzyskiwania są używane do odzyskiwania operacyjnego. Platforma Azure służy jako dobra lokalizacja poza siedzibą firmy, zapewniając wyższą umowy SLA i gwarantowaną dostępność.
    >
    > Użyj Data Protection Manager (DPM), aby zaplanować kopie zapasowe platformy Azure po zakończeniu tworzenia kopii zapasowych na dysku lokalnym. Po zakończeniu tej czynności najnowsza kopia zapasowa dysku jest kopiowana na platformę Azure.
    >

1. Wybierz harmonogram zasad przechowywania. Aby uzyskać więcej informacji na temat działania zasad przechowywania, zobacz temat [używanie Azure Backup do zastępowania infrastruktury taśmowej](backup-azure-backup-cloud-as-tape.md).

    ![Wybierz zasady przechowywania w serwera usługi MAB](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są wykonywane codziennie o godzinie 12:00 PM i 8:00 PM. Są one przechowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godzinie 12:00 godzin będzie utrzymywana przez 104 tygodni.
    * Kopia zapasowa z ostatniej soboty miesiąca o godzinie 12:00 PM jest utrzymywana przez 60 miesięcy.
    * Kopia zapasowa od ostatniej soboty marca o godzinie 12:00 PM jest utrzymywana przez 10 lat.

    Po wybraniu zasad przechowywania wybierz pozycję **dalej**.
1. Wybierz sposób transferu początkowej kopii zapasowej na platformę Azure.

    * Opcja **automatycznie za pośrednictwem sieci** jest zgodna z harmonogramem tworzenia kopii zapasowych w celu przeniesienia danych na platformę Azure.
    * Aby uzyskać więcej informacji na temat **kopii zapasowej offline**, zobacz [Omówienie kopii zapasowej offline](offline-backup-overview.md).

    Po wybraniu mechanizmu transferu wybierz pozycję **dalej**.
1. Na stronie **Podsumowanie** Przejrzyj szczegóły zasad. Następnie wybierz pozycję **Utwórz grupę**. Możesz wybrać pozycję **Zamknij** i obejrzyj postęp zadania w obszarze roboczym **monitorowanie** .

    ![Postęp tworzenia grupy ochrony](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Tworzenie kopii zapasowych na żądanie dla bazy danych SQL Server

Punkt odzyskiwania jest tworzony podczas tworzenia pierwszej kopii zapasowej. Zamiast czekać na uruchomienie harmonogramu, można ręcznie wyzwolić utworzenie punktu odzyskiwania:

1. W grupie ochrony upewnij się, że baza danych ma stan **OK**.

    ![Grupa ochrony pokazująca stan bazy danych](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz polecenie **Utwórz punkt odzyskiwania**.

    ![Wybierz, aby utworzyć punkt odzyskiwania online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Z menu rozwijanego wybierz pozycję Ochrona w **trybie online**. Następnie wybierz przycisk **OK** , aby rozpocząć tworzenie punktu odzyskiwania na platformie Azure.

    ![Rozpocznij tworzenie punktu odzyskiwania na platformie Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Postęp zadania można wyświetlić w obszarze roboczym **monitorowanie** .

    ![Wyświetlanie postępu zadania w konsoli monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych SQL Server z platformy Azure

Aby odzyskać chronioną jednostkę, taką jak baza danych SQL Server, z platformy Azure:

1. Otwórz konsolę zarządzania serwerem programu DPM. Przejdź do obszaru roboczego **odzyskiwanie** , aby zobaczyć serwery, których kopię zapasową programu DPM. Wybierz bazę danych (w tym przykładzie ReportServer $ MSDPM2012). Wybierz **godzinę odzyskiwania** kończącą się w **trybie online**.

    ![Wybieranie punktu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Kliknij prawym przyciskiem myszy nazwę bazy danych i wybierz polecenie **Odzyskaj**.

    ![Odzyskiwanie bazy danych z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. Program DPM wyświetla szczegóły punktu odzyskiwania. Wybierz opcję **Dalej**. Aby zastąpić bazę danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia SQL Server**. Następnie wybierz pozycję **Dalej**.

    ![Odzyskiwanie bazy danych do jej oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie program DPM umożliwia odzyskiwanie bazy danych do innego wystąpienia SQL Server lub do autonomicznego folderu sieciowego.
1. Na stronie **Określ opcje odzyskiwania** możesz wybrać opcje odzyskiwania. Na przykład można wybrać ograniczenie przepustowości **sieci** , aby ograniczyć przepustowość wykorzystywaną przez funkcję odzyskiwania. Następnie wybierz pozycję **Dalej**.
1. Na stronie **Podsumowanie** zostanie wyświetlona bieżąca Konfiguracja odzyskiwania. Wybierz pozycję **Odzyskaj**.

    Stan odzyskiwania przedstawia odzyskaną bazę danych. Możesz wybrać przycisk **Zamknij** , aby zamknąć kreatora i wyświetlić postęp w obszarze roboczym **monitorowanie** .

    ![Rozpocznij proces odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po zakończeniu odzyskiwania przywrócona baza danych jest spójna z aplikacją.

### <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Azure Backup często zadawane pytania](backup-azure-backup-faq.md).
