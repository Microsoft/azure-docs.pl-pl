---
title: Dokumentacja interfejsu API zarządzania dla wystąpienia zarządzanego usługi Azure SQL
description: Dowiedz się więcej na temat tworzenia i konfigurowania wystąpień zarządzanych wystąpienia zarządzanego Azure SQL.
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
ms.openlocfilehash: cf100861705bf1654b7206445c884b5fe315b06d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792637"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Dokumentacja zarządzanego interfejsu API dla usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Zarządzane wystąpienia wystąpienia zarządzanego usługi Azure SQL można tworzyć i konfigurować przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsów API REST i języka Transact-SQL. Ten artykuł zawiera omówienie funkcji i interfejsu API, za pomocą których można tworzyć i konfigurować wystąpienia zarządzane.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Tworzenie wystąpienia zarządzanego

Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak utworzyć wystąpienie zarządzane, zobacz [Szybki Start: Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: Tworzenie i Konfigurowanie wystąpień zarządzanych

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRM są zasadniczo identyczne.

Aby tworzyć wystąpienia zarządzane i zarządzać nimi za pomocą Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Przykładowe skrypty programu PowerShell można znaleźć w temacie [skrypt szybkiego startu: Tworzenie wystąpienia zarządzanego przy użyciu biblioteki programu PowerShell](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell).

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Tworzy wystąpienie zarządzane. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Zwraca informacje dotyczące wystąpienia zarządzanego.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Ustawia właściwości wystąpienia zarządzanego.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Usuwa wystąpienie zarządzane.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Pobiera listę operacji zarządzania wykonanych w wystąpieniu zarządzanym lub określonej operacji.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Anuluje konkretną operację zarządzania wykonywaną na zarządzanym wystąpieniu.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Tworzy bazę danych wystąpienia zarządzanego SQL.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Zwraca informacje o bazie danych wystąpienia zarządzanego SQL.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Usuwa bazę danych wystąpienia zarządzanego SQL.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Przywraca bazę danych wystąpienia zarządzanego SQL.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Interfejs wiersza polecenia platformy Azure: Tworzenie i Konfigurowanie wystąpień zarządzanych

Aby utworzyć i skonfigurować wystąpienia zarządzane za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure), użyj następujących [poleceń interfejsu wiersza polecenia platformy Azure dla wystąpienia zarządzanego SQL](/cli/azure/sql/mi). Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) , aby uruchomić interfejs wiersza polecenia w przeglądarce, lub [Zainstaluj](/cli/azure/install-azure-cli) go w systemie MacOS, Linux lub Windows.

> [!TIP]
> Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure zawiera temat [Praca z wystąpieniem zarządzanym SQL przy użyciu interfejsu wiersza polecenia platformy Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)

| Polecenie cmdlet | Opis |
| --- | --- |
|[AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create) |Tworzy wystąpienie zarządzane.|
|[AZ SQL mi list](/cli/azure/sql/mi#az-sql-mi-list)|Wyświetla dostępne wystąpienia zarządzane.|
|[AZ SQL mi show](/cli/azure/sql/mi#az-sql-mi-show)|Pobiera szczegóły wystąpienia zarządzanego.|
|[AZ SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje wystąpienie zarządzane.|
|[AZ SQL mi Delete](/cli/azure/sql/mi#az-sql-mi-delete)|Usuwa wystąpienie zarządzane.|
|[AZ SQL mi op list](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Pobiera listę operacji zarządzania wykonanych w wystąpieniu zarządzanym.|
|[AZ SQL mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Pobiera konkretną operację zarządzania wykonywaną na zarządzanym wystąpieniu.|
|[AZ SQL mi op Cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Anuluje konkretną operację zarządzania wykonywaną na zarządzanym wystąpieniu.|
|[AZ SQL MidB Create](/cli/azure/sql/midb#az-sql-midb-create) |Tworzy zarządzaną bazę danych.|
|[AZ SQL MidB list](/cli/azure/sql/midb#az-sql-midb-list)|Wyświetla listę dostępnych zarządzanych baz danych.|
|[AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore)|Przywraca zarządzaną bazę danych.|
|[AZ SQL MidB Delete](/cli/azure/sql/midb#az-sql-midb-delete)|Usuwa zarządzaną bazę danych.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Tworzenie i Konfigurowanie baz danych wystąpień

Aby utworzyć i skonfigurować wystąpienia baz danych po utworzeniu wystąpienia zarządzanego, należy użyć następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może nawiązać połączenie z serwerem i przekazać polecenia języka Transact-SQL.

> [!TIP]
> Przewodniki Szybki Start pokazujące, jak skonfigurować i połączyć się z wystąpieniem zarządzanym przy użyciu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Szybki Start: Konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym Azure SQL](connect-vm-instance-configure.md) i [Szybki Start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym Azure SQL z lokalnego](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Nie można utworzyć lub usunąć wystąpienia zarządzanego przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Tworzy nową bazę danych wystąpienia w wystąpieniu zarządzanym SQL. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Modyfikuje bazę danych wystąpienia w wystąpieniu zarządzanym SQL.|

## <a name="rest-api-create-and-configure-managed-instances"></a>Interfejs API REST: Tworzenie i Konfigurowanie wystąpień zarządzanych

Aby utworzyć i skonfigurować wystąpienia zarządzane, Użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Wystąpienia zarządzane — Utwórz lub zaktualizuj](/rest/api/sql/managedinstances/createorupdate)|Tworzy lub aktualizuje wystąpienie zarządzane.|
|[Wystąpienia zarządzane — usuwanie](/rest/api/sql/managedinstances/delete)|Usuwa wystąpienie zarządzane.|
|[Wystąpienia zarządzane — Pobierz](/rest/api/sql/managedinstances/get)|Pobiera wystąpienie zarządzane.|
|[Wystąpienia zarządzane — lista](/rest/api/sql/managedinstances/list)|Zwraca listę wystąpień zarządzanych w ramach subskrypcji.|
|[Wystąpienia zarządzane — lista według grupy zasobów](/rest/api/sql/managedinstances/listbyresourcegroup)|Zwraca listę wystąpień zarządzanych w grupie zasobów.|
|[Wystąpienia zarządzane — aktualizacja](/rest/api/sql/managedinstances/update)|Aktualizuje wystąpienie zarządzane.|
|[Operacje wystąpienia zarządzanego — lista według wystąpienia zarządzanego](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Pobiera listę operacji zarządzania wykonanych w wystąpieniu zarządzanym.|
|[Operacje wystąpienia zarządzanego — Pobierz](/rest/api/sql/managedinstanceoperations/get)|Pobiera konkretną operację zarządzania wykonywaną na zarządzanym wystąpieniu.|
|[Operacje wystąpienia zarządzanego — anulowanie](/rest/api/sql/managedinstanceoperations/cancel)|Anuluje konkretną operację zarządzania wykonywaną na zarządzanym wystąpieniu.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat migrowania bazy danych SQL Server na platformę Azure, zobacz [Migrowanie do Azure SQL Database](../database/migrate-to-database-from-sql-server.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](../database/features-comparison.md).