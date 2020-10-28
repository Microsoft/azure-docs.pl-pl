---
title: Automatyzuj replikację zmian schematu w SQL Data Sync
description: Dowiedz się, jak zautomatyzować replikację zmian schematu w usłudze Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790189"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL Data Sync umożliwia użytkownikom synchronizowanie danych między bazami danych w Azure SQL Database i SQL Server wystąpieniami w jednym kierunku lub w obu kierunkach. Jednym z bieżących ograniczeń SQL Data Sync jest brak obsługi replikacji zmian schematu. Za każdym razem, gdy zmieniasz schemat tabeli, musisz ręcznie zastosować zmiany we wszystkich punktach końcowych, włącznie z centrum i wszystkimi elementami członkowskimi, a następnie zaktualizować schemat synchronizacji.

W tym artykule wprowadzono rozwiązanie umożliwiające automatyczne Replikowanie zmian schematu do wszystkich punktów końcowych SQL Data Sync.

1. To rozwiązanie używa wyzwalacza DDL do śledzenia zmian schematu.
1. Wyzwalacz wstawia polecenia zmiany schematu w tabeli śledzenia.
1. Ta tabela śledzenia jest synchronizowana ze wszystkimi punktami końcowymi przy użyciu usługi synchronizacji danych.
1. Wyzwalacze DML po wstawieniu są używane do zastosowania zmian schematu w innych punktach końcowych.

W tym artykule zastosowano polecenie ALTER TABLE jako przykład zmiany schematu, ale to rozwiązanie działa również dla innych typów zmian schematu.

> [!IMPORTANT]
> Zalecamy zapoznanie się z tym artykułem uważnie, szczególnie w sekcjach dotyczących [rozwiązywania problemów](#troubleshoot) i [innych zagadnień](#other), przed rozpoczęciem wdrażania zautomatyzowanej replikacji zmian schematu w środowisku synchronizacji. Zalecamy również odczytywanie [danych synchronizacji między wieloma bazami danych w chmurze i lokalnymi przy użyciu SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md). Niektóre operacje bazy danych mogą spowodować uszkodzenie rozwiązania opisanego w tym artykule. Aby rozwiązać te problemy, może być wymagana dodatkowa znajomość domeny SQL Server i języka Transact-SQL.

![Automatyzowanie replikacji zmian schematu](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Skonfiguruj automatyczną replikację zmian schematu

### <a name="create-a-table-to-track-schema-changes"></a>Tworzenie tabeli do śledzenia zmian schematu

Utwórz tabelę, aby śledzić zmiany schematu we wszystkich bazach danych w grupie synchronizacji:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Ta tabela zawiera kolumnę tożsamości do śledzenia kolejności zmian w schemacie. Możesz dodać więcej pól, aby rejestrować więcej informacji w razie potrzeby.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Utwórz tabelę, aby śledzić historię zmian schematu

Dla wszystkich punktów końcowych Utwórz tabelę, aby śledzić identyfikator ostatnio zastosowanego polecenia zmiany schematu.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Utwórz wyzwalacz ALTER TABLE DDL w bazie danych, w której są wprowadzane zmiany schematu

Utwórz wyzwalacz DDL dla operacji ALTER TABLE. Ten wyzwalacz trzeba utworzyć tylko w bazie danych, w której są wprowadzane zmiany schematu. Aby uniknąć konfliktów, należy zezwolić tylko na zmiany schematu w jednej bazie danych w grupie synchronizacji.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Wyzwalacz wstawia rekord w tabeli śledzenia zmian schematu dla każdego polecenia ALTER TABLE. Ten przykład dodaje filtr, aby uniknąć replikowania zmian schematu w ramach schematu **synchronizacji** danych, ponieważ najprawdopodobniej jest to spowodowane tym, że ta usługa jest najbardziej prawdopodobną przyczyną. Dodaj więcej filtrów, jeśli chcesz tylko replikować pewne typy zmian schematu.

Możesz również dodać więcej wyzwalaczy, aby replikować inne typy zmian schematu. Na przykład utwórz wyzwalacze CREATE_PROCEDURE, ALTER_PROCEDURE i DROP_PROCEDURE, aby replikować zmiany w procedurach składowanych.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Utwórz wyzwalacz dla innych punktów końcowych, aby zastosować zmiany schematu podczas wstawiania

Ten wyzwalacz wykonuje polecenie zmiany schematu, gdy jest synchronizowane z innymi punktami końcowymi. Ten wyzwalacz należy utworzyć we wszystkich punktach końcowych, z wyjątkiem tego, gdzie wprowadzono zmiany schematu (czyli w bazie danych, w której wyzwalacz DDL `AlterTableDDLTrigger` został utworzony w poprzednim kroku).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Ten wyzwalacz jest uruchamiany po wstawieniu i sprawdza, czy bieżące polecenie powinno zostać uruchomione dalej. Logika kodu gwarantuje, że żadna instrukcja zmiany schematu nie zostanie pominięta i wszystkie zmiany są stosowane nawet wtedy, gdy wstawienie jest nieuporządkowane.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synchronizuj tabelę śledzenia zmian schematu z wszystkimi punktami końcowymi

Tabelę śledzenia zmian schematu można zsynchronizować ze wszystkimi punktami końcowymi przy użyciu istniejącej grupy synchronizacji lub nowej grupy synchronizacji. Upewnij się, że zmiany w tabeli śledzenia można synchronizować ze wszystkimi punktami końcowymi, szczególnie w przypadku korzystania z synchronizacji jednokierunkowej.

Nie Synchronizuj tabeli historii zmian schematu, ponieważ ta tabela przechowuje różne stany w różnych punktach końcowych.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Zastosuj zmiany schematu w grupie synchronizacji

Replikowane są tylko zmiany schematu wprowadzone w bazie danych, w której tworzony jest wyzwalacz DDL. Zmiany schematu wykonane w innych bazach danych nie są replikowane.

Po zreplikowaniu zmian schematu do wszystkich punktów końcowych należy również wykonać dodatkowe czynności, aby zaktualizować schemat synchronizacji w celu rozpoczęcia lub zatrzymania synchronizacji nowych kolumn.

#### <a name="add-new-columns"></a>Dodaj nowe kolumny

1.  Zmień schemat.

1.  Należy unikać zmian danych, w których nowe kolumny są wykorzystywane do momentu ukończenia kroku, który tworzy wyzwalacz.

1.  Zaczekaj, aż zmiany schematu zostaną zastosowane do wszystkich punktów końcowych.

1.  Odśwież schemat bazy danych i Dodaj nową kolumnę do schematu synchronizacji.

1.  Dane w nowej kolumnie są synchronizowane podczas następnej operacji synchronizacji.

#### <a name="remove-columns"></a>Usuwanie kolumn

1.  Usuń kolumny ze schematu synchronizacji. Synchronizacja danych uniemożliwia synchronizowanie danych w tych kolumnach.

1.  Zmień schemat.

1.  Odśwież schemat bazy danych.

#### <a name="update-data-types"></a>Aktualizowanie typów danych

1.  Zmień schemat.

1.  Zaczekaj, aż zmiany schematu zostaną zastosowane do wszystkich punktów końcowych.

1.  Odśwież schemat bazy danych.

1.  Jeśli nowe i stare typy danych nie są w pełni zgodne — na przykład, jeśli zmieni się z `int` na `bigint` — Synchronizacja może zakończyć się niepowodzeniem przed wykonaniem kroków tworzących wyzwalacze. Synchronizacja powiedzie się po ponowieniu próby.

#### <a name="rename-columns-or-tables"></a>Zmiana nazw kolumn lub tabel

Zmiana nazw kolumn lub tabel powoduje, że synchronizacja danych przestanie działać. Utwórz nową tabelę lub kolumnę, wypełnij dane, a następnie usuń starą tabelę lub kolumnę zamiast zmieniać nazwy.

#### <a name="other-types-of-schema-changes"></a>Inne typy zmian schematu

W przypadku innych typów zmian schematu — na przykład tworzenie procedur składowanych lub usuwanie indeksu-aktualizowanie schematu synchronizacji nie jest wymagane.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Rozwiązywanie problemów z automatyczną replikacją zmian schematu

Logika replikacji opisana w tym artykule przestaje działać w niektórych sytuacjach — na przykład w przypadku zmiany schematu w lokalnej bazie danych, która nie jest obsługiwana w Azure SQL Database. W takim przypadku synchronizacja tabeli śledzenia zmian schematu kończy się niepowodzeniem. Ten problem należy rozwiązać ręcznie:

1.  Wyłącz wyzwalacz DDL i unikaj dalszych zmian schematu, dopóki problem nie zostanie rozwiązany.

1.  W bazie danych punktu końcowego, w której występuje problem, wyłącz wyzwalacz AFTER INSERT w punkcie końcowym, w którym nie można wprowadzić zmiany schematu. Ta akcja pozwala synchronizować polecenie zmiany schematu.

1.  Wyzwalanie synchronizacji w celu zsynchronizowania tabeli śledzenia zmian schematu.

1.  W bazie danych punktu końcowego, w której występuje problem, wykonaj zapytanie do tabeli historii zmian schematu, aby uzyskać identyfikator ostatnio zastosowanego polecenia zmiany schematu.

1.  Wykonaj zapytanie względem tabeli śledzenia zmian schematu, aby wyświetlić listę wszystkich poleceń o IDENTYFIKATORze większym niż wartość identyfikatora, która została pobrana w poprzednim kroku.

    a.  Ignoruj te polecenia, których nie można wykonać w bazie danych punktu końcowego. Należy zająć się niespójnością schematu. Przywróć oryginalne zmiany schematu, jeśli niespójność ma wpływ na aplikację.

    b.  Ręcznie Zastosuj te polecenia, które powinny być stosowane.

1.  Zaktualizuj tabelę historii zmian schematu i ustaw prawidłową wartość dla ostatniego zastosowanego identyfikatora.

1.  Sprawdź dokładnie, czy schemat jest aktualny.

1.  Ponownie włącz wyzwalacz AFTER INSERT wyłączony w drugim kroku.

1.  Ponownie włącz wyzwalacz DDL wyłączony w pierwszym kroku.

Jeśli chcesz wyczyścić rekordy w tabeli śledzenia zmian schematu, użyj polecenia DELETE zamiast Truncate. Nigdy nie należy odpełniać kolumny Identity w tabeli śledzenia zmian schematu przy użyciu DBCC CHECKIDENT. Można utworzyć nowe tabele śledzenia zmian schematu i zaktualizować nazwę tabeli w wyzwalaczu DDL, jeśli jest wymagane ponowne umieszczanie.

## <a name="other-considerations"></a><a name="other"></a> Inne zagadnienia

-   Użytkownicy baz danych, którzy konfigurują centrum i bazy danych elementów członkowskich, muszą mieć wystarczające uprawnienia do wykonywania poleceń zmiany schematu.

-   Można dodać więcej filtrów w wyzwalaczu DDL, aby replikować zmiany schematu w wybranych tabelach lub operacjach.

-   W bazie danych, w której jest tworzony wyzwalacz DDL, można wprowadzać tylko zmiany w schemacie.

-   W przypadku wprowadzania zmian w bazie danych SQL Server upewnij się, że zmiana schematu jest obsługiwana w Azure SQL Database.

-   W przypadku wprowadzenia zmian schematu w bazach danych innych niż baza danych, w której jest tworzony wyzwalacz DDL, zmiany nie są replikowane. Aby uniknąć tego problemu, można utworzyć wyzwalacze DDL do blokowania zmian w innych punktach końcowych.

-   Jeśli trzeba zmienić schemat tabeli śledzenia zmian schematu, należy wyłączyć wyzwalacz DDL przed wprowadzeniem zmiany, a następnie ręcznie zastosować zmiany do wszystkich punktów końcowych. Aktualizacja schematu w wyzwalaczu AFTER INSERT w tej samej tabeli nie działa.

-   Nie należy odpełniać kolumny tożsamości za pomocą DBCC CHECKIDENT.

-   Nie używaj OBCINAnia, aby oczyścić dane w tabeli śledzenia zmian schematu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

-   Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
-   Konfigurowanie synchronizacji danych
    - W portalu — [Samouczek: skonfiguruj SQL Data Sync, aby synchronizować dane między Azure SQL Database i SQL Server](sql-data-sync-sql-server-configure.md)
    - Z programem PowerShell
        -  [Synchronizowanie wielu baz danych w Azure SQL Database przy użyciu programu PowerShell](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Używanie programu PowerShell do synchronizowania bazy danych w Azure SQL Database i bazy danych w wystąpieniu SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](sql-data-sync-agent-overview.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
-   Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](sql-data-sync-best-practices.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
-   Monitor- [monitor SQL Data Sync z dziennikami Azure monitor](./monitor-tune-overview.md)
-   Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync]() (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
-   Aktualizowanie schematu synchronizacji
    -   Używanie programu PowerShell — [Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](scripts/update-sync-schema-in-sync-group.md)