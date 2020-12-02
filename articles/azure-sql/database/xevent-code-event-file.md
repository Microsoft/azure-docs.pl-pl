---
title: Kod pliku zdarzeń systemu XEvent
description: Udostępnia program PowerShell i język Transact-SQL dla dwufazowego przykładu kodu, który pokazuje docelowy plik zdarzeń w zdarzeniu rozszerzonym na Azure SQL Database. Usługa Azure Storage to wymagana część tego scenariusza.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/06/2020
ms.openlocfilehash: d7a57f98551cf91ed87858caba0907471bcf6b12
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501230"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Kod docelowy pliku zdarzeń dla zdarzeń rozszerzonych w Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Chcesz uzyskać pełny przykład kodu, aby uzyskać niezawodny sposób przechwytywania i zgłaszania informacji dla zdarzenia rozszerzonego.

W Microsoft SQL Server [obiekt docelowy pliku zdarzenia](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) służy do przechowywania danych wyjściowych zdarzenia w pliku lokalnego dysku twardego. Ale takie pliki nie są dostępne do Azure SQL Database. Zamiast tego użyjemy usługi Azure Storage do obsługi celu pliku zdarzeń.

Ten temat przedstawia dwufazowy przykład kodu:

- Program PowerShell umożliwia utworzenie kontenera usługi Azure Storage w chmurze.
- Język Transact-SQL:
  - Aby przypisać kontener usługi Azure Storage do docelowego pliku zdarzeń.
  - Aby utworzyć i uruchomić sesję zdarzeń i tak dalej.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

- Konto i subskrypcja platformy Azure. Po zarejestrowaniu się możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Dowolna baza danych, w której można utworzyć tabelę.
  
  - Opcjonalnie możesz [utworzyć demonstracyjną bazę danych **AdventureWorksLT**](single-database-create-quickstart.md) w ciągu kilku minut.

- SQL Server Management Studio (ssms.exe), najlepiej jej Najnowsza wersja aktualizacji miesięcznych.
  Najnowszą ssms.exe można pobrać z:
  
  - Temat [SQL Server Management Studio pobierania](/sql/ssms/download-sql-server-management-studio-ssms).
  - [Bezpośredni link do pobierania.](https://go.microsoft.com/fwlink/?linkid=616025)

- Musisz mieć zainstalowane [moduły Azure PowerShell](https://go.microsoft.com/?linkid=9811175) .

  - Moduły udostępniają polecenia, takie jak- **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Faza 1: kod programu PowerShell dla kontenera usługi Azure Storage

Ten program PowerShell to faza 1 przykładu kodu dwuetapowego.

Skrypt rozpoczyna się od poleceń, które mają zostać oczyszczone po możliwym wcześniejszym uruchomieniu i jest rerunnable.

1. Wklej skrypt programu PowerShell do prostego edytora tekstu, takiego jak Notepad.exe, i Zapisz skrypt jako plik z rozszerzeniem **. ps1**.
2. Uruchom program PowerShell ISE jako administrator.
3. W wierszu polecenia wpisz<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>a następnie naciśnij klawisz ENTER.
4. W programie PowerShell ISE Otwórz plik **. ps1** . Uruchom skrypt.
5. Skrypt najpierw uruchamia nowe okno, w którym można się zalogować do platformy Azure.

   - Jeśli ponownie uruchomisz skrypt bez zakłócania sesji, masz wygodną opcję komentowania polecenia **Add-AzureAccount** .

![Program PowerShell ISE z zainstalowanym modułem platformy Azure gotowy do uruchomienia skryptu.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>Kod programu PowerShell

Ten skrypt programu PowerShell zakłada, że zainstalowano już AZ module. Aby uzyskać więcej informacji, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script.

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run,
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account.
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Zwróć uwagę na kilka nazwanych wartości, które skrypt programu PowerShell drukuje po zakończeniu. Należy edytować te wartości w skrypcie Transact-SQL, który następuje po fazie 2.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> W poprzednim przykładzie kodu programu PowerShell zdarzenia rozszerzone SQL nie są zgodne z kontami magazynu ADLS Gen2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Faza 2: kod języka Transact-SQL, który używa kontenera usługi Azure Storage

- W fazie 1 tego przykładu kodu uruchomiono skrypt programu PowerShell w celu utworzenia kontenera usługi Azure Storage.
- Dalej w fazie 2 następujący skrypt języka Transact-SQL musi używać kontenera.

Skrypt rozpoczyna się od poleceń, które mają zostać oczyszczone po możliwym wcześniejszym uruchomieniu i jest rerunnable.

Skrypt programu PowerShell drukuje kilka nazwanych wartości po zakończeniu. Aby użyć tych wartości, należy edytować skrypt Transact-SQL. Znajdź **go w skrypcie** Transact-SQL, aby zlokalizować punkty edycji.

1. Otwórz SQL Server Management Studio (ssms.exe).
2. Nawiąż połączenie z bazą danych w Azure SQL Database.
3. Kliknij, aby otworzyć nowe okienko zapytania.
4. Wklej następujący skrypt Transact-SQL w okienku zapytania.
5. Znajdź każde **zadanie** w skrypcie i wprowadź odpowiednie zmiany.
6. Zapisz, a następnie uruchom skrypt.

> [!WARNING]
> Wartość klucza sygnatury dostępu współdzielonego wygenerowanego przez poprzedni skrypt programu PowerShell może rozpoczynać się od znaku "?" (znak zapytania). Jeśli używasz klucza sygnatury dostępu współdzielonego w następującym skrypcie T-SQL, musisz *usunąć wiodący znak "?"*. W przeciwnym razie działania mogą być blokowane przez zabezpieczenia.

### <a name="transact-sql-code"></a>Kod języka Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Jeśli obiekt docelowy nie zostanie dołączony podczas uruchamiania, należy zatrzymać i ponownie uruchomić sesję zdarzeń:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Dane wyjściowe

Po zakończeniu działania skryptu Transact-SQL kliknij komórkę pod nagłówkiem kolumny **event_data_XML** . **\<event>** Wyświetlany jest jeden element, który zawiera jedną instrukcję Update.

Oto jeden **\<event>** element, który został wygenerowany podczas testowania:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Poprzedni skrypt Transact-SQL użył następującej funkcji systemowej, aby odczytać event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

Objaśnienie zaawansowanych opcji wyświetlania danych z zdarzeń rozszerzonych jest dostępne pod adresem:

- [Zaawansowane wyświetlanie danych docelowych ze zdarzeń rozszerzonych](/sql/relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konwertowanie przykładu kodu do uruchomienia na SQL Server

Załóżmy, że chcemy uruchomić poprzedni przykład Transact-SQL w Microsoft SQL Server.

- Dla uproszczenia warto całkowicie zastąpić korzystanie z kontenera usługi Azure Storage za pomocą prostego pliku, takiego jak *C:\myeventdata.XEL*. Plik zostanie zapisany na lokalnym dysku twardym komputera, który jest hostem SQL Server.
- Nie trzeba potrzebować żadnego rodzaju instrukcji języka Transact-SQL do **tworzenia klucza głównego** i **tworzenia poświadczeń**.
- W instrukcji **Create Event Session** w jej klauzuli **Add Target** należy zastąpić wartość http przypisanej do **filename =** ciągiem pełnej ścieżki, na przykład *C:\myfile.XEL*.
  
  - Nie trzeba brać udziału w koncie usługi Azure Storage.

## <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat kont i kontenerów w usłudze Azure Storage, zobacz:

- [Jak używać usługi BLOB Storage z platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Nazewnictwo kontenerów, obiektów blob i metadanych oraz odwoływanie się do nich](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
- [Praca z kontenerem głównym](/rest/api/storageservices/Working-with-the-Root-Container)
- [Lekcja 1. Tworzenie zasad dostępu przechowywanego i sygnatury dostępu współdzielonego w kontenerze platformy Azure](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)
  - [Lekcja 2. Tworzenie poświadczeń SQL Server przy użyciu sygnatury dostępu współdzielonego](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [Zdarzenia rozszerzone dla Microsoft SQL Server](/sql/relational-databases/extended-events/extended-events)