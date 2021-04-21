---
title: Tworzenie & zarządzanie serwerami i pojedynczymi bazami danych
description: Dowiedz się więcej na temat tworzenia serwerów i pojedynczych baz danych oraz zarządzania nimi w usłudze Azure SQL Database przy użyciu interfejsu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka Transact-SQL (T-SQL) i interfejsu API REST.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 4aaabdb3d21c41b973b21e6e52442be132796196
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781599"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Tworzenie serwerów i pojedynczych baz danych oraz zarządzanie nimi w Azure SQL Database

Serwery i pojedyncze bazy danych można tworzyć i zarządzać nimi w usłudze Azure SQL Database przy użyciu programu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, interfejsu API REST i języka Transact-SQL.

## <a name="the-azure-portal"></a>Witryna Azure Portal

Możesz utworzyć grupę zasobów dla Azure SQL Database wcześniej lub podczas tworzenia samego serwera.

### <a name="create-a-server"></a>Tworzenie serwera

Aby utworzyć serwer przy użyciu [Azure Portal](https://portal.azure.com), utwórz nowy [zasób serwera](logical-servers.md) z Azure Marketplace. Możesz też utworzyć serwer podczas wdrażania serwera Azure SQL Database.

  ![tworzenie serwera](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Tworzenie pustej lub przykładowej bazy danych

Aby utworzyć pojedynczą Azure SQL Database przy użyciu [Azure Portal](https://portal.azure.com), wybierz Azure SQL Database w Azure Marketplace. Grupę zasobów i serwer można utworzyć wcześniej lub podczas tworzenia pojedynczej bazy danych. Możesz utworzyć pustą bazę danych lub przykładową bazę danych w oparciu o bazę danych Adventure Works LT.

  ![tworzenie bazy danych 1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje na temat wybierania warstwy cenowej dla bazy danych, zobacz Model zakupów oparty na jednostkach [DTU](service-tiers-dtu.md) i Model zakupów oparty na [rdzeniu wirtualnych.](service-tiers-vcore.md)

## <a name="manage-an-existing-server"></a>Zarządzanie istniejącym serwerem

Aby zarządzać istniejącym serwerem, przejdź do serwera przy użyciu wielu metod, takich jak z określonej strony bazy danych, strony serwerów **SQL** lub **strony Wszystkie** zasoby.

Aby zarządzać istniejącą bazą danych, przejdź do **strony Bazy** danych SQL i wybierz bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu przedstawia, jak rozpocząć ustawianie zapory na poziomie serwera dla bazy danych na **stronie Przegląd** dla bazy danych.

   ![reguła zapory serwera](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności dla bazy danych, zobacz Model zakupów oparty na jednostkach [DTU](service-tiers-dtu.md) i Model zakupów oparty na [rdzeniu wirtualnych.](service-tiers-vcore.md)
> [!TIP]
> Aby uzyskać Azure Portal szybki start, zobacz Tworzenie bazy danych w SQL Database [w Azure Portal](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł powershell Azure Resource Manager jest nadal obsługiwany przez Azure SQL Database, ale cały przyszły rozwój jest dla modułu Az.Sql. Aby uzyskać informacje na temat tych poleceń cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty poleceń w module Az i modułach AzureRm są w znacznym stopniu identyczne.

Aby tworzyć serwery, pojedyncze bazy danych i bazy danych w puli oraz zapory na poziomie serwera oraz zarządzać nimi za pomocą Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps).

> [!TIP]
> Aby uzyskać przykładowe skrypty programu [PowerShell,](scripts/create-and-configure-database-powershell.md) zobacz Tworzenie bazy danych w programie SQL Database przy użyciu programu PowerShell i konfigurowanie reguły zapory na poziomie serwera oraz Monitorowanie i skalowanie bazy danych w programie [SQL Database programu PowerShell.](scripts/monitor-and-scale-database-powershell.md)

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy bazę danych |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera co najmniej jedną bazę danych|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do elastycznej puli|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Usuwa bazę danych|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Tworzy grupę zasobów|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Tworzy serwer|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Zwraca informacje o serwerach|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Modyfikuje właściwości serwera|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Usuwa serwer|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Tworzy regułę zapory na poziomie serwera |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Pobiera reguły zapory dla serwera|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modyfikuje regułę zapory na serwerze|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Usuwa regułę zapory z serwera.|
| New-AzSqlServerVirtualNetworkRule | Tworzy [*regułę sieci wirtualnej*](vnet-service-endpoint-rule-overview.md)na podstawie podsieci, która jest punktem końcowym Virtual Network usługi. |

## <a name="the-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć serwery, bazy danych i zapory oraz zarządzać nimi za pomocą interfejsu wiersza polecenia platformy [Azure,](/cli/azure)użyj następujących poleceń interfejsu wiersza polecenia platformy [Azure.](/cli/azure/sql/db) Używaj usługi [Cloud Shell](../../cloud-shell/overview.md), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Aby uzyskać informacje na temat tworzenia pul elastycznych i zarządzania nimi, zobacz [Pule elastyczne](elastic-pool-overview.md).

> [!TIP]
> Aby uzyskać przewodnik Szybki start dla interfejsu wiersza polecenia platformy Azure, zobacz [Create a single Azure SQL Database using the Azure CLI (Tworzenie pojedynczego interfejsu wiersza polecenia przy użyciu interfejsu wiersza polecenia platformy Azure).](az-cli-script-samples-content-guide.md) Przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć w tematach Use CLI to create a database in Azure SQL Database and configure a SQL Database firewall rule (Używanie interfejsu wiersza polecenia do monitorowania i skalowania bazy danych w programie [Azure SQL Database)](scripts/create-and-configure-database-cli.md) i Use CLI to monitor and scale a database in Azure SQL Database (Używanie interfejsu wiersza polecenia do monitorowania i skalowania bazy danych w programie [Azure SQL Database).](scripts/monitor-and-scale-database-cli.md)
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Tworzy bazę danych|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Wyświetla listę wszystkich baz danych i magazynów danych na serwerze lub wszystkich baz danych w elastycznej puli|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Wyświetla listę dostępnych celów usługi i limitów magazynu|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Zwraca użycie bazy danych|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Pobiera bazę danych lub magazyn danych|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Aktualizuje bazę danych|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Usuwa bazę danych|
|[az group create](/cli/azure/group#az_group_create)|Tworzy grupę zasobów|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Tworzy serwer|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|Wyświetla listę serwerów|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|Zwraca użycie serwera|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|Pobiera serwer|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|Aktualizuje serwer|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Usuwa serwer|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Tworzy regułę zapory serwera|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Wyświetla listę reguł zapory na serwerze|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Przedstawia szczegóły reguły zapory|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aktualizuje regułę zapory|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Usuwa regułę zapory|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Aby utworzyć serwery, bazy danych i zapory oraz zarządzać nimi za pomocą języka Transact-SQL, użyj następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu poleceń Azure Portal, [SQL Server Management Studio,](/sql/ssms/use-sql-server-management-studio) [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może łączyć się z serwerem w programie SQL Database i przekazywać polecenia języka Transact-SQL. Aby zarządzać elastycznymi pulami, zobacz [Pule elastyczne](elastic-pool-overview.md).

> [!TIP]
> Aby uzyskać przewodnik Szybki start z SQL Server Management Studio w systemie Microsoft Windows, zobacz Azure SQL Database: Używanie SQL Server Management Studio do nawiązywania połączeń z danymi i wykonywania [dotyczących ich zapytań.](connect-query-ssms.md) Aby uzyskać przewodnik Szybki start Visual Studio Code w systemie macOS, Linux lub Windows, zobacz Azure SQL Database: Używanie Visual Studio Code do nawiązywania połączeń z danymi i wykonywania dotyczących [ich zapytań.](connect-query-vscode.md)
> [!IMPORTANT]
> Nie można utworzyć ani usunąć serwera przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Tworzy nową pojedynczą bazę danych. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych master.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Modyfikuje bazę danych lub pulę elastyczną. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca edycję (warstwę usługi), cel usługi (warstwa cenowa) i nazwę puli elastycznej, jeśli jest Azure SQL Database, lub dedykowaną pulę SQL w Azure Synapse Analytics. Po zalogowaniu się do bazy danych master na serwerze w u SQL Database zwraca informacje o wszystkich bazach danych. Aby Azure Synapse Analytics, musisz mieć połączenie z bazą danych master.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca użycie procesora CPU, we/wy i pamięci dla bazy danych w Azure SQL Database. Jeden wiersz istnieje co 15 sekund, nawet jeśli w bazie danych nie ma żadnych działań.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane użycia procesora CPU i magazynu dla bazy danych w Azure SQL Database. Dane są zbierane i agregowane w odstępach pięciominutowych.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera statystyki dotyczące SQL Database łączności, zapewniając przegląd powodzenia i niepowodzeń połączeń z bazą danych. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślne Azure SQL Database połączeń, błędów połączeń i zakleszczeń. Tych informacji można użyć do śledzenia aktywności bazy danych lub rozwiązywania problemów z SQL Database.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera. Ta procedura składowana jest dostępna tylko w bazie danych master dla głównego identyfikatora logowania na poziomie serwera. Regułę zapory na poziomie serwera można utworzyć przy użyciu języka Transact-SQL dopiero po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnieniami na poziomie platformy Azure|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie serwera skojarzonych z bazą danych w Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa ustawienia zapory na poziomie serwera z serwera. Ta procedura składowana jest dostępna tylko w bazie danych master dla głównego identyfikatora logowania na poziomie serwera.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla bazy danych w Azure SQL Database. Reguły zapory bazy danych można skonfigurować dla bazy danych master oraz dla baz danych użytkowników na SQL Database. Reguły zapory bazy danych są przydatne w przypadku korzystania z użytkowników zawartej bazy danych. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie bazy danych skojarzonych z bazą danych w Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych z bazy danych. |

## <a name="rest-api"></a>Interfejs API REST

Aby utworzyć serwery, bazy danych i zapory oraz zarządzać nimi, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — tworzenie lub aktualizowanie](/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowy serwer.|
|[Serwery — usuwanie](/rest/api/sql/servers/delete)|Usuwa serwer SQL.|
|[Serwery — pobierz](/rest/api/sql/servers/get)|Pobiera serwer.|
|[Serwery — lista](/rest/api/sql/servers/list)|Zwraca listę serwerów w subskrypcji.|
|[Serwery — lista według grupy zasobów](/rest/api/sql/servers/listbyresourcegroup)|Zwraca listę serwerów w grupie zasobów.|
|[Serwery — aktualizacja](/rest/api/sql/servers/update)|Aktualizuje istniejący serwer.|
|[Bazy danych — tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — usuwanie](/rest/api/sql/databases/delete)|Usuwa bazę danych.|
|[Bazy danych — pobierz](/rest/api/sql/databases/get)|Pobiera bazę danych.|
|[Bazy danych — lista według puli elastycznej](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w elastycznej puli.|
|[Bazy danych — lista według serwera](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Reguły zapory — tworzenie lub aktualizowanie](/rest/api/sql/firewallrules/createorupdate)|Tworzy lub aktualizuje regułę zapory.|
|[Reguły zapory — usuwanie](/rest/api/sql/firewallrules/delete)|Usuwa regułę zapory.|
|[Reguły zapory — pobierz](/rest/api/sql/firewallrules/get)|Pobiera regułę zapory.|
|[Reguły zapory — lista według serwera](/rest/api/sql/firewallrules/listbyserver)|Zwraca listę reguł zapory.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat migrowania bazy SQL Server na platformę Azure, zobacz [Migrowanie do Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](features-comparison.md).
