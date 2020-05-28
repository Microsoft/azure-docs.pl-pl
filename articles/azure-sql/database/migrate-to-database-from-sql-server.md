---
title: SQL Server migrację bazy danych do jednej lub puli Azure SQL Database
description: Dowiedz się więcej o SQL Server migracji bazy danych do Azure SQL Database.
keywords: migracja bazy danych,migracja bazy danych programu sql server,narzędzia migracji bazy danych,migracja bazy danych,migracja bazy danych sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: f1e89b46f25fd4213b09680b441d3ecfd7a1e13b
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044171"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>SQL Server migrację bazy danych do Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera informacje o podstawowych metodach migrowania bazy danych SQL Server 2005 lub nowszej do jednego lub Azure SQL Database puli. Aby uzyskać informacje na temat migracji do wystąpienia zarządzanego usługi Azure SQL, zobacz [Migrowanie do wystąpienia SQL Server do wystąpienia zarządzanego Azure SQL](../managed-instance/migrate-to-instance-from-sql-server.md). Informacje o migracji z innych platform znajdują się w temacie [Przewodnik po migracji bazy danych Azure](https://datamigration.microsoft.com/).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrowanie do pojedynczej bazy danych lub bazy danych w puli

Istnieją dwie podstawowe metody migrowania bazy danych SQL Server 2005 lub nowszej do jednej lub puli baz danych w Azure SQL Database. Pierwsza metoda jest prostsza, ale wymaga pewnego, prawdopodobnie znaczącego, przestoju podczas migracji. Druga metoda jest bardziej skomplikowana, ale znacznie eliminuje przestój podczas migracji.

W obu przypadkach należy upewnić się, że źródłowa baza danych jest zgodna z Azure SQL Database przy użyciu [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database zbliża się do SQL Server, innych niż problemy [związane z](features-comparison.md) operacjami między bazami danych i na poziomie serwera. Bazy danych i aplikacje oparte na [częściowo obsługiwanych lub nieobsługiwanych funkcjach](transact-sql-tsql-differences-sql-server.md) muszą zostać w pewnym stopniu [przeprojektowane, aby usunąć niezgodności](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) i umożliwić migrację bazy danych programu SQL Server.

> [!NOTE]
> Aby wykonać migrację bazy danych innej niż baza danych programu SQL Server, w tym bazy danych Microsoft Access, Sybase, MySQL Oracle i DB2, do usługi Azure SQL Database, zobacz temat [Asystent migracji programu SQL Server](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Metoda 1. Migracja z przestojem podczas migracji

 Ta metoda służy do migrowania do jednej lub puli baz danych, jeśli jest możliwe przestoje lub przeprowadzana jest testowa migracja produkcyjnej bazy danych do późniejszej migracji. Aby zapoznać się z samouczkiem, zobacz [Migrowanie bazy danych SQL Server](../../dms/tutorial-sql-server-to-azure-sql.md).

Poniższa lista zawiera ogólny przepływ pracy dla SQL Server migracji bazy danych z jedną lub pulą baz danych za pomocą tej metody. Aby przeprowadzić migrację do wystąpienia zarządzanego, zobacz [migracja do wystąpienia zarządzanego](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![Diagram migracji VSSSDT](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Oceń](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) bazę danych pod kątem zgodności przy użyciu najnowszej wersji [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Przygotowanie wszelkich niezbędnych poprawek jako skryptów języka Transact-SQL.
3. Wykonaj transakcję spójną ze sobą poddawaną migracji źródłowej bazy danych lub zatrzymywanie nowych transakcji w źródłowej bazie danych podczas migracji. Metody do wykonania tej ostatniej opcji obejmują wyłączenie łączności klienta lub utworzenie [migawki bazy danych](https://msdn.microsoft.com/library/ms175876.aspx). Po migracji może być możliwe użycie replikacji transakcyjnej w celu zaktualizowania zmigrowanych baz danych przy użyciu zmian, które wystąpiły po punkcie odcięcia migracji. Zobacz [Migrowanie przy użyciu migracji transakcyjnej](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Wdrożenie skryptów języka Transact-SQL w celu zastosowania poprawek do kopii bazy danych.
5. [Przeprowadź migrację](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) kopii bazy danych do nowej Azure SQL Database przy użyciu Data Migration Assistant.

> [!NOTE]
> Zamiast korzystać z usługi DMA, można również użyć pliku BACPAC. Zobacz [Importowanie pliku BACPAC do nowego Azure SQL Database](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optymalizowanie wydajności transferu danych podczas migracji

Poniższa lista zawiera zalecenia pozwalające uzyskać najlepszą wydajność podczas procesu importowania.

- Wybierz najwyższą warstwę usługi i wielkość obliczeń, którą pozwala budżet na maksymalizację wydajności transferu. Po zakończeniu migracji będzie można dokonać skalowania w dół, aby nie ponosić nadmiernych kosztów.
- Zminimalizuj odległość między plikiem BACPAC a docelowym centrum danych.
- Wyłącz automatyczne statystyki na czas migracji.
- Partycjonuj tabele i indeksy.
- Usuń poindeksowane widoki i utwórz je ponownie po zakończeniu.
- Usuń rzadko badane dane historyczne do innej bazy danych i Przeprowadź migrację tych danych historycznych do oddzielnego Azure SQL Database. Następnie będzie można wykonywać zapytania o te dane historyczne za pomocą [zapytań elastycznych](elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optymalizacja wydajności po zakończeniu migracji

[Zaktualizuj statystyki](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) poprzez pełne skanowanie po zakończeniu migracji.

## <a name="method-2-use-transactional-replication"></a>Metoda 2. Użycie replikacji transakcyjnej

Gdy nie możesz sobie pozwolić na usunięcie bazy danych programu SQL Server ze środowiska produkcyjnego na czas migracji, jako rozwiązania do migracji możesz wykorzystać replikację transakcyjną programu SQL Server. Aby użyć tej metody, źródłowa baza danych musi spełniać [wymagania dotyczące replikacji transakcyjnej](https://msdn.microsoft.com/library/mt589530.aspx) i być zgodna z usługą Azure SQL Database. Aby uzyskać informacje o replikacji SQL z funkcją zawsze włączone, zobacz [Konfigurowanie replikacji dla zawsze włączonych grup dostępności (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Aby użyć tego rozwiązania, usługę Azure SQL Database konfiguruje się jako subskrybenta wystąpienia programu SQL Server do migracji. Dystrybutor replikacji transakcyjnej synchronizuje dane z bazy danych do synchronizacji (wydawcy), podczas gdy cały czas realizowane są nowe transakcje.

Przy replikacji transakcyjnej wszystkie zmiany wprowadzane do danych lub schematu pojawiają się w usłudze Azure SQL Database. Gdy synchronizacja zostanie zakończona i wszystko będzie gotowe do migracji, zmień parametry połączenia swoich aplikacji w taki sposób, aby wskazać im usługę Azure SQL Database. Gdy w wyniku replikacji transakcyjnej wszystkie zmiany pozostałe w źródłowej bazie danych zostaną pobrane, a wszystkie aplikacje będą wskazywać bazę danych platformy Azure, można odinstalować replikację transakcyjną. Usługa Azure SQL Database stanie się Twoim systemem produkcyjnym.

 ![Diagram SeedCloudTR](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> Replikacji transakcyjnej możesz także używać do migrowania podzestawu źródłowej bazy danych. Publikacja replikowana do usługi Azure SQL Database może być ograniczona do podzbioru tabel w replikowanej bazie danych. Dla każdej replikowanej tabeli dane można ograniczyć do podzestawu wierszy i/lub podzestawu kolumn.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Przepływ pracy migracji do usługi SQL Database za pomocą replikacji transakcyjnej

> [!IMPORTANT]
> Użyj najnowszej wersji SQL Server Management Studio, aby zachować synchronizację z aktualizacjami platformy Azure i SQL Database. Starsze wersje programu SQL Server Management Studio nie mogą skonfigurować usługi SQL Database jako subskrybenta. [Zaktualizuj program SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Konfigurowanie dystrybucji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Przy użyciu języka Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Tworzenie publikacji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Przy użyciu języka Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Tworzenie subskrypcji
   - [Przy użyciu programu SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Przy użyciu języka Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

Niektóre wskazówki i różnice dotyczące migracji do usługi SQL Database

- Użycie dystrybutora lokalnego:
  - Wykonanie tej czynności powoduje wpływ na wydajność serwera.
  - Jeśli negatywny wpływ na wydajność jest nieakceptowalny, można użyć innego serwera, ale komplikuje to zarządzanie i administrowanie.
- Wybierając folder migawki, upewnij się, że wybrany folder jest wystarczająco duży, aby pomieścić kopię BCP każdej tabeli, którą chcesz zreplikować.
- Tworzenie migawki powoduje zablokowanie skojarzonych tabel do czasu zakończenia operacji, dlatego tworzenie migawek należy odpowiednio zaplanować.
- Usługa Azure SQL Database obsługuje wyłącznie subskrypcje wypychane. Subskrybentów można dodawać wyłącznie ze źródłowej bazy danych.

## <a name="resolving-database-migration-compatibility-issues"></a>Rozwiązywanie problemów ze zgodnością migracji bazy danych

Może wystąpić wiele różnych problemów ze zgodnością, w zależności od wersji programu SQL Server w źródłowej bazie danych oraz złożoności migrowanej bazy danych. Starsze wersje programu SQL Server mają więcej problemów ze zgodnością. Oprócz ukierunkowanego wyszukiwania w Internecie za pomocą preferowanej wyszukiwarki użyj następujących zasobów:

- [Funkcje bazy danych programu SQL Server nieobsługiwane przez usługę Azure SQL Database](transact-sql-tsql-differences-sql-server.md)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Nieobsługiwane funkcje aparatu bazy danych w programie SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oprócz przeszukiwania Internetu i korzystania z tych zasobów, na potrzeby Azure SQL Database lub [StackOverflow](https://stackoverflow.com/)należy użyć strony pytań i odpowiedzi [Microsoft&pytań](https://docs.microsoft.com/answers/topics/azure-sql-database.html) i odpowiedzi.

> [!IMPORTANT]
> Wystąpienie zarządzane Azure SQL umożliwia migrowanie istniejącego wystąpienia SQL Server i jego baz danych z minimalnym brakiem problemów ze zgodnością. Zobacz [, co to jest wystąpienie zarządzane usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Następne kroki

- Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować użycie bazy danych tempdb podczas migracji](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Użyj skryptu z blogu SQL EMEA Engineers, aby [monitorować obszar rejestrowania transakcji Twojej bazy danych w czasie trwania migracji](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby uzyskać informacje na temat pracy z czasem UTC po migracji, zobacz [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (Modyfikowanie domyślnej strefy czasowej dla Twojej lokalnej strefy czasowej).
- Aby uzyskać informacje na temat zmieniania domyślnego języka bazy danych po migracji, zobacz [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (Jak zmienić domyślny język usługi Azure SQL Database).
