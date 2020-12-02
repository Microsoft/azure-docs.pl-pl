---
title: Zarządzanie elastycznymi pulami
description: Tworzenie pul elastycznych Azure SQL Database i zarządzanie nimi przy użyciu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka Transact-SQL (T-SQL) i API REST.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 9c9af6e3bc3dfd798f4b3f0cad9319aa573c425d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455989"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Zarządzanie pulami elastycznymi w Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W przypadku puli elastycznej można określić ilość zasobów wymaganych przez pulę elastyczną do obsługi obciążenia jego baz danych oraz ilość zasobów dla każdej bazy danych w puli.

## <a name="azure-portal"></a>Azure Portal

Wszystkie ustawienia puli można znaleźć w jednym miejscu: w bloku **Konfigurowanie puli** . Aby rozpocząć, Znajdź pulę elastyczną w Azure Portal a następnie kliknij pozycję **Konfiguruj pulę** w górnej części bloku lub w menu zasób po lewej stronie.

W tym miejscu możesz wprowadzić dowolną kombinację następujących zmian i zapisać je wszystkie w jednej partii:

1. Zmiana warstwy usług puli
2. Skalowanie wydajności (DTU lub rdzeni wirtualnych) i przechowywanie w górę lub w dół
3. Dodawanie lub usuwanie baz danych do/z puli
4. Ustaw minimalny (gwarantowany) i maksymalny limit wydajności dla baz danych w pulach
5. Przejrzyj podsumowanie kosztów, aby wyświetlić zmiany w rachunku w wyniku nowych wyborów

![Blok konfiguracji elastycznej puli](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby tworzyć i zarządzać SQL Database elastycznymi pulami i bazami danych w puli za pomocą Azure PowerShell, należy użyć następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [install Azure PowerShell module](/powershell/azure/install-az-ps). Aby utworzyć serwery i zarządzać nimi dla puli elastycznej, zobacz [Tworzenie serwerów i zarządzanie nimi](logical-servers.md). Aby utworzyć reguły zapory i zarządzać nimi, zobacz [Tworzenie reguł zapory i zarządzanie nimi przy użyciu programu PowerShell](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Przykładowe skrypty programu PowerShell można znaleźć w temacie [tworzenie pul elastycznych i przenoszenie baz danych między pulami a poza pulą przy użyciu programu PowerShell](scripts/move-database-between-elastic-pools-powershell.md) i [monitorowanie i skalowanie elastycznej puli SQL w Azure SQL Database przy użyciu programu PowerShell](scripts/monitor-and-scale-pool-powershell.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Tworzy elastyczną pulę.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Pobiera elastyczne pule i ich wartości właściwości.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Modyfikuje właściwości puli elastycznej na przykład użyj właściwości **StorageMB** , aby zmodyfikować maksymalną wielkość magazynu puli elastycznej.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Usuwa pulę elastyczną.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Pobiera stan operacji w puli elastycznej|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczą bazę danych. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera co najmniej jedną bazę danych.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do puli elastycznej lub z niej.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Usuwa bazę danych.|

> [!TIP]
> Tworzenie wielu baz danych w elastycznej puli może zająć trochę czasu w przypadku użycia portalu lub poleceń cmdlet programu PowerShell, które tworzą tylko pojedynczą bazę danych w danym momencie. Aby zautomatyzować tworzenie w puli elastycznej, zobacz [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby tworzyć pule elastyczne SQL Database i zarządzać nimi za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure), użyj następujących poleceń [SQL Database interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/db) . Używaj usługi [Cloud Shell](../../cloud-shell/overview.md), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> Przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć w temacie [Używanie interfejsu wiersza polecenia do przenoszenia bazy danych w SQL Database w elastycznej puli SQL](scripts/move-database-between-elastic-pools-cli.md) i [Używanie interfejsu wiersza polecenia platformy Azure do skalowania elastycznej puli sql w programie Azure SQL Database](scripts/scale-pool-cli.md).
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[AZ SQL Elastic-Pool Create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Tworzy elastyczną pulę.|
|[AZ SQL Elastic-Pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Zwraca listę pul elastycznych na serwerze.|
|[AZ SQL Elastic-Pool list-baz danych](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Zwraca listę baz danych w puli elastycznej.|
|[AZ SQL Elastic-Pool list-Editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Obejmuje także dostępne ustawienia jednostek DTU puli, limity magazynu i ustawienia na bazę danych. Aby zmniejszyć poziom szczegółowości, dodatkowe limity magazynu i ustawienia poszczególnych baz danych są domyślnie ukryte.|
|[AZ SQL Elastic-Pool Update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualizuje pulę elastyczną.|
|[AZ SQL Elastic-Pool Delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Usuwa pulę elastyczną.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Aby tworzyć i przenosić bazy danych w ramach istniejących pul elastycznych lub zwracać informacje o SQL Database elastycznej puli przy użyciu języka Transact-SQL, należy użyć następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może nawiązać połączenie z serwerem i przekazać polecenia języka Transact-SQL. Aby utworzyć reguły zapory i zarządzać nimi przy użyciu języka T-SQL, zobacz [Zarządzanie regułami zapory za pomocą Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Nie można tworzyć, aktualizować ani usuwać Azure SQL Database elastycznej puli przy użyciu języka Transact-SQL. Można dodawać i usuwać bazy danych z puli elastycznej, a także używać widoków DMV, aby zwracać informacje o istniejących pulach elastycznych.
>

| Polecenie | Opis |
| --- | --- |
|[Utwórz bazę danych (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczą bazę danych. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych Master.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Przenoszenie bazy danych do puli elastycznej lub poza nią.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Zwraca statystyki użycia zasobów dla wszystkich pul elastycznych na serwerze. Dla każdej elastycznej puli istnieje jeden wiersz dla każdego 15 drugiego okna raportowania (cztery wiersze na minutę). Obejmuje to procesor CPU, operacje we/wy, dziennik, użycie magazynu i współbieżne użycie żądania/sesji przez wszystkie bazy danych w puli.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usług), cel usługi (warstwę cenową) i nazwę puli elastycznej (jeśli istnieje) dla bazy danych w SQL Database lub Azure Synapse Analytics. Jeśli użytkownik jest zalogowany do bazy danych Master na serwerze, zwraca informacje o wszystkich bazach danych. W przypadku usługi Azure Synapse Analytics musisz mieć połączenie z bazą danych Master.|

## <a name="rest-api"></a>Interfejs API REST

Aby tworzyć i zarządzać SQL Database elastycznymi pulami i bazami danych w puli, należy użyć tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Pule elastyczne — Tworzenie lub aktualizowanie](/rest/api/sql/elasticpools/createorupdate)|Tworzy nową pulę elastyczną lub aktualizuje istniejącą pulę elastyczną.|
|[Pule elastyczne — usuwanie](/rest/api/sql/elasticpools/delete)|Usuwa pulę elastyczną.|
|[Pule elastyczne — pobieranie](/rest/api/sql/elasticpools/get)|Pobiera pulę elastyczną.|
|[Pule elastyczne — lista według serwera](/rest/api/sql/elasticpools/listbyserver)|Zwraca listę pul elastycznych na serwerze.|
|[Pule elastyczne — aktualizacja](/rest/api/sql/elasticpools/listbyserver)|Aktualizuje istniejącą pulę elastyczną.|
|[Działania puli elastycznej](/rest/api/sql/elasticpoolactivities)|Zwraca działania puli elastycznej.|
|[Działania bazy danych puli elastycznej](/rest/api/sql/elasticpooldatabaseactivities)|Zwraca aktywność dla baz danych w puli elastycznej.|
|[Bazy danych — Utwórz lub zaktualizuj](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — Pobierz](/rest/api/sql/databases/get)|Pobiera bazę danych.|
|[Bazy danych — lista według elastycznej puli](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w puli elastycznej.|
|[Bazy danych — lista według serwera](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](saas-tenancy-app-design-patterns.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
* Aby zapoznać się z samouczkiem SaaS przy użyciu pul elastycznych, zobacz [wprowadzenie do aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).