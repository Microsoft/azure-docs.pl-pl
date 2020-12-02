---
title: Co to jest serwer w Azure SQL Database i usługa Azure Synapse Analytics?
titleSuffix: ''
description: Dowiedz się więcej na temat logicznych serwerów SQL używanych przez Azure SQL Database i usługę Azure Synapse Analytics oraz jak zarządzać nimi.
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
ms.openlocfilehash: e0736e58983d4c28b8f2f4a20241cd36ac84fdfa
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445393"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Co to jest logiczny serwer SQL w Azure SQL Database i Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

W Azure SQL Database i Azure Synapse Analytics serwer jest konstrukcyjną logiczną, która działa jako centralny punkt administracyjny dla kolekcji baz danych. Na poziomie serwera można administrować nazwami [logowania](logins-create-manage.md), [regułami zapory](firewall-configure.md), [regułami inspekcji](../../azure-sql/database/auditing-overview.md), [zasadami wykrywania zagrożeń](threat-detection-configure.md)i [grupami automatycznego trybu failover](auto-failover-group-overview.md). Serwer może znajdować się w innym regionie niż jego grupa zasobów. Serwer musi istnieć, aby można było utworzyć bazę danych w Azure SQL Database lub bazie danych magazynu danych w usłudze Azure Synapse Analytics. Wszystkie bazy danych zarządzane przez pojedynczy serwer są tworzone w tym samym regionie, w którym znajduje się serwer.

Ten serwer różni się od wystąpienia SQL Server, które może być znane w lokalnym świecie. W związku z tym nie ma gwarancji dotyczących lokalizacji baz danych lub bazy danych magazynu danych w odniesieniu do serwera, który zarządza nimi. Ponadto żadna Azure SQL Database ani usługa Azure Synapse nie uwidaczniają żadnego dostępu lub funkcji na poziomie wystąpienia. W przeciwieństwie do bazy danych wystąpienia w zarządzanym wystąpieniu są fizycznie wspólnie zlokalizowane w taki sam sposób, jak w przypadku SQL Server w środowisku lokalnym lub na świecie maszyn wirtualnych.

Podczas tworzenia serwera należy podać konto logowania do serwera i hasło z uprawnieniami administracyjnymi do bazy danych Master na tym serwerze i wszystkich baz danych utworzonych na tym serwerze. To początkowe konto jest kontem logowania SQL. Azure SQL Database i usługa Azure Synapse Analytics obsługuje uwierzytelnianie SQL i Azure Active Directory uwierzytelniania na potrzeby uwierzytelniania. Informacje o logowaniach i uwierzytelnianiu znajdują się [w temacie Zarządzanie bazami danych i nazwami logowania w Azure SQL Database](logins-create-manage.md). Uwierzytelnianie systemu Windows nie jest obsługiwane.

Serwer w SQL Database i Azure Synapse:

- Jest tworzony w ramach subskrypcji platformy Azure, ale można go przenieść wraz z zawartymi w nim zasobami do innej subskrypcji
- Jest zasobem nadrzędnym dla baz danych, pul elastycznych i magazynów danych
- Zapewnia przestrzeń nazw dla baz danych, pul elastycznych i bazy danych magazynu danych
- Jest kontenerem logicznym ze silną semantyką okresu istnienia — usunięcie serwera i usunięcie jego baz danych, pul elastycznych i pul SQK
- Uczestnictwo w [kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure](../../role-based-access-control/overview.md) — bazy danych, pule elastyczne i baza danych magazynu danych na serwerze dziedziczą prawa dostępu z serwera
- To wysoce uporządkowany element tożsamości baz danych, pul elastycznych i bazy danych magazynu danych na potrzeby zarządzania zasobami platformy Azure (patrz schemat adresu URL dla baz danych i pul)
- Rozmieszcza zasoby w regionie
- Udostępnia punkt końcowy połączenia dla dostępu do baz danych (`<serverName>`.database.windows.net)
- Zapewnia dostęp do metadanych dotyczących zawartych zasobów przy użyciu dynamicznych widoków zarządzania, łącząc się z główną bazą danych
- Zapewnia zakres zasad zarządzania, które mają zastosowanie do swoich baz danych — logowania, zapory, inspekcji, wykrywania zagrożeń i takich
- Jest ograniczony przez przydział w ramach subskrypcji nadrzędnej (domyślnie sześć serwerów na subskrypcję — [Zobacz limity dotyczące subskrypcji tutaj](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- Zapewnia zakres przydziału bazy danych i limit przydziału jednostek DTU lub rdzeń wirtualny dla zasobów, które zawiera (na przykład 45 000 jednostek DTU)
- Jest zakresem przechowywania wersji dla funkcji włączonych w zawartych zasobach
- Logowania główne na poziomie serwera mogą zarządzać wszystkimi bazami danych na serwerze
- Może zawierać identyfikatory logowania podobne do tych w wystąpieniach SQL Server w środowisku lokalnym, którym udzielono dostępu do co najmniej jednej bazy danych na serwerze i można przyznać ograniczone prawa administracyjne. Aby uzyskać więcej informacji, zobacz temat [Logowania](logins-create-manage.md).
- Domyślne sortowanie dla wszystkich baz danych utworzonych na serwerze to `SQL_LATIN1_GENERAL_CP1_CI_AS` , gdzie `LATIN1_GENERAL` jest angielski (Stany Zjednoczone), `CP1` jest stroną kodową 1252, bez `CI` uwzględniania wielkości liter i `AS` jest uwzględniana w akcentie.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu Azure Portal

Można utworzyć grupę zasobów dla serwera przed czasem lub podczas tworzenia samego serwera. Istnieje wiele metod uzyskiwania informacji o nowym formularzu programu SQL Server, tworząc nowy serwer SQL lub w ramach tworzenia nowej bazy danych.

### <a name="create-a-blank-server"></a>Utwórz pusty serwer

Aby utworzyć serwer (bez bazy danych, puli elastycznej lub bazy danych magazynu danych) przy użyciu [Azure Portal](https://portal.azure.com), przejdź do pustego formularza programu SQL Server (logicznego serwera SQL).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Utwórz pustą lub przykładową bazę danych w Azure SQL Database

Aby utworzyć bazę danych w SQL Database przy użyciu [Azure Portal](https://portal.azure.com), przejdź do pustego formularza SQL Database i podaj żądane informacje. Można utworzyć grupę zasobów i serwer przed czasem lub podczas tworzenia samej bazy danych. Możesz utworzyć pustą bazę danych lub utworzyć przykładową bazę danych na podstawie firmy Adventure Works LT.

  ![tworzenie bazy danych 1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Aby uzyskać informacje na temat wybierania warstwy cenowej dla bazy danych, zobacz [model zakupów oparty na](service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md).

Aby utworzyć wystąpienie zarządzane, zobacz [Tworzenie wystąpienia zarządzanego](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Zarządzanie istniejącym serwerem

Aby zarządzać istniejącym serwerem, przejdź do serwera przy użyciu szeregu metod, takich jak z określonej strony bazy danych, strony **serwery SQL** lub strony **wszystkie zasoby** .

Aby zarządzać istniejącą bazą danych, przejdź do strony **bazy danych SQL** , a następnie kliknij bazę danych, którą chcesz zarządzać. Poniższy zrzut ekranu pokazuje, jak rozpocząć ustawianie zapory na poziomie serwera dla bazy danych na stronie **Przegląd** dla bazy danych.

   ![reguła zapory serwera](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Aby skonfigurować właściwości wydajności dla bazy danych, zobacz [model zakupów oparty na](service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md).
> [!TIP]
> Aby uzyskać Azure Portal przewodniku Szybki Start, zobacz [Tworzenie bazy danych w SQL Database w Azure Portal](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Do tworzenia serwerów, baz danych i zapór oraz zarządzania nimi za pomocą Azure PowerShell Użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps). Aby utworzyć pule elastyczne i zarządzać nimi, zobacz [Pule elastyczne](elastic-pool-overview.md).

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

> [!TIP]
> Aby zapoznać się z przewodnikiem Szybki Start dla programu PowerShell, zobacz [Tworzenie bazy danych w Azure SQL Database przy użyciu programu PowerShell](single-database-create-quickstart.md). Przykładowe skrypty programu PowerShell można znaleźć [w temacie Używanie programu PowerShell do tworzenia bazy danych w Azure SQL Database i konfigurowania reguły zapory](scripts/create-and-configure-database-powershell.md) oraz [monitorowania i skalowania bazy danych w Azure SQL Database przy użyciu programu PowerShell](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu interfejsu wiersza polecenia platformy Azure

Aby utworzyć serwery, bazy danych i zapory za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure)i zarządzać nimi, użyj następujących poleceń [SQL Database interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) . Używaj usługi [Cloud Shell](../../cloud-shell/overview.md), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows. Aby utworzyć pule elastyczne i zarządzać nimi, zobacz [Pule elastyczne](elastic-pool-overview.md).

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Tworzy bazę danych|
|[Lista AZ SQL DB](/cli/azure/sql/db#az-sql-db-list)|Wyświetla listę wszystkich baz danych zarządzanych przez serwer lub wszystkich baz danych w puli elastycznej|
|[AZ SQL DB list-Edition](/cli/azure/sql/db#az-sql-db-list-editions)|Wyświetla dostępne cele usługi i limity magazynu|
|[AZ SQL DB list-Usages](/cli/azure/sql/db#az-sql-db-list-usages)|Zwraca użycie bazy danych|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Pobiera bazę danych
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

> [!TIP]
> Przewodnik Szybki Start dotyczący interfejsu wiersza polecenia platformy Azure znajduje się [w temacie Tworzenie bazy danych w Azure SQL Database przy użyciu interfejsu wiersza polecenia platformy Azure](az-cli-script-samples-content-guide.md). Przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć [w temacie Używanie interfejsu wiersza polecenia do tworzenia bazy danych w Azure SQL Database i konfigurowania reguły zapory](scripts/create-and-configure-database-cli.md) i [używania interfejsu wiersza polecenia do monitorowania i skalowania bazy danych w programie Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu języka Transact-SQL

Aby utworzyć serwery, bazy danych i zapory w języku Transact-SQL i zarządzać nimi, użyj następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może nawiązać połączenie z serwerem i przekazać polecenia języka Transact-SQL. Aby zarządzać pulami elastycznymi, zobacz [Pule elastyczne](elastic-pool-overview.md).

> [!IMPORTANT]
> Nie można utworzyć lub usunąć serwera za pomocą języka Transact-SQL.

| Polecenie | Opis |
| --- | --- |
|[Utwórz bazę danych (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) | Tworzy nową bazę danych w Azure SQL Database. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
|[Tworzenie bazy danych (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest) | Tworzy nową bazę danych magazynu danych w usłudze Azure Synapse. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Modyfikuje bazę danych lub pulę elastyczną. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=sql-server-ver15)|Modyfikuje bazę danych magazynu danych w usłudze Azure Synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usługi), cel usługi (warstwę cenową) i nazwę puli elastycznej (jeśli istnieje) dla bazy danych. Jeśli użytkownik jest zalogowany do bazy danych Master dla serwera, zwraca informacje o wszystkich bazach danych. W przypadku usługi Azure Synapse musisz mieć połączenie z bazą danych Master.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zwraca użycie procesora CPU, operacji we/wy i pamięci dla bazy danych w Azure SQL Database. Jeden wiersz istnieje przez co 15 sekund, nawet jeśli w bazie danych nie ma żadnych działań.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Zwraca dane użycia procesora CPU i magazynu dla bazy danych w Azure SQL Database. Dane są zbierane i agregowane w ciągu pięciu minut.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Zawiera statystyki zdarzeń związanych z łącznością bazy danych dla Azure SQL Database, co zapewnia przegląd udanych i niepowodzeń połączeń z bazą danych. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Zwraca pomyślne Azure SQL Database połączenia z bazą danych, błędy połączeń i zakleszczenie dla Azure SQL Database. Te informacje służą do śledzenia i rozwiązywania problemów związanych z bazą danych.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje ustawienia zapory na poziomie serwera dla serwera. Ta procedura składowana jest dostępna tylko w bazie danych Master do nazwy logowania podmiotu zabezpieczeń na poziomie serwera. Regułę zapory na poziomie serwera można utworzyć tylko przy użyciu języka Transact-SQL po utworzeniu pierwszej reguły zapory na poziomie serwera przez użytkownika z uprawnieniami na poziomie platformy Azure.|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie serwera skojarzonych z serwerem.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Usuwa z serwera ustawienia zapory na poziomie serwera. Ta procedura składowana jest dostępna tylko w bazie danych Master do nazwy logowania podmiotu zabezpieczeń na poziomie serwera.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Tworzy lub aktualizuje reguły zapory na poziomie bazy danych dla bazy danych w Azure SQL Database. Reguły zapory bazy danych można skonfigurować dla bazy danych Master oraz dla baz danych użytkowników w SQL Database. Reguły zapory bazy danych są przydatne w przypadku korzystania z użytkowników zawartej bazy danych. Reguły zapory bazy danych nie są obsługiwane w usłudze Azure Synapse.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Zwraca informacje o ustawieniach zapory na poziomie bazy danych dla bazy danych w Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Usuwa ustawienie zapory na poziomie bazy danych dla bazy danych w Azure SQL Database. |

> [!TIP]
> Aby uzyskać szybki Start przy użyciu SQL Server Management Studio w systemie Microsoft Windows, zobacz [Azure SQL Database: używanie SQL Server Management Studio do nawiązywania połączenia i wykonywania zapytań dotyczących danych](connect-query-ssms.md). Aby uzyskać szybki Start przy użyciu Visual Studio Code w macOS, Linux lub Windows, zobacz [Azure SQL Database: używanie Visual Studio Code do nawiązywania połączenia i wykonywania zapytań dotyczących danych](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Zarządzanie serwerami, bazami danych i zaporami przy użyciu interfejsu API REST

Do tworzenia serwerów, baz danych i zapór oraz zarządzania nimi należy używać tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Serwery — Utwórz lub zaktualizuj](/rest/api/sql/servers/createorupdate)|Tworzy lub aktualizuje nowy serwer.|
|[Serwery — usuwanie](/rest/api/sql/servers/delete)|Usuwa serwer.|
|[Serwery — Pobierz](/rest/api/sql/servers/get)|Pobiera serwer.|
|[Serwery — lista](/rest/api/sql/servers/list)|Zwraca listę serwerów.|
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

- Aby dowiedzieć się więcej na temat migrowania bazy danych SQL Server do Azure SQL Database, zobacz [Migrowanie do Azure SQL Database](migrate-to-database-from-sql-server.md).
- Informacje dotyczące obsługiwanych funkcji można znaleźć w temacie [Funkcje](features-comparison.md).