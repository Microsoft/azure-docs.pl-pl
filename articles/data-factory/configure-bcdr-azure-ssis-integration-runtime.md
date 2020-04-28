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
ms.openlocfilehash: 39d55d4372f03a1625bb04d8377ed6533401e281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188726"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurowanie Azure-SSIS Integration Runtime z Azure SQL Database replikacją geograficzną i trybem failover

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób konfigurowania Azure-SSIS Integration Runtime przy użyciu Azure SQL Database replikacji geograficznej dla bazy danych SSISDB. W przypadku przejścia w tryb failover można upewnić się, że Azure-SSIS IR nadal pracuje z pomocniczą bazą danych.

Aby uzyskać więcej informacji na temat replikacji geograficznej i trybu failover dla SQL Database, zobacz temat [przegląd: aktywnej replikacji geograficznej i grup Autotryb failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR trybu failover z wystąpieniem Azure SQL Database zarządzanym

### <a name="prerequisites"></a>Wymagania wstępne
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

### <a name="solution"></a>Rozwiązanie
W przypadku przejścia w tryb failover, jeśli chcesz używać istniejących Azure-SSIS IR w regionie podstawowym:
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

4. Zmień nazwę serwera w **ConnectionManager** pakietów SSIS na nazwę serwera wystąpienia pomocniczego, a następnie wdróż ponownie te pakiety i uruchom je.

Jeśli chcesz zainicjować obsługę nowego Azure-SSIS IR w regionie pomocniczym:
> [!NOTE]
> Krok 4 (Tworzenie IR) musi być realizowany za pośrednictwem programu PowerShell. Azure Portal zgłosi błąd informujący o tym, że SSISDB już istnieje.
1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Wykonaj procedurę składowaną, aby zaktualizować metadane w programie SSISDB, aby akceptować połączenia z ** \<new_data_factory_name\> ** i ** \<new_integration_runtime_name\>**.
   
```SQL
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

5. Zmień nazwę serwera w **ConnectionManager** pakietów SSIS na nazwę serwera wystąpienia pomocniczego, a następnie wdróż ponownie te pakiety i uruchom je.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR trybu failover z Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenariusz 1 — Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu i zapisu

#### <a name="conditions"></a>Warunki

Ta sekcja ma zastosowanie w przypadku spełnienia następujących warunków:

- Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu i zapisu grupy trybu failover.

  AND

- Serwer SQL Database *nie* jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej.

#### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover jest on niewidoczny dla Azure-SSIS IR. Azure-SSIS IR automatycznie nawiązuje połączenie z nowym podstawowym grupą trybu failover.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenariusz 2 — Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera

#### <a name="conditions"></a>Warunki

Ta sekcja ma zastosowanie w przypadku spełnienia jednego z następujących warunków:

- Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera grupy trybu failover. Ten punkt końcowy zmienia się w przypadku przejścia w tryb failover.

  LUB

- Serwer Azure SQL Database jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej.


#### <a name="solution"></a>Rozwiązanie

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

4. Zmień nazwę serwera w **ConnectionManager** pakietów SSIS na nazwę serwera wystąpienia pomocniczego, a następnie wdróż ponownie te pakiety i uruchom je.


### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenariusz 3 — dołączanie istniejącego SSISDB (katalog SSIS) do nowego Azure-SSIS IR

Gdy w bieżącym regionie wystąpi awaria ADF lub Azure-SSIS IR, można sprawić, aby SSISDB pracował z nowym Azure-SSIS IR w nowym regionie.

#### <a name="solution"></a>Rozwiązanie

> [!NOTE]
> Krok 4 (Tworzenie IR) musi być realizowany za pośrednictwem programu PowerShell. Azure Portal zgłosi błąd informujący o tym, że SSISDB już istnieje.

1. Zatrzymaj Azure-SSIS IR w regionie podstawowym.

2. Wykonaj procedurę składowaną, aby zaktualizować metadane w programie SSISDB, aby akceptować połączenia z ** \<new_data_factory_name\> ** i ** \<new_integration_runtime_name\>**.
   
```SQL
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

5. Zmień nazwę serwera w **ConnectionManager** pakietów SSIS na nazwę serwera wystąpienia pomocniczego, a następnie wdróż ponownie te pakiety i uruchom je.


## <a name="next-steps"></a>Następne kroki

Należy wziąć pod uwagę następujące inne opcje konfiguracji Azure-SSIS IR:

- [Konfigurowanie Azure-SSIS Integration Runtime na potrzeby wysokiej wydajności](configure-azure-ssis-integration-runtime-performance.md)

- [Dostosowywanie konfiguracji środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Udostępnianie wersji Enterprise Edition dla Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
