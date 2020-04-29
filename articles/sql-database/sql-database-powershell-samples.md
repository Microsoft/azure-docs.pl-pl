---
title: Przykłady skryptów Azure PowerShell
description: Przykładowe skrypty programu Azure PowerShell umożliwiające tworzenie serwerów, elastycznych pul, baz danych i zapór usługi Azure SQL Database oraz zarządzanie nimi.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: dd16753a9b057e441884b0a6a019701766aaa321
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73821400"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Przykłady programu Azure PowerShell dla usługi Azure SQL Database

Usługa Azure SQL Database umożliwia konfigurowanie baz danych, wystąpień i pul za pomocą programu Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="single-database-and-elastic-pools"></a>[Pojedyncza baza danych i elastyczne pule](#tab/single-database)

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure SQL Database.

| |  |
|---|---|
|**Tworzenie i konfigurowanie pojedynczych baz danych i elastycznych pul**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory serwera bazy danych](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu Azure PowerShell tworzy jedną bazę danych Azure SQL Database i konfiguruje regułę zapory na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy elastyczne pule usługi Azure SQL Database, przenosi bazy danych w puli i zmienia rozmiary obliczeniowe.|
|**Konfigurowanie replikacji geograficznej i trybu failover**||
| [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla jednej bazy danych Azure SQL Database i wprowadzenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla bazy danych Azure SQL Database w elastycznej puli SQL i wprowadzenie jej w tryb failover do repliki pomocniczej. |
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla pojedynczej bazy danych](scripts/sql-database-add-single-db-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy bazę danych i grupę trybu failover, dodaje bazę danych do grupy trybu failover i testuje tryb failover na serwerze pomocniczym. | 
| [Konfigurowanie grupy trybu failover dla puli elastycznej](scripts/sql-database-add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy bazę danych, dodaje ją do puli elastycznej, dodaje elastyczną pulę do grupy trybu failover i testuje tryb failover na serwerze pomocniczym. | 
|**Skalowanie pojedynczej bazy danych i puli elastycznej**||
| [Skalowanie pojedynczej bazy danych](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności bazy danych Azure SQL Database, skaluje ją do większego rozmiaru obliczeniowego i tworzy regułę alertu dla jednej z metryk wydajności. |
| [Skalowanie puli elastycznej](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności elastycznej puli Azure SQL Database, skaluje ją do większego rozmiaru obliczeniowego i tworzy regułę alertu dla jednej z metryk wydajności. |
| **Inspekcja i wykrywanie zagrożeń** |
| [Konfigurowanie inspekcji i wykrywanie zagrożeń](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie inspekcji i wykrywania zagrożeń dla bazy danych Azure SQL Database. |
| **Przywracanie, kopiowanie i importowanie bazy danych**||
| [Przywracanie bazy danych](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia przywrócenie bazy danych Azure SQL Database z geograficznie nadmiarowej kopii zapasowej i przywrócenie najnowszej kopii zapasowej usuniętej bazy danych Azure SQL Database. |
| [Kopiowanie bazy danych na nowy serwer](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia utworzenie kopii istniejącej bazy danych Azure SQL Database na nowym serwerze Azure SQL. |
| [Importowanie bazy danych z pliku BACPAC](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia zaimportowanie bazy danych z pliku BACPAC na serwer Azure SQL. |
| **Synchronizowanie danych między bazami danych**||
| [Synchronizowanie danych między bazami danych SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie funkcji synchronizacji danych w celu przeprowadzenia synchronizacji wielu baz danych Azure SQL Database. |
| [Synchronizowanie danych między bazą danych SQL Database i lokalnym programem SQL Server](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie synchronizacji danych w celu przeprowadzenia synchronizacji pomiędzy bazą danych Azure SQL Database i lokalną bazą danych programu SQL Server. |
| [Aktualizacja schematu synchronizacji usługi SQL Data Sync](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia dodanie lub usunięcie elementu ze schematu synchronizacji usługi Data Sync. |
|||

Dowiedz się więcej o [interfejsie API programu Azure PowerShell pojedynczej bazy danych](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>[Wystąpienie zarządzane](#tab/managed-instance)

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla wystąpienia zarządzanego usługi Azure SQL Database.

| |  |
|---|---|
|**Tworzenie i konfigurowanie wystąpień zarządzanych**||
| [Tworzenie wystąpienia zarządzanego i zarządzanie nim](scripts/sql-database-create-configure-managed-instance-powershell.md) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu programu Azure PowerShell |
| [Tworzenie wystąpienia zarządzanego za pomocą szablonu usługi Azure Resource Manager oraz zarządzanie nim](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu programu Azure PowerShell i szablonu usługi Azure Resource Manager.|
| [Przywracanie bazy danych do wystąpienia zarządzanego w innym regionie geograficznym](scripts/sql-managed-instance-restore-geo-backup.md) | Ten skrypt programu PowerShell wykonuje kopię zapasową jednej bazy danych i przywraca ją do innego regionu. Jest to tzw. scenariusz odzyskiwania po awarii w przypadku przywracania geograficznego. |
| **Konfigurowanie funkcji Transparent Data Encryption (TDE)**||
| [Zarządzanie funkcją Transparent Data Encryption w wystąpieniu zarządzanym przy użyciu własnego klucza z usługi Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell konfiguruje funkcję Transparent Data Encryption (TDE) w scenariuszu własnego klucza (BYOK, Bring Your Own Key) dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z usługi Azure Key Vault|
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla wystąpienia zarządzanego](scripts/sql-database-add-managed-instance-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje tryb failover z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego. | 
|||

Dowiedz się więcej o [interfejsie API programu Azure PowerShell wystąpienia zarządzanego](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Dodatkowe zasoby

Przykłady wymienione na tej stronie używają [Azure SQL Database poleceń cmdlet](/powershell/module/az.sql/) do tworzenia zasobów usługi Azure SQL i zarządzania nimi. Dodatkowe polecenia cmdlet służące do uruchamiania zapytań i wykonywania wielu zadań bazy danych znajdują się w module [SqlServer](/powershell/module/sqlserver/) . Aby uzyskać więcej informacji, zobacz [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
