---
title: 'Interfejs API REST: synchronizacja między wieloma bazami danych'
description: Użyj skryptu przykładowego interfejsu API REST do synchronizacji między wieloma bazami danych.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: ef2823b870f76922dd0dc157341aea9b502fb4bb
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565541"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Synchronizowanie danych między wieloma bazami danych przy użyciu interfejsu API REST 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ten przykład interfejsu API REST służy do konfigurowania SQL Data Sync synchronizowania danych między wieloma bazami danych.

Aby zapoznać się z omówieniem SQL Data Sync, zobacz [synchronizowanie danych między wieloma bazami danych w chmurze i lokalnymi przy użyciu SQL Data Sync na platformie Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> W tej chwili SQL Data Sync nie obsługuje wystąpienia zarządzanego usługi Azure SQL.

## <a name="create-sync-group"></a>Utwórz grupę synchronizacji

Użyj szablonu [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate) , aby utworzyć grupę synchronizacji.
 
Podczas tworzenia grupy synchronizacji nie przekazuj schematu synchronizacji (table\column) i nie przekazuj masterSyncMemberName, ponieważ w tej grupie synchronizacji nie ma jeszcze informacji o table\column.

Przykładowe żądanie utworzenia grupy synchronizacji: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Przykładowa odpowiedź na potrzeby tworzenia grupy synchronizacji:

Kod stanu: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Kod stanu: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Utwórz element członkowski synchronizacji

Użyj szablonu [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate) , aby utworzyć element członkowski synchronizacji.

Przykładowe żądanie utworzenia elementu członkowskiego synchronizacji:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Przykładowa odpowiedź na potrzeby tworzenia elementu członkowskiego synchronizacji:

Kod stanu: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Kod stanu: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Odśwież schemat

Po pomyślnym utworzeniu grupy synchronizacji Odśwież schemat przy użyciu następujących szablonów.

Aby odświeżyć schemat bazy danych centrów, użyj szablonu [schematu Refresh Hub](https://docs.microsoft.com/rest/api/sql/syncgroups/refreshhubschema)  . 

Przykładowe żądanie odświeżania schematu bazy danych centrum: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Przykładowa odpowiedź na potrzeby odświeżania schematu bazy danych centrum: 

Kod stanu: 200

Kod stanu: 202

Użyj szablonu [schematy centrów listy](https://docs.microsoft.com/rest/api/sql/syncgroups/listhubschemas) , aby wyświetlić schemat bazy danych centrum. 

Użyj szablonu [schematu Odśwież element członkowski](https://docs.microsoft.com/rest/api/sql/syncmembers/refreshmemberschema) , aby odświeżyć schemat bazy danych elementu członkowskiego. 

Użyj szablonu [schematu elementu członkowskiego listy](https://docs.microsoft.com/rest/api/sql/syncmembers/listmemberschemas) , aby wyświetlić schemat bazy danych elementu członkowskiego. 

Przejdź do następnego kroku, gdy schemat zostanie pomyślnie odświeżony. 

## <a name="update-sync-group"></a>Aktualizowanie grupy synchronizacji 

Użyj szablonu [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate) , aby zaktualizować grupę synchronizacji.

Zaktualizuj grupę synchronizacji, określając schemat synchronizacji. Dołącz schemat i masterSyncMemberName, czyli nazwę, która zawiera schemat, którego chcesz użyć. 

Przykładowe żądanie aktualizacji grupy synchronizacji: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Przykładowa odpowiedź dotycząca aktualizacji grupy synchronizacji: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Aktualizuj element członkowski synchronizacji

Użyj szablonu [Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate) , aby zaktualizować element członkowski synchronizacji.

Przykładowe żądanie aktualizacji elementu członkowskiego synchronizacji: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Przykładowa odpowiedź na potrzeby aktualizacji elementu członkowskiego synchronizacji: 

Kod stanu: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Kod stanu: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Synchronizuj wyzwalacz

Użyj szablonu [synchronizacji wyzwalacza](https://docs.microsoft.com/rest/api/sql/syncgroups/triggersync) , aby wyzwolić operację synchronizacji.

Przykładowe żądanie wyzwalania operacji synchronizacji: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Przykładowa odpowiedź na potrzeby wyzwalania operacji synchronizacji: 

Kod stanu: 200

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/).

Więcej przykładowych skryptów programu PowerShell dla usługi SQL Database można znaleźć w [skryptach programu PowerShell dla usługi Azure SQL Database](../powershell-script-content-guide.md).

Aby uzyskać więcej informacji na temat SQL Data Sync, zobacz:

- Przegląd — [Synchronizacja danych między wieloma bazami danych w chmurze i lokalnymi przy użyciu SQL Data Sync na platformie Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Konfigurowanie synchronizacji danych
    - Użyj [samouczka Azure Portal: skonfiguruj SQL Data Sync do synchronizowania danych między Azure SQL Database i SQL Server](../sql-data-sync-sql-server-configure.md)
    - Korzystanie z programu PowerShell — [Używanie programu PowerShell do synchronizowania danych między bazą danych w Azure SQL Database i SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Agent synchronizacji danych — [Agent synchronizacji danych dla SQL Data Sync na platformie Azure](../sql-data-sync-agent-overview.md)
- Najlepsze rozwiązania — [najlepsze rozwiązania dotyczące SQL Data Sync na platformie Azure](../sql-data-sync-best-practices.md)
- Monitor- [monitor SQL Data Sync z dziennikami Azure monitor](../monitor-tune-overview.md)
- Rozwiązywanie problemów — [Rozwiązywanie problemów z SQL Data Sync na platformie Azure](../sql-data-sync-troubleshoot.md)
- Aktualizowanie schematu synchronizacji
    - Korzystanie z języka Transact-SQL — [Automatyzowanie replikacji zmian schematu w SQL Data Sync na platformie Azure](../sql-data-sync-update-sync-schema.md)
    - Korzystanie z programu PowerShell — [Używanie programu PowerShell do aktualizowania schematu synchronizacji w istniejącej grupie synchronizacji](update-sync-schema-in-sync-group.md)

Aby uzyskać więcej informacji na temat SQL Database, zobacz:

- [Przegląd SQL Database](../sql-database-paas-overview.md)
- [Database Lifecycle Management (Zarządzanie cyklem życia bazy danych)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
