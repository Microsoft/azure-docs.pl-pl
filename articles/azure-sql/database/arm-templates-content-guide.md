---
title: Szablony usługi Azure Resource Manager
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Za pomocą szablonów Azure Resource Manager Utwórz i skonfiguruj Azure SQL Database i wystąpienie zarządzane usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053291"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Szablony Azure Resource Manager dla Azure SQL Database & wystąpienie zarządzane SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Szablony Azure Resource Manager umożliwiają definiowanie infrastruktury jako kodu i wdrażanie rozwiązań w chmurze platformy Azure dla Azure SQL Database i wystąpienia zarządzanego Azure SQL.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Azure SQL Database.

| |  |
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ten Azure Resource Manager szablonu służy do tworzenia pojedynczej bazy danych w programie Azure SQL Database i konfigurowania reguł zapory adresów IP na poziomie serwera. |
| [Serwer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ten Azure Resource Manager szablon tworzy serwer dla Azure SQL Database. |
| [Pula elastyczna](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ten szablon umożliwia wdrożenie elastycznej puli i przypisanie do niej baz danych. |
| [Grupy trybu failover](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ten szablon tworzy dwa serwery, pojedynczą bazę danych i grupę trybu failover w Azure SQL Database.|
| [Wykrywanie zagrożeń](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ten szablon umożliwia wdrożenie serwera i zestawu baz danych z włączonym wykrywaniem zagrożeń przy użyciu adresu e-mail dla alertów dla każdej bazy danych. Wykrywanie zagrożeń jest częścią oferty SQL Advanced Threat Protection (ATP) i zapewnia warstwę zabezpieczeń, która reaguje na potencjalne zagrożenia za pośrednictwem serwerów i baz danych.|
| [Przeprowadzanie inspekcji w usłudze Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ten szablon umożliwia wdrożenie serwera z włączoną inspekcją w celu zapisania dzienników inspekcji do magazynu obiektów BLOB. Inspekcja usługi Azure SQL Database śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji, który można umieścić na Twoim koncie magazynu platformy Azure, w obszarze roboczym pakietu OMS lub w centrach zdarzeń.|
| [Przeprowadzanie inspekcji w Centrum zdarzeń platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ten szablon umożliwia wdrożenie serwera z włączoną inspekcją w celu zapisania dzienników inspekcji w istniejącym centrum zdarzeń. Aby można było wysyłać zdarzenia inspekcji do centrum zdarzeń, należy ustawić ustawienia inspekcji z `Enabled` `State` i ustawić `IsAzureMonitorTargetEnabled` jako `true` . Ponadto skonfiguruj ustawienia diagnostyczne z `SQLSecurityAuditEvents` kategorią dziennika w `master` bazie danych programu (na potrzeby inspekcji na poziomie). Inspekcja śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji, który można umieścić na koncie usługi Azure Storage, w obszarze roboczym pakietu OMS lub w Event Hubs.|
| [Aplikacja internetowa platformy Azure z usługą SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | W tym przykładzie zostanie utworzona bezpłatna aplikacja internetowa platformy Azure i baza danych w Azure SQL Database na poziomie usługi "podstawowa".|
| [Aplikacja internetowa platformy Azure i pamięć podręczna Redis Cache z usługą SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ten szablon tworzy aplikację internetową, pamięć podręczną Redis Cache i bazę danych SQL w tej samej grupie zasobów oraz tworzy dwa zestawy parametrów połączenia w aplikacji internetowej dla bazy danych SQL i pamięci podręcznej Redis Cache.|
| [Importowanie danych z magazynu obiektów blob za pomocą usługi ADF w wersji 2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ten szablon usługi Azure Resource Manager tworzy usługę Azure Data Factory w wersji 2, która kopiuje dane z usługi Azure Blob Storage do usługi SQL Database.|
| [Klaster HDInsight z usługą SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ten szablon umożliwia utworzenie klastra usługi HDInsight, logicznego serwera SQL, bazy danych i dwóch tabel. Ten szablon jest używany przez artykuł [Używanie składnika Sqoop z usługą Hadoop w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Aplikacja logiki platformy Azure, która uruchamia procedurę składowaną SQL zgodnie z harmonogramem](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ten szablon umożliwia tworzenie aplikacji logiki, która będzie uruchamiać procedurę składowaną SQL zgodnie z harmonogramem. Wszelkie argumenty dla procedury można umieścić w sekcji treści szablonu.|

## <a name="azure-sql-managed-instance"></a>[Wystąpienie zarządzane Azure SQL](#tab/managed-instance)

Poniższa tabela zawiera linki do Azure Resource Manager szablonów dla wystąpienia zarządzanego usługi Azure SQL.

| |  |
|---|---|
| [Wystąpienie zarządzane SQL w nowej sieci wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ten Azure Resource Manager szablonu służy do tworzenia nowej skonfigurowanej sieci wirtualnej platformy Azure i wystąpienia zarządzanego SQL w sieci wirtualnej. |
| [Środowisko sieciowe dla wystąpienia zarządzanego SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | To wdrożenie spowoduje utworzenie skonfigurowanej Virtual Network platformy Azure z dwiema podsieciami, które będą przeznaczone dla wystąpień zarządzanych SQL, oraz inne, w których można umieścić inne zasoby (na przykład maszyny wirtualne, środowiska App Service itp.). Ten szablon spowoduje utworzenie prawidłowo skonfigurowanego środowiska sieciowego, w którym można wdrożyć wystąpienia zarządzane SQL. |
| [Wystąpienie zarządzane SQL z połączeniem P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | To wdrożenie utworzy sieć wirtualną platformy Azure z dwiema podsieciami `ManagedInstance` i `GatewaySubnet`. Wystąpienie zarządzane SQL zostanie wdrożone w podsieci ManagedInstance. Brama sieci wirtualnej zostanie utworzona w podsieci `GatewaySubnet` i skonfigurowana dla połączenia sieci VPN typu punkt-lokacja. |
| [Wystąpienie zarządzane SQL z maszyną wirtualną](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | To wdrożenie utworzy sieć wirtualną platformy Azure z dwiema podsieciami `ManagedInstance` i `Management`. Wystąpienie zarządzane SQL zostanie wdrożone w `ManagedInstance` podsieci. Maszyna wirtualna z najnowszą wersją programu SQL Server Management Studio (SSMS) zostanie wdrożona w podsieci `Management`. |

---
