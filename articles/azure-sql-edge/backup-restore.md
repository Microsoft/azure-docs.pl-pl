---
title: Tworzenie kopii zapasowych i przywracanie baz danych — Azure SQL Edge
description: Informacje o możliwościach tworzenia kopii zapasowych i przywracania danych w usłudze Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f2cc8901ee3952f7d258d768e175412254ec5d1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905946"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Tworzenie kopii zapasowych i przywracanie baz danych w usłudze Azure SQL Edge 

Usługa Azure SQL Edge jest oparta na najnowszych wersjach aparatu Microsoft SQL Database Engine. Zapewnia ona podobne możliwości tworzenia kopii zapasowych i przywracania baz danych, jak te dostępne w SQL Server on Linux i SQL Server uruchomione w kontenerach. Składnik kopia zapasowa i przywracanie zapewnia podstawowe zabezpieczenia ochrony danych przechowywanych w bazach danych usługi Azure SQL Edge. 

Aby zminimalizować ryzyko utraty danych, należy okresowo tworzyć kopie zapasowe baz danych, aby regularnie zachować modyfikacje danych. Dobrze zaplanowana strategii tworzenia kopii zapasowej i przywracania pomaga chronić bazy danych przed utratą danych spowodowaną przez różne błędy. Przetestuj strategię przez przywrócenie zestawu kopii zapasowych, a następnie odzyskiwanie bazy danych, aby przygotować się do skutecznej reakcji na awarię.

Aby dowiedzieć się więcej o tym, dlaczego są ważne kopie zapasowe, zobacz [Tworzenie kopii zapasowych i przywracanie baz danych SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Usługa Azure SQL Edge umożliwia tworzenie kopii zapasowych i przywracanie danych zarówno z magazynu lokalnego, jak i obiektów blob platformy Azure. Aby uzyskać więcej informacji, zobacz [SQL Server kopia zapasowa i przywracanie za pomocą usługi Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) i [SQL Server kopii zapasowej do adresu URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Tworzenie kopii zapasowej bazy danych w usłudze Azure SQL Edge

Usługa Azure SQL Edge obsługuje te same typy kopii zapasowych co SQL Server. Aby uzyskać pełną listę, zobacz [Omówienie usługi Backup](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Bazy danych utworzone w usłudze Azure SQL Edge domyślnie korzystają z modelu odzyskiwania prostego. W związku z tym nie można wykonywać kopii zapasowych dzienników w tych bazach danych. Jeśli chcesz to zrobić, musisz mieć uprawnienia administratora, aby zmienić model odzyskiwania bazy danych na model odzyskiwania pełnego. Aby zapoznać się z pełną listą modeli odzyskiwania obsługiwanych przez SQL Server, zobacz temat [Omówienie modelu odzyskiwania](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="back-up-to-local-disk"></a>Tworzenie kopii zapasowej na dysku lokalnym

W poniższym przykładzie użyto `BACKUP DATABASE` polecenia Transact-SQL do utworzenia kopii zapasowej bazy danych w kontenerze. Na potrzeby tego przykładu tworzony jest nowy folder o nazwie *Backup* do przechowywania plików kopii zapasowej.

1. Utwórz folder kopii zapasowych. Uruchom to polecenie na hoście, na którym działa kontener usługi Azure SQL Edge. W poniższym poleceniu Zastąp **<AzureSQLEdge_Container_Name>** nazwą kontenera usługi Azure SQL Edge w danym wdrożeniu.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Połącz się z wystąpieniem usługi Azure SQL Edge przy użyciu programu SQL Server Management Studio (SSMS) lub przy użyciu Azure Data Studio. Uruchom `BACKUP DATABASE` polecenie, aby wykonać kopię zapasową bazy danych użytkownika. W poniższym przykładzie wykonujesz kopię zapasową bazy danych *IronOreSilicaPrediction* .

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Po uruchomieniu polecenia, jeśli kopia zapasowa bazy danych zakończy się pomyślnie, zobaczysz komunikaty podobne do następujących w sekcji Results programu SSMS lub Azure Data Studio.

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

### <a name="back-up-to-url"></a>Utwórz kopię zapasową do adresu URL

Usługa Azure SQL Edge obsługuje kopie zapasowe zarówno stronicowych obiektów blob, jak i blokowych obiektów BLOB. Aby uzyskać więcej informacji, zobacz [Tworzenie kopii zapasowej do blokowego obiektu BLOB programu vs Page](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob). W poniższym przykładzie kopia zapasowa bazy danych *IronOreSilicaPrediction* jest tworzona w blokowym obiekcie blob. 

1. Aby skonfigurować tworzenie kopii zapasowych w celu blokowania obiektów blob, należy najpierw wygenerować token sygnatury dostępu współdzielonego (SAS), za pomocą którego można utworzyć poświadczenia SQL Server w usłudze Azure SQL Edge. Skrypt tworzy sygnaturę dostępu współdzielonego, która jest skojarzona z przechowywanymi zasadami. Aby uzyskać więcej informacji, zobacz [sygnatury dostępu współdzielonego, część 1: Omówienie modelu SAS](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/). Skrypt zapisuje również polecenie T-SQL wymagane do utworzenia poświadczenia na SQL Server. W poniższym skrypcie założono, że masz już subskrypcję platformy Azure z kontem magazynu i kontenerem magazynu dla kopii zapasowych.

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

    Po pomyślnym uruchomieniu skryptu Skopiuj `CREATE CREDENTIAL` polecenie do narzędzia zapytania. Następnie połącz się z wystąpieniem SQL Server i uruchom polecenie, aby utworzyć poświadczenia przy użyciu sygnatury dostępu współdzielonego.

2. Połącz się z wystąpieniem usługi Azure SQL Edge przy użyciu programu SSMS lub Azure Data Studio i Utwórz poświadczenie przy użyciu polecenia z poprzedniego kroku. Upewnij się, że polecenie zostało zastąpione `CREATE CREDENTIAL` rzeczywistym wyjściem z poprzedniego kroku.

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

## <a name="restore-a-database-in-azure-sql-edge"></a>Przywracanie bazy danych w usłudze Azure SQL Edge

W usłudze Azure SQL Edge można przywrócić z dysku lokalnego, lokalizacji sieciowej lub konta usługi Azure Blob Storage. Aby uzyskać więcej informacji na temat przywracania i odzyskiwania w SQL Server, zobacz [przywracanie i odzyskiwanie — Omówienie](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server). Aby zapoznać się z omówieniem prostego modelu odzyskiwania w SQL Server, zobacz [Kończenie przywracania bazy danych (model odzyskiwania prostego)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model).

> [!IMPORTANT] 
> Baz danych utworzonych w usłudze Azure SQL Edge nie można przywrócić w wystąpieniu Microsoft SQL Server lub Azure SQL. Ponadto baza danych utworzona w Microsoft SQL Server lub Azure SQL może być przywracana w usłudze Azure SQL Edge, pod warunkiem, że baza danych nie zawiera żadnych funkcji nieobsługiwanych przez usługę Azure SQL Edge. 

### <a name="restore-from-a-local-disk"></a>Przywracanie z dysku lokalnego

W tym przykładzie używa się kopii zapasowej *IronOreSilicaPrediction* , która została wprowadzona w poprzednim przykładzie. Teraz zostanie przywrócony jako nowa baza danych o innej nazwie.

1. Jeśli plik kopii zapasowej bazy danych nie znajduje się już w kontenerze, można użyć następującego polecenia, aby skopiować plik do kontenera. W poniższym przykładzie przyjęto założenie, że plik kopii zapasowej znajduje się na hoście lokalnym i jest kopiowany do folderu/var/opt/MSSQL/Backup w kontenerze usługi Azure SQL Edge o nazwie *SQL1*.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Połącz się z wystąpieniem usługi Azure SQL Edge przy użyciu programu SSMS lub Azure Data Studio, aby uruchomić polecenie Restore. W poniższym przykładzie zostanie przywrócony **IronOrePredictDB. bak** , aby utworzyć nową bazę danych **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Po uruchomieniu polecenia Restore, jeśli operacja przywracania zakończyła się pomyślnie, zobaczysz komunikaty podobne do następujących w oknie danych wyjściowych. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Przywróć z adresu URL

Usługa Azure SQL Edge obsługuje również przywracanie bazy danych z konta usługi Azure Storage. Można przywrócić z kopii zapasowych blokowych obiektów blob lub stronicowych obiektów BLOB. W poniższym przykładzie plik kopii zapasowej bazy danych *IronOreSilicaPrediction_2020_04_16. bak* w blokowym obiekcie BLOB jest przywracany w celu utworzenia bazy danych, *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


