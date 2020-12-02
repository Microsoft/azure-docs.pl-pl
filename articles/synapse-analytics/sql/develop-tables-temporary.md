---
title: Używanie tabel tymczasowych w Synapse SQL
description: Podstawowe wskazówki dotyczące używania tabel tymczasowych w programie SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451779"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tabele tymczasowe w Synapse SQL

Ten artykuł zawiera podstawowe wskazówki dotyczące używania tabel tymczasowych i wyróżnia zasady tabel tymczasowych na poziomie sesji w programie Synapse SQL. 

Zarówno dedykowana Pula SQL, jak i bezserwerowe zasoby puli SQL mogą korzystać z tabel tymczasowych. Bezserwerowa Pula SQL ma ograniczenia, które zostały omówione na końcu tego artykułu. 

## <a name="temporary-tables"></a>Tabele tymczasowe

Tabele tymczasowe są przydatne podczas przetwarzania danych, zwłaszcza podczas przekształcania, w którym wyniki pośrednie są przejściowe. W przypadku języka SQL Synapse tabele tymczasowe istnieją na poziomie sesji.  Są one widoczne tylko dla sesji, w której zostały utworzone. W związku z tym są one automatycznie porzucane podczas wylogowywania sesji. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Tabele tymczasowe w dedykowanej puli SQL

W przypadku dedykowanego zasobu puli SQL tabele tymczasowe oferują korzyść wydajności, ponieważ ich wyniki są zapisywane w lokalnym, a nie w magazynie zdalnym.

### <a name="create-a-temporary-table"></a>Tworzenie tabeli tymczasowej

Tabele tymczasowe są tworzone przez utworzenie prefiksu nazwy tabeli z `#` .  Przykład:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Tabele tymczasowe można także tworzyć przy `CTAS` użyciu dokładnie takiej samej metody:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` jest zaawansowanym poleceniem i ma dodatkową zaletę, która jest wydajna podczas korzystania z przestrzeni dzienników transakcji. 
> 
> 

### <a name="drop-temporary-tables"></a>Usuwanie tabel tymczasowych

Po utworzeniu nowej sesji nie powinny istnieć tabele tymczasowe.  Jednakże w przypadku wywołania tej samej procedury składowanej, która tworzy tymczasową o tej samej nazwie, aby upewnić się, że `CREATE TABLE` instrukcje zostały wykonane pomyślnie, należy użyć prostej kontroli wstępnej z  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Aby zapewnić spójność kodowania, dobrym sposobem jest użycie tego wzorca dla tabel i tabel tymczasowych.  Dobrym pomysłem jest również `DROP TABLE` usunięcie tabel tymczasowych po zakończeniu pracy z nimi.  

W opracowywaniu procedury składowanej, często widzisz polecenia upuszczania powiązane razem na końcu procedury, aby upewnić się, że te obiekty zostały oczyszczone.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Kod modularyzacji

Tabel tymczasowych można używać w dowolnym miejscu w sesji użytkownika. Można następnie wykorzystać tę możliwość do modularyzacji kodu aplikacji.  W celu zademonstrowania następującej procedury składowanej generuje kod DDL w celu zaktualizowania wszystkich statystyk w bazie danych według nazwy statystycznej:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Na tym etapie jedyną akcją, która miała miejsce, jest utworzenie procedury składowanej, która generuje #stats_ddl tabelę tymczasową.  Procedura składowana opuszcza #stats_ddl, jeśli już istnieje. To porzucanie gwarantuje, że nie kończy się niepowodzeniem w przypadku uruchomienia więcej niż raz w ramach sesji.  

Ponieważ nie ma `DROP TABLE` na końcu procedury składowanej, po zakończeniu procedury składowanej utworzona tabela pozostaje i można ją odczytać poza procedurą składowaną.  

W przeciwieństwie do innych baz danych SQL Server program SQL Server Synapse umożliwia korzystanie z tabeli tymczasowej poza procedurą, która ją utworzyła.  Tabele tymczasowe utworzone za pośrednictwem dedykowanej puli SQL mogą być używane w **dowolnym miejscu** w ramach sesji. W związku z tym będziesz mieć więcej kodu modularnego i możliwego do zarządzania, jak pokazano w poniższym przykładzie:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>Ograniczenia tabeli tymczasowej

Dedykowana Pula SQL ma kilka ograniczeń implementacji tabel tymczasowych:

- Obsługiwane są tylko tabele tymczasowe w zakresie sesji.  Globalne tabele tymczasowe nie są obsługiwane.
- Nie można tworzyć widoków w tabelach tymczasowych.
- Tabele tymczasowe można tworzyć tylko za pomocą rozkładu mieszania lub działania okrężnego.  Zreplikowana tymczasowa dystrybucja tabel nie jest obsługiwana. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Tabele tymczasowe w puli SQL bezserwerowej

Tabele tymczasowe w puli SQL bezserwerowej są obsługiwane, ale ich użycie jest ograniczone. Nie mogą być używane w zapytaniach, które są plikami docelowymi. 

Na przykład nie można przyłączyć tabeli tymczasowej z danymi z plików w magazynie. Liczba tabel tymczasowych jest ograniczona do 100, a ich łączny rozmiar jest ograniczony do 100 MB.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat opracowywania tabel, zobacz artykuł [Projektowanie tabel przy użyciu zasobów Synapse SQL](develop-tables-overview.md) .

