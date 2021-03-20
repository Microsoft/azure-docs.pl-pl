---
title: Wprowadzenie do tabel danych czasowych
description: Dowiedz się, jak rozpocząć korzystanie z tabel danych czasowych w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 06/26/2019
ms.openlocfilehash: ea037d12417c8fad9d80b77df69285ed2c8df31b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91618662"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Wprowadzenie do tabel danych czasowych w Azure SQL Database i wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Tabele danych czasowych to funkcja programowalności Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL, które pozwala śledzić i analizować pełną historię zmian danych, bez potrzeby kodowania niestandardowego. Tabele danych czasowych przechowują dane ściśle powiązane z kontekstem czasu, dzięki czemu przechowywane fakty mogą być interpretowane jako prawidłowe tylko w określonym okresie. Ta właściwość tabel danych czasowych pozwala na efektywną analizę opartą na czasie i uzyskiwanie szczegółowych informacji na podstawie ewolucji danych.

## <a name="temporal-scenario"></a>Scenariusz danych czasowych

W tym artykule przedstawiono kroki umożliwiające korzystanie z tabel danych czasowych w scenariuszu aplikacji. Załóżmy, że chcesz śledzić aktywność użytkowników w nowej witrynie sieci Web, która jest opracowywana od podstaw lub w istniejącej witrynie sieci Web, która ma zostać rozszerzona za pomocą analizy aktywności użytkownika. W tym uproszczonym przykładzie zakładamy, że liczba odwiedzonych stron sieci Web w danym okresie jest wskaźnikiem, który musi być przechwytywany i monitorowany w bazie danych witryny sieci Web hostowanej na Azure SQL Database lub wystąpieniu zarządzanym usługi Azure SQL. Celem analitycznej analizy aktywności użytkownika jest uzyskanie danych wejściowych w celu przeprojektowania witryny sieci Web i zapewnienia lepszej funkcjonalności dla odwiedzających.

Model bazy danych dla tego scenariusza to bardzo prosta Metryka aktywności użytkownika, która jest reprezentowana przy użyciu pojedynczego pola liczb całkowitych, **PageVisited** i jest przechwytywana wraz z podstawowymi informacjami w profilu użytkownika. Ponadto w przypadku analizy opartej na czasie można przechowywać serię wierszy dla każdego użytkownika, gdzie każdy wiersz reprezentuje liczbę stron, które użytkownik odwiedzał w określonym czasie.

![Schemat](./media/temporal-tables/AzureTemporal1.png)

Na szczęście nie trzeba umieszczać żadnych nakładów pracy w aplikacji, aby zachować te informacje o działaniach. W przypadku tabel danych czasowych proces ten jest zautomatyzowany — zapewnianie pełnej elastyczności podczas projektowania witryny sieci Web i więcej czasu na skoncentrowanie się na samej analizie. Jedyną czynnością, którą należy wykonać, jest upewnienie się, że tabela **WebSiteInfo** jest skonfigurowana jako czasowa [wersja systemu](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table). Poniżej opisano szczegółowe instrukcje dotyczące korzystania z tabel danych czasowych w tym scenariuszu.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1. Konfigurowanie tabel jako danych czasowych

W zależności od tego, czy uruchamiasz nowe programowanie, czy uaktualniasz istniejącą aplikację, należy utworzyć tabele danych czasowych lub zmodyfikować istniejące, dodając atrybuty czasowe. Ogólnie rzecz biorąc, Twój scenariusz może być kombinacją tych dwóch opcji. Wykonaj te czynności, korzystając z [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), [SQL Server narzędzi do danych](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT), [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)lub innych narzędzi programistycznych języka Transact-SQL.

> [!IMPORTANT]
> Zaleca się, aby zawsze używać najnowszej wersji Management Studio, aby zachować synchronizację z aktualizacjami Azure SQL Database i wystąpienia zarządzanego Azure SQL. [Zaktualizuj program SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>Utwórz nową tabelę

Użyj elementu menu kontekstowego "Nowa System-Versioned tabela" w programie SSMS Eksplorator obiektów, aby otworzyć Edytor zapytań z skryptowym szablonem tabeli danych czasowych, a następnie użyć opcji "Określ wartości parametrów szablonu" (Ctrl + Shift + M), aby wypełnić szablon:

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

W SSDT wybierz szablon "dane czasowe (systemowo z systemem)" podczas dodawania nowych elementów do projektu bazy danych. Spowoduje to otwarcie projektanta tabel i umożliwi łatwe Określanie układu tabeli:

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

Możesz również utworzyć tabelę danych czasowych, określając bezpośrednio instrukcje języka Transact-SQL, jak pokazano w poniższym przykładzie. Należy zauważyć, że obowiązkowe elementy każdej tabeli danych CZASowych to definicja okresu i klauzula SYSTEM_VERSIONING z odwołaniem do innej tabeli użytkownika, w której będą przechowywane wersje wierszy historycznych:

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Podczas tworzenia tabeli danych czasowych z systemową obsługą wersji zostanie automatycznie utworzona załączona tabela historii z domyślną konfiguracją. Domyślna tabela historii zawiera klastrowany indeks drzewa B w kolumnach okresów (end, Start) z włączoną kompresją strony. Ta konfiguracja jest optymalna dla większości scenariuszy, w których używane są tabele danych czasowych, szczególnie w przypadku [inspekcji danych](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit).

W tym konkretnym przypadku chcemy przeprowadzać analizę trendu na podstawie czasu na dłuższej historii danych i z większymi zestawami danych, dlatego wybór magazynu dla tabeli historii jest klastrowanym indeksem magazynu kolumn. Klastrowana magazyn kolumn zapewnia bardzo dobrą kompresję i wydajność zapytań analitycznych. Tabele danych czasowych zapewniają elastyczność konfigurowania indeksów w tabelach bieżących i danych czasowych w całości niezależnie od siebie.

> [!NOTE]
> Indeksy magazynu kolumn są dostępne w warstwach Krytyczne dla działania firmy, Ogólnego przeznaczenia i Premium oraz w warstwie Standardowa, S3 i wyższych.

Poniższy skrypt pokazuje, jak domyślny indeks tabeli historii można zmienić na klastrowaną magazyn kolumn:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Tabele danych czasowych są reprezentowane w Eksplorator obiektów z określoną ikoną ułatwiającą identyfikację, a jej tabela historii jest wyświetlana jako węzeł podrzędny.

![ALTER](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Zmień istniejącą tabelę na czasowe

Przyjrzyjmy się alternatywnemu scenariuszowi, w którym tabela WebsiteUserInfo już istnieje, ale nie została zaprojektowana w celu zachowania historii zmian. W takim przypadku można po prostu zwiększyć istniejącą tabelę, aby stała się okresem, jak pokazano w następującym przykładzie:

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>Krok 2. Regularne uruchamianie obciążeń

Główną zaletą tabel danych czasowych jest to, że nie trzeba zmieniać ani dostosowywać witryny sieci Web w taki sposób, aby można było wykonywać śledzenie zmian. Po utworzeniu tabele danych czasowych w sposób przezroczysty zachowują poprzednie wersje wierszy za każdym razem, gdy dokonywane są modyfikacje w przypadku zmian.

Aby skorzystać z automatycznego śledzenia zmian w tym konkretnym scenariuszu, należy po prostu zaktualizować kolumnę **PagesVisited** za każdym razem, gdy użytkownik skończy swoją sesję w witrynie sieci Web:

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

Należy zauważyć, że zapytanie Update nie musi znać dokładnego czasu podczas rzeczywistej operacji, ani sposobu, w jaki dane historyczne zostaną zachowane do przyszłej analizy. Oba aspekty są automatycznie obsługiwane przez Azure SQL Database i wystąpienie zarządzane usługi Azure SQL. Na poniższym diagramie przedstawiono sposób generowania danych historii dla każdej aktualizacji.

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3. wykonywanie analizy danych historycznych

Teraz, gdy jest włączone okresowe wersje systemu, analiza danych historycznych jest tylko jedną kwerendą od użytkownika. W tym artykule przedstawimy kilka przykładów dotyczących typowych scenariuszy analizy — aby poznać wszystkie szczegóły, zapoznaj się z różnymi opcjami wprowadzonymi za pomocą klauzuli [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data) .

Aby zobaczyć 10 najważniejszych użytkowników zamówionych przez liczbę odwiedzonych stron sieci Web w ciągu godziny temu, uruchom następujące zapytanie:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Możesz łatwo zmodyfikować to zapytanie, aby przeanalizować wizyty w witrynie na dzień temu, miesiąc temu lub w dowolnym miejscu w przeszłości.

Aby przeprowadzić podstawową analizę statystyczną dla poprzedniego dnia, użyj poniższego przykładu:

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Aby wyszukać działania określonego użytkownika w danym okresie, użyj klauzuli ZAWARTEj w:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Wizualizacja graficzna jest szczególnie przydatna w przypadku zapytań czasowych, ponieważ można łatwo pokazać trendy i wzorce użycia w intuicyjny sposób:

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Rozwijanie schematu tabeli

Zwykle podczas tworzenia aplikacji należy zmienić schemat tabeli danych czasowych. W tym celu wystarczy uruchomić regularne instrukcje ALTER TABLE i Azure SQL Database lub wystąpienie zarządzane Azure SQL odpowiednio propaguje zmiany do tabeli historii. Poniższy skrypt pokazuje, jak dodać dodatkowy atrybut do śledzenia:

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Analogicznie, można zmienić definicję kolumny, gdy obciążenie jest aktywne:

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Na koniec można usunąć kolumnę, która nie jest już potrzebna.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

Alternatywnie możesz użyć najnowszej [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) , aby zmienić czasowy schemat tabeli podczas połączenia z bazą danych (tryb online) lub jako część projektu bazy danych (tryb offline).

## <a name="controlling-retention-of-historical-data"></a>Kontrolowanie przechowywania danych historycznych

W przypadku tabel danych czasowych z systemową obsługą wersji w tabeli historii może być zwiększana wartość rozmiaru większej niż tabele zwykłe. Duża i coraz większa tabela historii może stać się problemem zarówno ze względu na czyste koszty magazynowania, jak i nakładania podatku za wykonywanie zapytań czasowych. Tym samym, opracowywanie zasad przechowywania danych na potrzeby zarządzania danymi w tabeli historii jest ważnym aspektem planowania i zarządzania cyklem życia każdej tabeli danych czasowych. Korzystając z Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL, istnieją następujące podejścia do zarządzania danymi historycznymi w tabeli danych czasowych:

- [Partycjonowanie tabel](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [Niestandardowy skrypt czyszczący](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o tabelach danych czasowych, zobacz Wyewidencjonowywanie [tabel](/sql/relational-databases/tables/temporal-tables)danych czasowych.
- Odwiedź stronę Channel 9, aby poznać [historię sukcesu wykonywania](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) danych czasowych klienta i obejrzyj [prezentację](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)czasową na żywo.
