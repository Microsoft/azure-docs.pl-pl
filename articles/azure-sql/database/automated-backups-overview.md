---
title: Automatyczne nadmiarowe kopie zapasowe
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database i wystąpienie zarządzane SQL Azure automatycznie tworzy kopię zapasową lokalnej bazy danych co kilka minut i korzysta z magazynu geograficznie nadmiarowego platformy Azure do odczytu na potrzeby nadmiarowości geograficznej.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: e450d9ede3b073d2d1a791b341e4376b40919933
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116465"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Zautomatyzowane kopie zapasowe — Azure SQL Database & wystąpienia zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Zarówno Azure SQL Database, jak i wystąpienie zarządzane usługi Azure SQL, tworzą kopie zapasowe bazy danych, które są przechowywane na czas trwania skonfigurowanego okresu przechowywania. Korzystają one z [magazynu geograficznie nadmiarowego (RA-GRS)](../../storage/common/storage-redundancy.md) platformy Azure, aby zapewnić, że kopie zapasowe są zachowywane nawet wtedy, gdy centrum danych jest niedostępne.

Kopie zapasowe bazy danych są istotną częścią strategii ciągłości działania i odzyskiwania po awarii, ponieważ chronią dane przed przypadkowym uszkodzeniem lub usunięciem. Jeśli reguły zabezpieczeń wymagają, aby kopie zapasowe były dostępne przez dłuższy czas (do 10 lat), można skonfigurować [długoterminowe przechowywanie](long-term-retention-overview.md) zarówno dla baz danych o pojedynczej, jak i w puli.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Co to jest SQL Database kopia zapasowa?

Zarówno SQL Database, jak i wystąpienie zarządzane SQL używają technologii SQL Server do tworzenia [pełnych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) co tydzień, [różnicowych kopii zapasowych](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) co 12 godzin i [kopii zapasowych dziennika transakcji](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) co 5 – 10 minut. Kopie zapasowe są przechowywane w obiektach [BLOB usługi RA-GRS](../../storage/common/storage-redundancy.md) , które są replikowane do [sparowanego centrum](../../best-practices-availability-paired-regions.md) danych w celu ochrony przed awarią centrum danych. Podczas przywracania bazy danych usługa określa, które pełne, różnicowe i transakcyjne kopie zapasowe dziennika muszą zostać przywrócone.

Tych kopii zapasowych można użyć w następujących celach:

- **Przywróć istniejącą bazę danych do punktu w czasie w przeszłości w** okresie przechowywania przy użyciu Azure Portal, Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. W przypadku baz danych o pojedynczej i puli ta operacja spowoduje utworzenie nowej bazy danych na tym samym serwerze, na którym znajduje się oryginalna baza danych. W wystąpieniu zarządzanym ta operacja umożliwia utworzenie kopii bazy danych lub tego samego lub innego wystąpienia zarządzanego w ramach tej samej subskrypcji.
- **Przywracanie usuniętej bazy danych do czasu usunięcia** lub w dowolnym momencie w okresie przechowywania. Usuniętą bazę danych można przywrócić tylko na tym samym serwerze lub wystąpieniu zarządzanym, w którym została utworzona oryginalna baza danych.
- **Przywracanie bazy danych do innego regionu geograficznego**. Funkcja przywracania geograficznego umożliwia odzyskanie danych po awarii geograficznej, gdy nie można uzyskać dostępu do serwera i bazy danych. Tworzy nową bazę danych na dowolnym istniejącym serwerze i w dowolnym miejscu na świecie.
- **Przywracanie bazy danych z określonej długoterminowej kopii zapasowej** w pojedynczej bazie danych lub w puli baz danych, jeśli baza danych jest skonfigurowana z użyciem długoterminowych zasad przechowywania. LTR umożliwia przywrócenie starej wersji bazy danych za pomocą [Azure Portal](long-term-backup-retention-configure.md#using-azure-portal) lub [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) w celu spełnienia żądania zgodności lub w celu uruchomienia starej wersji aplikacji. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](long-term-retention-overview.md).

Aby wykonać przywracanie, zobacz [przywracanie bazy danych z kopii zapasowych](recovery-using-backups.md).

> [!NOTE]
> W usłudze Azure Storage termin *replikacja* dotyczy kopiowania plików z jednej lokalizacji do innej. W programie SQL *Replikacja bazy danych* dotyczy zachowania wielu pomocniczych baz danych zsynchronizowanych z podstawową bazą danych.

Niektóre z tych operacji można wypróbować, korzystając z następujących przykładów:

| | Witryna Azure Portal | Azure PowerShell |
|---|---|---|
| Zmień przechowywanie kopii zapasowych | [SQL Database](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Wystąpienie zarządzane SQL](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Wystąpienie zarządzane SQL](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Zmiana długoterminowego przechowywania kopii zapasowych | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Wystąpienie zarządzane SQL — nie dotyczy  | [SQL Database](long-term-backup-retention-configure.md)<br/>Wystąpienie zarządzane SQL — nie dotyczy  |
| Przywracanie bazy danych od punktu w czasie | [SQL Database](recovery-using-backups.md#point-in-time-restore) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Wystąpienie zarządzane SQL](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Przywracanie usuniętej bazy danych | [SQL Database](recovery-using-backups.md) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Wystąpienie zarządzane SQL](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Przywracanie bazy danych z usługi Azure Blob Storage | SQL Database-N/A <br/>Wystąpienie zarządzane SQL — nie dotyczy  | SQL Database-N/A <br/>[Wystąpienie zarządzane SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-frequency"></a>Częstotliwość wykonywania kopii zapasowych

### <a name="point-in-time-restore"></a>Przywracanie do określonego momentu

SQL Database i wystąpienie zarządzane SQL obsługują samoobsługowe przywracanie do punktu w czasie (kopie) przez automatyczne tworzenie pełnych kopii zapasowych, różnicowych kopii zapasowych i kopii zapasowych dziennika transakcji. Tworzone są pełne kopie zapasowe bazy danych, a kopie zapasowe baz danych są zwykle tworzone co 12 godzin. Kopie zapasowe dziennika transakcji są zwykle tworzone co 5 – 10 minut. Częstotliwość tworzenia kopii zapasowych dziennika transakcji zależy od rozmiaru obliczeń i liczby działań związanych z bazą danych.

Pierwsza pełna kopia zapasowa jest zaplanowana natychmiast po utworzeniu bazy danych. Ta kopia zapasowa zwykle kończy się w ciągu 30 minut, ale może trwać dłużej, gdy baza danych jest duża. Na przykład początkowa kopia zapasowa może trwać dłużej w przywróconej bazie danych lub kopii bazy danych. Po utworzeniu pierwszej pełnej kopii zapasowej wszystkie kolejne kopie zapasowe są zaplanowane automatycznie i zarządzane w trybie dyskretnym w tle. Dokładny chronometraż wszystkich kopii zapasowych bazy danych jest określany przez SQL Database i wystąpienie zarządzane SQL, ponieważ równoważy całkowite obciążenie systemu. Nie można zmienić ani wyłączyć zadań tworzenia kopii zapasowej.

### <a name="default-backup-retention-period"></a>Domyślny okres przechowywania kopii zapasowej

Kopie zapasowe kopie są chronione za pomocą magazynu geograficznie nadmiarowego. Aby uzyskać więcej informacji, zobacz [Nadmiarowość usługi Azure Storage](../../storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji na temat kopie, zobacz [przywracanie do punktu w czasie](recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Długoterminowe przechowywanie

W przypadku baz danych z jedną i pulą można skonfigurować długoterminowe przechowywanie (LTR) dla pełnych kopii zapasowych w okresie do 10 lat w usłudze Azure Blob Storage. Jeśli włączysz zasady LTR, cotygodniowe pełne kopie zapasowe są automatycznie kopiowane do innego kontenera magazynu RA-GRS. Aby spełnić różne wymagania dotyczące zgodności, można wybrać różne okresy przechowywania dla cotygodniowych, comiesięcznych i/lub corocznych kopii zapasowych. Użycie magazynu zależy od wybranej częstotliwości wykonywania kopii zapasowych oraz okresu przechowywania lub okresów. Możesz użyć [kalkulatora cen ltr](https://azure.microsoft.com/pricing/calculator/?service=sql-database) , aby oszacować koszt magazynu ltr.

Podobnie jak w przypadku kopii zapasowych kopie, kopie zapasowe są chronione za pomocą magazynu geograficznie nadmiarowego. Aby uzyskać więcej informacji, zobacz [Nadmiarowość usługi Azure Storage](../../storage/common/storage-redundancy.md).

Aby uzyskać więcej informacji na temat LTR, zobacz [długoterminowe przechowywanie kopii zapasowych](long-term-retention-overview.md).

## <a name="backup-storage-consumption"></a>Użycie magazynu kopii zapasowych

W przypadku pojedynczych baz danych w SQL Database i wystąpieniach zarządzanych w wystąpieniu zarządzanym SQL to równanie jest używane do obliczania łącznego użycia magazynu kopii zapasowych:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

W przypadku baz danych w puli w SQL Database łączny rozmiar magazynu kopii zapasowych jest agregowany na poziomie puli i obliczany w następujący sposób:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Kopie zapasowe wykonywane przed okresem przechowywania są automatycznie przeczyszczane na podstawie ich sygnatur czasowych. Ze względu na to, że różnicowe kopie zapasowe i kopie zapasowe dzienników wymagają użycia wcześniejszej pełnej kopii zapasowej, są one usuwane razem w tygodniowych fragmentach.

SQL Database i wystąpienie zarządzane SQL oblicza łączny magazyn kopii zapasowych w magazynie jako wartość zbiorczą. Co godzinę ta wartość jest raportowana w potoku rozliczeń platformy Azure, który jest odpowiedzialny za agregowanie tego użycia godzinowego w celu obliczenia zużycia na koniec każdego miesiąca. Po porzucenia bazy danych zużycie zmniejsza się w miarę starzenia się kopii zapasowych. Gdy kopie zapasowe staną się starsze niż okres przechowywania, rozliczenia zostaną zatrzymane.

   > [!IMPORTANT]
   > Kopie zapasowe bazy danych są przechowywane przez określony okres przechowywania, nawet jeśli baza danych została porzucona. Porzucenie i ponowne utworzenie bazy danych może często zaoszczędzić na kosztach magazynowania i obliczeń, ale może zwiększyć koszty magazynu kopii zapasowych, ponieważ firma Microsoft zachowuje kopię zapasową dla pozostałego okresu przechowywania dla każdej porzuconej bazy danych, za każdym razem, gdy zostanie usunięta.

### <a name="monitor-consumption"></a>Monitorowanie użycia

Każdy typ kopii zapasowej (pełny, różnicowa i log) jest raportowany w bloku monitorowania bazy danych jako oddzielna Metryka. Na poniższym diagramie przedstawiono sposób monitorowania użycia magazynu kopii zapasowej dla pojedynczej bazy danych. Ta funkcja jest obecnie niedostępna dla wystąpień zarządzanych.

![Monitoruj użycie kopii zapasowej bazy danych w Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Dostosuj użycie magazynu kopii zapasowych

Nadmierne użycie magazynu kopii zapasowych będzie zależeć od obciążenia i rozmiaru poszczególnych baz danych. Należy wziąć pod uwagę niektóre spośród następujących technik dostrajania, aby zmniejszyć wykorzystanie magazynu kopii zapasowych:

- Skrócenie [okresu przechowywania kopii zapasowej](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) do minimum możliwego dla Twoich potrzeb.
- Unikaj wykonywania dużych operacji zapisu, takich jak rekompilacje indeksów, częściej niż jest to konieczne.
- W przypadku dużych operacji ładowania danych Rozważ użycie [klastrowanych indeksów magazynu kolumn](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), zmniejszenie liczby indeksów nieklastrowanych i uwzględnienie operacji ładowania zbiorczego z liczbą wierszy około 1 000 000.
- W warstwie usług ogólnego przeznaczenia zainicjowany magazyn danych jest tańszy niż cena nadmiernego magazynu kopii zapasowych. Jeśli masz ciągle wysokie koszty magazynowania kopii zapasowych, możesz rozważyć zwiększenie ilości miejsca do magazynowania danych w magazynie kopii zapasowych.
- Do przechowywania wyników tymczasowych należy używać bazy danych TempDB zamiast trwałych tabel w logice ETL. (Dotyczy tylko wystąpienia zarządzanego SQL).
- Rozważ wyłączenie szyfrowania TDE dla baz danych, które nie zawierają danych poufnych (na przykład baz danych programistycznych lub testowych). Kopie zapasowe nieszyfrowanych baz danych są zazwyczaj kompresowane z wyższym współczynnikiem kompresji.

> [!IMPORTANT]
> W przypadku składni danych analitycznych \ obciążenia magazynu danych zdecydowanie zalecamy użycie [klastrowanych indeksów magazynu kolumn](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), zmniejszenie liczby indeksów nieklastrowanych i rozważenie operacji ładowania zbiorczego z liczbą wierszy około 1 000 000, aby zmniejszyć zużycie nadmiernej ilości miejsca do magazynowania kopii zapasowych.

## <a name="storage-costs"></a>Koszty magazynowania

Cena magazynu różni się w zależności od tego, czy używasz modelu DTU czy modelu rdzeń wirtualny.

### <a name="dtu-model"></a>Model jednostki DTU

W przypadku korzystania z modelu DTU nie ma dodatkowej opłaty za magazyn kopii zapasowych baz danych i elastycznych pul baz danych.

### <a name="vcore-model"></a>Model rdzenia wirtualnego

W przypadku pojedynczych baz danych w SQL Database minimalna wielkość magazynu kopii zapasowej równa 100% rozmiaru bazy danych jest zapewniana bez dodatkowych opłat. W przypadku pul elastycznych w SQL Database i pojedynczych wystąpień i pul wystąpień w wystąpieniu zarządzanym SQL minimalna wielkość magazynu kopii zapasowej równa 100% przydziału magazynu danych dla puli lub rozmiaru wystąpienia jest dostępna bez dodatkowych opłat. Dodatkowe użycie magazynu kopii zapasowych wiąże się z comiesięczną opłatą za ilość używanych GB. To dodatkowe zużycie będzie zależeć od obciążenia i rozmiaru poszczególnych baz danych.

SQL Database i wystąpienie zarządzane SQL obliczy całkowity magazyn kopii zapasowych w magazynie jako wartość zbiorczą. Co godzinę ta wartość jest raportowana w potoku rozliczeń platformy Azure, który jest odpowiedzialny za agregowanie tego użycia godzinowego w celu uzyskania zużycia na koniec każdego miesiąca. Po porzucenia bazy danych firma Microsoft zmniejsza zużycie w miarę wieku kopii zapasowych. Gdy kopie zapasowe staną się starsze niż okres przechowywania, rozliczenia zostaną zatrzymane. Ponieważ wszystkie kopie zapasowe dzienników i różnicowe kopie zapasowe są zachowywane przez pełny okres przechowywania, wielokrotnie modyfikowane bazy danych będą miały większe opłaty za tworzenie kopii zapasowych.

Załóżmy, że baza danych ma 744 GB miejsca w magazynie kopii zapasowych i że ta kwota pozostaje stała przez cały miesiąc. Aby dokonać konwersji zbiorczego użycia magazynu na użycie godzinowe, Podziel go o 744,0 (31 dni miesięcznie * 24 godziny dziennie). Dlatego SQL Database będzie zgłaszać, że baza danych zużywa 1 GB kopii zapasowej kopie co godzinę. Rozliczenia platformy Azure będą agregowane w tym zużyciu i wykorzystano 744 GB przez cały miesiąc. Koszt będzie naliczany w oparciu o stawkę $/GB/month w Twoim regionie.

Teraz bardziej skomplikowany przykład. Załóżmy, że przechowywanie danych zostało zwiększone do 14 dni w środku miesiąca. Przyjęto założenie, że ten wzrost (teoretycznie) powoduje, że całkowity magazyn kopii zapasowych podwaja się do 1 488 GB. SQL Database mógłby zgłosić 1 GB użycia w godzinach od 1 do 372. Może zgłosić użycie jako 2 GB dla godzin od 373 do 744. To użycie będzie agregowane do ostatecznego rachunku 1 116 GB/miesiąc.

### <a name="monitor-costs"></a>Monitorowanie kosztów

Aby poznać koszty magazynu kopii zapasowych, przejdź do pozycji **Cost Management + rozliczenia** w Azure Portal, wybierz pozycję **Cost Management**, a następnie wybierz pozycję **Analiza kosztów**. Wybierz żądaną subskrypcję jako **zakres**, a następnie odfiltruj dla odpowiedniego okresu i usługi.

Dodaj filtr dla **nazwy usługi**, a następnie na liście rozwijanej wybierz pozycję **baza danych SQL** . Użyj filtru **podkategorii mierników** , aby wybrać licznik rozliczeń dla usługi. W przypadku pojedynczej bazy danych lub elastycznej puli baz danych wybierz **jedną/elastyczną pulę kopie magazynu kopii zapasowych**. W przypadku wystąpienia zarządzanego wybierz pozycję **mi kopie Backup Storage**. Podkategorie **magazynu** i **obliczeń** mogą być również przydatne, ale nie są one skojarzone z kosztami magazynu kopii zapasowych.

![Analiza kosztów magazynu kopii zapasowych](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Przechowywanie kopii zapasowych

Wszystkie bazy danych w Microsoft Azure SQL mają domyślny okres przechowywania kopii zapasowych wynoszący 7 dni. [Okres przechowywania kopii zapasowej można zmienić](#change-the-pitr-backup-retention-period) na maksymalnie 35 dni.

Jeśli usuniesz bazę danych, platforma Azure przechowuje kopie zapasowe w taki sam sposób, jak w przypadku bazy danych w trybie online. Na przykład po usunięciu podstawowej bazy danych, która ma okres przechowywania wynoszący siedem dni, kopia zapasowa, która jest starsza niż cztery dni, jest zapisywana przez trzy więcej dni.

Jeśli chcesz zachować kopie zapasowe dłużej niż maksymalny okres przechowywania, możesz zmodyfikować właściwości kopii zapasowej, aby dodać jeden lub więcej długoterminowych okresów przechowywania do bazy danych. Aby uzyskać więcej informacji, zobacz [Długoterminowe przechowywanie](long-term-retention-overview.md).

> [!IMPORTANT]
> Usunięcie serwera lub wystąpienia zarządzanego spowoduje również usunięcie wszystkich baz danych zarządzanych przez ten serwer lub wystąpienie zarządzane. Nie można ich odzyskać. Nie można przywrócić usuniętego serwera lub wystąpienia zarządzanego. Ale jeśli skonfigurowano długoterminowe przechowywanie dla SQL Database, kopie zapasowe baz danych z LTR nie zostaną usunięte i te bazy danych mogą zostać przywrócone.

## <a name="encrypted-backups"></a>Szyfrowane kopie zapasowe

Jeśli baza danych jest zaszyfrowana przy użyciu programu TDE, kopie zapasowe są automatycznie szyfrowane w stanie spoczynku, łącznie z kopiami zapasowymi LTR. Gdy TDE jest włączona dla wystąpienia zarządzanego SQL Database lub SQL, kopie zapasowe również są szyfrowane. Wszystkie nowe bazy danych w usłudze Azure SQL są domyślnie skonfigurowane z włączoną funkcją TDE. Aby uzyskać więcej informacji na temat TDE, zobacz [transparent Data Encryption z SQL Database i wystąpieniem zarządzanym SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integralność kopii zapasowych

Na bieżąco zespół inżynierów usługi Azure SQL Database automatycznie testuje przywracanie zautomatyzowanych kopii zapasowych baz danych w SQL Database. (Ten test nie jest dostępny w wystąpieniu zarządzanym SQL). W przypadku przywracania do punktu w czasie bazy danych również są odbierane testy integralności DBCC CHECKDB.

Wystąpienie zarządzane SQL pobiera automatycznie początkową kopię zapasową z `CHECKSUM` baz danych przywróconych za pomocą `RESTORE` polecenia macierzystego lub z usługą Azure Data Migration Service po zakończeniu migracji.

Wszelkie problemy znalezione podczas kontroli integralności spowodują powstanie alertu dla zespołu inżynieryjnego. Aby uzyskać więcej informacji, zobacz [integralność danych w SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Zgodność

W przypadku migrowania bazy danych z warstwy usług opartych na protokole DTU do warstwy usług opartych na protokole rdzeń wirtualny zachowywane jest zachowanie kopie, aby upewnić się, że zasady odzyskiwania danych aplikacji nie zostały naruszone. Jeśli domyślne przechowywanie nie spełnia wymagań dotyczących zgodności, można zmienić okres przechowywania kopie przy użyciu programu PowerShell lub interfejsu API REST. Aby uzyskać więcej informacji, zobacz [Zmienianie okresu przechowywania kopii zapasowych kopie](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Zmienianie okresu przechowywania kopii zapasowej kopie

Domyślny okres przechowywania kopii zapasowej kopie można zmienić przy użyciu Azure Portal, programu PowerShell lub interfejsu API REST. W poniższych przykładach pokazano, jak zmienić przechowywanie kopie na 28 dni.

> [!WARNING]
> W przypadku skrócenia bieżącego okresu przechowywania wszystkie istniejące kopie zapasowe, które są starsze niż nowy okres przechowywania, nie będą już dostępne. W przypadku zwiększenia bieżącego okresu przechowywania, platforma Azure zachowuje istniejące kopie zapasowe do momentu osiągnięcia końca dłuższego okresu przechowywania.

> [!NOTE]
> Te interfejsy API wpłyną tylko na kopie okres przechowywania. Jeśli skonfigurowano opcję LTR dla bazy danych, nie będzie to miało wpływu. Aby uzyskać informacje o sposobie zmiany okresów przechowywania LTR, zobacz [długoterminowe przechowywanie](long-term-retention-overview.md)danych.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Zmień okres przechowywania kopii zapasowej kopie przy użyciu Azure Portal

Aby zmienić kopie okres przechowywania kopii zapasowej przy użyciu Azure Portal, przejdź do obiektu serwera, którego okres przechowywania chcesz zmienić w portalu. Następnie wybierz odpowiednią opcję w zależności od obiektu serwera, który jest modyfikowany.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Zmiany w kopie przechowywania kopii zapasowych dla SQL Database są wykonywane na poziomie serwera. Zmiany wprowadzone na poziomie serwera mają zastosowanie do baz danych na serwerze. Aby zmienić kopie przechowywania serwera z Azure Portal, przejdź do bloku przegląd serwera. W lewym okienku wybierz pozycję **Zarządzaj kopiami zapasowymi** , a następnie wybierz pozycję **Konfiguruj przechowywanie** w górnej części ekranu:

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
