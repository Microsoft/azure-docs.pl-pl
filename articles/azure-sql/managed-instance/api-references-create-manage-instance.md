---
title: interfejs API Zarządzanie dla Azure SQL Managed Instance
description: Dowiedz się więcej na temat tworzenia i konfigurowania wystąpień zarządzanych Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 148b24aea42072f1901c76c7a09a126340ef9951
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784375"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Dokumentacja zarządzanego interfejsu API dla usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienia zarządzane aplikacji można tworzyć i konfigurować przy użyciu Azure SQL Managed Instance języka Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i języka Transact-SQL. Ten artykuł zawiera omówienie funkcji i interfejsu API, których można używać do tworzenia i konfigurowania wystąpień zarządzanych.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Tworzenie wystąpienia zarządzanego

Aby uzyskać przewodnik Szybki start przedstawiający sposób tworzenia wystąpienia zarządzanego, zobacz [Szybki start: tworzenie wystąpienia zarządzanego.](instance-create-quickstart.md)

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: tworzenie i konfigurowanie wystąpień zarządzanych

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł powershell Azure Resource Manager jest nadal obsługiwany przez Azure SQL Database, ale cały przyszły rozwój jest dla modułu Az.Sql. Aby uzyskać informacje na temat tych poleceń cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty poleceń w module Az i modułach AzureRM są zasadniczo identyczne.

Aby tworzyć wystąpienia zarządzane i zarządzać nimi za Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps).

> [!TIP]
> Aby uzyskać przykładowe skrypty programu PowerShell, zobacz [Szybki start script: Create a managed instance using a PowerShell library (Skrypt szybkiego startu: tworzenie wystąpienia zarządzanego przy użyciu biblioteki programu PowerShell).](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell)

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Tworzy wystąpienie zarządzane. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Zwraca informacje o wystąpieniu zarządzanym.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Ustawia właściwości wystąpienia zarządzanego.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Usuwa wystąpienie zarządzane.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Pobiera listę operacji zarządzania wykonywanych na wystąpieniu zarządzanym lub określonej operacji.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Anuluje określoną operację zarządzania wykonywaną w wystąpieniu zarządzanym.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Tworzy bazę SQL Managed Instance danych.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Zwraca informacje o SQL Managed Instance danych.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Usuwa SQL Managed Instance danych.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Przywraca bazę SQL Managed Instance danych.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Interfejs wiersza polecenia platformy Azure: tworzenie i konfigurowanie wystąpień zarządzanych

Aby tworzyć i konfigurować wystąpienia zarządzane za pomocą interfejsu wiersza polecenia platformy [Azure,](/cli/azure)użyj następujących poleceń interfejsu wiersza polecenia platformy [Azure SQL Managed Instance](/cli/azure/sql/mi). Użyj [Azure Cloud Shell,](../../cloud-shell/overview.md) aby uruchomić interfejs wiersza polecenia w przeglądarce, [lub](/cli/azure/install-azure-cli) zainstaluj go w systemie macOS, Linux lub Windows.

> [!TIP]
> Aby uzyskać przewodnik Szybki start dla interfejsu wiersza polecenia platformy Azure, zobacz Working with SQL Managed Instance using Azure CLI (Praca z interfejsem wiersza polecenia [platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure).](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az_sql_mi_create) |Tworzy wystąpienie zarządzane.|
|[az sql mi list](/cli/azure/sql/mi#az_sql_mi_list)|Wyświetla listę dostępnych wystąpień zarządzanych.|
|[az sql mi show](/cli/azure/sql/mi#az_sql_mi_show)|Pobiera szczegóły wystąpienia zarządzanego.|
|[az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)|Aktualizuje wystąpienie zarządzane.|
|[az sql mi delete](/cli/azure/sql/mi#az_sql_mi_delete)|Usuwa wystąpienie zarządzane.|
|[az sql mi op list](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Pobiera listę operacji zarządzania wykonywanych na wystąpieniu zarządzanym.|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Pobiera określoną operację zarządzania wykonywaną na wystąpieniu zarządzanym.|
|[az sql mi op cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Anuluje określoną operację zarządzania wykonywaną w wystąpieniu zarządzanym.|
|[az sql midb create](/cli/azure/sql/midb#az_sql_midb_create) |Tworzy zarządzaną bazę danych.|
|[az sql midb list](/cli/azure/sql/midb#az_sql_midb_list)|Wyświetla listę dostępnych zarządzanych baz danych.|
|[az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore)|Przywraca zarządzaną bazę danych.|
|[az sql midb delete](/cli/azure/sql/midb#az_sql_midb_delete)|Usuwa zarządzaną bazę danych.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Tworzenie i konfigurowanie baz danych wystąpień

Aby utworzyć i skonfigurować bazy danych wystąpień po utworzeniu wystąpienia zarządzanego, użyj następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu poleceń Azure Portal, [SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio) [Azure Data Studio,](/sql/azure-data-studio/what-is) [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może połączyć się z serwerem i przekazać polecenia języka Transact-SQL.

> [!TIP]
> Aby uzyskać informacje na temat konfigurowania wystąpienia zarządzanego i nawiązywania z nimi połączenia przy użyciu usługi SQL Server Management Studio w systemie Microsoft Windows, zobacz Szybki [start:](connect-vm-instance-configure.md) konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z usługą Azure SQL Managed Instance i Szybki start: konfigurowanie połączenia [punkt-lokacja](point-to-site-p2s-configure.md)z usługą Azure SQL Managed Instance z lokalnego systemu .

> [!IMPORTANT]
> Nie można utworzyć ani usunąć wystąpienia zarządzanego przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Tworzy nową bazę danych wystąpienia w SQL Managed Instance. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych master.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Modyfikuje bazę danych wystąpienia w SQL Managed Instance.|

## <a name="rest-api-create-and-configure-managed-instances"></a>Interfejs API REST: tworzenie i konfigurowanie wystąpień zarządzanych

Aby utworzyć i skonfigurować wystąpienia zarządzane, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Wystąpienia zarządzane — tworzenie lub aktualizowanie](/rest/api/sql/managedinstances/createorupdate)|Tworzy lub aktualizuje wystąpienie zarządzane.|
|[Wystąpienia zarządzane — usuwanie](/rest/api/sql/managedinstances/delete)|Usuwa wystąpienie zarządzane.|
|[Wystąpienia zarządzane — pobierz](/rest/api/sql/managedinstances/get)|Pobiera wystąpienie zarządzane.|
|[Wystąpienia zarządzane — lista](/rest/api/sql/managedinstances/list)|Zwraca listę wystąpień zarządzanych w subskrypcji.|
|[Wystąpienia zarządzane — lista według grupy zasobów](/rest/api/sql/managedinstances/listbyresourcegroup)|Zwraca listę wystąpień zarządzanych w grupie zasobów.|
|[Wystąpienia zarządzane — aktualizacja](/rest/api/sql/managedinstances/update)|Aktualizuje wystąpienie zarządzane.|
|[Operacje wystąpienia zarządzanego — lista według wystąpienia zarządzanego](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Pobiera listę operacji zarządzania wykonywanych na wystąpieniu zarządzanym.|
|[Operacje wystąpienia zarządzanego — pobierz](/rest/api/sql/managedinstanceoperations/get)|Pobiera określoną operację zarządzania wykonywaną na wystąpieniu zarządzanym.|
|[Operacje wystąpienia zarządzanego — anulowanie](/rest/api/sql/managedinstanceoperations/cancel)|Anuluje określoną operację zarządzania wykonywaną w wystąpieniu zarządzanym.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o migrowaniu bazy danych SQL Server na platformę Azure, zobacz [Migrowanie](../database/migrate-to-database-from-sql-server.md)do Azure SQL Database .
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](../database/features-comparison.md).