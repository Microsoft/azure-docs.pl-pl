---
title: Konfigurowanie Azure-SSIS Integration Runtime przy użyciu programu PowerShell
description: Dowiedz się, jak skonfigurować Azure-SSIS Integration Runtime w Azure Data Factory za pomocą programu PowerShell, dzięki czemu możesz wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.
ms.service: data-factory
ms.devlang: powershell
ms.topic: tutorial
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: a0a6e115d2b76ab3f060ee1bcb197da58f2c8fa6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391290"
---
# <a name="set-up-an-azure-ssis-ir-in-azure-data-factory-by-using-powershell"></a>Konfigurowanie Azure-SSIS IR w Azure Data Factory przy użyciu programu PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten samouczek zawiera instrukcje dotyczące korzystania z programu PowerShell w celu aprowizacji Integration Runtime platformy Azure — SQL Server Integration Services (SSIS) w Azure Data Factory (ADF). Azure-SSIS IR obsługuje:

- Uruchomione pakiety wdrożone do wykazu usług SSIS (SSISDB) hostowane przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu)
- Uruchomione pakiety wdrożone w systemie plików, Azure Files lub SQL Server Database (MSDB) hostowane przez wystąpienie zarządzane Azure SQL (model wdrażania pakietów)

Po zainicjowaniu Azure-SSIS IR można używać dobrze znanych narzędzi do wdrażania i uruchamiania pakietów na platformie Azure. Te narzędzia już obsługują platformę Azure i obejmują SQL Server narzędzia do obsługi danych (SSDT), SQL Server Management Studio (SSMS) i narzędzia wiersza polecenia, takie jak [dtutil](/sql/integration-services/dtutil-utility) i [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Aby uzyskać informacje koncepcyjne dotyczące środowisk Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

> [!NOTE]
> W tym artykule pokazano, jak skonfigurować Azure-SSIS IR za pomocą Azure PowerShell. Aby skonfigurować Azure-SSIS IR za pomocą aplikacji Azure Portal lub Azure Data Factory, zobacz [Samouczek: konfigurowanie Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md). 

W tym samouczku wykonasz następujące czynności:
> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz Azure-SSIS Integration Runtime.
> * Uruchom Azure-SSIS Integration Runtime.
> * Przejrzyj kompletny skrypt.
> * Wdróż pakiety usług SSIS.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

- **Serwer Azure SQL Database lub wystąpienie zarządzane (opcjonalnie)**. Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Data Factory spowoduje utworzenie wystąpienia SSISDB na tym serwerze bazy danych. 

  Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure.

  Należy pamiętać o następujących kwestiach:

  - Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych w ramach puli elastycznej lub w wystąpieniu zarządzanym. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie SQL Database i wystąpienia zarządzanego SQL](create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Jeśli używasz serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub potrzebujesz dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, musisz dołączyć Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR w sieci wirtualnej](./create-azure-ssis-integration-runtime.md).

  - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To ustawienie nie ma zastosowania w przypadku używania serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB. Aby uzyskać więcej informacji, zobacz [bezpieczny Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, do listy adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../azure-sql/database/firewall-configure.md).

  - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub przy użyciu uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych. Dla tych ostatnich należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR przy użyciu uwierzytelniania usługi Azure AD](./create-azure-ssis-integration-runtime.md).

  - Upewnij się, że serwer bazy danych nie ma już wystąpienia SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącego wystąpienia SSISDB.

- Zainstalowanie programu **Azure PowerShell**. Aby uruchomić skrypt programu PowerShell w celu skonfigurowania Azure-SSIS IR, postępuj zgodnie z instrukcjami podanymi w temacie [Instalowanie i konfigurowanie Azure PowerShell](/powershell/azure/install-Az-ps).

> [!NOTE]
> Listę regionów świadczenia usługi Azure, w których Azure Data Factory i Azure-SSIS IR są obecnie dostępne, można znaleźć w temacie [Azure Data Factory i Azure-SSIS IR dostępność według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="open-the-windows-powershell-ise"></a>Otwórz Windows PowerShell ISE

Otwórz środowisko Windows PowerShell Integrated Scripting Environment (ISE) z uprawnieniami administratora. 

## <a name="create-variables"></a>Tworzenie zmiennych

Skopiuj następujący skrypt do ISE. Określ wartości zmiennych. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character (for example, "$"), precede it with the escape character "`" (for example, "`$")
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info; this is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, although Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

## <a name="sign-in-and-select-your-subscription"></a>Zaloguj się i wybierz swoją subskrypcję

Aby zalogować się i wybrać subskrypcję platformy Azure, Dodaj następujący kod do skryptu:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-your-database-server"></a>Weryfikowanie połączenia z serwerem bazy danych

Aby sprawdzić poprawność połączenia, Dodaj następujący skrypt: 

```powershell
# Validate only if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Aby utworzyć wystąpienie Azure SQL Database jako część skryptu, zapoznaj się z poniższym przykładem. Ustaw wartości dla zmiennych, które nie zostały jeszcze zdefiniowane (na przykład SSISDBServerName, FirewallIPAddress). 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów jest kontenerem logicznym, do którego zasoby platformy Azure są wdrażane i zarządzane jako Grupa.

Jeśli grupa zasobów już istnieje, nie kopiuj tego kodu do skryptu. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Uruchom następujące polecenie, aby utworzyć fabrykę danych:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie Azure-SSIS Integration Runtime

Aby utworzyć Azure-SSIS Integration Runtime, w których są uruchamiane pakiety usług SSIS na platformie Azure, uruchom następujące polecenia. Jeśli nie używasz SSISDB, możesz pominąć parametry CatalogServerEndpoint, CatalogPricingTier i CatalogAdminCredential.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
```

## <a name="start-the-azure-ssis-integration-runtime"></a>Uruchom Azure-SSIS Integration Runtime

Aby uruchomić Azure-SSIS IR, uruchom następujące polecenia:

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut.
>
> Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. 
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać więcej informacji na temat wbudowanych/preinstalowanych składników, zobacz [składniki wbudowane/preinstalowane na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Aby uzyskać więcej informacji o dodatkowych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Pełny skrypt

Skrypt programu PowerShell w tej sekcji służy do konfigurowania wystąpienia Azure-SSIS IR, na którym są uruchamiane pakiety usług SSIS. Po pomyślnym uruchomieniu tego skryptu możesz wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.

1. Otwórz ISE.
2. W wierszu polecenia ISE Uruchom następujące polecenie:  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Skopiuj skrypt programu PowerShell z tej sekcji do ISE.
4. Podaj odpowiednie wartości dla wszystkich parametrów na początku skryptu.
5. Uruchom skrypt. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS Integration Runtime info - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x the number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

### SSISDB info
$SSISDBServerEndpoint = "[your server name.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you're not using SSISDB]" # WARNING: If you want to use SSISDB, ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you're using SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier `
        -CatalogAdminCredential $serverCreds
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-your-azure-ssis-ir"></a>Monitoruj Azure-SSIS IR i zarządzaj nimi

Aby uzyskać informacje na temat monitorowania Azure-SSIS IR i zarządzania nim, zobacz: 

- [Monitoruj Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Zarządzanie Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi SSDT lub SSMS z obsługą platformy Azure. Narzędzia te łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera: 

- W przypadku serwera Azure SQL Database format punktu końcowego serwera to `<server name>.database.windows.net` .
- W przypadku wystąpienia zarządzanego z prywatnym punktem końcowym format punktu końcowego serwera to `<server name>.<dns prefix>.database.windows.net` .
- W przypadku wystąpienia zarządzanego z publicznym punktem końcowym format punktu końcowego serwera to `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Jeśli nie korzystasz z SSISDB, możesz wdrożyć pakiety w systemie plików, Azure Files lub MSDB, hostowanym przez wystąpienie zarządzane usługi Azure SQL i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi wiersza polecenia [dtutil](/sql/integration-services/dtutil-utility) i [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) . 

Aby uzyskać więcej informacji, zobacz [wdrażanie projektów SSIS/pakietów](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonywania pakietu SSIS w potokach Data Factory. Aby uzyskać więcej informacji, zobacz [wywołania wykonywania pakietu SSIS jako działania pierwszej klasy Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

Aby uzyskać więcej dokumentacji usług SSIS, zobacz: 

- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Zaplanuj wykonywanie pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności: 

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Utwórz Azure-SSIS Integration Runtime.
> * Uruchom Azure-SSIS Integration Runtime.
> * Przejrzyj kompletny skrypt.
> * Wdróż pakiety usług SSIS.

Aby dowiedzieć się więcej o dostosowywaniu Azure-SSIS Integration Runtime, zobacz następujący artykuł:

> [!div class="nextstepaction"]
>[Dostosowywanie Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)