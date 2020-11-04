---
title: Tworzenie i aktualizowanie statystyk w tabelach
description: Zalecenia i przykłady dotyczące tworzenia i aktualizowania statystyk optymalizacji zapytań w tabelach w dedykowanej puli SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d9349c5d1c4e6255dc0854537bb7e93e3e636ce8
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321063"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Statystyka tabeli dla dedykowanej puli SQL w usłudze Azure Synapse Analytics

W tym artykule znajdziesz zalecenia i przykłady dotyczące tworzenia i aktualizowania statystyk optymalizacji zapytań w tabelach w dedykowanej puli SQL.

## <a name="why-use-statistics"></a>Dlaczego warto używać statystyk

Bardziej dedykowana Pula SQL wie o danych, tym szybciej może wykonywać zapytania względem tego użytkownika. Po załadowaniu danych do dedykowanej puli SQL zbieranie danych statystycznych jest jednym z najważniejszych rzeczy, które można wykonać, aby zoptymalizować zapytania.

Optymalizator zapytań dedykowanej puli SQL to Optymalizator oparty na kosztach. Porównuje koszt różnych planów zapytań, a następnie wybiera plan z najniższym kosztem. W większości przypadków wybiera plan, który będzie wykonywał najszybszy.

Na przykład, jeśli optymalizator szacuje, że data filtrowania zapytania zwróci jeden wiersz, wybierze jeden plan. Jeśli szacuje się, że wybrana data zwróci 1 000 000 wierszy, zwróci inny plan.

## <a name="automatic-creation-of-statistic"></a>Automatyczne tworzenie statystyk

Gdy opcja AUTO_CREATE_STATISTICS bazy danych jest włączona, dedykowana Pula SQL analizuje przychodzące zapytania użytkowników pod kątem brakujących statystyk.

Jeśli brakuje statystyk, optymalizator zapytań tworzy statystyki dla poszczególnych kolumn w predykacie zapytania lub warunek sprzężenia, aby poprawić oszacowania kardynalności dla planu zapytania.

> [!NOTE]
> Automatyczne tworzenie statystyk jest obecnie domyślnie włączone.

Aby sprawdzić, czy dedykowana Pula SQL ma AUTO_CREATE_STATISTICS skonfigurowany, należy uruchomić następujące polecenie:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Jeśli dedykowana Pula SQL nie ma skonfigurowanych AUTO_CREATE_STATISTICS, zalecamy włączenie tej właściwości, uruchamiając następujące polecenie:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Te instrukcje wyzwalają automatyczne tworzenie statystyk:

- SELECT
- WSTAW — WYBIERZ
- CTAS
- UPDATE
- DELETE
- WYJAŚNIj, kiedy zostanie wykryta wartość Join lub obecność predykatu

> [!NOTE]
> Automatyczne tworzenie statystyk nie jest tworzone w tabelach tymczasowych lub zewnętrznych.

Automatyczne tworzenie statystyk jest wykonywane synchronicznie, dzięki czemu może nastąpić nieznacznie obniżenie wydajności zapytań, jeśli w kolumnach brakuje statystyk. Czas tworzenia statystyk dla pojedynczej kolumny zależy od rozmiaru tabeli.

Aby uniknąć wymiernego obniżenia wydajności, należy upewnić się, że statystyki zostały najpierw utworzone przez wykonanie obciążenia testowego przed przeprowadzeniem profilowania systemu.

> [!NOTE]
> Tworzenie statystyk zostanie zalogowane [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) w innym kontekście użytkownika.

W przypadku tworzenia automatycznych statystyk będą one miały postać: _WA_Sys_ <8 Identyfikator kolumny cyfr w>_ szesnastkowym<8 identyfikator tabeli cyfrowej w> szesnastkowym. Można wyświetlić statystyki, które zostały już utworzone, uruchamiając polecenie [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name to nazwa tabeli zawierającej statystyki do wyświetlenia. Ta tabela nie może być tabelą zewnętrzną. Obiekt docelowy jest nazwą docelowego indeksu, statystyki lub kolumny, dla którego mają być wyświetlone informacje statystyczne.

## <a name="update-statistics"></a>Aktualizowanie statystyk

Najlepszym rozwiązaniem jest aktualizacja statystyk dla kolumn dat każdego dnia, gdy dodawane są nowe daty. Za każdym razem, gdy nowe wiersze są ładowane do dedykowanej puli SQL, dodawane są nowe daty ładowania lub daty transakcji. Te dodatki zmieniają dystrybucję danych i statystyki są nieaktualne.

Statystyki w kolumnie kraj/region w tabeli klienta nigdy nie muszą zostać zaktualizowane, ponieważ dystrybucja wartości nie jest na ogół zmieniana. Przy założeniu, że dystrybucja jest stała między klientami, dodanie nowych wierszy do odmiany tabeli nie powoduje zmiany dystrybucji danych.

Jeśli jednak dedykowana Pula SQL zawiera tylko jeden kraj/region, a dane są przechowywane z nowego kraju/regionu, w wyniku czego dane pochodzą z wielu krajów/regionów, należy zaktualizować statystyki w kolumnie kraj/region.

Poniżej przedstawiono zalecenia dotyczące aktualizowania statystyk:

|||
|-|-|
| **Częstotliwość aktualizacji statystyk**  | Ostrożne: codziennie </br> Po załadowaniu lub przeprowadzeniu przekształcenia danych |
| **Próbkowanie** |  Mniej niż 1 000 000 000 wierszy, Użyj domyślnego próbkowania (20 procent). </br> Mając więcej niż 1 000 000 000 wierszy, użyj próbkowania dwóch procent. |

Jednym z pierwszych pytań, które należy zadać w przypadku rozwiązywania problemów z kwerendą jest **"czy statystyki są aktualne?"**

To pytanie nie jest takie, którego można udzielić odpowiedzi według wieku danych. Aktualny obiekt statystyk może być stary, jeśli nie wprowadzono żadnych istotnych zmian w danych źródłowych. Gdy liczba wierszy uległa znacznej zmianie lub w dystrybucji wartości dla kolumny istnieje istotna zmiana, *należy ją zaktualizować* . 

Nie istnieje dynamiczny widok zarządzania, aby określić, czy dane w tabeli uległy zmianie od czasu ostatniego aktualizowania statystyk.  Poniższe dwa zapytania mogą pomóc w ustaleniu, czy statystyki są przestarzałe.

**Zapytanie 1:**  Sprawdź różnicę między liczbą wierszy z statystyk ( **stats_row_count** ) i rzeczywistą liczbę wierszy ( **actual_row_count** ). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Zapytanie 2:** Zapoznaj się z wiekiem statystyk, sprawdzając czas ostatniej aktualizacji statystyk w każdej tabeli. 

> [!NOTE]
> W przypadku zmiany materiału w dystrybucji wartości dla kolumny należy zaktualizować statystyki bez względu na czas ostatniej aktualizacji.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Kolumny dat** w dedykowanej puli SQL, na przykład zazwyczaj potrzebują częste aktualizacje statystyk. Za każdym razem, gdy nowe wiersze są ładowane do dedykowanej puli SQL, dodawane są nowe daty ładowania lub daty transakcji. Te dodatki zmieniają dystrybucję danych i statystyki są nieaktualne.

Z drugiej strony statystyki w kolumnie płeć w tabeli klienta nigdy nie muszą być aktualizowane. Przy założeniu, że dystrybucja jest stała między klientami, dodanie nowych wierszy do odmiany tabeli nie powoduje zmiany dystrybucji danych.

Jeśli dedykowana Pula SQL zawiera tylko jedną płeć, a nowe wymaganie ma wiele płci, należy zaktualizować statystyki w kolumnie płeć.

Aby uzyskać więcej informacji, zobacz Ogólne wskazówki dotyczące [statystyk](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="implementing-statistics-management"></a>Implementowanie zarządzania statystykami

Często dobrym pomysłem jest rozbudowa procesu ładowania danych w celu zapewnienia, że statystyki są aktualizowane na końcu obciążenia, aby uniknąć/zminimalizować Blokowanie lub rywalizację o zasoby między współbieżnymi zapytaniami.  

Ładowanie danych jest w przypadku, gdy tabele często zmieniają ich rozmiar i/lub rozkład wartości. Ładowanie danych jest logicznym miejscem do implementacji niektórych procesów zarządzania.

Następujące zasady dotyczące identyfikatorów GUID są dostępne do aktualizowania statystyk:

- Upewnij się, że każda załadowana tabela ma zaktualizowany co najmniej jeden obiekt statystyk. Spowoduje to zaktualizowanie informacji o rozmiarze tabeli (liczbie wierszy i liczbie stron) w ramach aktualizacji statystyk.
- Skup się na kolumnach uczestniczących w klauzulach JOIN, GROUP BY, ORDER BY i DISTINCT.
- Należy rozważyć aktualizowanie kolumn "Ascending Key", takich jak daty transakcji, ponieważ te wartości nie zostaną uwzględnione w histogramie statystyki.
- Należy rozważyć częste Aktualizowanie statycznych kolumn dystrybucji.
- Należy pamiętać, że każdy obiekt statystyki jest aktualizowany w kolejności. Po prostu implementacja `UPDATE STATISTICS <TABLE_NAME>` nie zawsze jest idealnym rozwiązaniem, szczególnie w przypadku szerokich tabel zawierających wiele obiektów statystyk.

Aby uzyskać więcej informacji, zobacz [oszacowanie kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="examples-create-statistics"></a>Przykłady: Tworzenie statystyk

W poniższych przykładach pokazano, jak używać różnych opcji tworzenia statystyk. Opcje, które są używane dla każdej kolumny, zależą od charakterystyki danych i sposobu użycia kolumny w zapytaniach.

### <a name="create-single-column-statistics-with-default-options"></a>Tworzenie statystyk z jedną kolumną z opcjami domyślnymi

Aby utworzyć statystyki dla kolumny, podaj nazwę dla obiektu statystyki i nazwę kolumny.

Ta składnia używa wszystkich opcji domyślnych. Domyślnie **20%** tabeli jest próbkowane podczas tworzenia statystyk.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Utwórz statystyki pojedynczej kolumny, badając każdy wiersz

Domyślna częstotliwość próbkowania 20 procent jest wystarczająca w większości sytuacji. Można jednak dostosować częstotliwość próbkowania.

Aby pobrać pełną tabelę, użyj następującej składni:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Przykład:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Utwórz statystyki pojedynczej kolumny, określając rozmiar próbki

Alternatywnie możesz określić rozmiar próbki jako procent:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Utwórz statystyki pojedynczej kolumny tylko dla niektórych wierszy

Możesz również utworzyć statystyki dotyczące części wierszy w tabeli. Ta nazwa jest nazywana przefiltrowanym statystyką.

Można na przykład użyć filtrowanych statystyk, gdy planujesz wykonać zapytanie dotyczące określonej partycji dużej partycjonowanej tabeli. Dzięki tworzeniu statystyk dotyczących tylko wartości partycji, dokładność statystyk zostanie zwiększona, a tym samym poprawi wydajność zapytań.

Ten przykład tworzy statystyki dotyczące zakresu wartości. Wartości mogą być łatwo zdefiniowane, aby odpowiadały zakresowi wartości w partycji.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Aby optymalizator zapytań mógł rozważyć użycie filtrowanych statystyk przy wyborze planu zapytania rozproszonego, zapytanie musi pasować do definicji obiektu statystyk. W poprzednim przykładzie klauzula WHERE zapytania musi określać wartości Kol1 z zakresu od 2000101 do 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Utwórz statystyki pojedynczej kolumny ze wszystkimi opcjami

Możesz również połączyć opcje razem. Poniższy przykład tworzy filtrowany obiekt statystyczny z niestandardowym rozmiarem próbki:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Aby uzyskać pełne informacje, zobacz [Tworzenie statystyk](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="create-multi-column-statistics"></a>Tworzenie statystyk wielokolumnowych

Aby utworzyć obiekt statystyk z wieloma kolumnami, należy użyć powyższych przykładów, ale określić więcej kolumn.

> [!NOTE]
> Histogram, który jest używany do oszacowania liczby wierszy w wyniku zapytania, jest dostępny tylko dla pierwszej kolumny wymienionej w definicji obiektu statystyki.

W tym przykładzie histogram znajduje się w *\_ kategorii Product (produkt* ). Statystyki między kolumnami są obliczane na *podstawie \_ kategorii produktu* i *\_ sub_category produktu* :

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Ponieważ istnieje korelacja między *\_ kategorią produktu* a *\_ podrzędną \_ kategorią produktu* , obiekt statystyk wielokolumnowych może być przydatny, jeśli dostęp do tych kolumn odbywa się w tym samym czasie.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Tworzenie statystyk dla wszystkich kolumn w tabeli

Jednym ze sposobów tworzenia statystyk jest wygenerowanie poleceń tworzenia statystyk po utworzeniu tabeli:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Użyj procedury składowanej, aby utworzyć statystyki dla wszystkich kolumn w bazie danych

Dedykowana Pula SQL nie ma systemowej procedury składowanej równoważnej sp_create_stats w SQL Server. Ta procedura składowana tworzy obiekt statystyki jednokolumnowej dla każdej kolumny bazy danych, która nie ma jeszcze statystyk.

Poniższy przykład pomoże Ci rozpocząć pracę z projektem bazy danych. Możesz dostosowywać je do Twoich potrzeb.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Aby utworzyć statystyki dla wszystkich kolumn w tabeli przy użyciu wartości domyślnych, wykonaj procedurę składowaną.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Aby utworzyć statystyki dla wszystkich kolumn w tabeli przy użyciu FULLSCAN, Wywołaj tę procedurę.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Aby utworzyć próbkowanie statystyk dla wszystkich kolumn w tabeli, wprowadź 3 i procent próbki. Ta procedura stosuje częstotliwość próbkowania 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Przykłady: aktualizowanie statystyk

Aby zaktualizować dane statystyczne, możesz:

- Aktualizowanie jednego obiektu statystyki. Określ nazwę obiektu statystyki, który chcesz zaktualizować.
- Aktualizowanie wszystkich obiektów statystyk w tabeli. Określ nazwę tabeli zamiast jednego określonego obiektu statystyki.

### <a name="update-one-specific-statistics-object"></a>Aktualizowanie jednego określonego obiektu statystyki

Aby zaktualizować konkretny obiekt Statystyczny, należy użyć następującej składni:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Przykład:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Dzięki aktualizacji określonych obiektów statystyk można zminimalizować czas i zasoby wymagane do zarządzania statystykami. W związku z tym konieczne jest wybranie najlepszych obiektów statystyk do zaktualizowania.

### <a name="update-all-statistics-on-a-table"></a>Aktualizowanie wszystkich statystyk w tabeli

Prostą metodą aktualizowania wszystkich obiektów statystyk w tabeli jest:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Przykład:

```sql
UPDATE STATISTICS dbo.table1;
```

Instrukcja UPDATE STATISTICs jest łatwa w użyciu. Należy pamiętać, że aktualizuje *wszystkie* statystyki tabeli i dlatego może wykonywać więcej pracy niż jest to konieczne. Jeśli nie jest to problem, najłatwiejszym i najbardziej kompletnym sposobem zagwarantowania, że statystyki są aktualne.

> [!NOTE]
> Podczas aktualizowania wszystkich statystyk w tabeli dedykowana Pula SQL wykonuje skanowanie w celu próbkowania tabeli dla każdego obiektu statystyki. Jeśli tabela jest duża i zawiera wiele kolumn i wiele statystyk, może być bardziej wydajna aktualizacja indywidualnych statystyk w zależności od potrzeb.

Aby uzyskać implementację `UPDATE STATISTICS` procedury, zobacz [tabele tymczasowe](sql-data-warehouse-tables-temporary.md). Metoda implementacji różni się nieco od poprzedniej `CREATE STATISTICS` procedury, ale wynik jest taki sam.

Pełną składnię można znaleźć w temacie [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="statistics-metadata"></a>Metadane statystyk

Istnieje kilka widoków i funkcji systemu, których można użyć, aby znaleźć informacje o statystyce. Można na przykład sprawdzić, czy obiekt statystyki może być nieaktualny przy użyciu funkcji Statystyka daty, aby zobaczyć, kiedy statystyki zostały ostatnio utworzone lub zaktualizowane.

### <a name="catalog-views-for-statistics"></a>Widoki wykazu dla statystyk

Te widoki systemowe zawierają informacje o statystyce:

| Widok wykazu | Opis |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdej kolumny. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego obiektu w bazie danych. |
| [sys. schematy](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego schematu w bazie danych. |
| [sys. statystyki](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego obiektu statystyki. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdej kolumny w obiekcie Statystyka. Linki z powrotem do widoku sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdej tabeli (zawiera tabele zewnętrzne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego typu danych. |

### <a name="system-functions-for-statistics"></a>Funkcje systemowe dla statystyk

Te funkcje systemowe są przydatne do pracy z statystykami:

| Funkcja systemowa | Opis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Data ostatniej aktualizacji obiektu statystyki. |
| [SHOW_STATISTICS DBCC](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Poziom podsumowania i szczegółowe informacje o dystrybucji wartości zrozumiałe dla obiektu Statystyka. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Łączenie kolumn statystyk i funkcji w jeden widok

Ten widok umożliwia łączenie kolumn odnoszących się do statystyk i wyników z funkcji STATS_DATE ().

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>Przykłady polecenia DBCC SHOW_STATISTICS ()

Polecenie DBCC SHOW_STATISTICS () pokazuje dane przechowywane w obiekcie statystyk. Te dane wchodzą w skład trzech części:

- Header
- Wektor gęstości
- Histogram

Metadane nagłówka dotyczące statystyk. Histogram wyświetla rozkład wartości w pierwszej kolumnie klucza obiektu Statystyka. Wektor gęstości mierzy korelację między kolumnami.

> [!NOTE]
> Dedykowana Pula SQL oblicza oszacowania kardynalności przy użyciu dowolnych danych w obiekcie Statystyka.

### <a name="show-header-density-and-histogram"></a>Pokaż nagłówek, gęstość i histogram

Ten prosty przykład przedstawia wszystkie trzy części obiektu statystyki:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Pokaż co najmniej jedną część SHOW_STATISTICS DBCC ()

Jeśli interesuje Cię tylko wyświetlanie określonych części, użyj `WITH` klauzuli i określ, które części mają być widoczne:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Różnice w programie DBCC SHOW_STATISTICS ()

Program DBCC SHOW_STATISTICS () jest bardziej ściśle zaimplementowany w dedykowanej puli SQL w porównaniu do SQL Server:

- Nieudokumentowane funkcje nie są obsługiwane.
- Nie można użyć Stats_stream.
- Nie można dołączyć wyników dla określonych podzestawów danych statystycznych. Na przykład STAT_HEADER JOIN DENSITY_VECTOR.
- Nie można ustawić NO_INFOMSGS do pomijania komunikatów.
- Nie można użyć nawiasów kwadratowych wokół nazw statystyk.
- Nie można używać nazw kolumn do identyfikowania obiektów statystyk.
- Błąd niestandardowy 2767 nie jest obsługiwany.

## <a name="next-steps"></a>Następne kroki

Aby zwiększyć wydajność zapytań, zobacz [monitorowanie obciążenia](sql-data-warehouse-manage-monitor.md)
