---
title: Konfigurowanie środowiska wykonawczego integracji platformy Azure-SSIS dla trybu failover bazy danych SQL
description: W tym artykule opisano sposób konfigurowania środowiska wykonawczego integracji platformy Azure-SSIS za pomocą replikacji geograficznej usługi Azure SQL Database i pracy awaryjnej dla bazy danych SSISDB
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
ms.openlocfilehash: 9548d3eb4f51dd61186aa7f13343d946035d95ef
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603635"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurowanie środowiska wykonawczego integracji platformy Azure-SSIS za pomocą replikacji geograficznej usługi Azure SQL Database i pracy awaryjnej

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano sposób konfigurowania środowiska wykonawczego integracji platformy Azure-SSIS za pomocą replikacji geograficznej bazy danych SQL bazy danych azure dla bazy danych SSISDB. W przypadku pracy awaryjnej można upewnić się, że usługa Azure-SSIS IR kontynuuje pracę z pomocniczą bazą danych.

Aby uzyskać więcej informacji na temat replikacji geograficznej i pracy awaryjnej bazy danych SQL, zobacz [Omówienie: Aktywne grupy replikacji geograficznej i automatycznego trybu failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenariusz 1 — usługa Azure-SSIS IR wskazuje punkt końcowy odbiornika odczytu i zapisu

### <a name="conditions"></a>Warunki

Ta sekcja ma zastosowanie, gdy spełnione są następujące warunki:

- Usługa Azure-SSIS IR wskazuje punkt końcowy odbiornika odczytu i zapisu grupy trybu failover.

  AND

- Serwer bazy danych SQL *nie* jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

Po wykonaniu trybu failover jest niewidoczny dla usługi Azure-SSIS IR. Usługa Azure-SSIS IR automatycznie łączy się z nową podstawową grupą trybu failover.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenariusz 2 — usługa Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera

### <a name="conditions"></a>Warunki

Ta sekcja ma zastosowanie, gdy spełniony jest jeden z następujących warunków:

- Usługa Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera grupy trybu failover. Ten punkt końcowy zmienia się po wystąpieniu pracy awaryjnej.

  LUB

- Serwer bazy danych SQL azure jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej.

  LUB

- Serwer bazy danych jest wystąpieniem zarządzanym bazy danych SQL skonfigurowanym z siecią wirtualną.

### <a name="solution"></a>Rozwiązanie

W przypadku pracy awaryjnej należy wykonać następujące czynności:

1. Zatrzymaj usługę Azure-SSIS IR.

2. Ponownie skonfiguruj środowisko IR, aby wskazywały nowy podstawowy punkt końcowy i sieć wirtualną w nowym regionie.

3. Uruchom ponownie podczerwem.

W poniższych sekcjach opisano te kroki bardziej szczegółowo.

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że włączono odzyskiwanie po awarii dla serwera usługi Azure SQL Database w przypadku awarii serwera w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Omówienie ciągłości biznesowej za pomocą usługi Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Jeśli używasz sieci wirtualnej w bieżącym regionie, należy użyć innej sieci wirtualnej w nowym regionie, aby połączyć środowisko uruchomieniowe integracji azure-SSIS. Aby uzyskać więcej informacji, zobacz [Dołączanie środowiska wykonawczego integracji platformy Azure-SSIS do sieci wirtualnej.](join-azure-ssis-integration-runtime-virtual-network.md)

- Jeśli używasz konfiguracji niestandardowej, może być konieczne przygotowanie innego identyfikatora URI sygnatury dostępu Współdzielonego dla kontenera obiektów blob, który przechowuje niestandardowy skrypt konfiguracji i skojarzone pliki, dzięki czemu nadal jest dostępny podczas awarii. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konfiguracji niestandardowej w czasie wykonywania integracji platformy Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroki

Wykonaj następujące kroki, aby zatrzymać usługę Azure-SSIS IR, przełączyć podczerwenie do nowego regionu i uruchomić go ponownie.

1. Zatrzymaj podczerwę w pierwotnym regionie.

2. Wywołanie następującego polecenia w programie PowerShell, aby zaktualizować iR z nowymi ustawieniami.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Aby uzyskać więcej informacji na temat tego polecenia programu PowerShell, zobacz [Tworzenie środowiska wykonawczego integracji platformy Azure-SSIS w usłudze Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Uruchom iR ponownie.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenariusz 3 — dołączanie istniejącego katalogu SSISDB (SSIS) do nowego identyfikatora IR usługi Azure-SSIS

Gdy awaria usługi ADF lub Azure-SSIS IR występuje w bieżącym regionie, można sprawić, że twój SSISDB będzie nadal pracował z nową usługą Azure-SSIS IR w nowym regionie.

### <a name="prerequisites"></a>Wymagania wstępne

- Jeśli używasz sieci wirtualnej w bieżącym regionie, należy użyć innej sieci wirtualnej w nowym regionie, aby połączyć środowisko uruchomieniowe integracji azure-SSIS. Aby uzyskać więcej informacji, zobacz [Dołączanie środowiska wykonawczego integracji platformy Azure-SSIS do sieci wirtualnej.](join-azure-ssis-integration-runtime-virtual-network.md)

- Jeśli używasz konfiguracji niestandardowej, może być konieczne przygotowanie innego identyfikatora URI sygnatury dostępu Współdzielonego dla kontenera obiektów blob, który przechowuje niestandardowy skrypt konfiguracji i skojarzone pliki, dzięki czemu nadal jest dostępny podczas awarii. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konfiguracji niestandardowej w czasie wykonywania integracji platformy Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroki

Wykonaj następujące kroki, aby przenieść usługę Azure-SSIS IR do nowego regionu.
> [!NOTE]
> Krok 3 (tworzenie IR) musi być wykonane za pośrednictwem programu PowerShell. Usługa Azure portal zgłosi błąd informujący, że SSISDB już istnieje.

1. Wykonaj procedurę składowaną, aby zaktualizować metadane w SSISDB, aby akceptować połączenia z ** \<new_data_factory_name\> ** i ** \<new_integration_runtime_name\>**.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Utwórz nową fabrykę danych o nazwie ** \<new_data_factory_name\> ** w nowym regionie. Aby uzyskać więcej informacji, zobacz Tworzenie fabryki danych.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Aby uzyskać więcej informacji na temat tego polecenia programu PowerShell, zobacz [Tworzenie fabryki danych platformy Azure przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)

3. Utwórz nowy identyfikator Azure-SSIS O nazwie ** \<new_integration_runtime_name\> ** w nowym regionie przy użyciu programu Azure PowerShell.

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

    Aby uzyskać więcej informacji na temat tego polecenia programu PowerShell, zobacz [Tworzenie środowiska wykonawczego integracji platformy Azure-SSIS w usłudze Azure Data Factory](create-azure-ssis-integration-runtime.md)

4. Uruchom iR ponownie.

## <a name="next-steps"></a>Następne kroki

Należy wziąć pod uwagę te inne opcje konfiguracji dla usługi Azure-SSIS IR:

- [Konfigurowanie środowiska wykonawczego integracji azure-SSIS w celu uzyskania wysokiej wydajności](configure-azure-ssis-integration-runtime-performance.md)

- [Dostosowywanie konfiguracji środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Aprowizuj wersję Enterprise Edition dla środowiska wykonawczego integracji azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
