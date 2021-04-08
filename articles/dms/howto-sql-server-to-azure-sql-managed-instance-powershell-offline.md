---
title: 'PowerShell: Migrowanie SQL Server do wystąpienia zarządzanego SQL w trybie offline'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację w trybie offline z SQL Server do wystąpienia zarządzanego usługi Azure SQL, używając Azure PowerShell i Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697852"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>Migrowanie SQL Server do wystąpienia zarządzanego SQL w trybie offline za pomocą programu PowerShell & Azure Database Migration Service

W tym artykule przeprowadzisz migrację bazy danych **Adventureworks2016** do lokalnego wystąpienia SQL Server 2005 lub nowszego do wystąpienia zarządzanego usługi Azure SQL SQL przy użyciu Microsoft Azure PowerShell. Bazy danych można migrować z wystąpienia SQL Server do wystąpienia zarządzanego SQL przy użyciu `Az.DataMigration` modułu w programie Microsoft Azure PowerShell.

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Utwórz grupę zasobów.
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji w wystąpieniu Azure Database Migration Service.
> * Uruchom migrację w trybie offline.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ten artykuł zawiera instrukcje dotyczące migracji w trybie offline, ale jest również możliwe przeprowadzenie migracji do [trybu online](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md).


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


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Zaloguj się do subskrypcji Microsoft Azure

Zaloguj się do subskrypcji platformy Azure za pomocą programu PowerShell. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure.

Utwórz grupę zasobów za pomocą [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) polecenia.

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w regionie *Wschodnie stany USA* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Utwórz obiekt informacji o połączeniu z bazą danych dla połączeń źródłowych i docelowych

Obiekt informacji o połączeniu z bazą danych można utworzyć za pomocą `New-AzDmsConnInfo` polecenia cmdlet, które oczekuje następujących parametrów:

* *Serwertype*. Typ żądanego połączenia z bazą danych, na przykład SQL, Oracle lub MySQL. Użyj programu SQL dla SQL Server i usługi Azure SQL.
* *Źródło danych*. Nazwa lub adres IP wystąpienia SQL Server lub wystąpienia Azure SQL Database.
* *AuthType*. Typ uwierzytelniania dla połączenia, który może mieć wartość sqlauthentication lub WindowsAuthentication.
* *TrustServerCertificate*. Ten parametr ustawia wartość wskazującą, czy kanał jest szyfrowany podczas pomijania przechodzenia łańcucha certyfikatów w celu zweryfikowania zaufania. Wartość może być `$true` lub `$false` .

Poniższy przykład tworzy obiekt informacji o połączeniu dla źródła SQL Server o nazwie *MySourceSQLServer* przy użyciu uwierzytelniania SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

W następnym przykładzie pokazano tworzenie informacji o połączeniu dla wystąpienia zarządzanego Azure SQL o nazwie "targetmanagedinstance":

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Podaj bazy danych dla projektu migracji

Utwórz listę `AzDataMigrationDatabaseInfo` obiektów, które określają bazy danych jako część projektu Azure Database Migration Service, który można podać jako parametr do tworzenia projektu. Możesz użyć polecenia cmdlet, `New-AzDataMigrationDatabaseInfo` Aby utworzyć `AzDataMigrationDatabaseInfo` .

Poniższy przykład tworzy `AzDataMigrationDatabaseInfo` projekt dla bazy danych **AdventureWorks2016** i dodaje ją do listy, która ma zostać dostarczona jako parametr do tworzenia projektu.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Utwórz obiekt projektu

Na koniec można utworzyć projekt Azure Database Migration Service o nazwie *MyDMSProject* znajdujący się w *regionie Wschodnie stany USA* przy użyciu `New-AzDataMigrationProject` i dodać wcześniej utworzone połączenie źródłowe i docelowe oraz listę baz danych do migracji.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Następnie utwórz i uruchom zadanie Azure Database Migration Service. To zadanie wymaga informacji o poświadczeniach połączenia zarówno dla źródła, jak i celu, jak również listy tabel bazy danych do migracji i informacji już dostarczonych z projektem utworzonym jako warunek wstępny.

### <a name="create-credential-parameters-for-source-and-target"></a>Utwórz parametry poświadczeń dla źródła i celu

Utwórz poświadczenia zabezpieczeń połączenia jako obiekt [PSCredential](/dotnet/api/system.management.automation.pscredential) .

W poniższym przykładzie pokazano Tworzenie obiektów *PSCredential* dla połączeń źródłowych i docelowych, dostarczając hasła jako zmienne ciągów *$sourcePassword* i *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Tworzenie kopii zapasowej obiektu udziału

Teraz należy utworzyć obiekt udziału plików reprezentujący lokalny udział sieciowy SMB, do którego Azure Database Migration Service mogą pobrać kopie zapasowe źródłowej bazy danych przy użyciu `New-AzDmsFileShare` polecenia cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Utwórz wybrany obiekt bazy danych

Następnym krokiem jest wybranie źródłowej i docelowej bazy danych przy użyciu `New-AzDmsSelectedDB` polecenia cmdlet.

Poniższy przykład dotyczy migrowania pojedynczej bazy danych z SQL Server do wystąpienia zarządzanego usługi Azure SQL:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Jeśli całe wystąpienie SQL Server wymaga podnoszenia i przełączenia do wystąpienia zarządzanego usługi Azure SQL, poniżej podano pętlę dotyczącą wszystkich baz danych ze źródła. W poniższym przykładzie dla $Server, $SourceUserName i $SourcePassword Podaj szczegóły źródła SQL Server.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Identyfikator URI sygnatury dostępu współdzielonego dla kontenera usługi Azure Storage

Utwórz zmienną zawierającą identyfikator URI sygnatury dostępu współdzielonego, który zapewnia Azure Database Migration Service z dostępem do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure Database Migration Service nie obsługuje korzystania z tokenu SAS na poziomie konta. Musisz użyć identyfikatora URI sygnatury dostępu współdzielonego dla kontenera konta magazynu. [Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego dla kontenera obiektów blob](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Dodatkowe wymagania dotyczące konfiguracji

Istnieje kilka dodatkowych wymagań, które należy spełnić:


* **Wybierz pozycję logowania**. Utwórz listę logowań do migracji, jak pokazano w następującym przykładzie:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Obecnie Azure Database Migration Service obsługuje tylko Migrowanie nazw logowania SQL.

* **Wybierz zadania agenta**. Utwórz listę zadań agentów do migracji, jak pokazano w następującym przykładzie:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Obecnie Azure Database Migration Service obsługuje tylko zadania z podsystemem.



### <a name="create-and-start-the-migration-task"></a>Utwórz i uruchom zadanie migracji

Użyj `New-AzDataMigrationTask` polecenia cmdlet, aby utworzyć i uruchomić zadanie migracji.

#### <a name="specify-parameters"></a>Określ parametry

`New-AzDataMigrationTask`Polecenie cmdlet oczekuje następujących parametrów:

* *TaskType*. Typ zadania migracji, które ma zostać utworzone dla SQL Server do migracji wystąpienia zarządzanego usługi Azure SQL *MigrateSqlServerSqlDbMi* jest oczekiwany. 
* *Nazwa grupy zasobów*. Nazwa grupy zasobów platformy Azure, w której ma zostać utworzone zadanie.
* *Nazwa ServiceName*. Wystąpienie Azure Database Migration Service, w którym ma zostać utworzone zadanie.
* *ProjectName*. Nazwa projektu Azure Database Migration Service, w którym ma zostać utworzone zadanie. 
* *Zadaniename*. Nazwa zadania do utworzenia. 
* *SourceConnection*. Obiekt AzDmsConnInfo reprezentujący połączenie SQL Server źródłowej.
* *TargetConnection*. Obiekt AzDmsConnInfo reprezentujący docelowe połączenie wystąpienia zarządzanego Azure SQL.
* *SourceCred*. Obiekt [PSCredential](/dotnet/api/system.management.automation.pscredential) do nawiązywania połączenia z serwerem źródłowym.
* *TargetCred*. Obiekt [PSCredential](/dotnet/api/system.management.automation.pscredential) do nawiązywania połączenia z serwerem docelowym.
* *SelectedDatabase*. Obiekt AzDataMigrationSelectedDB reprezentujący mapowanie źródłowe i docelowe bazy danych.
* *BackupFileShare*. Obiekt dataudziału reprezentujący udział sieciowy lokalny, do którego Azure Database Migration Service mogą pobrać kopie zapasowe źródłowej bazy danych.
* *BackupBlobSasUri*. Identyfikator URI sygnatury dostępu współdzielonego, który zapewnia Azure Database Migration Service z dostępem do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej. Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego dla kontenera obiektów blob.
* *SelectedLogins*. Lista wybranych logowań do migracji.
* *SelectedAgentJobs*. Lista wybranych zadań agenta do migracji.
* *SelectedLogins*. Lista wybranych logowań do migracji.
* *SelectedAgentJobs*. Lista wybranych zadań agenta do migracji.



#### <a name="create-and-start-a-migration-task"></a>Tworzenie i uruchamianie zadania migracji

Poniższy przykład tworzy i uruchamia zadanie migracji w trybie offline o nazwie **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```


## <a name="monitor-the-migration"></a>Monitorowanie migracji

Aby monitorować migrację, należy wykonać następujące zadania.

1. Konsoliduj wszystkie szczegóły migracji do zmiennej o nazwie $CheckTask.

    Aby połączyć szczegóły migracji, takie jak właściwości, stan i informacje o bazie danych skojarzone z migracją, należy użyć następującego fragmentu kodu:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Użyj `$CheckTask` zmiennej, aby uzyskać bieżący stan zadania migracji.

    Aby użyć `$CheckTask` zmiennej do uzyskania bieżącego stanu zadania migracji, można monitorować uruchomione zadanie migracji, wykonując zapytania o Właściwość State zadania, jak pokazano w następującym przykładzie:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```


## <a name="delete-the-instance-of-azure-database-migration-service"></a>Usuń wystąpienie Azure Database Migration Service

Po zakończeniu migracji można usunąć wystąpienie Azure Database Migration Service:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Azure Database Migration Service w artykule [co to jest Azure Database Migration Service?](./dms-overview.md).

Aby uzyskać informacje na temat dodatkowych scenariuszy migracji (par Source/Target), zobacz [Przewodnik po migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.