---
title: Tabele tymczasowe
description: Podstawowe wskazówki dotyczące używania tabel tymczasowych w puli SQL Synapse, podświetlanie zasad tabel tymczasowych na poziomie sesji.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408040"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Tabele tymczasowe w puli SQL Synapse
Ten artykuł zawiera podstawowe wskazówki dotyczące korzystania z tabel tymczasowych i podkreśla zasady tabel tymczasowych na poziomie sesji. 

Korzystanie z informacji w tym artykule może pomóc modularize kodu, poprawiając zarówno ponownego użycia i łatwość konserwacji.

## <a name="what-are-temporary-tables"></a>Co to są tabele tymczasowe?
Tabele tymczasowe są przydatne podczas przetwarzania danych, szczególnie podczas transformacji, gdzie wyniki pośrednie są przejściowe. W puli SQL tabele tymczasowe istnieją na poziomie sesji.  

Tabele tymczasowe są widoczne tylko dla sesji, w której zostały utworzone i są automatycznie usuwane po wylogowaniu się tej sesji.  

Tabele tymczasowe oferują korzyści wydajności, ponieważ ich wyniki są zapisywane do magazynu lokalnego, a nie zdalnego.

Tabele tymczasowe są przydatne podczas przetwarzania danych, szczególnie podczas transformacji, gdzie wyniki pośrednie są przejściowe. W usłudze SQL Analytics tabele tymczasowe istnieją na poziomie sesji.  Są one widoczne tylko dla sesji, w której zostały utworzone. W związku z tym są one automatycznie usuwane po wylogowaniu się tej sesji. 

## <a name="temporary-tables-in-sql-pool"></a>Tabele tymczasowe w puli SQL

W zasobie puli SQL tabele tymczasowe oferują korzyści z wydajności, ponieważ ich wyniki są zapisywane w magazynie lokalnym, a nie zdalnym.

### <a name="create-a-temporary-table"></a>Tworzenie tabeli tymczasowej

Tabele tymczasowe są tworzone przez prefiks nazwy tabeli z `#`.  Przykład:

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

Tabele tymczasowe można również `CTAS` utworzyć przy użyciu dokładnie tego samego podejścia:

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
> `CTAS`jest potężnym poleceniem i ma dodatkową zaletę, że jest wydajny w korzystaniu z miejsca dziennika transakcji. 
> 
> 

## <a name="dropping-temporary-tables"></a>Upuszczanie tabel tymczasowych
Po utworzeniu nowej sesji nie powinny istnieć żadne tabele tymczasowe.  

Jeśli wywołujesz tę samą procedurę składowaną, która tworzy tymczasową `CREATE TABLE` o tej samej nazwie, aby `DROP` upewnić się, że instrukcje są pomyślne, można użyć prostego sprawdzenia przed istnieniem za pomocą a, jak w poniższym przykładzie:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Spójność kodowania jest dobrą praktyką, aby użyć tego wzorca dla tabel i tabel tymczasowych.  Dobrym pomysłem jest również `DROP TABLE` usunięcie tabel tymczasowych po zakończeniu ich pracy w kodzie.  

W rozwoju procedury składowanej jest często, aby zobaczyć polecenia drop wiązane razem na końcu procedury, aby upewnić się, że te obiekty są czyszczone.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modułowość kodu
Ponieważ tabele tymczasowe można zobaczyć w dowolnym miejscu w sesji użytkownika, ta funkcja może być wykorzystana, aby ułatwić modularyzacji kodu aplikacji.  

Na przykład następująca procedura składowana generuje DDL, aby zaktualizować wszystkie statystyki w bazie danych według nazwy statystycznej:

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

Na tym etapie jedyną czynnością, która wystąpiła jest utworzenie procedury składowanej, która generuje tabelę tymczasową, #stats_ddl, z instrukcjami DDL.  

Ta procedura składowana porzuca istniejący #stats_ddl, aby upewnić się, że nie zakończy się niepowodzeniem, jeśli zostanie uruchomiony więcej niż jeden raz w ramach sesji.  

Jednak ponieważ nie `DROP TABLE` ma na końcu procedury składowanej, po zakończeniu procedury składowanej pozostawia utworzonej tabeli, dzięki czemu można odczytać poza procedurą składowaną.  

W puli SQL, w przeciwieństwie do innych baz danych programu SQL Server, jest możliwe użycie tabeli tymczasowej poza procedury, która go utworzyła.  Tabele tymczasowe puli SQL mogą być używane w **dowolnym miejscu** wewnątrz sesji. Ta funkcja może prowadzić do bardziej modułowego i łatwego w zarządzaniu kodu, jak w poniższym przykładzie:

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

## <a name="temporary-table-limitations"></a>Tymczasowe ograniczenia tabeli
Pula SQL nakłada kilka ograniczeń podczas implementowania tabel tymczasowych.  Obecnie obsługiwane są tylko tabele tymczasowe o zakresie sesji.  Globalne tabele tymczasowe nie są obsługiwane.  

Ponadto widoki nie mogą być tworzone w tabelach tymczasowych.  Tabele tymczasowe można tworzyć tylko z dystrybucją mieszania lub okrężnego.  Replikowana tymczasowa dystrybucja tabeli nie jest obsługiwana. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia tabel, zobacz [projektowanie tabel przy użyciu zasobów usługi SQL Analytics.](sql-data-warehouse-tables-overview.md)

