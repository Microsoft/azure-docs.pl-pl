---
title: Zarządzanie elastycznymi pulami
description: Tworzenie pul elastycznych Azure SQL Database i zarządzanie nimi przy użyciu programu Azure Portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, języka Transact-SQL (T-SQL) i interfejsu API REST.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: dc2bb24880b77eae24e9bb2ef0baf70ac0b92ac7
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588636"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Zarządzanie elastycznymi pulami w Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W przypadku elastycznej puli określasz ilość zasobów wymaganych przez pulę elastyczną do obsługi obciążenia jej baz danych oraz ilość zasobów dla każdej bazy danych w puli.

## <a name="azure-portal"></a>Azure Portal

Wszystkie ustawienia puli można znaleźć w jednym miejscu: **bloku Konfigurowanie** puli. Aby uzyskać dostęp do tego elementu, znajdź  pulę elastyczną w Azure Portal i kliknij pozycję Konfiguruj pulę w górnej części bloku lub w menu zasobów po lewej stronie.

W tym miejscu możesz wprowadzić dowolną kombinację następujących zmian i zapisać je wszystkie w jednej partii:

1. Zmienianie warstwy usługi puli
2. Skalowanie wydajności (jednostek DTU lub rdzeni wirtualnych) i magazynu w górę lub w dół
3. Dodawanie lub usuwanie baz danych do puli i z puli
4. Ustawianie minimalnego (gwarantowanego) i maksymalnego limitu wydajności dla baz danych w pulach
5. Przejrzyj podsumowanie kosztów, aby wyświetlić wszelkie zmiany na rachunku w wyniku nowych wyborów

![Blok konfiguracji puli elastycznej](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł powershell Azure Resource Manager jest nadal obsługiwany przez Azure SQL Database, ale cały przyszły rozwój jest dla modułu Az.Sql. Aby uzyskać informacje na temat tych poleceń cmdlet, [zobacz AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenty poleceń w module Az i modułach AzureRm są zasadniczo identyczne.

Aby tworzyć pule SQL Database i bazy danych w puli oraz zarządzać nimi za pomocą Azure PowerShell, użyj następujących poleceń cmdlet programu PowerShell. Jeśli musisz zainstalować lub uaktualnić program PowerShell, zobacz [Instalowanie Azure PowerShell modułu](/powershell/azure/install-az-ps). Aby utworzyć serwery dla elastycznej puli i zarządzać nimi, zobacz [Tworzenie serwerów i zarządzanie nimi.](logical-servers.md) Aby utworzyć reguły zapory i zarządzać nimi, zobacz Tworzenie reguł zapory i zarządzanie [nimi przy użyciu programu PowerShell.](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)

> [!TIP]
> Przykładowe skrypty programu PowerShell można znaleźć w tematach Create elastic pools and move databases between pools and out of a pool using PowerShell (Tworzenie pul elastycznych i przenoszenie baz danych między pulami i poza pulę przy użyciu programu [PowerShell)](scripts/move-database-between-elastic-pools-powershell.md) i Use PowerShell to monitor and scale a SQL elastic pool in Azure SQL Database (Używanie programu PowerShell do monitorowania i skalowania elastycznej puli SQL w [programie Azure SQL Database).](scripts/monitor-and-scale-pool-powershell.md)
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Tworzy elastyczną pulę.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Pobiera elastyczne pule i ich wartości właściwości.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Modyfikuje właściwości elastycznej puli. Na przykład użyj właściwości **StorageMB,** aby zmodyfikować maksymalny rozmiar magazynu elastycznej puli.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Usuwa elastyczną pulę.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Pobiera stan operacji w elastycznej puli|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczą bazę danych. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Pobiera co najmniej jedną bazę danych.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Ustawia właściwości bazy danych lub przenosi istniejącą bazę danych do pul elastycznych, poza pule elastyczne lub między nimi.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Usuwa bazę danych.|

> [!TIP]
> Tworzenie wielu baz danych w elastycznej puli może zająć trochę czasu w przypadku korzystania z portalu lub poleceń cmdlet programu PowerShell, które tworzą jednocześnie tylko jedną bazę danych. Aby zautomatyzować tworzenie w elastycznej puli, zobacz [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby tworzyć pule elastyczne SQL Database i zarządzać nimi za pomocą interfejsu wiersza polecenia platformy [Azure,](/cli/azure)użyj następujących poleceń interfejsu wiersza [SQL Database](/cli/azure/sql/db) Azure. Używaj usługi [Cloud Shell](../../cloud-shell/overview.md), aby uruchamiać interfejs wiersza polecenia w przeglądarce, albo [zainstaluj](/cli/azure/install-azure-cli) go w systemie macOS, Linux lub Windows.

> [!TIP]
> Przykładowe skrypty interfejsu wiersza polecenia platformy Azure można znaleźć w tematach Use [CLI to move a database in SQL Database in a SQL elastic pool](scripts/move-database-between-elastic-pools-cli.md) (Używanie interfejsu wiersza polecenia platformy Azure do przenoszenia bazy danych w elastycznej puli SQL) i Use Azure CLI to scale a SQL elastic pool in Azure SQL Database (Używanie interfejsu wiersza polecenia platformy Azure do skalowania elastycznej puli SQL w [programie Azure SQL Database).](scripts/scale-pool-cli.md)
>

| Polecenie cmdlet | Opis |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Tworzy elastyczną pulę.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Zwraca listę elastycznych pul na serwerze.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Zwraca listę baz danych w elastycznej puli.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Obejmuje również dostępne ustawienia jednostek DTU puli, limity magazynu i ustawienia 1 bazy danych. Aby zmniejszyć poziom szczegółowości, dodatkowe limity magazynu i ustawienia na bazę danych są domyślnie ukryte.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Aktualizuje pulę elastyczną.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Usuwa pulę elastyczną.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Aby utworzyć i przenieść bazy danych w istniejących pulach elastycznych lub zwrócić informacje o SQL Database puli elastycznej za pomocą języka Transact-SQL, użyj następujących poleceń języka T-SQL. Te polecenia można wydać przy użyciu poleceń Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)lub dowolnego innego programu, który może nawiązać połączenie z serwerem i przekazać polecenia języka Transact-SQL. Aby utworzyć reguły zapory i zarządzać nimi przy użyciu języka T-SQL, zobacz [Manage firewall rules using Transact-SQL (Zarządzanie regułami zapory przy użyciu języka Transact-SQL).](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)

> [!IMPORTANT]
> Nie można tworzyć, aktualizować ani usuwać puli Azure SQL Database przy użyciu języka Transact-SQL. Bazy danych można dodawać do puli elastycznej lub usuwać z nich, a do zwracania informacji o istniejących pulach elastycznych można używać widoków DMV.
>

| Polecenie | Opis |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Tworzy nową bazę danych w istniejącej puli lub jako pojedynczą bazę danych. Aby utworzyć nową bazę danych, musisz mieć połączenie z bazą danych master.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Przenieś bazę danych do pul elastycznych, poza pule elastyczne lub między nimi.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Usuwa bazę danych.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Zwraca statystyki użycia zasobów dla wszystkich pul elastycznych na serwerze. Dla każdej elastycznej puli istnieje jeden wiersz dla każdego 15-sekundowego okna raportowania (cztery wiersze na minutę). Obejmuje to użycie procesora CPU, we/wy, dziennika, magazynu i współbieżne wykorzystanie żądań/sesji przez wszystkie bazy danych w puli.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Zwraca wersję (warstwę usługi), cel usługi (warstwę cenową) i nazwę puli elastycznej, jeśli jest, dla bazy danych w SQL Database lub Azure Synapse Analytics. Jeśli użytkownik jest zalogowany do bazy danych master na serwerze, zwraca informacje o wszystkich bazach danych. Aby Azure Synapse Analytics, musisz mieć połączenie z bazą danych master.|

## <a name="rest-api"></a>Interfejs API REST

Aby tworzyć pule elastyczne SQL Database baz danych w puli i zarządzać nimi, użyj tych żądań interfejsu API REST.

| Polecenie | Opis |
| --- | --- |
|[Elastyczne pule — tworzenie lub aktualizowanie](/rest/api/sql/elasticpools/createorupdate)|Tworzy nową pulę elastyczną lub aktualizuje istniejącą pulę elastyczną.|
|[Elastyczne pule — usuwanie](/rest/api/sql/elasticpools/delete)|Usuwa pulę elastyczną.|
|[Elastyczne pule — pobierz](/rest/api/sql/elasticpools/get)|Pobiera elastyczną pulę.|
|[Elastyczne pule — lista według serwera](/rest/api/sql/elasticpools/listbyserver)|Zwraca listę elastycznych pul na serwerze.|
|[Pule elastyczne — aktualizacja] (/rest/api/sql/2020-11-01-preview/elasticpools/update
)|Aktualizuje istniejącą pulę elastyczną.|
|[Działania puli elastycznej](/rest/api/sql/elasticpoolactivities)|Zwraca działania puli elastycznej.|
|[Działania bazy danych elastycznej puli](/rest/api/sql/elasticpooldatabaseactivities)|Zwraca działanie w bazach danych wewnątrz elastycznej puli.|
|[Bazy danych — tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate)|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|[Bazy danych — pobierz](/rest/api/sql/databases/get)|Pobiera bazę danych.|
|[Bazy danych — lista według puli elastycznej](/rest/api/sql/databases/listbyelasticpool)|Zwraca listę baz danych w elastycznej puli.|
|[Bazy danych — lista według serwera](/rest/api/sql/databases/listbyserver)|Zwraca listę baz danych na serwerze.|
|[Bazy danych — aktualizacja](/rest/api/sql/databases/update)|Aktualizuje istniejącą bazę danych.|

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat wzorców projektowych dla aplikacji SaaS wykorzystujących pule elastyczne, zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](saas-tenancy-app-design-patterns.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database).
* Aby uzyskać samouczek saaS dotyczący używania elastycznych pul, [zobacz Introduction to the Wingtip SaaS application (Wprowadzenie do aplikacji SaaS Wingtip).](saas-dbpertenant-wingtip-app-overview.md)
