---
title: Back up SQL Server to Azure as a DPM workload (SQL Server kopii zapasowej na platformie Azure jako obciążenie programu DPM
description: Wprowadzenie do kopii zapasowej baz danych SQL Server przy użyciu usługi Azure Backup service
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 03763c3bee5ee4ca5239c49c99fdbeedc242b24d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515179"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Back up SQL Server to Azure as a DPM workload (SQL Server kopii zapasowej na platformie Azure jako obciążenie programu DPM

Ten artykuł zawiera instrukcje konfiguracji służące do SQL Server kopii zapasowej baz danych przy użyciu Azure Backup.

Do kopii zapasowej SQL Server baz danych na platformie Azure potrzebne jest konto platformy Azure. Jeśli go nie masz, możesz utworzyć bezpłatne konto w zaledwie kilka minut. Aby uzyskać więcej informacji, zobacz [Tworzenie bezpłatnego konta platformy Azure.](https://azure.microsoft.com/pricing/free-trial/)

Aby wrócić do kopii zapasowej bazy SQL Server na platformie Azure i odzyskać ją z platformy Azure:

1. Utwórz zasady tworzenia kopii zapasowych w celu ochrony SQL Server baz danych na platformie Azure.
1. Tworzenie kopii zapasowych na żądanie na platformie Azure.
1. Odzyskiwanie bazy danych z platformy Azure.

>[!NOTE]
>Program DPM 2019 UR2 obsługuje SQL Server klastra trybu failover przy użyciu udostępnionych woluminów klastra (CSV).<br><br>
>Ta funkcja SQL Server ochrony wystąpienia klastra trybu [failover](../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md) za pomocą usługi Bezpośrednie miejsca do magazynowania na platformie Azure i SQL Server klastra trybu failover za pomocą dysków udostępnionych platformy [Azure.](../azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md) Aby chronić wystąpienie usługi SQL FCI wdrożone na maszynach wirtualnych platformy Azure, należy wdrożyć serwer programu DPM na maszynie wirtualnej platformy Azure. 

## <a name="prerequisites-and-limitations"></a>Wymagania wstępne i ograniczenia

* Jeśli Twoja baza danych ma pliki w zdalnym udziale plików, ochrona zakończy się niepowodzeniem z błędem o identyfikatorze 104. Program DPM nie obsługuje ochrony SQL Server danych w zdalnym udziału plików.
* Program DPM nie może chronić baz danych przechowywanych w zdalnych udziałach SMB.
* Upewnij [się, że repliki grupy dostępności są skonfigurowane jako tylko do odczytu.](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server)
* Należy jawnie dodać konto systemowe **NTAuthority\System do** grupy Sysadmin na SQL Server.
* Podczas odzyskiwania lokalizacji alternatywnej dla częściowo zawartej bazy danych należy się upewnić, że docelowe wystąpienie SQL ma włączoną funkcję [Zawarte bazy](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#enable) danych.
* Podczas odzyskiwania lokalizacji alternatywnej dla bazy danych strumienia plików należy się upewnić, że docelowe wystąpienie SQL ma włączoną [funkcję bazy](/sql/relational-databases/blob/enable-and-configure-filestream) danych strumienia plików.
* Ochrona programu SQL Server z włączoną funkcją AlwaysOn:
  * Program DPM wykrywa grupy dostępności podczas uruchamiania zapytań w trakcie tworzenia grupy ochrony.
  * Program DPM wykrywa tryb failover i nadal chroni bazę danych.
  * Program DPM obsługuje konfiguracje klastra wielolokalizacyjnego dla wystąpienia programu SQL Server.
* W przypadku ochrony baz danych używających funkcji AlwaysOn program DPM ma następujące ograniczenia:
  * Program DPM będzie honorować zasady tworzenia kopii zapasowych dla grup dostępności ustawionych w programie SQL Server na podstawie preferencji kopii zapasowej w następujący sposób:
    * Preferuj pomocniczą — kopie zapasowe będą stosowane tylko do repliki pomocniczej z wyjątkiem sytuacji, gdy replika podstawowa jest jedyną repliką online. Jeśli dostępnych jest wiele replik pomocniczych, do kopii zapasowej zostanie wybrany węzeł o najwyższym priorytecie kopii zapasowej. Jeśli dostępna jest tylko replika podstawowa, kopia zapasowa powinna zostać kopia zapasowa repliki podstawowej.
    * Tylko pomocnicza — kopia zapasowa nie powinna być wykonywana dla repliki podstawowej. Jeśli replika podstawowa jest jedyną repliką online, kopia zapasowa nie powinna być wykonywana.
    * Podstawowa — kopie zapasowe powinny być zawsze wykonywane dla repliki podstawowej.
    * Dowolna replika — kopie zapasowe powinny być wykonywane dla dowolnych replik w grupie dostępności. Węzeł, z którego będzie wykonywana kopia zapasowa, powinien być oparty na priorytetach kopii zapasowej dla każdego z węzłów.
  * . Weź pod uwagę następujące kwestie:
    * Kopie zapasowe mogą być wykonywane z dowolnej możliwej do odczytu repliki — podstawowej, synchronicznej pomocniczej, asynchronicznej pomocniczej.
    * Jeśli jakakolwiek replika jest wykluczona z kopii zapasowej, na przykład opcja Wyklucz replikę jest włączona lub oznaczona jako nie do odczytu, ta replika nie zostanie wybrana do kopii zapasowej w żadnej z opcji. 
    * Jeśli dostępnych jest wiele replik, które można odczytać, do kopii zapasowej zostanie wybrany węzeł o najwyższym priorytecie kopii zapasowej.
    * Jeśli tworzenie kopii zapasowej w wybranym węźle zakończy się niepowodzeniem, operacja tworzenia kopii zapasowej zakończy się niepowodzeniem.
    * Odzyskiwanie do oryginalnej lokalizacji nie jest obsługiwane.
* SQL Server problemy z tworzeniem kopii zapasowej w programie 2014 lub jego powyżej:
  * W programie SQL Server 2014 dodano nową funkcję tworzenia bazy danych dla lokalnych serwerów [SQL Server usłudze Windows Azure Blob Storage.](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) Do ochrony tej konfiguracji nie można używać programu DPM.
  * Istnieją znane problemy z preferencjami tworzenia kopii zapasowych "Preferuj pomocniczą" dla opcji Zawsze wł. SQL. Program DPM zawsze pobiera kopię zapasową z pomocniczej bazy danych. Jeśli nie można znaleźć pomocniczej bazy danych, tworzenie kopii zapasowej nie powiedzie się.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem upewnij się, że [](backup-azure-dpm-introduction.md#prerequisites-and-limitations) zostały spełnione wymagania wstępne dotyczące używania Azure Backup do ochrony obciążeń. Poniżej podano niektóre wstępnie wymagane zadania:

* Utwórz magazyn kopii zapasowych.
* Pobierz poświadczenia magazynu.
* Zainstaluj Azure Backup agenta.
* Zarejestruj serwer w magazynie.

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Aby chronić SQL Server baz danych na platformie Azure, najpierw utwórz zasady tworzenia kopii zapasowych:

1. Na serwerze Data Protection Manager (DPM) wybierz obszar **roboczy** Ochrona.
1. Wybierz **pozycję Nowa,** aby utworzyć grupę ochrony.

    ![Tworzenie grupy ochrony](./media/backup-azure-backup-sql/protection-group.png)
1. Na stronie startowej przejrzyj wskazówki dotyczące tworzenia grupy ochrony. Następnie wybierz przycisk **Dalej**.
1. Wybierz **pozycję Serwery.**

    ![Wybierz typ grupy ochrony Serwery](./media/backup-azure-backup-sql/pg-servers.png)
1. Rozwiń SQL Server maszynę wirtualną, na której znajdują się bazy danych, których kopię zapasową chcesz wrócić. Zostaną wyświetlony źródła danych, których kopię zapasową można utworzyć z tego serwera. Rozwiń **pozycję Wszystkie udziały SQL,** a następnie wybierz bazy danych, których kopię zapasową chcesz wrócić. W tym przykładzie wybierzemy pozycję ReportServer$MSDPM2012 i ReportServer$MSDPM2012TempDB. Następnie wybierz przycisk **Dalej**.

    ![Wybieranie bazy SQL Server danych](./media/backup-azure-backup-sql/pg-databases.png)
1. Nadaj grupie ochrony nazwę, a następnie wybierz pozycję **Chcę chronić w trybie online.**

    ![Wybieranie metody ochrony danych — krótkoterminowej ochrony dysku lub ochrony online na platformie Azure](./media/backup-azure-backup-sql/pg-name.png)
1. Na stronie **Short-Term cele** uwzględnij dane wejściowe niezbędne do utworzenia punktów kopii zapasowej na dysku.

    W tym przykładzie **dla ustawienia Zakres przechowywania** ustawiono wartość *5 dni.* Częstotliwość synchronizacji **kopii zapasowych jest** ustawiana na wartość co *15 minut.* **Ekspresowa pełna kopia** zapasowa jest *ustawiona na 20:00.*

    ![Konfigurowanie krótkoterminowych celów ochrony kopii zapasowych](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > W tym przykładzie punkt kopii zapasowej jest tworzony codziennie o godzinie 20:00. Dane, które zostały zmodyfikowane od punktu kopii zapasowej z poprzedniego dnia o 20:00, zostaną przeniesione. Ten proces nosi nazwę **ekspresowej pełnej kopii zapasowej.** Mimo że dzienniki transakcji są synchronizowane co 15 minut, jeśli musimy odzyskać bazę danych o godzinie 21:00, punkt jest tworzony przez ponowne odtwarzanie dzienników z ostatniego punktu ekspresowej pełnej kopii zapasowej, czyli 20:00 w tym przykładzie.
   >
   >

1. Wybierz opcję **Dalej**. Program DPM pokazuje ogólną ilość dostępnego miejsca do magazynowania. Pokazuje również potencjalne wykorzystanie miejsca na dysku.

    ![Konfigurowanie alokacji dysku](./media/backup-azure-backup-sql/pg-storage.png)

    Domyślnie program DPM tworzy jeden wolumin na źródło danych (SQL Server bazy danych). Wolumin jest używany do początkowej kopii zapasowej. W tej konfiguracji Menedżer dysków logicznych (LDM) ogranicza ochronę programu DPM do 300 źródeł danych (SQL Server baz danych). Aby omiąć to ograniczenie, wybierz pozycję **Kolokowanie danych w puli magazynów programu DPM.** W przypadku użycia tej opcji program DPM używa jednego woluminu dla wielu źródeł danych. Ta konfiguracja umożliwia programowi DPM ochronę maksymalnie 2000 baz SQL Server danych.

    W przypadku wybrania **opcji Automatycznie powiększaj woluminy** program DPM może uwzględnić zwiększony wolumin kopii zapasowej w miarę wzrostu ilości danych produkcyjnych. Jeśli nie wybierzesz opcji Automatycznie powiększaj woluminy, program DPM ogranicza magazyn kopii zapasowych do źródeł danych w grupie ochrony.

1. Jeśli jesteś administratorem, możesz wybrać opcję automatycznego  przesyłania początkowej kopii zapasowej za pośrednictwem sieci i wybrać czas transferu. Możesz też ręcznie **przenieść kopię** zapasową. Następnie wybierz przycisk **Dalej**.

    ![Wybieranie metody tworzenia repliki](./media/backup-azure-backup-sql/pg-manual.png)

    Początkowa kopia zapasowa wymaga transferu całego źródła danych (SQL Server bazy danych). Dane kopii zapasowej są przesunene z serwera SQL Server produkcyjnego na serwer DPM. Jeśli ta kopia zapasowa jest duża, przesyłanie danych za pośrednictwem sieci może spowodować przeciążenie przepustowości. Z tego powodu administratorzy mogą użyć nośnika wymiennego do ręcznego przeniesienia początkowej **kopii zapasowej.** Mogą też przesyłać dane **automatycznie przez sieć** w określonym czasie.

    Po zakończeniu tworzenia początkowej kopii zapasowej kopie zapasowe będą kontynuowane przyrostowo w początkowej kopii zapasowej. Przyrostowe kopie zapasowe są zwykle małe i można je łatwo przesyłać przez sieć.

1. Wybierz, kiedy uruchomić sprawdzanie spójności. Następnie wybierz przycisk **Dalej**.

    ![Wybieranie, kiedy należy uruchomić sprawdzanie spójności](./media/backup-azure-backup-sql/pg-consistent.png)

    Program DPM może uruchomić sprawdzanie spójności integralności punktu kopii zapasowej. Oblicza sumę kontrolną pliku kopii zapasowej na serwerze produkcyjnym (SQL Server w tym przykładzie) i dane kopii zapasowej dla tego pliku w programie DPM. Jeśli sprawdzanie wykryje konflikt, zakłada się, że plik kopii zapasowej w programie DPM jest uszkodzony. Program DPM naprawia dane kopii zapasowej, wysyłając bloki, które odpowiadają niezgodności sumy kontrolnej. Ponieważ sprawdzanie spójności jest operacją intensywnie obciążaną wydajnością, administratorzy mogą zaplanować sprawdzanie spójności lub uruchomić je automatycznie.

1. Wybierz źródła danych do ochrony na platformie Azure. Następnie wybierz przycisk **Dalej**.

    ![Wybieranie źródeł danych do ochrony na platformie Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Jeśli jesteś administratorem, możesz wybrać harmonogramy tworzenia kopii zapasowych i zasady przechowywania, które odpowiadają zasadom organizacji.

    ![Wybieranie harmonogramów i zasad przechowywania](./media/backup-azure-backup-sql/pg-schedule.png)

    W tym przykładzie kopie zapasowe są podejmowane codziennie o godzinie 12:00 i 20:00.

    > [!TIP]
    > Aby szybko odzyskiwać dane, zachowaj na dysku kilka krótkoterminowych punktów odzyskiwania. Te punkty odzyskiwania są używane do odzyskiwania operacyjnego. Platforma Azure służy jako dobra lokalizacja poza lokalizacją, zapewniając wyższe świadczenia usług SLA i gwarantowaną dostępność.
    >
    > Użyj programu DPM, aby zaplanować tworzenie kopii zapasowych platformy Azure po zakończeniu tworzenia kopii zapasowych dysków lokalnych. W przypadku korzystania z tej metody najnowsza kopia zapasowa dysku jest kopiowana na platformę Azure.
    >

1. Wybierz harmonogram zasad przechowywania. Aby uzyskać więcej informacji na temat działania zasad przechowywania, zobacz [Use Azure Backup to replace your tape infrastructure](backup-azure-backup-cloud-as-tape.md).

    ![Wybieranie zasad przechowywania](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    W tym przykładzie:

    * Kopie zapasowe są podejmowane codziennie o godzinie 12:00 i 20:00. Są one przechowywane przez 180 dni.
    * Kopia zapasowa w sobotę o godzinie 12:00 jest przechowywana przez 104 tygodnie.
    * Kopia zapasowa z ostatniej soboty miesiąca o godzinie 12:00 jest przechowywana przez 60 miesięcy.
    * Kopia zapasowa z ostatniej soboty marca o godzinie 12:00 jest przechowywana przez 10 lat.

    Po wybraniu zasad przechowywania wybierz pozycję **Dalej.**

1. Wybierz sposób przenoszenia początkowej kopii zapasowej na platformę Azure.

    * Opcja **Automatycznie za pośrednictwem sieci jest** zgodna z harmonogramem tworzenia kopii zapasowych w celu transferu danych na platformę Azure.
    * Aby uzyskać więcej informacji na temat kopii **zapasowej offline,** zobacz [Overview of Offline Backup (Omówienie kopii zapasowej offline).](offline-backup-overview.md)

    Po wybraniu mechanizmu transferu wybierz pozycję **Dalej.**

1. Na **stronie Podsumowanie** przejrzyj szczegóły zasad. Następnie wybierz **pozycję Utwórz grupę**. Możesz wybrać pozycję **Zamknij i** obserwować postęp zadania w obszarze **roboczym** Monitorowanie.

    ![Postęp tworzenia grupy ochrony](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Tworzenie kopii zapasowych bazy danych SQL Server na żądanie

Punkt odzyskiwania jest tworzony podczas tworzenia pierwszej kopii zapasowej. Zamiast czekać na uruchomienie harmonogramu, możesz ręcznie wyzwolić tworzenie punktu odzyskiwania:

1. W grupie ochrony upewnij się, że stan bazy danych to **OK.**

    ![Grupa ochrony przedstawiająca stan bazy danych](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Kliknij prawym przyciskiem myszy bazę danych, a następnie wybierz **polecenie Utwórz punkt odzyskiwania.**

    ![Wybieranie tworzenia punktu odzyskiwania online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Z menu rozwijanego wybierz pozycję **Ochrona w trybie online.** Następnie wybierz **przycisk OK,** aby rozpocząć tworzenie punktu odzyskiwania na platformie Azure.

    ![Rozpoczynanie tworzenia punktu odzyskiwania na platformie Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Postęp zadania można wyświetlić w obszarze **roboczym** Monitorowanie.

    ![Wyświetlanie postępu zadania w konsoli monitorowania](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Odzyskiwanie bazy danych SQL Server z platformy Azure

Aby odzyskać chronioną jednostkę, taką jak SQL Server bazy danych, z platformy Azure:

1. Otwórz konsolę zarządzania serwerem programu DPM. Przejdź do obszaru **roboczego Odzyskiwanie,** aby wyświetlić serwery, których kopię zapasową programu DPM ma wrócić. Wybierz bazę danych (w tym przykładzie ReportServer$MSDPM2012). Wybierz czas **odzyskiwania, który** kończy się na **online.**

    ![Wybieranie punktu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Kliknij prawym przyciskiem myszy nazwę bazy danych i wybierz polecenie **Odzyskaj.**

    ![Odzyskiwanie bazy danych z platformy Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. Program DPM wyświetla szczegóły punktu odzyskiwania. Wybierz opcję **Dalej**. Aby zastąpić bazę danych, wybierz typ odzyskiwania **Odzyskaj do oryginalnego wystąpienia SQL Server**. Następnie wybierz przycisk **Dalej**.

    ![Odzyskiwanie bazy danych do oryginalnej lokalizacji](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    W tym przykładzie program DPM umożliwia odzyskanie bazy danych do innego SQL Server lub do autonomicznego folderu sieciowego.
1. Na stronie **Określanie opcji odzyskiwania** można wybrać opcje odzyskiwania. Możesz na przykład wybrać pozycję **Ograniczanie przepustowości** sieci, aby ograniczać przepustowość używaną przez odzyskiwanie. Następnie wybierz przycisk **Dalej**.
1. Na **stronie Podsumowanie** zobaczysz bieżącą konfigurację odzyskiwania. Wybierz pozycję **Odzyskaj.**

    Stan odzyskiwania pokazuje odzyskiwaną bazę danych. Możesz wybrać pozycję **Zamknij,** aby zamknąć kreatora i wyświetlić postęp w obszarze **roboczym** Monitorowanie.

    ![Uruchamianie procesu odzyskiwania](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Po zakończeniu odzyskiwania przywrócona baza danych jest spójna z aplikacją.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Azure Backup FAQ (Często zadawane pytania).](backup-azure-backup-faq.yml)