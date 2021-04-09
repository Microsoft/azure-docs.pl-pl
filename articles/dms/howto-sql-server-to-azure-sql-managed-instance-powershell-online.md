---
title: 'PowerShell: Migrowanie SQL Server do wystąpienia zarządzanego SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z SQL Server do wystąpienia zarządzanego usługi Azure SQL, używając Azure PowerShell i Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697828"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Migrowanie SQL Server do wystąpienia zarządzanego SQL w trybie online za pomocą programu PowerShell & Azure Database Migration Service

W tym artykule opisano, jak przeprowadzić migrację bazy danych **Adventureworks2016** do lokalnego wystąpienia SQL Server 2005 lub nowszego do wystąpienia zarządzanego usługi Azure SQL SQL przy użyciu Microsoft Azure PowerShell. Bazy danych można migrować z wystąpienia SQL Server do wystąpienia zarządzanego SQL przy użyciu `Az.DataMigration` modułu w programie Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Utwórz grupę zasobów.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu Azure Database Migration Service.
> * Uruchom migrację online.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ten artykuł zawiera kroki związane z migracją w trybie online, ale możliwe jest również przeprowadzenie migracji do [trybu offline](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md).


## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* [SQL Server 2016 lub nowszy](https://www.microsoft.com/sql-server/sql-server-downloads) (dowolna wersja).
* Lokalna kopia bazy danych **AdventureWorks2016** , która jest dostępna do pobrania w [tym miejscu](/sql/samples/adventureworks-install-configure).
* Aby włączyć protokół TCP/IP, który jest domyślnie wyłączony z instalacją SQL Server Express. Włącz protokół TCP/IP, wykonując czynności opisane w artykule [Włączanie lub wyłączanie protokołu sieciowego serwera](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Aby skonfigurować [zaporę systemu Windows na potrzeby dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .
* Wystąpienie zarządzane SQL. Wystąpienie zarządzane SQL można utworzyć, postępując zgodnie ze szczegółowymi informacjami w artykule [Tworzenie wystąpienia zarządzanego ASQL](../azure-sql/managed-instance/instance-create-quickstart.md).
* Aby pobrać i zainstalować [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 lub nowszy.
* Microsoft Azure Virtual Network utworzony przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia Azure Database Migration Service z połączeniem lokacja-lokacja z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Zakończono ocenę lokalnej bazy danych i migracji schematu przy użyciu Data Migration Assistant, zgodnie z opisem w artykule [wykonywanie oceny SQL Server migracji](/sql/dma/dma-assesssqlonprem).
* Aby pobrać i zainstalować `Az.DataMigration` moduł (w wersji 0.7.2 lub nowszej) z Galeria programu PowerShell przy użyciu [polecenia cmdlet programu PowerShell Install-module](/powershell/module/powershellget/Install-Module).
* Aby upewnić się, że poświadczenia używane do nawiązania połączenia z wystąpieniem źródła SQL Server mają uprawnienia [serwera kontroli](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Aby upewnić się, że poświadczenia używane do nawiązania połączenia z docelowym wystąpieniem zarządzanym SQL mają uprawnienie Kontrola bazy danych dla docelowych baz danych wystąpienia zarządzanego SQL.

    > [!IMPORTANT]
    > W przypadku migracji w trybie online należy wcześniej skonfigurować poświadczenia Azure Active Directory. Aby uzyskać więcej informacji, zobacz artykuł [Używanie portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure.

Utwórz grupę zasobów za pomocą [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) polecenia.

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w regionie *Wschodnie stany USA* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>Tworzenie wystąpienia DMS

Nowe wystąpienie Azure Database Migration Service można utworzyć przy użyciu `New-AzDataMigrationService` polecenia cmdlet.
To polecenie cmdlet oczekuje następujących wymaganych parametrów:

* *Nazwa grupy zasobów platformy Azure*. Możesz użyć [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) polecenia, aby utworzyć grupę zasobów platformy Azure jak wcześniej pokazana i podać jej nazwę jako parametr.
* *Nazwa usługi*. Ciąg, który odpowiada żądanej unikatowej nazwie usługi dla Azure Database Migration Service.
* *Lokalizacja*. Określa lokalizację usługi. Określ lokalizację centrum danych platformy Azure, np. Zachodnie stany USA lub Azja Południowo-Wschodnia.
* *Jednostka SKU*. Ten parametr odnosi się do nazwy jednostki SKU DMS. Obecnie obsługiwane nazwy jednostek SKU to *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Identyfikator podsieci wirtualnej*. Za pomocą polecenia cmdlet można [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) utworzyć podsieć.

Poniższy przykład tworzy usługę o nazwie *MyDMS* w grupie zasobów *MyDMSResourceGroup* znajdującej się w regionie *Wschodnie stany USA* przy użyciu sieci wirtualnej o nazwie *MyVNET* i podsieci o nazwie Moja *podsieć*.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia Azure Database Migration Service Utwórz projekt migracji. Projekt Azure Database Migration Service wymaga informacji o połączeniu zarówno dla wystąpienia źródłowego, jak i docelowego, jak również listy baz danych, które mają być migrowane w ramach projektu.
Zdefiniuj parametry połączenia z połączeniem źródłowym i docelowym.

Poniższy skrypt definiuje szczegóły połączenia źródłowego SQL Server: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

Poniższy skrypt definiuje szczegóły połączenia wystąpienia zarządzanego SQL: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Definiowanie mapowania źródłowej i docelowej bazy danych

Podaj bazy danych do migracji w tym projekcie migracji

Poniższy skrypt mapuje źródłową bazę danych do odpowiedniej nowej bazy danych w docelowym wystąpieniu zarządzanym SQL o podanej nazwie.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

W przypadku wielu baz danych należy dodać listę baz danych do powyższego skryptu, używając następującego formatu:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>Tworzenie projektu usługi DMS

Można utworzyć projekt Azure Database Migration Service w ramach wystąpienia DMS.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Tworzenie kopii zapasowej obiektu udziału

Teraz Utwórz obiekt elementu przejęcia reprezentujący lokalny udział sieciowy SMB, do którego Azure Database Migration Service mogą pobrać kopie zapasowe źródłowej bazy danych za pomocą polecenia cmdlet New-AzDmsFileShare.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Definiowanie usługi Azure Storage 

Wybierz kontener usługi Azure Storage, który ma być używany na potrzeby migracji: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Konfigurowanie aplikacji Azure Active Directory

Podaj wymagane szczegóły dotyczące Azure Active Directory migracji wystąpienia zarządzanego SQL w trybie online: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Następnie utwórz i uruchom zadanie Azure Database Migration Service. Wywołaj źródło i cel przy użyciu zmiennych i Wyświetl listę tabel bazy danych do migracji: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

Poniższy przykład tworzy i uruchamia zadanie migracji w trybie online: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Aby uzyskać więcej informacji, zobacz polecenie [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Monitorowanie migracji

Aby monitorować migrację, należy wykonać następujące zadania.

### <a name="check-the-status-of-task"></a>Sprawdź stan zadania

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Aby uzyskać listę błędów, użyj następujących elementów:

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Wykonywanie uruchomienie produkcyjne 

W przypadku migracji w trybie online wykonywana jest pełna kopia zapasowa i przywracanie baz danych, a następnie działa kontynuując Przywracanie dzienników transakcji przechowywanych w BackupFileShare.

Gdy baza danych w wystąpieniu zarządzanym Azure SQL jest aktualizowana przy użyciu najnowszych danych i jest synchronizowana ze źródłową bazą danych, można wykonać uruchomienie produkcyjne.

W poniższym przykładzie zostanie wykonane cutover\migration. Użytkownicy wywołują to polecenie według własnego uznania.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Usuwanie wystąpienia Azure Database Migration Service

Po zakończeniu migracji można usunąć wystąpienie Azure Database Migration Service:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać informacje na temat dodatkowych scenariuszy migracji (par Source/Target), zobacz [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Azure Database Migration Service w artykule [co to jest Azure Database Migration Service?](./dms-overview.md).