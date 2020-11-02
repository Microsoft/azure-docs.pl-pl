---
title: Tworzenie i aktualizowanie statystyk przy użyciu zasobów SQL usługi Azure Synapse
description: Zalecenia i przykłady dotyczące tworzenia i aktualizowania statystyk optymalizacji zapytań w programie SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 368d43283d713b8d4e101c2ee26724242f29756c
ms.sourcegitcommit: 8ad5761333b53e85c8c4dabee40eaf497430db70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148256"
---
# <a name="statistics-in-synapse-sql"></a>Statystyka w programie SQL Synapse

W tym artykule przedstawiono zalecenia i przykłady dotyczące tworzenia i aktualizowania statystyk optymalizacji zapytań za pomocą Synapse zasobów SQL: puli SQL i SQL na żądanie (wersja zapoznawcza).

## <a name="statistics-in-sql-pool"></a>Statystyki w puli SQL

### <a name="why-use-statistics"></a>Dlaczego warto używać statystyk

Im więcej zasobów puli SQL wie o danych, tym szybciej może wykonywać zapytania. Po załadowaniu danych do puli SQL zbieranie danych statystycznych jest jednym z najważniejszych rzeczy, które można wykonać w celu optymalizacji zapytań.  

Optymalizator zapytań puli SQL jest Optymalizatorem opartym na kosztach. Porównuje koszt różnych planów zapytań, a następnie wybiera plan z najniższym kosztem. W większości przypadków wybiera plan, który będzie wykonywał najszybszy.

Na przykład, jeśli optymalizator szacuje, że data filtrowania kwerendy zwróci jeden wiersz, wybierze jeden plan. Jeśli szacuje się, że wybrana data zwróci 1 000 000 wierszy, zwróci inny plan.

### <a name="automatic-creation-of-statistics"></a>Automatyczne tworzenie statystyk

Pula SQL będzie analizować przychodzące zapytania użytkownika pod kątem braku statystyk, gdy opcja AUTO_CREATE_STATISTICS bazy danych jest ustawiona na `ON` .  Jeśli brakuje statystyk, optymalizator zapytań tworzy statystyki dla poszczególnych kolumn w predykacie zapytania lub w warunku sprzężenia. 

Ta funkcja służy do poprawiania oszacowania kardynalności dla planu zapytania.

> [!IMPORTANT]
> Automatyczne tworzenie statystyk jest obecnie domyślnie włączone.

Aby sprawdzić, czy magazyn danych ma AUTO_CREATE_STATISTICS skonfigurowany, należy uruchomić następujące polecenie:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Jeśli magazyn danych nie ma włączonej AUTO_CREATE_STATISTICS, zalecamy włączenie tej właściwości, uruchamiając następujące polecenie:

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
> Automatyczne tworzenie statystyk nie jest generowane w tabelach tymczasowych lub zewnętrznych.

Automatyczne tworzenie statystyk jest wykonywane synchronicznie. W związku z tym może wystąpić nieznacznie obniżona wydajność zapytań, jeśli w kolumnach brakuje statystyk. Czas tworzenia statystyk dla pojedynczej kolumny zależy od rozmiaru tabeli.

Aby uniknąć wymiernego obniżenia wydajności, należy upewnić się, że statystyki zostały najpierw utworzone przez wykonanie obciążenia testowego przed przeprowadzeniem profilowania systemu.

> [!NOTE]
> Tworzenie statystyk jest rejestrowane [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) w innym kontekście użytkownika.

Gdy są tworzone automatyczne statystyki, przyjmują one formę: _WA_Sys_ <8 Identyfikator kolumny liczbowej w>_ szesnastkowym<8 identyfikator tabeli cyfrowej w> szesnastkowym. Można wyświetlić już utworzone statystyki poprzez uruchomienie polecenia [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

Table_name to nazwa tabeli zawierającej statystyki do wyświetlenia, które nie mogą być tabelami zewnętrznymi. Obiekt docelowy jest nazwą docelowego indeksu, statystyki lub kolumny, dla którego mają być wyświetlone informacje statystyczne.

### <a name="update-statistics"></a>Aktualizowanie statystyk

Najlepszym rozwiązaniem jest aktualizacja statystyk dla kolumn dat każdego dnia, gdy dodawane są nowe daty. Za każdym razem, gdy nowe wiersze są ładowane do magazynu danych, dodawane są nowe daty ładowania lub daty transakcji. Te dodatki zmieniają dystrybucję danych i statystyki są nieaktualne.

Statystyki w kolumnie kraj lub region w tabeli klienta nigdy nie muszą zostać zaktualizowane, ponieważ dystrybucja wartości nie jest zwykle zmieniana. Przy założeniu, że dystrybucja jest stała między klientami, dodanie nowych wierszy do odmiany tabeli nie powoduje zmiany dystrybucji danych.

Jeśli jednak magazyn danych zawiera tylko jeden kraj lub region i dane pochodzą z nowego kraju lub regionu, należy zaktualizować statystyki w kolumnie kraj lub region.

Poniżej przedstawiono zalecenia dotyczące aktualizowania statystyk:

|||
|-|-|
| **Częstotliwość aktualizacji statystyk**  | Ostrożne: codziennie </br> Po załadowaniu lub przeprowadzeniu przekształcenia danych |
| **Próbkowanie** |  Mniej niż 1 000 000 000 wierszy, Użyj domyślnego próbkowania (20 procent). </br> Mając więcej niż 1 000 000 000 wierszy, użyj próbkowania dwóch procent. |

### <a name="determine-last-statistics-update"></a>Określanie ostatniej aktualizacji statystyk

Jednym z pierwszych pytań, które należy zadać w przypadku rozwiązywania problemów z kwerendą jest **"czy statystyki są aktualne?"**

To pytanie nie jest takie, którego można udzielić odpowiedzi według wieku danych. Aktualny obiekt statystyk może być stary, jeśli nie wprowadzono żadnych istotnych zmian w danych źródłowych. W przypadku zmiany liczby wierszy w istotny sposób lub zmiany materiału w dystrybucji wartości dla kolumny występuje czas na aktualizację statystyk. *then*

Nie ma dostępnego dynamicznego widoku zarządzania, aby określić, czy dane w tabeli uległy zmianie od czasu ostatniego aktualizowania statystyk. Poznanie wieku Twoich statystyk może stanowić część obrazu. 

Przy użyciu następującego zapytania można określić czas ostatniej aktualizacji statystyk w każdej tabeli.

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

**Kolumny dat** w magazynie danych, na przykład zazwyczaj potrzebują częste aktualizacje statystyk. Za każdym razem, gdy nowe wiersze są ładowane do magazynu danych, dodawane są nowe daty ładowania lub daty transakcji. Te dodatki zmieniają dystrybucję danych i statystyki są nieaktualne.

Nie trzeba aktualizować statystyk w kolumnie o płci w tabeli klienta. Przy założeniu, że dystrybucja jest stała między klientami, dodanie nowych wierszy do odmiany tabeli nie powoduje zmiany dystrybucji danych.

Ale jeśli magazyn danych zawiera tylko jedną płeć, a nowe wymaganie ma wiele płci, należy zaktualizować statystyki w kolumnie płeć. 

Aby uzyskać więcej informacji, zapoznaj się z artykułem [statystyki](/sql/relational-databases/statistics/statistics) .

### <a name="implement-statistics-management"></a>Zaimplementuj Zarządzanie statystykami

Często dobrym pomysłem jest rozbudowa procesu ładowania danych w celu zapewnienia, że statystyki są aktualizowane po zakończeniu ładowania. Ładowanie danych jest w przypadku, gdy tabele często zmieniają ich rozmiar, dystrybucję wartości lub obie. W związku z tym proces ładowania jest logicznym miejscem do implementacji niektórych procesów zarządzania.

Następujące zasady dotyczące identyfikatorów GUID są udostępniane do aktualizowania statystyk podczas procesu ładowania:

- Upewnij się, że każda załadowana tabela ma zaktualizowany co najmniej jeden obiekt statystyk. Ten proces aktualizuje informacje o rozmiarze tabeli (liczbie wierszy i liczbie stron) w ramach aktualizacji statystyk.
- Skup się na kolumnach uczestniczących w klauzulach JOIN, GROUP BY, ORDER BY i DISTINCT.
- Warto rozważyć aktualizowanie kolumn "Ascending Key", takich jak daty transakcji, ponieważ te wartości nie zostaną uwzględnione w histogramie statystyki.
- Należy rozważyć częste Aktualizowanie statycznych kolumn dystrybucji.
- Należy pamiętać, że każdy obiekt statystyki jest aktualizowany w kolejności. Po prostu implementacja `UPDATE STATISTICS <TABLE_NAME>` nie zawsze jest idealnym rozwiązaniem, szczególnie w przypadku szerokich tabel zawierających wiele obiektów statystyk.

Aby uzyskać więcej informacji, zobacz [oszacowanie kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Przykłady: Tworzenie statystyk

W poniższych przykładach pokazano, jak używać różnych opcji tworzenia statystyk. Opcje, które są używane dla każdej kolumny, zależą od charakterystyki danych i sposobu użycia kolumny w zapytaniach.

#### <a name="create-single-column-statistics-with-default-options"></a>Tworzenie statystyk z jedną kolumną z opcjami domyślnymi

Aby utworzyć statystyki dla kolumny, podaj nazwę dla obiektu statystyki i nazwę kolumny.
Ta składnia używa wszystkich opcji domyślnych. Domyślnie podczas tworzenia statystyk w puli SQL są pobierane **20 procenty** tabeli.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Przykład:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Utwórz statystyki pojedynczej kolumny, badając każdy wiersz

Domyślna częstotliwość próbkowania 20 procent jest wystarczająca w większości sytuacji. Można jednak dostosować częstotliwość próbkowania. Aby pobrać pełną tabelę, użyj następującej składni:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Przykład:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Utwórz statystyki pojedynczej kolumny, określając rozmiar próbki

Kolejną opcją jest określenie rozmiaru próbki jako procentu:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Utwórz statystyki pojedynczej kolumny tylko dla niektórych wierszy

Możesz również utworzyć statystyki dotyczące części wierszy w tabeli, która jest nazywana przefiltrowanym statystyką.

Można na przykład użyć filtrowanych statystyk, gdy planujesz wykonać zapytanie dotyczące określonej partycji dużej partycjonowanej tabeli. Dzięki tworzeniu statystyk dotyczących tylko wartości partycji, dokładność statystyk zostanie zwiększona. Wystąpi również wzrost wydajności zapytań.

Ten przykład tworzy statystyki dotyczące zakresu wartości. Wartości mogą być łatwo zdefiniowane, aby odpowiadały zakresowi wartości w partycji.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Aby optymalizator zapytań mógł rozważyć użycie filtrowanych statystyk przy wyborze planu zapytania rozproszonego, zapytanie musi pasować do definicji obiektu statystyk. W poprzednim przykładzie klauzula WHERE zapytania musi określać wartości Kol1 z zakresu od 2000101 do 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Utwórz statystyki pojedynczej kolumny ze wszystkimi opcjami

Możesz również połączyć opcje razem. Poniższy przykład tworzy filtrowany obiekt statystyczny z niestandardowym rozmiarem próbki:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Aby uzyskać pełne informacje, zobacz [Tworzenie statystyk](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Tworzenie statystyk wielokolumnowych

Aby utworzyć obiekt statystyk z wieloma kolumnami, należy użyć powyższych przykładów, ale określić więcej kolumn.

> [!NOTE]
> Histogram, który jest używany do oszacowania liczby wierszy w wyniku zapytania, jest dostępny tylko dla pierwszej kolumny wymienionej w definicji obiektu statystyki.

W tym przykładzie histogram znajduje się w *\_ kategorii Product (produkt* ). Statystyki między kolumnami są obliczane na *podstawie \_ kategorii produktu* i *\_ sub_category produktu* :

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Ponieważ istnieje korelacja między *\_ kategorią produktu* a *\_ podrzędną \_ kategorią produktu* , obiekt statystyk wielokolumnowych może być przydatny, jeśli te kolumny są dostępne w tym samym czasie.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Tworzenie statystyk dla wszystkich kolumn w tabeli

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Użyj procedury składowanej, aby utworzyć statystyki dla wszystkich kolumn w bazie danych

Pula SQL nie ma procedury składowanej systemowej równoważnej sp_create_stats w SQL Server. Ta procedura składowana tworzy obiekt statystyki jednokolumnowej dla każdej kolumny bazy danych, która nie ma jeszcze statystyk.

Poniższy przykład pomoże Ci rozpocząć pracę z projektem bazy danych. Śmiało, aby dostosować je do swoich potrzeb:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

Aby utworzyć statystyki dla wszystkich kolumn w tabeli przy użyciu FULLSCAN, Wywołaj tę procedurę:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Aby utworzyć próbkowanie statystyk dla wszystkich kolumn w tabeli, wprowadź 3 i procent próbki. W poniższej procedurze zastosowano częstotliwość próbkowania 20 procent.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Przykłady: aktualizowanie statystyk

Aby zaktualizować dane statystyczne, możesz:

- Aktualizowanie jednego obiektu statystyki. Określ nazwę obiektu statystyki, który chcesz zaktualizować.
- Aktualizowanie wszystkich obiektów statystyk w tabeli. Określ nazwę tabeli zamiast jednego określonego obiektu statystyki.

#### <a name="update-one-specific-statistics-object"></a>Aktualizowanie jednego określonego obiektu statystyki

Aby zaktualizować konkretny obiekt Statystyczny, należy użyć następującej składni:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Przykład:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Dzięki aktualizacji określonych obiektów statystyk można zminimalizować czas i zasoby wymagane do zarządzania statystykami. Ta akcja wymaga zastosowania niektórych opcji wyboru najlepszych obiektów statystyk do zaktualizowania.

#### <a name="update-all-statistics-on-a-table"></a>Aktualizowanie wszystkich statystyk w tabeli

Prostą metodą aktualizowania wszystkich obiektów statystyk w tabeli jest:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Przykład:

```sql
UPDATE STATISTICS dbo.table1;
```

Instrukcja UPDATE STATISTICs jest łatwa w użyciu. Pamiętaj, że aktualizuje *wszystkie* statystyki tabeli, monitując więcej pracy niż jest to konieczne. 

Jeśli wydajność nie jest problemem, ta metoda jest najłatwiejszym i najbardziej kompletnym sposobem na zagwarantowanie Aktualności statystyk.

> [!NOTE]
> Podczas aktualizowania wszystkich statystyk w tabeli usługa SQL Pool wykonuje skanowanie w celu próbkowania tabeli dla każdego obiektu statystyki. Jeśli tabela jest duża i zawiera wiele kolumn i wiele statystyk, może być bardziej wydajna aktualizacja indywidualnych statystyk w zależności od potrzeb.

Aby uzyskać implementację `UPDATE STATISTICS` procedury, zobacz [tabele tymczasowe](develop-tables-temporary.md). Metoda implementacji różni się nieco od poprzedniej `CREATE STATISTICS` procedury, ale wynik jest taki sam.
Pełną składnię można znaleźć w temacie [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="statistics-metadata"></a>Metadane statystyk

Istnieje kilka widoków i funkcji systemu, których można użyć, aby znaleźć informacje o statystyce. Można na przykład sprawdzić, czy obiekt statystyki może być nieaktualny przy użyciu funkcji STATS_DATE (). STATS_DATE () umożliwia sprawdzenie, kiedy statystyki zostały ostatnio utworzone lub zaktualizowane.

#### <a name="catalog-views-for-statistics"></a>Widoki wykazu dla statystyk

Te widoki systemowe zawierają informacje o statystyce:

| Widok wykazu | Opis |
|:--- |:--- |
| [sys. Columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden wiersz dla każdej kolumny. |
| [sys. Objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden wiersz dla każdego obiektu w bazie danych. |
| [sys. schematy](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden wiersz dla każdego schematu w bazie danych. |
| [sys. statystyki](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden wiersz dla każdego obiektu statystyki. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden wiersz dla każdej kolumny w obiekcie Statystyka. Linki z powrotem do widoku sys. Columns. |
| [sys. Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden wiersz dla każdej tabeli (zawiera tabele zewnętrzne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Jeden wiersz dla każdego typu danych. |

#### <a name="system-functions-for-statistics"></a>Funkcje systemowe dla statystyk

Te funkcje systemowe są przydatne do pracy z statystykami:

| Funkcja systemowa | Opis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Data ostatniej aktualizacji obiektu statystyki. |
| [SHOW_STATISTICS DBCC](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Poziom podsumowania i szczegółowe informacje o dystrybucji wartości zrozumiałe dla obiektu Statystyka. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Łączenie kolumn statystyk i funkcji w jeden widok

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>Przykłady polecenia DBCC SHOW_STATISTICS ()

Polecenie DBCC SHOW_STATISTICS () pokazuje dane przechowywane w obiekcie statystyk. Te dane wchodzą w skład trzech części:

- Header
- Wektor gęstości
- Histogram

Nagłówek to metadane dotyczące statystyk. Histogram wyświetla rozkład wartości w pierwszej kolumnie klucza obiektu Statystyka. 

Wektor gęstości mierzy korelację między kolumnami. W puli SQL są obliczane oszacowania kardynalności z dowolnymi danymi w obiekcie Statystyka.

#### <a name="show-header-density-and-histogram"></a>Pokaż nagłówek, gęstość i histogram

Ten prosty przykład przedstawia wszystkie trzy części obiektu statystyki:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Pokaż co najmniej jedną część SHOW_STATISTICS DBCC ()

Jeśli interesuje Cię tylko wyświetlanie określonych części, użyj `WITH` klauzuli i określ, które części mają być widoczne:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Różnice w programie DBCC SHOW_STATISTICS ()

`DBCC SHOW_STATISTICS()` jest bardziej ściśle zaimplementowany w puli SQL w porównaniu do SQL Server:

- Nieudokumentowane funkcje nie są obsługiwane.
- Nie można użyć Stats_stream.
- Nie można dołączyć wyników dla określonych podzestawów danych statystycznych. Na przykład STAT_HEADER JOIN DENSITY_VECTOR.
- Nie można ustawić NO_INFOMSGS do pomijania komunikatów.
- Nie można użyć nawiasów kwadratowych wokół nazw statystyk.
- Nie można używać nazw kolumn do identyfikowania obiektów statystyk.
- Błąd niestandardowy 2767 nie jest obsługiwany.

### <a name="next-steps"></a>Następne kroki

Aby zwiększyć wydajność zapytań, zobacz [monitorowanie obciążenia](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>Statystyki na żądanie SQL (wersja zapoznawcza)

Statystyki są tworzone na określoną kolumnę dla określonego zestawu danych (ścieżka magazynu).

### <a name="why-use-statistics"></a>Dlaczego warto używać statystyk

Im więcej informacji na żądanie (wersja zapoznawcza) dotyczących Twoich danych, tym szybciej można wykonać zapytania. Zbieranie danych statystycznych jest jednym z najważniejszych rzeczy, które można wykonać, aby zoptymalizować zapytania. 

Optymalizator zapytań na żądanie SQL jest Optymalizatorem opartym na kosztach. Porównuje koszt różnych planów zapytań, a następnie wybiera plan z najniższym kosztem. W większości przypadków wybiera plan, który będzie wykonywał najszybszy. 

Na przykład, jeśli optymalizator szacuje, że data filtrowania zapytania zwróci jeden wiersz, wybierze jeden plan. Jeśli szacuje się, że wybrana data zwróci 1 000 000 wierszy, zwróci inny plan.

### <a name="automatic-creation-of-statistics"></a>Automatyczne tworzenie statystyk

SQL na żądanie analizuje przychodzące zapytania użytkownika pod kątem brakujących statystyk. Jeśli brakuje statystyk, optymalizator zapytań tworzy statystyki dla poszczególnych kolumn w predykacie zapytania lub warunek sprzężenia, aby poprawić oszacowania kardynalności dla planu zapytania.

Instrukcja SELECT wyzwoli automatyczne tworzenie statystyk.

> [!NOTE]
> Automatyczne tworzenie statystyk jest włączone dla plików Parquet. W przypadku plików CSV należy ręcznie utworzyć statystyki do momentu, gdy automatyczne tworzenie statystyk plików CSV jest obsługiwane.

Automatyczne tworzenie statystyk jest wykonywane synchronicznie, dzięki czemu może nastąpić nieznacznie obniżenie wydajności zapytań, jeśli w kolumnach brakuje statystyk. Czas tworzenia statystyk dla pojedynczej kolumny zależy od rozmiaru plików, do których są przeznaczone.

### <a name="manual-creation-of-statistics"></a>Ręczne tworzenie statystyk

SQL na żądanie umożliwia ręczne tworzenie statystyk. W przypadku plików CSV należy ręcznie utworzyć statystyki, ponieważ automatyczne tworzenie statystyk nie jest włączone dla plików CSV. 

Zapoznaj się z poniższymi przykładami, aby uzyskać instrukcje dotyczące ręcznego tworzenia statystyk.

### <a name="update-statistics"></a>Aktualizowanie statystyk

Zmiany danych w plikach, usuwaniu i dodawaniu plików powodują zmiany dystrybucji danych i statystyk są nieaktualne. W takim przypadku statystyki należy zaktualizować.

SQL na żądanie automatycznie ponownie tworzy statystyki, jeśli dane są zmieniane znacznie. Za każdym razem, gdy statystyki są tworzone automatycznie, bieżący stan zestawu danych jest również zapisywany: ścieżki plików, rozmiary, daty ostatniej modyfikacji.

Gdy statystyki są przestarzałe, zostaną utworzone nowe. Algorytm przechodzi przez dane i porównuje go z bieżącym stanem zestawu danych. Jeśli rozmiar zmian jest większy niż określony próg, stare statystyki są usuwane i zostaną ponownie utworzone przez nowy zestaw danych.

Statystyki ręczne nigdy nie są deklarowane jako przestarzałe.

> [!NOTE]
> Automatyczne odtwarzanie statystyk jest włączone dla plików Parquet. W przypadku plików CSV należy ręcznie porzucić i utworzyć statystyki do momentu, gdy automatyczne tworzenie statystyk plików CSV jest obsługiwane. Zapoznaj się z poniższymi przykładami dotyczącymi usuwania i tworzenia statystyk.

Jednym z pierwszych pytań, które należy zadać w przypadku rozwiązywania problemów z kwerendą jest **"czy statystyki są aktualne?"**

Gdy liczba wierszy uległa znacznej zmianie lub w dystrybucji wartości dla kolumny istnieje istotna zmiana, *należy ją zaktualizować* .

> [!NOTE]
> W przypadku zmiany materiału w dystrybucji wartości dla kolumny należy zaktualizować statystyki bez względu na czas ostatniej aktualizacji.

### <a name="implement-statistics-management"></a>Zaimplementuj Zarządzanie statystykami

Możesz chcieć wydłużyć Potok danych, aby zapewnić, że statystyki są aktualizowane, gdy dane są znacząco zmieniane poprzez dodanie, usunięcie lub zmianę plików.

Następujące zasady dotyczące identyfikatorów GUID są dostępne do aktualizowania statystyk:

- Upewnij się, że zestaw danych ma zaktualizowany co najmniej jeden obiekt statystyk. Ten rozmiar aktualizacji (liczba wierszy i liczba stron) w ramach aktualizacji statystyk.
- Skup się na kolumnach, w których uczestniczą klauzule WHERE, JOIN, GROUP BY, ORDER BY i DISTINCT.
- Aktualizuj kolumny "Ascending Key", takie jak daty transakcji częściej, ponieważ te wartości nie zostaną uwzględnione w histogramie statystyki.
- Rzadziej Aktualizuj statyczne kolumny dystrybucji.

Aby uzyskać więcej informacji, zobacz [oszacowanie kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Przykłady: Tworzenie statystyk dla kolumny w ścieżce funkcji OPENROWSET

W poniższych przykładach pokazano, jak używać różnych opcji tworzenia statystyk. Opcje, które są używane dla każdej kolumny, zależą od charakterystyki danych i sposobu użycia kolumny w zapytaniach.

> [!NOTE]
> W tej chwili można utworzyć statystyki pojedynczej kolumny.
>
> Następujące uprawnienia są wymagane do wykonania sp_create_openrowset_statistics i sp_drop_openrowset_statistics: ADMINISTROWANie OPERACJAmi ZBIORCZymi lub ADMINISTROWANie OPERACJAmi ZBIORCZymi bazy danych.

Następująca procedura składowana służy do tworzenia statystyk:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argumenty: [ @stmt =] N ' statement_text '-określa instrukcję języka Transact-SQL, która zwróci wartości kolumn, które będą używane na potrzeby statystyk. Aby określić przykłady danych do użycia, można użyć klasy postanowień. Jeśli nie określono parametruexception, zostanie użyta FULLSCAN.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Próbkowanie CSV nie działa w tym czasie, tylko FULLSCAN jest obsługiwane dla woluminów CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Utwórz statystyki pojedynczej kolumny, badając każdy wiersz

Aby utworzyć statystyki dla kolumny, podaj zapytanie, które zwraca kolumnę, dla której potrzebujesz statystyk.

Domyślnie, jeśli nie określisz inaczej, funkcja SQL na żądanie będzie używać 100% danych udostępnionych w zestawie danych podczas tworzenia statystyk.

Na przykład, aby utworzyć statystyki z opcjami domyślnymi (FULLSCAN) dla kolumny Year zestawu danych na podstawie pliku population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Utwórz statystyki pojedynczej kolumny, określając rozmiar próbki

Rozmiar próbki można określić jako wartość procentową:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Przykłady: aktualizowanie statystyk

Aby zaktualizować statystyki, należy porzucić i utworzyć statystyki. Poniższa procedura składowana służy do usuwania statystyk:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Następujące uprawnienia są wymagane do wykonania sp_create_openrowset_statistics i sp_drop_openrowset_statistics: ADMINISTROWANie OPERACJAmi ZBIORCZymi lub ADMINISTROWANie OPERACJAmi ZBIORCZymi bazy danych.

Argumenty: [ @stmt =] N "statement_text" — określa tę samą instrukcję Transact-SQL używaną podczas tworzenia statystyk.

Aby zaktualizować statystyki dla kolumny Year w zestawie danych, która jest oparta na pliku population.csv, należy porzucić i utworzyć statystyki:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Przykłady: Tworzenie statystyk dla kolumny tabeli zewnętrznej

W poniższych przykładach pokazano, jak używać różnych opcji tworzenia statystyk. Opcje, które są używane dla każdej kolumny, zależą od charakterystyki danych i sposobu użycia kolumny w zapytaniach.

> [!NOTE]
> W tej chwili można utworzyć statystyki pojedynczej kolumny.

Aby utworzyć statystyki dla kolumny, podaj nazwę dla obiektu statystyki i nazwę kolumny.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumenty: external_table określa tabelę zewnętrzną, która powinna być utworzona Statystyka.

FULLSCAN statystyk obliczeniowych przez skanowanie wszystkich wierszy. FULLSCAN i przykład 100 procent mają te same wyniki. Nie można używać FULLSCAN z PRZYKŁADową opcją.

PRZYKŁAD numeru PROCENTowego określa przybliżoną wartość procentową lub liczbę wierszy w tabeli lub indeksowanym widoku dla optymalizatora zapytań, który ma być używany podczas tworzenia statystyk. Liczba może mieć wartość od 0 do 100.

Nie można użyć próbki z opcją FULLSCAN.

> [!NOTE]
> Próbkowanie CSV nie działa w tym czasie, tylko FULLSCAN jest obsługiwane dla woluminów CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Utwórz statystyki pojedynczej kolumny, badając każdy wiersz

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Utwórz statystyki pojedynczej kolumny, określając rozmiar próbki

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Przykłady: aktualizowanie statystyk

Aby zaktualizować statystyki, należy porzucić i utworzyć statystyki. Najpierw Porzuć statystyki:

```sql
DROP STATISTICS census_external_table.sState
```

I Utwórz statystyki:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dalsze ulepszenia wydajności zapytań, zobacz [najlepsze rozwiązania dla puli SQL](best-practices-sql-pool.md#maintain-statistics).
