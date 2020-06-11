---
title: Automatyczne nadmiarowe kopie zapasowe
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database i wystąpienie zarządzane SQL Azure automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i korzysta z magazynu geograficznie nadmiarowego platformy Azure do odczytu na potrzeby nadmiarowości geograficznej.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 06/04/2020
ms.openlocfilehash: 41df5190f2a7435ad91de94cb6f407037e1783a2
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84667832"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Zautomatyzowane kopie zapasowe — Azure SQL Database & wystąpienia zarządzane SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>Co to jest kopia zapasowa bazy danych?

Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed uszkodzeniem lub usunięciem.

Zarówno SQL Database, jak i wystąpienie zarządzane SQL używają technologii SQL Server do tworzenia [pełnych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) co tydzień, [różnicowych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) co 12-24 godzin i [kopii zapasowych dziennika transakcji](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) co 5 – 10 minut. Częstotliwość tworzenia kopii zapasowych dziennika transakcji zależy od rozmiaru obliczeń i liczby działań związanych z bazą danych.

Podczas przywracania bazy danych usługa określa, które pełne, różnicowe i transakcyjne kopie zapasowe dziennika muszą zostać przywrócone.

Te kopie zapasowe umożliwiają przywracanie bazy danych do punktu w czasie w skonfigurowanym okresie przechowywania. Kopie zapasowe są przechowywane jako [obiekty blob magazynu RA-GRS](../../storage/common/storage-redundancy.md) , które są replikowane do [sparowanego regionu](../../best-practices-availability-paired-regions.md) w celu ochrony przed awariami magazynu kopii zapasowych w regionie podstawowym. 

Jeśli reguły ochrony danych wymagają, aby kopie zapasowe były dostępne przez dłuższy czas (do 10 lat), można skonfigurować [długoterminowe przechowywanie](long-term-retention-overview.md) zarówno dla baz danych o pojedynczej, jak i w puli.

Tych kopii zapasowych można użyć w następujących celach:

- [Przywracanie istniejącej bazy danych do punktu w czasie w przeszłości w](recovery-using-backups.md#point-in-time-restore) okresie przechowywania przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. W przypadku baz danych z jedną i pulą ta operacja spowoduje utworzenie nowej bazy danych na tym samym serwerze, na którym znajduje się oryginalna baza danych, ale pod inną nazwą, aby uniknąć zastąpienia oryginalnej bazy danych. Po zakończeniu przywracania możesz usunąć oryginalną bazę danych lub [zmienić jej nazwę](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) i zmienić nazwę przywróconej bazy danych tak, aby była oryginalna. W wystąpieniu zarządzanym ta operacja może w podobny sposób utworzyć kopię bazy danych w tym samym lub innym wystąpieniu zarządzanym w ramach tej samej subskrypcji i w tym samym regionie.
- [Przywracanie usuniętej bazy danych do czasu usunięcia](recovery-using-backups.md#deleted-database-restore) lub do dowolnego punktu w czasie w okresie przechowywania. Usuniętą bazę danych można przywrócić tylko na tym samym serwerze lub wystąpieniu zarządzanym, w którym została utworzona oryginalna baza danych. Podczas usuwania bazy danych usługa pobiera ostateczną kopię zapasową dziennika transakcji przed usunięciem, aby zapobiec utracie danych.
- [Przywracanie bazy danych do innego regionu geograficznego](recovery-using-backups.md#geo-restore). Funkcja przywracania geograficznego umożliwia odzyskanie sprawności po awarii geograficznej, gdy nie można uzyskać dostępu do bazy danych lub kopii zapasowych w regionie podstawowym. Tworzy nową bazę danych na dowolnym istniejącym serwerze lub wystąpieniu zarządzanym w dowolnym regionie świadczenia usługi Azure.
- [Przywracanie bazy danych z określonej długoterminowej kopii zapasowej](long-term-retention-overview.md) pojedynczej bazy danych lub bazy danych w puli, jeśli baza danych została skonfigurowana z użyciem długoterminowych zasad przechowywania. LTR umożliwia przywrócenie starej wersji bazy danych za pomocą [Azure Portal](long-term-backup-retention-configure.md#using-the-azure-portal) lub [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) w celu spełnienia żądania zgodności lub w celu uruchomienia starej wersji aplikacji. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](long-term-retention-overview.md).

Aby wykonać przywracanie, zobacz [przywracanie bazy danych z kopii zapasowych](recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure Storage termin *replikacja* dotyczy kopiowania obiektów blob z jednej lokalizacji do innej. W programie SQL *Replikacja bazy danych* odnosi się do różnych technologii służących do utrzymywania synchronizacji wielu pomocniczych baz danych z podstawową bazą danych.

Operacje konfiguracji i przywracania kopii zapasowej można wypróbować przy użyciu następujących przykładów:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Zmień przechowywanie kopii zapasowych | [Pojedyncza baza danych](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Wystąpienie zarządzane](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Pojedyncza baza danych](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Zmiana długoterminowego przechowywania kopii zapasowych | [Pojedyncza baza danych](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Wystąpienie zarządzane — nie dotyczy  | [Pojedyncza baza danych](long-term-backup-retention-configure.md)<br/>Wystąpienie zarządzane — nie dotyczy  |
| Przywracanie bazy danych od punktu w czasie | [Pojedyncza baza danych](recovery-using-backups.md#point-in-time-restore) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Przywracanie usuniętej bazy danych | [Pojedyncza baza danych](recovery-using-backups.md) | [Pojedyncza baza danych](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Wystąpienie zarządzane](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Przywracanie bazy danych z usługi Azure Blob Storage | Pojedyncza baza danych — nie dotyczy <br/>Wystąpienie zarządzane — nie dotyczy  | Pojedyncza baza danych — nie dotyczy <br/>[Wystąpienie zarządzane](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

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
- W przypadku dużych operacji ładowania danych należy rozważyć użycie [klastrowanych indeksów magazynu kolumn](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes) oraz pokrewnych [najlepszych](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)rozwiązań i/lub zmniejszenie liczby indeksów nieklastrowanych.
- W Ogólnego przeznaczenia warstwie usług zainicjowany magazyn danych jest tańszy niż cena magazynu kopii zapasowych. Jeśli masz ciągle wysokie koszty magazynowania kopii zapasowych, możesz rozważyć zwiększenie ilości miejsca do magazynowania danych w magazynie kopii zapasowych.
- Należy używać bazy danych TempDB zamiast trwałych tabel w logice aplikacji do przechowywania tymczasowych wyników i/lub danych przejściowych.

## <a name="backup-retention"></a>Przechowywanie kopii zapasowych

W przypadku wszystkich nowych, przywróconych i kopiowanych baz danych Azure SQL Database i wystąpienie zarządzane SQL Azure zachowują wystarczające kopie zapasowe, aby domyślnie zezwolić na kopie w ciągu ostatnich 7 dni. Z wyjątkiem baz danych ze skalowaniem można [zmienić okres przechowywania kopii zapasowej](#change-the-pitr-backup-retention-period) dla bazy danych w 1-35. Zgodnie z opisem w temacie [użycie magazynu kopii zapasowych](#backup-storage-consumption), kopie zapasowe przechowywane w celu włączenia kopie mogą być starsze niż okres przechowywania.

W przypadku usunięcia bazy danych System przechowuje kopie zapasowe w taki sam sposób jak w przypadku bazy danych w trybie online z określonym okresem przechowywania. Nie można zmienić okresu przechowywania kopii zapasowej dla usuniętej bazy danych.

> [!IMPORTANT]
> Usunięcie serwera lub wystąpienia zarządzanego spowoduje również usunięcie wszystkich baz danych na tym serwerze lub w zarządzanym wystąpieniu i nie będzie możliwe ich odzyskanie. Nie można przywrócić usuniętego serwera lub wystąpienia zarządzanego. Ale jeśli skonfigurowano długoterminowe przechowywanie (LTR) dla bazy danych lub wystąpienia zarządzanego, kopie zapasowe przechowywania długoterminowego nie są usuwane i mogą być używane do przywracania baz danych na innym serwerze lub wystąpieniu zarządzanym w ramach tej samej subskrypcji, do punktu w czasie, w którym wykonano długoterminowe kopie zapasowe.

Przechowywanie kopii zapasowych dla celów kopie w ciągu ostatnich 1-35 dni jest czasami nazywane krótkoterminowym przechowywaniem kopii zapasowych. Jeśli chcesz zachować kopie zapasowe dłużej niż maksymalny okres krótkoterminowy, 35 dni, możesz włączyć [długoterminowe przechowywanie](long-term-retention-overview.md).

### <a name="long-term-retention"></a>Długoterminowe przechowywanie

W przypadku baz danych o pojedynczej i puli i wystąpieniach zarządzanych można skonfigurować długoterminowe przechowywanie (LTR) dla pełnych kopii zapasowych w okresie do 10 lat w usłudze Azure Blob Storage. W przypadku włączenia zasad LTR, cotygodniowe pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu RA-GRS. Aby spełnić różne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Użycie magazynu zależy od wybranej częstotliwości wykonywania kopii zapasowych LTR oraz okresu przechowywania lub okresów. Możesz użyć [kalkulatora cen ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) , aby oszacować koszt magazynu ltr.

Podobnie jak w przypadku kopii zapasowych kopie, kopie zapasowe są chronione za pomocą magazynu geograficznie nadmiarowego. Aby uzyskać więcej informacji, zobacz [Nadmiarowość usługi Azure Storage](../../storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji na temat LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md).

## <a name="storage-costs"></a>Koszty magazynowania

Cena magazynu różni się w zależności od tego, czy używasz modelu DTU czy modelu rdzeń wirtualny.

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

Łączna ilość rozliczanych magazynów kopii zapasowych (jeśli istnieje) będzie naliczana w GB/miesiąc. To użycie magazynu kopii zapasowych będzie zależeć od obciążenia i rozmiaru poszczególnych baz danych, pul elastycznych i wystąpień zarządzanych. Wielokrotnie modyfikowane bazy danych mają większe różnice i kopie zapasowe dzienników, ponieważ rozmiar tych kopii zapasowych jest proporcjonalny do ilości zmian danych. W związku z tym te bazy danych będą miały większe opłaty za tworzenie kopii zapasowych.

SQL Database i wystąpienie zarządzane SQL oblicza łączny magazyn kopii zapasowych rozliczany jako wartość zbiorczą dla wszystkich plików kopii zapasowej. Co godzinę ta wartość jest raportowana w potoku rozliczania na platformie Azure, która agreguje to użycie godzinowe w celu uzyskania zużycia magazynu kopii zapasowych na koniec każdego miesiąca. Jeśli baza danych zostanie usunięta, użycie magazynu kopii zapasowych stopniowo zmniejszy się w miarę jak starsze kopie zapasowe zostaną usunięte. Ponieważ różnicowe kopie zapasowe i kopie zapasowe dzienników wymagają wcześniejszej pełnej kopii zapasowej do dostępnych, wszystkie trzy typy kopii zapasowych są czyszczone w zestawach tygodniowych. Po usunięciu wszystkich kopii zapasowych zostaną zatrzymane rozliczenia. 

Jako uproszczony przykład załóżmy, że baza danych ma 744 GB miejsca w magazynie kopii zapasowych i że ta kwota pozostaje stała przez cały miesiąc, ponieważ baza danych jest całkowicie bezczynna. Aby dokonać konwersji zbiorczego użycia magazynu na użycie godzinowe, Podziel go o 744,0 (31 dni miesięcznie * 24 godziny dziennie). SQL Database wyśle raport do potoku rozliczeń platformy Azure, który baza danych wykorzysta 1 GB kopie kopii zapasowej co godzinę, przy stałej szybkości. Rozliczenia platformy Azure będą agregowane w tym zużyciu i wykorzystano 744 GB przez cały miesiąc. Koszt będzie naliczany na podstawie stawki za ilość/GB/miesiąc w Twoim regionie.

Teraz bardziej skomplikowany przykład. Załóżmy, że ta sama bezczynna baza danych została zwiększona o od 7 do 14 dni w środku miesiąca. Spowoduje to zwiększenie całkowitej ilości miejsca w magazynie kopii zapasowych podwajania do 1 488 GB. SQL Database mógłby zgłosić 1 GB użycia w godzinach od 1 do 372 (pierwsza połowa miesiąca). Może zgłosić użycie jako 2 GB dla godzin 373 do 744 (druga połowa miesiąca). To użycie będzie agregowane do ostatecznego rachunku 1 116 GB/miesiąc.

Rzeczywiste Scenariusze rozliczania kopii zapasowych są bardziej skomplikowane. Ze względu na to, że częstotliwość zmian w bazie danych zależy od obciążenia i jest zmienna w czasie, rozmiar poszczególnych różnic i kopii zapasowych dziennika będzie również różny, co oznacza, że zużycie godzinowe magazynu kopii zapasowych jest odpowiednio zmieniane. Ponadto każda różnicowa kopia zapasowa zawiera wszystkie zmiany dokonane w bazie danych od momentu ostatniej pełnej kopii zapasowej, w związku z czym łączny rozmiar wszystkich różnicowych kopii zapasowych stopniowo rośnie w ciągu tygodnia, a następnie gwałtownie maleje po starszym zestawie pełnych, różnicowych i dzienników kopii zapasowych. Na przykład jeśli duże działanie zapisu, takie jak ponowne kompilowanie indeksu, zostało uruchomione zaraz po zakończeniu pełnej kopii zapasowej, modyfikacje wprowadzone w ramach odbudowywania indeksu zostaną uwzględnione w kopiach zapasowych dziennika transakcji wykonanych w czasie trwania odbudowy, w następnej różnicowej kopii zapasowej i w każdej różnicowej kopii zapasowej wykonanej do następnej pełnej kopii zapasowej. W tym ostatnim scenariuszu w większych bazach danych optymalizacja w usłudze tworzy pełną kopię zapasową zamiast różnicowej kopii zapasowej, jeśli różnicowe kopie zapasowe byłyby nadmiernie duże w inny sposób. Pozwala to zmniejszyć rozmiar wszystkich różnicowych kopii zapasowych do następującej pełnej kopii zapasowej.

Można monitorować całkowite użycie magazynu kopii zapasowych dla każdego typu kopii zapasowej (pełny, różnicowa, dziennik transakcji) w czasie, zgodnie z opisem w temacie [użycie monitorowania](#monitor-consumption).

### <a name="monitor-costs"></a>Monitorowanie kosztów

Aby poznać koszty magazynu kopii zapasowych, przejdź do pozycji **Cost Management + rozliczenia** w Azure Portal, wybierz pozycję **Cost Management**, a następnie wybierz pozycję **Analiza kosztów**. Wybierz żądaną subskrypcję jako **zakres**, a następnie odfiltruj dla odpowiedniego okresu i usługi.

Dodaj filtr dla **nazwy usługi**, a następnie na liście rozwijanej wybierz pozycję **baza danych SQL** . Użyj filtru **podkategorii mierników** , aby wybrać licznik rozliczeń dla usługi. W przypadku pojedynczej bazy danych lub elastycznej puli baz danych wybierz **jedną/elastyczną pulę kopie magazynu kopii zapasowych**. W przypadku wystąpienia zarządzanego wybierz pozycję **mi kopie Backup Storage**. Podkategorie **magazynu** i **obliczeń** mogą być również przydatne, ale nie są one skojarzone z kosztami magazynu kopii zapasowych.

![Analiza kosztów magazynu kopii zapasowych](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="encrypted-backups"></a>Szyfrowane kopie zapasowe

Jeśli baza danych jest zaszyfrowana przy użyciu programu TDE, kopie zapasowe są automatycznie szyfrowane w stanie spoczynku, łącznie z kopiami zapasowymi LTR. Wszystkie nowe bazy danych w usłudze Azure SQL są domyślnie skonfigurowane z włączoną funkcją TDE. Aby uzyskać więcej informacji na temat TDE, zobacz [transparent Data Encryption z SQL Database & wystąpienie zarządzane SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

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

Aby zmienić kopie okres przechowywania kopii zapasowej przy użyciu Azure Portal, przejdź do serwera lub wystąpienia zarządzanego z bazami danych, których okres przechowywania chcesz zmienić. 

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
> Moduł AzureRM programu PowerShell jest nadal obsługiwany przez wystąpienia zarządzane SQL Database i SQL, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Aby uzyskać więcej informacji, zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module są zasadniczo identyczne z tymi w modułach AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

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

Aby uzyskać więcej informacji, zobacz [interfejs API REST przechowywania kopii zapasowych](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Następne kroki

- Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Aby dowiedzieć się więcej na temat innych SQL Database rozwiązań z zakresu ciągłości biznesowej, zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Uzyskaj więcej informacji na temat sposobu [przywracania bazy danych do punktu w czasie za pomocą Azure Portal](recovery-using-backups.md).
- Uzyskaj więcej informacji na temat [przywracania bazy danych do punktu w czasie za pomocą programu PowerShell](scripts/restore-database-powershell.md).
- Aby uzyskać informacje dotyczące sposobu konfigurowania, zarządzania i przywracania długoterminowego przechowywania zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage za pomocą Azure Portal, zobacz [Zarządzanie długoterminową przechowywanie kopii zapasowych przy użyciu Azure Portal](long-term-backup-retention-configure.md).
- Aby uzyskać informacje na temat sposobu konfigurowania, zarządzania i przywracania długoterminowego przechowywania zautomatyzowanych kopii zapasowych w usłudze Azure Blob Storage za pomocą programu PowerShell, zobacz [Zarządzanie długoterminową przechowywaniem kopii zapasowych za pomocą programu PowerShell](long-term-backup-retention-configure.md).
