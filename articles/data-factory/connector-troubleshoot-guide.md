---
title: Rozwiązywanie problemów z łącznikami Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z łącznikiem w Azure Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: fdc4bbd463c45fecfc9e3961e42f81ed93d820ae
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054640"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Rozwiązywanie problemów z łącznikami Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule opisano typowe sposoby rozwiązywania problemów z łącznikami Azure Data Factory.
  
## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Kod błędu: AzureBlobOperationFailed

- **Komunikat**: "operacja obiektu BLOB nie powiodła się. ContainerName:% ContainerName;, ścieżka:% Path;. "

- **Przyczyna**: Wystąpił problem z BLOB Storage operacji.

- **Zalecenie**: Aby sprawdzić szczegóły błędu, zobacz [BLOB Storage kodów błędów](https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes). Aby uzyskać pomoc, skontaktuj się z zespołem Blob Storage.


### <a name="invalid-property-during-copy-activity"></a>Nieprawidłowa właściwość w trakcie działania kopiowania

- **Komunikat**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Przyczyna**: typ zdefiniowany w zestawie danych jest niespójny ze źródłem lub typem ujścia zdefiniowanym w działaniu kopiowania.

- **Rozwiązanie**: Edytuj definicję zestawu danych lub pliku JSON potoku, aby zapewnić spójność typów, a następnie ponownie uruchom wdrożenie.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Komunikat o błędzie: rozmiar żądania jest zbyt duży

- **Objawy**: podczas kopiowania danych do Azure Cosmos dB przy użyciu domyślnego rozmiaru partii zapisu zostanie wyświetlony następujący błąd: `Request size is too large.`

- **Przyczyna**: Azure Cosmos DB ogranicza rozmiar pojedynczego żądania do 2 MB. Formuła jest *rozmiarem żądania = rozmiar pojedynczego dokumentu \* zapis wsadu*. Jeśli rozmiar dokumentu jest duży, zachowanie domyślne spowoduje, że rozmiar żądania jest zbyt duży. Można dostosować rozmiar partii zapisu.

- **Rozwiązanie**: w ujścia działania kopiowania Zmniejsz wartość *rozmiaru partii zapisu* (wartość domyślna to 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Komunikat o błędzie: naruszenie ograniczenia indeksu unikatowego

- **Objawy**: podczas kopiowania danych do Azure Cosmos DB pojawia się następujący błąd:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Przyczyna**: Istnieją dwie możliwe przyczyny:

    - Przyczyna 1: Jeśli używasz instrukcji **INSERT** jako zachowanie zapisu, ten błąd oznacza, że dane źródłowe mają wiersze lub obiekty o tym samym identyfikatorze.
    - Przyczyna 2: Jeśli używasz **upsert** jako zachowania do zapisu i ustawisz kolejny unikatowy klucz dla kontenera, ten błąd oznacza, że dane źródłowe mają wiersze lub obiekty z różnymi identyfikatorami, ale te same wartości dla zdefiniowanego unikatowego klucza.

- **Rozwiązanie**: 

    - Dla przyczyny 1 Ustaw **upsert** jako zachowanie zapisu.
    - W przypadku przyczyny 2 Upewnij się, że każdy dokument ma inną wartość dla zdefiniowanego unikatowego klucza.

### <a name="error-message-request-rate-is-large"></a>Komunikat o błędzie: częstotliwość żądań jest duża

- **Objawy**: podczas kopiowania danych do Azure Cosmos DB pojawia się następujący błąd:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Przyczyna**: Liczba użytych jednostek żądań (jednostek ru) jest większa niż dostępna jednostek ru skonfigurowana w Azure Cosmos DB. Aby dowiedzieć się, jak Azure Cosmos DB oblicza jednostek ru, zobacz [jednostki żądań w Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Rozwiązanie**: Wypróbuj jedno z następujących dwóch rozwiązań:

    - Zwiększ liczbę *jednostek ru kontenera* do większej wartości w Azure Cosmos DB. To rozwiązanie poprawi wydajność działania kopiowania, ale powiąże się z dodatkowymi kosztami Azure Cosmos DB. 
    - Zmniejsz *writeBatchSize* do mniejszej wartości, na przykład 1000, i zmniejsz *parallelCopies* do mniejszej wartości, na przykład 1. To rozwiązanie zmniejszy wydajność uruchamiania kopii, ale nie spowoduje ponoszenia dodatkowych kosztów w Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Brak kolumn w mapowaniu kolumn

- **Objawy**: podczas importowania schematu dla Azure Cosmos DB na potrzeby mapowania kolumn brakuje niektórych kolumn. 

- **Przyczyna**: Data Factory wnioskuje schemat z pierwszych 10 Azure Cosmos DB dokumentów. Jeśli niektóre kolumny lub właściwości dokumentu nie zawierają wartości, schemat nie zostanie wykryty przez Data Factory i w związku z tym nie jest wyświetlany.

- **Rozwiązanie**: można dostosować zapytanie, jak pokazano w poniższym kodzie, aby wymusić Wyświetlanie wartości kolumn w zestawie wyników z pustymi wartościami. Załóżmy, że w pierwszych 10 dokumentach brakuje *niemożliwej* kolumny. Alternatywnie można ręcznie dodać kolumnę do mapowania.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Komunikat o błędzie: GuidRepresentation dla czytnika to CSharpLegacy

- **Objawy**: podczas kopiowania danych z Azure Cosmos DB MongoAPI lub MongoDB przy użyciu pola identyfikatora uniwersalnego (UUID) pojawia się następujący błąd:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Przyczyna**: Istnieją dwa sposoby reprezentowania identyfikatora UUID w binarnym kodzie JSON (BSON): UuidStardard i UuidLegacy. Domyślnie UuidLegacy jest używany do odczytywania danych. Zostanie wyświetlony komunikat o błędzie, jeśli dane identyfikatora UUID w MongoDB to UuidStandard.

- **Rozwiązanie**: w parametrach połączenia MongoDB Dodaj opcję *uuidRepresentation = Standard* . Aby uzyskać więcej informacji, zobacz [MongoDB parametry połączenia](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (interfejs API SQL)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>Kod błędu: CosmosDbSqlApiOperationFailed

- **Komunikat**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Przyczyna**: problem z operacją CosmosDbSqlApi.

- **Zalecenie**: Aby sprawdzić szczegóły błędu, zobacz [dokument pomocy Azure Cosmos DB](../cosmos-db/troubleshoot-dot-net-sdk.md). Aby uzyskać pomoc, skontaktuj się z zespołem Azure Cosmos DB.

## <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Komunikat o błędzie: Połączenie podstawowe zostało zamknięte: nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS.

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Przyczyna**: Walidacja certyfikatu zakończyła się niepowodzeniem podczas UZGADNIANIA protokołu TLS.

- **Rozwiązanie**: jako obejście Użyj kopii przygotowanej, aby pominąć weryfikację Transport Layer Security (TLS) dla Azure Data Lake Storage Gen1. Należy odtworzyć ten problem i zebrać ślad Monitora sieci (netmon), a następnie skontaktować się z zespołem sieci, aby sprawdzić konfigurację sieci lokalnej.

    ![Diagram Azure Data Lake Storage Gen1 połączeń w celu rozwiązywania problemów.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Komunikat o błędzie: serwer zdalny zwrócił błąd: (403) zabroniony

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Przyczyna**: jedną z możliwych przyczyn jest to, że główna lub zarządzana tożsamość usługi nie ma uprawnień dostępu do określonych folderów lub plików.

- **Rozwiązanie**: Przyznaj odpowiednie uprawnienia do wszystkich folderów i podfolderów, które chcesz skopiować. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych do lub z Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Komunikat o błędzie: nie można uzyskać tokenu dostępu przy użyciu nazwy głównej usługi. Błąd ADAL: service_unavailable

- **Objawy**: działanie kopiowania kończy się niepowodzeniem z powodu następującego błędu:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Przyczyna**: gdy serwer tokenów usług (STS), którego właścicielem jest Azure Active Directory, jest niedostępny, oznacza to, że jest zbyt zajęty, aby obsługiwać żądania i zwraca błąd HTTP 503. 

- **Rozwiązanie**: ponownie uruchom działanie kopiowania po kilku minutach.


## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="error-code-adlsgen2operationfailed"></a>Kod błędu: ADLSGen2OperationFailed

- **Komunikat**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Przyczyna**: Jeśli Azure Data Lake Storage Gen2 zgłasza ten błąd, operacja zakończyła się niepowodzeniem.

- **Zalecenie**: Sprawdź szczegółowy komunikat o błędzie zgłoszony przez Azure Data Lake Storage Gen2. Jeśli błąd jest przejściowy, spróbuj ponownie wykonać operację. Aby uzyskać pomoc, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "zabronione", nazwa główna usługi lub zarządzana tożsamość może nie mieć wystarczających uprawnień dostępu Azure Data Lake Storage Gen2.

- **Zalecenie**: Aby rozwiązać ten problem, zobacz [kopiowanie i Przekształcanie danych w Azure Data Lake Storage Gen2 przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication).

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "InternalServerError", błąd jest zwracany przez Azure Data Lake Storage Gen2.

- **Zalecenie**: błąd może być spowodowany przez przejściowy błąd. Jeśli jest, spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie.

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Żądanie Azure Data Lake Storage Gen2 konta spowodowało błąd przekroczenia limitu czasu

- **Komunikat**: 
  * Kod błędu = `UserErrorFailedBlobFSOperation`
  * Komunikat o błędzie = `BlobFS operation failed for: A task was canceled.`

- **Przyczyna**: problem jest spowodowany błędem limitu czasu ujścia Azure Data Lake Storage Gen2, który zwykle występuje w przypadku samodzielnej maszyny Integration Runtime (IR).

- **Zalecenie**: 

    - W tym samym regionie należy umieścić własne hostowane urządzenie IR i docelowe konto Azure Data Lake Storage Gen2, jeśli jest to możliwe. Może to pomóc uniknąć losowego limitu czasu i uzyskać lepszą wydajność.

    - Sprawdź, czy istnieje specjalne ustawienie sieci, takie jak ExpressRoute, i upewnij się, że sieć ma wystarczającą przepustowość. Sugerujemy obniżenie ustawienia współbieżnych zadań obsługiwanych przez środowisko IR, gdy ogólna przepustowość jest niska. Wykonanie tej czynności może pomóc w uniknięciu konkurencji zasobów sieciowych między wieloma współbieżnymi zadaniami.

    - Jeśli rozmiar pliku jest umiarkowany lub mały, użyj mniejszego rozmiaru bloku dla kopiowania niebinarnego, aby zmniejszyć ten błąd. Aby uzyskać więcej informacji, zobacz [BLOB Storage Put Block](/rest/api/storageservices/put-block).

       Aby określić niestandardowy rozmiar bloku, Edytuj właściwość w edytorze plików JSON, jak pokazano poniżej:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Magazyn Azure Files

### <a name="error-code--azurefileoperationfailed"></a>Kod błędu: AzureFileOperationFailed

- **Komunikat**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Przyczyna**: Wystąpił problem z operacją Azure Files Storage.

- **Zalecenie**: Aby sprawdzić szczegóły błędu, zobacz [Pomoc Azure Files](https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes). Aby uzyskać pomoc, skontaktuj się z zespołem Azure Files.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database i SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Kod błędu: SqlFailedToConnect

- **Komunikat**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Przyczyna**: w przypadku usługi Azure SQL, jeśli komunikat o błędzie zawiera ciąg "SqlErrorNumber = 47073", oznacza to, że dostęp do sieci publicznej jest zabroniony w ustawieniu łączności.

- **Zalecenie**: w zaporze Azure SQL ustaw opcję **Odmów dostępu do sieci publicznej** na wartość *nie*. Aby uzyskać więcej informacji, zobacz [Ustawienia łączności usługi Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access).

- **Przyczyna**: w przypadku usługi Azure SQL, jeśli komunikat o błędzie zawiera kod błędu SQL, taki jak "SqlErrorNumber = [ErrorCode]", zobacz Przewodnik rozwiązywania problemów z usługą Azure SQL.

- **Zalecenie**: Aby uzyskać zalecenie, zobacz [Rozwiązywanie problemów z łącznością i inne błędy przy użyciu Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues).

- **Przyczyna**: Sprawdź, czy port 1433 znajduje się na liście dozwolonych zapór.

- **Zalecenie**: Aby uzyskać więcej informacji, zobacz [porty używane przez SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-).

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "SqlException", SQL Database błąd wskazuje, że niektóre konkretne operacje nie powiodły się.

- **Zalecenie**: Aby uzyskać więcej informacji, Wyszukaj według kodu błędu SQL w temacie [Błędy aparatu bazy danych](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors). Aby uzyskać pomoc, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna**: Jeśli jest to problem przejściowy (na przykład niestabilne połączenie sieciowe), należy dodać polecenie ponów próbę w zasadach działania, aby ograniczyć.

- **Zalecenie**: Aby uzyskać więcej informacji, zobacz [potoki i działania w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy).

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "klient z adresem IP"... " nie można uzyskać dostępu do serwera "i próbujesz nawiązać połączenie z Azure SQL Database, ten błąd jest zwykle spowodowany problemem Azure SQL Database zapory.

- **Zalecenie**: w konfiguracji zapory SQL Server platformy Azure Włącz opcję **Zezwalaj usługom i zasobom platformy Azure na dostęp do tego serwera** . Aby uzyskać więcej informacji, zobacz [Azure SQL Database i reguły zapory adresów IP w usłudze Azure Synapse](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).


### <a name="error-code--sqloperationfailed"></a>Kod błędu: SqlOperationFailed

- **Komunikat**: `A database operation failed. Please search error to get more details.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "SqlException", SQL Database zgłasza błąd wskazujący, że niektóre konkretne operacje nie powiodły się.

- **Zalecenie**: Jeśli błąd SQL nie jest wyczyszczony, spróbuj zmienić bazę danych na najnowszy poziom zgodności "150". Może zgłosić najnowsze wersje programu SQL Server. Więcej informacji zawiera strona [dokumentacji](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Aby uzyskać więcej informacji na temat rozwiązywania problemów z programem SQL, wyszukaj kod błędu SQL w obszarze [Błędy aparatu bazy danych](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors). Aby uzyskać pomoc, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "PdwManagedToNativeInteropException", zazwyczaj jest to spowodowane niezgodnością między rozmiarem kolumny źródłowej i ujścia.

- **Zalecenie**: Sprawdź rozmiar kolumn źródłowych i ujścia. Aby uzyskać pomoc, skontaktuj się z pomocą techniczną Azure SQL.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "InvalidOperationException", zazwyczaj jest on spowodowany przez nieprawidłowe dane wejściowe.

- **Zalecenie**: aby ustalić, który wiersz napotkał problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierować problematyczne wiersze do magazynu w celu dalszej analizy. Aby uzyskać więcej informacji, zobacz [odporność na uszkodzenia działania kopiowania w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance).


### <a name="error-code--sqlunauthorizedaccess"></a>Kod błędu: SqlUnauthorizedAccess

- **Komunikat**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Przyczyna**: poświadczenia są niepoprawne lub konto logowania nie może uzyskać dostępu do bazy danych SQL.

- **Zalecenie**: Upewnij się, że konto logowania ma wystarczające uprawnienia dostępu do bazy danych SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Kod błędu: SqlOpenConnectionTimeout

- **Komunikat**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Przyczyna**: problem może być błędem przejściowym bazy danych SQL.

- **Zalecenie**: spróbuj ponownie wykonać operację, aby zaktualizować parametry połączenia połączonej usługi z większą wartością limitu czasu połączenia.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Kod błędu: SqlAutoCreateTableTypeMapFailed

- **Komunikat**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Przyczyna**: tabela autotworzenia nie może spełnić wymagań źródłowych.

- **Zalecenie**: zaktualizuj typ kolumny w *mapowaniu* lub ręcznie Utwórz tabelę ujścia na serwerze docelowym.


### <a name="error-code--sqldatatypenotsupported"></a>Kod błędu: SqlDataTypeNotSupported

- **Komunikat**: `A database operation failed. Check the SQL errors.`

- **Przyczyna**: Jeśli problem występuje w źródle SQL i błąd jest związany z przepełnieniem SqlDateTime, wartość danych przekracza zakres typów logiki (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM).

- **Zalecenie**: rzutowanie typu na ciąg w źródłowym zapytaniu SQL lub w mapowaniu kolumny działania kopiowania Zmień typ kolumny na *ciąg*.

- **Przyczyna**: Jeśli problem występuje w ujścia SQL, a błąd jest związany z przepełnieniem elementu SqlDateTime, wartość danych przekracza dozwolony zakres w tabeli sink.

- **Zalecenie**: zaktualizuj odpowiedni typ kolumny do typu *datetime2* w tabeli sink.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Kod błędu: SqlInvalidDbStoredProcedure

- **Komunikat**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Przyczyna**: określona procedura składowana jest nieprawidłowa. Przyczyną może być to, że procedura składowana nie zwraca żadnych danych.

- **Zalecenie**: Sprawdź poprawność procedury składowanej przy użyciu narzędzi SQL. Upewnij się, że procedura składowana może zwracać dane.


### <a name="error-code--sqlinvaliddbquerystring"></a>Kod błędu: SqlInvalidDbQueryString

- **Komunikat**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Przyczyna**: określone zapytanie SQL jest nieprawidłowe. Przyczyną może być to, że zapytanie nie zwraca żadnych danych.

- **Zalecenie**: Sprawdź poprawność zapytania SQL przy użyciu narzędzi SQL. Upewnij się, że zapytanie może zwracać dane.


### <a name="error-code--sqlinvalidcolumnname"></a>Kod błędu: SqlInvalidColumnName

- **Komunikat**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Przyczyna**: nie można odnaleźć kolumny, ponieważ konfiguracja może być niepoprawna.

- **Zalecenie**: Sprawdź kolumnę w kwerendzie, *strukturę* w zestawie danych i *mapowania* w działaniu.


### <a name="error-code--sqlbatchwritetimeout"></a>Kod błędu: SqlBatchWriteTimeout

- **Komunikat**: `Timeouts in SQL write operation.`

- **Przyczyna**: przyczyną problemu może być błąd przejściowy bazy danych SQL.

- **Zalecenie**: spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Kod błędu: SqlBatchWriteTransactionFailed

- **Komunikat**: `SQL transaction commits failed.`

- **Przyczyna**: Jeśli szczegóły wyjątku stale wskazują limit czasu transakcji, opóźnienie sieci między środowiskiem Integration Runtime i bazą danych jest większe niż domyślny próg wynoszący 30 sekund.

- **Zalecenie**: zaktualizuj parametry połączenia usługi połączonej z SQL Server za pomocą wartości *limitu czasu połączenia* równej lub większej niż 120 i ponownie uruchom działanie.

- **Przyczyna**: Jeśli szczegóły wyjątku sporadycznie wskazują, że połączenie SQL jest przerwane, może to być przejściowy błąd sieci lub problem ze stroną usługi SQL Database.

- **Zalecenie**: spróbuj ponownie wykonać działanie i przejrzyj metryki po stronie bazy danych SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Kod błędu: SqlBulkCopyInvalidColumnLength

- **Komunikat**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Przyczyna**: Kopiowanie zbiorcze SQL nie powiodło się, ponieważ odebrano nieprawidłową długość kolumny od klienta narzędzia kopiowania zbiorczego (bcp).

- **Zalecenie**: aby ustalić, który wiersz napotkał problem, Włącz funkcję odporności na uszkodzenia w działaniu kopiowania. Może to przekierować problematyczne wiersze do magazynu w celu dalszej analizy. Aby uzyskać więcej informacji, zobacz [odporność na uszkodzenia działania kopiowania w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance).


### <a name="error-code--sqlconnectionisclosed"></a>Kod błędu: SqlConnectionIsClosed

- **Komunikat**: `The connection is closed by SQL Database.`

- **Przyczyna**: połączenie SQL jest zamykane przez bazę danych SQL, gdy duże współbieżne uruchomienie i serwer zakończą połączenie.

- **Zalecenie**: spróbuj ponownie nawiązać połączenie. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną Azure SQL.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Komunikat o błędzie: konwersja nie powiodła się podczas konwertowania ciągu znaków na unikatowy identyfikator

- **Objawy**: w przypadku kopiowania danych z tabelarycznego źródła danych (takiego jak SQL Server) do usługi Azure Synapse Analytics przy użyciu kopii etapowej i wielopodstawa zostanie wyświetlony następujący błąd:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Przyczyna**: baza Azure Synapse Analytics Base nie może przekonwertować pustego ciągu na identyfikator GUID.

- **Rozwiązanie**: w ujścia działania kopiowania w obszarze Ustawienia wielobazowe ustaw opcję Użyj opcji **domyślnej** na *Fałsz*.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Komunikat o błędzie: Oczekiwano typu danych: DECIMAL (x, x), wartość powodująca problemy

- **Objawy**: w przypadku kopiowania danych z tabelarycznego źródła danych (takiego jak SQL Server) do usługi Azure Synapse Analytics przy użyciu kopii etapowej i bazy, pojawia się następujący błąd:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Przyczyna**: usługa Azure Synapse Analytics Base nie może wstawić pustego ciągu (wartość null) do kolumny dziesiętnej.

- **Rozwiązanie**: w ujścia działania kopiowania w obszarze Ustawienia wielobazowe ustaw opcję Użyj opcji **domyślnej** na FAŁSZ.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Komunikat o błędzie: komunikat o wyjątku Java: HdfsBridge:: CreateRecordReader

- **Objawy**: skopiujesz dane do usługi Azure Synapse Analytics przy użyciu bazy danych Base i otrzymujesz następujący błąd:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Przyczyna**: przyczyną może być to, że schemat (całkowita szerokość kolumny) jest zbyt duży (większy niż 1 MB). Sprawdź schemat docelowej tabeli usługi Azure Synapse Analytics przez dodanie rozmiaru wszystkich kolumn:

    - Int = 4 bajty
    - Bigint = 8 bajtów
    - Varchar (n), char (n), Binary (n), varbinary (n) = n bajtów
    - Nvarchar (n), nchar (n) = n * 2 bajty
    - Data = 6 bajtów
    - DateTime/(2), smalldatetime = 16 bajtów
    - DateTimeOffset = 20 bajtów
    - Liczba dziesiętna = 19 bajtów
    - Float = 8 bajtów
    - Pieniądze = 8 bajtów
    - Smallmoney = 4 bajty
    - Rzeczywiste = 4 bajty
    - Smallint = 2 bajty
    - Czas = 12 bajtów
    - Tinyint = 1 bajt

- **Rozwiązanie**: 
    - Zmniejsz szerokość kolumny do mniej niż 1 MB.
    - Lub użyj podejścia BULK INSERT poprzez wyłączenie bazy.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Komunikat o błędzie: warunek określony przy użyciu nagłówków warunkowych HTTP nie jest spełniony

- **Objawy**: używasz zapytania SQL do ściągania danych z usługi Azure Synapse Analytics i otrzymujesz następujący błąd:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Przyczyna**: usługa Azure Synapse Analytics napotkała problem podczas badania tabeli zewnętrznej w usłudze Azure Storage.

- **Rozwiązanie**: Uruchom to samo zapytanie w SQL Server Management Studio (SSMS) i sprawdź, czy otrzymujesz ten sam wynik. W takim przypadku należy otworzyć bilet pomocy technicznej w usłudze Azure Synapse Analytics i podać nazwę serwera i bazę danych usługi Azure Synapse Analytics.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Warstwa wydajności jest niska i prowadzi do niepowodzenia kopiowania

- **Objawy**: skopiujesz dane do Azure SQL Database i wystąpi następujący błąd: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Przyczyna**: Azure SQL Database S1 ma limity wejścia/wyjścia (we/wy).

- **Rozwiązanie**: uaktualnij Azure SQL Database warstwę wydajności, aby rozwiązać ten problem. 


### <a name="sql-table-cant-be-found"></a>Nie można znaleźć tabeli SQL 

- **Objawy**: skopiujesz dane ze hybrydowej do lokalnej tabeli SQL Server i otrzymasz następujący błąd:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Przyczyna**: bieżące konto SQL nie ma wystarczających uprawnień do wykonywania żądań wystawionych przez platformę .NET SqlBulkCopy. WriteToServer.

- **Rozwiązanie**: Przełącz na bardziej uprzywilejowane konto SQL.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Komunikat o błędzie: dane typu String lub Binary zostały obcięte

- **Objawy**: Wystąpił błąd podczas kopiowania danych do lokalnej tabeli SQL Server platformy Azure. 

- **Przyczyna**: definicja schematu tabeli SQL CX zawiera co najmniej jedną kolumnę o długości mniejszej niż oczekiwano.

- **Rozwiązanie**: Aby rozwiązać ten problem, spróbuj wykonać następujące czynności:

    1. Aby rozwiązać problem z tymi wierszami, Zastosuj [tolerancję błędów](./copy-activity-fault-tolerance.md)ujścia SQL, szczególnie "redirectIncompatibleRowSettings".

        > [!NOTE]
        > Odporność na uszkodzenia może wymagać dodatkowego czasu wykonania, co może prowadzić do wyższych kosztów.

    2. Podwójne sprawdzenie przekierowanych danych względem długości kolumny schematu tabeli SQL, aby zobaczyć, które kolumny muszą zostać zaktualizowane.

    3. Odpowiednio zaktualizuj schemat tabeli.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>Kod błędu: AzureTableDuplicateColumnsFromSource

- **Komunikat**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Przyczyna**: zduplikowane kolumny źródłowe mogą wystąpić z jednego z następujących powodów:
   * Używasz bazy danych jako źródła i zastosowano sprzężenia tabeli.
   * W wierszu nagłówka znajdują się pliki CSV bez struktury ze zduplikowanymi nazwami kolumn.

- **Zalecenie**: w razie potrzeby Sprawdź podwójne i popraw kolumny źródłowe.


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>Kod błędu: DB2DriverRunFailed

- **Komunikat**: `Error thrown from driver. Sql code: '%code;'`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "SQLSTATE = 51002 SQLCODE =-805", postępuj zgodnie z "poradą" w temacie [Kopiowanie danych z programu DB2 przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties).

- **Zalecenie**: spróbuj ustawić wartość "NULLID" we `packageCollection`  właściwości.


## <a name="delimited-text-format"></a>Format tekstu rozdzielanego

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Kod błędu: DelimitedTextColumnNameNotAllowNull

- **Komunikat**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Przyczyna**: po ustawieniu opcji "użycia" w działaniu pierwszy wiersz jest używany jako nazwa kolumny. Ten błąd oznacza, że pierwszy wiersz zawiera pustą wartość (na przykład "Column, ColumnB").

- **Zalecenie**: Sprawdź pierwszy wiersz i popraw wartość, jeśli jest pusta.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Kod błędu: DelimitedTextMoreColumnsThanDefined

- **Komunikat**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Przyczyna**: liczba kolumn problematycznych wierszy jest większa niż liczba kolumn w pierwszym wierszu. Przyczyną może być problem z danymi lub nieprawidłowy ogranicznik kolumny lub ustawienia znaku cudzysłowu.

- **Zalecenie**: Pobierz liczbę wierszy z komunikatu o błędzie, Sprawdź kolumnę wiersza i napraw dane.

- **Przyczyna**: jeśli oczekiwana liczba kolumn jest równa "1" w komunikacie o błędzie, być może określono nieprawidłowe ustawienia kompresji lub formatu, co spowodowało nieprawidłowe przeanalizowanie plików przez Data Factory.

- **Zalecenie**: Sprawdź ustawienia formatu, aby upewnić się, że są one zgodne z plikami źródłowymi.

- **Przyczyna**: Jeśli źródło jest folderem, pliki w określonym folderze mogą mieć inny schemat.

- **Zalecenie**: Upewnij się, że pliki w określonym folderze mają identyczny schemat.


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service i Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Kod błędu: DynamicsCreateServiceClientError

- **Komunikat**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Przyczyna**: problem jest przejściowym problemem po stronie serwera Dynamics.

- **Zalecenie**: Uruchom ponownie potok. Jeśli operacja nie powiedzie się, spróbuj zmniejszyć liczbę równoległości. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną systemu Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Brak kolumn podczas importowania schematu lub podglądu danych

- **Objawy**: Brak niektórych kolumn podczas importowania schematu lub podglądu danych. Komunikat o błędzie: `The valid structure information (column name and type) are required for Dynamics source.`

- **Przyczyna**: ten problem jest zaprojektowany, ponieważ Data Factory nie może wyświetlić kolumn, które nie zawierają żadnych wartości w pierwszych 10 rekordach. Upewnij się, że dodawane kolumny mają prawidłowy format. 

- **Zalecenie**: ręcznie Dodaj kolumny na karcie mapowanie.


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>Kod błędu: DynamicsMissingTargetForMultiTargetLookupField

- **Komunikat**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Przyczyna**: kolumna docelowa nie istnieje w źródle lub w mapowaniu kolumny.

- **Zalecenie**:  
  1. Upewnij się, że źródło zawiera kolumnę docelową. 
  2. Dodaj kolumnę Target w mapowaniu kolumn. Upewnij się, że kolumna ujścia ma format *{FieldName} @EntityReference*.


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>Kod błędu: DynamicsInvalidTargetForMultiTargetLookupField

- **Komunikat**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Przyczyna**: podano nieprawidłową nazwę jednostki jako jednostkę docelową dla wielowymiarowego pola wyszukiwania.

- **Zalecenie**: Podaj prawidłową nazwę jednostki dla wielowymiarowego pola wyszukiwania.


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>Kod błędu: DynamicsInvalidTypeForMultiTargetLookupField

- **Komunikat**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Przyczyna**: wartość w kolumnie Target nie jest ciągiem.

- **Zalecenie**: Podaj prawidłowy ciąg w kolumnie cel wyszukiwania wielowymiarowego.


### <a name="error-code--dynamicsfailedtorequetserver"></a>Kod błędu: DynamicsFailedToRequetServer

- **Komunikat**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Przyczyna**: serwer Dynamics jest niestabilny lub niedostępny lub wystąpił problem z siecią.

- **Zalecenie**: Aby uzyskać więcej szczegółów, sprawdź łączność z siecią lub Sprawdź dziennik serwera Dynamics. Aby uzyskać pomoc, skontaktuj się z pomocą techniczną systemu Dynamics.
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>Kod błędu: FtpFailedToConnectToFtpServer

- **Komunikat**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Przyczyna**: w celu nawiązania połączenia z serwerem FTP można użyć nieprawidłowego typu połączonej usługi dla serwera FTP, na przykład przy użyciu usługi połączonej Secure FTP (SFTP).

- **Zalecenie**: Sprawdź port serwera docelowego. FTP używa portu 21.


## <a name="http"></a>HTTP

### <a name="error-code--httpfilefailedtoread"></a>Kod błędu: HttpFileFailedToRead

- **Komunikat**: `Failed to read data from http server. Check the error from http server：%message;`

- **Przyczyna**: ten błąd występuje, gdy Azure Data Factory rozmowy z serwerem HTTP, ale operacja żądania HTTP kończy się niepowodzeniem.

- **Zalecenie**: Sprawdź kod stanu HTTP w komunikacie o błędzie i rozwiąż problem z serwerem zdalnym.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Kod błędu: wyjątku ArgumentOutOfRangeException

- **Komunikat**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Przyczyna**: w Data Factory wartości datetime są obsługiwane w zakresie od 0001-01-01 00:00:00 do 9999-12-31 23:59:59. Jednak program Oracle obsługuje szersze wartości daty i godziny, takie jak Century BC lub min/s>59, co prowadzi do awarii w Data Factory.

- **Zalecenie**: 

    Aby sprawdzić, czy wartość w programie Oracle znajduje się w zakresie Data Factory, uruchom polecenie `select dump(<column name>)` . 

    Aby poznać sekwencję bajtów w wyniku, zobacz [jak są daty przechowywane w bazie danych Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>Format ORC

### <a name="error-code--orcjavainvocationexception"></a>Kod błędu: OrcJavaInvocationException

- **Komunikat**: `An error occurred when invoking Java, message: %javaException;.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciągi "Java. lang. OutOfMemory," "Java sterty Space" i "doubleCapacity", zazwyczaj jest to problem z zarządzaniem pamięcią w starej wersji środowiska Integration Runtime.

- **Zalecenie**: Jeśli korzystasz z samoobsługowego Integration Runtime, zalecamy przeprowadzenie uaktualnienia do najnowszej wersji.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "Java. lang. OutOfMemory", środowisko Integration Runtime nie ma wystarczającej ilości zasobów do przetworzenia plików.

- **Zalecenie**: Ogranicz współbieżne uruchomienia w środowisku Integration Runtime. W przypadku samodzielnego środowiska IR można skalować w górę do zaawansowanej maszyny z pamięcią równą lub większą niż 8 GB.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "NullPointerReference", przyczyną może być błąd przejściowy.

- **Zalecenie**: spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "BufferOverflowException", przyczyną może być błąd przejściowy.

- **Zalecenie**: spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "Java. lang. ClassCastException:org. Apache. Hadoop. Hive. serde2. IO. HiveCharWritable nie można rzutować na typ org. Apache. Hadoop. w ramach operacji we/wy. text" przyczyną może być błąd konwersji typu w środowisku uruchomieniowym języka Java. Zazwyczaj oznacza to, że dane źródłowe nie mogą być obsługiwane prawidłowo w środowisku uruchomieniowym Java.

- **Zalecenie**: to jest problem z danymi. Spróbuj użyć ciągu zamiast char lub varchar w danych formatu ORC.

### <a name="error-code--orcdatetimeexceedlimit"></a>Kod błędu: OrcDateTimeExceedLimit

- **Komunikat**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Przyczyna**: Jeśli wartość daty i godziny to "0001-01-01 00:00:00", może to być spowodowane różnicami między [kalendarzem juliańskim a kalendarzem gregoriańskim](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Zalecenie**: Sprawdź wartość Takty i Unikaj używania wartości datetime "0001-01-01 00:00:00".


## <a name="parquet-format"></a>Format Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Kod błędu: ParquetJavaInvocationException

- **Komunikat**: `An error occurred when invoking java, message: %javaException;.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciągi "Java. lang. OutOfMemory," "Java sterty Space" i "doubleCapacity", zazwyczaj jest to problem z zarządzaniem pamięcią w starej wersji Integration Runtime.

- **Zalecenie**: Jeśli używasz własnego środowiska IR, a wersja jest wcześniejsza niż 3.20.7159.1, zalecamy przeprowadzenie uaktualnienia do najnowszej wersji.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "Java. lang. OutOfMemory", środowisko Integration Runtime nie ma wystarczającej ilości zasobów do przetworzenia plików.

- **Zalecenie**: Ogranicz współbieżne uruchomienia w środowisku Integration Runtime. W przypadku samodzielnego środowiska IR można skalować w górę do zaawansowanej maszyny z pamięcią, która jest równa lub większa niż 8 GB.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "NullPointerReference", może to być błąd przejściowy.

- **Zalecenie**: spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną.


### <a name="error-code--parquetinvalidfile"></a>Kod błędu: ParquetInvalidFile

- **Komunikat**: `File is not a valid Parquet file.`

- **Przyczyna**: jest to problem z plikiem Parquet.

- **Zalecenie**: Sprawdź, czy dane wejściowe są prawidłowym plikiem Parquet.


### <a name="error-code--parquetnotsupportedtype"></a>Kod błędu: ParquetNotSupportedType

- **Komunikat**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Przyczyna**: format Parquet nie jest obsługiwany w Azure Data Factory.

- **Zalecenie**: dokładnie sprawdź dane źródłowe, przechodząc do opcji [obsługiwane formaty plików i kodery-dekoder kompresji przez działanie kopiowania w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Kod błędu: ParquetMissedDecimalPrecisionScale

- **Komunikat**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Przyczyna**: przeanalizowano precyzję i skalę liczby, ale nie podano takich informacji.

- **Zalecenie**: Źródło nie zwraca prawidłowych informacji o dokładności i skali. Aby uzyskać informacje, zapoznaj się z kolumną problemu.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Kod błędu: ParquetInvalidDecimalPrecisionScale

- **Komunikat**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Przyczyna**: schemat jest nieprawidłowy.

- **Zalecenie**: Sprawdź w kolumnie problem precyzja i skala.


### <a name="error-code--parquetcolumnnotfound"></a>Kod błędu: ParquetColumnNotFound

- **Komunikat**: `Column %column; does not exist in Parquet file.`

- **Przyczyna**: schemat źródłowy jest niezgodny ze schematem ujścia.

- **Zalecenie**: Sprawdź mapowania w działaniu. Upewnij się, że kolumna źródłowa może być zmapowana do odpowiedniej kolumny ujścia.


### <a name="error-code--parquetinvaliddataformat"></a>Kod błędu: ParquetInvalidDataFormat

- **Komunikat**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Przyczyna**: nie można przekonwertować danych na typ określony w mapowaniu. Source.

- **Zalecenie**: należy dokładnie sprawdzić dane źródłowe lub określić poprawny typ danych dla tej kolumny w mapowaniu kolumny działania kopiowania. Aby uzyskać więcej informacji, zobacz [obsługiwane formaty plików i kodeki kompresji przez działanie kopiowania w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs).


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Kod błędu: ParquetDataCountNotMatchColumnCount

- **Komunikat**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Przyczyna**: niezgodność między liczbą kolumn źródłowych a liczbą kolumn ujścia.

- **Zalecenie**: Sprawdź podwójne, aby upewnić się, że liczba kolumn źródłowych jest taka sama jak liczba kolumn ujścia w obszarze "mapowanie".


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Kod błędu: ParquetDataTypeNotMatchColumnType

- **Komunikat**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Przyczyna**: dane ze źródła nie mogą zostać przekonwertowane na typ, który jest zdefiniowany w zlewie.

- **Zalecenie**: Określ poprawny typ w mapowaniu. sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Kod błędu: ParquetBridgeInvalidData

- **Komunikat**: `%message;`

- **Przyczyna**: przekroczono limit wartości danych.

- **Zalecenie**: spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z nami.


### <a name="error-code--parquetunsupportedinterpretation"></a>Kod błędu: ParquetUnsupportedInterpretation

- **Komunikat**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Przyczyna**: ten scenariusz nie jest obsługiwany.

- **Zalecenie**: element "ParquetInterpretFor" nie może być "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Kod błędu: ParquetUnsupportFileLevelCompressionOption

- **Komunikat**: `File level compression is not supported for Parquet.`

- **Przyczyna**: ten scenariusz nie jest obsługiwany.

- **Zalecenie**: Usuń element "CompressionType" w ładunku.


### <a name="error-code--usererrorjniexception"></a>Kod błędu: UserErrorJniException

- **Komunikat**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Przyczyna**: nie można utworzyć wirtualna maszyna Java (JVM), ponieważ ustawiono pewne niedozwolone (globalne) argumenty.

- **Zalecenie**: Zaloguj się do komputera, który hostuje *każdy węzeł* własnego środowiska IR. Sprawdź, czy zmienna systemowa jest ustawiona poprawnie, w następujący sposób: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Uruchom ponownie wszystkie węzły IR, a następnie uruchom ponownie potok.


### <a name="arithmetic-overflow"></a>Przepełnienie arytmetyczne

- **Objawy**: podczas kopiowania plików Parquet wystąpił komunikat o błędzie: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Przyczyna**: obecnie tylko dziesiętna precyzja <= 38 i długość części całkowitej <= 20 są obsługiwane w przypadku kopiowania plików z programu Oracle do Parquet. 

- **Rozwiązanie**: Aby obejść ten problem, można przekonwertować wszystkie kolumny z tym problemem na varchar2.


### <a name="no-enum-constant"></a>Brak stałej wyliczenia

- **Objawy**: komunikat o błędzie wystąpił podczas kopiowania danych do formatu Parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , lub: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Przyczyna**: 

    Przyczyną problemu może być spacje lub nieobsługiwane znaki specjalne (takie jak,; {} () \n\t =) w nazwie kolumny, ponieważ Parquet nie obsługuje takiego formatu. 

    Na przykład nazwa kolumny, taka jak *contoso (test)* , będzie analizować typ w nawiasach klamrowych z [kodu](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Błąd jest zgłaszany, ponieważ nie ma takiego typu "test".

    Aby sprawdzić obsługiwane typy, przejdź do witryny GitHub [Apache/Parquet-Mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java).

- **Rozwiązanie**: 

    Sprawdź dwukrotnie, aby sprawdzić, czy:
    - Nazwa kolumny ujścia zawiera białe znaki.
    - Pierwszy wiersz z białymi znakami jest używany jako nazwa kolumny.
    - Typ Oryginalnatype jest obsługiwany. Spróbuj uniknąć używania tych znaków specjalnych: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>Kod błędu: RestSinkCallFailed

- **Komunikat**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Przyczyna**: ten błąd występuje, gdy Azure Data Factory rozmowy z punktem końcowym REST za pośrednictwem protokołu HTTP i operacja żądania nie powiedzie się.

- **Zalecenie**: Sprawdź kod stanu HTTP lub komunikat w komunikacie o błędzie, a następnie Rozwiąż problem z serwerem zdalnym.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Nieoczekiwana odpowiedź sieciowa z łącznika REST

- **Objawy**: punkt końcowy czasami otrzymuje nieoczekiwaną odpowiedź (400, 401, 403, 500) z łącznika Rest.

- **Przyczyna**: Łącznik źródła REST używa adresu URL i metody/nagłówka/treści http ze połączonej usługi/zestawu danych/Kopiuj źródło jako parametry podczas konstrukcji żądania HTTP. Problem jest najprawdopodobniej spowodowany przez pewne błędy w co najmniej jednym z określonych parametrów.

- **Rozwiązanie**: 
    - Użyj "zwinięcie" w oknie wiersza polecenia, aby sprawdzić, czy parametr jest przyczyną problemu (należy zawsze uwzględniać nagłówki **Zaakceptuj** i **User-Agent** ):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Jeśli polecenie zwróci tę samą nieoczekiwaną odpowiedź, napraw poprzednie parametry z "zwinięciem", dopóki nie zwróci oczekiwanej odpowiedzi. 

      Możesz również użyć "zwinięcie--Help", aby bardziej zaawansowane użycie polecenia.

    - Jeśli tylko Data Factory łącznika REST zwróci nieoczekiwaną odpowiedź, skontaktuj się z pomocą techniczną firmy Microsoft w celu dalszej rozwiązywania problemów.
    
    - Zauważ, że "zwinięcie" może nie być odpowiednie do odtworzenia problemu weryfikacji certyfikatu SSL. W niektórych scenariuszach polecenie "zwinięcie" zostało wykonane pomyślnie bez napotkania jakichkolwiek problemów z walidacją certyfikatu SSL. Ale jeśli ten sam adres URL jest wykonywany w przeglądarce, certyfikat SSL nie jest faktycznie zwracany dla klienta, aby ustanowić relację zaufania z serwerem.

      W poprzednim przypadku zalecane są narzędzia takie jak **Poster** i **programu Fiddler** .


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>Kod błędu: SftpOperationFail

- **Komunikat**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Przyczyna**: problem z operacją SFTP.

- **Zalecenie**: Sprawdź szczegóły błędu z protokołu SFTP.


### <a name="error-code--sftprenameoperationfail"></a>Kod błędu: SftpRenameOperationFail

- **Komunikat**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Przyczyna**: serwer SFTP nie obsługuje zmiany nazwy pliku tymczasowego.

- **Zalecenie**: Ustaw "useTempFileRename" jako FAŁSZ w ujścia kopiowania, aby wyłączyć przekazywanie do pliku tymczasowego.


### <a name="error-code--sftpinvalidsftpcredential"></a>Kod błędu: SftpInvalidSftpCredential

- **Komunikat**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Przyczyna**: zawartość klucza prywatnego jest pobierana z magazynu kluczy Azure lub zestawu SDK, ale nie została poprawnie zaszyfrowana.

- **Zalecenie**:  

    Jeśli zawartość klucza prywatnego pochodzi z magazynu kluczy, oryginalny plik klucza może zadziałać, Jeśli przekażesz go bezpośrednio do połączonej usługi SFTP.

    Aby uzyskać więcej informacji, zobacz [Kopiowanie danych z i do serwera SFTP przy użyciu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication). Zawartość klucza prywatnego jest zakodowana w formacie base64 zawartość klucza prywatnego SSH.

    Koduj *cały* oryginalny plik klucza prywatnego przy użyciu kodowania base64 i Zapisz zakodowany ciąg w magazynie kluczy. Oryginalny plik klucza prywatnego to ten, który może współdziałać z połączoną usługą SFTP w przypadku wybrania z pliku opcji **Przekaż** .

    Oto kilka przykładów, których można użyć do wygenerowania ciągu:

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

    - Użyj narzędzia konwersji Base64 innej firmy. Zalecamy użycie narzędzia [do formatowania kodowania w formacie base64](https://www.base64encode.org/) .

- **Przyczyna**: wybrano nieprawidłowy format zawartości klucza.

- **Zalecenie**:  

    Format PKCS # 8 SSH klucz prywatny (Rozpocznij od "-----rozpoczęcia szyfrowanego klucza prywatnego-----") nie jest obecnie obsługiwany w celu uzyskania dostępu do serwera SFTP w Data Factory. 

    Aby przekonwertować klucz na tradycyjny format klucza SSH, rozpoczynając od "-----Rozpocznij klucz prywatny RSA-----", uruchom następujące polecenia:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Przyczyna**: Nieprawidłowe poświadczenia lub zawartość klucza prywatnego.

- **Zalecenie**: Aby sprawdzić, czy plik klucza lub hasło są poprawne, sprawdź podwójne z narzędziami, takimi jak WinSCP.

### <a name="sftp-copy-activity-failed"></a>Działanie kopiowania SFTP nie powiodło się

- **Objawy**: 
  * Kod błędu: UserErrorInvalidColumnMappingColumnNotFound 
  * Komunikat o błędzie: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Przyczyna**: Źródło nie zawiera kolumny o nazwie "AccMngr".

- **Rozwiązanie**: aby określić, czy kolumna "AccMngr" istnieje, należy dokładnie sprawdzić konfigurację zestawu danych, mapując kolumnę docelowego zestawu danych.


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>Kod błędu: SftpFailedToConnectToSftpServer

- **Komunikat**: `Failed to connect to SFTP server '%server;'.`

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "operacja odczytu gniazda przekroczyła limit czasu po 30000 milisekund," jedną z możliwych przyczyn jest to, że dla serwera SFTP jest używany nieprawidłowy typ połączonej usługi. Na przykład możesz użyć połączonej usługi FTP, aby nawiązać połączenie z serwerem SFTP.

- **Zalecenie**: Sprawdź port serwera docelowego. Protokół SFTP domyślnie używa portu 22.

- **Przyczyna**: Jeśli komunikat o błędzie zawiera ciąg "odpowiedź serwera nie zawiera identyfikacji protokołu SSH," jedną z możliwych przyczyn jest to, że serwer SFTP ograniczy połączenie. Data Factory spowoduje utworzenie wielu połączeń do pobrania z serwera SFTP równolegle, a czasami spowoduje wystąpienie ograniczenia serwera SFTP. Zwykle różne serwery zwracają różne błędy w przypadku wystąpienia ograniczenia przepustowości.

- **Zalecenie**:  

    Określ maksymalną liczbę równoczesnych połączeń zestawu danych SFTP jako 1 i ponownie uruchom działanie kopiowania. Jeśli działanie zakończy się pomyślnie, możesz mieć pewność, że ograniczenie jest przyczyną.

    Jeśli chcesz podwyższyć poziom przepływności, skontaktuj się z administratorem protokołu SFTP w celu zwiększenia limitu liczby połączeń współbieżnych lub wykonaj jedną z następujących czynności:

    * Jeśli używasz własnego środowiska IR, Dodaj do listy dozwolonych adres IP samoobsługowego środowiska IR.
    * Jeśli używasz Azure IR, Dodaj [Azure Integration Runtime adresy IP](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses). Jeśli nie chcesz dodawać zakresu adresów IP do listy dozwolonych serwerów SFTP, zamiast tego użyj własnego środowiska IR.

## <a name="sharepoint-online-list"></a>Lista usługi SharePoint Online

### <a name="error-code--sharepointonlineauthfailed"></a>Kod błędu: SharePointOnlineAuthFailed

- **Komunikat**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Przyczyna**: identyfikator i klucz jednostki usługi mogą nie być poprawnie ustawione.

- **Zalecenie**: Sprawdź zarejestrowaną aplikację (identyfikator jednostki usługi) i klucz, aby sprawdzić, czy są one ustawione prawidłowo.


## <a name="xml-format"></a>Format XML

### <a name="error-code--xmlsinknotsupported"></a>Kod błędu: XmlSinkNotSupported

- **Komunikat**: `Write data in XML format is not supported yet, choose a different format!`

- **Przyczyna**: zestaw danych XML został użyty jako zestaw danych ujścia w działaniu kopiowania.

- **Zalecenie**: Użyj zestawu danych w innym formacie niż zestaw danych ujścia.


### <a name="error-code--xmlattributecolumnnameconflict"></a>Kod błędu: XmlAttributeColumnNameConflict

- **Komunikat**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Przyczyna**: użyto prefiksu atrybutu, który spowodował konflikt.

- **Zalecenie**: ustaw inną wartość dla właściwości "attributePrefix".


### <a name="error-code--xmlvaluecolumnnameconflict"></a>Kod błędu: XmlValueColumnNameConflict

- **Komunikat**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Przyczyna**: jedna z nazw elementów podrzędnych została użyta jako nazwa kolumny dla wartości elementu.

- **Zalecenie**: ustaw inną wartość dla właściwości "valueColumn".


### <a name="error-code--xmlinvalid"></a>Kod błędu: xmlnieprawidłowy

- **Komunikat**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Przyczyna**: wejściowy plik XML nie jest poprawnie sformułowany.

- **Zalecenie**: Popraw plik XML, aby był poprawnie sformułowany.


## <a name="general-copy-activity-error"></a>Ogólny błąd działania kopiowania

### <a name="error-code--jrenotfound"></a>Kod błędu: JreNotFound

- **Komunikat**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Przyczyna**: nie można znaleźć środowiska uruchomieniowego języka Java przez własne środowisko IR. Środowisko uruchomieniowe Java jest wymagane do odczytywania określonych źródeł.

- **Zalecenie**: Sprawdź środowisko Integration Runtime, zobacz [Korzystanie z Integration Runtime samoobsługowego](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime).


### <a name="error-code--wildcardpathsinknotsupported"></a>Kod błędu: WildcardPathSinkNotSupported

- **Komunikat**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Przyczyna**: zestaw danych ujścia nie obsługuje wartości symboli wieloznacznych.

- **Zalecenie**: Sprawdź zestaw danych ujścia i ponownie Napisz ścieżkę bez użycia wartości symbolu wieloznacznego.


### <a name="fips-issue"></a>Problem ze standardem FIPS

- **Objawy**: działanie kopiowania kończy się niepowodzeniem na własnym hostowanym komputerze z OBSŁUGą standardu FIPS z następującym komunikatem o błędzie: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Przyczyna**: ten błąd może wystąpić podczas kopiowania danych z łącznikami, takimi jak Azure Blob, SFTP i tak dalej. Standardy FIPS (Federal Information Processing Standards) definiują określony zestaw algorytmów kryptograficznych, które mogą być używane. Gdy tryb FIPS jest włączony na komputerze, niektóre klasy kryptograficzne, od których zależy działanie kopiowania, są blokowane w niektórych scenariuszach.

- **Rozwiązanie**: Dowiedz się, [dlaczego nie zalecamy już korzystania z trybu FIPS](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037), i Oceń, czy można wyłączyć tryb FIPS na własnym hostowanym urządzeniu IR.

    Alternatywnie, jeśli chcesz zezwolić na Azure Data Factory ominięcie trybu FIPS i spowodować pomyślne uruchomienie działania, wykonaj następujące czynności:

    1. Otwórz folder, w którym jest zainstalowany własny środowisko IR. Ścieżka jest zwykle *C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Otwórz plik *diawp.exe.config* a następnie na końcu `<runtime>` sekcji Dodaj `<enforceFIPSPolicy enabled="false"/>` , jak pokazano poniżej:

        ![Zrzut ekranu przedstawiający sekcję diawp.exe.config pliku z wyłączonym FIPS.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Zapisz plik, a następnie uruchom ponownie maszynę podczerwieni z własnym obsługą.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&stronie](/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
