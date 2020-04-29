---
title: Przykład programu PowerShell — aktualizacja schematu synchronizacji SQL Data Sync
description: Przykładowy skrypt z programu Azure PowerShell służący do aktualizowania schematu synchronizacji dla funkcji SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 00f9e217df65838e75f351dfe00fb60594acdcfc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81383492"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Używanie programu PowerShell do zaktualizowania schematu synchronizacji w istniejącej grupie synchronizacji

Ten przykład z programu PowerShell służy do aktualizacji schematu synchronizacji w istniejącej grupie synchronizacji funkcji SQL Data Sync. Podczas synchronizowania wielu tabel ten skrypt ułatwia wydajnie aktualizowanie schematu synchronizacji. W tym przykładzie przedstawiono użycie skryptu **UpdateSyncSchema**, który jest dostępny w witrynie GitHub jako [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

Omówienie usługi SQL Data Sync zawiera temat [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)](../sql-database-sync-data.md).

> [!IMPORTANT]
> Usługa Azure SQL Data Sync nie obsługuje teraz wystąpienia zarządzanego usługi Azure SQL Database.

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
| $refreshDatabaseSchema | Określ, czy skrypt ma odświeżać schemat bazy danych. Jeśli schemat bazy danych został zmieniony względem poprzedniej konfiguracji, na przykład jeśli dodano nową tabelę lub od nową kolumnę, należy odświeżyć schemat przed jego ponownym skonfigurowaniem. Wartość domyślna to false. |
| $addAllTables | Jeśli ta wartość to true, wszystkie prawidłowe tabele i kolumny zostaną dodane do schematu synchronizacji. Wartości parametrów $TablesAndColumnsToAdd i $TablesAndColumnsToRemove są ignorowane. |
| $tablesAndColumnsToAdd | Określ tabelę lub kolumny do dodania do schematu synchronizacji. Wszystkie nazwy tabel lub kolumn muszą być w pełni rozdzielone od nazwy schematu. Przykład: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Można określić wiele nazw tabel lub kolumn i rozdzielić je przecinkami (,). |
| $tablesAndColumnsToRemove | Określ tabele lub kolumny do usunięcia ze schematu synchronizacji. Wszystkie nazwy tabel lub kolumn muszą być w pełni rozdzielone od nazwy schematu. Przykład: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Można określić wiele nazw tabel lub kolumn i rozdzielić je przecinkami (,). |

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Skrypt **UpdateSyncSchema** używa następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [Get-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncgroup) | Zwraca informacje o grupie synchronizacji. |
| [Update-AzSqlSyncGroup](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncgroup) | Aktualizuje grupę synchronizacji. |
| [Get-AzSqlSyncMember](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncmember) | Zwraca informacje o składniku synchronizacji. |
| [Get-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlsyncschema) | Zwraca informacje o schemacie synchronizacji. |
| [Update-AzSqlSyncSchema](https://docs.microsoft.com/powershell/module/az.sql/update-azsqlsyncschema) | Aktualizuje schemat synchronizacji. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell w usłudze SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../sql-database-powershell-samples.md).

Aby uzyskać więcej informacji na temat usługi SQL Data Sync, zobacz:

- Omówienie — [Sync data across multiple cloud and on-premises databases with Azure SQL Data Sync](../sql-database-sync-data.md) (Synchronizowanie danych między wieloma bazami danych w chmurze i lokalnie za pomocą usługi Azure SQL Data Sync)
- Konfigurowanie synchronizacji danych
    - W portalu — [Tutorial: Set up SQL Data Sync to sync data between Azure SQL Database and SQL Server on-premises](../sql-database-get-started-sql-data-sync.md) (Samouczek: konfigurowanie usługi SQL Data Sync w celu synchronizowania danych między usługą Azure SQL Database i lokalnym programem SQL Server)
    - Z programem PowerShell
        - [Użycie programu PowerShell do synchronizowania wielu baz danych Azure SQL Database](sql-database-sync-data-between-sql-databases.md)
        - [Use PowerShell to sync between an Azure SQL Database and a SQL Server on-premises database (Synchronizacja bazy danych usługi Azure SQL Database i lokalnej bazy danych programu SQL Server przy użyciu programu PowerShell)](sql-database-sync-data-between-azure-onprem.md)
- Agent synchronizacji danych — [Data Sync Agent for Azure SQL Data Sync](../sql-database-data-sync-agent.md) (Agent synchronizacji danych dla usługi Azure SQL Data Sync)
- Najlepsze rozwiązania — [Best practices for Azure SQL Data Sync](../sql-database-best-practices-data-sync.md) (Najlepsze rozwiązania dotyczące korzystania z usługi Azure SQL Data Sync)
- Monitor- [monitor SQL Data Sync z dziennikami Azure monitor](../sql-database-sync-monitor-oms.md)
- Rozwiązywanie problemów — [Troubleshoot issues with Azure SQL Data Sync](../sql-database-troubleshoot-data-sync.md) (Rozwiązywanie problemów z usługą Azure SQL Data Sync)
- Aktualizowanie schematu synchronizacji
    - Za pomocą języka Transact-SQL — [Automate the replication of schema changes in Azure SQL Data Sync](../sql-database-update-sync-schema.md) (Automatyzowanie replikacji zmian schematu w usłudze Azure SQL Data Sync)

Aby uzyskać więcej informacji na temat usługi SQL Database, zobacz:

- [Omówienie usługi SQL Database](../sql-database-technical-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](https://msdn.microsoft.com/library/jj907294.aspx)
