---
title: Wymagania wstępne dotyczące Azure Database Migration Service
description: Dowiedz się więcej na temat wymagań wstępnych dotyczących korzystania z Azure Database Migration Service w celu przeprowadzenia migracji bazy danych.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 55d5594229bccb5fcb6a406e671ed104c1e12378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094056"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Omówienie wymagań wstępnych dotyczących używania usługi Azure Database Migration Service

Istnieje kilka wymagań wstępnych, aby zapewnić bezproblemowy przebieg Azure Database Migration Service podczas przeprowadzania migracji baz danych. Niektóre wymagania wstępne dotyczą wszystkich scenariuszy (par Source-Target) obsługiwanych przez usługę, a inne wymagania wstępne są unikatowe dla konkretnego scenariusza.

Wymagania wstępne związane z używaniem Azure Database Migration Service są wymienione w poniższych sekcjach.

## <a name="prerequisites-common-across-migration-scenarios"></a>Wymagania wstępne typowe dla scenariuszy migracji

Azure Database Migration Service wymagania wstępne, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji, to m.in.:

* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej (sieciowej grupy zabezpieczeń) nie blokują portu wychodzącego 443 z ServiceTag dla ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych może zajść potrzeba dodania reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Utworzenie wystąpienia Azure Database Migration Service wymaga dostępu do ustawień sieci wirtualnej, które zwykle nie należą do tej samej grupy zasobów. W związku z tym użytkownik tworzący wystąpienie elementu DMS wymaga uprawnień na poziomie subskrypcji. Aby utworzyć wymagane role, które można przypisać w razie potrzeby, uruchom następujący skrypt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Wymagania wstępne dotyczące migrowania SQL Server do Azure SQL Database

Oprócz Azure Database Migration Service wymagań wstępnych, które są wspólne dla wszystkich scenariuszy migracji, istnieją także wymagania wstępne, które są stosowane w odniesieniu do jednego scenariusza lub innego.

W przypadku używania Azure Database Migration Service do przeprowadzenia SQL Server do migracji Azure SQL Database, oprócz wymagań wstępnych, które są wspólne dla wszystkich scenariuszy migracji, należy pamiętać o spełnieniu następujących dodatkowych wymagań wstępnych:

* Utwórz wystąpienie Azure SQL Database wystąpienia, postępując zgodnie ze szczegółowymi informacjami w artykule [Tworzenie bazy danych w Azure SQL Database w Azure Portal](../azure-sql/database/single-database-create-quickstart.md).
* Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Utwórz [regułę zapory](../azure-sql/database/firewall-configure.md) na poziomie serwera dla SQL Database, aby umożliwić Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla Azure Database Migration Service.
* Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Upewnij się, że poświadczenia używane do nawiązania połączenia z docelową bazą danych mają uprawnienia do sterowania bazą danych w docelowej bazie danych.

   > [!NOTE]
   > Aby zapoznać się z pełną listą wymagań wstępnych wymaganych Azure Database Migration Service do przeprowadzenia migracji z SQL Server do Azure SQL Database, zapoznaj się z samouczkiem [migrowanie SQL Server do Azure SQL Database](./tutorial-sql-server-to-azure-sql.md).
   >

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance"></a>Wymagania wstępne dotyczące migrowania SQL Server do wystąpienia zarządzanego usługi Azure SQL

* Utwórz wystąpienie zarządzane SQL, postępując zgodnie ze szczegółowymi informacjami w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL w Azure Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Otwórz zaporę, aby zezwolić na ruch SMB na porcie 445 dla adresu IP lub zakresu podsieci Azure Database Migration Service.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.
* Utworzenie udziału sieciowego umożliwiającego wykonanie kopii zapasowej źródłowej bazy danych za pomocą usługi Azure Database Migration Service.
* Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
* Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage w celu wykonania operacji przywracania.
* Utwórz kontener obiektów blob i Pobierz jego identyfikator URI sygnatury dostępu współdzielonego, wykonując kroki opisane w artykule [Zarządzanie zasobami BLOB Storage platformy Azure przy użyciu Eksplorator usługi Storage](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Pamiętaj, aby podczas tworzenia identyfikatora URI SAS wybrać opcję wszystkie uprawnienia (odczyt, zapis, usuwanie, lista) w oknie zasady.

   > [!NOTE]
   > Aby zapoznać się z pełną listą wymagań wstępnych potrzebnych do korzystania z Azure Database Migration Service w celu przeprowadzenia migracji z SQL Server do wystąpienia zarządzanego SQL, zobacz samouczek [migrowanie SQL Server do wystąpienia zarządzanego SQL](./tutorial-sql-server-to-managed-instance.md).

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem Azure Database Migration Service i regionalnej dostępności, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).