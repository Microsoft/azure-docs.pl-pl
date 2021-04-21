---
title: 'PowerShell: uruchamianie migracji w trybie offline z bazy danych MySQL do bazy Azure Database for MySQL przy użyciu usługi DMS'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak migrować lokalną bazę danych MySQL do usługi Azure Database for MySQL przy użyciu Azure Database Migration Service za pomocą skryptu programu PowerShell.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 424443288dddcb09d15b7d00ffe9a2840f602959
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819786"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>Migrowanie programu MySQL do trybu Azure Database for MySQL offline przy użyciu programu PowerShell & Azure Database Migration Service

W tym artykule zmigrujesz bazę danych MySQL przywróconą do wystąpienia lokalnego do usługi Azure Database for MySQL przy użyciu możliwości migracji w trybie offline programu Azure Database Migration Service za pośrednictwem Microsoft Azure PowerShell. Ten artykuł dokumentuje kolekcję skryptów programu PowerShell, które mogą być wykonywane kolejno w celu przeprowadzenia migracji bazy danych MySQL w trybie offline na platformę Azure.

> [!NOTE]
> Obecnie nie można uruchomić pełnej migracji bazy danych przy użyciu modułu Az.DataMigration. W międzyczasie przykładowy skrypt programu PowerShell jest dostarczany w stanie "w stanie rzeczywistym", który korzysta z interfejsu API REST usługi [DMS](https://docs.microsoft.com/rest/api/datamigration/tasks/get) i umożliwia zautomatyzowanie migracji. Ten skrypt zostanie zmodyfikowany lub przestarzały, po dodaniu oficjalnej obsługi w module Az.DataMigration i interfejsie wiersza polecenia platformy Azure. 

> [!IMPORTANT]
> Scenariusz migracji online "MySQL do Azure Database for MySQL" został zastąpiony równoległym, wysoce wydajnym scenariuszem migracji offline z 1 czerwca 2021 r. W przypadku migracji online możesz użyć tej nowej oferty wraz z [replikacją typu data-in.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Alternatywnie możesz użyć narzędzi typu open source, takich jak [MyDumper/MyLoader,](https://centminmod.com/mydumper.html) z replikacją typu data-in na użytek migracji online. 

Ten artykuł pomaga zautomatyzować scenariusz, w którym nazwy źródłowej i docelowej bazy danych mogą być takie same lub różne. W ramach migracji należy zmigrować wszystkie lub kilka tabel w docelowej bazie danych, które mają taką samą nazwę i strukturę tabel. Chociaż w artykułach przyjęto założenie, że źródłem jest wystąpienie bazy danych MySQL i obiektem docelowym Azure Database for MySQL, można go użyć do migracji z jednego serwera Azure Database for MySQL do innego tylko przez zmianę nazwy i poświadczeń serwera źródłowego. Ponadto obsługiwana jest również migracja z serwerów MySQL w niższej wersji (wersja 5.6 lub nowsza) do wyższych wersji.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Migrowanie schematu bazy danych.
> * Utwórz grupę zasobów.
> * Tworzenie wystąpienia usługi Azure Database Migration Service.
> * Utwórz projekt migracji w Azure Database Migration Service wystąpienia.
> * Skonfiguruj projekt migracji do używania funkcji migracji w trybie offline dla programu MySQL.
> * Uruchamianie migracji.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tych kroków potrzebne są:

* Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free).
* Lokalna baza danych MySQL w wersji 5.6 lub nowsza. Jeśli tak nie jest, pobierz i zainstaluj [program MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 lub jego więcej.
* [Utwórz wystąpienie usługi Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Zapoznaj się z artykułem [Use MySQL Workbench to](../mysql/connect-workbench.md) connect and query data (Nawiązywanie połączeń z danymi i wykonywanie na nich zapytań), aby uzyskać szczegółowe informacje na temat nawiązywania połączenia i tworzenia bazy danych przy użyciu aplikacji Workbench. Wersja Azure Database for MySQL powinna być równa lub wyższa niż wersja lokalnego programu MySQL. Na przykład system MySQL 5.7 można migrować do wersji Azure Database for MySQL 5.7 lub uaktualnić do wersji 8.  
* Utwórz Microsoft Azure Virtual Network dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność lokacja-lokacja z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub sieci [VPN.](../vpn-gateway/vpn-gateway-about-vpngateways.md) Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz dokumentację usługi [Virtual Network, a](../virtual-network/index.yml)zwłaszcza artykuły Szybki start ze szczegółami krok po kroku.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z sieciową [](../virtual-network/virtual-network-service-endpoints-overview.md) komunikacji równorzędnej z firmą Microsoft, dodaj punkt końcowy usługi *Microsoft.Sql* do podsieci, w której zostanie aprowizowana usługa. Ta konfiguracja jest niezbędna, Azure Database Migration Service nie ma łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieci wirtualnej nie blokują portu wychodzącego 443 tagu usługi ServiceTag dla magazynu i usługi AzureMonitor. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł Filtrowanie ruchu [sieciowego przy użyciu sieciowych grup zabezpieczeń.](../virtual-network/virtual-network-vnet-plan-design-arm.md)
* Otwórz zaporę systemu Windows, aby zezwolić na połączenia z usługi Virtual Network for Azure Database Migration Service na dostęp do źródłowego serwera MySQL Server, który domyślnie jest portem TCP 3306.
* W przypadku korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby zezwolić na połączenia z usługi Virtual Network dla programu Azure Database Migration Service w celu uzyskania dostępu do źródłowych baz danych na potrzeby migracji.
* Utwórz regułę zapory na [](../mysql/howto-manage-vnet-using-portal.md) [poziomie](../azure-sql/database/firewall-configure.md) serwera lub skonfiguruj punkty końcowe usługi sieci wirtualnej dla serwerów docelowych Azure Database for MySQL aby umożliwić Virtual Network dostępu Azure Database Migration Service do docelowych baz danych.
* Źródłowy serwer MySQL musi znajdować się w obsługiwanej bazie danych MySQL Community Edition. Aby określić wersję instancji programu MySQL, w narzędziu MySQL lub środowisku roboczym bazy danych MySQL uruchom następujące polecenie:

    ```
    SELECT @@version;
    ```

* Usługa Azure Database for MySQL obsługuje wyłącznie tabele InnoDB. Aby przekonwertować tabele MyISAM na format InnoDB, zapoznaj się z artykułem [Konwertowanie tabel z formatu MyISAM na InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)
* Użytkownik musi mieć uprawnienia do odczytu danych w źródłowej bazie danych.
* Przewodnik używa programu PowerShell w wersji 7.1 z programem PSEdition Core, który można zainstalować zgodnie z [przewodnikiem instalacji](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)
* Pobierz i zainstaluj następujące moduły z Galeria programu PowerShell polecenia [cmdlet programu PowerShell Install-Module;](/powershell/module/powershellget/Install-Module) Pamiętaj, aby otworzyć okno poleceń programu PowerShell przy użyciu polecenia Uruchom jako administrator:
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Migrowanie schematu bazy danych

Aby przenieść wszystkie obiekty bazy danych, takie jak schematy tabel, indeksy i procedury składowane, musimy wyodrębnić schemat ze źródłowej bazy danych i zastosować go do docelowej bazy danych. Aby wyodrębnić schemat, użyj polecenia mysqldump z parametrem `--no-data`. Do tego celu potrzebna jest maszyna, która może łączyć się zarówno ze źródłową bazą danych MySQL, jak i docelową bazą Azure Database for MySQL.

Aby wyeksportować schemat przy użyciu narzędzia mysqldump, uruchom następujące polecenie:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Na przykład:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Aby zaimportować schemat do Azure Database for MySQL docelowego, uruchom następujące polecenie:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Na przykład:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Jeśli schemat zawiera klucze obce, równoległe ładowanie danych podczas migracji będzie obsługiwane przez zadanie migracji. Podczas migracji schematu nie trzeba popuszczać kluczy obcych.

Jeśli w bazie danych masz wyzwalacze, wymusi to integralność danych w celu przed pełną migracją danych ze źródła. Zaleca się wyłączenie wyzwalaczy we wszystkich tabelach w docelowej tabeli podczas migracji, a następnie włączenie wyzwalaczy po zakończeniu migracji.

Wykonaj następujący skrypt w aplikacji MySQL Workbench w docelowej bazie danych, aby wyodrębnić skrypt wyzwalacza upuszczania i dodać skrypt wyzwalacza.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Uruchom wygenerowane zapytanie wyzwalacza upuszczania (kolumnę DropQuery) w wyniku, aby usunąć wyzwalacze w docelowej bazie danych. Można zapisać zapytanie dodawania wyzwalacza, które będzie używane po zakończeniu migracji danych.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji Microsoft Azure subskrypcji

Użyj polecenia [Connect-AzAccount programu PowerShell,](/powershell/module/az.accounts/connect-azaccount) aby zalogować się do subskrypcji platformy Azure przy użyciu programu PowerShell zgodnie z instrukcjami w artykule Logowanie za pomocą [Azure PowerShell](/powershell/azure/authenticate-azureps).

Poniższy skrypt ustawia domyślną subskrypcję sesji programu PowerShell po zalogowaniu i tworzy funkcję rejestrowania pomocnika dla sformatowanych dzienników konsoli.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

Rejestrację dostawcy zasobów należy wykonać w każdej subskrypcji platformy Azure tylko raz. Bez rejestracji nie będzie można utworzyć wystąpienia klasy **Azure Database Migration Service**.

Zarejestruj dostawcę zasobów przy użyciu [polecenia Register-AzResourceProvider.](/powershell/module/az.resources/register-azresourceprovider) Poniższy skrypt rejestruje dostawcę zasobów wymaganego do **Azure Database Migration Service**

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów przed utworzeniem jakichkolwiek zasobów dms.

Utwórz grupę zasobów za pomocą [polecenia New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup)

Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w regionie Zachodnie stany USA *2* w ramach subskrypcji domyślnej *mySubscription*.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

Możesz utworzyć nowe wystąpienie usługi Azure Database Migration Service za pomocą polecenia [New-AzDataMigrationService.](/powershell/module/az.datamigration/new-azdatamigrationservice) To polecenie oczekuje następujących wymaganych parametrów:
* *Nazwa grupy zasobów platformy Azure.* Możesz użyć polecenia [New-AzResourceGroup,](/powershell/module/az.resources/new-azresourcegroup) aby utworzyć grupę zasobów platformy Azure, jak pokazano wcześniej, i podać jej nazwę jako parametr.
* *Nazwa usługi*. Ciąg odpowiadający żądanej unikatowej nazwie usługi dla Azure Database Migration Service 
* *Lokalizacja*. Określa lokalizację usługi. Określanie lokalizacji centrum danych platformy Azure, na przykład Zachodnie stany USA lub Azja Południowo-Wschodnia
* *Sku*. Ten parametr odpowiada nazwie sku dms. Obecnie obsługiwana nazwa SKU to *Standard_1vCore*, *Standard_2vCores*, *Standard_4vCores*, *Premium_4vCores*.
* *Identyfikator podsieci wirtualnej*. Aby uzyskać informacje o podsieci, możesz użyć polecenia [Get-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) 

Poniższy skrypt oczekuje, że sieć wirtualna *myVirtualNetwork* istnieje z podsiecią o nazwie *default,* a następnie tworzy sieć Database Migration Service o nazwie *myDmService* w grupie zasobów utworzonej w kroku **3** i w tym samym regionie.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu Azure Database Migration Service utworzysz projekt migracji. Projekt migracji określa typ migracji, który należy wykonać.

Poniższy skrypt tworzy projekt migracji o nazwie *myfirstmysqlofflineproject* do migracji offline z programu MySQL do programu Azure Database for MySQL w ramach wystąpienia programu Database Migration Service utworzonego w kroku **4** i w tym samym regionie.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Tworzenie obiektu informacji o połączeniu z bazą danych dla połączeń źródłowych i docelowych

Po utworzeniu projektu migracji utworzysz informacje o połączeniu z bazą danych. Te informacje o połączeniu będą używane do nawiązywania połączenia z serwerami źródłowymi i docelowymi podczas procesu migracji.

Poniższy skrypt pobiera nazwę serwera, nazwę użytkownika i hasło dla wystąpień źródłowych i docelowych programu MySQL oraz tworzy obiekty informacji o połączeniu. Skrypt monituje użytkownika o wprowadzenie hasła dla wystąpień źródłowych i docelowych mySQL. W przypadku skryptów dyskretnych poświadczenia można pobrać z Azure Key Vault. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>Wyodrębnianie listy nazw tabel z docelowej bazy danych

Listę tabel bazy danych można wyodrębnić przy użyciu zadania migracji i informacji o połączeniu. Lista tabel zostanie wyodrębniona zarówno ze źródłowej bazy danych, jak i docelowej bazy danych, aby można było wykonać odpowiednie mapowanie i walidację. 

Poniższy skrypt pobiera nazwy źródłowych i docelowych baz danych, a następnie wyodrębnia listę tabel z baz danych przy użyciu zadania migracji *GetUserTablesMySql.* 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Mapowanie tabeli kompilacji na podstawie konfiguracji użytkownika

W ramach konfigurowania zadania migracji utworzysz mapowanie między tabelami źródłowymi i docelowymi. Mapowanie jest na poziomie nazwy tabeli, ale zakłada się, że struktura tabeli (liczba kolumn, nazwy kolumn, typy danych itp.) mapowanych tabel jest dokładnie taka sama.

Poniższy skrypt tworzy mapowanie na podstawie listy tabeli docelowej i źródłowej wyodrębnione w **kroku 7.** W przypadku częściowego ładowania danych użytkownik może podać listę tabel, aby odfiltrować tabele. Jeśli nie podano żadnych danych wejściowych użytkownika, wszystkie tabele docelowe są mapowane. Skrypt sprawdza również, czy tabela o tej samej nazwie istnieje w źródle. Jeśli nazwa tabeli nie istnieje w źródle, tabela docelowa jest ignorowana w przypadku migracji. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>Tworzenie i konfigurowanie danych wejściowych zadania migracji

Po utworzeniu mapowania tabeli utworzysz dane wejściowe zadania migracji typu *Migrate.MySql.AzureDbForMySql* i skonfigurujesz właściwości.

Poniższy skrypt tworzy zadanie migracji i ustawia połączenia, nazwy baz danych i mapowanie tabel.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Konfigurowanie parametrów dostrajania wydajności

Zgodnie z modułem programu PowerShell dostępnych jest kilka opcjonalnych parametrów, które można dostroić na podstawie środowiska. Te parametry mogą służyć do poprawy wydajności zadania migracji. Wszystkie te parametry są opcjonalne, a ich wartość domyślna to NULL.

> [!NOTE]
> Następujące konfiguracje wydajności wykazały zwiększenie przepływności podczas migracji w ramach jednostki SKU Premium.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 sekund
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

Poniższy skrypt pobiera wartości użytkownika parametrów i ustawia parametry we właściwościach zadania migracji.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Po skonfigurowaniu danych wejściowych zadania zadanie zostanie utworzone i wykonane na agencie. Skrypt wyzwala wykonanie zadania i czeka na ukończenie migracji.

Poniższy skrypt wywołuje skonfigurowane zadanie migracji i czeka na jego ukończenie.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Usuwanie Database Migration Service

Ta sama Database Migration Service może być używana do wielu migracji, dzięki czemu utworzone wystąpienie może zostać ponownie użyte. Jeśli nie zamierzasz nadal używać usługi Database Migration Service, możesz usunąć usługę za pomocą polecenia [Remove-AzDataMigrationService.](/powershell/module/az.datamigration/remove-azdatamigrationservice?)

Poniższy skrypt usuwa wystąpienie Azure Database Migration Service i skojarzone z nim projekty.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o znanych problemach i ograniczeniach podczas przeprowadzania migracji przy użyciu usługi DMS, zobacz artykuł Typowe problemy [— Azure Database Migration Service](./known-issues-troubleshooting-dms.md).
* Aby rozwiązać problemy z łącznością ze źródłową bazą danych podczas korzystania z usługi DMS, zobacz artykuł Issues connecting source databases (Problemy z łączeniu [źródłowych baz danych).](./known-issues-troubleshooting-dms-source-connectivity.md)
* Aby uzyskać informacje Azure Database Migration Service, zobacz artykuł [Co to jest Azure Database Migration Service?](./dms-overview.md).
* Aby uzyskać informacje Azure Database for MySQL, zobacz artykuł [Co to jest Azure Database for MySQL?](../mysql/overview.md).
* Aby uzyskać samouczek dotyczący korzystania z usługi DMS za pośrednictwem portalu, zobacz artykuł Samouczek: migrowanie programu [MySQL](./tutorial-mysql-azure-mysql-offline-portal.md) do usługi Azure Database for MySQL offline przy użyciu usługi DMS