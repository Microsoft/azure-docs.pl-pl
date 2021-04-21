---
title: Co to jest serwer w Azure SQL Database i Azure Synapse Analytics?
titleSuffix: ''
description: Dowiedz się więcej o serwerach logicznych SQL używanych Azure SQL Database i Azure Synapse Analytics oraz o tym, jak nimi zarządzać.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c76d3ae78bf2b9b4a71d9520f7f1c6c2c322483b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784519"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Co to jest serwer logiczny SQL w Azure SQL Database i Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

W Azure SQL Database i Azure Synapse Analytics serwer jest konstrukcją logiczną, która działa jako centralny punkt administracyjny dla kolekcji baz danych. Na poziomie serwera można administrować nazwami [logowania,](logins-create-manage.md)regułami [zapory,](firewall-configure.md)regułami [inspekcji,](../../azure-sql/database/auditing-overview.md)zasadami wykrywania zagrożeń i [grupami automatycznego trybu failover.](auto-failover-group-overview.md) [](threat-detection-configure.md) Serwer może być w innym regionie niż jego grupa zasobów. Serwer musi istnieć, aby można było utworzyć bazę danych w Azure SQL Database lub bazę danych magazynu danych w Azure Synapse Analytics. Wszystkie bazy danych zarządzane przez jeden serwer są tworzone w tym samym regionie co serwer.

Ten serwer różni się od SQL Server, które być może znasz w środowisku lokalnym. W szczególności nie ma żadnych gwarancji dotyczących lokalizacji baz danych lub bazy danych magazynu danych w odniesieniu do serwera, który nimi zarządza. Ponadto ani Azure SQL Database, ani Azure Synapse żadnych funkcji ani dostępu na poziomie wystąpienia. Z kolei bazy danych wystąpień w wystąpieniu zarządzanym są fizycznie kolokujące — w taki sam sposób, jak znasz SQL Server w środowisku lokalnym lub środowisku maszyny wirtualnej.

Podczas tworzenia serwera należy podać konto logowania i hasło serwera z uprawnieniami administracyjnymi do bazy danych master na tym serwerze i wszystkich baz danych utworzonych na tym serwerze. To początkowe konto jest kontem logowania SQL. Azure SQL Database i Azure Synapse Analytics obsługują uwierzytelnianie SQL i Azure Active Directory uwierzytelniania na celu uwierzytelnianie. Aby uzyskać informacje na temat identyfikatorów logowania i uwierzytelniania, zobacz Zarządzanie bazami danych i [nazwami logowania w Azure SQL Database](logins-create-manage.md). Uwierzytelnianie systemu Windows nie jest obsługiwane.

Serwer w SQL Database i Azure Synapse:

- Jest tworzony w ramach subskrypcji platformy Azure, ale można go przenieść wraz z zawartymi w nim zasobami do innej subskrypcji
- Jest zasobem nadrzędnym dla baz danych, pul elastycznych i magazynów danych
- Zapewnia przestrzeń nazw dla baz danych, pul elastycznych i bazy danych magazynu danych
- Jest kontenerem logicznym z silną semantyką okresu istnienia — usuwa serwer i usuwa jego bazy danych, elastyczne pule i pule SQK
- Uczestniczy w kontroli dostępu na podstawie ról [(RBAC)](../../role-based-access-control/overview.md) platformy Azure — bazy danych, elastyczne pule i baza danych magazynu danych na serwerze dziedziczą prawa dostępu z serwera
- Jest elementem o wysokiej kolejności tożsamości baz danych, pul elastycznych i bazy danych magazynu danych na potrzeby zarządzania zasobami platformy Azure (zobacz schemat adresów URL dla baz danych i pul)
- Rozmieszcza zasoby w regionie
- Udostępnia punkt końcowy połączenia dla dostępu do baz danych (`<serverName>`.database.windows.net)
- Zapewnia dostęp do metadanych dotyczących zawartych zasobów przy użyciu dynamicznych widoków zarządzania, łącząc się z główną bazą danych
- Zapewnia zakres zasad zarządzania, które mają zastosowanie do jego baz danych — identyfikatory logowania, zapora, inspekcja, wykrywanie zagrożeń i takie
- Jest ograniczony przez limit przydziału w ramach subskrypcji nadrzędnej (domyślnie sześć serwerów na subskrypcję — [zobacz limity subskrypcji tutaj](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- Zapewnia zakres przydziału bazy danych i przydziału jednostek DTU lub rdzeni wirtualnych dla zasobów, które zawiera (na przykład 45 000 jednostek DTU)
- Czy zakres obsługi wersji dla funkcji jest włączony dla zawartych zasobów
- Logowania główne na poziomie serwera mogą zarządzać wszystkimi bazami danych na serwerze
- Może zawierać identyfikatory logowania podobne do tych w wystąpieniach usługi SQL Server w środowisku lokalnym, którym udzielono dostępu do co najmniej jednej bazy danych na serwerze i którym można przyznać ograniczone prawa administracyjne. Aby uzyskać więcej informacji, zobacz temat [Logowania](logins-create-manage.md).
- Domyślne sortowanie dla wszystkich baz danych utworzonych na serwerze to , gdzie to angielski (Stany Zjednoczone), to strona kodowa `SQL_LATIN1_GENERAL_CP1_CI_AS` `LATIN1_GENERAL` `CP1` 1252, nie jest uwzględniania wielkość liter i jest z uwzględnianiem `CI` `AS` akcentów.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu Azure Portal

Grupę zasobów dla serwera można utworzyć wcześniej lub podczas tworzenia samego serwera. Istnieje wiele metod uzyskiwania nowego formularza serwera SQL, tworząc nowy serwer SQL lub tworząc nową bazę danych.

### <a name="create-a-blank-server"></a>Tworzenie pustego serwera

Aby utworzyć serwer (bez bazy danych, elastycznej puli ani bazy danych magazynu danych) przy użyciu narzędzia [Azure Portal,](https://portal.azure.com)przejdź do pustego formularza serwera SQL (serwera logicznego SQL).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Tworzenie pustej lub przykładowej bazy danych w Azure SQL Database

Aby utworzyć bazę danych w SQL Database przy użyciu Azure Portal [,](https://portal.azure.com)przejdź do pustego SQL Database formularza i podaj żądane informacje. Grupę zasobów i serwer można utworzyć wcześniej lub podczas tworzenia samej bazy danych. Możesz utworzyć pustą bazę danych lub przykładową bazę danych w oparciu o bazę danych Adventure Works LT.

  ![tworzenie bazy danych 1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje na temat wybierania warstwy cenowej dla bazy danych, zobacz Model zakupów oparty na jednostkach [DTU](service-tiers-dtu.md) i Model zakupów oparty na [rdzeniu wirtualnych.](service-tiers-vcore.md)

Aby utworzyć wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Zarządzanie istniejącym serwerem

Aby zarządzać istniejącym serwerem, przejdź do serwera przy użyciu wielu metod, takich jak ze strony określonej bazy danych, strony serwerów **SQL** lub **strony Wszystkie** zasoby.

Aby zarządzać istniejącą bazą danych, przejdź do **strony Bazy** danych SQL i kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu przedstawia, jak rozpocząć ustawianie zapory na poziomie serwera dla bazy danych na **stronie Przegląd** dla bazy danych.

   ![reguła zapory serwera](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności dla bazy danych, zobacz Model zakupów oparty na jednostkach [DTU](service-tiers-dtu.md) i Model zakupów oparty na [rdzeniu wirtualnych.](service-tiers-vcore.md)
> [!TIP]
> Aby uzyskać Azure Portal szybki start, zobacz Tworzenie bazy danych w SQL Database [w Azure Portal](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager PowerShell jest nadal obsługiwany, ale cały przyszły rozwój jest dla modułu Az.Sql. Aby uzyskać informacje na temat tych poleceń cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty poleceń w module Az i modułach AzureRm są zasadniczo identyczne.

Aby tworzyć serwery, bazy danych i zapory oraz zarządzać nimi za pomocą Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps). Aby uzyskać informacje na temat tworzenia pul elastycznych i zarządzania nimi, zobacz [Pule elastyczne](elastic-pool-overview.md).

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

> [!TIP]
> Aby uzyskać przewodnik Szybki start programu PowerShell, zobacz Create a database in Azure SQL Database using PowerShell (Tworzenie bazy danych [w programie Azure SQL Database użyciu programu PowerShell).](single-database-create-quickstart.md) Aby uzyskać przykładowe skrypty programu [PowerShell,](scripts/create-and-configure-database-powershell.md) zobacz Tworzenie bazy danych w programie Azure SQL Database przy użyciu programu PowerShell i konfigurowanie reguły zapory oraz Monitorowanie i skalowanie bazy danych w programie [Azure SQL Database pomocą programu PowerShell.](scripts/monitor-and-scale-database-powershell.md)
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu interfejsu wiersza polecenia platformy Azure

Aby tworzyć serwery, bazy danych i zapory oraz zarządzać nimi za pomocą interfejsu wiersza polecenia platformy [Azure,](/cli/azure)użyj następujących poleceń interfejsu wiersza [SQL Database](/cli/azure/sql/db) Azure. Używaj usługi [Cloud Shell](../../cloud-shell/overview.md), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Aby uzyskać informacje na temat tworzenia pul elastycznych i zarządzania nimi, zobacz [Pule elastyczne](elastic-pool-overview.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Tworzy bazę danych|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Wyświetla listę wszystkich baz danych zarządzanych przez serwer lub wszystkich baz danych w elastycznej puli|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Wyświetla listę dostępnych celów usługi i limitów magazynu|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Zwraca użycie bazy danych|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Pobiera bazę danych
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

> [!TIP]
> Aby uzyskać przewodnik Szybki start dla interfejsu wiersza polecenia platformy Azure, zobacz Tworzenie bazy danych w [usłudze Azure SQL Database użyciu interfejsu wiersza polecenia platformy Azure.](az-cli-script-samples-content-guide.md) Przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć w tematach Use the CLI to create a database in Azure SQL Database and configure a firewall rule (Używanie interfejsu wiersza polecenia do monitorowania i skalowania bazy danych w programie [Azure SQL Database)](scripts/create-and-configure-database-cli.md) i Use the CLI to monitor and scale a database in Azure SQL Database (Używanie interfejsu wiersza polecenia do monitorowania i skalowania bazy danych w [programie Azure SQL Database).](scripts/monitor-and-scale-database-cli.md)
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu języka Transact-SQL

Aby tworzyć serwery, bazy danych i zapory oraz zarządzać nimi za pomocą języka Transact-SQL, użyj następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu poleceń Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może nawiązać połączenie z serwerem i przekazać polecenia języka Transact-SQL. Aby zarządzać elastycznymi pulami, zobacz [Pule elastyczne](elastic-pool-overview.md).

> [!IMPORTANT]
> Nie można utworzyć ani usunąć serwera przy użyciu języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Tworzy nową bazę danych w Azure SQL Database. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych master.|
|[CREATE DATABASE (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Tworzy nową bazę danych magazynu danych w Azure Synapse. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych master.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Modyfikuje bazę danych lub pulę elastyczną. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Modyfikuje bazę danych magazynu danych w Azure Synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usługi), cel usługi (warstwę cenową) i nazwę puli elastycznej, jeśli jest, dla bazy danych. Jeśli użytkownik jest zalogowany do głównej bazy danych serwera, zwraca informacje o wszystkich bazach danych. Aby Azure Synapse, musisz mieć połączenie z bazą danych master.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca użycie procesora CPU, we/wy i pamięci dla bazy danych w Azure SQL Database. Jeden wiersz istnieje co 15 sekund, nawet jeśli w bazie danych nie ma żadnych działań.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane użycia procesora CPU i magazynu dla bazy danych w Azure SQL Database. Dane są zbierane i agregowane w odstępach pięciominutowych.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera statystyki zdarzeń łączności bazy danych dla Azure SQL Database, udostępniając przegląd powodzenia i niepowodzeń połączeń z bazą danych. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślne Azure SQL Database z bazą danych, błędy połączeń i zakleszczenia dla Azure SQL Database. Tych informacji można użyć do śledzenia aktywności bazy danych lub rozwiązywania problemów z jej działaniem.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera. Ta procedura składowana jest dostępna tylko w bazie danych master dla głównego identyfikatora logowania na poziomie serwera. Regułę zapory na poziomie serwera można utworzyć przy użyciu języka Transact-SQL dopiero po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnieniami na poziomie platformy Azure|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie serwera skojarzonych z serwerem.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa ustawienia zapory na poziomie serwera z serwera. Ta procedura składowana jest dostępna tylko w bazie danych master dla głównego identyfikatora logowania na poziomie serwera.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla bazy danych w Azure SQL Database. Reguły zapory bazy danych można skonfigurować dla bazy danych master oraz dla baz danych użytkowników w SQL Database. Reguły zapory bazy danych są przydatne w przypadku korzystania z użytkowników zawartej bazy danych. Reguły zapory bazy danych nie są obsługiwane w Azure Synapse.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie bazy danych dla bazy danych w Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych dla twojej bazy danych w Azure SQL Database. |

> [!TIP]
> Aby uzyskać przewodnik Szybki start z SQL Server Management Studio w systemie Microsoft Windows, zobacz Azure SQL Database: Używanie SQL Server Management Studio do nawiązywania połączeń z danymi i wykonywania [dotyczących ich zapytań.](connect-query-ssms.md) Aby uzyskać przewodnik Szybki start Visual Studio Code w systemie macOS, Linux lub Windows, zobacz Azure SQL Database: Używanie Visual Studio Code do nawiązywania połączeń z danymi i wykonywania dotyczących [ich zapytań.](connect-query-vscode.md)

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu interfejsu API REST

Aby tworzyć serwery, bazy danych i zapory oraz zarządzać nimi, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — tworzenie lub aktualizowanie](/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowy serwer.|
|[Serwery — usuwanie](/rest/api/sql/servers/delete)|Usuwa serwer.|
|[Serwery — pobierz](/rest/api/sql/servers/get)|Pobiera serwer.|
|[Serwery — lista](/rest/api/sql/servers/list)|Zwraca listę serwerów.|
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

- Aby dowiedzieć się więcej o migrowaniu bazy danych SQL Server do Azure SQL Database, zobacz [Migrowanie](migrate-to-database-from-sql-server.md)do Azure SQL Database .
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](features-comparison.md).