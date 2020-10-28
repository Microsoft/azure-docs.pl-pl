---
title: Tworzenie & zarządzanie serwerami i pojedynczymi bazami danych
description: Dowiedz się więcej na temat tworzenia serwerów i pojedynczych baz danych w programie Azure SQL Database przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka Transact-SQL (T-SQL) i interfejsu REST API.
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
ms.openlocfilehash: 2c487b5bc5c8d5fa01388b2942a70defa0001253
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791532"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Tworzenie serwerów i pojedynczych baz danych w programie Azure SQL Database oraz zarządzanie nimi

Serwery i pojedyncze bazy danych można tworzyć i zarządzać nimi w Azure SQL Database przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure i języka Transact-SQL.

## <a name="the-azure-portal"></a>Witryna Azure Portal

Można utworzyć grupę zasobów dla Azure SQL Database przed chwilą lub podczas tworzenia serwera.

### <a name="create-a-server"></a>Tworzenie serwera

Aby utworzyć serwer przy użyciu [Azure Portal](https://portal.azure.com), Utwórz nowy zasób [serwera](logical-servers.md) z witryny Azure Marketplace. Alternatywnie można utworzyć serwer podczas wdrażania Azure SQL Database.

  ![Utwórz serwer](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Tworzenie pustej lub przykładowej bazy danych

Aby utworzyć pojedynczy Azure SQL Database przy użyciu [Azure Portal](https://portal.azure.com), wybierz zasób Azure SQL Database w witrynie Azure Marketplace. Można utworzyć grupę zasobów i serwer przed czasem lub podczas tworzenia pojedynczej bazy danych. Możesz utworzyć pustą bazę danych lub utworzyć przykładową bazę danych na podstawie firmy Adventure Works LT.

  ![tworzenie bazy danych 1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje na temat wybierania warstwy cenowej dla bazy danych, zobacz [model zakupów oparty na](service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Zarządzanie istniejącym serwerem

Aby zarządzać istniejącym serwerem, przejdź do serwera przy użyciu szeregu metod — na przykład z określonej strony bazy danych, strony **serwery SQL** lub strony **wszystkie zasoby** .

Aby zarządzać istniejącą bazą danych, przejdź do strony **bazy danych SQL** , a następnie wybierz bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć ustawianie zapory na poziomie serwera dla bazy danych na stronie **Przegląd** dla bazy danych.

   ![reguła zapory serwera](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności dla bazy danych, zobacz [model zakupów oparty na](service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md).
> [!TIP]
> Aby uzyskać Azure Portal przewodniku Szybki Start, zobacz [Tworzenie bazy danych w SQL Database w Azure Portal](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby utworzyć serwery, pojedyncze i w puli baz danych oraz zapory na poziomie serwera z Azure PowerShell, należy użyć następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps).

> [!TIP]
> Przykładowe skrypty programu PowerShell można znaleźć [w temacie Używanie programu PowerShell do tworzenia bazy danych w SQL Database i konfigurowania reguły zapory na poziomie serwera](scripts/create-and-configure-database-powershell.md) oraz [monitorowania i skalowania bazy danych w SQL Database przy użyciu programu PowerShell](scripts/monitor-and-scale-database-powershell.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy bazę danych |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera co najmniej jedną bazę danych|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej|
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
| New-AzSqlServerVirtualNetworkRule | Tworzy [*regułę sieci wirtualnej*](vnet-service-endpoint-rule-overview.md)na podstawie podsieci, która jest punktem końcowym usługi Virtual Network. |

## <a name="the-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby utworzyć serwery, bazy danych i zapory za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure)i zarządzać nimi, użyj następujących poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) . Używaj usługi [Cloud Shell](../../cloud-shell/overview.md), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Aby utworzyć pule elastyczne i zarządzać nimi, zobacz [Pule elastyczne](elastic-pool-overview.md).

> [!TIP]
> Aby uzyskać szybki start interfejs wiersza polecenia platformy Azure, zobacz [Tworzenie jednego Azure SQL Database przy użyciu interfejsu wiersza polecenia platformy Azure](az-cli-script-samples-content-guide.md). Przykładowe skrypty interfejsu wiersza polecenia platformy Azure znajdują [się w temacie Używanie interfejsu wiersza polecenia do tworzenia bazy danych w Azure SQL Database i konfigurowania reguły zapory SQL Database](scripts/create-and-configure-database-cli.md) i [używania interfejsu wiersza polecenia do monitorowania i skalowania bazy danych w Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Tworzy bazę danych|
|[Lista AZ SQL DB](/cli/azure/sql/db#az-sql-db-list)|Wyświetla listę wszystkich baz danych i magazynów danych na serwerze lub wszystkich baz danych w puli elastycznej|
|[AZ SQL DB list-Edition](/cli/azure/sql/db#az-sql-db-list-editions)|Wyświetla dostępne cele usługi i limity magazynu|
|[AZ SQL DB list-Usages](/cli/azure/sql/db#az-sql-db-list-usages)|Zwraca użycie bazy danych|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Pobiera bazę danych lub magazyn danych|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aktualizuje bazę danych|
|[Usuń AZ SQL DB](/cli/azure/sql/db#az-sql-db-delete)|Usuwa bazę danych|
|[az group create](/cli/azure/group#az-group-create)|Tworzy grupę zasobów|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Tworzy serwer|
|[AZ SQL Server list](/cli/azure/sql/server#az-sql-server-list)|Wyświetla listę serwerów|
|[AZ SQL Server list-Usages](/cli/azure/sql/server#az-sql-server-list-usages)|Zwraca użycie serwera|
|[AZ SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Pobiera serwer|
|[AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Aktualizuje serwer|
|[AZ SQL Server Delete](/cli/azure/sql/server#az-sql-server-delete)|Usuwa serwer|
|[AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Tworzy regułę zapory serwera|
|[AZ SQL Server firewall-Rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Wyświetla listę reguł zapory na serwerze|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Pokazuje szczegóły reguły zapory|
|[AZ SQL Server firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aktualizuje regułę zapory|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Usuwa regułę zapory|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Aby utworzyć serwery, bazy danych i zapory w języku Transact-SQL i zarządzać nimi, użyj następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może połączyć się z serwerem w SQL Database i przekazać polecenia języka Transact-SQL. Aby zarządzać pulami elastycznymi, zobacz [Pule elastyczne](elastic-pool-overview.md).

> [!TIP]
> Aby uzyskać szybki Start przy użyciu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Azure SQL Database: używanie SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych](connect-query-ssms.md). Aby uzyskać szybki Start przy użyciu Visual Studio Code w macOS, Linux lub Windows, zobacz [Azure SQL Database: używanie Visual Studio Code do nawiązywania połączenia i wykonywania zapytań dotyczących danych](connect-query-vscode.md).
> [!IMPORTANT]
> Nie można utworzyć lub usunąć serwera za pomocą języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[UTWÓRZ BAZĘ DANYCH](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Tworzy nową pojedynczą bazę danych. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Modyfikuje bazę danych lub pulę elastyczną. |
|[USUŃ BAZĘ DANYCH](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usługi), cel usługi (warstwę cenową) i nazwę puli elastycznej (jeśli istnieje) dla Azure SQL Database lub puli SQL usługi Azure Synapse Analytics. Jeśli użytkownik jest zalogowany do bazy danych Master na serwerze w SQL Database, zwraca informacje o wszystkich bazach danych. W przypadku usługi Azure Synapse Analytics musisz mieć połączenie z bazą danych Master.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca użycie procesora CPU, operacji we/wy i pamięci dla bazy danych w Azure SQL Database. Jeden wiersz istnieje przez co 15 sekund, nawet jeśli nie ma żadnych działań w bazie danych.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane użycia procesora CPU i magazynu dla bazy danych w Azure SQL Database. Dane są zbierane i agregowane w ciągu pięciu minut.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera dane statystyczne dotyczące zdarzeń łączności SQL Database, które zawierają omówienie sukcesów i niepowodzeń połączeń z bazą danych. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślne połączenia Azure SQL Database, błędy połączeń i zakleszczenie. Te informacje służą do śledzenia i rozwiązywania problemów z działaniem bazy danych za pomocą SQL Database.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera. Ta procedura składowana jest dostępna tylko w bazie danych Master do nazwy logowania podmiotu zabezpieczeń na poziomie serwera. Regułę zapory na poziomie serwera można utworzyć tylko przy użyciu języka Transact-SQL po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnieniami na poziomie platformy Azure.|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie serwera skojarzonych z bazą danych w Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa z serwera ustawienia zapory na poziomie serwera. Ta procedura składowana jest dostępna tylko w bazie danych Master do nazwy logowania podmiotu zabezpieczeń na poziomie serwera.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla bazy danych w Azure SQL Database. Reguły zapory bazy danych można skonfigurować dla bazy danych Master oraz dla baz danych użytkowników na SQL Database. Reguły zapory bazy danych są przydatne w przypadku korzystania z użytkowników zawartej bazy danych. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie bazy danych skojarzonych z bazą danych w Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych z bazy danych. |

## <a name="rest-api"></a>Interfejs API REST

Do tworzenia serwerów, baz danych i zapór oraz zarządzania nimi należy używać tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — Utwórz lub zaktualizuj](/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowy serwer.|
|[Serwery — usuwanie](/rest/api/sql/servers/delete)|Usuwa program SQL Server.|
|[Serwery — Pobierz](/rest/api/sql/servers/get)|Pobiera serwer.|
|[Serwery — lista](/rest/api/sql/servers/list)|Zwraca listę serwerów w ramach subskrypcji.|
|[Serwery — lista według grupy zasobów](/rest/api/sql/servers/listbyresourcegroup)|Zwraca listę serwerów w grupie zasobów.|
|[Serwery — aktualizacja](/rest/api/sql/servers/update)|Aktualizuje istniejący serwer.|
|[Bazy danych — Utwórz lub zaktualizuj](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — usuwanie](/rest/api/sql/databases/delete)|Usuwa bazę danych.|
|[Bazy danych — Pobierz](/rest/api/sql/databases/get)|Pobiera bazę danych.|
|[Bazy danych — lista według elastycznej puli](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według serwera](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|
|[Reguły zapory — Utwórz lub zaktualizuj](/rest/api/sql/firewallrules/createorupdate)|Tworzy lub aktualizuje regułę zapory.|
|[Reguły zapory — usuwanie](/rest/api/sql/firewallrules/delete)|Usuwa regułę zapory.|
|[Reguły zapory — Pobierz](/rest/api/sql/firewallrules/get)|Pobiera regułę zapory.|
|[Reguły zapory — lista według serwera](/rest/api/sql/firewallrules/listbyserver)|Zwraca listę reguł zapory.|

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat migrowania bazy danych SQL Server na platformę Azure, zobacz [Migrowanie do Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](features-comparison.md).
 
