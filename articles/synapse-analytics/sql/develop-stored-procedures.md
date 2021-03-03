---
title: Korzystanie z procedur składowanych
description: Porady dotyczące implementowania procedur składowanych przy użyciu języka SQL Synapse w usłudze Azure Synapse Analytics na potrzeby opracowywania rozwiązań.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674147"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Procedury składowane przy użyciu języka SQL Synapse w usłudze Azure Synapse Analytics

Synapse aprowizacji SQL i pule bezserwerowe umożliwiają umieszczenie złożonej logiki przetwarzania danych w procedurach składowanych SQL. Procedury składowane to doskonały sposób na hermetyzację kodu SQL i przechowywanie go w pobliżu danych w magazynie danych. Procedury składowane ułatwiają deweloperom modularyzacji swoich rozwiązań poprzez Hermetyzowanie kodu w jednostki umożliwiające zarządzanie i ułatwiają lepsze wykorzystywanie kodu. Każda procedura składowana może również akceptować parametry, aby zapewnić im jeszcze większą elastyczność.
W tym artykule znajdziesz wskazówki dotyczące wdrażania procedur składowanych w puli Synapse SQL na potrzeby tworzenia rozwiązań.

## <a name="what-to-expect"></a>Czego oczekiwać

Program SQL Synapse obsługuje wiele funkcji T-SQL, które są używane w SQL Server. Co ważniejsze, dostępne są funkcje skalowania w poziomie, których można użyć w celu zmaksymalizowania wydajności rozwiązania. W tym artykule przedstawiono informacje o funkcjach, które można umieścić w procedurach składowanych.

> [!NOTE]
> W treści procedury można używać tylko funkcji, które są obsługiwane w obszarze powierzchni Synapse SQL. Zapoznaj się z [tym artykułem](overview-features.md) , aby zidentyfikować obiekty, instrukcję, która może być używana w procedurach składowanych. W przykładach w tych artykułach są używane ogólne funkcje, które są dostępne zarówno w przypadku bezserwerowego, jak i dedykowanego obszaru powierzchni. Zapoznaj się z dodatkowymi [ograniczeniami dotyczącymi zainicjowanych i bezserwerowych pul SQL Synapse](#limitations) na końcu tego artykułu.

Aby zachować skalę i wydajność puli SQL, istnieją także pewne funkcje i funkcje, które mają różnice zachowania i inne, które nie są obsługiwane.

## <a name="stored-procedures-in-synapse-sql"></a>Procedury składowane w Synapse SQL

W poniższym przykładzie można zobaczyć procedury, które usuwają obiekty zewnętrzne, jeśli istnieją w bazie danych:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Procedury te można wykonać przy użyciu `EXEC` instrukcji, gdzie można określić nazwę i parametry procedury:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL stanowi uproszczoną i udoskonaloną implementację procedury składowanej. Największą różnicą w porównaniu do SQL Server jest to, że procedura składowana nie jest wstępnie kompilowanym kodem. W przypadku magazynów danych czas kompilacji jest mały w porównaniu do czasu potrzebnego do uruchamiania zapytań dotyczących dużych ilości danych. Należy upewnić się, że kod procedury składowanej jest poprawnie zoptymalizowany pod kątem dużych zapytań. Celem jest oszczędność godzin, minut i sekund, a nie milisekund. W związku z tym bardziej pomocne jest, aby traktować procedury składowane jako kontenery dla logiki SQL.

Gdy Synapse SQL wykonuje procedurę składowaną, instrukcje SQL są analizowane, tłumaczone i optymalizowane w czasie wykonywania. W trakcie tego procesu każda instrukcja jest konwertowana na zapytania rozproszone. Kod SQL, który jest wykonywany względem danych, różni się od przesłanego zapytania.

## <a name="encapsulate-validation-rules"></a>Hermetyzowanie reguł walidacji

Procedury składowane umożliwiają znalezienie logiki walidacji w pojedynczym module przechowywanym w usłudze SQL Database. W poniższym przykładzie można zobaczyć, jak sprawdzać wartości parametrów i zmieniać ich wartości domyślne.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

Logika w procedurze SQL sprawdzi parametry wejściowe, gdy procedura jest wywoływana.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Zagnieżdżanie procedur składowanych

Gdy procedury składowane wywołują inne procedury składowane lub wykonują dynamiczną instrukcję SQL, wewnętrzna procedura składowana lub wywołanie kodu są określane jako zagnieżdżone.
Przykładowa procedura zagnieżdżona jest pokazana w poniższym kodzie:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Ta procedura akceptuje parametr reprezentujący nazwę, a następnie wywołuje inne procedury usuwania obiektów o tej nazwie.
Synapse Pula SQL obsługuje maksymalnie osiem poziomów zagnieżdżenia. Ta funkcja jest nieco inna niż SQL Server. Poziom zagnieżdżenia w SQL Server to 32.

Wywołanie procedury składowanej najwyższego poziomu jest równe zagnieżdżeniu poziomu 1.

```sql
EXEC clean_up 'mytest'
```

Jeśli procedura składowana wykonuje również inne wywołanie programu EXEC, poziom zagnieżdżenia zwiększy się do dwóch.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Jeśli druga procedura wykonuje następnie dynamiczny SQL, poziom zagnieżdżenia rośnie do trzech.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL nie obsługuje obecnie [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true). Musisz śledzić poziom zagnieżdżenia. Jest mało prawdopodobne, aby przekroczyć limit ośmiu poziomów zagnieżdżenia, ale jeśli to zrobisz, musisz ponownie obsłużyć swój kod, aby dopasować poziomy zagnieżdżenia w ramach tego limitu.

## <a name="insertexecute"></a>INSERT..EXEUROCZE

Zainicjowana Pula SQL Synapse nie zezwala na korzystanie z zestawu wyników procedury składowanej z instrukcją INSERT. Istnieje alternatywne podejście, którego można użyć. Aby zapoznać się z przykładem, zapoznaj się z artykułem dotyczącym [tabel tymczasowych](develop-tables-temporary.md) dla puli aprowizacji Synapse.

## <a name="limitations"></a>Ograniczenia

Istnieją pewne aspekty procedur składowanych Transact-SQL, które nie są zaimplementowane w Synapse SQL, takie jak:

| Funkcja/opcja | Zaaprowizowane | Praca bezserwerowa |
| --- | --- |
| Tymczasowe procedury składowane | Nie | Tak |
| Numerowane procedury składowane | Nie | Nie |
| Rozszerzone procedury składowane | Nie | Nie |
| Procedury składowane CLR | Nie | Nie |
| Opcja szyfrowania | Nie | Tak |
| Opcja replikacji | Nie | Nie |
| Parametry z wartościami przechowywanymi w tabeli | Nie | Nie |
| Parametry tylko do odczytu | Nie | Nie |
| Parametry domyślne | Nie | Tak |
| Konteksty wykonywania | Nie | Nie |
| Return — Instrukcja | Nie | Tak |
| WSTAW DO.. SZEF | Nie | Tak |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej porad programistycznych, zobacz [Omówienie projektowania](develop-overview.md).
