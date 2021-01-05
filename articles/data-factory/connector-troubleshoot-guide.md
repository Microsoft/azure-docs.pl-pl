---
title: Rozwiązywanie problemów z łącznikami usługi Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z łącznikiem w Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: e6591762ed6a7e2b462a209730276f3198d86ae8
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821472"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Rozwiązywanie problemów z łącznikami usługi Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z łącznikami w Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Kod błędu: AzureBlobOperationFailed

- **Komunikat**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Przyczyna**: problem z trafieniem magazynu obiektów BLOB.

- **Zalecenie**: Sprawdź błąd w obszarze Szczegóły. Zapoznaj się z dokumentem pomocy obiektu BLOB: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes . Skontaktuj się z zespołem ds. magazynu, jeśli potrzebujesz pomocy.


### <a name="invalid-property-during-copy-activity"></a>Nieprawidłowa właściwość w trakcie działania kopiowania

- **Komunikat**: `Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **Przyczyna**: typ zdefiniowany w zestawie danych jest niespójny ze źródłem/typem ujścia zdefiniowanym w działaniu kopiowania.

- **Rozwiązanie**: Edytuj definicję zestawu danych lub pliku JSON potoku, aby zapewnić spójność typów i ponowne uruchomienie wdrożenia.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Komunikat o błędzie: rozmiar żądania jest zbyt duży

- **Objawy**: dane są kopiowane do Azure Cosmos dB przy użyciu domyślnego rozmiaru wsadu zapisu i wystąpił błąd *"**rozmiar żądania jest zbyt duży**"*.

- **Przyczyna**: Cosmos DB ogranicza jeden rozmiar pojedynczego żądania do 2 MB. Formuła jest, rozmiar żądania = rozmiar pojedynczego dokumentu * Zapisz rozmiar partii. Jeśli rozmiar dokumentu jest duży, zachowanie domyślne spowoduje zbyt duży rozmiar żądania. Można dostosować rozmiar partii zapisu.

- **Rozwiązanie**: w ujścia działania kopiowania Zmniejsz wartość w polu "Zapisz rozmiar partii" (wartość domyślna to 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Komunikat o błędzie: naruszenie ograniczenia indeksu unikatowego

- **Objawy**: podczas kopiowania danych do Cosmos DB można napotkać następujący błąd:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Przyczyna**: Istnieją dwie możliwe przyczyny:

    - Jeśli używasz funkcji **Wstaw** jako zachowanie zapisu, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty o takim samym identyfikatorze.
    - Jeśli używasz **upsert** jako zachowania zapisu i ustawisz kolejny unikatowy klucz do kontenera, ten błąd oznacza, że dane źródłowe mają wiersze/obiekty z różnymi identyfikatorami, ale te same wartości zdefiniowanego unikatowego klucza.

- **Rozwiązanie**: 

    - W przypadku cause1 Ustaw **upsert** jako zachowanie zapisu.
    - W przypadku przyczyny 2 Upewnij się, że każdy dokument ma inną wartość dla zdefiniowanego unikatowego klucza.

### <a name="error-message-request-rate-is-large"></a>Komunikat o błędzie: częstotliwość żądań jest duża

- **Objawy**: podczas kopiowania danych do Cosmos DB można napotkać następujący błąd:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Przyczyna**: użyte jednostki żądania są większe niż dostępne ru skonfigurowane w Cosmos DB. Dowiedz się, jak Cosmos DB oblicza RU z tego [miejsca](../cosmos-db/request-units.md#request-unit-considerations).

- **Rozwiązanie**: Oto dwa rozwiązania:

    - **Zwiększ wartość kontenera ru** do większej w Cosmos DB, co poprawi wydajność działania kopiowania, ale powiąże się z dodatkowym kosztem Cosmos DB. 
    - Zmniejsz **writeBatchSize** do mniejszej wartości (na przykład 1000), a następnie ustaw **parallelCopies** na mniejszą wartość, taką jak 1, co spowoduje, że wydajność działania kopii będzie gorsza niż bieżąca, ale nie spowoduje to ponoszenia dodatkowych kosztów w Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Brak kolumny w mapowaniu kolumny

- **Objawy**: podczas importowania schematu dla Cosmos DB na potrzeby mapowania kolumn brakuje niektórych kolumn. 

- **Przyczyna**: ADF zawnioskuje schemat z pierwszych 10 Cosmos DB dokumentów. Jeśli niektóre kolumny/właściwości nie mają wartości w tych dokumentach, nie będą one wykrywane przez ADF, dlatego nie będą wyświetlane.

- **Rozwiązanie**: można dostosować zapytanie jak poniżej, aby wymusić wyświetlenie kolumny w zestawie wyników z wartością pustą: (Załóżmy, że w pierwszych 10 dokumentach brakuje kolumny "niemożliwe"). Alternatywnie można ręcznie dodać kolumnę do mapowania.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Komunikat o błędzie: GuidRepresentation dla czytnika to CSharpLegacy

- **Objawy**: podczas kopiowania danych z Cosmos DB MongoAPI/MongoDB z użyciem pola UUID wystąpił następujący błąd:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Przyczyna**: Istnieją dwa sposoby reprezentowania identyfikatora UUID w BSON-UuidStardard i UuidLegacy. Domyślnie UuidLegacy jest używany do odczytywania danych. Zostanie trafiony błąd, jeśli dane UUID w MongoDB jest UuidStandard.

- **Rozwiązanie**: w parametrach połączenia MongoDB Dodaj opcję "**uuidRepresentation = Standard**". Aby uzyskać więcej informacji, zobacz [MongoDB parametry połączenia](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (interfejs API SQL)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>Kod błędu: CosmosDbSqlApiOperationFailed

- **Komunikat**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Przyczyna**: Wystąpił problem z CosmosDbSqlApi operacji.

- **Zalecenie**: Sprawdź błąd w obszarze Szczegóły. Zapoznaj się z [dokumentem pomocy CosmosDb](https://docs.microsoft.com/azure/cosmos-db/troubleshoot-dot-net-sdk). Skontaktuj się z zespołem CosmosDb, jeśli potrzebujesz pomocy.


## <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Komunikat o błędzie: Połączenie podstawowe zostało zamknięte: nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS.

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
    ```

- **Przyczyna**: Walidacja certyfikatu nie powiodła się podczas UZGADNIANIA protokołu TLS.

- **Rozwiązanie**: obejście: Użyj kopii etapowej, aby pominąć weryfikację TLS dla ADLS Gen1. Trzeba odtworzyć ten problem i zebrać ślad netmon, a następnie skontaktować się z zespołem sieci, aby sprawdzić konfigurację sieci lokalnej.

    ![Rozwiązywanie problemów ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Komunikat o błędzie: serwer zdalny zwrócił błąd: (403) zabroniony

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Przyczyna**: jedną z możliwych przyczyn jest to, że główna lub zarządzana tożsamość usługi nie ma uprawnień dostępu do określonego folderu/pliku.

- **Rozwiązanie**: Przyznaj odpowiednie uprawnienia do wszystkich folderów i podfolderów, które chcesz skopiować. Zapoznaj się z [tym dokumentem](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Komunikat o błędzie: nie można uzyskać tokenu dostępu przy użyciu nazwy głównej usługi. Błąd ADAL: service_unavailable

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Przyczyna**: gdy serwer tokenów usług (STS) należący do Azure Active Directory nie jest niedostępny, czyli zbyt zajęty, aby obsługiwać żądania, zwraca błąd HTTP 503. 

- **Rozwiązanie**: ponownie uruchom działanie kopiowania po kilku minutach.


## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="error-code-adlsgen2operationfailed"></a>Kod błędu: ADLSGen2OperationFailed

- **Komunikat**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Przyczyna**: ADLS Gen2 zgłasza błąd wskazujący, że operacja nie powiodła się.

- **Zalecenie**: Sprawdź szczegółowy komunikat o błędzie zgłoszony przez ADLS Gen2. Jeśli jest to spowodowane błędem przejściowym, spróbuj ponownie. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "zabronione", nazwa główna usługi lub tożsamość zarządzana może nie mieć wystarczających uprawnień, aby uzyskać dostęp do ADLS Gen2.

- **Zalecenie**: Zapoznaj się z dokumentem pomocy: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication .

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "InternalServerError", błąd jest zwracany przez ADLS Gen2.

- **Zalecenie**: może to być spowodowane błędem przejściowym, spróbuj ponownie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>Wystąpił błąd limitu czasu żądania ADLS Gen2 konta

- **Komunikat**: kod błędu = `UserErrorFailedBlobFSOperation` , komunikat o błędzie = `BlobFS operation failed for: A task was canceled` .

- **Przyczyna**: problem jest spowodowany błędem limitu czasu ujścia ADLS Gen2, który polega głównie na samoobsługowej maszynie IR.

- **Zalecenie**: 

    - W miarę możliwości Umieść swoją maszynę podczerwieni i docelowe konto ADLS Gen2 w tym samym regionie. Pozwala to uniknąć losowego limitu czasu i zwiększyć wydajność.

    - Sprawdź, czy istnieją jakieś specjalne ustawienia sieci, takie jak ExpressRoute i upewnij się, że sieć ma wystarczającą przepustowość. Zaleca się obniżenie ustawienia samodzielnego zadania współbieżnego środowiska IR, gdy ogólna przepustowość jest niska, przez co może uniknąć konkurencji zasobów sieciowych między wieloma współbieżnymi zadaniami.

    - Użyj mniejszego rozmiaru bloku dla kopiowania niebinarnego, aby wyeliminować ten błąd limitu czasu, jeśli rozmiar pliku wynosi umiarkowany lub mały. Odwołaj się do [BLOB Storage Put Block](https://docs.microsoft.com/rest/api/storageservices/put-block).

       Aby określić rozmiar bloku niestandardowego, można edytować właściwość w edytorze JSON:
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Azure File Storage

### <a name="error-code--azurefileoperationfailed"></a>Kod błędu: AzureFileOperationFailed

- **Komunikat**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Przyczyna**: problem z trafieniem usługi Azure File Storage.

- **Zalecenie**: Sprawdź błąd w obszarze Szczegóły. Zapoznaj się z dokumentem pomocy usługi Azure File: https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes . Jeśli potrzebujesz pomocy, skontaktuj się z zespołem magazynu.


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kod błędu: SqlFailedToConnect

- **Komunikat**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Przyczyna**: Azure SQL: Jeśli komunikat o błędzie zawiera wartość "SqlErrorNumber = 47073", oznacza to, że dostęp do sieci publicznej jest zabroniony w ustawieniu łączności.

- **Zalecenie**: w zaporze Azure SQL ustaw opcję "Odmów dostępu do sieci publicznej" na wartość "nie". Dowiedz się więcej z https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access .

- **Przyczyna**: Azure SQL: Jeśli komunikat o błędzie zawiera kod błędu SQL, taki jak "SqlErrorNumber = [ErrorCode]", zapoznaj się z przewodnikiem rozwiązywania problemów z usługą Azure SQL.

- **Zalecenie**: Dowiedz się więcej z https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues .

- **Przyczyna**: Sprawdź, czy port 1433 znajduje się na liście dozwolonych zapór.

- **Zalecenie**: Postępuj zgodnie z tym dokumentem referencyjnym: https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- .

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "SqlException", SQL Database zgłasza błąd wskazujący, że określona operacja nie powiodła się.

- **Zalecenie**: Wyszukaj według kodu błędu SQL w tym dokumencie odwołania, aby uzyskać więcej szczegółów: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna**: Jeśli jest to problem przejściowy (np. niestabilne połączenie sieciowe), należy dodać polecenie ponów próbę działania, aby ograniczyć.

- **Zalecenie**: Postępuj zgodnie z tym dokumentem referencyjnym: https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy .

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "klient z adresem IP"... " nie ma dostępu do serwera "i próbujesz nawiązać połączenie z Azure SQL Database, zwykle jest to spowodowane przez Azure SQL Database problem z zaporą.

- **Zalecenie**: w konfiguracji zapory SQL Server platformy Azure Włącz opcję "Zezwalaj na dostęp do tego serwera dla usług i zasobów platformy Azure". Doc Reference: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure .


### <a name="error-code--sqloperationfailed"></a>Kod błędu: SqlOperationFailed

- **Komunikat**: `A database operation failed. Please search error to get more details.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "SqlException", SQL Database zgłasza błąd wskazujący, że określona operacja nie powiodła się.

- **Zalecenie**: Jeśli błąd SQL nie jest wyczyszczony, spróbuj zmienić bazę danych na najnowszy poziom zgodności "150". Może zgłosić najnowsze wersje programu SQL Server. Zapoznaj się z [dokumentem szczegółowym](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Aby rozwiązać problemy związane z programem SQL, wyszukaj kod błędu SQL w tym dokumencie odwołania, aby uzyskać więcej szczegółów: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera "PdwManagedToNativeInteropException", zazwyczaj jest spowodowany niezgodnością rozmiarów kolumn źródłowych i ujścia.

- **Zalecenie**: Sprawdź rozmiar kolumn źródłowych i ujścia. Jeśli potrzebujesz dalszej pomocy, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "InvalidOperationException", zazwyczaj jest to spowodowane przez nieprawidłowe dane wejściowe.

- **Zalecenie**: aby ustalić, który wiersz napotyka problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu przeprowadzenia dalszej analizy. Doc Reference: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlunauthorizedaccess"></a>Kod błędu: SqlUnauthorizedAccess

- **Komunikat**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Przyczyna**: poświadczenie jest niepoprawne lub konto logowania nie może uzyskać dostępu do SQL Database.

- **Zalecenie**: Sprawdź, czy konto logowania ma wystarczające uprawnienia dostępu do SQL Database.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kod błędu: SqlOpenConnectionTimeout

- **Komunikat**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Przyczyna**: może być SQL Database błąd przejściowy.

- **Zalecenie**: spróbuj ponownie zaktualizować parametry połączenia połączonej usługi z większą wartością limitu czasu połączenia.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kod błędu: SqlAutoCreateTableTypeMapFailed

- **Komunikat**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Przyczyna**: tabela autotworzenia nie może spełnić wymagań źródłowych.

- **Zalecenie**: zaktualizuj typ kolumny w polu "mapowania" lub ręcznie Utwórz tabelę ujścia na serwerze docelowym.


### <a name="error-code--sqldatatypenotsupported"></a>Kod błędu: SqlDataTypeNotSupported

- **Komunikat**: `A database operation failed. Check the SQL errors.`

- **Przyczyna**: Jeśli problem występuje w źródle SQL, a błąd jest związany z przepełnieniem SqlDateTime, wartość danych jest poza zakresem typów logiki (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Zalecenie**: rzutowanie typu na ciąg w źródłowym zapytaniu SQL lub w mapowaniu kolumny działania kopiowania Zmień typ kolumny na "String".

- **Przyczyna**: Jeśli problem występuje w usłudze SQL sink i błąd jest związany z przepełnieniem elementu SqlDateTime, wartość danych jest poza dozwolonym zakresem w tabeli ujścia.

- **Zalecenie**: zaktualizuj odpowiedni typ kolumny na typ "datetime2" w tabeli sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kod błędu: SqlInvalidDbStoredProcedure

- **Komunikat**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Przyczyna**: określona procedura składowana jest nieprawidłowa. Może to być spowodowane tym, że procedura składowana nie zwraca żadnych danych.

- **Zalecenie**: Sprawdź poprawność procedury składowanej przez narzędzia SQL. Upewnij się, że procedura składowana może zwracać dane.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kod błędu: SqlInvalidDbQueryString

- **Komunikat**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Przyczyna**: określone zapytanie SQL jest nieprawidłowe. Przyczyną może być to, że zapytanie nie zwraca żadnych danych

- **Zalecenie**: Sprawdź poprawność zapytania SQL przez narzędzia SQL. Upewnij się, że zapytanie może zwracać dane.


### <a name="error-code--sqlinvalidcolumnname"></a>Kod błędu: SqlInvalidColumnName

- **Komunikat**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Przyczyna**: nie można odnaleźć kolumny. Nieprawidłowa konfiguracja.

- **Zalecenie**: Sprawdź kolumnę w kwerendzie, "Structure" w zestawie danych i w działaniu.


### <a name="error-code--sqlbatchwritetimeout"></a>Kod błędu: SqlBatchWriteTimeout

- **Komunikat**: `Timeouts in SQL write operation.`

- **Przyczyna**: może być SQL Database błąd przejściowy.

- **Zalecenie**: ponów próbę. Jeśli problem Odtwórz, skontaktuj się z pomocą techniczną Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Kod błędu: SqlBatchWriteTransactionFailed

- **Komunikat**: `SQL transaction commits failed`

- **Przyczyna**: Jeśli szczegóły wyjątku stale informują o limicie czasu transakcji, opóźnienie sieci między środowiskiem Integration Runtime i bazą danych jest większe niż domyślna wartość progowa co 30 sekund.

- **Zalecenie**: zaktualizuj parametry połączenia połączonej usługi SQL z wartością "limit czasu połączenia" równą 120 lub wyższej i ponownie uruchom działanie.

- **Przyczyna**: Jeśli szczegóły wyjątku sporadycznie informują o przerwaniu usługi SqlConnection, może to być tylko przejściowy błąd sieci lub SQL Database problemu ze stroną

- **Zalecenie**: spróbuj ponownie wykonać działanie i przejrzyj metryki po stronie SQL Database.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kod błędu: SqlBulkCopyInvalidColumnLength

- **Komunikat**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Przyczyna**: Kopiowanie zbiorcze SQL nie powiodło się z powodu odebrania nieprawidłowej długości kolumny od klienta bcp.

- **Zalecenie**: aby ustalić, który wiersz napotyka problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu przeprowadzenia dalszej analizy. Doc Reference: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance .


### <a name="error-code--sqlconnectionisclosed"></a>Kod błędu: SqlConnectionIsClosed

- **Komunikat**: `The connection is closed by SQL Database.`

- **Przyczyna**: połączenie SQL jest zamknięte przez SQL Database w przypadku dużego połączenia współbieżnego przebiegu i przerwania serwera.

- **Zalecenie**: serwer zdalny zamknął połączenie SQL. Spróbuj ponownie wykonać. Jeśli problem Odtwórz, skontaktuj się z pomocą techniczną Azure SQL.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Komunikat o błędzie: konwersja nie powiodła się podczas konwertowania ciągu znaków na unikatowy identyfikator

- **Objawy**: w przypadku kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do usługi Azure Synapse Analytics przy użyciu kopiowania etapowego i wielopodstawa, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Przyczyna**: baza narzędziowa usługi Azure Synapse Analytics nie może przekonwertować pustego ciągu na identyfikator GUID.

- **Rozwiązanie**: w ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji **Użyj typu domyślnego**.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Komunikat o błędzie: Oczekiwano typu danych: DECIMAL (x, x), wartość powodująca problemy

- **Objawy**: w przypadku kopiowania danych ze źródła danych tabelarycznych (takich jak SQL Server) do usługi Azure Synapse Analytics przy użyciu kopiowania etapowego i wielopodstawa, wystąpił następujący błąd:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Przyczyna**: Baza wiedzy Azure Synapse Analytics Base nie może wstawić pustego ciągu (wartość null) do kolumny dziesiętnej.

- **Rozwiązanie**: w ujścia działania kopiowania w obszarze Ustawienia bazy danych ustaw wartość false dla opcji **Użyj typu domyślnego**.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Komunikat o błędzie: komunikat o wyjątku Java: HdfsBridge:: CreateRecordReader

- **Objawy**: skopiujesz dane do usługi Azure Synapse Analytics przy użyciu bazy danych Base i wystąpił następujący błąd:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Przyczyna**: możliwe przyczyny jest to, że schemat (całkowita szerokość kolumny) jest zbyt duży (większy niż 1 MB). Sprawdź schemat docelowej tabeli usługi Azure Synapse Analytics przez dodanie rozmiaru wszystkich kolumn:

    - Int-> 4 bajty
    - Bigint-> 8 bajtów
    - Varchar (n), char (n), Binary (n), varbinary (n)-> n bajtów
    - Nvarchar (n), nchar (n)-> n * 2 bajty
    - Data — > 6 bajtów
    - DateTime/(2), smalldatetime-> 16 bajtów
    - DateTimeOffset — > 20 bajtów
    - Liczba dziesiętna — > 19 bajtów
    - Float-> 8 bajtów
    - Money-> 8 bajtów
    - Smallmoney — > 4 bajty
    - Real-> 4 bajty
    - Smallint — > 2 bajty
    - Czas — > 12 bajtów
    - Tinyint — > 1 bajt

- **Rozwiązanie**: 
    - Zmniejsz szerokość kolumny, tak aby była mniejsza niż 1 MB.
    - Możesz też użyć metody wstawiania zbiorczego, wyłączając funkcję Polybase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Komunikat o błędzie: warunek określony przy użyciu nagłówków warunkowych HTTP nie jest spełniony

- **Objawy**: używasz zapytania SQL do ściągania danych z usługi Azure Synapse Analytics i wystąpił następujący błąd:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Przyczyna**: problem z analizą usługi Azure Synapse Analytics w tabeli zewnętrznej w usłudze Azure Storage.

- **Rozwiązanie**: Uruchom to samo zapytanie w programie SSMS i sprawdź, czy widzisz ten sam wynik. Jeśli tak, Otwórz bilet pomocy technicznej w usłudze Azure Synapse Analytics i podaj nazwę serwera usługi Azure Synapse Analytics oraz bazę danych, aby kontynuować rozwiązywanie problemów.
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>Niska wydajność podczas ładowania danych do usługi Azure SQL

- **Objawy**: kopiowanie danych w programie do usługi Azure SQL powoduje powolne działanie.

- **Przyczyna**: główna przyczyna problemu jest przede wszystkim wyzwalana przez wąskie gardło po stronie usługi Azure SQL. Poniżej przedstawiono niektóre możliwe przyczyny:

    - Warstwa usługi Azure DB nie jest wystarczająco duża.

    - Użycie jednostek DTU usługi Azure DB jest bliskie 100%. Możesz [monitorować wydajność](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) i zastanowić się nad uaktualnieniem warstwy bazy danych.

    - Indeksy nie są ustawione prawidłowo. Usuń wszystkie indeksy przed załadowaniem danych i utwórz je ponownie po zakończeniu ładowania.

    - WriteBatchSize nie jest wystarczająco duży, aby dopasować rozmiar wiersza schematu. Spróbuj powiększyć Właściwość problemu.

    - Zamiast zbiorczego wstawania, jest używana procedura składowana, która powinna mieć gorszą wydajność. 

- **Rozwiązanie**: Zapoznaj się z TSG na potrzeby [wydajności działania kopiowania](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Warstwa wydajności jest niska i prowadzi do niepowodzenia kopiowania

- **Objawy**: podczas kopiowania danych do usługi Azure SQL wystąpił następujący komunikat o błędzie: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Przyczyna**: jest używana usługa Azure SQL S1, w takim przypadku trafij limity operacji we/wy.

- **Rozwiązanie**: Uaktualnij warstwę wydajności usługi Azure SQL, aby rozwiązać ten problem. 


### <a name="sql-table-cannot-be-found"></a>Nie można znaleźć tabeli SQL 

- **Objawy**: Wystąpił błąd podczas kopiowania danych z hybrydowej do premiumej tabeli SQL Server:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Przyczyna**: bieżące konto SQL nie ma wystarczających uprawnień do wykonywania żądań wystawionych przez platformę .NET SqlBulkCopy. WriteToServer.

- **Rozwiązanie**: Przełącz na bardziej uprzywilejowane konto SQL.


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>Komunikat o błędzie: ciąg lub dane binarne zostałyby obcięte

- **Objawy**: Wystąpił błąd podczas kopiowania danych do Premium/tabeli SQL Server platformy Azure: 

- **Przyczyna**: w definicji schematu tabeli SQL w języku CX występuje co najmniej jedna kolumna o długości mniejszej niż oczekiwano.

- **Rozwiązanie**: Spróbuj wykonać następujące czynności, aby rozwiązać ten problem:

    1. Zastosuj [tolerancję błędów](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)ujścia programu SQL, szczególnie "redirectIncompatibleRowSettings", aby rozwiązać problemy z tymi wierszami.

        > [!NOTE]
        > Należy zauważyć, że odporność na uszkodzenia może spowodować dodatkowy czas wykonywania, co może prowadzić do wyższych kosztów.

    2. Aby zobaczyć, które kolumny muszą zostać zaktualizowane, należy dokładnie sprawdzić długość przekierowanych danych przy użyciu kolumny schemat tabeli SQL.

    3. Odpowiednio zaktualizuj schemat tabeli.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>Kod błędu: AzureTableDuplicateColumnsFromSource

- **Komunikat**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **Przyczyna**: może być typowy dla zapytania SQL z funkcją Join lub bez struktury plików CSV

- **Zalecenie**: dokładnie sprawdź kolumny źródłowe i popraw je.


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>Kod błędu: DB2DriverRunFailed

- **Komunikat**: `Error thrown from driver. Sql code: '%code;'`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "SQLSTATE = 51002 SQLCODE =-805", zapoznaj się z poradami w tym dokumencie: https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties

- **Zalecenie**: spróbuj ustawić "NULLID" we właściwości "packagecollection"


## <a name="delimited-text-format"></a>Format tekstu rozdzielanego

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Kod błędu: DelimitedTextColumnNameNotAllowNull

- **Komunikat**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Przyczyna**: w przypadku ustawienia opcji "użycia" w działaniu pierwszy wiersz będzie używany jako nazwa kolumny. Ten błąd oznacza, że pierwszy wiersz zawiera wartość pustą. Na przykład: "ColumnName, ColumnB".

- **Zalecenie**: zaznacz pierwszy wiersz i popraw wartość, jeśli wartość jest pusta.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Kod błędu: DelimitedTextMoreColumnsThanDefined

- **Komunikat**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Przyczyna**: liczba kolumn problematycznych wierszy jest większa niż liczba kolumn w pierwszym wierszu. Może to być spowodowane problemem z danymi lub nieprawidłowym ogranicznikiem kolumny/ustawieniami znaku cudzysłowu.

- **Zalecenie**: Pobierz liczbę wierszy w komunikacie o błędzie, Sprawdź kolumnę wiersza i popraw dane.

- **Przyczyna**: jeśli oczekiwana liczba kolumn wynosi "1" w komunikacie o błędzie, być może określono nieprawidłowe ustawienia kompresji lub formatu. W rezultacie ADF nieprawidłowo przeanalizowane pliki.

- **Zalecenie**: Sprawdź ustawienia formatu, aby upewnić się, że jest ono zgodne z plikami źródłowymi.

- **Przyczyna**: Jeśli źródło jest folderem, istnieje możliwość, że pliki w określonym folderze mają inny schemat.

- **Zalecenie**: Upewnij się, że pliki w danym folderze mają identyczny schemat.


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Kod błędu: DynamicsCreateServiceClientError

- **Komunikat**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Przyczyna**: jest to przejściowy problem po stronie serwera Dynamics.

- **Zalecenie**: Uruchom ponownie potok. Jeśli zachowanie nie powiedzie się, spróbuj zmniejszyć liczbę równoległości. Jeśli nadal nie powiedzie się, skontaktuj się z pomocą techniczną systemu Dynamics.


### <a name="columns-are-missing-when-previewingimporting-schema"></a>Brak kolumn podczas wyświetlania/importowania schematu

- **Objawy**: niektóre kolumny nie zostaną utracone podczas importowania schematu lub wyświetlania podglądu danych. Komunikat o błędzie: `The valid structure information (column name and type) are required for Dynamics source.`

- **Przyczyna**: ten problem jest zasadniczo projektem, ponieważ ADF nie jest w stanie pokazać kolumn, które nie mają wartości w pierwszych 10 rekordach. Upewnij się, że dodawane kolumny mają prawidłowy format. 

- **Zalecenie**: ręcznie Dodaj kolumny na karcie mapowanie.


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>Kod błędu: DynamicsMissingTargetForMultiTargetLookupField

- **Komunikat**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Przyczyna**: kolumna docelowa nie istnieje w obiekcie źródłowym ani w mapowaniu kolumn.

- **Zalecenie**: 1. Upewnij się, że źródło zawiera kolumnę docelową. 2. Dodaj kolumnę Target w mapowaniu kolumn. Upewnij się, że kolumna ujścia jest w wzorcu "{FieldName} @EntityReference ".


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>Kod błędu: DynamicsInvalidTargetForMultiTargetLookupField

- **Komunikat**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **Przyczyna**: podano nieprawidłową nazwę jednostki jako jednostkę docelową dla wielowymiarowego pola wyszukiwania.

- **Zalecenie**: Podaj prawidłową nazwę jednostki dla wielowymiarowego pola wyszukiwania.


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>Kod błędu: DynamicsInvalidTypeForMultiTargetLookupField

- **Komunikat**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Przyczyna**: wartość w kolumnie docelowej nie jest ciągiem

- **Zalecenie**: Podaj prawidłowy ciąg w kolumnie cel wyszukiwania wielowymiarowego.


### <a name="error-code--dynamicsfailedtorequetserver"></a>Kod błędu: DynamicsFailedToRequetServer

- **Komunikat**: `The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **Przyczyna**: serwer Dynamics jest niestabilny lub niedostępny lub wystąpił problem z siecią.

- **Zalecenie**: sprawdź łączność sieciową lub Sprawdź dziennik programu Dynamics Server, aby uzyskać więcej szczegółów. Skontaktuj się z pomocą techniczną w celu uzyskania dalszej pomocy.


## <a name="excel-format"></a>Format programu Excel

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Przekroczenie limitu czasu lub niska wydajność podczas analizowania dużego pliku programu Excel

- **Objawy**:

    - Podczas tworzenia zestawu danych programu Excel i importowania schematu z usługi Connect/Store, podglądu danych, listy lub odświeżania arkuszy można napotkać błąd limitu czasu, jeśli rozmiar pliku programu Excel jest duży.

    - W przypadku używania działania kopiowania do kopiowania danych z dużego pliku programu Excel (>= 100 MB) do innych magazynów danych może wystąpić problem z niską wydajnością lub OOM.

- **Przyczyna**: 

    - W przypadku operacji, takich jak importowanie schematu, Podgląd danych i wyświetlanie listy arkuszy danych programu Excel, limit czasu to 100 s i static. W przypadku dużych plików programu Excel te operacje mogą nie kończyć się wartością limitu czasu.

    - Działanie kopiowania ADF odczytuje cały plik programu Excel do pamięci, a następnie lokalizuje określony arkusz i komórki do odczytu danych. To zachowanie jest spowodowane użyciem bazowego ADF zestawu SDK.

- **Rozwiązanie**: 

    - W przypadku importowania schematu można wygenerować mniejszy przykładowy plik, który jest podzbiorem oryginalnego pliku, a następnie wybrać opcję "Importuj schemat z pliku przykładowego" zamiast "Importuj schemat z połączenia/magazynu".

    - W przypadku arkusza z listą, na liście rozwijanej arkusza można kliknąć pozycję "Edytuj" i wprowadzić zamiast niej nazwę/indeks arkusza.

    - Aby skopiować duży plik programu Excel (>100 MB) do innego sklepu, możesz użyć źródła programu Excel dotyczącego przepływu danych, które umożliwia odczytanie i przeprowadzenie przesyłania strumieniowego przez Sport.
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>Kod błędu: FtpFailedToConnectToFtpServer

- **Komunikat**: `Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **Przyczyna**: nieprawidłowy typ połączonej usługi może być używany dla serwera FTP, na przykład przy użyciu połączonej usługi SFTP do łączenia się z serwerem FTP.

- **Zalecenie**: Sprawdź port serwera docelowego. Domyślnie usługa FTP używa portu 21.


## <a name="http"></a>HTTP

### <a name="error-code--httpfilefailedtoread"></a>Kod błędu: HttpFileFailedToRead

- **Komunikat**: `Failed to read data from http server. Check the error from http server：%message;`

- **Przyczyna**: ten błąd występuje, gdy Azure Data Factory porozmawiać z serwerem HTTP, ale operacja żądania HTTP kończy się niepowodzeniem.

- **Zalecenie**: Sprawdź kod stanu HTTP \ komunikat w komunikacie o błędzie i rozwiąż problem z serwerem zdalnym.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Kod błędu: wyjątku ArgumentOutOfRangeException

- **Komunikat**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Przyczyna**: w podajniku ADF wartości typu datetime są obsługiwane w zakresie od 0001-01-01 00:00:00 do 9999-12-31 23:59:59. Jednak Oracle obsługuje szerszy zakres wartości daty i godziny (na przykład BC lub min/s>59), co prowadzi do błędu w ramach ADF.

- **Zalecenie**: 

    Uruchom `select dump(<column name>)` , aby sprawdzić, czy wartość w programie Oracle jest w zakresie ADF. 

    Jeśli chcesz znać sekwencję bajtów w wyniku, sprawdź https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle .


## <a name="orc-format"></a>Format Orc

### <a name="error-code--orcjavainvocationexception"></a>Kod błędu: OrcJavaInvocationException

- **Komunikat**: `An error occurred when invoking java, message: %javaException;.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera "Java. lang. OutOfMemory", "Space sterty Java" i "doubleCapacity", zazwyczaj jest to problem z zarządzaniem pamięcią w starej wersji środowiska Integration Runtime.

- **Zalecenie**: Jeśli używasz samoobsługowego Integration Runtime, Sugeruj uaktualnienie do najnowszej wersji.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera plik "Java. lang. OutOfMemory", środowisko Integration Runtime nie ma wystarczającej ilości zasobów, aby przetworzyć pliki.

- **Zalecenie**: Ogranicz współbieżne uruchomienia w środowisku Integration Runtime. W przypadku samodzielnej Integration Runtime skalowanie w górę do zaawansowanej maszyny z pamięcią równą lub większą niż 8 GB.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "NullPointerReference", możliwe, że jest to błąd przejściowy.

- **Zalecenie**: ponów próbę. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "BufferOverflowException", możliwe, że jest to błąd przejściowy.

- **Zalecenie**: ponów próbę. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera "Java. lang. ClassCastException:org. Apache. Hadoop. Hive. serde2. IO. HiveCharWritable nie można rzutować na typ org. Apache. Hadoop. IO. text", jest to błąd konwersji typu w środowisku uruchomieniowym języka Java. Zazwyczaj przyczyną jest to, że dane źródłowe nie mogą być prawidłowo obsługiwane w środowisku uruchomieniowym języka Java.

- **Zalecenie**: ten problem dotyczący danych. Spróbuj użyć ciągu zamiast char/varchar w danych formatu ORC.

### <a name="error-code--orcdatetimeexceedlimit"></a>Kod błędu: OrcDateTimeExceedLimit

- **Komunikat**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Przyczyna**: Jeśli wartość daty i godziny to "0001-01-01 00:00:00", może to być spowodowane różnicą między kalendarzem juliańskim i kalendarzem gregoriańskim. https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **Zalecenie**: Sprawdź wartość Takty i Unikaj używania wartości datetime "0001-01-01 00:00:00".


## <a name="parquet-format"></a>Format Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Kod błędu: ParquetJavaInvocationException

- **Komunikat**: `An error occurred when invoking java, message: %javaException;.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera "Java. lang. OutOfMemory", "Space sterty Java" i "doubleCapacity", zazwyczaj jest to problem z zarządzaniem pamięcią w starej wersji środowiska Integration Runtime.

- **Zalecenie**: Jeśli korzystasz z samodzielnej Integration Runtime, a wersja jest wcześniejsza niż 3.20.7159.1, zaleca się uaktualnienie do najnowszej wersji.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera plik "Java. lang. OutOfMemory", środowisko Integration Runtime nie ma wystarczającej ilości zasobów, aby przetworzyć pliki.

- **Zalecenie**: Ogranicz współbieżne uruchomienia w środowisku Integration Runtime. W przypadku samodzielnej Integration Runtime skalowanie w górę do zaawansowanej maszyny z pamięcią równą lub większą niż 8 GB.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "NullPointerReference", możliwe, że jest to błąd przejściowy.

- **Zalecenie**: ponów próbę. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.


### <a name="error-code--parquetinvalidfile"></a>Kod błędu: ParquetInvalidFile

- **Komunikat**: `File is not a valid Parquet file.`

- **Przyczyna**: problem z plikiem Parquet.

- **Zalecenie**: Sprawdź, czy dane wejściowe są prawidłowym plikiem Parquet.


### <a name="error-code--parquetnotsupportedtype"></a>Kod błędu: ParquetNotSupportedType

- **Komunikat**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Przyczyna**: format Parquet nie jest obsługiwany w Azure Data Factory.

- **Zalecenie**: dokładnie sprawdź dane źródłowe. Zapoznaj się z dokumentem: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Kod błędu: ParquetMissedDecimalPrecisionScale

- **Komunikat**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Przyczyna**: Spróbuj przeanalizować dokładność i skalę liczby, ale nie ma takich informacji.

- **Zalecenie**: element "source" nie zwraca prawidłowych dokładności i skali. Sprawdź precyzję i skalę kolumny problemu.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Kod błędu: ParquetInvalidDecimalPrecisionScale

- **Komunikat**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Przyczyna**: schemat jest nieprawidłowy.

- **Zalecenie**: Sprawdź precyzję i skalę kolumny problemu.


### <a name="error-code--parquetcolumnnotfound"></a>Kod błędu: ParquetColumnNotFound

- **Komunikat**: `Column %column; does not exist in Parquet file.`

- **Przyczyna**: schemat źródłowy jest niezgodny ze schematem ujścia.

- **Zalecenie**: Sprawdź the'mappings "w" Activity ". Upewnij się, że kolumna źródłowa może być zmapowana do prawej kolumny ujścia.


### <a name="error-code--parquetinvaliddataformat"></a>Kod błędu: ParquetInvalidDataFormat

- **Komunikat**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Przyczyna**: nie można przekonwertować danych na typ określony w mapowaniu. Źródło

- **Zalecenie**: należy dokładnie sprawdzić dane źródłowe lub określić poprawny typ danych dla tej kolumny w mapowaniu kolumny działania kopiowania. Zapoznaj się z dokumentem: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Kod błędu: ParquetDataCountNotMatchColumnCount

- **Komunikat**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Przyczyna**: niezgodność liczby kolumn źródłowych i liczby kolumn ujścia

- **Zalecenie**: liczba kolumn źródłowych sprawdzania podwójnego jest taka sama jak liczba kolumn ujścia w elemencie "mapowanie".


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Kod błędu: ParquetDataTypeNotMatchColumnType

- **Komunikat**: typ danych% srcType; nie jest zgodny z podanym typem kolumny% dstType;. w kolumnie "% columnIndex;".

- **Przyczyna**: dane ze źródła nie mogą zostać skonwertowane na typ zdefiniowany w ujścia

- **Zalecenie**: Określ poprawny typ w mapowaniu. sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Kod błędu: ParquetBridgeInvalidData

- **Komunikat**: `%message;`

- **Przyczyna**: wartość danych przekracza ograniczenie

- **Zalecenie**: ponów próbę. Jeśli problem będzie się powtarzać, skontaktuj się z nami.


### <a name="error-code--parquetunsupportedinterpretation"></a>Kod błędu: ParquetUnsupportedInterpretation

- **Komunikat**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Przyczyna**: nieobsługiwany scenariusz

- **Zalecenie**: element "ParquetInterpretFor" nie może być "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Kod błędu: ParquetUnsupportFileLevelCompressionOption

- **Komunikat**: `File level compression is not supported for Parquet.`

- **Przyczyna**: nieobsługiwany scenariusz

- **Zalecenie**: Usuń element "CompressionType" w ładunku.


### <a name="error-code--usererrorjniexception"></a>Kod błędu: UserErrorJniException

- **Komunikat**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Przyczyna**: nie można utworzyć JVM, ponieważ ustawiono pewne niedozwolone (globalne) argumenty.

- **Zalecenie**: Zaloguj się do komputera, który hostuje **każdy węzeł** własnego środowiska IR. Sprawdź, czy zmienna systemowa jest prawidłowo ustawiona w następujący sposób: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Uruchom ponownie wszystkie węzły IR, a następnie uruchom ponownie potok.


### <a name="arithmetic-overflow"></a>Przepełnienie arytmetyczne

- **Objawy**: podczas kopiowania plików Parquet wystąpił komunikat o błędzie: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Przyczyna**: obecnie w przypadku kopiowania plików z programu Oracle do Parquet jest obsługiwana tylko liczba dziesiętna precyzji <= 38 i długość części całkowitej <= 20. 

- **Rozwiązanie**: można skonwertować kolumny z takim problemem na varchar2 jako obejście.


### <a name="no-enum-constant"></a>Brak stałej wyliczenia

- **Objawy**: Wystąpił komunikat o błędzie podczas kopiowania danych do formatu Parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , lub: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Przyczyna**: 

    Przyczyną problemu może być spacje lub nieobsługiwane znaki (np.). {} () \n\t =) w nazwie kolumny, ponieważ Parquet nie obsługuje tego formatu. 

    Na przykład nazwa kolumny, taka jak *contoso (test)* , będzie analizować typ w nawiasach klamrowych z [kodu](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Ten błąd zostanie podniesiony, ponieważ nie ma takiego typu "test".

    Aby sprawdzić obsługiwane typy, możesz je sprawdzić [tutaj](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Rozwiązanie**: 

    - Sprawdź podwójne znaki w nazwie kolumny ujścia.

    - Sprawdź podwójne, czy pierwszy wiersz z białymi znakami jest używany jako nazwa kolumny.

    - Sprawdź, czy typ Oryginalnytype jest obsługiwany. Spróbuj uniknąć tych specjalnych symboli `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>Kod błędu: RestSinkCallFailed

- **Komunikat**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Przyczyna**: ten błąd występuje, gdy Azure Data Factory komunikować się z punktem końcowym REST za pośrednictwem protokołu HTTP i operacja żądania nie powiedzie się.

- **Zalecenie**: Sprawdź kod stanu HTTP \ komunikat w komunikacie o błędzie i rozwiąż problem z serwerem zdalnym.

### <a name="unexpected-network-response-from-rest-connector"></a>Nieoczekiwana odpowiedź sieciowa z łącznika REST

- **Objawy**: punkt końcowy czasami otrzymuje nieoczekiwaną odpowiedź (400/401/403/500) z łącznika Rest.

- **Przyczyna**: Łącznik źródła REST używa adresu URL i metody/nagłówka/treści http z połączonej usługi/zestawu danych/Kopiuj źródło jako parametry podczas konstruowania żądania HTTP. Problem jest najprawdopodobniej spowodowany przez pewne błędy w co najmniej jednym z określonych parametrów.

- **Rozwiązanie**: 
    - Użyj opcji "zwinięcie" w oknie cmd, aby sprawdzić, czy parametr jest przyczyną lub nie (należy zawsze uwzględniać nagłówki "**Akceptuj** i **User-Agent"** ):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Jeśli polecenie zwróci tę samą nieoczekiwaną odpowiedź, Popraw powyższe parametry do momentu, aż zwróci oczekiwaną odpowiedź. 

      Ponadto można użyć "zwinięcie--Help", aby bardziej zaawansowane użycie polecenia.

    - Jeśli tylko łącznik REST ADF zwróci nieoczekiwaną odpowiedź, skontaktuj się z pomocą techniczną firmy Microsoft w celu dalszej rozwiązywania problemów.
    
    - Należy pamiętać, że "zwinięcie" może nie być odpowiednie do odtworzenia problemu weryfikacji certyfikatu SSL. W niektórych scenariuszach polecenie "zwinięcie" zostało wykonane pomyślnie bez przekroczenia jakiegokolwiek problemu weryfikacji certyfikatu SSL. Ale jeśli ten sam adres URL jest wykonywany w przeglądarce, w pierwszym miejscu nie zostanie zwrócony żaden certyfikat SSL, aby klient mógł ustanowić relację zaufania z serwerem.

      Narzędzia, takie jak **Poster** i **programu Fiddler** , są zalecane w przypadku powyższego przypadku.


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>Kod błędu: SftpOperationFail

- **Komunikat**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Przyczyna**: problem z trafieniem SFTP.

- **Zalecenie**: Sprawdź szczegóły błędu z protokołu SFTP.


### <a name="error-code--sftprenameoperationfail"></a>Kod błędu: SftpRenameOperationFail

- **Komunikat**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Przyczyna**: serwer SFTP nie obsługuje zmiany nazwy pliku tymczasowego.

- **Zalecenie**: Ustaw "useTempFileRename" jako FAŁSZ w ujścia kopiowania, aby wyłączyć przekazywanie do pliku tymczasowego.


### <a name="error-code--sftpinvalidsftpcredential"></a>Kod błędu: SftpInvalidSftpCredential

- **Komunikat**: `Invalid Sftp credential provided for '%type;' authentication type.`

- **Przyczyna**: zawartość klucza prywatnego jest pobierana z AKV/SDK, ale nie została poprawnie zaszyfrowana.

- **Zalecenie**:  

    Jeśli zawartość klucza prywatnego pochodzi z AKV, a oryginalny plik klucza może być działał, jeśli klient przekaże go bezpośrednio do połączonej usługi SFTP

    Zapoznaj się z https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication zawartością PrivateKey jest zakodowana w formacie base64 zawartość klucza prywatnego SSH.

    Zakoduj **całą zawartość oryginalnego pliku klucza prywatnego** z kodowaniem Base64 i Zapisz zakodowany ciąg do AKV. Oryginalny plik klucza prywatnego to ten, który może współdziałać z połączoną usługą SFTP po kliknięciu przycisku Przekaż z pliku.

    Oto przykłady używane do generowania ciągu:

    - Użyj kodu w języku C#:
    ```
    byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
    string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
    ```

    - Użyj kodu języka Python:
    ```
    import base64
    rfd = open(r'{Private Key Path}', 'rb')
    keyContent = rfd.read()
    rfd.close()
    print base64.b64encode(Key Content)
    ```

    - Korzystanie z narzędzia konwersji Base64 innej firmy

        https://www.base64encode.org/Zalecane są narzędzia.

- **Przyczyna**: Wybrano niewłaściwy format zawartości klucza

- **Zalecenie**:  

    Format PKCS # 8 SSH klucz prywatny (Rozpocznij od "-----rozpoczęcia szyfrowanego klucza prywatnego-----") nie jest obecnie obsługiwany w celu uzyskania dostępu do serwera SFTP w usłudze ADF. 

    Uruchom poniższe polecenia, aby skonwertować klucz na tradycyjny format klucza SSH (Zacznij od "-----Rozpocznij klucz prywatny RSA-----"):

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Przyczyna**: nieprawidłowa zawartość poświadczeń lub klucza prywatnego

- **Zalecenie**: podwójne sprawdzenie przy użyciu narzędzi, takich jak WinSCP, aby sprawdzić, czy plik klucza lub hasło są poprawne.

### <a name="sftp-copy-activity-failed"></a>Działanie kopiowania SFTP nie powiodło się

- **Objawy**: kod błędu: UserErrorInvalidColumnMappingColumnNotFound. Komunikat o błędzie: `Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **Przyczyna**: Źródło nie zawiera kolumny o nazwie "AccMngr".

- **Rozwiązanie**: dokładnie sprawdź, jak zestaw danych został skonfigurowany przez mapowanie kolumny docelowy zestaw danych, aby potwierdzić, czy jest to kolumna "AccMngr".


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>Kod błędu: SftpFailedToConnectToSftpServer

- **Komunikat**: `Failed to connect to Sftp server '%server;'.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera limit czasu operacji odczytu gniazda po 30000 milisekundach, jedną z możliwych przyczyn jest to, że w przypadku serwera SFTP jest używany niewłaściwy typ połączonej usługi, na przykład przy użyciu połączonej usługi FTP do nawiązania połączenia z serwerem SFTP.

- **Zalecenie**: Sprawdź port serwera docelowego. Domyślnie protokół SFTP używa portu 22.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera wartość "odpowiedź serwera nie zawiera identyfikacji protokołu SSH", jedną z możliwych przyczyn jest to, że serwer SFTP ograniczy połączenie. Funkcja ADF utworzy wiele połączeń do pobrania z serwera SFTP równolegle, a czasami spowoduje to natrafienie ograniczenia serwera SFTP. Praktycznie inny serwer zwróci inny błąd, gdy zostanie osiągnięty ograniczenie przepustowości.

- **Zalecenie**:  

    Określ maksymalną liczbę równoczesnych połączeń zestawu danych SFTP z 1 i ponownie uruchom kopię. Jeśli zajdzie taka sukces, możemy upewnić się, że ograniczenie jest przyczyną.

    Jeśli chcesz podnieść niską przepływność, skontaktuj się z administratorem protokołu SFTP, aby zwiększyć limit liczby połączeń współbieżnych, lub Dodaj następujący adres IP do listy dozwolonych:

    - Jeśli używasz zarządzanego środowiska IR, Dodaj [zakresy adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653).
      Możesz też zainstalować własne środowisko IR, jeśli nie chcesz dodawać dużej listy zakresów adresów IP do listy dozwolonych serwerów SFTP.

    - Jeśli używasz własnego środowiska IR, Dodaj do listy dozwolony adres IP komputera, na którym zainstalowano SHIR.


## <a name="sharepoint-online-list"></a>Lista usługi SharePoint Online

### <a name="error-code--sharepointonlineauthfailed"></a>Kod błędu: SharePointOnlineAuthFailed

- **Komunikat**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Przyczyna**: identyfikator jednostki usługi i klucz może być niepoprawnie ustawiony.

- **Zalecenie**: Sprawdź zarejestrowaną aplikację (identyfikator jednostki usługi) i klucz, czy jest on poprawnie ustawiony.


## <a name="xml-format"></a>Format XML

### <a name="error-code--xmlsinknotsupported"></a>Kod błędu: XmlSinkNotSupported

- **Komunikat**: `Write data in xml format is not supported yet, please choose a different format!`

- **Przyczyna**: użyto zestawu danych XML jako elementu ujścia w działaniu kopiowania

- **Zalecenie**: Użyj zestawu danych w innym formacie jako ujścia kopiowania.


### <a name="error-code--xmlattributecolumnnameconflict"></a>Kod błędu: XmlAttributeColumnNameConflict

- **Komunikat**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Przyczyna**: użyto prefiksu atrybutu, który spowodował konflikt.

- **Zalecenie**: ustaw inną wartość właściwości "attributePrefix".


### <a name="error-code--xmlvaluecolumnnameconflict"></a>Kod błędu: XmlValueColumnNameConflict

- **Komunikat**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Przyczyna**: użyto jednej z nazw elementów podrzędnych jako nazwy kolumny dla wartości elementu.

- **Zalecenie**: ustaw inną wartość właściwości "valueColumn".


### <a name="error-code--xmlinvalid"></a>Kod błędu: xmlnieprawidłowy

- **Komunikat**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Przyczyna**: wejściowy plik XML nie jest poprawnie sformułowany.

- **Zalecenie**: Popraw plik XML, aby był poprawnie sformułowany.


## <a name="general-copy-activity-error"></a>Ogólny błąd działania kopiowania

### <a name="error-code--jrenotfound"></a>Kod błędu: JreNotFound

- **Komunikat**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Przyczyna**: własne środowisko Integration Runtime nie może znaleźć środowiska uruchomieniowego języka Java. Środowisko uruchomieniowe Java jest wymagane do odczytywania określonego źródła.

- **Zalecenie**: Sprawdź środowisko Integration Runtime, doc Reference: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Kod błędu: WildcardPathSinkNotSupported

- **Komunikat**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Przyczyna**: zestaw danych ujścia nie obsługuje symboli wieloznacznych.

- **Zalecenie**: Sprawdź zestaw danych ujścia i popraw ścieżkę bez symbolu wieloznacznego.


### <a name="fips-issue"></a>Problem ze standardem FIPS

- **Objawy**: działanie kopiowania kończy się niepowodzeniem na samohostowanej Integration Runtime komputerze z włączoną obsługą FIPS z komunikatem o błędzie `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` . Może się tak zdarzyć podczas kopiowania danych z łącznikami, takimi jak Azure Blob, SFTP itp.

- **Przyczyna**: FIPS (Federal Information Processing Standards) definiuje określony zestaw algorytmów kryptograficznych, które mogą być używane. Gdy tryb FIPS jest włączony na komputerze, niektóre klasy kryptograficzne, od których zależy działanie kopiowania, są blokowane w niektórych scenariuszach.

- **Rozwiązanie**: informacje na temat bieżącej sytuacji w trybie FIPS w systemie Windows można znaleźć w [artykule](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)i oszacować, czy można wyłączyć tryb FIPS na samohostowanej Integration Runtime komputerze.

    Z drugiej strony, jeśli chcesz, aby Azure Data Factory pomijać FIPS i spowodować pomyślne uruchomienie działania, możesz wykonać następujące czynności:

    1. Otwórz folder, w którym jest zainstalowany samoobsługowy Integration Runtime, zwykle poniżej `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` .

    2. Otwórz pozycję "diawp.exe.config", Dodaj `<enforceFIPSPolicy enabled="false"/>` do `<runtime>` sekcji w następujący sposób.

        ![Wyłącz standard FIPS](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Uruchom ponownie samodzielną maszynę Integration Runtime.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona pytania&pytań i odpowiedzi](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
