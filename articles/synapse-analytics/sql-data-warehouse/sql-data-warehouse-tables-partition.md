---
title: Partycjonowanie tabel
description: Zalecenia i przykłady dotyczące korzystania z partycji tabeli w dedykowanej puli SQL
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8a59c24100b433719ccfd3a9ea1b6a676695d381
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673438"
---
# <a name="partitioning-tables-in-dedicated-sql-pool"></a>Partycjonowanie tabel w dedykowanej puli SQL

Zalecenia i przykłady dotyczące korzystania z partycji tabeli w dedykowanej puli SQL.

## <a name="what-are-table-partitions"></a>Co to są partycje tabeli?

Partycje tabel umożliwiają dzielenie danych na mniejsze grupy danych. W większości przypadków partycje tabeli są tworzone w kolumnie Date. Partycjonowanie jest obsługiwane we wszystkich dedykowanych typach tabel puli SQL; uwzględnienie klastrowanego magazynu kolumn, klastrowanego indeksu i sterty. Partycjonowanie jest również obsługiwane dla wszystkich typów dystrybucji, w tym rozdystrybuowanych zarówno wartości skrótu, jak i działania okrężnego.  

Partycjonowanie może korzystać z konserwacji danych i wydajności zapytań. Niezależnie od tego, czy program korzysta z usługi, czy tylko jeden, zależy od tego, jak dane są ładowane i czy ta sama kolumna może być używana do obu celów, ponieważ partycjonowanie można wykonać tylko w jednej kolumnie.

### <a name="benefits-to-loads"></a>Zalety ładowania

Główną zaletą partycjonowania w dedykowanej puli SQL jest poprawa wydajności i wydajności ładowania danych przy użyciu usuwania partycji, przełączania i scalania. W większości przypadków dane są partycjonowane w kolumnie dat, która jest ściśle związana z kolejnością, w której dane są ładowane do puli SQL. Jedną z największych korzyści wynikających z używania partycji do obsługi danych jest unikanie rejestrowania transakcji. Podczas gdy po prostu Wstawianie, aktualizowanie lub usuwanie danych może być najbardziej prostym podejściem, przy użyciu partycjonowania podczas procesu ładowania może znacznie poprawić wydajność.

Przełączanie partycji może służyć do szybkiego usuwania lub zastępowania sekcji tabeli.  Na przykład tabela faktów sprzedaży może zawierać tylko dane dla ostatnich 36 miesięcy. Na koniec każdego miesiąca z tabeli jest usuwany najstarszy miesiąc danych sprzedaży.  Te dane można usunąć za pomocą instrukcji DELETE w celu usunięcia danych z najstarszego miesiąca. 

Jednak usunięcie dużej ilości danych wiersz po wierszu z instrukcją DELETE może zająć zbyt dużo czasu, a także utworzyć ryzyko związane z dużymi transakcjami, które trwają do wycofania, jeśli wystąpił problem. Najlepszym rozwiązaniem jest porzucenie najstarszej partycji danych. Jeśli usunięcie pojedynczych wierszy może zająć kilka godzin, usunięcie całej partycji może potrwać kilka sekund.

### <a name="benefits-to-queries"></a>Zalety zapytań

Partycjonowanie może być również używane w celu zwiększenia wydajności zapytań. Zapytanie, które stosuje filtr do danych partycjonowanych, może ograniczyć skanowanie tylko do partycji kwalifikujących. Ta metoda filtrowania umożliwia uniknięcie pełnego skanowania tabeli i skanowanie mniejszego podzestawu danych. Wprowadzając klastrowane indeksy magazynu kolumn, korzyści wynikające z wydajności eliminacji predykatu są mniej korzystne, ale w niektórych przypadkach może być korzystne dla zapytań. 

Na przykład jeśli tabela faktów sprzedaży jest podzielona na 36 miesięcy przy użyciu pola Data sprzedaży, kwerendy filtrujące datę sprzedaży mogą pominąć wyszukiwanie w partycjach, które nie są zgodne z filtrem.

## <a name="sizing-partitions"></a>Ustalanie wielkości partycji

Podczas partycjonowania można użyć w celu zwiększenia wydajności niektórych scenariuszy, ale utworzenie tabeli z **zbyt dużą liczbą** partycji może obniżyć wydajność w pewnych okolicznościach.  Te problemy są szczególnie prawdziwe w przypadku klastrowanych tabel magazynu kolumn. 

Aby partycjonowanie było pomocne, należy zrozumieć, kiedy należy używać partycjonowania i liczby partycji do utworzenia. Nie ma żadnej twardej szybkiej reguły, jak wiele partycji jest zbyt wiele, zależy od danych i liczby przechowywanych jednocześnie partycji. Pomyślne schematy partycjonowania zazwyczaj mają dziesiątki do setek partycji, a nie tysięcy.

Podczas tworzenia partycji w **klastrowanych tabelach magazynu kolumn** należy wziąć pod uwagę liczbę wierszy należących do każdej partycji. Aby zapewnić optymalną kompresję i wydajność klastrowanych tabel magazynu kolumn, wymagany jest co najmniej 1 000 000 wierszy na dystrybucję i partycję. Przed utworzeniem partycji dedykowana Pula SQL jest już dzielona każdej tabeli do 60 rozproszonych baz danych. 

Każde partycjonowanie dodawane do tabeli jest uzupełnieniem dystrybucji utworzonych w tle. Korzystając z tego przykładu, jeśli tabela faktów sprzedaży zawiera 36 partycji miesięcznych i że dedykowana Pula SQL ma następującą liczbę dystrybucji 60, tabela faktów sprzedaży powinna zawierać 60 000 000 wierszy miesięcznie lub 2 100 000 000 wierszy, gdy wszystkie miesiące są wypełnione. Jeśli tabela zawiera mniej niż zalecaną minimalną liczbę wierszy na partycję, rozważ użycie mniejszej liczby partycji w celu zwiększenia liczby wierszy na partycję. 

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [indeksowania](sql-data-warehouse-tables-index.md) , w tym kwerendy, które mogą ocenić jakość indeksów magazynu kolumn klastra.

## <a name="syntax-differences-from-sql-server"></a>Różnice składniowe z SQL Server

Dedykowana Pula SQL wprowadza sposób definiowania partycji, które są prostsze niż SQL Server. Funkcje i schematy partycjonowania nie są używane w dedykowanej puli SQL, ponieważ znajdują się w SQL Server. Zamiast tego należy tylko określić kolumnę partycjonowaną i punkty graniczne. 

Chociaż składnia partycjonowania może się nieco różnić od SQL Server, podstawowe koncepcje są takie same. SQL Server i dedykowana Pula SQL obsługuje jedną kolumnę partycji na tabelę, która może być partycją z zakresem. Aby dowiedzieć się więcej na temat partycjonowania, zobacz [partycjonowane tabele i indeksy](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Poniższy przykład używa instrukcji [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby podzielić tabelę FactInternetSales na kolumnę OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrowanie partycjonowania z SQL Server

Aby migrować definicje partycji SQL Server do dedykowanej puli SQL po prostu:

- Usuń [schemat partycji](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)SQL Server.
- Dodaj definicję [funkcji partycji](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) do CREATE TABLE.

W przypadku migrowania tabeli partycjonowanej z wystąpienia SQL Server następujące polecenie SQL może pomóc ustalić liczbę wierszy w poszczególnych partycjach. Należy pamiętać, że jeśli ten sam stopień szczegółowości partycjonowania jest używany w dedykowanej puli SQL, liczba wierszy na partycję zmniejsza się o współczynnik 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Przełączanie partycji

Dedykowana Pula SQL obsługuje dzielenie, scalanie i przełączanie partycji. Każda z tych funkcji jest wykonywana przy użyciu instrukcji [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .

Aby przełączyć partycje między dwiema tabelami, należy się upewnić, że partycje są wyrównane do odpowiednich granic i że definicje tabeli są zgodne. Ponieważ ograniczenia check nie są dostępne, aby wymusić zakres wartości w tabeli, tabela źródłowa musi zawierać te same granice partycji co tabela docelowa. Jeśli granice partycji nie są takie same, przełączenie nie powiedzie się, ponieważ metadane partycji nie zostaną zsynchronizowane.

### <a name="how-to-split-a-partition-that-contains-data"></a>Jak podzielić partycję zawierającą dane

Najbardziej wydajną metodą podziału partycji, która zawiera już dane, jest użycie `CTAS` instrukcji. Jeśli partycjonowana tabela jest klastrowaną magazynem kolumn, partycja tabeli musi być pusta, aby można ją było podzielić.

Poniższy przykład tworzy partycjonowaną tabelę magazynu kolumn. Wstawia jeden wiersz do każdej partycji:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

Poniższe zapytanie umożliwia znalezienie liczby wierszy przy użyciu `sys.partitions` widoku wykazu:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Następujące polecenie Split otrzymuje komunikat o błędzie:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, poziom 15, stan 1, klauzula line 44 SPLIT instrukcji ALTER PARTITION nie powiodła się, ponieważ partycja nie jest pusta. W przypadku istnienia indeksu magazynu kolumn w tabeli można podzielić tylko puste partycje. Rozważ wyłączenie indeksu magazynu kolumn przed wygenerowaniem instrukcji ALTER PARTITION, a następnie odbudowanie indeksu magazynu kolumn po ukończeniu operacji ALTER PARTITION.

Można jednak użyć programu, `CTAS` Aby utworzyć nową tabelę do przechowywania danych.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Gdy granice partycji są wyrównane, dozwolony jest przełącznik. Spowoduje to pozostawienie tabeli źródłowej pustą partycją, którą można następnie podzielić.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Wszystko, co pozostało, jest wyrównanie danych do nowej granicy partycji przy użyciu `CTAS` , a następnie przełączenie danych z powrotem do głównej tabeli.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Po zakończeniu przenoszenia danych dobrym pomysłem jest odświeżenie statystyk w tabeli docelowej. Aktualizacja statystyk gwarantuje, że dane statystyczne dokładnie odzwierciedlają nową dystrybucję danych w odpowiednich partycjach.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Ładowanie nowych danych do partycji zawierających dane w jednym kroku

Ładowanie danych do partycji z przełączaniem do partycji jest wygodnym sposobem na umieszczenie nowych danych w tabeli, która nie jest widoczna dla użytkowników.  Może to być trudne w przypadku zajętych systemów, aby zaradzić sobie z zablokowaniem zawartości skojarzonej z przełączeniem partycji.  

Aby wyczyścić istniejące dane w partycji, należy `ALTER TABLE` użyć do przełączenia danych.  Następnie `ALTER TABLE` do przełączenia nowych danych jest wymagane inne.  

W dedykowanej puli SQL `TRUNCATE_TARGET` opcja jest obsługiwana w `ALTER TABLE` poleceniu.  `TRUNCATE_TARGET` `ALTER TABLE` Polecenie zastępuje istniejące dane w partycji nowymi danymi.  Poniżej znajduje się przykład, za pomocą którego `CTAS` można utworzyć nową tabelę z istniejącymi danymi, wstawia nowe dane, a następnie przełącza wszystkie dane z powrotem do tabeli docelowej, zastępując istniejące dane.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Kontrola źródła partycjonowania tabel

Aby uniknąć definicji tabeli z **rusting** w systemie kontroli źródła, warto wziąć pod uwagę następujące podejście:

1. Tworzenie tabeli jako partycjonowanej tabeli, ale bez wartości partycji

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` tabela w ramach procesu wdrażania:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

W tym podejściu kod w kontroli źródła pozostaje statyczny, a wartości graniczne partycjonowania mogą być dynamiczne; rozwijanie z pulą SQL wraz z upływem czasu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat tworzenia tabel, zobacz artykuły w [tabeli przegląd](sql-data-warehouse-tables-overview.md).
