---
title: Automatyczne nadmiarowe kopie zapasowe
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database i wystąpienie zarządzane SQL Azure automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i korzysta z magazynu geograficznie nadmiarowego platformy Azure do odczytu na potrzeby nadmiarowości geograficznej.
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 10/30/2020
ms.openlocfilehash: a97e39314b4dc15a360a01408f183a3f9a19c76f
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131364"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Zautomatyzowane kopie zapasowe — Azure SQL Database & wystąpienia zarządzane SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Co to jest kopia zapasowa bazy danych?

Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed uszkodzeniem lub usunięciem. Te kopie zapasowe umożliwiają przywracanie bazy danych do punktu w czasie w skonfigurowanym okresie przechowywania. Jeśli reguły ochrony danych wymagają, aby kopie zapasowe były dostępne przez dłuższy czas (do 10 lat), można skonfigurować [długoterminowe przechowywanie](long-term-retention-overview.md) zarówno dla baz danych o pojedynczej, jak i w puli.

### <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

Zarówno SQL Database, jak i wystąpienie zarządzane SQL używają technologii SQL Server do tworzenia [pełnych kopii zapasowych](/sql/relational-databases/backup-restore/full-database-backups-sql-server) co tydzień, [różnicowych kopii zapasowych](/sql/relational-databases/backup-restore/differential-backups-sql-server) co 12-24 godzin i [kopii zapasowych dziennika transakcji](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) co 5 – 10 minut. Częstotliwość tworzenia kopii zapasowych dziennika transakcji zależy od rozmiaru obliczeń i liczby działań związanych z bazą danych.

Podczas przywracania bazy danych usługa określa, które pełne, różnicowe i transakcyjne kopie zapasowe dziennika muszą zostać przywrócone.

### <a name="backup-storage-redundancy"></a>Nadmiarowość magazynu kopii zapasowych

Domyślnie, SQL Database i wystąpienia zarządzane SQL przechowują dane w [magazynach obiektów BLOB](../../storage/common/storage-redundancy.md) geograficznie nadmiarowych (RA-GRS), które są replikowane do [sparowanego regionu](../../best-practices-availability-paired-regions.md). Ułatwia to ochronę przed awariami magazynu kopii zapasowych w regionie podstawowym i umożliwia przywrócenie serwera do innego regionu w przypadku awarii. 

Opcja konfigurowania nadmiarowości magazynu kopii zapasowych zapewnia elastyczność wyboru między lokalnie nadmiarową, nadmiarową strefą lub geograficznie nadmiarowym magazynem obiektów BLOB dla wystąpienia zarządzanego SQL lub SQL Database. Aby upewnić się, że dane są przechowywane w tym samym regionie, w którym wdrożono wystąpienie zarządzane lub baza danych SQL, można zmienić domyślną nadmiarowość geograficznie nadmiarową magazynu kopii zapasowych i skonfigurować obiekty blob magazynu lokalnie nadmiarowego (LRS) lub strefy nadmiarowe (ZRS) dla kopii zapasowych. Mechanizmy nadmiarowości magazynu przechowują wiele kopii danych w taki sposób, aby były chronione przed planowanymi i nieplanowanymi zdarzeniami, w tym przejściowym awariami sprzętowymi, siecią lub przestojem lub bardzo naturalnymi katastrofami. Skonfigurowana nadmiarowość magazynu kopii zapasowych jest stosowana do obu krótkoterminowych ustawień przechowywania kopii zapasowych, które są używane do przywracania do punktu w czasie (kopie) oraz długoterminowych kopii zapasowych używanych do długoterminowych kopii zapasowych (LTR). 

W przypadku SQL Database nadmiarowość magazynu kopii zapasowej można skonfigurować w momencie tworzenia bazy danych lub można ją zaktualizować dla istniejącej bazy danych. zmiany wprowadzone do istniejącej bazy danych mają zastosowanie tylko do przyszłych kopii zapasowych. Po zaktualizowaniu nadmiarowości magazynu kopii zapasowej istniejącej bazy danych może upłynąć do 48 godzin, aby zmiany zostały zastosowane. Należy pamiętać, że funkcja przywracania geograficznego jest wyłączona, gdy tylko baza danych zostanie zaktualizowana w celu używania magazynu lokalnego lub Strefowo nadmiarowego. 


> [!IMPORTANT]
> Skonfiguruj nadmiarowość magazynu kopii zapasowych podczas procesu tworzenia wystąpienia zarządzanego, jak po zainicjowaniu obsługi administracyjnej zasobu nie można już zmieniać nadmiarowości magazynu. 

> [!IMPORTANT]
> Magazyn strefowo nadmiarowy jest obecnie dostępny tylko w [określonych regionach](../../storage/common/storage-redundancy.md#zone-redundant-storage). 

> [!NOTE]
> Konfigurowalne nadmiarowości magazynu kopii zapasowych dla Azure SQL Database są obecnie dostępne w publicznej wersji zapoznawczej w Brazylii Południowej Ta funkcja nie jest jeszcze dostępna dla warstwy skalowania. 

### <a name="backup-usage"></a>Użycie kopii zapasowej

Tych kopii zapasowych można użyć w następujących celach:

- **Przywracanie do punktu w czasie istniejącej bazy danych**  -  [Przywracanie istniejącej bazy danych do punktu w czasie w przeszłości w](recovery-using-backups.md#point-in-time-restore) okresie przechowywania przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. W przypadku SQL Database ta operacja tworzy nową bazę danych na tym samym serwerze, na którym znajduje się oryginalna baza danych, ale używa innej nazwy, aby uniknąć zastąpienia oryginalnej bazy danych. Po zakończeniu przywracania można usunąć oryginalną bazę danych. Alternatywnie można [zmienić](/sql/relational-databases/databases/rename-a-database) nazwę oryginalnej bazy danych, a następnie zmienić nazwę przywróconej bazy danych na oryginalną. Podobnie w przypadku wystąpienia zarządzanego SQL ta operacja tworzy kopię bazy danych w tym samym lub innym wystąpieniu zarządzanym w ramach tej samej subskrypcji i tego samego regionu.
- **Przywracanie do punktu w czasie usuniętej bazy danych**  -  [Przywracanie usuniętej bazy danych do czasu usunięcia](recovery-using-backups.md#deleted-database-restore) lub do dowolnego punktu w czasie w okresie przechowywania. Usuniętą bazę danych można przywrócić tylko na tym samym serwerze lub wystąpieniu zarządzanym, w którym została utworzona oryginalna baza danych. Podczas usuwania bazy danych usługa pobiera ostateczną kopię zapasową dziennika transakcji przed usunięciem, aby zapobiec utracie danych.
- **Przywracanie geograficzne**  -  [Przywracanie bazy danych do innego regionu geograficznego](recovery-using-backups.md#geo-restore). Funkcja przywracania geograficznego umożliwia odzyskanie sprawności po awarii geograficznej, gdy nie można uzyskać dostępu do bazy danych lub kopii zapasowych w regionie podstawowym. Tworzy nową bazę danych na dowolnym istniejącym serwerze lub wystąpieniu zarządzanym w dowolnym regionie świadczenia usługi Azure.
   > [!IMPORTANT]
   > Przywracanie geograficzne jest dostępne tylko dla baz danych SQL lub wystąpień zarządzanych skonfigurowanych przy użyciu geograficznie nadmiarowego magazynu kopii zapasowych.
- **Przywracanie z długoterminowej kopii zapasowej**  -  [Przywracanie bazy danych z określonej długoterminowej kopii zapasowej](long-term-retention-overview.md) pojedynczej bazy danych lub bazy danych w puli, jeśli baza danych została skonfigurowana z użyciem długoterminowych zasad przechowywania. LTR umożliwia przywrócenie starej wersji bazy danych za pomocą [Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) lub [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) w celu spełnienia żądania zgodności lub w celu uruchomienia starej wersji aplikacji. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](long-term-retention-overview.md).

Aby wykonać przywracanie, zobacz [przywracanie bazy danych z kopii zapasowych](recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure Storage termin *replikacja* dotyczy kopiowania obiektów blob z jednej lokalizacji do innej. W programie SQL *Replikacja bazy danych* odnosi się do różnych technologii służących do utrzymywania synchronizacji wielu pomocniczych baz danych z podstawową bazą danych.

Operacje konfiguracji i przywracania kopii zapasowej można wypróbować przy użyciu następujących przykładów:

| Operacja | Azure Portal | Azure PowerShell |
|---|---|---|
| **Zmień przechowywanie kopii zapasowych** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Wystąpienie zarządzane SQL](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Wystąpienie zarządzane SQL](/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **Zmiana długoterminowego przechowywania kopii zapasowych** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Wystąpienie zarządzane SQL — nie dotyczy  | [SQL Database](long-term-backup-retention-configure.md)<br/>[Wystąpienie zarządzane SQL](../managed-instance/long-term-backup-retention-configure.md)  |
| **Przywracanie bazy danych od punktu w czasie** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[Wystąpienie zarządzane SQL](../managed-instance/point-in-time-restore.md) | [SQL Database](/powershell/module/az.sql/restore-azsqldatabase) <br/> [Wystąpienie zarządzane SQL](/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **Przywracanie usuniętej bazy danych** | [SQL Database](recovery-using-backups.md)<br>[Wystąpienie zarządzane SQL](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Wystąpienie zarządzane SQL](/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Przywracanie bazy danych z usługi Azure Blob Storage** | SQL Database-N/A <br/>Wystąpienie zarządzane SQL — nie dotyczy  | SQL Database-N/A <br/>[Wystąpienie zarządzane SQL](../managed-instance/restore-sample-database-quickstart.md) |

## <a name="backup-scheduling"></a>Planowanie kopii zapasowych

Pierwsza pełna kopia zapasowa jest zaplanowana natychmiast po utworzeniu lub przywróceniu nowej bazy danych. Ta kopia zapasowa zwykle kończy się w ciągu 30 minut, ale może trwać dłużej, gdy baza danych jest duża. Na przykład początkowa kopia zapasowa może trwać dłużej na przywróconej bazie danych lub kopii bazy danych, która zwykle jest większa niż nowa baza danych. Po wykonaniu pierwszej pełnej kopii zapasowej wszystkie dalsze kopie zapasowe są planowane i zarządzane automatycznie. Dokładny chronometraż wszystkich kopii zapasowych bazy danych jest określany przez SQL Database lub usługę wystąpienia zarządzanego SQL, ponieważ równoważy całkowite obciążenie systemu. Nie można zmienić harmonogramu zadań tworzenia kopii zapasowej ani ich wyłączyć.

> [!IMPORTANT]
> W przypadku nowej, przywróconej lub skopiowanej bazy danych Funkcja przywracania do punktu w czasie zostanie udostępniona od momentu utworzenia początkowej kopii zapasowej dziennika transakcji, która następuje po początkowej pełnej kopii zapasowej.

## <a name="backup-storage-consumption"></a>Użycie magazynu kopii zapasowych

W przypadku SQL Server technologii tworzenia kopii zapasowych i przywracania bazy danych do punktu w czasie wymaga nieprzerwanego łańcucha kopii zapasowych składającego się z jednej pełnej kopii zapasowej, opcjonalnie jednej różnicowej kopii zapasowej i co najmniej jednej kopii zapasowej dziennika transakcji. Harmonogram tworzenia kopii zapasowych SQL Database i wystąpienia zarządzanego SQL zawiera jedną pełną kopię zapasową co tydzień. W związku z tym, aby włączyć kopie w całym okresie przechowywania, system musi przechowywać dodatkowe kopie zapasowe, różnicowe i transakcyjne dziennika transakcji przez maksymalnie tydzień dłuższy niż skonfigurowany okres przechowywania. 

Innymi słowy, w dowolnym momencie w okresie przechowywania, musi istnieć pełna kopia zapasowa, która jest starsza niż najstarsza godzina okresu przechowywania, a także nieprzerwany łańcuch różnic i kopii zapasowych dziennika transakcji z tej pełnej kopii zapasowej aż do następnej pełnej kopii zapasowej.

> [!NOTE]
> Aby włączyć kopie, dodatkowe kopie zapasowe są przechowywane przez maksymalnie tydzień dłużej niż skonfigurowany okres przechowywania. Magazyn kopii zapasowych jest naliczany według stawki za wszystkie kopie zapasowe. 

Kopie zapasowe, które nie są już potrzebne do udostępnienia funkcji kopie, są automatycznie usuwane. Ponieważ różnicowe kopie zapasowe i kopie zapasowe dzienników wymagają wcześniejszej pełnej kopii zapasowej do dostępnych, wszystkie trzy typy kopii zapasowych są czyszczone w zestawach tygodniowych.

Dla wszystkich baz danych, w tym [TDE szyfrowanych](transparent-data-encryption-tde-overview.md) baz danych, kopie zapasowe są kompresowane w celu zmniejszenia kompresji i kosztów magazynu kopii zapasowych. Średni współczynnik kompresji kopii zapasowej to 3-4 razy, ale może być znacznie mniejszy lub większy w zależności od rodzaju danych i tego, czy kompresja danych jest używana w bazie danych.

SQL Database i wystąpienie zarządzane SQL oblicza łączny używany magazyn kopii zapasowych jako wartość zbiorczą. Co godzinę ta wartość jest raportowana w potoku rozliczeń platformy Azure, który jest odpowiedzialny za agregowanie tego użycia godzinowego w celu obliczenia zużycia na koniec każdego miesiąca. Po usunięciu bazy danych zużycie zmniejsza się w miarę starzenia się kopii zapasowych i jest usuwana. Gdy wszystkie kopie zapasowe zostaną usunięte, a kopie nie jest już możliwe, rozliczenia zostaną zatrzymane.
   
> [!IMPORTANT]
> Kopie zapasowe bazy danych są zachowywane w celu włączenia kopie nawet wtedy, gdy baza danych została usunięta. Usunięcie i ponowne utworzenie bazy danych może spowodować oszczędność magazynu i kosztów obliczeniowych. może to spowodować zwiększenie kosztów magazynu kopii zapasowych, ponieważ usługa zachowuje kopie zapasowe dla każdej usuniętej bazy danych, za każdym razem, gdy zostanie usunięta. 

### <a name="monitor-consumption"></a>Monitorowanie użycia

W przypadku baz danych rdzeń wirtualny magazyn używany przez każdy typ kopii zapasowej (pełna, różnicowa i log) jest raportowany w bloku monitorowanie bazy danych jako oddzielną metrykę. Na poniższym diagramie przedstawiono sposób monitorowania użycia magazynu kopii zapasowej dla pojedynczej bazy danych. Ta funkcja jest obecnie niedostępna dla wystąpień zarządzanych.

![Monitoruj użycie kopii zapasowej bazy danych w Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Dostosuj użycie magazynu kopii zapasowych

Użycie magazynu kopii zapasowej do maksymalnego rozmiaru danych dla bazy danych nie jest rozliczane. Nadmierne użycie magazynu kopii zapasowych będzie zależeć od obciążenia i maksymalnego rozmiaru poszczególnych baz danych. Należy wziąć pod uwagę niektóre spośród następujących technik dostrajania, aby zmniejszyć wykorzystanie magazynu kopii zapasowych:

- Skrócenie [okresu przechowywania kopii zapasowej](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) do minimum możliwego dla Twoich potrzeb.
- Unikaj wykonywania dużych operacji zapisu, takich jak rekompilacje indeksów, częściej niż jest to konieczne.
- W przypadku dużych operacji ładowania danych należy rozważyć użycie [klastrowanych indeksów magazynu kolumn](/sql/relational-databases/indexes/columnstore-indexes-overview) oraz pokrewnych [najlepszych](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)rozwiązań i/lub zmniejszenie liczby indeksów nieklastrowanych.
- W Ogólnego przeznaczenia warstwie usług zainicjowany magazyn danych jest tańszy niż cena magazynu kopii zapasowych. Jeśli masz ciągle wysokie koszty magazynowania kopii zapasowych, możesz rozważyć zwiększenie ilości miejsca do magazynowania danych w magazynie kopii zapasowych.
- Należy używać bazy danych TempDB zamiast trwałych tabel w logice aplikacji do przechowywania tymczasowych wyników i/lub danych przejściowych.
- Użyj lokalnie nadmiarowego magazynu kopii zapasowych zawsze wtedy, gdy jest to możliwe (na przykład środowiska deweloperskie/testowe)

## <a name="backup-retention"></a>Przechowywanie kopii zapasowej

W przypadku wszystkich nowych, przywróconych i kopiowanych baz danych Azure SQL Database i wystąpienie zarządzane SQL Azure zachowują wystarczające kopie zapasowe, aby domyślnie zezwolić na kopie w ciągu ostatnich 7 dni. Z wyjątkiem baz danych ze skalowaniem można [zmienić okres przechowywania kopii zapasowej](#change-the-pitr-backup-retention-period) dla każdej aktywnej bazy danych w 1-35 dzień. Zgodnie z opisem w temacie [użycie magazynu kopii zapasowych](#backup-storage-consumption), kopie zapasowe przechowywane w celu włączenia kopie mogą być starsze niż okres przechowywania. Tylko w przypadku wystąpienia zarządzanego Azure SQL można ustawić szybkość przechowywania kopii zapasowych kopie po usunięciu bazy danych w zakresie 0-35 dni. 

W przypadku usunięcia bazy danych System przechowuje kopie zapasowe w taki sam sposób jak w przypadku bazy danych w trybie online z określonym okresem przechowywania. Nie można zmienić okresu przechowywania kopii zapasowej dla usuniętej bazy danych.

> [!IMPORTANT]
> Usunięcie serwera lub wystąpienia zarządzanego spowoduje również usunięcie wszystkich baz danych na tym serwerze lub w zarządzanym wystąpieniu i nie będzie możliwe ich odzyskanie. Nie można przywrócić usuniętego serwera lub wystąpienia zarządzanego. Ale jeśli skonfigurowano długoterminowe przechowywanie (LTR) dla bazy danych lub wystąpienia zarządzanego, kopie zapasowe przechowywania długoterminowego nie są usuwane i mogą być używane do przywracania baz danych na innym serwerze lub wystąpieniu zarządzanym w ramach tej samej subskrypcji, do punktu w czasie, w którym wykonano długoterminowe kopie zapasowe.

Przechowywanie kopii zapasowych dla celów kopie w ciągu ostatnich 1-35 dni jest czasami nazywane krótkoterminowym przechowywaniem kopii zapasowych. Jeśli chcesz zachować kopie zapasowe dłużej niż maksymalny okres krótkoterminowy, 35 dni, możesz włączyć [długoterminowe przechowywanie](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Długoterminowe przechowywanie

W przypadku wystąpienia zarządzanego zarówno SQL Database, jak i SQL można skonfigurować pełną kopię zapasową długoterminowego przechowywania (LTR) przez maksymalnie 10 lat w usłudze Azure Blob Storage. Po skonfigurowaniu zasad "LTR" pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu co tydzień. Aby spełnić różne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Użycie magazynu zależy od wybranej częstotliwości i okresów przechowywania kopii zapasowych LTR. Możesz użyć [kalkulatora cen ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) , aby oszacować koszt magazynu ltr.

> [!IMPORTANT]
> Aktualizacja nadmiarowości magazynu kopii zapasowej dla istniejącej Azure SQL Database ma zastosowanie tylko do przyszłych kopii zapasowych wykonanych dla bazy danych. Wszystkie istniejące kopie zapasowe w bazie danych LTR będą nadal znajdować się w istniejącym obiekcie blob magazynu, a nowe kopie zapasowe będą przechowywane w żądanym typie obiektu blob magazynu. 

Aby uzyskać więcej informacji na temat LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md).

## <a name="storage-costs"></a>Koszty magazynowania

Cena magazynu kopii zapasowych jest różna i zależy od modelu zakupu (DTU lub rdzeń wirtualny), wybranej opcji nadmiarowości magazynu kopii zapasowych, a także w Twoim regionie. W magazynie kopii zapasowych jest naliczana opłata za GB/miesiąc zużyte. Cennik można znaleźć na stronie [Azure SQL Database cennika](https://azure.microsoft.com/pricing/details/sql-database/single/) oraz na stronie [cennika wystąpienia zarządzanego usługi Azure SQL](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) .

### <a name="dtu-model"></a>Model jednostki DTU

W modelu DTU nie ma dodatkowej opłaty za magazyn kopii zapasowych baz danych i pul elastycznych. Cena magazynu kopii zapasowych jest częścią bazy danych lub ceny puli.

### <a name="vcore-model"></a>Model rdzenia wirtualnego

W przypadku pojedynczych baz danych w SQL Database kwota magazynu kopii zapasowej równa 100% maksymalnego rozmiaru magazynu danych dla bazy danych jest zapewniana bez dodatkowych opłat. W przypadku pul elastycznych i wystąpień zarządzanych ilość miejsca do magazynowania kopii zapasowych równa 100% maksymalnego magazynu danych dla puli lub maksymalnego rozmiaru magazynu wystąpień jest dostępna bez dodatkowych opłat. 

W przypadku pojedynczych baz danych to równanie służy do obliczania łącznego użycia magazynu kopii zapasowej do rozliczenia:

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

W przypadku baz danych w puli łączny rozmiar magazynu kopii zapasowej do rozliczenia jest agregowany na poziomie puli i obliczany w następujący sposób:

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

W przypadku wystąpień zarządzanych Łączna ilość magazynowanych magazynów kopii zapasowych jest agregowana na poziomie wystąpienia i jest obliczana w następujący sposób:

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

Łączna ilość rozliczanych magazynów kopii zapasowych (jeśli istnieje) będzie naliczana w GB/miesiąc zgodnie z stawką używanej nadmiarowości magazynu kopii zapasowych. To użycie magazynu kopii zapasowych będzie zależeć od obciążenia i rozmiaru poszczególnych baz danych, pul elastycznych i wystąpień zarządzanych. Wielokrotnie modyfikowane bazy danych mają większe różnice i kopie zapasowe dzienników, ponieważ rozmiar tych kopii zapasowych jest proporcjonalny do ilości zmian danych. W związku z tym te bazy danych będą miały większe opłaty za tworzenie kopii zapasowych.

SQL Database i wystąpienie zarządzane SQL oblicza łączny magazyn kopii zapasowych rozliczany jako wartość zbiorczą dla wszystkich plików kopii zapasowej. Co godzinę ta wartość jest raportowana w potoku rozliczania na platformie Azure, która agreguje to użycie godzinowe w celu uzyskania zużycia magazynu kopii zapasowych na koniec każdego miesiąca. Jeśli baza danych zostanie usunięta, użycie magazynu kopii zapasowych stopniowo zmniejszy się w miarę jak starsze kopie zapasowe zostaną usunięte. Ponieważ różnicowe kopie zapasowe i kopie zapasowe dzienników wymagają wcześniejszej pełnej kopii zapasowej do dostępnych, wszystkie trzy typy kopii zapasowych są czyszczone w zestawach tygodniowych. Po usunięciu wszystkich kopii zapasowych zostaną zatrzymane rozliczenia. 

Jako uproszczony przykład załóżmy, że baza danych ma 744 GB miejsca w magazynie kopii zapasowych i że ta kwota pozostaje stała przez cały miesiąc, ponieważ baza danych jest całkowicie bezczynna. Aby dokonać konwersji zbiorczego użycia magazynu na użycie godzinowe, Podziel go o 744,0 (31 dni miesięcznie * 24 godziny dziennie). SQL Database wyśle raport do potoku rozliczeń platformy Azure, który baza danych wykorzysta 1 GB kopie kopii zapasowej co godzinę, przy stałej szybkości. Rozliczenia platformy Azure będą agregowane w tym zużyciu i wykorzystano 744 GB przez cały miesiąc. Koszt będzie naliczany na podstawie stawki za ilość/GB/miesiąc w Twoim regionie.

Teraz bardziej skomplikowany przykład. Załóżmy, że ta sama bezczynna baza danych została zwiększona o od 7 do 14 dni w środku miesiąca. Spowoduje to zwiększenie całkowitej ilości miejsca w magazynie kopii zapasowych podwajania do 1 488 GB. SQL Database mógłby zgłosić 1 GB użycia w godzinach od 1 do 372 (pierwsza połowa miesiąca). Może zgłosić użycie jako 2 GB dla godzin 373 do 744 (druga połowa miesiąca). To użycie będzie agregowane do ostatecznego rachunku 1 116 GB/miesiąc.

Rzeczywiste Scenariusze rozliczania kopii zapasowych są bardziej skomplikowane. Ze względu na to, że częstotliwość zmian w bazie danych zależy od obciążenia i jest zmienna w czasie, rozmiar poszczególnych różnic i kopii zapasowych dziennika będzie również różny, co oznacza, że zużycie godzinowe magazynu kopii zapasowych jest odpowiednio zmieniane. Ponadto każda różnicowa kopia zapasowa zawiera wszystkie zmiany dokonane w bazie danych od momentu ostatniej pełnej kopii zapasowej, w związku z czym łączny rozmiar wszystkich różnicowych kopii zapasowych stopniowo rośnie w ciągu tygodnia, a następnie gwałtownie maleje po starszym zestawie pełnych, różnicowych i dzienników kopii zapasowych. Na przykład jeśli duże działanie zapisu, takie jak ponowne kompilowanie indeksu, zostało uruchomione zaraz po zakończeniu pełnej kopii zapasowej, modyfikacje wprowadzone w ramach odbudowywania indeksu zostaną uwzględnione w kopiach zapasowych dziennika transakcji wykonanych w czasie trwania odbudowy, w następnej różnicowej kopii zapasowej i w każdej różnicowej kopii zapasowej wykonanej do następnej pełnej kopii zapasowej. W tym ostatnim scenariuszu w większych bazach danych optymalizacja w usłudze tworzy pełną kopię zapasową zamiast różnicowej kopii zapasowej, jeśli różnicowe kopie zapasowe byłyby nadmiernie duże w inny sposób. Pozwala to zmniejszyć rozmiar wszystkich różnicowych kopii zapasowych do następującej pełnej kopii zapasowej.

Można monitorować całkowite użycie magazynu kopii zapasowych dla każdego typu kopii zapasowej (pełny, różnicowa, dziennik transakcji) w czasie, zgodnie z opisem w temacie [użycie monitorowania](#monitor-consumption).

### <a name="backup-storage-redundancy"></a>Nadmiarowość magazynu kopii zapasowych

Nadmiarowość magazynu kopii zapasowych ma wpływ na koszty tworzenia kopii zapasowych w następujący sposób:
- LRS cena = x
- Cena ZRS = 1,25 x
- RA-GRS Price = 2x

Aby uzyskać więcej informacji na temat cennika usługi Backup Storage, Azure SQL Database odwiedź stronę [cennika](https://azure.microsoft.com/pricing/details/sql-database/single/) i [Cennik wystąpienia zarządzanego usługi Azure SQL](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

> [!IMPORTANT]
> Konfigurowalna nadmiarowość magazynu kopii zapasowych dla wystąpienia zarządzanego SQL jest dostępna we wszystkich regionach świadczenia usługi Azure i jest obecnie dostępna w regionie "Azja Południowo-Wschodnia" tylko dla SQL Database. W przypadku wystąpienia zarządzanego można je określić tylko podczas procesu tworzenia wystąpienia zarządzanego. Po zainicjowaniu obsługi administracyjnej zasobu nie można zmienić opcji nadmiarowości magazynu kopii zapasowej.

### <a name="monitor-costs"></a>Monitorowanie kosztów

Aby poznać koszty magazynu kopii zapasowych, przejdź do pozycji **Cost Management + rozliczenia** w Azure Portal, wybierz pozycję **Cost Management** , a następnie wybierz pozycję **Analiza kosztów** . Wybierz żądaną subskrypcję jako **zakres** , a następnie odfiltruj dla odpowiedniego okresu i usługi.

Dodaj filtr dla **nazwy usługi** , a następnie na liście rozwijanej wybierz pozycję **baza danych SQL** . Użyj filtru **podkategorii mierników** , aby wybrać licznik rozliczeń dla usługi. W przypadku pojedynczej bazy danych lub elastycznej puli baz danych wybierz **jedną/elastyczną pulę kopie magazynu kopii zapasowych** . W przypadku wystąpienia zarządzanego wybierz pozycję **mi kopie Backup Storage** . Podkategorie **magazynu** i **obliczeń** mogą być również przydatne, ale nie są one skojarzone z kosztami magazynu kopii zapasowych.

![Analiza kosztów magazynu kopii zapasowych](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > Liczniki są widoczne tylko dla liczników, które są obecnie używane. Jeśli licznik nie jest dostępny, prawdopodobnie Kategoria nie jest obecnie używana. Na przykład liczniki wystąpienia zarządzanego nie będą obecne dla klientów, którzy nie mają wdrożonego wystąpienia zarządzanego. Podobnie liczniki magazynu nie będą widoczne dla zasobów, które nie zużywają magazynu. 

## <a name="encrypted-backups"></a>Szyfrowane kopie zapasowe

Jeśli baza danych jest zaszyfrowana przy użyciu programu TDE, kopie zapasowe są automatycznie szyfrowane w stanie spoczynku, łącznie z kopiami zapasowymi LTR. Wszystkie nowe bazy danych w usłudze Azure SQL są domyślnie skonfigurowane z włączoną funkcją TDE. Aby uzyskać więcej informacji na temat TDE, zobacz  [transparent Data Encryption z SQL Database & wystąpienie zarządzane SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integralność kopii zapasowych

Na bieżąco Zespół inżynieryjny usługi Azure SQL automatycznie testuje przywracanie zautomatyzowanych kopii zapasowych bazy danych. (To testowanie nie jest obecnie dostępne w wystąpieniu zarządzanym SQL). W przypadku przywracania do punktu w czasie bazy danych również są odbierane testy integralności DBCC CHECKDB.

Wszelkie problemy znalezione podczas kontroli integralności spowodują powstanie alertu dla zespołu inżynieryjnego. Aby uzyskać więcej informacji, zobacz [integralność danych w SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

Wszystkie kopie zapasowe bazy danych są pobierane z opcją sum KONTROLnych, aby zapewnić dodatkową integralność kopii zapasowych.

## <a name="compliance"></a>Zgodność

W przypadku migrowania bazy danych z warstwy usług opartych na protokole DTU do warstwy usług opartych na protokole rdzeń wirtualny zachowywane jest zachowanie kopie, aby upewnić się, że zasady odzyskiwania danych aplikacji nie zostały naruszone. Jeśli domyślne przechowywanie nie spełnia wymagań dotyczących zgodności, można zmienić okres przechowywania kopie. Aby uzyskać więcej informacji, zobacz [Zmienianie okresu przechowywania kopii zapasowych kopie](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Zmienianie okresu przechowywania kopii zapasowej kopie

Domyślny okres przechowywania kopii zapasowej kopie można zmienić przy użyciu Azure Portal, programu PowerShell lub interfejsu API REST. W poniższych przykładach pokazano, jak zmienić przechowywanie kopie na 28 dni.

> [!WARNING]
> W przypadku skrócenia bieżącego okresu przechowywania utracisz możliwość przywrócenia punktów w czasie starszym niż nowy okres przechowywania. Kopie zapasowe, które nie są już potrzebne do zapewnienia kopie w nowym okresie przechowywania, są usuwane. W przypadku zwiększenia bieżącego okresu przechowywania nie można natychmiast uzyskać możliwości przywrócenia do starszych punktów w czasie w ramach nowego okresu przechowywania. W miarę upływu czasu system zachowuje kopie zapasowe dłużej.

> [!NOTE]
> Te interfejsy API wpłyną tylko na kopie okres przechowywania. Jeśli skonfigurowano opcję LTR dla bazy danych, nie będzie to miało wpływu. Aby uzyskać informacje o sposobie zmiany okresów przechowywania LTR, zobacz [długoterminowe przechowywanie](long-term-retention-overview.md)danych.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Zmień okres przechowywania kopii zapasowej kopie przy użyciu Azure Portal

Aby zmienić kopie okres przechowywania kopii zapasowych dla aktywnych baz danych przy użyciu Azure Portal, przejdź do serwera lub wystąpienia zarządzanego z bazami danych, których okres przechowywania chcesz zmienić. 

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Zmiany w kopie przechowywanie kopii zapasowych dla SQL Database są wykonywane na stronie serwera w portalu. Aby zmienić przechowywanie kopie dla baz danych na serwerze, przejdź do bloku przegląd serwera. Wybierz pozycję **Zarządzaj kopiami zapasowymi** w lewym okienku, wybierz bazy danych w zakresie zmiany, a następnie wybierz pozycję **Konfiguruj przechowywanie** w górnej części ekranu:

![Zmień kopie przechowywania, poziom serwera](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[Wystąpienie zarządzane SQL](#tab/managed-instance)

Zmiany w kopie przechowywanie kopii zapasowych dla wystąpienia zarządzanego SQL są wykonywane na poziomie poszczególnych baz danych. Aby zmienić kopie przechowywanie kopii zapasowych dla bazy danych wystąpienia z Azure Portal, przejdź do bloku przegląd poszczególnych baz danych. Następnie wybierz pozycję **Konfiguruj przechowywanie kopii zapasowych** w górnej części ekranu:

![Zmień kopie przechowywania, wystąpienie zarządzane](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Zmienianie okresu przechowywania kopii zapasowych kopie przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł AzureRM programu PowerShell jest nadal obsługiwany przez wystąpienia zarządzane SQL Database i SQL, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Aby uzyskać więcej informacji, zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module są zasadniczo identyczne z tymi w modułach AzureRm.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Aby zmienić kopie przechowywanie kopii zapasowych dla aktywnych baz danych usługi Azure SQL, użyj następującego przykładu programu PowerShell.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[Wystąpienie zarządzane SQL](#tab/managed-instance)

Aby zmienić kopie przechowywanie kopii zapasowych dla **indywidualnych aktywnych** baz danych wystąpienia zarządzanego SQL, użyj następującego przykładu programu PowerShell.

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

Aby zmienić kopie przechowywanie kopii zapasowych dla **wszystkich aktywnych** baz danych wystąpienia zarządzanego SQL, użyj poniższego przykładu programu PowerShell.

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

Aby zmienić kopie przechowywanie kopii zapasowej dla **pojedynczej usuniętej** bazy danych wystąpienia zarządzanego SQL, użyj następującego przykładu programu PowerShell.
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Aby zmienić kopie przechowywanie kopii zapasowych dla **wszystkich usuniętych** baz danych wystąpienia zarządzanego SQL, użyj następującego przykładu programu PowerShell.

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

Zero (0) dni przechowywania spowoduje, że kopia zapasowa zostanie natychmiast usunięta i nie będzie już przechowywana dla usuniętej bazy danych.
Gdy kopie przechowywanie kopii zapasowych zostało zmniejszone dla usuniętej bazy danych, nie można już jej zwiększyć.

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Zmienianie okresu przechowywania kopii zapasowych kopie przy użyciu interfejsu API REST

#### <a name="sample-request"></a>Przykładowe żądanie

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Treść żądania

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Przykładowa odpowiedź

Kod stanu: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Aby uzyskać więcej informacji, zobacz [interfejs API REST przechowywania kopii zapasowych](/rest/api/sql/backupshorttermretentionpolicies).

#### <a name="sample-request"></a>Przykładowe żądanie

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Treść żądania

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Przykładowa odpowiedź

Kod stanu: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Aby uzyskać więcej informacji, zobacz [interfejs API REST przechowywania kopii zapasowych](/rest/api/sql/backupshorttermretentionpolicies).

## <a name="configure-backup-storage-redundancy"></a>Konfigurowanie nadmiarowości magazynu kopii zapasowych

> [!NOTE]
> Konfigurowalną nadmiarowość magazynu dla kopii zapasowych wystąpienia zarządzanego SQL można określić tylko podczas procesu tworzenia wystąpienia zarządzanego. Po zainicjowaniu obsługi administracyjnej zasobu nie można zmienić opcji nadmiarowości magazynu kopii zapasowej. W przypadku SQL Database publiczna wersja zapoznawcza tej funkcji jest obecnie dostępna w Brazylii Południowej i jest ogólnie dostępna w regionie na platformie Azure Południowo-Wschodnia. 

Nadmiarowość magazynu kopii zapasowych wystąpienia zarządzanego można ustawić tylko podczas tworzenia wystąpienia. SQL Database można ustawić podczas tworzenia bazy danych lub można ją zaktualizować dla istniejącej bazy danych. Wartość domyślna to magazyn Geograficznie nadmiarowy (RA-GRS). W przypadku różnic cen między lokalnie nadmiarowy (LRS), strefowo nadmiarowy (ZRS) i geograficznie nadmiarowy (RA-GRS) magazyn kopii zapasowych odwiedź [stronę z cennikiem wystąpienia zarządzanego](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Konfigurowanie nadmiarowości magazynu kopii zapasowych przy użyciu Azure Portal

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

W Azure Portal można skonfigurować nadmiarowość magazynu kopii zapasowych w bloku **tworzenie SQL Database** . Ta opcja jest dostępna w sekcji nadmiarowość magazynu kopii zapasowych. 
![Otwórz blok tworzenie SQL Database](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[Wystąpienie zarządzane SQL](#tab/managed-instance)

W Azure Portal opcja zmiany nadmiarowości magazynu kopii zapasowych znajduje się w bloku **obliczenia + magazyn** dostępnego z poziomu opcji **Konfiguruj wystąpienie zarządzane** na karcie **podstawowe** podczas tworzenia wystąpienia zarządzanego SQL.
![Otwórz przystawkę Konfiguracja obliczeń i magazynu — blok](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

Znajdź opcję wyboru opcji nadmiarowości magazynu kopii zapasowych w bloku **obliczenia + magazyn** .
![Konfigurowanie nadmiarowości magazynu kopii zapasowych](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>Konfigurowanie nadmiarowości magazynu kopii zapasowych za pomocą programu PowerShell

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Aby skonfigurować nadmiarowość magazynu kopii zapasowych podczas tworzenia nowej bazy danych, można określić parametr-BackupStoageRedundancy. Możliwe wartości to geograficzne, strefy i lokalne. Domyślnie wszystkie bazy danych SQL używają geograficznie nadmiarowego magazynu dla kopii zapasowych. Przywracanie geograficzne jest wyłączone, jeśli baza danych została utworzona przy użyciu magazynu kopii zapasowej lokalnego lub nadmiarowego. 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

Aby uzyskać szczegółowe informacje, odwiedź stronę [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).

Aby zaktualizować nadmiarowość magazynu kopii zapasowej istniejącej bazy danych, można użyć parametru-BackupStorageRedundancy. Możliwe wartości to geograficzne, strefy i lokalne.
Należy pamiętać, że zastosowanie zmian w bazie danych może potrwać do 48 godzin. Przełączenie z magazynu kopii zapasowej nadmiarowej geograficznie do magazynu lokalnego lub Strefowo nadmiarowego wyłącza przywracanie geograficzne. 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

Aby uzyskać szczegółowe informacje, zobacz [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)

> [!NOTE]
> Aby użyć parametru-BackupStorageRedundancy w przypadku przywracania bazy danych, kopiowania bazy danych lub tworzenia operacji dodatkowych, użyj Azure PowerShell w wersji AZ. SQL 2.11.0. 


#### <a name="sql-managed-instance"></a>[Wystąpienie zarządzane SQL](#tab/managed-instance)

Aby skonfigurować nadmiarowość magazynu kopii zapasowych podczas tworzenia wystąpienia zarządzanego, można określić parametr-BackupStoageRedundancy. Możliwe wartości to geograficzne, strefy i lokalne.

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

Aby uzyskać więcej informacji, zapoznaj się z artykułem [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance).

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>Użyj Azure Policy, aby wymusić nadmiarowość magazynu kopii zapasowych

Jeśli masz wymagania dotyczące danych miejsca zamieszkania, które wymagają zachowania wszystkich danych w jednym regionie świadczenia usługi Azure, możesz chcieć wymusić wykonywanie kopii zapasowej strefowo nadmiarowe lub lokalnie nadmiarowe dla SQL Database lub wystąpienia zarządzanego przy użyciu Azure Policy. Azure Policy to usługa, za pomocą której można tworzyć i przypisywać zasady stosujące reguły do zasobów platformy Azure oraz zarządzać nimi. Azure Policy pomaga zachować zgodność tych zasobów ze standardami firmy i umowami dotyczącymi poziomu usług. Aby uzyskać więcej informacji, zobacz [omówienie Azure Policy](../../governance/policy/overview.md). 

### <a name="built-in-backup-storage-redundancy-policies"></a>Wbudowane zasady nadmiarowości magazynu kopii zapasowych 

Dodawane są następujące nowe zasady wbudowane, które mogą być przypisywane na poziomie subskrypcji lub grupy zasobów w celu blokowania tworzenia nowych baz danych lub wystąpień z geograficznie nadmiarowym magazynem kopii zapasowych. 

[SQL Database należy unikać używania nadmiarowości kopii zapasowej GRS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[Wystąpienia zarządzane SQL powinny unikać używania nadmiarowości kopii zapasowej GRS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

Pełną listę wbudowanych definicji zasad dla SQL Database i wystąpienia zarządzanego można znaleźć [tutaj](./policy-reference.md).

Aby wymusić wymagania dotyczące miejsca zamieszkania danych na poziomie organizacji, te zasady można przypisać do subskrypcji. Po przypisaniu tych użytkowników na poziomie subskrypcji użytkownicy w danej subskrypcji nie będą mogli utworzyć bazy danych ani wystąpienia zarządzanego z magazynem kopii zapasowych nadmiarowym geograficznie za pośrednictwem Azure Portal lub Azure PowerShell. 

> [!IMPORTANT]
> Zasady platformy Azure nie są wymuszane podczas tworzenia bazy danych przy użyciu języka T-SQL. Aby wymusić zamiejscowe dane podczas tworzenia bazy danych przy użyciu języka T-SQL, [należy użyć elementu "Local" lub "Zone" jako danych wejściowych do BACKUP_STORAGE_REDUNDANCY parametr w instrukcji CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#create-database-using-zone-redundancy-for-backups).

Dowiedz się, jak przypisywać zasady przy użyciu [Azure Portal](../../governance/policy/assign-policy-portal.md) lub [Azure PowerShell](../../governance/policy/assign-policy-powershell.md)


## <a name="next-steps"></a>Następne kroki

- Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Aby dowiedzieć się więcej na temat innych SQL Database rozwiązań z zakresu ciągłości biznesowej, zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Uzyskaj więcej informacji na temat sposobu [przywracania bazy danych do punktu w czasie za pomocą Azure Portal](recovery-using-backups.md).
- Uzyskaj więcej informacji na temat [przywracania bazy danych do punktu w czasie za pomocą programu PowerShell](scripts/restore-database-powershell.md).
- Aby uzyskać informacje dotyczące sposobu konfigurowania, zarządzania i przywracania długoterminowego przechowywania zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage za pomocą Azure Portal, zobacz [Zarządzanie długoterminową przechowywanie kopii zapasowych przy użyciu Azure Portal](long-term-backup-retention-configure.md).
- Aby uzyskać informacje na temat sposobu konfigurowania, zarządzania i przywracania długoterminowego przechowywania zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage za pomocą programu PowerShell, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych za pomocą programu PowerShell](long-term-backup-retention-configure.md).
- Aby dowiedzieć się, jak dostosować przechowywanie i koszty przechowywania kopii zapasowych dla wystąpienia zarządzanego usługi Azure SQL, zobacz [dostrajanie kosztów magazynu kopii zapasowych na wystąpieniu zarządzanym](https://aka.ms/mi-backup-tuning).
