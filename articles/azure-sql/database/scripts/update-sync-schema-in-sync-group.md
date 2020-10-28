---
title: 'PowerShell: Aktualizuj schemat synchronizacji SQL Data Sync'
description: Przykładowy skrypt z programu Azure PowerShell służący do aktualizowania schematu synchronizacji dla funkcji SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792722"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ten Azure PowerShell przykład aktualizuje schemat synchronizacji w istniejącej grupie synchronizacji SQL Data Sync. Podczas synchronizowania wielu tabel ten skrypt ułatwia wydajnie aktualizowanie schematu synchronizacji. W tym przykładzie przedstawiono użycie skryptu **UpdateSyncSchema** , który jest dostępny w witrynie GitHub jako [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> W tej chwili SQL Data Sync nie obsługuje wystąpienia zarządzanego usługi Azure SQL.

## <a name="examples"></a>Przykłady

### <a name="add-all-tables-to-the-sync-schema"></a>Dodaj wszystkie tabele do schematu synchronizacji

Poniższy przykład służy do odświeżania schematu bazy danych i dodawania wszystkich prawidłowych tabel w bazie danych centrum do schematu synchronizacji.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Dodawanie i usuwanie tabel i kolumn

Poniższy przykład służy do dodawania tabel `[dbo].[Table1]` i `[dbo].[Table2].[Column1]` do schematu synchronizacji oraz usuwania tabeli `[dbo].[Table3]`.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parametry skryptu

Skrypt **UpdateSyncSchema** ma następujące parametry:

| Parametr | Uwagi |
|---|---|
| $subscriptionId | Subskrypcja, w ramach której tworzona jest grupa synchronizacji. |
| $resourceGroupName | Grupa zasobów, w ramach której tworzona jest grupa synchronizacji.|
| $serverName | Nazwa serwera bazy danych centrum.|
| $databaseName | Nazwa bazy danych centrum. |
| $syncGroupName | Nazwa grupy synchronizacji. |
| $memberName | Określ nazwę elementu członkowskiego, jeśli schemat bazy danych ma zostać załadowany z elementu członkowskiego synchronizacji zamiast z bazy danych centrum. Jeśli schemat bazy danych ma zostać załadowany z centrum, pozostaw ten parametr pusty. |
| $timeoutInSeconds | Limit czasu odświeżania schematu bazy danych przez skrypt. Wartość domyślna to 900 sekund. |
| $refreshDatabaseSchema | Określ, czy skrypt ma odświeżać schemat bazy danych. Jeśli schemat bazy danych został zmieniony z poprzedniej konfiguracji (na przykład jeśli dodano nową tabelę lub kolumnę nową), należy odświeżyć schemat przed jego ponownym skonfigurowaniem. Wartość domyślna to false. |
| $addAllTables | Jeśli ta wartość to true, wszystkie prawidłowe tabele i kolumny zostaną dodane do schematu synchronizacji. Wartości parametrów $TablesAndColumnsToAdd i $TablesAndColumnsToRemove są ignorowane. |
| $tablesAndColumnsToAdd | Określ tabelę lub kolumny do dodania do schematu synchronizacji. Wszystkie nazwy tabel lub kolumn muszą być w pełni rozdzielone od nazwy schematu. Przykład: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Można określić wiele nazw tabel lub kolumn i oddzielić je przecinkami (,). |
| $tablesAndColumnsToRemove | Określ tabele lub kolumny do usunięcia ze schematu synchronizacji. Wszystkie nazwy tabel lub kolumn muszą być w pełni rozdzielone od nazwy schematu. Przykład: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Można określić wiele nazw tabel lub kolumn i oddzielić je przecinkami (,). |

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Skrypt **UpdateSyncSchema** używa następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | Zwraca informacje o grupie synchronizacji. |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | Aktualizuje grupę synchronizacji. |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | Zwraca informacje na temat elementu członkowskiego synchronizacji. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | Zwraca informacje o schemacie synchronizacji. |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | Aktualizuje schemat synchronizacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../powershell-script-content-guide.md).

Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz:

- Przegląd — [Synchronizacja danych między Azure SQL Database i SQL Server za pomocą SQL Data Sync na platformie Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Konfigurowanie synchronizacji danych
    - Użyj [samouczka Azure Portal: skonfiguruj SQL Data Sync do synchronizowania danych między Azure SQL Database i SQL Server](../sql-data-sync-sql-server-configure.md)
    - Korzystanie z programu PowerShell
        -  [Synchronizowanie danych między wieloma bazami danych w Azure SQL Database przy użyciu programu PowerShell](sql-data-sync-sync-data-between-sql-databases.md)
        -  [Synchronizuj dane między Azure SQL Database i SQL Server przy użyciu programu PowerShell](sql-data-sync-sync-data-between-azure-onprem.md)
- Agent synchronizacji danych — [Agent synchronizacji danych dla SQL Data Sync na platformie Azure](../sql-data-sync-agent-overview.md)
- Najlepsze rozwiązania — [najlepsze rozwiązania dotyczące SQL Data Sync na platformie Azure](../sql-data-sync-best-practices.md)
- Monitor- [monitor SQL Data Sync z dziennikami Azure monitor](../monitor-tune-overview.md)
- Rozwiązywanie problemów — [Rozwiązywanie problemów z SQL Data Sync na platformie Azure](../sql-data-sync-troubleshoot.md)
- Aktualizowanie schematu synchronizacji
    - Korzystanie z języka Transact-SQL — [Automatyzowanie replikacji zmian schematu w SQL Data Sync na platformie Azure](../sql-data-sync-update-sync-schema.md)

Aby uzyskać więcej informacji na temat SQL Database, zobacz:

- [Przegląd SQL Database](../sql-database-paas-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))