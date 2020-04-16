---
title: Rozwiązywanie problemów z łącznikami usługi Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z łącznikami w usłudze Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 62ad337646cf3fc0bbe4305dccad5adb56f8ee15
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410225"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Rozwiązywanie problemów z łącznikami usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano typowe metody rozwiązywania problemów z łącznikami w usłudze Azure Data Factory.
  

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>Kod błędu: AzureBlobOperationFailed

- **Komunikat**:`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Przyczyna:** Problem z działaniem magazynu obiektów blob.

- **Zalecenie**: Sprawdź błąd w szczegółach. Zapoznaj się z dokumentem pomocy obiektu blob: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Skontaktuj się z zespołem magazynu, jeśli potrzebujesz pomocy.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Kod błędu: AzureBlobServiceNotReturnExpectedDataLength

- **Komunikat**:`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Kod błędu: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Komunikat**:`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Kod błędu: AzureStorageOperationFailedConcurrentWrite

- **Komunikat**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Komunikat o błędzie: rozmiar żądania jest za duży

- **Symptomy: Kopiowanie**danych do usługi Azure Cosmos DB z domyślnym rozmiarem partii zapisu i błąd trafienia ***"Rozmiar żądania jest zbyt duży**"*.

- **Przyczyna:** Usługa Cosmos DB ogranicza rozmiar jednego żądania do 2 MB. Formuła jest, Rozmiar żądania = Pojedynczy rozmiar dokumentu * Rozmiar partii zapisu. Jeśli rozmiar dokumentu jest duży, domyślne zachowanie spowoduje zbyt duży rozmiar żądania. Można dostroić rozmiar partii zapisu.

- **Rozwiązanie**: W umywalka działania kopiowania zmniejsz wartość "Napisz rozmiar partii" (wartość domyślna to 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Komunikat o błędzie: Unikatowe naruszenie ograniczenia indeksu

- **Symptomy**: Podczas kopiowania danych do usługi Cosmos DB wystąpił następujący błąd:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Przyczyna**: Istnieją dwie możliwe przyczyny:

    - Jeśli używasz **Wstaw** jako zachowanie zapisu, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty o tym samym identyfikatorze.

    - Jeśli używasz **Upsert** jako zachowania zapisu i ustawisz inny unikatowy klucz do kontenera, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty o różnych identyfikatorach, ale taką samą wartość dla zdefiniowanego klucza unikatowego.

- **Rozdzielczość:** 

    - Dla przyczyny1, skonfigurować **upsert** jako zachowanie zapisu.
    - Dla przyczyny 2 upewnij się, że każdy dokument ma inną wartość dla zdefiniowanego klucza unikatowego.

### <a name="error-message-request-rate-is-large"></a>Komunikat o błędzie: szybkość żądania jest duża

- **Symptomy**: Podczas kopiowania danych do usługi Cosmos DB wystąpił następujący błąd:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Przyczyna:** Używane jednostki żądania są większe niż dostępne ru skonfigurowane w usłudze Cosmos DB. Dowiedz się, jak Cosmos DB oblicza RU [stąd](../cosmos-db/request-units.md#request-unit-considerations).

- **Rozwiązanie**: Oto dwa rozwiązania:

    1. **Zwiększ rozmiar kontenera RU** do większej wartości w usłudze Cosmos DB, co poprawi wydajność działania kopiowania, ale poniesie więcej kosztów w usłudze Cosmos DB. 

    2. Zmniejsz **writeBatchSize** do mniejszej wartości (na przykład 1000) i ustaw **parallelCopies** do mniejszej wartości, takich jak 1, co sprawi, że wydajność uruchamiania kopią będzie gorsza niż bieżąca, ale nie poniesie większych kosztów w usłudze Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Brak kolumny w mapowaniu kolumn

- **Symptomy:** Podczas importowania schematu usługi Cosmos DB do mapowania kolumn brakuje niektórych kolumn. 

- **Przyczyna:** ADF wywnioskować schemat z pierwszych 10 dokumentów usługi Cosmos DB. Jeśli niektóre kolumny/właściwości nie mają wartości w tych dokumentach, nie zostaną wykryte przez podajnik ADF, więc nie będą wyświetlane.

- **Rozwiązanie:** Możesz dostroić kwerendę jak poniżej, aby wymusić kolumnę, aby wyświetlić w zestawie wyników z pustą wartością: (załóżmy, że w pierwszych 10 dokumentach brakuje kolumny "niemożliwe"). Alternatywnie można ręcznie dodać kolumnę do mapowania.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Komunikat o błędzie: GuidRepresentation dla czytnika jest CSharpLegacy

- **Symptomy**: Podczas kopiowania danych z usługi Cosmos DB MongoAPI/MongoDB z polem UUID wystąpił następujący błąd:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Przyczyna:** Istnieją dwa sposoby reprezentowania UUID w BSON - UuidStardard i UuidLegacy. Domyślnie UuidLegacy jest używany do odczytu danych. Zostanie wyświetlony błąd, jeśli twoje dane UUID w MongoDB to UuidStandard.

- **Rozdzielczość**: W ciągu połączenia MongoDB dodaj opcję "**uuidRepresentation=standard**". Aby uzyskać więcej informacji, zobacz [parametry połączenia MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="error-code--adlsgen2operationfailed"></a>Kod błędu: AdlsGen2OperationFailed

- **Komunikat**:`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Przyczyna:** ADLS Gen2 zgłasza błąd wskazujący, że operacja nie powiodła się.

- **Zalecenie:** Sprawdź szczegółowy komunikat o błędzie generowany przez ADLS Gen2. Jeśli jest to spowodowane przez błąd przejściowy, ponów próbę. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.

- **Przyczyna:** Gdy komunikat o błędzie zawiera "Zabronione", jednostki usługi lub tożsamości zarządzanej, którego używasz może nie mieć wystarczających uprawnień dostępu do ADLS Gen2.

- **Zalecenie**: Zapoznaj się https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authenticationz dokumentem pomocy: .

- **Przyczyna:** Gdy komunikat o błędzie zawiera "InternalServerError", błąd jest zwracany przez ADLS Gen2.

- **Zalecenie:** Może to być spowodowane przez błąd przejściowy, ponów próbę. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.


### <a name="error-code--adlsgen2invalidurl"></a>Kod błędu: AdlsGen2InvalidUrl

- **Komunikat**:`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Kod błędu: AdlsGen2InvalidFolderPath

- **Komunikat**:`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Kod błędu: AdlsGen2OperationFailedConcurrentWrite

- **Komunikat**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Kod błędu: AdlsGen2TimeoutError

- **Komunikat**:`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Komunikat o błędzie: Serwer zdalny zwrócił błąd: (403) Zabronione

- **Symptomy**: Aktywność kopiowania kończy się niepowodzeniem z następującym błędem: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Przyczyna:** Jedną z możliwych przyczyn jest to, że jednostki usługi lub tożsamości zarządzanej, którego używasz nie ma uprawnień dostępu do określonego folderu/pliku.

- **Rozwiązanie**: Udziel odpowiednich uprawnień do wszystkich folderów i podfolderów, które musisz skopiować. Patrz [ten doc](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Komunikat o błędzie: nie można uzyskać tokenu dostępu przy użyciu jednostki usługi. Błąd ADAL: service_unavailable

- **Symptomy**: Aktywność kopiowania kończy się niepowodzeniem z następującym błędem:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Przyczyna:** Gdy serwer tokenu usługi (STS) należący do usługi Azure Active Directory nie jest niedostępny, to znaczy, zbyt zajęty do obsługi żądań, zwraca błąd HTTP 503. 

- **Rozwiązanie**: Uruchom ponownie działanie kopiowania po kilku minutach.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kod błędu: SqlFailedToConnect

- **Komunikat**:`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Przyczyna:** Jeśli komunikat o błędzie zawiera "SqlException", baza danych SQL zgłasza błąd wskazujący, że niektóre określone działanie nie powiodło się.

- **Zalecenie**: Proszę szukać przez kod błędu SQL w https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorstym doc odniesienia, aby uzyskać więcej szczegółów: . Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną usługi Azure SQL.

- **Przyczyna:** Jeśli komunikat o błędzie zawiera komunikat "Klient z adresem IP '..."' nie ma dostępu do serwera", a użytkownik próbuje połączyć się z usługą Azure SQL Database, zwykle jest to spowodowane problemem z zaporą usługi Azure SQL Database.

- **Zalecenie:** W konfiguracji zapory programu Azure SQL Server włącz opcję "Zezwalaj na dostęp do tego serwera usług i zasobów platformy Azure". Doc referencyjny: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Kod błędu: SqlOperationFailed

- **Komunikat**:`A database operation failed. Please search error to get more details.`

- **Przyczyna:** Jeśli komunikat o błędzie zawiera "SqlException", baza danych SQL zgłasza błąd wskazujący, że niektóre określone działanie nie powiodło się.

- **Zalecenie:** Jeśli błąd SQL nie jest jasny, spróbuj zmienić bazę danych na najnowszy poziom zgodności "150". Może rzucać błędy SQL najnowszej wersji. Proszę odnieść się do https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompatdoc szczegóły: .
        Aby rozwiązać problemy z SQL, wyszukaj kod błędu SQL https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsw tym doc odwołania, aby uzyskać więcej szczegółów: . Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną usługi Azure SQL.

- **Przyczyna:** Jeśli komunikat o błędzie zawiera "PdwManagedToNativeInteropException", zwykle jest to spowodowane niezgodnością między rozmiarami kolumn źródłowych i ujścia.

- **Zalecenie:** Sprawdź rozmiar kolumn źródłowych i zlewów. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną usługi Azure SQL.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera "InvalidOperationException", zwykle jest to spowodowane nieprawidłowymi danymi wejściowymi.

- **Zalecenie:** Aby określić, który wiersz napotyka problem, włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu dalszego zbadania. Doc referencyjny: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Kod błędu: SqlUnauthorizedAccess

- **Komunikat**:`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Przyczyna:** Poświadczenia są niepoprawne lub konto logowania nie może uzyskać dostępu do bazy danych SQL.

- **Zalecenie:** Sprawdź, czy konto logowania ma wystarczające uprawnienia dostępu do bazy danych SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kod błędu: SqlOpenConnectionTimeout

- **Komunikat**:`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Przyczyna:** Może to być błąd przejściowy bazy danych SQL.

- **Zalecenie:** Ponów próbę aktualizacji połączonego ciągu połączenia usługi o większej wartości limitu czasu połączenia.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kod błędu: SqlAutoCreateTableTypeMapFailed

- **Komunikat**:`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Przyczyna:** Tabela automatycznego tworzenia nie może spełniać wymagań źródłowych.

- **Zalecenie:** Zaktualizuj typ kolumny w "mapowaniach" lub ręcznie utwórz tabelę ujścia na serwerze docelowym.


### <a name="error-code--sqldatatypenotsupported"></a>Kod błędu: SqlDataTypeNotSupported

- **Komunikat**:`A database operation failed. Check the SQL errors.`

- **Przyczyna:** Jeśli problem występuje na źródle SQL i błąd jest związany z przepełnienie SqlDateTime, wartość danych jest w zakresie typu logiki (1/1/1753 12:00:00 - 12/31/9999 11:59:59 PM).

- **Zalecenie:** Rzutowanie typu na ciąg w źródłowej kwerendzie SQL lub w mapowaniu kolumn aktywność kopiowania zmień typ kolumny na "Ciąg".

- **Przyczyna:** Jeśli problem występuje na ujściu SQL i błąd jest związany z przepełnieniem SqlDateTime, wartość danych jest w dozwolonym zakresie w tabeli ujścia.

- **Zalecenie:** Zaktualizuj odpowiedni typ kolumny do typu "datetime2" w tabeli ujścia.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kod błędu: SqlInvalidDbStoredProcedure

- **Komunikat**:`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Przyczyna:** Określona procedura składowana jest nieprawidłowa. Może to być spowodowane przez to, że procedura składowana nie zwraca żadnych danych.

- **Zalecenie:** Sprawdź poprawność procedury składowanej przez narzędzia SQL. Upewnij się, że procedura składowana może zwracać dane.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kod błędu: SqlInvalidDbQueryString

- **Komunikat**:`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Przyczyna:** Określona kwerenda SQL jest nieprawidłowa. Może to być spowodowane tym, że kwerenda nie zwraca żadnych danych

- **Zalecenie:** Sprawdź poprawność zapytania SQL przez narzędzia SQL. Upewnij się, że kwerenda może zwracać dane.


### <a name="error-code--sqlinvalidcolumnname"></a>Kod błędu: SqlInvalidColumnName

- **Komunikat**:`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Przyczyna:** Nie można odnaleźć kolumny. Możliwa nieprawidłowa konfiguracja.

- **Zalecenie:** Sprawdź kolumnę w kwerendzie, "strukturę" w zestawie danych i "mapowania" w działaniu.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Kod błędu: SqlColumnNameMismatchByCaseSensitive

- **Komunikat**:`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Kod błędu: SqlBatchWriteTimeout

- **Komunikat**:`Timeouts in SQL write operation.`

- **Przyczyna:** Może to być błąd przejściowy bazy danych SQL.

- **Zalecenie**: Ponów próbę. Jeśli problem repro, skontaktuj się z pomocą techniczną sql platformy Azure.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Kod błędu: SqlBatchWriteTransactionFailed

- **Komunikat**:`SQL transaction commits failed`

- **Przyczyna:** Jeśli szczegóły wyjątku stale informują o przekroju czasu transakcji, opóźnienie sieci między czasem wykonywania integracji a bazą danych jest wyższe niż próg domyślny jako 30 sekund.

- **Zalecenie:** Zaktualizuj ciąg połączenia połączonego usługi SQL o wartość limitu czasu połączenia równą 120 lub nowszej i uruchom ponownie działanie.

- **Przyczyna:** Jeśli szczegóły wyjątku sporadycznie informują o przerwaniu połączenia sqlconnection, może to być po prostu przejściowa awaria sieci lub problem po stronie bazy danych SQL

- **Zalecenie:** Ponów próbę wykonania działania i przejrzyj metryki po stronie bazy danych SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kod błędu: SqlBulkCopyInvalidColumnLength

- **Komunikat**:`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Przyczyna:** Kopiowanie zbiorcze SQL nie powiodło się z powodu otrzymania nieprawidłowej długości kolumny od klienta bcp.

- **Zalecenie:** Aby określić, który wiersz napotyka problem, włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu dalszego zbadania. Doc referencyjny: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Kod błędu: SqlConnectionIsClosed

- **Komunikat**:`The connection is closed by SQL Database.`

- **Przyczyna:** Połączenie SQL jest zamykane przez bazę danych SQL, gdy wysokie równoczesne uruchamianie i zakończenie połączenia serwera.

- **Zalecenie:** Serwer zdalny zamknął połączenie SQL. Spróbuj ponownie. Jeśli problem repro, skontaktuj się z pomocą techniczną sql platformy Azure.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Kod błędu: SqlCreateTableFailedUnsupportedType

- **Komunikat**:`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Komunikat o błędzie: Konwersja nie powiodła się podczas konwersji z ciągu znaków na uniqueidentifier

- **Symptomy:** Podczas kopiowania danych z tabelaryczne źródło danych (takich jak SQL Server) do usługi Azure SQL Data Warehouse przy użyciu kopii etapowej i PolyBase, można nacisnąć następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Przyczyna:** Azure SQL Data Warehouse PolyBase nie może przekonwertować pustego ciągu na identyfikator GUID.

- **Rozdzielczość**: W umywalka działania kopiowania w obszarze Ustawienia bazy polybase ustaw opcję **"użyj typu domyślnego**" na false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Komunikat o błędzie: Oczekiwany typ danych: DECIMAL(x,x), Wartość naruszająca

- **Symptomy:** Podczas kopiowania danych z tabelaryczne źródło danych (takich jak SQL Server) do SQL DW przy użyciu kopii etapowej i PolyBase, następuje następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Przyczyna:** Azure SQL Data Warehouse Polybase nie można wstawić pusty ciąg (wartość null) do kolumny dziesiętnej.

- **Rozdzielczość**: W umywalka działania kopiowania w obszarze Ustawienia bazy polybase ustaw opcję **"użyj typu domyślnego**" na false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Komunikat o błędzie: komunikat o wyjątku Java:HdfsBridge::CreateRecordReader

- **Symptomy: Kopiowanie**danych do usługi Azure SQL Data Warehouse przy użyciu polybase i wystąpił następujący błąd:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Przyczyna:** Możliwa przyczyna jest zbyt duża (całkowita szerokość kolumny) (większa niż 1 MB). Sprawdź schemat docelowej tabeli SQL DW, dodając rozmiar wszystkich kolumn:

    - Int -> 4 bajtów
    - Bigint -> 8 bajtów
    - Varchar(n),char(n),binary(n), varbinary(n) -> n bajtów
    - Nvarchar(n), nchar(n) -> n*2 bajtów
    - Data -> 6 bajtów
    - Datetime/(2), smalldatetime -> 16 bajtów
    - Datetimeoffset -> 20 bajtów
    - Dziesiętny -> 19 bajtów
    - Float -> 8 bajtów
    - Pieniądze -> 8 bajtów
    - Smallmoney -> 4 bajtów
    - Real -> 4 bajtów
    - Smallint -> 2 bajtów
    - Czas -> 12 bajtów
    - Tinyint -> 1 bajt

- **Rozdzielczość:** Zmniejsz szerokość kolumny do mniej niż 1 MB

- Lub użyj podejścia wstawiacza zbiorczego, wyłączając Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Komunikat o błędzie: Warunek określony przy użyciu nagłówków warunkowych HTTP nie jest spełniony

- **Symptomy:** Użyj kwerendy SQL do pobierania danych z usługi Azure SQL Data Warehouse i wystąpił następujący błąd:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Przyczyna:** Problem z trafieniem usługi Azure SQL Data Warehouse, który wysyłał zapytanie do tabeli zewnętrznej w usłudze Azure Storage.

- **Rozwiązanie:** Uruchom tę samą kwerendę w systemie SSMS i sprawdź, czy widzisz ten sam wynik. Jeśli tak, otwórz bilet pomocy technicznej dotyczący usługi Azure SQL Data Warehouse i podaj nazwę serwera i bazy danych SQL DW, aby kontynuować rozwiązywanie problemów.
            

## <a name="delimited-text-format"></a>Rozdzielony format tekstu

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Kod błędu: DelimitedTextColumnNameNotAllowNull

- **Komunikat**:`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Przyczyna:** Po ustawieniu "firstRowAsHeader" w działaniu, pierwszy wiersz będzie używany jako nazwa kolumny. Ten błąd oznacza, że pierwszy wiersz zawiera pustą wartość. Na przykład: 'ColumnA,,ColumnB'.

- **Zalecenie:** Sprawdź pierwszy wiersz i napraw wartość, jeśli jest pusta wartość.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Kod błędu: DelimitedTextMoreColumnsThanDedefined

- **Komunikat**:`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Przyczyna:** Liczba kolumn problematycznego wiersza jest duża niż liczba kolumn pierwszego wiersza. Może to być spowodowane przez problem z danymi lub nieprawidłowe ustawienia ogranicznika kolumny/cudzysłowu.

- **Zalecenie:** Pobierz liczbę wierszy w komunikacie o błędzie, sprawdź kolumnę wiersza i napraw dane.

- **Przyczyna:** Jeśli oczekiwana liczba kolumn jest "1" w komunikacie o błędzie, możliwe, że określono nieprawidłowe ustawienia kompresji lub formatu, które spowodowały, że podajnik ADF błędnie przeanalizował pliki.

- **Zalecenie:** Sprawdź ustawienia formatu, aby upewnić się, że są zgodne z plikami źródłowymi.

- **Przyczyna:** Jeśli źródłem jest folder, możliwe, że pliki w określonym folderze mają inny schemat.

- **Zalecenie**: Upewnij się, że pliki w danym folderze mają identyczny schemat.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Kod błędu: DelimitedTextIncorrectRowDelimiter

- **Komunikat**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Kod błędu: DelimitedTextTooLargeColumnCount

- **Komunikat**:`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Kod błędu: DelimitedTextInvalidSettings

- **Komunikat**:`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Kod błędu: DynamicsCreateServiceClientError

- **Komunikat**:`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Przyczyna:** Jest to przejściowy problem po stronie serwera dynamiki.

- **Zalecenie:** Uruchom ponownie potok. Jeśli zachować nie, spróbuj zmniejszyć równoległość. Jeśli nadal się nie powiedzie, skontaktuj się z pomocą techniczną dynamics.



## <a name="json-format"></a>JSON Format

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Kod błędu: JsonInvalidArrayPathDefinition

- **Komunikat**:`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Kod błędu: JsonEmptyJObjectData

- **Komunikat**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Kod błędu: JsonNullValueInPathDefinition

- **Komunikat**:`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Kod błędu: JsonUnsupportedHierarchicalComplexValue

- **Komunikat**:`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Kod błędu: JsonConflictPartitionDiscoverySchema

- **Komunikat**:`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Kod błędu: JsonInvalidDataFormat

- **Komunikat**:`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Kod błędu: JsonInvalidDataMixedArrayAndObject

- **Komunikat**:`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Format parkietu

### <a name="error-code--parquetjavainvocationexception"></a>Kod błędu: ParquetJavaInvocationException

- **Komunikat**:`An error occurred when invoking java, message: %javaException;.`

- **Przyczyna:** Gdy komunikat o błędzie zawiera 'java.lang.OutOfMemory', 'Java heap space' i 'doubleCapacity', zwykle jest to problem z zarządzaniem pamięcią w starej wersji środowiska wykonawczego integracji.

- **Zalecenie:** Jeśli używasz self-hosted Integration Runtime i wersja jest wcześniejsza niż 3.20.7159.1, zaproponuj uaktualnienie do najnowszej wersji.

- **Przyczyna:** Gdy komunikat o błędzie zawiera komunikat "java.lang.OutOfMemory", środowisko wykonawcze integracji nie ma wystarczającej ilości zasobów do przetworzenia plików.

- **Zalecenie:** Ogranicz równoczesne uruchomienia w czasie wykonywania integracji. W przypadku środowiska wykonawczego integracji hostowanej samodzielnie skaluj do zaawansowanego komputera z pamięcią równą lub większą niż 8 GB.

- **Przyczyna:** Gdy komunikat o błędzie zawiera "NullPointerReference", możliwe jest błąd przejściowy.

- **Zalecenie**: Ponów próbę. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną.


### <a name="error-code--parquetinvalidfile"></a>Kod błędu: ParquetInvalidFile

- **Komunikat**:`File is not a valid parquet file.`

- **Przyczyna:** Problem z plikiem parkietu.

- **Zalecenie:** Sprawdź, czy dane wejściowe są prawidłowym plikiem parkietu.


### <a name="error-code--parquetnotsupportedtype"></a>Kod błędu: ParquetNotSupportedType

- **Komunikat**:`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Przyczyna:** Format parkietu nie jest obsługiwany w usłudze Azure Data Factory.

- **Zalecenie:** Sprawdź dokładnie dane źródłowe. Patrz do dok: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Kod błędu: ParquetMissedDecimalPrecisionScale

- **Komunikat**:`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Przyczyna:** Spróbuj przeanalizować dokładność i skalę liczb, ale nie podano takich informacji.

- **Zalecenie:**"Źródło" nie zwraca poprawnej precyzji i skali. Sprawdź dokładność i skalę kolumny problemu.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Kod błędu: ParquetInvalidDecimalPrecisionScale

- **Komunikat**:`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Przyczyna:** Schemat jest nieprawidłowy.

- **Zalecenie:** Sprawdź dokładność i skalę kolumny problemu.


### <a name="error-code--parquetcolumnnotfound"></a>Kod błędu: ParquetColumnNotFound

- **Komunikat**:`Column %column; does not exist in Parquet file.`

- **Przyczyna:** Schemat źródłowy jest niezgodność ze schematem ujścia.

- **Zalecenie:** Sprawdź "mapowania" w "aktywność". Upewnij się, że kolumna źródłowa może być mapowana do prawej kolumny ujścia.


### <a name="error-code--parquetinvaliddataformat"></a>Kod błędu: ParquetInvalidDataFormat

- **Komunikat**:`Incorrect format of %srcValue; for converting to %dstType;.`

- **Przyczyna:** Danych nie można przekonwertować na typ określony w pliku mappings.source

- **Zalecenie:** Sprawdź dane źródłowe lub określ poprawny typ danych dla tej kolumny w mapowaniu kolumn aktywności kopiowania. Patrz do dok: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Kod błędu: ParquetDataCountNotMatchColumnCount

- **Komunikat**:`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Przyczyna:** Niezgodność liczby kolumn źródłowych i liczby zlewów

- **Zalecenie:** Podwójne sprawdzanie liczby kolumn źródłowych jest taka sama jak liczba kolumn ujścia w "mapowaniu".


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Kod błędu: ParquetDataTypeNotMatchColumnType

- **Komunikat**: Typ danych %srcType; nie jest zgodny z danym typem kolumny %dstType; w kolumnie "%columnIndex;".

- **Przyczyna**: Dane ze źródła nie mogą być konwertowane na wpisane zdefiniowane w zlewie

- **Zalecenie:** Proszę określić poprawny typ w mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Kod błędu: ParquetBridgeInvalidData

- **Komunikat**:`%message;`

- **Przyczyna**: Wartość danych ponad ograniczenie

- **Zalecenie**: Ponów próbę. Jeśli problem będzie się powtarzał, skontaktuj się z nami.


### <a name="error-code--parquetunsupportedinterpretation"></a>Kod błędu: ParquetUnsupportedInterpretacja

- **Komunikat**:`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Przyczyna:** Nie obsługiwany scenariusz

- **Zalecenie**: "ParquetInterpretFor" nie powinno być "iskrąSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Kod błędu: ParquetUnsupportFileLevelCompressionOption

- **Komunikat**:`File level compression is not supported for Parquet.`

- **Przyczyna:** Nie obsługiwany scenariusz

- **Zalecenie:** Usuń "CompressionType" w ładunku.



## <a name="general-copy-activity-error"></a>Błąd ogólnego działania kopiowania

### <a name="error-code--jrenotfound"></a>Kod błędu: JreNotFound

- **Komunikat**:`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Przyczyna:** Środowisko uruchomieniowe integracji hostowanego samodzielnie nie może znaleźć środowiska wykonawczego java. Środowisko wykonawcze Java jest wymagane do odczytu określonego źródła.

- **Zalecenie:** Sprawdź środowisko wykonawcze integracji, doc odwołania:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Kod błędu: WildcardPathSinkNotSupported

- **Komunikat**:`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Przyczyna:** Zestaw danych sink nie obsługuje symboli wieloznacznych.

- **Zalecenie:** Sprawdź zestaw danych ujścia i naprawić ścieżkę bez wartości symboli wieloznacznych.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Kod błędu: MappingInvalidPropertyWithEmptyValue

- **Komunikat**:`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Kod błędu: MappingInvalidPropertyWithNamePathAndOrdinal

- **Komunikat**:`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Kod błędu: MappingDuplicatedOrdinal

- **Komunikat**:`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Kod błędu: MappingInvalidOrdinalForSinkColumn

- **Komunikat**:`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej pomocy dotyczącej rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Usługi data factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji fabryki danych](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum przepełnienia stosu dla fabryki danych](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informacje o fabryce danych](https://twitter.com/hashtag/DataFactory)
            
