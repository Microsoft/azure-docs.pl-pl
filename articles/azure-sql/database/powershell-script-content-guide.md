---
title: Przykłady skryptów Azure PowerShell
description: Przykłady skryptów Azure PowerShell ułatwiające tworzenie Azure SQL Database i zasobów wystąpienia zarządzanego usługi Azure SQL oraz zarządzanie nimi
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 2d80291909b29576c60f39afc63ec174c091f1c5
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053357"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Przykłady Azure PowerShell dla Azure SQL Database i wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL umożliwiają konfigurowanie baz danych, wystąpień i pul przy użyciu Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure SQL Database.

| |  |
|---|---|
|**Tworzenie i konfigurowanie pojedynczych baz danych i elastycznych pul**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory na poziomie serwera](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy pojedynczą bazę danych i konfiguruje regułę zapory adresów IP na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy pule elastyczne, przenosi bazy danych w puli i zmienia rozmiary obliczeń.|
|**Konfigurowanie replikacji geograficznej i trybu failover**||
| [Konfigurowanie pojedynczej bazy danych i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla pojedynczej bazy danych i przełączenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie bazy danych w puli i wprowadzanie jej w tryb failover przy użyciu funkcji aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej bazy danych w puli elastycznej i przełączenie jej w tryb failover do repliki pomocniczej. |
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla pojedynczej bazy danych](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy bazę danych i grupę trybu failover, dodaje bazę danych do grupy trybu failover i testuje tryb failover na serwerze pomocniczym. |
| [Konfigurowanie grupy trybu failover dla puli elastycznej](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy bazę danych, dodaje ją do puli elastycznej, dodaje elastyczną pulę do grupy trybu failover i testuje tryb failover na serwerze pomocniczym. |
|**Skalowanie pojedynczej bazy danych i puli elastycznej**||
| [Skalowanie pojedynczej bazy danych](scripts/monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności pojedynczej bazy danych, skaluje je do wyższego rozmiaru i tworzy regułę alertu dla jednej z metryk wydajności. |
| [Skalowanie puli elastycznej](scripts/monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell monitoruje metryki wydajności elastycznej puli, skaluje je do większego rozmiaru i tworzy regułę alertu dla jednej z metryk wydajności. |
| **Inspekcja i wykrywanie zagrożeń** |
| [Konfigurowanie inspekcji i wykrywanie zagrożeń](scripts/auditing-threat-detection-powershell-configure.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie zasad inspekcji i wykrywania zagrożeń dla bazy danych. |
| **Przywracanie, kopiowanie i importowanie bazy danych**||
| [Przywracanie bazy danych](scripts/restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell przywraca bazę danych z geograficznie nadmiarowej kopii zapasowej i przywraca usuniętą bazę danych do najnowszej kopii zapasowej. |
| [Kopiowanie bazy danych na nowy serwer](scripts/copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell tworzy kopię istniejącej bazy danych na nowym serwerze. |
| [Importowanie bazy danych z pliku BACPAC](scripts/import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell importuje bazę danych do Azure SQL Database z pliku BACPAC. |
| **Synchronizowanie danych między bazami danych**||
| [Synchronizowanie danych między bazami danych SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell konfiguruje synchronizację danych do synchronizacji między wieloma bazami danych w Azure SQL Database. |
| [Synchronizowanie danych między bazą danych SQL Database i lokalnym programem SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie synchronizacji danych w celu synchronizacji między bazą danych w Azure SQL Database a SQL Server lokalną bazą danych. |
| [Aktualizacja schematu synchronizacji usługi SQL Data Sync](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia dodanie lub usunięcie elementu ze schematu synchronizacji usługi Data Sync. |
|||

Dowiedz się więcej o [interfejsie API programu Azure PowerShell pojedynczej bazy danych](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Wystąpienie zarządzane Azure SQL](#tab/managed-instance)

Poniższa tabela zawiera linki do przykładowych skryptów Azure PowerShell dla wystąpienia zarządzanego Azure SQL.

| |  |
|---|---|
|**Tworzenie i konfigurowanie wystąpień zarządzanych**||
| [Tworzenie wystąpienia zarządzanego i zarządzanie nim](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu Azure PowerShell |
| [Tworzenie wystąpienia zarządzanego i zarządzanie nim przy użyciu szablonu Azure Resource Manager](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu szablonu Azure PowerShell i Azure Resource Manager.|
| [Przywracanie bazy danych do wystąpienia zarządzanego w innym regionie geograficznym](../managed-instance/scripts/restore-geo-backup.md) | Ten skrypt programu PowerShell wykonuje kopię zapasową jednej bazy danych i przywraca ją do innego regionu. Jest to tzw. scenariusz odzyskiwania po awarii w przypadku przywracania geograficznego. |
| **Konfigurowanie funkcji Transparent Data Encryption (TDE)**||
| [Zarządzaj Transparent Data Encryption w zarządzanym wystąpieniu przy użyciu własnego klucza z Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell konfiguruje funkcję Transparent Data Encryption (TDE) w scenariuszu własnego klucza (BYOK, Bring Your Own Key) dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z usługi Azure Key Vault|
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla wystąpienia zarządzanego](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje tryb failover z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego. |
|||

Dowiedz się więcej [na temat poleceń cmdlet programu PowerShell dla wystąpienia zarządzanego Azure SQL](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances).

---

## <a name="additional-resources"></a>Zasoby dodatkowe

Przykłady wymienione na tej stronie używają [poleceń cmdlet programu PowerShell](/powershell/module/az.sql/) do tworzenia zasobów usługi Azure SQL i zarządzania nimi. Dodatkowe polecenia cmdlet służące do uruchamiania zapytań i wykonywania wielu zadań bazy danych znajdują się w module [SqlServer](/powershell/module/sqlserver/) . Aby uzyskać więcej informacji, zobacz [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
