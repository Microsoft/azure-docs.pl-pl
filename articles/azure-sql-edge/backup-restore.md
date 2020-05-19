---
title: Tworzenie kopii zapasowych i przywracanie baz danych — Azure SQL Edge (wersja zapoznawcza)
description: Informacje o możliwościach tworzenia kopii zapasowych i przywracania w usłudze Azure SQL Edge (wersja zapoznawcza)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5c7bdbc49d8f1c6af7f38911919c660b03e1a37a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596953"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Tworzenie kopii zapasowych i przywracanie baz danych w usłudze Azure SQL Edge (wersja zapoznawcza) 

Usługa Azure SQL Edge jest oparta na najnowszych wersjach aparatu bazy danych Microsoft SQL Server w systemie Linux, oferując podobne możliwości tworzenia kopii zapasowych i przywracania baz danych, jak te dostępne w SQL Server on Linux i SQL Server działające w kontenerach. Składnik kopia zapasowa i przywracanie zapewnia podstawowe zabezpieczenia dotyczące ochrony danych przechowywanych w bazach danych usługi Azure SQL Edge. Aby zminimalizować ryzyko utraty danych, zaleca się okresowe tworzenie kopii zapasowych baz danych w celu regularnego zachowywania zmian w danych. Dobrze zaplanowana strategii tworzenia kopii zapasowej i przywracania pomaga chronić bazy danych przed utratą danych spowodowaną przez różne błędy. Przetestuj swoją strategię, przywracając zestaw kopii zapasowych, a następnie odzyskując bazę danych, aby przygotować się na skuteczne reagowanie na awarię.

Aby dowiedzieć się więcej o tym, dlaczego są ważne kopie zapasowe, zobacz [wykonywanie kopii zapasowych i przywracanie baz danych SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Usługa Azure SQL Edge obsługuje tworzenie kopii zapasowych i przywracanie danych z magazynu lokalnego lub z obiektów blob platformy Azure. Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych i przywracania ich z poziomu usługi Azure Blob Storage, należy zapoznać się z tematem [SQL Server Backup i Restore with Microsoft Azure Blob Storage Service](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) i [SQL Server Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Tworzenie kopii zapasowej bazy danych w usłudze Azure SQL Edge

Usługa Azure SQL Edge obsługuje te same typy kopii zapasowych, co obsługiwane przez SQL Server. Aby uzyskać pełną listę typów kopii zapasowych obsługiwanych w SQL Server, zobacz [Omówienie usługi Backup](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Bazy danych utworzone w usłudze Azure SQL Edge domyślnie korzystają z modelu odzyskiwania prostego. Ponieważ takie kopie zapasowe dziennika nie mogą być wykonywane na tych bazach danych. Jeśli istnieje potrzeba wykonania kopii zapasowych dzienników w tych bazach danych, administrator musi zmienić model odzyskiwania bazy danych na model odzyskiwania pełnego. Aby zapoznać się z pełną listą modeli odzyskiwania obsługiwanych przez SQL Server, zobacz [Omówienie modelu odzyskiwania](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="backup-to-local-disk"></a>Tworzenie kopii zapasowej na dysku lokalnym

W poniższym przykładzie polecenie BACKUP DATABASE języka Transact-SQL służy do tworzenia kopii zapasowej bazy danych w kontenerze. Na potrzeby tego przykładu tworzony jest nowy folder o nazwie "kopia zapasowa" do przechowywania plików kopii zapasowej.

1. Utwórz folder kopii zapasowych. To polecenie należy wykonać na hoście, na którym działa kontener usługi Azure SQL Edge. W poniższym poleceniu Zastąp **<AzureSQLEdge_Container_Name>** nazwą kontenera usługi Azure SQL Edge w danym wdrożeniu.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Połącz się z wystąpieniem usługi Azure SQL Edge przy użyciu SQL Server Management Studio (SSMS) lub za pomocą Azure Data Studio (ADS) i uruchom polecenie Backup Database, aby utworzyć kopię zapasową bazy danych użytkownika. W poniższym przykładzie zajmiemy się wykonywaniem kopii zapasowej bazy danych *IronOreSilicaPrediction* .

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Po uruchomieniu polecenia i w przypadku pomyślnego wykonania kopii zapasowej bazy danych w sekcji Results programu SSMS lub ADS zobaczysz komunikaty podobne do następujących:

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Utwórz kopię zapasową do adresu URL

Usługa Azure SQL Edge obsługuje kopie zapasowe zarówno stronicowych obiektów blob, jak i blokowych obiektów BLOB. Aby uzyskać więcej informacji na temat stronicowych obiektów blob i blokowych obiektów blob, zapoznaj się [z kopią zapasową, aby zablokować](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob)obiekt BLOB W poniższym przykładzie kopia zapasowa bazy danych *IronOreSilicaPrediction* jest tworzona w blokowym obiekcie blob. 

1. Pierwszym krokiem w konfigurowaniu kopii zapasowych w celu zablokowania obiektów BLOB jest wygenerowanie tokenu sygnatury dostępu współdzielonego, za pomocą którego można utworzyć poświadczenia SQL Server w usłudze Azure SQL Edge. Skrypt tworzy sygnaturę dostępu współdzielonego, która jest skojarzona z przechowywanymi zasadami dostępu. Aby uzyskać więcej informacji, zobacz [sygnatury dostępu współdzielonego, część 1: Omówienie modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). Skrypt zapisuje również polecenie T-SQL wymagane do utworzenia poświadczenia na SQL Server. W poniższym skrypcie założono, że masz już subskrypcję platformy Azure z kontem magazynu i kontenerem magazynu dla kopii zapasowych.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Po pomyślnym uruchomieniu skryptu Skopiuj polecenie Utwórz poświadczenie do narzędzia zapytania, Połącz się z wystąpieniem SQL Server i uruchom polecenie, aby utworzyć poświadczenia z sygnaturą dostępu współdzielonego.

2. Połącz się z wystąpieniem usługi Azure SQL Edge przy użyciu narzędzia SQL Server Management Studio (SSMS) lub Azure Data Studio (ADS) i Utwórz poświadczenia przy użyciu polecenia z poprzedniego kroku. Pamiętaj o zamianie polecenia CREATE CREDENTIAL na dane wyjściowe z poprzedniego kroku.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Następujące polecenie tworzy kopię zapasową *IronOreSilicaPrediction* w kontenerze usługi Azure Storage.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Przywracanie bazy danych w usłudze Azure SQL Edge

Usługa Azure SQL Edge obsługuje przywracanie zarówno z dysku lokalnego, lokalizacji sieciowej, jak i z konta usługi Azure Blob Storage. Aby zapoznać się z omówieniem przywracania i odzyskiwania w SQL Server, zobacz [przywracanie i odzyskiwanie — Omówienie](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). Aby zapoznać się z omówieniem prostego modelu odzyskiwania w SQL Server, zobacz [kompletne przywracanie bazy danych (model odzyskiwania prostego)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Przywróć z dysku lokalnego

W tym przykładzie używa się kopii zapasowej *IronOreSilicaPrediction* wykonanej w poprzednim przykładzie do przywracania jako nowej bazy danych o innej nazwie.

1. Jeśli plik kopii zapasowej bazy danych nie znajduje się już w kontenerze, możesz użyć poniższego polecenia, aby skopiować plik do kontenera. W poniższym przykładzie przyjęto założenie, że plik kopii zapasowej znajduje się na hoście lokalnym i jest kopiowany do folderu/var/opt/MSSQL/Backup w kontenerze usługi Azure SQL Edge o nazwie SQL1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Połącz się z wystąpieniem usługi Azure SQL Edge przy użyciu SQL Server Management Studio (SSMS) lub Azure Data Studio (ADS), aby wykonać polecenie Restore. W poniższym przykładzie zostanie przywrócony **IronOrePredictDB. bak** , aby utworzyć nową bazę danych **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Po uruchomieniu polecenia Restore i jeśli operacja przywracania zakończyła się pomyślnie, zobaczysz komunikaty podobne do następujących w oknie danych wyjściowych. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Przywróć z adresu URL

Usługa Azure SQL Edge obsługuje również przywracanie bazy danych z konta usługi Azure Storage. Przywrócenie można wykonać z poziomu blokowych obiektów blob lub kopii zapasowych obiektów BLOB strony. W poniższym przykładzie plik kopii zapasowej bazy danych *IronOreSilicaPrediction_2020_04_16. bak* w blokowym obiekcie blob zostanie przywrócony do utworzenia *IronOreSilicaPrediction_3*bazy danych.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


