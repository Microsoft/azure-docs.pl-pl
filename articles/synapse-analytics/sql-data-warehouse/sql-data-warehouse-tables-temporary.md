---
title: Tabele tymczasowe
description: Podstawowe wskazówki dotyczące używania tabel tymczasowych w puli SQL Synapse, wyróżnianie zasad tabel tymczasowych na poziomie sesji.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 61cc351470c0446b58d83d2d7f9c998d959c3649
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85414406"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Tabele tymczasowe w puli SQL Synapse
Ten artykuł zawiera podstawowe wskazówki dotyczące używania tabel tymczasowych i wyróżniania zasad tabel tymczasowych na poziomie sesji. 

Korzystając z informacji zawartych w tym artykule, można modularyzacji swój kod, ulepszając możliwości ponownego wykorzystania i prostoty konserwacji.

## <a name="what-are-temporary-tables"></a>Co to są tabele tymczasowe?
Tabele tymczasowe są przydatne podczas przetwarzania danych, zwłaszcza podczas przekształcania, w którym wyniki pośrednie są przejściowe. W puli SQL tabele tymczasowe istnieją na poziomie sesji.  

Tabele tymczasowe są widoczne tylko dla sesji, w której zostały utworzone i są automatycznie usuwane po wylogowaniu się tej sesji.  

Tabele tymczasowe oferują korzyść wydajności, ponieważ ich wyniki są zapisywane w lokalnym, a nie w magazynie zdalnym.

Tabele tymczasowe są przydatne podczas przetwarzania danych, zwłaszcza podczas przekształcania, w którym wyniki pośrednie są przejściowe. W przypadku puli SQL tabele tymczasowe istnieją na poziomie sesji.  Są one widoczne tylko dla sesji, w której zostały utworzone. W związku z tym są one automatycznie porzucane podczas wylogowywania sesji. 

## <a name="temporary-tables-in-sql-pool"></a>Tabele tymczasowe w puli SQL

W przypadku zasobu puli SQL tabele tymczasowe oferują korzyść wydajności, ponieważ ich wyniki są zapisywane w lokalnym, a nie w magazynie zdalnym.

### <a name="create-a-temporary-table"></a>Tworzenie tabeli tymczasowej

Tabele tymczasowe są tworzone przez utworzenie prefiksu nazwy tabeli z `#` .  Na przykład:

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

## <a name="dropping-temporary-tables"></a>Usuwanie tabel tymczasowych
Po utworzeniu nowej sesji nie powinny istnieć tabele tymczasowe.  

Jeśli wywołujesz tę samą procedurę składowaną, która tworzy tymczasową o takiej samej nazwie, aby upewnić się, że `CREATE TABLE` instrukcje zostały wykonane pomyślnie, prosta kontrola wstępna z `DROP` może być użyta jako w poniższym przykładzie:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Aby zapewnić spójność kodowania, dobrym sposobem jest użycie tego wzorca dla tabel i tabel tymczasowych.  Dobrym pomysłem jest również `DROP TABLE` usunięcie tabel tymczasowych po zakończeniu pracy z nimi w kodzie.  

W opracowywaniu procedury składowanej, często widzisz polecenia upuszczania powiązane razem na końcu procedury, aby upewnić się, że te obiekty zostały oczyszczone.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Kod Modularizing
Ponieważ tabele tymczasowe mogą być widoczne w dowolnym miejscu w sesji użytkownika, można skorzystać z tej możliwości, aby pomóc Ci w modularyzacji kodu aplikacji.  

Na przykład następująca procedura składowana generuje kod DDL, aby zaktualizować wszystkie statystyki w bazie danych według nazwy statystycznej:

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
FROM    #stats_ddl
;
GO
```

Na tym etapie jedyną akcją, która miała miejsce, jest utworzenie procedury składowanej, która generuje tabelę tymczasową #stats_ddl, przy użyciu instrukcji języka DDL.  

Ta procedura składowana opuszcza istniejący #stats_ddl, aby upewnić się, że nie kończy się niepowodzeniem w przypadku uruchomienia więcej niż raz w ramach sesji.  

Jednakże, ponieważ nie ma `DROP TABLE` na końcu procedury składowanej, gdy procedura składowana zostanie ukończona, opuszcza utworzoną tabelę, aby można ją było odczytać poza procedurą składowaną.  

W puli SQL, w przeciwieństwie do innych baz danych SQL Server, można użyć tabeli tymczasowej poza procedurą, która ją utworzyła.  Tabele tymczasowe puli SQL mogą być używane w **dowolnym miejscu** w ramach sesji. Ta funkcja może prowadzić do bardziej modularnego i możliwego do zarządzania kodu, jak w poniższym przykładzie:

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

## <a name="temporary-table-limitations"></a>Ograniczenia tabeli tymczasowej
Podczas implementowania tabel tymczasowych usługa SQL Pool nakłada kilka ograniczeń.  Obecnie obsługiwane są tylko tabele tymczasowe w zakresie sesji.  Globalne tabele tymczasowe nie są obsługiwane.  

Ponadto nie można tworzyć widoków w tabelach tymczasowych.  Tabele tymczasowe można tworzyć tylko za pomocą rozkładu mieszania lub działania okrężnego.  Zreplikowana tymczasowa dystrybucja tabel nie jest obsługiwana. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat opracowywania tabel, zobacz artykuł [Projektowanie tabel przy użyciu zasobów Synapse SQL](sql-data-warehouse-tables-overview.md) .

