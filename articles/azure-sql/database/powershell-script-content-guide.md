---
title: Przykłady skryptów Azure PowerShell
description: Przykłady skryptów Azure PowerShell ułatwiają tworzenie Azure SQL Database i zarządzanie zasobami wystąpienia zarządzanego usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 77220fabc78dd10b4299219ef84c1f6eb32ba82e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85987324"
---
# <a name="azure-powershell-samples-for-azure-sql-database-and-azure-sql-managed-instance"></a>Przykłady Azure PowerShell dla Azure SQL Database i wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database i wystąpienie zarządzane usługi Azure SQL umożliwiają konfigurowanie baz danych, wystąpień i pul przy użyciu Azure PowerShell.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga AZ PowerShell 1.4.0 lub nowszego. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Poniższa tabela zawiera linki do przykładowych skryptów programu Azure PowerShell dla usługi Azure SQL Database.

|Łącze|Opis|
|---|---|
|**Tworzenie i Konfigurowanie pojedynczych baz danych i pul elastycznych**||
| [Tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory na poziomie serwera](scripts/create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy pojedynczą bazę danych i konfiguruje regułę zapory adresów IP na poziomie serwera. |
| [Tworzenie pul elastycznych i przenoszenie baz danych w puli](scripts/move-database-between-elastic-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy pule elastyczne, przenosi bazy danych w puli i zmienia rozmiary obliczeniowe.|
|**Konfigurowanie replikacji geograficznej i trybu failover**||
| [Konfigurowanie i przełączanie w tryb failover pojedynczej bazy danych przy użyciu aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej dla pojedynczej bazy danych i przełączenie jej w tryb failover do repliki pomocniczej. |
| [Konfigurowanie i przełączanie w tryb failover bazy danych w puli przy użyciu aktywnej replikacji geograficznej](scripts/setup-geodr-and-failover-elastic-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie aktywnej replikacji geograficznej bazy danych w elastycznej puli i przełączenie jej w tryb failover do repliki pomocniczej. |
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla pojedynczej bazy danych](scripts/add-database-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy bazę danych i grupę trybu failover, dodaje bazę danych do grupy trybu failover i testuje tryb failover na serwerze pomocniczym. |
| [Konfigurowanie grupy trybu failover dla puli elastycznej](scripts/add-elastic-pool-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy bazę danych, dodaje ją do puli elastycznej, dodaje pulę elastyczną do grupy trybu failover i testuje tryb failover na serwerze pomocniczym. |
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
| [Synchronizowanie danych między bazami danych](scripts/sql-data-sync-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell konfiguruje synchronizację danych do synchronizacji między wieloma bazami danych w Azure SQL Database. |
| [Synchronizowanie danych między bazą danych SQL Database i lokalnym programem SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia skonfigurowanie synchronizacji danych w celu synchronizacji między bazą danych w Azure SQL Database a SQL Server lokalną bazą danych. |
| [Aktualizacja schematu synchronizacji usługi SQL Data Sync](scripts/update-sync-schema-in-sync-group.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell umożliwia dodanie lub usunięcie elementu ze schematu synchronizacji usługi Data Sync. |
|||

Dowiedz się więcej o [interfejsie API Azure PowerShell pojedynczej bazy danych](single-database-manage.md#powershell). 

## <a name="azure-sql-managed-instance"></a>[Wystąpienie zarządzane Azure SQL](#tab/managed-instance)

Poniższa tabela zawiera linki do przykładowych skryptów Azure PowerShell dla wystąpienia zarządzanego Azure SQL.

|Łącze|Opis|
|---|---|
|**Tworzenie i konfigurowanie wystąpień zarządzanych**||
| [Tworzenie wystąpienia zarządzanego i zarządzanie nim](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu Azure PowerShell. |
| [Tworzenie wystąpienia zarządzanego i zarządzanie nim przy użyciu szablonu Azure Resource Manager](../managed-instance/scripts/create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell pokazuje, jak utworzyć wystąpienie zarządzane i zarządzać nim przy użyciu Azure PowerShell i szablonu Azure Resource Manager.|
| [Przywracanie bazy danych do wystąpienia zarządzanego w innym regionie geograficznym](../managed-instance/scripts/restore-geo-backup.md) | Ten skrypt programu PowerShell tworzy kopię zapasową jednej bazy danych i przywraca ją do innego regionu. Jest to tzw. scenariusz odzyskiwania po awarii w przypadku przywracania geograficznego. |
| **Konfigurowanie przezroczystego szyfrowania danych**||
| [Zarządzaj przezroczystym szyfrowaniem danych w wystąpieniu zarządzanym przy użyciu własnego klucza z Azure Key Vault](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ten skrypt programu PowerShell umożliwia skonfigurowanie funkcji przezroczystego szyfrowania danych w Bring Your Own Key scenariuszu dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z Azure Key Vault.|
|**Konfigurowanie grupy trybu failover**||
| [Konfigurowanie grupy trybu failover dla wystąpienia zarządzanego](../managed-instance/scripts/add-to-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ten skrypt programu PowerShell tworzy dwa wystąpienia zarządzane, dodaje je do grupy trybu failover, a następnie testuje tryb failover z podstawowego wystąpienia zarządzanego do pomocniczego wystąpienia zarządzanego. |
|||

Dowiedz się więcej [na temat poleceń cmdlet programu PowerShell dla wystąpienia zarządzanego Azure SQL](../managed-instance/api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances).

---

## <a name="additional-resources"></a>Zasoby dodatkowe

Przykłady wymienione na tej stronie używają [poleceń cmdlet programu PowerShell](/powershell/module/az.sql/) do tworzenia zasobów usługi Azure SQL i zarządzania nimi. Dodatkowe polecenia cmdlet służące do uruchamiania zapytań i wykonywania wielu zadań bazy danych znajdują się w module [SqlServer](/powershell/module/sqlserver/) . Aby uzyskać więcej informacji, zobacz [SQL Server PowerShell](/sql/powershell/sql-server-powershell/).
