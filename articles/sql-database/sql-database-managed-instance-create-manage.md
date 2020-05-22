---
title: Dokumentacja interfejsu API zarządzania dla wystąpienia zarządzanego
description: Dowiedz się więcej na temat tworzenia i zarządzania Azure SQL Database wystąpieniami zarządzanymi.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 12f9411b62ac218e99d0006a743805c50c63b39a
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773577"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Dokumentacja zarządzanego interfejsu API dla Azure SQL Database wystąpień zarządzanych

Można tworzyć i zarządzać Azure SQL Database wystąpieniami zarządzanymi przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure i języka Transact-SQL. Ten artykuł zawiera omówienie funkcji i interfejsu API, których można użyć do tworzenia i konfigurowania wystąpienia zarządzanego.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Tworzenie wystąpienia zarządzanego

Aby zapoznać się z przewodnikiem Szybki Start pokazujący, jak utworzyć wystąpienie zarządzane Azure SQL Database, zobacz [Szybki Start: tworzenie Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Tworzenie wystąpień zarządzanych i zarządzanie nimi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby tworzyć wystąpienia zarządzane i zarządzać nimi za pomocą Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps).

> [!TIP]
> Przykładowe skrypty programu PowerShell można znaleźć w sekcji [skrypt szybkiego startu: Tworzenie wystąpienia zarządzanego Azure SQL przy użyciu biblioteki programu PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Tworzy wystąpienie zarządzane Azure SQL Database |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Zwraca informacje na temat wystąpienia zarządzanego usługi Azure SQL|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Ustawia właściwości dla Azure SQL Database wystąpienia zarządzanego|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Usuwa wystąpienie bazy danych Azure SQL Managed Database|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Tworzy bazę danych wystąpienia zarządzanego Azure SQL Database|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Zwraca informacje o bazie danych wystąpienia zarządzanego Azure SQL|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Usuwa bazę danych wystąpienia zarządzanej bazy danych Azure SQL|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Przywraca bazę danych wystąpienia zarządzanej bazy danych Azure SQL|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Interfejs wiersza polecenia platformy Azure: Tworzenie wystąpień zarządzanych i zarządzanie nimi

Aby tworzyć wystąpienia zarządzane i zarządzać nimi za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure), użyj następujących poleceń [wystąpienia zarządzanego SQL interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/mi) . Używaj usługi [Cloud Shell](/azure/cloud-shell/overview), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure zawiera temat [Praca z wystąpieniem zarządzanym SQL przy użyciu interfejsu wiersza polecenia platformy Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)

| Polecenie cmdlet | Opis |
| --- | --- |
|[AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Tworzy wystąpienie zarządzane|
|[AZ SQL mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Wyświetla dostępne wystąpienia zarządzane|
|[AZ SQL mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Pobierz szczegóły wystąpienia zarządzanego|
|[AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Aktualizuje wystąpienie zarządzane|
|[AZ SQL mi Delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Usuwa wystąpienie zarządzane|
|[AZ SQL MidB Create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Tworzy zarządzaną bazę danych|
|[AZ SQL MidB list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Wyświetla dostępne zarządzane bazy danych|
|[AZ SQL MidB Restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Przywracanie zarządzanej bazy danych|
|[AZ SQL MidB Delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Usuwa zarządzaną bazę danych|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Tworzenie i Zarządzanie wystąpieniami baz danych

Aby utworzyć bazę danych wystąpienia i zarządzać nią po utworzeniu wystąpienia zarządzanego, użyj następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu Azure Portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może nawiązać połączenie z serwerem Azure SQL Database i przekazać polecenia języka Transact-SQL.

> [!TIP]
> Przewodniki Szybki Start przedstawiające Konfigurowanie i nawiązywanie połączenia z wystąpieniem zarządzanym przy użyciu SQL Server Management Studio w systemie Microsoft Windows można znaleźć w artykule [Szybki Start: Konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym Azure SQL Database](sql-database-managed-instance-configure-vm.md) i [Szybki Start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem Azure SQL Database zarządzanym lokalnie](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Nie można utworzyć lub usunąć wystąpienia zarządzanego przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[UTWÓRZ BAZĘ DANYCH](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Tworzy nową bazę danych wystąpienia zarządzanego. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modyfikuje bazę danych wystąpienia zarządzanego Azure SQL.|

## <a name="rest-api-create-and-manage-managed-instances"></a>Interfejs API REST: Tworzenie wystąpień zarządzanych i zarządzanie nimi

Aby tworzyć wystąpienia zarządzane i zarządzać nimi, Użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Wystąpienia zarządzane — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Tworzy lub aktualizuje wystąpienie zarządzane.|
|[Wystąpienia zarządzane — usuwanie](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Usuwa wystąpienie zarządzane.|
|[Wystąpienia zarządzane — Pobierz](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Pobiera wystąpienie zarządzane.|
|[Wystąpienia zarządzane — lista](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Zwraca listę wystąpień zarządzanych w ramach subskrypcji.|
|[Wystąpienia zarządzane — lista według grupy zasobów](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Zwraca listę wystąpień zarządzanych w grupie zasobów.|
|[Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Aktualizuje wystąpienie zarządzane.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat migrowania bazy danych SQL Server na platformę Azure, zobacz [Migrowanie do Azure SQL Database](sql-database-single-database-migrate.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](sql-database-features.md).
