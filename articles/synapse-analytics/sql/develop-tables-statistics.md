---
title: Tworzenie, aktualizowanie statystyk
description: Zalecenia i przykłady tworzenia i aktualizowania statystyk optymalizacji zapytań w synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 5196c85ca1d68028893caee55035c6c455b37d64
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676932"
---
# <a name="statistics-in-synapse-sql"></a>Statystyki w Synapse SQL

W tym artykule przedstawiono zalecenia i przykłady tworzenia i aktualizowania statystyk optymalizacji zapytań przy użyciu zasobów SQL Synapse: puli SQL i SQL na żądanie (wersja zapoznawcza).

## <a name="statistics-in-sql-pool"></a>Statystyki w puli SQL

### <a name="why-use-statistics"></a>Dlaczego warto korzystać ze statystyk

Im więcej zasób puli SQL wie o danych, tym szybciej może wykonywać kwerendy. Po załadowaniu danych do puli SQL zbieranie statystyk dotyczących danych jest jedną z najważniejszych czynności, które można wykonać w celu optymalizacji zapytań.  

Optymalizator zapytań puli SQL jest optymalizatorem opartym na kosztach. Porównuje koszt różnych planów zapytań, a następnie wybiera plan z najniższym kosztem. W większości przypadków wybiera plan, który wykona najszybciej.

Na przykład jeśli optymalizator szacuje, że data kwerendy jest filtrowanie na zwróci jeden wiersz wybierze jeden plan. Jeśli szacuje, że wybrana data zwróci 1 milion wierszy, zwróci inny plan.

### <a name="automatic-creation-of-statistics"></a>Automatyczne tworzenie statystyk

Pula SQL przeanalizuje przychodzące zapytania użytkowników pod kątem brakujących statystyk, gdy baza danych AUTO_CREATE_STATISTICS opcja jest ustawiona na `ON`.  Jeśli brakuje statystyk, optymalizator kwerendy tworzy statystyki poszczególnych kolumn w predykacie kwerendy lub warunku sprzężenia. Ta funkcja jest używana do poprawy oszacowań kardynalności dla planu kwerend.

> [!IMPORTANT]
> Automatyczne tworzenie statystyk jest obecnie domyślnie włączone.

Można sprawdzić, czy magazyn danych AUTO_CREATE_STATISTICS skonfigurowany, uruchamiając następujące polecenie:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Jeśli magazyn danych nie ma włączonej AUTO_CREATE_STATISTICS, zaleca się włączenie tej właściwości, uruchamiając następujące polecenie:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Oświadczenia te będą powodować automatyczne tworzenie statystyk:

- SELECT
- WSTAW-WYBIERZ
- CTAS
- UPDATE
- DELETE
- WYJAŚNIJ, kiedy znajduje się sprzężenie lub wykryto obecność predykatu

> [!NOTE]
> Automatyczne tworzenie statystyk nie jest generowane w tabelach tymczasowych lub zewnętrznych.

Automatyczne tworzenie statystyk odbywa się synchronicznie. Tak więc może ponieść nieco obniżoną wydajność kwerendy, jeśli kolumny brakuje statystyk. Czas tworzenia statystyk dla pojedynczej kolumny zależy od rozmiaru tabeli.

Aby uniknąć mierzalnej degradacji wydajności, należy upewnić się, że statystyki zostały utworzone najpierw przez wykonanie obciążenia testu porównawczego przed profilowanie systemu.

> [!NOTE]
> Tworzenie statystyk jest rejestrowane w [pliku sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w innym kontekście użytkownika.

Podczas tworzenia statystyk automatycznych będą one miały formę: _WA_Sys_<8-cyfrowy identyfikator kolumny w hex>_<8-cyfrowym identyfikatorze tabeli w> Hex. Możesz wyświetlić już utworzone statystyki, uruchamiając polecenie [DBCC SHOW_STATISTICS:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name to nazwa tabeli zawierającej statystyki do wyświetlenia, która nie może być tabelą zewnętrzną. Obiekt docelowy to nazwa docelowego indeksu, statystyk lub kolumny, dla której mają być wyświetlane informacje statystyczne.

### <a name="update-statistics"></a>Aktualizowanie statystyk

Jedną z najlepszych praktyk jest aktualizowanie statystyk kolumn daty każdego dnia w miarę dodawania nowych dat. Za każdym razem, gdy nowe wiersze są ładowane do magazynu danych, dodawane są nowe daty ładowania lub daty transakcji. Te dodatki zmieniają dystrybucję danych i sprawiają, że statystyki są nieaktualne.

Statystyki dotyczące kolumny kraju lub regionu w tabeli klienta mogą nigdy nie wymagać aktualizacji, ponieważ rozkład wartości zwykle się nie zmienia. Zakładając, że dystrybucja jest stała między klientami, dodawanie nowych wierszy do odmiany tabeli nie zmieni dystrybucji danych.

Jeśli jednak magazyn danych zawiera tylko jeden kraj lub region i przynosisz dane z nowego kraju lub regionu, musisz zaktualizować statystyki dotyczące kolumny kraju lub regionu.

Poniżej przedstawiono zalecenia aktualizujące statystyki:

|||
|-|-|
| **Częstotliwość aktualizacji statystyk**  | Konserwatywny: Codziennie </br> Po załadowaniu lub przekształceniu danych |
| **Próbkowanie** |  Mniej niż 1 miliard wierszy, użyj domyślnego próbkowania (20 procent). </br> Z ponad 1 miliard wierszy, użyj próbkowania dwóch procent. |

### <a name="determine-last-statistics-update"></a>Określanie ostatniej aktualizacji statystyk

Jednym z pierwszych pytań, które należy zadać podczas rozwiązywania problemów z kwerendą, jest **"Czy statystyki są aktualne?"**

To pytanie nie jest pytanie, które można odpowiedzieć na wiek danych. Aktualny obiekt statystyk może być stary, jeśli nie nastąpiła żadna istotna zmiana danych źródłowych. Gdy liczba wierszy uległa znacznej zmianie lub nastąpi istotna zmiana w dystrybucji wartości dla *kolumny,* nadszedł czas, aby zaktualizować statystyki.

Nie ma dostępnego dynamicznego widoku zarządzania, aby określić, czy dane w tabeli uległy zmianie od czasu ostatniej aktualizacji statystyk. Znajomość wieku statystyk może zapewnić Ci część obrazu. Za pomocą następującej kwerendy można określić ostatni czas aktualizowanie statystyk w każdej tabeli.

> [!NOTE]
> Jeśli nastąpiła istotna zmiana w dystrybucji wartości dla kolumny, należy zaktualizować statystyki niezależnie od czasu ostatniej aktualizacji.

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

**Kolumny dat** w magazynie danych, na przykład, zwykle wymagają częstych aktualizacji statystyk. Za każdym razem, gdy nowe wiersze są ładowane do magazynu danych, dodawane są nowe daty ładowania lub daty transakcji. Te dodatki zmieniają dystrybucję danych i sprawiają, że statystyki są nieaktualne.

Statystyki dotyczące kolumny płci w tabeli klienta mogą nigdy nie wymagać aktualizacji. Zakładając, że dystrybucja jest stała między klientami, dodawanie nowych wierszy do odmiany tabeli nie zmieni dystrybucji danych.

Jeśli jednak magazyn danych zawiera tylko jedną płeć, a nowe wymaganie powoduje wiele płci, musisz zaktualizować statystyki dotyczące kolumny płci. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Statystyka.](/sql/relational-databases/statistics/statistics)

### <a name="implementing-statistics-management"></a>Wdrażanie zarządzania statystykami

Często warto rozszerzyć proces ładowania danych, aby upewnić się, że statystyki są aktualizowane na końcu obciążenia. Obciążenie danych jest, gdy tabele najczęściej zmieniają ich rozmiar, rozkład wartości lub obu. W związku z tym proces ładowania jest logicznym miejscem do zaimplementowania niektórych procesów zarządzania.

Następujące zasady przewodnie są przewidziane do aktualizacji statystyk podczas procesu ładowania:

- Upewnij się, że każda załadowana tabela ma co najmniej jeden obiekt statystyk zaktualizowany. Ten proces aktualizuje informacje o rozmiarze tabeli (liczba wierszy i liczba stron) w ramach aktualizacji statystyk.
- Skoncentruj się na kolumnach uczestniczących w klauzulach JOIN, GROUP BY, ORDER BY i DISTINCT.
- Należy rozważyć aktualizowanie kolumn "klucz rosnąco", takich jak daty transakcji częściej, ponieważ te wartości nie zostaną uwzględnione w histogramie statystyk.
- Należy rozważyć aktualizowanie statycznych kolumn dystrybucji rzadziej.
- Pamiętaj, że każdy obiekt statystyczny jest aktualizowany w kolejności. Samo wdrożenie `UPDATE STATISTICS <TABLE_NAME>` nie zawsze jest idealne, szczególnie w przypadku szerokich tabel z dużą ilością obiektów statystycznych.

Aby uzyskać więcej informacji, zobacz [Oszacowanie kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Przykłady: Tworzenie statystyk

Te przykłady pokazują, jak używać różnych opcji do tworzenia statystyk. Opcje używane dla każdej kolumny zależą od właściwości danych i sposobu, w jaki kolumna będzie używana w kwerendach.

#### <a name="create-single-column-statistics-with-default-options"></a>Tworzenie statystyk jednokolumnowych z opcjami domyślnymi

Aby utworzyć statystyki w kolumnie, podaj nazwę obiektu statystyk i nazwę kolumny.
Ta składnia używa wszystkich opcji domyślnych. Domyślnie pula SQL próbki **20 procent** tabeli podczas tworzenia statystyk.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Przykład:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Tworzenie statystyk jednokolumnowych przez badanie każdego wiersza

Domyślna częstotliwość próbkowania 20 procent jest wystarczająca w większości sytuacji. Można jednak dostosować częstotliwość próbkowania. Aby pobrać próbkę pełnej tabeli, użyj następującej składni:

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

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Tworzenie statystyk jednokolumnowych przez określenie rozmiaru próbki

Inną opcją jest określenie rozmiaru próbki jako procentu:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Tworzenie statystyk jednokolumnowych tylko dla niektórych wierszy

Można również utworzyć statystyki na części wierszy w tabeli, która jest nazywana filtrowana statystyka.

Na przykład można użyć filtrowanych statystyk, gdy planujesz kwerendy określonej partycji dużej tabeli podzielonej na partycje. Tworząc statystyki tylko wartości partycji, dokładność statystyk poprawi. Zostanie również poprawa wydajności kwerendy.

W tym przykładzie tworzy statystyki dotyczące zakresu wartości. Wartości można łatwo zdefiniować, aby dopasować zakres wartości w partycji.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Aby optymalizator kwerendy rozważył użycie filtrowanych statystyk, gdy wybierze plan kwerend rozproszonych, kwerenda musi zmieścić się w definicji obiektu statystyk. Przy użyciu poprzedniego przykładu kwerendy WHERE klauzuli musi określić wartości col1 między 2000101 i 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Tworzenie statystyk jednokolumnowych ze wszystkimi opcjami

Można również połączyć opcje razem. Poniższy przykład tworzy filtrowany obiekt statystyk o niestandardowym rozmiarze próbki:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Aby uzyskać pełne informacje, zobacz [TWORZENIE STATYSTYK](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="create-multi-column-statistics"></a>Tworzenie statystyk wielokolumnowych

Aby utworzyć obiekt statystyk wielokolumnowych, użyj poprzednich przykładów, ale określ więcej kolumn.

> [!NOTE]
> Histogram, który jest używany do szacowania liczby wierszy w wyniku kwerendy, jest dostępny tylko dla pierwszej kolumny wymienionej w definicji obiektu statystyk.

W tym przykładzie histogram znajduje się w *kategorii produktu\_*. Statystyki międzykolumnowe są obliczane na *podstawie kategorii produktów\_* i sub_category *\_produktu:*

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Ponieważ istnieje korelacja między *\_kategorią produktu* a *\_podkategorią\_produktu,* wielokolumnowy obiekt statystyk może być przydatny, jeśli te kolumny są dostępne w tym samym czasie.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Tworzenie statystyk wszystkich kolumn w tabeli

Jednym ze sposobów tworzenia statystyk jest wydanie poleceń CREATE STATISTICS po utworzeniu tabeli:

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Tworzenie statystyk wszystkich kolumn w bazie danych za pomocą procedury składowanej

Pula SQL nie ma procedury składowanej systemu równoważnej sp_create_stats w programie SQL Server. Ta procedura składowana tworzy obiekt statystyk pojedynczej kolumny w każdej kolumnie bazy danych, która nie ma jeszcze statystyk.
Poniższy przykład pomoże Ci rozpocząć pracę z projektem bazy danych. Możesz dostosować go do swoich potrzeb:

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

Aby utworzyć statystyki dla wszystkich kolumn w tabeli przy użyciu wartości domyślnych, należy wykonać procedurę składowaną.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Aby utworzyć statystyki wszystkich kolumn w tabeli przy użyciu fullscan, należy wywołać tę procedurę:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Aby utworzyć próbkowanie statystyk dla wszystkich kolumn w tabeli, wprowadź 3 i procent próbki. Poniższa procedura wykorzystuje 20 procent próbki.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Przykłady: Aktualizowanie statystyk

Aby zaktualizować statystyki, można:

- Zaktualizuj jeden obiekt statystyk. Określ nazwę obiektu statystyk, który chcesz zaktualizować.
- Aktualizowanie wszystkich obiektów statystyk w tabeli. Określ nazwę tabeli zamiast jednego określonego obiektu statystyk.

#### <a name="update-one-specific-statistics-object"></a>Aktualizowanie jednego konkretnego obiektu statystyk

Aby zaktualizować określony obiekt statystyk, użyj następującej składni:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Przykład:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Aktualizując określone obiekty statystyk, można zminimalizować czas i zasoby wymagane do zarządzania statystykami. Ta akcja wymaga pewnego przemyślenia w celu wybrania najlepszych obiektów statystyk do zaktualizowania.

#### <a name="update-all-statistics-on-a-table"></a>Aktualizowanie wszystkich statystyk w tabeli

Prostą metodą aktualizowania wszystkich obiektów statystyk w tabeli jest:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Przykład:

```sql
UPDATE STATISTICS dbo.table1;
```

Instrukcja UPDATE STATISTICS jest łatwa w użyciu. Pamiętaj tylko, że aktualizuje *wszystkie* statystyki w tabeli, monitując więcej pracy niż jest to konieczne. Jeśli wydajność nie jest problemem, ta metoda jest najprostszym i najbardziej kompletnym sposobem zagwarantowania, że statystyki są aktualne.

> [!NOTE]
> Podczas aktualizowania wszystkich statystyk w tabeli, pula SQL wykonuje skanowanie, aby próbkować tabelę dla każdego obiektu statystyk. Jeśli tabela jest duża i zawiera wiele kolumn i wiele statystyk, może być bardziej efektywne aktualizowanie poszczególnych statystyk w zależności od potrzeb.

Aby zapoznać `UPDATE STATISTICS` się z implementacją procedury, zobacz [Tabele tymczasowe](develop-tables-temporary.md). Metoda implementacji różni się nieco `CREATE STATISTICS` od poprzedniej procedury, ale wynik jest taki sam.
Aby uzyskać pełną składnię, zobacz [Aktualizowanie statystyk](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="statistics-metadata"></a>Metadane statystyk

Istnieje kilka widoków systemu i funkcji, których można użyć do znalezienia informacji o statystykach. Na przykład można sprawdzić, czy obiekt statystyk może być nieaktualny za pomocą funkcji STATS_DATE(). STATS_DATE() umożliwia sprawdzenie, kiedy statystyki zostały ostatnio utworzone lub zaktualizowane.

#### <a name="catalog-views-for-statistics"></a>Widoki wykazu dla statystyk

Te widoki systemowe zawierają informacje o statystykach:

| Widok katalogu | Opis |
|:--- |:--- |
| [Sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdej kolumny. |
| [Sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego obiektu w bazie danych. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego schematu w bazie danych. |
| [Sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego obiektu statystyk. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdej kolumny w obiekcie statystyk. Linki z powrotem do sys.columns. |
| [Sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdej tabeli (zawiera tabele zewnętrzne). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Jeden wiersz dla każdego typu danych. |

#### <a name="system-functions-for-statistics"></a>Funkcje systemowe dla statystyk

Te funkcje systemowe są przydatne do pracy ze statystykami:

| Funkcja systemowa | Opis |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Data ostatniej aktualizacji obiektu statystyk. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Poziom podsumowania i szczegółowe informacje o podziale wartości rozumianych przez obiekt statystyk. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Łączenie kolumn i funkcji statystyk w jeden widok

Ten widok łączy kolumny, które odnoszą się do statystyk i wyników funkcji STATS_DATE().

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

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() przykłady

DBCC SHOW_STATISTICS() pokazuje dane przechowywane w obiekcie statystyk. Dane te są dostępne w trzech częściach:

- Nagłówek
- Wektor gęstości
- Histogram

Nagłówek jest metadane dotyczące statystyk. Histogram wyświetla rozkład wartości w pierwszej kolumnie klucza obiektu statystyk. Wektor gęstości mierzy korelację między kolumnami. Pula SQL oblicza szacunki kardynalności z dowolnymi danymi w obiekcie statystyk.

#### <a name="show-header-density-and-histogram"></a>Pokaż nagłówek, gęstość i histogram

W tym prostym przykładzie przedstawiono wszystkie trzy części obiektu statystyk:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Pokaż jedną lub więcej części DBCC SHOW_STATISTICS()

Jeśli interesuje Cię tylko przeglądanie określonych `WITH` części, użyj tej klauzuli i określ, które części chcesz wyświetlić:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Przykład:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Różnice DBCC SHOW_STATISTICS()

`DBCC SHOW_STATISTICS()`jest bardziej rygorystycznie realizowane w puli SQL w porównaniu do programu SQL Server:

- Nieudokumentowane funkcje nie są obsługiwane.
- Nie można użyć Stats_stream.
- Nie można dołączyć do wyników dla określonych podzbiorów danych statystycznych. Na przykład STAT_HEADER DENSITY_VECTOR JOIN.
- NO_INFOMSGS nie można ustawić do tłumienia wiadomości.
- Nie można używać nawiasów kwadratowych wokół nazw statystyk.
- Nie można używać nazw kolumn do identyfikowania obiektów statystyk.
- Błąd niestandardowy 2767 nie jest obsługiwany.

### <a name="next-steps"></a>Następne kroki

Aby zwiększyć wydajność zapytań, zobacz [Monitorowanie obciążenia](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>Statystyki w języku SQL na żądanie (wersja zapoznawcza)

Statystyki są tworzone dla określonej kolumny dla określonego zestawu danych (ścieżka magazynu).

### <a name="why-use-statistics"></a>Dlaczego warto korzystać ze statystyk

Im więcej sql na żądanie (wersja zapoznawcza) wie o danych, tym szybciej można wykonywać zapytania przeciwko niemu. Zbieranie statystyk dotyczących danych jest jedną z najważniejszych rzeczy, które możesz zrobić, aby zoptymalizować swoje zapytania. Optymalizator zapytań SQL na żądanie jest optymalizatorem opartym na kosztach. Porównuje koszt różnych planów zapytań, a następnie wybiera plan z najniższym kosztem. W większości przypadków wybiera plan, który wykona najszybciej. Na przykład jeśli optymalizator szacuje, że data kwerendy jest filtrowanie na zwróci jeden wiersz wybierze jeden plan. Jeśli szacuje, że wybrana data zwróci 1 milion wierszy, zwróci inny plan.

### <a name="automatic-creation-of-statistics"></a>Automatyczne tworzenie statystyk

SQL na żądanie analizuje przychodzące zapytania użytkowników pod kątem brakujących statystyk. Jeśli brakuje statystyk, optymalizator kwerendy tworzy statystyki poszczególnych kolumn w predykacie kwerendy lub warunek sprzężenia w celu poprawy oszacowań kardynalności dla planu kwerend.

Instrukcja SELECT wyzwoli automatyczne tworzenie statystyk.

> [!NOTE]
> Automatyczne tworzenie statystyk jest włączone dla plików Parkietu. W przypadku plików CSV należy ręcznie tworzyć statystyki, dopóki nie zostanie obsługiwane automatyczne tworzenie statystyk plików CSV.

Automatyczne tworzenie statystyk odbywa się synchronicznie, więc może wystąpić nieco obniżona wydajność kwerendy, jeśli w kolumnach brakuje statystyk. Czas tworzenia statystyk dla pojedynczej kolumny zależy od rozmiaru plików, na które kierowane.

### <a name="manual-creation-of-statistics"></a>Ręczne tworzenie statystyk

SQL na żądanie umożliwia ręczne tworzenie statystyk. W przypadku plików CSV musisz ręcznie utworzyć statystyki, ponieważ automatyczne tworzenie statystyk nie jest włączone dla plików CSV. Zapoznaj się z poniższymi przykładami, aby uzyskać instrukcje dotyczące ręcznego tworzenia statystyk.

### <a name="updating-statistics"></a>Aktualizowanie statystyk

Zmiany danych w plikach, usuwanie i dodawanie plików powodują zmiany w dystrybucji danych i sprawiają, że statystyki są nieaktualne. W takim przypadku statystyki muszą zostać zaktualizowane.

SQL na żądanie automatycznie odtwarza statystyki, jeśli dane zostały znacznie zmienione. Za każdym razem, gdy statystyki są tworzone automatycznie, zapisywany jest również bieżący stan zestawu danych: ścieżki plików, rozmiary, daty ostatniej modyfikacji.

Gdy statystyki są przestarzałe, zostaną utworzone nowe. Algorytm przechodzi przez dane i porównuje je z bieżącym stanem zestawu danych. Jeśli rozmiar zmian jest większy niż określony próg, stare statystyki są usuwane i zostaną ponownie utworzone zaoczną nad nowym zestawem danych.

Statystyki ręczne nigdy nie są deklarowane jako przestarzałe.

> [!NOTE]
> Automatyczne odtworzenie statystyk jest włączone dla plików Parkietu. W przypadku plików CSV musisz ręcznie upuścić i utworzyć statystyki, dopóki nie zostanie obsługiwane automatyczne tworzenie statystyk plików CSV. Sprawdź poniższe przykłady dotyczące sposobu upuszczania i tworzenia statystyk.

Jednym z pierwszych pytań, które należy zadać podczas rozwiązywania problemów z kwerendą, jest **"Czy statystyki są aktualne?"**

Gdy liczba wierszy uległa znacznej zmianie lub nastąpi istotna zmiana w dystrybucji wartości dla *kolumny,* nadszedł czas, aby zaktualizować statystyki.

> [!NOTE]
> Jeśli nastąpiła istotna zmiana w dystrybucji wartości dla kolumny, należy zaktualizować statystyki niezależnie od czasu ostatniej aktualizacji.

### <a name="implementing-statistics-management"></a>Wdrażanie zarządzania statystykami

Można rozszerzyć potok danych, aby upewnić się, że statystyki są aktualizowane, gdy dane są znacznie zmieniane przez dodawanie, usuwanie lub zmianę plików.

Do aktualizacji statystyk podano następujące zasady przewodnie:

- Upewnij się, że zestaw danych ma co najmniej jeden obiekt statystyki zaktualizowane. Ta aktualizacja rozmiaru (liczba wierszy i liczba stron) jest częścią aktualizacji statystyk.
- Skoncentruj się na kolumnach uczestniczących w klauzulach JOIN, GROUP BY, ORDER BY i DISTINCT.
- Aktualizuj kolumny "klucz rosnący", takie jak daty transakcji częściej, ponieważ te wartości nie zostaną uwzględnione w histogramie statystyk.
- Rzadszy okres aktualizacji kolumn dystrybucji statycznej.

Aby uzyskać więcej informacji, zobacz [Oszacowanie kardynalności](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Przykłady: Tworzenie statystyk dla kolumny w ścieżce OPENROWSET

Poniższe przykłady pokazują, jak używać różnych opcji do tworzenia statystyk. Opcje używane dla każdej kolumny zależą od właściwości danych i sposobu, w jaki kolumna będzie używana w kwerendach.

> [!NOTE]
> Statystyki jednokolumnowe można tworzyć tylko w tej chwili.

Do tworzenia statystyk używana jest następująca procedura składowana:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Argumenty: @stmt [ = ] N'statement_text' - Określa instrukcję Transact-SQL, która zwróci wartości kolumn, które mają być używane dla statystyk. Za pomocą TABELAMPLE można określić próbki danych, które mają być używane. Jeśli TABELAAMPLE nie jest określony, fullscan będzie używany.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Próbkowanie CSV nie działa w tej chwili, tylko FULLSCAN jest obsługiwany dla CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Tworzenie statystyk jednokolumnowych przez badanie każdego wiersza

Aby utworzyć statystyki w kolumnie, podaj kwerendę, która zwraca kolumnę, dla której potrzebne są statystyki.

Domyślnie, jeśli nie określisz inaczej, SQL na żądanie używa 100% danych podanych w zestawie danych podczas tworzenia statystyk.

Aby na przykład utworzyć statystyki z opcjami domyślnymi (FULLSCAN) dla kolumny roku zestawu danych na podstawie pliku population.csv:

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

EXEC sys.sp_create_file_statistics N'SELECT year
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

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Tworzenie statystyk jednokolumnowych przez określenie rozmiaru próbki

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

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Przykłady: Aktualizowanie statystyk

Aby zaktualizować statystyki, musisz upuścić i utworzyć statystyki. Następująca procedura składowana służy do upuszczania statystyk:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

Argumenty: @stmt [ = ] N'statement_text' - Określa tę samą instrukcję Transact-SQL używaną podczas tworzenia statystyk.

Aby zaktualizować statystyki dla kolumny roku w zestawie danych, która jest oparta na pliku population.csv, należy upuścić i utworzyć statystyki:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
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

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Przykłady: Tworzenie statystyk dla kolumny tabeli zewnętrznej

Poniższe przykłady pokazują, jak używać różnych opcji do tworzenia statystyk. Opcje używane dla każdej kolumny zależą od właściwości danych i sposobu, w jaki kolumna będzie używana w kwerendach.

> [!NOTE]
> Statystyki jednokolumnowe można tworzyć tylko w tej chwili.

Aby utworzyć statystyki w kolumnie, podaj nazwę obiektu statystyk i nazwę kolumny.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumenty: external_table Określa tabelę zewnętrzną, w których powinny być tworzone statystyki.

STATYSTYKI FULLSCAN Compute przez skanowanie wszystkich wierszy. FULLSCAN i SAMPLE 100 PROCENT mają takie same wyniki. Program FULLSCAN nie może być używany z opcją PRÓBKA.

LICZBA PRÓBKOWANIA PROCENT Określa przybliżoną wartość procentową lub liczbę wierszy w tabeli lub widoku indeksowanym dla optymalizatora kwerendy, który ma być używany podczas tworzenia statystyk. Liczba może być od 0 do 100.

Próbki nie można używać z opcją FULLSCAN.

> [!NOTE]
> Próbkowanie CSV nie działa w tej chwili, tylko FULLSCAN jest obsługiwany dla CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Tworzenie statystyk jednokolumnowych przez badanie każdego wiersza

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Tworzenie statystyk jednokolumnowych przez określenie rozmiaru próbki

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Przykłady: Aktualizowanie statystyk

Aby zaktualizować statystyki, musisz upuścić i utworzyć statystyki. Najpierw upuść statystyki:

```sql
DROP STATISTICS census_external_table.sState
```

I tworzyć statystyki:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dalsze ulepszenia wydajności kwerend, zobacz [Najważniejsze wskazówki dotyczące puli SQL](best-practices-sql-pool.md#maintain-statistics).
