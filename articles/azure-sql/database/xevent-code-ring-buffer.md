---
title: Kod buforu pierścienia systemu XEvent
description: Zawiera przykładowy kod języka Transact-SQL, który jest łatwy i szybki dzięki użyciu obiektu docelowego buforu pierścieniowego w Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 57449b0bbd39b6ea04ecae5a3ad766ae5687ca0b
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619835"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Kod docelowy buforu pierścieniowego dla zdarzeń rozszerzonych w Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Chcesz uzyskać pełny przykładowy kod dla najłatwiejszego szybkiego sposobu przechwytywania i raportowania informacji dla zdarzenia rozszerzonego podczas testu. Najłatwiejszym celem dla rozszerzonych danych zdarzeń jest [obiekt docelowy bufora pierścieniowego](https://msdn.microsoft.com/library/ff878182.aspx).

W tym temacie przedstawiono przykładowy kod języka Transact-SQL, który:

1. Tworzy tabelę zawierającą dane, które mają być prezentowane za pomocą.
2. Tworzy sesję dla istniejącego zdarzenia rozszerzonego, mianowicie **SqlServer. sql_statement_starting**.

   * Zdarzenie jest ograniczone do instrukcji SQL zawierających określony ciąg aktualizacji: instrukcja, taka **jak "% Update tabEmployee%"**.
   * Wybiera do wysłania danych wyjściowych zdarzenia do obiektu docelowego bufora pierścieniowego, mianowicie  **package0. ring_buffer**.
3. Uruchamia sesję zdarzeń.
4. Wystawia kilka prostych instrukcji dotyczących aktualizacji SQL.
5. Emituje instrukcję SELECT języka SQL w celu pobrania danych wyjściowych zdarzenia z bufora pierścieni.

   * **sys. dm_xe_database_session_targets** i inne dynamiczne widoki zarządzania (widoków DMV) są dołączone.
6. Kończy sesję zdarzeń.
7. Odrzuca obiekt docelowy buforu pierścieniowego, aby zwolnić jego zasoby.
8. Odrzuca sesję zdarzeń i tabelę demonstracyjną.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Po zarejestrowaniu się możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Dowolna baza danych, w której można utworzyć tabelę.
  
  * Opcjonalnie możesz [utworzyć demonstracyjną bazę danych **AdventureWorksLT** ](single-database-create-quickstart.md) w ciągu kilku minut.
* SQL Server Management Studio (ssms.exe), najlepiej jej Najnowsza wersja aktualizacji miesięcznych.
  Najnowszą ssms.exe można pobrać z:
  
  * Temat [SQL Server Management Studio pobierania](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Bezpośredni link do pobierania.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Przykład kodu

W przypadku bardzo drobnej modyfikacji Poniższy przykład kodu buforu pierścieniowego można uruchomić na Azure SQL Database lub Microsoft SQL Server. Różnica polega na obecności węzła "_database" w nazwie niektórych dynamicznych widoków zarządzania (widoków DMV) użytego w klauzuli FROM w kroku 5. Na przykład:

* sys. dm_xe<strong>_database</strong>_session_targets
* sys. dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Zawartość bufora pierścieniowego

Użyto `ssms.exe` do uruchomienia przykładu kodu.

Aby wyświetlić wyniki, należy kliknąć komórkę poniżej nagłówka kolumny **target_data_XML**.

Następnie w okienku wyników kliknięto komórkę pod nagłówkiem kolumny **target_data_XML**. W tym celu kliknij pozycję utworzono inną kartę pliku w ssms.exe, w której zawartość komórki wynikowej była wyświetlana jako XML.

Dane wyjściowe są wyświetlane w następującym bloku. Wygląda na to, że jest on długi, ale jest tylko dwa **\<event>** elementy.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Zwalnianie zasobów przechowywanych w buforze pierścieniowym

Po zakończeniu pracy z buforem pierścieniowym można go usunąć i zwolnić jego zasoby, wykonując **zmiany** podobne do następujących:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

Definicja sesji zdarzeń jest aktualizowana, ale nie porzucana. Później można dodać kolejne wystąpienie buforu pierścienia do sesji zdarzeń:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Więcej informacji

Podstawowy temat dla rozszerzonych zdarzeń na Azure SQL Database to:

* [Rozbudowane zagadnienia dotyczące zdarzeń w Azure SQL Database](xevent-db-diff-from-svr.md), które różnią się pewnymi aspektami rozszerzonych zdarzeń, które różnią się między Azure SQL Database a Microsoft SQL Server.

Inne tematy przykładowe kodu dla zdarzeń rozszerzonych są dostępne na następujących łączach. Należy jednak regularnie sprawdzać dowolną próbkę, aby zobaczyć, czy Przykładowe elementy docelowe Microsoft SQL Server, a nie Azure SQL Database. Następnie możesz zdecydować, czy niewielkie zmiany są potrzebne do uruchomienia przykładu.

* Przykład kodu dla Azure SQL Database: [kod docelowy pliku zdarzeń dla zdarzeń rozszerzonych w Azure SQL Database](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
