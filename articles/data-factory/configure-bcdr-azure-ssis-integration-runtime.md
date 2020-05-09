---
title: Konfigurowanie Azure-SSIS Integration Runtime dla SQL Database trybu failover
description: W tym artykule opisano sposób konfigurowania Azure-SSIS Integration Runtime z Azure SQL Database replikacją geograficzną i trybem failover dla bazy danych SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592098"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurowanie Azure-SSIS Integration Runtime z Azure SQL Database replikacją geograficzną i trybem failover

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano sposób konfigurowania Azure-SSIS Integration Runtime przy użyciu Azure SQL Database replikacji geograficznej dla bazy danych SSISDB. W przypadku przejścia w tryb failover można upewnić się, że Azure-SSIS IR nadal pracuje z pomocniczą bazą danych.

Aby uzyskać więcej informacji na temat replikacji geograficznej i trybu failover dla SQL Database, zobacz temat [przegląd: aktywnej replikacji geograficznej i grup Autotryb failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR trybu failover z wystąpieniem Azure SQL Database zarządzanym

### <a name="prerequisites"></a>Wymagania wstępne

Azure SQL Database wystąpienie zarządzane używa **klucza głównego bazy danych (DMK)** w celu zapewnienia bezpieczeństwa danych, poświadczeń i informacji o połączeniu, które są przechowywane w bazie danych. Aby włączyć automatyczne odszyfrowywanie DMK, kopia klucza jest szyfrowana przy użyciu **klucza głównego serwera (klucza)**. Ale klucza nie jest replikowany w grupie trybu failover, dlatego należy dodać dodatkowe hasło na wystąpieniach podstawowych i pomocniczych na potrzeby odszyfrowywania DMK po przejściu do trybu failover.

1. Wykonaj poniższe polecenie na SSISDB w wystąpieniu podstawowym. Ten krok polega na dodaniu nowego hasła szyfrowania.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Utwórz grupę trybu failover dla Azure SQL Database wystąpienia zarządzanego.

3. Uruchom **sp_control_dbmasterkey_password** w wystąpieniu pomocniczym przy użyciu nowego hasła szyfrowania.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenariusz 1 — Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu i zapisu

Jeśli chcesz, aby Azure-SSIS IR punkt końcowy odbiornika do odczytu i zapisu, musisz najpierw wskazać podstawowy punkt końcowy serwera. Po wprowadzeniu SSISDB do grupy trybu failover można zmienić punkt końcowy odbiornika do odczytu i zapisu, a następnie ponownie uruchomić Azure-SSIS IR.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover należy wykonać następujące czynności:

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Edytuj Azure-SSIS IR przy użyciu nowego regionu, sieci wirtualnej i niestandardowego identyfikatora URI SAS dla wystąpienia pomocniczego. Jako Azure-SSIS IR wskazuje odbiornik do odczytu i zapisu, a punkt końcowy jest niewidoczny dla Azure-SSIS IR, nie trzeba edytować punktu końcowego.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Uruchom ponownie Azure-SSIS IR.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenariusz 2 — Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera

Scenariusz jest odpowiedni, jeśli Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover należy wykonać następujące czynności:

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Edytuj Azure-SSIS IR z nowym regionem, punktem końcowym i informacjami o sieci wirtualnej wystąpienia pomocniczego.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Uruchom ponownie Azure-SSIS IR.

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Scenariusz 3 — Azure-SSIS IR wskazuje publiczny punkt końcowy Azure SQL Database wystąpienia zarządzanego

Scenariusz jest odpowiedni, jeśli Azure-SSIS IR wskazuje publiczny punkt końcowy Azure SQL Database wystąpienia zarządzanego i nie łączy się z siecią wirtualną. Jedyną różnicą między scenariuszem 2 i tym scenariuszem jest to, że nie trzeba edytować informacji o sieci wirtualnej Azure-SSIS IR po przejściu do trybu failover.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover należy wykonać następujące czynności:

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Edytuj Azure-SSIS IR z nowym regionem i informacjami o punkcie końcowym wystąpienia pomocniczego.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Uruchom ponownie Azure-SSIS IR.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenariusz 4 — dołączanie istniejącego SSISDB (katalog SSIS) do nowego Azure-SSIS IR

Ten scenariusz jest przydatny, jeśli chcesz zainicjować obsługę nowego Azure-SSIS IR w regionie pomocniczym lub chcesz, aby SSISDB pracował z nowym Azure-SSIS IR w nowym regionie, gdy w bieżącym regionie wystąpi awaria APD lub Azure-SSIS IR.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover należy wykonać następujące czynności:

> [!NOTE]
> Krok 4 (Tworzenie IR) musi być realizowany za pośrednictwem programu PowerShell. Azure Portal zgłosi błąd informujący o tym, że SSISDB już istnieje.

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Wykonaj procedurę składowaną, aby zaktualizować metadane w programie SSISDB, aby akceptować połączenia z ** \<new_data_factory_name\> ** i ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Utwórz nową fabrykę danych o ** \<nazwie\> new_data_factory_name** w nowym regionie. Aby uzyskać więcej informacji, zobacz Tworzenie fabryki danych.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie fabryki danych Azure przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)

4. Utwórz nową Azure-SSIS IR o nazwie ** \<new_integration_runtime_name\> ** w nowym regionie przy użyciu Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md)



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR trybu failover z Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenariusz 1 — Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu i zapisu

W tym scenariuszu odpowiednie Azure-SSIS IR wskazują punkt końcowy odbiornika do odczytu i zapisu w grupie trybu failover, a serwer SQL Database *nie* jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej. Jeśli chcesz, aby Azure-SSIS IR punkt końcowy odbiornika do odczytu i zapisu, musisz najpierw wskazać podstawowy punkt końcowy serwera. Po wprowadzeniu SSISDB do grupy trybu failover można zmienić punkt końcowy odbiornika do odczytu i zapisu, a następnie ponownie uruchomić Azure-SSIS IR.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover jest on niewidoczny dla Azure-SSIS IR. Azure-SSIS IR automatycznie nawiązuje połączenie z nowym podstawowym grupą trybu failover. Jeśli chcesz zaktualizować region lub inne informacje w Azure-SSIS IR, możesz je zatrzymać, edytować i uruchomić ponownie.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenariusz 2 — Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera

Scenariusz jest odpowiedni, jeśli Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover należy wykonać następujące czynności:

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Edytuj Azure-SSIS IR przy użyciu nowego regionu, punktu końcowego i informacji o sieci wirtualnej wystąpienia pomocniczego.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Uruchom ponownie Azure-SSIS IR.

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenariusz 3 — dołączanie istniejącego SSISDB (katalog SSIS) do nowego Azure-SSIS IR

Ten scenariusz jest przydatny, jeśli chcesz zainicjować obsługę nowego Azure-SSIS IR w regionie pomocniczym lub chcesz, aby SSISDB pracował z nowym Azure-SSIS IR w nowym regionie, gdy w bieżącym regionie wystąpi awaria APD lub Azure-SSIS IR.

#### <a name="solution"></a>Rozwiązanie

> [!NOTE]
> Krok 4 (Tworzenie IR) musi być realizowany za pośrednictwem programu PowerShell. Azure Portal zgłosi błąd informujący o tym, że SSISDB już istnieje.

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Wykonaj procedurę składowaną, aby zaktualizować metadane w programie SSISDB, aby akceptować połączenia z ** \<new_data_factory_name\> ** i ** \<new_integration_runtime_name\>**.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Utwórz nową fabrykę danych o ** \<nazwie\> new_data_factory_name** w nowym regionie. Aby uzyskać więcej informacji, zobacz Tworzenie fabryki danych.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie fabryki danych Azure przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)

4. Utwórz nową Azure-SSIS IR o nazwie ** \<new_integration_runtime_name\> ** w nowym regionie przy użyciu Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md)


## <a name="next-steps"></a>Następne kroki

Należy wziąć pod uwagę następujące inne opcje konfiguracji Azure-SSIS IR:

- [Konfigurowanie Azure-SSIS Integration Runtime na potrzeby wysokiej wydajności](configure-azure-ssis-integration-runtime-performance.md)

- [Dostosowywanie konfiguracji środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Udostępnianie wersji Enterprise Edition dla Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
