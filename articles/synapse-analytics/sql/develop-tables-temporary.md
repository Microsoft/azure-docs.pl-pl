---
title: Używanie tabel tymczasowych w Synapse SQL
description: Podstawowe wskazówki dotyczące używania tabel tymczasowych w Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8513df83196b3521a749515c6bb22caad7d255b7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816047"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tabele tymczasowe w Synapse SQL

Ten artykuł zawiera podstawowe wskazówki dotyczące używania tabel tymczasowych i wyróżnia zasady dotyczące tabel tymczasowych na poziomie sesji w Synapse SQL. 

Zarówno dedykowana pula SQL, jak i bez serwera zasoby puli SQL mogą korzystać z tabel tymczasowych. Bez serwera pula SQL ma ograniczenia, które zostały omówione na końcu tego artykułu. 

## <a name="temporary-tables"></a>Tabele tymczasowe

Tabele tymczasowe są przydatne podczas przetwarzania danych, szczególnie podczas przekształcania, gdy wyniki pośrednie są przejściowe. W Synapse SQL tabele tymczasowe istnieją na poziomie sesji.  Są one widoczne tylko dla sesji, w której zostały utworzone. W związku z tym są one automatycznie porzucane, gdy ta sesja się wyloguje. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Tabele tymczasowe w dedykowanej puli SQL

W dedykowanym zasobie puli SQL tabele tymczasowe oferują korzyści w zakresie wydajności, ponieważ ich wyniki są zapisywane w magazynie lokalnym, a nie zdalnym.

### <a name="create-a-temporary-table"></a>Tworzenie tabeli tymczasowej

Tabele tymczasowe są tworzone przez poprzedanie nazwy tabeli prefiksem `#` .  Na przykład:

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

Tabele tymczasowe można również tworzyć przy `CTAS` użyciu dokładnie tego samego podejścia:

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
> `CTAS` jest zaawansowane polecenie i ma dodatkową zaletę jest wydajne w użyciu obszaru dziennika transakcji. 
> 
> 

### <a name="drop-temporary-tables"></a>Drop temporary tables (Upuszczanie tabel tymczasowych)

Po utworzeniu nowej sesji nie powinny istnieć żadne tabele tymczasowe.  Jeśli jednak wywołujesz tę samą procedurę składowaną, która tworzy tymczasowy plik o tej samej nazwie, aby upewnić się, że instrukcje są pomyślne, użyj prostego sprawdzania istnienia za pomocą `CREATE TABLE` funkcji  `DROP` : 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

W celu zapewnienia spójności kodowania dobrym rozwiązaniem jest użycie tego wzorca zarówno dla tabel, jak i tabel tymczasowych.  Dobrym pomysłem jest również usunięcie tabel tymczasowych po `DROP TABLE` zakończeniu pracy z nimi.  

Podczas opracowywania procedur składowanych często widać polecenia drop połączone razem na końcu procedury, aby upewnić się, że te obiekty są czyszczone.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Modularyzowanie kodu

Tabele tymczasowe mogą być używane w dowolnym miejscu sesji użytkownika. Tę możliwość można następnie wykorzystać, aby ułatwić modułowe modułowanie kodu aplikacji.  Aby to zademonstrować, następująca procedura składowana generuje DDL, aby zaktualizować wszystkie statystyki w bazie danych według nazwy statystyki:

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

Na tym etapie jedyną akcją, która miała miejsce, jest utworzenie procedury składowanej, która generuje #stats_ddl tabeli tymczasowej.  Procedura składowana porzuca #stats_ddl, jeśli już istnieje. Ten spadek gwarantuje, że nie powiedzie się, jeśli zostanie uruchomiony więcej niż raz w ramach sesji.  

Ponieważ nie ma na końcu procedury składowanej, po zakończeniu procedury składowanej utworzona tabela pozostaje i można ją odczytać poza `DROP TABLE` procedurą składowaną.  

W przeciwieństwie do SQL Server baz danych Synapse SQL umożliwia korzystanie z tabeli tymczasowej poza procedurą, która ją utworzyła.  Tabel tymczasowych utworzonych za pośrednictwem dedykowanej puli SQL można używać **w dowolnym** miejscu w sesji. W rezultacie będziesz mieć bardziej modularny i zarządzalny kod, jak pokazano w poniższym przykładzie:

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

### <a name="temporary-table-limitations"></a>Tymczasowe ograniczenia tabeli

Dedykowana pula SQL ma kilka ograniczeń implementacji tabel tymczasowych:

- Obsługiwane są tylko tabele tymczasowe o zakresie sesji.  Globalne tabele tymczasowe nie są obsługiwane.
- Widoków nie można tworzyć w tabelach tymczasowych.
- Tabele tymczasowe można tworzyć tylko przy użyciu dystrybucji skrótu lub okrężnego.  Zreplikowana tymczasowa dystrybucja tabel nie jest obsługiwana. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Tabele tymczasowe w bezserowej puli SQL

Tabele tymczasowe w bez serwerach puli SQL są obsługiwane, ale ich użycie jest ograniczone. Nie można ich używać w zapytaniach dotyczących plików docelowych. 

Na przykład nie można połączyć tabeli tymczasowej z danymi z plików w magazynie. Liczba tabel tymczasowych jest ograniczona do 100, a ich całkowity rozmiar jest ograniczony do 100 MB.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia tabel, zobacz [artykuł Designing tables using the Synapse SQL resources (Projektowanie](develop-tables-overview.md) tabel przy użyciu Synapse SQL zasobów).

