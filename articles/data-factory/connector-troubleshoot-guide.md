---
title: Rozwiązywanie problemów Azure Data Factory łącznikami
description: Dowiedz się, jak rozwiązywać problemy z łącznikiem w Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 21b5522f07519e9a0c3353cb2463e0ec49063f34
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713430"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Rozwiązywanie problemów Azure Data Factory łącznikami

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule o mowa o typowych sposobach rozwiązywania problemów z Azure Data Factory łącznikami.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>Kod błędu: AzureBlobOperationFailed

- **Komunikat:**"Operacja obiektu blob nie powiodła się. ContainerName: %containerName;, path: %path;."

- **Przyczyna:** Problem z Blob Storage operacji.

- **Zalecenie:** Aby sprawdzić szczegóły błędu, zobacz [Blob Storage kodów błędów](/rest/api/storageservices/blob-service-error-codes). Aby uzyskać dalszą pomoc, skontaktuj się z zespołem Blob Storage.


### <a name="invalid-property-during-copy-activity"></a>Nieprawidłowa właściwość podczas działania kopiowania

- **Komunikat**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Przyczyna:** Typ zdefiniowany w zestawie danych jest niespójny z typem źródła lub ujścia zdefiniowanym w działaniu kopiowania.

- **Rozwiązanie:** Edytuj definicję JSON zestawu danych lub potoku, aby zapewnić spójność typów, a następnie ponownie uruchomić wdrożenie.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Komunikat o błędzie: Rozmiar żądania jest zbyt duży

- **Objawy:** Podczas kopiowania danych do Azure Cosmos DB z domyślnym rozmiarem partii zapisu jest wyświetlany następujący błąd: `Request size is too large.`

- **Przyczyna:** Azure Cosmos DB ogranicza rozmiar pojedynczego żądania do 2 MB. Formuła to *rozmiar żądania = rozmiar pojedynczego dokumentu wsadowego \* zapisu.* Jeśli rozmiar dokumentu jest duży, zachowanie domyślne spowoduje zbyt duży rozmiar żądania. Możesz dostosować rozmiar partii zapisu.

- **Rozwiązanie:** W ujściu działania kopiowania zmniejsz wartość *rozmiaru* partii zapisu (wartość domyślna to 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Komunikat o błędzie: Naruszenie ograniczenia indeksu unikatowego

- **Objawy:** Podczas kopiowania danych do Azure Cosmos DB zostanie wyświetlony następujący błąd:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Przyczyna:** Istnieją dwie możliwe przyczyny:

    - Przyczyna 1: Jeśli używasz funkcji **Insert** jako zachowania zapisu, ten błąd oznacza, że dane źródłowe mają wiersze lub obiekty o tym samym identyfikatorze.
    - Przyczyna 2: Jeśli użyjesz operacji **Upsert** jako zachowania zapisu i ustawisz inny unikatowy klucz dla kontenera, ten błąd oznacza, że dane źródłowe mają wiersze lub obiekty z różnymi identyfikatorami, ale taką samą wartość dla zdefiniowanego unikatowego klucza.

- **Rozwiązanie**: 

    - Dla przyczyny 1 ustaw **upsert** jako zachowanie zapisu.
    - W przypadku przyczyny 2 upewnij się, że każdy dokument ma inną wartość dla zdefiniowanego unikatowego klucza.

### <a name="error-message-request-rate-is-large"></a>Komunikat o błędzie: Liczba żądań jest duża

- **Objawy:** Podczas kopiowania danych do Azure Cosmos DB zostanie wyświetlony następujący błąd:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Przyczyna:** Liczba użytych jednostek żądań jest większa niż liczba dostępnych jednostek ŻĄDAŃ skonfigurowanych w Azure Cosmos DB. Aby dowiedzieć się, Azure Cosmos DB oblicza liczbę jednostek ŻĄDAŃ, zobacz [Jednostki żądań w Azure Cosmos DB](../cosmos-db/request-units.md#request-unit-considerations).

- **Rozwiązanie:** Wypróbuj następujące dwa rozwiązania:

    - Zwiększ liczbę *procesorów RU kontenera* do większej wartości w Azure Cosmos DB. To rozwiązanie poprawi wydajność działania kopiowania, ale będzie wiązać się z dodatkowymi kosztami Azure Cosmos DB. 
    - Zmniejsz *wartość writeBatchSize* do mniejszej wartości, na przykład 1000, i zmniejsz *wartość parallelCopies* do mniejszej wartości, takiej jak 1. To rozwiązanie zmniejszy wydajność uruchamiania kopiowania, ale nie będzie wiązać się z dodatkowymi kosztami Azure Cosmos DB.

### <a name="columns-missing-in-column-mapping"></a>Brak kolumn w mapowaniu kolumn

- **Objawy:** Podczas importowania schematu dla Azure Cosmos DB do mapowania kolumn brakuje niektórych kolumn. 

- **Przyczyna:** Data Factory schemat z pierwszych 10 Azure Cosmos DB dokumentów. Jeśli niektóre kolumny lub właściwości dokumentu nie zawierają wartości, schemat nie jest wykrywany przez Data Factory i w związku z tym nie jest wyświetlany.

- **Rozwiązanie:** Możesz dostosować zapytanie, jak pokazano w poniższym kodzie, aby wymusić, aby wartości kolumn były wyświetlane w zestawie wyników z pustymi wartościami. Załóżmy, *że w* pierwszych 10 dokumentach brakuje kolumny niemożliwej. Alternatywnie możesz ręcznie dodać kolumnę do mapowania.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Komunikat o błędzie: Identyfikator GuidRepresentation dla czytnika to CSharpLegacy

- **Objawy:** Podczas kopiowania danych z bazy Azure Cosmos DB MongoAPI lub MongoDB z polem UUID (Universally Unique Identifier) zostanie wyświetlony następujący błąd:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Przyczyna:** Istnieją dwa sposoby reprezentowania wartości UUID w formacie Binary JSON (BSON): UuidStardard i UuidLegacy. Domyślnie do odczytywania danych jest używana wartość UuidLegacy. Jeśli dane UUID w bazie MongoDB mają wartość UuidStandard, zostanie wyświetlony błąd.

- **Rozwiązanie:** W ciągu połączenia bazy danych MongoDB dodaj *opcję uuidRepresentation=standard.* Aby uzyskać więcej informacji, zobacz [MongoDB connection string (Ciąg połączenia bazy danych MongoDB).](connector-mongodb.md#linked-service-properties)
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (interfejs API SQL)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Kod błędu: CosmosDbSqlApiOperationFailed

- **Komunikat**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Przyczyna:** Problem z operacją CosmosDbSqlApi.

- **Zalecenie:** Aby sprawdzić szczegóły błędu, zobacz [dokument Azure Cosmos DB pomocy.](../cosmos-db/troubleshoot-dot-net-sdk.md) Aby uzyskać dalszą pomoc, skontaktuj się z zespołem Azure Cosmos DB.

## <a name="azure-data-lake-storage-gen1"></a>Usługa Azure Data Lake Storage 1. generacji

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Komunikat o błędzie: Połączenie bazowe zostało zamknięte: Nie można ustanowić relacji zaufania dla bezpiecznego kanału SSL/TLS.

- **Objawy:** działanie Kopiuj kończy się niepowodzeniem z następującym błędem: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Przyczyna:** Weryfikacja certyfikatu nie powiodła się podczas uściśniania TLS.

- **Rozwiązanie:** Aby obejść ten problem, użyj kopii etapowej, aby pominąć weryfikację Transport Layer Security (TLS) dla Azure Data Lake Storage Gen1. Należy odtworzyć ten problem i zebrać ślad monitora sieci (netmon), a następnie skontaktuj się z zespołem sieci w celu sprawdzenia konfiguracji sieci lokalnej.

    ![Diagram przedstawiający Azure Data Lake Storage Gen1 na potrzeby rozwiązywania problemów.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Komunikat o błędzie: Serwer zdalny zwrócił błąd: (403) Zabronione

- **Objawy:** działanie Kopiuj się z następującym błędem: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Przyczyna:** Jedną z możliwych przyczyn jest to, że użytą jednostkę usługi lub tożsamość zarządzaną nie ma uprawnień dostępu do niektórych folderów lub plików.

- **Rozwiązanie:** przyznaj odpowiednie uprawnienia do wszystkich folderów i podfolderów, które chcesz skopiować. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych do lub z Azure Data Lake Storage Gen1 przy użyciu Azure Data Factory](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Komunikat o błędzie: Nie można uzyskać tokenu dostępu przy użyciu jednostki usługi. Błąd ADAL: service_unavailable

- **Objawy:** działanie Kopiuj kończy się niepowodzeniem z następującym błędem:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Przyczyna:** Gdy serwer tokenu usługi (STS) należący do usługi Azure Active Directory jest niedostępny, oznacza to, że jest on zbyt zajęty do obsługi żądań i zwraca błąd HTTP 503. 

- **Rozwiązanie:** po kilku minutach ponownie uruchomić działanie kopiowania.


## <a name="azure-data-lake-storage-gen2"></a>Usługa Azure Data Lake Storage 2. generacji

### <a name="error-code-adlsgen2operationfailed"></a>Kod błędu: ADLSGen2OperationFailed

- **Komunikat:**`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Przyczyny i zalecenia:** różne przyczyny mogą prowadzić do tego błędu. Sprawdź poniżej listę możliwych analiz przyczyn i powiązanych zaleceń.

  | Analiza przyczyn                                               | Zalecenie                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Jeśli Azure Data Lake Storage Gen2 zgłasza błąd wskazujący, że niektóre działania nie powiodły się.| Sprawdź szczegółowy komunikat o błędzie zgłoszony przez Azure Data Lake Storage Gen2. Jeśli błąd jest błędem przejściowym, spróbuj ponownie wykonać operację. Aby uzyskać dalszą pomoc, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania w komunikacie o błędzie. |
  | Jeśli komunikat o błędzie zawiera ciąg "Zabronione", jednostki usługi lub tożsamości zarządzanej, których używasz, mogą nie mieć wystarczających uprawnień dostępu do Azure Data Lake Storage Gen2. | Aby rozwiązać ten problem, zobacz [Kopiowanie i przekształcanie danych](./connector-azure-data-lake-storage.md#service-principal-authentication)w Azure Data Lake Storage Gen2 przy użyciu Azure Data Factory . |
  | Jeśli komunikat o błędzie zawiera ciąg "InternalServerError", błąd jest zwracany przez Azure Data Lake Storage Gen2. | Błąd może być spowodowany błędem przejściowym. Jeśli jest, spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną usługi Azure Storage i podaj identyfikator żądania z komunikatu o błędzie. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Żądanie do Azure Data Lake Storage Gen2 konta spowodowało błąd limitu czasu

- **Komunikat**: 
  * Kod błędu = `UserErrorFailedBlobFSOperation`
  * Komunikat o błędzie = `BlobFS operation failed for: A task was canceled.`

- **Przyczyna:** Problem jest spowodowany przez Azure Data Lake Storage Gen2 limitu czasu ujścia, który zazwyczaj występuje na maszynie z Integration Runtime (IR).

- **Rekomendacja**: 

    - Jeśli to możliwe, umieść swoją maszynę własnego Azure Data Lake Storage Gen2 IR i konto docelowe w tym samym regionie. Może to pomóc uniknąć losowego błędu limitu czasu i uzyskać lepszą wydajność.

    - Sprawdź, czy istnieje specjalne ustawienie sieci, takie jak ExpressRoute, i upewnij się, że sieć ma wystarczającą przepustowość. Zalecamy obniżenie ustawienia Współbieżne zadania własnego ir (SELF-hosted), gdy ogólna przepustowość jest niska. Może to pomóc uniknąć konkurencji zasobów sieciowych w wielu współbieżnych zadaniach.

    - Jeśli rozmiar pliku jest umiarkowany lub mały, użyj mniejszego rozmiaru bloku dla kopii niebinarnych, aby ograniczyć taki błąd limitu czasu. Aby uzyskać więcej informacji, zobacz [Blob Storage Put Block](/rest/api/storageservices/put-block).

       Aby określić niestandardowy rozmiar bloku, edytuj właściwość w edytorze plików JSON, jak pokazano poniżej:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files magazynu

### <a name="error-code-azurefileoperationfailed"></a>Kod błędu: AzureFileOperationFailed

- **Komunikat:**`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Przyczyna:** Problem z Azure Files magazynu.

- **Zalecenie:** Aby sprawdzić szczegóły błędu, [zobacz Azure Files pomocy.](/rest/api/storageservices/file-service-error-codes) Aby uzyskać dalszą pomoc, skontaktuj się z zespołem Azure Files.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics, Azure SQL Database i SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Kod błędu: SqlFailedToConnect

- **Komunikat:**`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Przyczyny i zalecenia:** Różne przyczyny mogą prowadzić do tego błędu. Sprawdź poniżej listę możliwych analiz przyczyn i powiązanych zaleceń.

    | Analiza przyczyn                                               | Zalecenie                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Jeśli Azure SQL komunikat o błędzie zawiera ciąg "SqlErrorNumber=47073", oznacza to, że w ustawieniu łączności zostanie odrzucony dostęp do sieci publicznej. | Na zaporze Azure SQL ustaw opcję Odmów dostępu do **sieci publicznej** na *wartość Nie.* Aby uzyskać więcej informacji, [zobacz Azure SQL ustawienia łączności.](../azure-sql/database/connectivity-settings.md#deny-public-network-access) |
    | Jeśli Azure SQL komunikat o błędzie zawiera kod błędu SQL, taki jak "SqlErrorNumber=[kod błędu]", zobacz Azure SQL rozwiązywania problemów. | Aby uzyskać rekomendację, zobacz Troubleshoot connectivity issues and other errors with Azure SQL Database and Azure SQL Managed Instance (Rozwiązywanie problemów z łącznością i innymi błędami [Azure SQL Database i Azure SQL Managed Instance).](../azure-sql/database/troubleshoot-common-errors-issues.md) |
    | Sprawdź, czy port 1433 znajduje się na liście zezwalań zapory. | Aby uzyskać więcej informacji, zobacz [Porty używane przez SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Jeśli komunikat o błędzie zawiera ciąg "SqlException", SQL Database błąd wskazuje, że niektóre konkretne działania nie powiodły się. | Aby uzyskać więcej informacji, wyszukaj według kodu błędu SQL w [błędach aparatu bazy danych](/sql/relational-databases/errors-events/database-engine-events-and-errors). Aby uzyskać dalszą pomoc, skontaktuj się Azure SQL pomocą techniczną. |
    | Jeśli jest to problem przejściowy (na przykład niestabilne połączenie sieciowe), dodaj ponawianie próby w zasadach działania, aby rozwiązać ten problem. | Aby uzyskać więcej informacji, zobacz [Potoki i działania w Azure Data Factory](./concepts-pipelines-activities.md#activity-policy). |
    | Jeśli komunikat o błędzie zawiera ciąg "Klient z adresem IP "...", nie może uzyskać dostępu do serwera" i próbujesz nawiązać połączenie z usługą Azure SQL Database, błąd jest zwykle spowodowany przez Azure SQL Database zapory. | W konfiguracji zapory Azure SQL Server włącz opcję Zezwalaj usługom i zasobom platformy Azure na **dostęp do tego serwera.** Aby uzyskać więcej informacji, [zobacz Azure SQL Database i Azure Synapse reguły zapory adresów IP.](../azure-sql/database/firewall-configure.md) |
    
### <a name="error-code-sqloperationfailed"></a>Kod błędu: SqlOperationFailed

- **Komunikat**: `A database operation failed. Please search error to get more details.`

- **Przyczyny i zalecenia:** Różne przyczyny mogą prowadzić do tego błędu. Sprawdź poniżej listę możliwych analiz przyczyn i powiązanych zaleceń.

    | Analiza przyczyn                                               | Zalecenie                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Jeśli komunikat o błędzie zawiera ciąg "SqlException", SQL Database zgłasza błąd wskazujący, że niektóre określone działania nie powiodły się. | Jeśli błąd SQL nie jest jasny, spróbuj zmienić bazę danych na najnowszy poziom zgodności "150". Może zgłaszać błędy SQL w najnowszej wersji. Więcej informacji zawiera strona [dokumentacji](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Aby uzyskać więcej informacji na temat rozwiązywania problemów z bazą danych SQL, wyszukaj według kodu błędu SQL w te [tematze Database engine errors (Błędy aparatu bazy danych).](/sql/relational-databases/errors-events/database-engine-events-and-errors) Aby uzyskać dalszą pomoc, skontaktuj się Azure SQL pomocą techniczną. |
    | Jeśli komunikat o błędzie zawiera ciąg "PdwManagedToNativeInteropException", jest to zwykle spowodowane niezgodnością rozmiarów kolumn źródła i ujścia. | Sprawdź rozmiar kolumn źródła i ujścia. Aby uzyskać dalszą pomoc, skontaktuj się Azure SQL pomocą techniczną. |
    | Jeśli komunikat o błędzie zawiera ciąg "InvalidOperationException", jest on zwykle spowodowany nieprawidłowymi danymi wejściowymi. | Aby ustalić, który wiersz napotkał problem, włącz funkcję odporności na uszkodzenia w działaniu kopiowania, która może przekierowywać problematyczne wiersze do magazynu w celu dalszej badania. Aby uzyskać więcej informacji, zobacz [Odporność na uszkodzenia działania kopiowania w Azure Data Factory](./copy-activity-fault-tolerance.md). |


### <a name="error-code-sqlunauthorizedaccess"></a>Kod błędu: SqlUnauthorizedAccess

- **Komunikat:**`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Przyczyna:** Poświadczenia są niepoprawne lub konto logowania nie może uzyskać dostępu do bazy danych SQL.

- **Zalecenie:** Upewnij się, że konto logowania ma wystarczające uprawnienia, aby uzyskać dostęp do bazy danych SQL.


### <a name="error-code-sqlopenconnectiontimeout"></a>Kod błędu: SqlOpenConnectionTimeout

- **Komunikat:**`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Przyczyna:** Problem może dotyczyć przejściowego błędu bazy danych SQL.

- **Zalecenie:** Ponów próbę wykonania operacji w celu zaktualizowania parametrów połączenia połączonej usługi przy użyciu większej wartości limitu czasu połączenia.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Kod błędu: SqlAutoCreateTableTypeMapFailed

- **Komunikat:**`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Przyczyna:** Tabela autotworzenie nie może spełniać wymagania źródłowego.

- **Zalecenie:** Zaktualizuj typ kolumny w *mapowaniach* lub ręcznie utwórz tabelę ujścia na serwerze docelowym.


### <a name="error-code-sqldatatypenotsupported"></a>Kod błędu: SqlDataTypeNotSupported

- **Komunikat:**`A database operation failed. Check the SQL errors.`

- **Przyczyna:** Jeśli problem występuje w źródle SQL, a błąd jest związany z przepełnieniem SqlDateTime, wartość danych przekracza zakres typów logiki (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Zalecenie:** Rzutuj typ na ciąg w źródłowym zapytaniu SQL lub w mapowaniu kolumny działania kopiowania zmień typ kolumny na *Ciąg*.

- **Przyczyna:** Jeśli problem występuje w ujściu SQL, a błąd jest związany z przepełnieniem SqlDateTime, wartość danych przekracza dozwolony zakres w tabeli ujścia.

- **Zalecenie:** Zaktualizuj odpowiedni typ kolumny do typu *datetime2* w tabeli ujścia.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Kod błędu: SqlInvalidDbStoredProcedure

- **Komunikat**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Przyczyna:** Określona procedura składowana jest nieprawidłowa. Przyczyną może być to, że procedura składowana nie zwraca żadnych danych.

- **Zalecenie:** zweryfikuj procedurę składowaną przy użyciu narzędzi SQL Tools. Upewnij się, że procedura składowana może zwrócić dane.


### <a name="error-code-sqlinvaliddbquerystring"></a>Kod błędu: SqlInvalidDbQueryString

- **Komunikat**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Przyczyna:** Określone zapytanie SQL jest nieprawidłowe. Przyczyną może być to, że zapytanie nie zwraca żadnych danych.

- **Zalecenie:** Zweryfikuj zapytanie SQL przy użyciu narzędzi SQL Tools. Upewnij się, że zapytanie może zwrócić dane.


### <a name="error-code-sqlinvalidcolumnname"></a>Kod błędu: SqlInvalidColumnName

- **Komunikat**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Przyczyna:** Nie można odnaleźć kolumny, ponieważ konfiguracja może być nieprawidłowa.

- **Zalecenie:** Zweryfikuj kolumnę w zapytaniu, *strukturę* w zestawie danych i *mapowania* w działaniu.


### <a name="error-code-sqlbatchwritetimeout"></a>Kod błędu: SqlBatchWriteTimeout

- **Komunikat**: `Timeouts in SQL write operation.`

- **Przyczyna:** Problem może być spowodowany przejściowym błędem bazy danych SQL.

- **Zalecenie:** Ponów próbę wykonania operacji. Jeśli problem będzie się powtarzać, skontaktuj się Azure SQL pomocą techniczną.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Kod błędu: SqlBatchWriteTransactionFailed

- **Komunikat:**`SQL transaction commits failed.`

- **Przyczyna:** Jeśli szczegóły wyjątku stale wskazują limit czasu transakcji, opóźnienie sieci między środowiskiem Integration Runtime i bazą danych jest większe niż domyślny próg 30 sekund.

- **Zalecenie:** Zaktualizuj parametrów połączenia usługi połączonej SQL z wartością limitu czasu połączenia, która jest równa lub większa niż 120 i ponownie uruchomić działanie. 

- **Przyczyna:** Jeśli szczegóły wyjątku sporadycznie wskazują na uszkodzenie połączenia SQL, może to być przejściowy błąd sieci lub problem po stronie bazy danych SQL.

- **Zalecenie:** Ponów próbę działania i przejrzyj metryki po stronie bazy danych SQL.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Kod błędu: SqlBulkCopyInvalidColumnLength

- **Komunikat:**`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Przyczyna:** Kopiowanie zbiorcze SQL nie powiodło się, ponieważ otrzymało nieprawidłową długość kolumny od klienta narzędzia do kopiowania zbiorczego (bcp).

- **Zalecenie:** Aby zidentyfikować wiersz, w którym wystąpił problem, włącz funkcję odporności na uszkodzenia w działaniu kopiowania. Może to przekierować problematyczne wiersze do magazynu w celu dalszego badania. Aby uzyskać więcej informacji, zobacz [Odporność na uszkodzenia działania kopiowania w Azure Data Factory](./copy-activity-fault-tolerance.md).


### <a name="error-code-sqlconnectionisclosed"></a>Kod błędu: SqlConnectionIsClosed

- **Komunikat:**`The connection is closed by SQL Database.`

- **Przyczyna:** Połączenie SQL jest zamykane przez bazę danych SQL, gdy wysokie współbieżne uruchomienie i serwer przerywa połączenie.

- **Zalecenie:** Ponów próbę połączenia. Jeśli problem będzie się powtarzać, skontaktuj się z Azure SQL pomocą techniczną.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Komunikat o błędzie: Konwersja nie powiodła się podczas konwertowania ciągu znaków na uniqueidentifier

- **Objawy:** Podczas kopiowania danych z tabelarygo źródła danych (takiego jak SQL Server) do programu Azure Synapse Analytics za pomocą kopiowania etapowego i technologii PolyBase pojawia się następujący błąd:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Przyczyna:** Azure Synapse Analytics polyBase nie może przekonwertować pustego ciągu na identyfikator GUID.

- **Rozwiązanie:** w ujściu działania kopiowania w obszarze ustawień programu PolyBase ustaw dla opcji **użyj typu domyślnego** wartość *false.*


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Komunikat o błędzie: Oczekiwany typ danych: DECIMAL(x,x), Wartość odejmowania

- **Objawy:** Podczas kopiowania danych z tabelarygo źródła danych (takiego jak SQL Server) do programu Azure Synapse Analytics za pomocą kopiowania etapowego i technologii PolyBase pojawia się następujący błąd:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Przyczyna:** Azure Synapse Analytics polyBase nie może wstawić pustego ciągu (wartości null) do kolumny dziesiętnej.

- **Rozwiązanie:** W ujściu działania kopiowania w obszarze Ustawienia programu PolyBase ustaw wartość false dla opcji użyj **typu domyślnego.**


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Komunikat o błędzie: Komunikat o wyjątku Java: HdfsBridge::CreateRecordReader

- **Objawy:** Podczas kopiowania danych Azure Synapse Analytics przy użyciu technologii PolyBase występuje następujący błąd:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Przyczyna:** Przyczyną może być zbyt duży (większy niż 1 MB) schemat (całkowita szerokość kolumny). Sprawdź schemat tabeli Azure Synapse Analytics, dodając rozmiar wszystkich kolumn:

    - Liczba całkowita = 4 bajty
    - Bigint = 8 bajtów
    - Varchar(n), char(n), binary(n), varbinary(n) = n bajtów
    - Nvarchar(n), nchar(n) = n*2 bajty
    - Data = 6 bajtów
    - Datetime/(2), smalldatetime = 16 bajtów
    - Datetimeoffset = 20 bajtów
    - Liczba dziesiętna = 19 bajtów
    - Liczba zmiennoprzecinkowa = 8 bajtów
    - Pieniądze = 8 bajtów
    - Small nieduzyska = 4 bajty
    - Rzeczywiste = 4 bajty
    - Smallint = 2 bajty
    - Czas = 12 bajtów
    - Tinyint = 1 bajt

- **Rozwiązanie**: 
    - Zmniejsz szerokość kolumny do mniej niż 1 MB.
    - Możesz też użyć metody zbiorczego wstawiania, wyłączając polyBase.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Komunikat o błędzie: Warunek określony przy użyciu nagłówków warunkowych HTTP nie jest spełniony

- **Objawy:** Zapytanie SQL jest wykorzystywane do ściągania danych z Azure Synapse Analytics i otrzymywania następującego błędu:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Przyczyna:** Azure Synapse Analytics wystąpił problem podczas wykonywania zapytania o tabelę zewnętrzną w usłudze Azure Storage.

- **Rozwiązanie:** Uruchom to samo zapytanie w programie SQL Server Management Studio (SSMS) i sprawdź, czy wynik jest taki sam. Jeśli to zrobisz, otwórz bilet pomocy technicznej, aby Azure Synapse Analytics i podać Azure Synapse Analytics nazwę serwera i bazy danych.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Warstwa wydajności jest niska i prowadzi do błędu kopiowania

- **Objawy:** Skopiuj dane do Azure SQL Database i zostanie wyświetlony następujący błąd: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Przyczyna:** Azure SQL Database s1 ma limity we/wy.

- **Rozwiązanie:** uaktualnij Azure SQL Database wydajności, aby rozwiązać ten problem. 


### <a name="sql-table-cant-be-found"></a>Nie można odnaleźć tabeli SQL 

- **Objawy:** Skopiuj dane z hybrydowej do lokalnej tabeli SQL Server i zostanie wyświetlony następujący błąd:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Przyczyna:** Bieżące konto SQL nie ma wystarczających uprawnień do wykonywania żądań wystawionych przez .NET SqlBulkCopy.WriteToServer.

- **Rozwiązanie:** Przełącz się na bardziej uprzywilejowane konto SQL.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Komunikat o błędzie: Dane ciągu lub danych binarnych są obcinane

- **Objawy:** Podczas kopiowania danych do lokalnej tabeli programu Azure SQL Server występuje błąd. 

- **Przyczyna:** Definicja schematu tabeli SQL języka Cx ma co najmniej jedną kolumnę o mniejszej długości niż oczekiwano.

- **Rozwiązanie:** Aby rozwiązać ten problem, spróbuj wykonać następujące czynności:

    1. Aby rozwiązać problem z wierszami, zastosuj odporność na uszkodzenia [ujścia](./copy-activity-fault-tolerance.md)SQL, szczególnie "redirectIncompatibleRowSettings".

        > [!NOTE]
        > Odporność na uszkodzenia może wymagać dodatkowego czasu wykonywania, co może prowadzić do wyższych kosztów.

    2. Sprawdź, które przekierowane dane mają długość kolumny schematu tabeli SQL, aby zobaczyć, które kolumny należy zaktualizować.

    3. Odpowiednio zaktualizuj schemat tabeli.


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Kod błędu: AzureTableDuplicateColumnsFromSource

- **Komunikat**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Przyczyna:** Zduplikowane kolumny źródłowe mogą wystąpić z jednej z następujących przyczyn:
   * Używasz bazy danych jako źródła i zastosowanych sprzężenia tabel.
   * Masz nieustrukturyzowane pliki CSV ze zduplikowanymi nazwami kolumn w wierszu nagłówka.

- **Zalecenie:** W razie potrzeby sprawdź i napraw kolumny źródłowe.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Kod błędu: DB2DriverRunFailed

- **Komunikat**: `Error thrown from driver. Sql code: '%code;'`

- **Przyczyna:** Jeśli komunikat o błędzie zawiera ciąg "SQLSTATE=51002 SQLCODE=-805", postępuj zgodnie z instrukcje z tematu "Porada" w części Kopiowanie danych z bazy danych [DB2](./connector-db2.md#linked-service-properties)przy użyciu Azure Data Factory .

- **Zalecenie:** Spróbuj ustawić wartość "NULLID" we właściwości `packageCollection`  .


## <a name="delimited-text-format"></a>Format tekstu rozdzielanego

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Kod błędu: DelimitedTextColumnNameNotAllowNull

- **Komunikat**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Przyczyna:** Jeśli w działaniu zostanie ustawiona wartość "firstRowAsHeader", jako nazwa kolumny zostanie użyty pierwszy wiersz. Ten błąd oznacza, że pierwszy wiersz zawiera pustą wartość (na przykład "ColumnA, ColumnB").

- **Zalecenie:** Sprawdź pierwszy wiersz i napraw wartość, jeśli jest pusta.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Kod błędu: DelimitedTextMoreColumnsThanDefined

- **Komunikat:**`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Przyczyny i zalecenia:** różne przyczyny mogą prowadzić do tego błędu. Sprawdź poniżej listę możliwych analiz przyczyn i powiązanych zaleceń.

  | Analiza przyczyn                                               | Zalecenie                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Liczba kolumn problematycznych wierszy jest większa niż liczba kolumn pierwszego wiersza. Może to być spowodowane problemem z danymi, nieprawidłowym ogranicznikiem kolumny lub ustawieniami znaków cudzysłowu. | Pobierz liczbę wierszy z komunikatu o błędzie, sprawdź kolumnę wiersza i napraw dane. |
  | Jeśli oczekiwana liczba kolumn w komunikacie o błędzie to "1", być może określono nieprawidłowe ustawienia kompresji lub formatu, które Data Factory niepoprawną analizę plików. | Sprawdź ustawienia formatu, aby upewnić się, że są one zgodne z Twoimi plikami źródłowymi. |
  | Jeśli źródłem jest folder, pliki w określonym folderze mogą mieć inny schemat. | Upewnij się, że pliki w określonym folderze mają identyczny schemat. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service i Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Kod błędu: DynamicsCreateServiceClientError

- **Komunikat:**`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Przyczyna:** Problem jest przejściowym problemem po stronie serwera Dynamics.

- **Zalecenie:** Ponownie uruchomić potok. Jeśli to się nie powiedzie ponownie, spróbuj zmniejszyć równoległość. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną usługi Dynamics.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Brak kolumn podczas importowania schematu lub podglądu danych

- **Objawy:** Brakuje niektórych kolumn podczas importowania schematu lub podglądu danych. Komunikat o błędzie: `The valid structure information (column name and type) are required for Dynamics source.`

- **Przyczyna:** Ten problem jest zgodnie z projektem, ponieważ Data Factory nie może wyświetlić kolumn, które nie zawierają żadnych wartości w pierwszych 10 rekordach. Upewnij się, że dodane kolumny mają prawidłowy format. 

- **Zalecenie:** ręcznie dodaj kolumny na karcie mapowania.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Kod błędu: DynamicsMissingTargetForMultiTargetLookupField

- **Komunikat**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Przyczyna:** Kolumna docelowa nie istnieje w źródle ani w mapowaniu kolumn.

- **Rekomendacja**:  
  1. Upewnij się, że źródło zawiera kolumnę docelową. 
  2. Dodaj kolumnę docelową w mapowaniu kolumny. Upewnij się, że kolumna ujścia ma format *{fieldName} @EntityReference*.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Kod błędu: DynamicsInvalidTargetForMultiTargetLookupField

- **Komunikat**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Przyczyna:** Nieprawidłowa nazwa jednostki jest podano jako jednostkę docelową pola wyszukiwania o wielu elementach docelowych.

- **Zalecenie:** Podaj prawidłową nazwę jednostki dla pola wyszukiwania o wielu elementach docelowych.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Kod błędu: DynamicsInvalidTypeForMultiTargetLookupField

- **Komunikat**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Przyczyna:** Wartość w kolumnie docelowej nie jest ciągiem.

- **Zalecenie:** Podaj prawidłowy ciąg w kolumnie docelowej wyszukiwania o wielu elementach docelowych.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Kod błędu: DynamicsFailedToRequetServer

- **Komunikat:**`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Przyczyna:** Serwer dynamics jest niestabilny lub niedostępny albo w sieci występują problemy.

- **Zalecenie:** Aby uzyskać więcej informacji, sprawdź łączność sieciową lub sprawdź dziennik serwera Dynamics. Aby uzyskać dalszą pomoc, skontaktuj się z pomocą techniczną usługi Dynamics.


### <a name="error-code--dynamicsfailedtoconnect"></a>Kod błędu: DynamicsFailedToConnect 
 
 - **Komunikat:**`Failed to connect to Dynamics: %message;` 
 

 - **Przyczyna:** Jeśli zostanie wyświetlony komunikat o błędzie, oznacza to, że serwer może mieć pewne konfiguracje `Office 365 auth with OAuth failed` niezgodne z uwierzytelnianiem OAuth. 
 
 - **Rekomendacja**: 
    1. Aby uzyskać pomoc, skontaktuj się z zespołem pomocy technicznej usługi Dynamics, aby uzyskać szczegółowy komunikat o błędzie.  
    1. Użyj uwierzytelniania jednostki usługi i zapoznaj się z tym artykułem: [Przykład: Dynamics online using Azure AD service-principal and certificate authentication (Przykład: Dynamics online przy](https://docs.microsoft.com/azure/data-factory/connector-dynamics-crm-office-365#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication)użyciu jednostki usługi Azure AD i uwierzytelniania certyfikatu). 
 

 - **Przyczyna:** Jeśli w komunikacie o błędzie zostanie wyświetlony komunikat o błędzie, oznacza to, że w celu przechwycenia ruchu należy wprowadzić nieprawidłowy adres URL usługi Dynamics lub serwer `Unable to retrieve authentication parameters from the serviceUri` proxy/zaporę. 
 
 - **Rekomendacja**:
    1. Upewnij się, że w połączonej usłudze umieściliśmy prawidłowy URI usługi. 
    1. W przypadku korzystania z własnego ir ir (Self Hosted) upewnij się, że zapora/serwer proxy nie przechwytuje żądań do serwera Dynamics. 
   
 
 - **Przyczyna:** Jeśli w komunikacie o błędzie zostanie wyświetlony komunikat o błędzie, oznacza to, że po stronie serwera wystąpiły `An unsecured or incorrectly secured fault was received from the other party` nieoczekiwane odpowiedzi. 
 
 - **Rekomendacja**: 
    1. Upewnij się, że nazwa użytkownika i hasło są poprawne, jeśli używasz uwierzytelniania usługi Office 365. 
    1. Upewnij się, że w danych wejściowych jest prawidłowy URI usługi. 
    1. Jeśli używasz regionalnego adresu URL crm (adres URL ma numer po 'crm'), upewnij się, że używasz poprawnego identyfikatora regionalnego.
    1. Skontaktuj się z zespołem pomocy technicznej usługi Dynamics, aby uzyskać pomoc. 
 

 - **Przyczyna:** Jeśli w komunikacie o błędzie zostanie wyświetlony komunikat o błędzie, oznacza to, że nazwa organizacji jest nieprawidłowa lub w adresie URL usługi został użyty `No Organizations Found` nieprawidłowy identyfikator regionu CRM. 
 
 - **Rekomendacja**: 
    1. Upewnij się, że w danych wejściowych jest prawidłowy URI usługi.
    1. Jeśli używasz regionalnego adresu URL crm (adres URL ma numer po 'crm'), upewnij się, że używasz poprawnego identyfikatora regionalnego. 
    1. Skontaktuj się z zespołem pomocy technicznej usługi Dynamics, aby uzyskać pomoc. 

 
 - **Przyczyna:** Jeśli zostanie wyświetlony komunikat o błędzie związany z usługą AAD, oznacza to, że wystąpił problem z `401 Unauthorized` jednostką usługi. 

 - **Zalecenie:** Postępuj zgodnie ze wskazówkami w komunikacie o błędzie, aby rozwiązać problem z jednostką usługi.  
 
 
 - **Przyczyna:** W przypadku innych błędów zwykle problem występuje po stronie serwera. 

 - **Zalecenie:** Użyj [narzędzia XrmToolBox,](https://www.xrmtoolbox.com/) aby nawiązaniu połączenia. Jeśli błąd będzie się powtarzać, skontaktuj się z zespołem pomocy technicznej usługi Dynamics, aby uzyskać pomoc. 
 
 
### <a name="error-code--dynamicsoperationfailed"></a>Kod błędu: DynamicsOperationFailed 
 
- **Komunikat**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Przyczyna:** Operacja nie powiodła się po stronie serwera. 

- **Zalecenie:** wyodrębnij kod błędu operacji dynamics z komunikatu o błędzie: i zapoznaj się z artykułem Kody błędów usługi internetowej, aby `Dynamics operation failed with error code: {code}` uzyskać bardziej szczegółowe [](https://docs.microsoft.com/powerapps/developer/data-platform/org-service/web-service-error-codes) informacje. W razie potrzeby możesz skontaktować się z zespołem pomocy technicznej usługi Dynamics. 
 
 
### <a name="error-code--dynamicsinvalidfetchxml"></a>Kod błędu: DynamicsInvalidFetchXml 
  
- **Komunikat**: `The Fetch Xml query specified is invalid.` 

- **Przyczyna:** W pliku XML pobierania istnieje błąd.  

- **Zalecenie:** Naprawienie błędu w pobranym pliku XML. 
 
 
### <a name="error-code--dynamicsmissingkeycolumns"></a>Kod błędu: DynamicsMissingKeyColumns 
 
- **Komunikat**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Przyczyna:** Dane źródłowe nie zawierają kolumny klucza dla jednostki ujścia. 

- **Zalecenie:** Upewnij się, że kolumny kluczy znajdują się w danych źródłowych, lub zamapuj kolumnę źródłową na kolumnę klucza w jednostce ujścia. 
 
 
### <a name="error-code--dynamicsprimarykeymustbeguid"></a>Kod błędu: DynamicsPrimaryKeyMustBeGuid 
 
- **Komunikat:**`The primary key attribute '%attribute;' must be of type guid.` 
 
- **Przyczyna:** Typ kolumny klucza podstawowego nie jest "Identyfikator GUID". 
 
- **Zalecenie:** Upewnij się, że kolumna klucza podstawowego w danych źródłowych ma typ "Guid". 
 

### <a name="error-code--dynamicsalternatekeynotfound"></a>Kod błędu: DynamicsAlternateKeyNotFound 
 
- **Komunikat:**`Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Przyczyna:** Podany klucz alternatywny nie istnieje, co może być spowodowane nieprawidłowymi nazwami kluczy lub niewystarczającymi uprawnieniami. 
 
- **Rekomendacja**: <br/> 
    1. Naprawiono literówki w nazwie klucza.<br/> 
    1. Upewnij się, że masz wystarczające uprawnienia do jednostki. 
 
 
### <a name="error-code--dynamicsinvalidschemadefinition"></a>Kod błędu: DynamicsInvalidSchemaDefinition 
 
- **Komunikat:**`The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Przyczyna:** Kolumny ujścia w mapowaniu kolumn pomijają właściwość "type". 
 
- **Zalecenie:** możesz dodać właściwość "type" do tych kolumn w mapowaniu kolumn przy użyciu edytora JSON w portalu. 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Kod błędu: FtpFailedToConnectToFtpServer

- **Komunikat:**`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Przyczyna:** Dla serwera FTP może być używany nieprawidłowy typ połączonej usługi, na przykład użycie połączonej usługi Secure FTP (SFTP) w celu nawiązania połączenia z serwerem FTP.

- **Zalecenie:** Sprawdź port serwera docelowego. Protokół FTP używa portu 21.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Kod błędu: HttpFileFailedToRead

- **Komunikat:**`Failed to read data from http server. Check the error from http server：%message;`

- **Przyczyna:** Ten błąd występuje, Azure Data Factory się z serwerem HTTP, ale operacja żądania HTTP kończy się niepowodzeniem.

- **Zalecenie:** Sprawdź kod stanu HTTP w komunikacie o błędzie i rozwiąż problem z serwerem zdalnym.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Kod błędu: ArgumentOutOfRangeException

- **Komunikat**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Przyczyna:** W Data Factory wartości DateTime są obsługiwane w zakresie od 0001-01-01 00:00:00 do 9999-12-31 23:59:59. Jednak firma Oracle obsługuje szerszy zakres wartości daty/godziny, takich jak bc century lub min/s>59, co prowadzi do awarii Data Factory.

- **Rekomendacja**: 

    Aby sprawdzić, czy wartość w programie Oracle znajduje się w zakresie Data Factory, `select dump(<column name>)` uruchom . 

    Aby poznać sekwencję bajtów w wyniku, zobacz How are dates stored in Oracle? (Jak [są przechowywane daty w programie Oracle?).](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)


## <a name="orc-format"></a>Format ORC

### <a name="error-code-orcjavainvocationexception"></a>Kod błędu: OrcJavaInvocationException

- **Komunikat**: `An error occurred when invoking Java, message: %javaException;.`
- **Przyczyny i zalecenia:** Różne przyczyny mogą prowadzić do tego błędu. Sprawdź poniżej listę możliwych analiz przyczyn i powiązanych zaleceń.

    | Analiza przyczyn                                               | Zalecenie                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Gdy komunikat o błędzie zawiera ciągi "java.lang.OutOfMemory", "Przestrzeń sterty Java" i "doubleCapacity", zazwyczaj jest to problem z zarządzaniem pamięcią w starej wersji środowiska Integration Runtime. | Jeśli używasz aplikacji hostowanych samodzielnie Integration Runtime, zalecamy uaktualnienie do najnowszej wersji. |
    | Gdy komunikat o błędzie zawiera ciąg "java.lang.OutOfMemory", środowisko Integration Runtime nie ma wystarczającej ilości zasobów do przetwarzania plików. | Ogranicz współbieżne przebiegi w środowisku Integration Runtime. W przypadku własnego ir ir (Self-hosted) przeskaluj w górę do zaawansowanej maszyny z pamięcią równą lub większą niż 8 GB. |
    |Jeśli komunikat o błędzie zawiera ciąg "NullPointerReference", przyczyną może być błąd przejściowy. | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną. |
    | Jeśli komunikat o błędzie zawiera ciąg "BufferOverflowException", przyczyną może być błąd przejściowy. | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną. |
    | Jeśli komunikat o błędzie zawiera ciąg "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable, nie można rzutować na org.apache.hadoop.io.Text", przyczyną może być problem z konwersją typu w środowisku uruchomieniowym języka Java. Zazwyczaj oznacza to, że dane źródłowe nie mogą być obsługiwane dobrze w środowisku uruchomieniowym Języka Java. | Jest to problem z danymi. Spróbuj użyć ciągu zamiast znaków lub varchar w danych formatu ORC. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Kod błędu: OrcDateTimeExceedLimit

- **Komunikat:**`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Przyczyna:** Jeśli wartość daty/godziny to '0001-01-01 00:00:00', może to być spowodowane różnicami między kalendarzem z gregoriańskim a [kalendarzem gregoriańskim.](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)

- **Zalecenie:** Sprawdź wartość znaczników i unikaj używania wartości daty/godziny "0001-01-01 00:00:00".


## <a name="parquet-format"></a>Format Parquet

### <a name="error-code-parquetjavainvocationexception"></a>Kod błędu: ParquetJavaInvocationException

- **Komunikat:**`An error occurred when invoking java, message: %javaException;.`

- **Przyczyny i zalecenia:** różne przyczyny mogą prowadzić do tego błędu. Sprawdź poniżej listę możliwych analiz przyczyn i powiązanych zaleceń.

    | Analiza przyczyn                                               | Zalecenie                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Gdy komunikat o błędzie zawiera ciągi "java.lang.OutOfMemory", "Przestrzeń sterty Java" i "doubleCapacity", zazwyczaj jest to problem z zarządzaniem pamięcią w starej wersji Integration Runtime. | Jeśli używasz własnego oprogramowania IR i wersja jest wcześniejsza niż 3.20.7159.1, zalecamy uaktualnienie do najnowszej wersji. |
    | Gdy komunikat o błędzie zawiera ciąg "java.lang.OutOfMemory", środowisko Integration Runtime nie ma wystarczającej ilości zasobów do przetwarzania plików. | Ogranicz współbieżne przebiegi w środowisku Integration Runtime. W przypadku własnego ir ir (Self-hosted) przeskaluj w górę do zaawansowanej maszyny z pamięcią równą lub większą niż 8 GB. |
    | Jeśli komunikat o błędzie zawiera ciąg "NullPointerReference", może to być błąd przejściowy. | Spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną. |

### <a name="error-code-parquetinvalidfile"></a>Kod błędu: ParquetInvalidFile

- **Komunikat**: `File is not a valid Parquet file.`

- **Przyczyna:** Jest to problem z plikiem Parquet.

- **Zalecenie:** Sprawdź, czy dane wejściowe są prawidłowym plikiem Parquet.


### <a name="error-code-parquetnotsupportedtype"></a>Kod błędu: ParquetNotSupportedType

- **Komunikat**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Przyczyna:** Format Parquet nie jest obsługiwany w Azure Data Factory.

- **Zalecenie:** Sprawdź dane źródłowe, przechodząc do tematu Obsługiwane formaty plików i [koderów-ów kompresji](./supported-file-formats-and-compression-codecs.md)przez działanie kopiowania w Azure Data Factory .


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Kod błędu: ParquetMissedDecimalPrecisionScale

- **Komunikat**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Przyczyna:** Liczba precyzji i skali zostały przejmowane, ale nie podano żadnych takich informacji.

- **Zalecenie:** Źródło nie zwraca poprawnej dokładności i informacji o skali. Sprawdź informacje w kolumnie problemu.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Kod błędu: ParquetInvalidDecimalPrecisionScale

- **Komunikat**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Przyczyna:** Schemat jest nieprawidłowy.

- **Zalecenie:** Sprawdź dokładność i skalę w kolumnie problemu.


### <a name="error-code-parquetcolumnnotfound"></a>Kod błędu: ParquetColumnNotFound

- **Komunikat:**`Column %column; does not exist in Parquet file.`

- **Przyczyna:** Schemat źródłowy jest niezgodnością ze schematem ujścia.

- **Zalecenie:** Sprawdź mapowania w działaniu. Upewnij się, że kolumnę źródłową można zamapować na poprawną kolumnę ujścia.


### <a name="error-code-parquetinvaliddataformat"></a>Kod błędu: ParquetInvalidDataFormat

- **Komunikat:**`Incorrect format of %srcValue; for converting to %dstType;.`

- **Przyczyna:** Danych nie można przekonwertować na typ określony w pliku mappings.source.

- **Zalecenie:** Dokładnie sprawdź dane źródłowe lub określ poprawny typ danych dla tej kolumny w mapowaniu kolumn działania kopiowania. Aby uzyskać więcej informacji, zobacz [Supported file formats and compression codecs by copy activity in Azure Data Factory](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Kod błędu: ParquetDataCountNotMatchColumnCount

- **Komunikat:**`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Przyczyna:** Niezgodność między wartością liczby kolumn źródłowych i liczby kolumn ujścia.

- **Zalecenie:** Sprawdź dwukrotnie, aby upewnić się, że liczba kolumn źródłowych jest taka sama jak liczba kolumn ujścia w "mapowaniu".


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Kod błędu: ParquetDataTypeNotMatchColumnType

- **Komunikat:**`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Przyczyna:** Danych ze źródła nie można przekonwertować na typ zdefiniowany w ujściu.

- **Zalecenie:** Określ poprawny typ w pliku mapping.sink.


### <a name="error-code-parquetbridgeinvaliddata"></a>Kod błędu: ParquetBridgeInvalidData

- **Komunikat:**`%message;`

- **Przyczyna:** Wartość danych przekroczyła limit.

- **Zalecenie:** Ponów próbę wykonania operacji. Jeśli problem będzie się powtarzać, skontaktuj się z nami.


### <a name="error-code-parquetunsupportedinterpretation"></a>Kod błędu: ParquetUnsupportedInterpretation

- **Komunikat**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Przyczyna:** Ten scenariusz nie jest obsługiwany.

- **Zalecenie:**"ParquetInterpretFor" nie powinno być "sparkSql".


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Kod błędu: ParquetUnsupportFileLevelCompressionOption

- **Komunikat**: `File level compression is not supported for Parquet.`

- **Przyczyna:** Ten scenariusz nie jest obsługiwany.

- **Zalecenie:** Usuń typ "CompressionType" z ładunku.


### <a name="error-code-usererrorjniexception"></a>Kod błędu: UserErrorJniException

- **Komunikat**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Przyczyna:** nie można wirtualna maszyna Java (JVM), ponieważ ustawiono niektóre niedozwolone argumenty (globalne).

- **Zalecenie:** Zaloguj się do maszyny, która *hostuje każdy węzeł* własnego ir ir. Upewnij się, że zmienna systemowa jest prawidłowo ustawiona w następujący sposób: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Uruchom ponownie wszystkie węzły IR, a następnie ponownie uruchom potok.


### <a name="arithmetic-overflow"></a>Przepełnienie arytmetyczne

- **Objawy:** Komunikat o błędzie wystąpił podczas kopiowania plików Parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Przyczyna:** Obecnie podczas kopiowania plików z programu Oracle do parquet jest obsługiwana tylko wartość dziesiętna precyzji <= 38 i długość części całkowitej <= 20. 

- **Rozwiązanie:** Aby obejść ten problem, można przekonwertować dowolne kolumny na VARCHAR2.


### <a name="no-enum-constant"></a>Brak stałej wyli

- **Objawy:** Komunikat o błędzie wystąpił podczas kopiowania danych do formatu Parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , lub: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Przyczyna:** 

    Problem może być spowodowany przez białe znaki lub nieobsługiwane znaki specjalne (takie jak; {} ()\n\t=) w nazwie kolumny, ponieważ parquet nie obsługuje takiego formatu. 

    Na przykład nazwa kolumny, taka jak *contoso(test),* analizuje typ w nawiasach kwadratowych z [kodu](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Błąd jest zgłaszany, ponieważ nie ma typu "test".

    Aby sprawdzić obsługiwane typy, przejdź do witryny [GitHub apache/parquet-mr.](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)

- **Rozwiązanie**: 

    Sprawdź, czy:
    - Nazwa kolumny ujścia zawiera białe spacje.
    - Pierwszy wiersz z białymi spacjami jest używany jako nazwa kolumny.
    - Typ OriginalType jest obsługiwany. Unikaj używania tych znaków specjalnych: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Kod błędu: RestSinkCallFailed

- **Komunikat:**`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Przyczyna:** Ten błąd występuje, Azure Data Factory się z punktem końcowym REST za pośrednictwem protokołu HTTP, a operacja żądania kończy się niepowodzeniem.

- **Zalecenie:** Sprawdź kod stanu HTTP lub komunikat w komunikacie o błędzie i rozwiąż problem z serwerem zdalnym.

### <a name="unexpected-network-response-from-the-rest-connector"></a>Nieoczekiwana odpowiedź sieciowa z łącznika REST

- **Objawy:** Punkt końcowy czasami otrzymuje nieoczekiwaną odpowiedź (400, 401, 403, 500) z łącznika REST.

- **Przyczyna:** Łącznik źródłowy REST używa adresu URL i metody/nagłówka/treści HTTP z połączonej usługi/zestawu danych/źródła kopii jako parametrów podczas konstruowania żądania HTTP. Problem jest najprawdopodobniej spowodowany błędami w co najmniej jednym określonym parametrze.

- **Rozwiązanie**: 
    - Użyj polecenia "curl" w oknie wiersza polecenia, aby sprawdzić, czy przyczyną jest parametr (nagłówki **Accept** i **User-Agent** powinny być zawsze uwzględniane):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Jeśli polecenie zwróci tę samą nieoczekiwaną odpowiedź, napraw poprzednie parametry za pomocą polecenia "curl", dopóki nie zwróci oczekiwanej odpowiedzi. 

      Możesz również użyć polecenia "curl--help", aby uzyskać bardziej zaawansowane użycie polecenia.

    - Jeśli tylko łącznik REST Data Factory zwraca nieoczekiwaną odpowiedź, skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać dalsze informacje na temat rozwiązywania problemów.
    
    - Pamiętaj, że "curl" może nie być odpowiedni do odtworzenia problemu z weryfikacją certyfikatu SSL. W niektórych scenariuszach polecenie "curl" zostało wykonane pomyślnie bez napotkania problemów z weryfikacją certyfikatu SSL. Jednak gdy ten sam adres URL jest wykonywany w przeglądarce, klient nie zwraca certyfikatu SSL w celu ustanowienia relacji zaufania z serwerem.

      W powyższym przypadku zaleca się użycie narzędzi, takich jak **Postman** i **Fiddler.**


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Kod błędu: SftpOperationFail

- **Komunikat**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Przyczyna:** Problem z operacją SFTP.

- **Zalecenie:** Sprawdź szczegóły błędu z sftp.


### <a name="error-code-sftprenameoperationfail"></a>Kod błędu: SftpRenameOperationFail

- **Komunikat**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Przyczyna:** Serwer SFTP nie obsługuje zmiany nazwy pliku tymczasowego.

- **Zalecenie:** ustaw wartość "useTempFileRename" na wartość false w ujściu kopii, aby wyłączyć przekazywanie do pliku tymczasowego.


### <a name="error-code-sftpinvalidsftpcredential"></a>Kod błędu: SftpInvalidSftpCredential

- **Komunikat**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Przyczyna:** Zawartość klucza prywatnego jest pobrana z usługi Azure Key Vault lub zestawu SDK, ale nie jest poprawnie kodowana.

- **Rekomendacja**:  

    Jeśli zawartość klucza prywatnego pochodzi z magazynu kluczy, oryginalny plik klucza może działać, jeśli przekażemy go bezpośrednio do połączonej usługi SFTP.

    Aby uzyskać więcej informacji, zobacz Kopiowanie danych z i [na serwer SFTP przy użyciu Azure Data Factory](./connector-sftp.md#use-ssh-public-key-authentication). Zawartość klucza prywatnego to zakodowana w formacie base64 zawartość klucza prywatnego SSH.

    *Zakoduj* cały oryginalny plik klucza prywatnego za pomocą kodowania base64 i przechowuj zakodowany ciąg w magazynie kluczy. Oryginalny plik klucza prywatnego jest plikiem, który może działać w połączonej usłudze SFTP, jeśli **wybierzesz pozycję Przekaż** z pliku.

    Oto kilka przykładów, których można użyć do wygenerowania ciągu:

    - Użyj kodu C#:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Użyj kodu w języku Python:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Użyj narzędzia konwersji base64 innej firmy. Zalecamy użycie narzędzia [formatu Encode to Base64.](https://www.base64encode.org/)

- **Przyczyna:** Wybrano nieprawidłowy format zawartości klucza.

- **Rekomendacja**:  

    Klucz prywatny SSH w formacie PKCS#8 (rozpoczyna się od ciągu "-----BEGIN ENCRYPTED PRIVATE KEY-----") nie jest obecnie obsługiwany w celu uzyskania dostępu do serwera SFTP w Data Factory. 

    Aby przekonwertować klucz na tradycyjny format klucza SSH, rozpoczynając od "-----BEGIN RSA PRIVATE KEY-----", uruchom następujące polecenia:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Przyczyna:** Nieprawidłowe poświadczenia lub zawartość klucza prywatnego.

- **Zalecenie:** Aby sprawdzić, czy plik klucza lub hasło są poprawne, sprawdź dwukrotnie narzędzia, takie jak WinSCP.

### <a name="sftp-copy-activity-failed"></a>Działanie kopiowania SFTP nie powiodło się

- **Objawy:** 
  * Kod błędu: UserErrorInvalidColumnMappingColumnNotFound 
  * Komunikat o błędzie: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Przyczyna:** Źródło nie zawiera kolumny o nazwie "AccMngr".

- **Rozwiązanie:** Aby ustalić, czy istnieje kolumna "AccMngr", sprawdź dokładnie konfigurację zestawu danych, mapując kolumnę docelowego zestawu danych.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Kod błędu: SftpFailedToConnectToSftpServer

- **Komunikat**: `Failed to connect to SFTP server '%server;'.`

- **Przyczyna:** Jeśli komunikat o błędzie zawiera ciąg "Uchwycono czas operacji odczytu gniazda po 30 000 milisekundach", jedną z możliwych przyczyn jest to, że serwer SFTP używa nieprawidłowego połączonego typu usługi. Na przykład możesz używać połączonej usługi FTP do nawiązywania połączenia z serwerem SFTP.

- **Zalecenie:** Sprawdź port serwera docelowego. Domyślnie sfTP używa portu 22.

- **Przyczyna:** Jeśli komunikat o błędzie zawiera ciąg "Odpowiedź serwera nie zawiera identyfikacji protokołu SSH", jedną z możliwych przyczyn jest to, że serwer SFTP ogranicza połączenie. Data Factory wiele połączeń do pobrania z serwera SFTP równolegle, a czasami wystąpi ograniczanie przepustowości serwera SFTP. Zwykle różne serwery zwracają różne błędy, gdy napotkają ograniczanie przepustowości.

- **Rekomendacja**:  

    Określ maksymalną liczbę współbieżnych połączeń zestawu danych SFTP jako 1 i ponownie uruchomić działanie kopiowania. Jeśli działanie zakończy się powodzeniem, możesz mieć pewność, że przyczyną jest ograniczanie przepustowości.

    Jeśli chcesz podwyższyć poziom niskiej przepływności, skontaktuj się z administratorem SFTP, aby zwiększyć limit liczby połączeń współbieżnych, lub wykonaj jedną z następujących czynności:

    * Jeśli używasz własnego ir (Self-hosted), dodaj adres IP własnego komputera IR do listy zezwalań.
    * Jeśli używasz usługi Azure IR, dodaj [Azure Integration Runtime IP.](./azure-integration-runtime-ip-addresses.md) Jeśli nie chcesz dodawać zakresu adresów IP do listy zezwalań serwera SFTP, zamiast tego użyj własnego ir ir(

## <a name="sharepoint-online-list"></a>Lista usługi SharePoint Online

### <a name="error-code-sharepointonlineauthfailed"></a>Kod błędu: SharePointOnlineAuthFailed

- **Komunikat**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Przyczyna:** Identyfikator i klucz jednostki usługi mogą nie być poprawnie ustawione.

- **Zalecenie:** Sprawdź zarejestrowaną aplikację (identyfikator jednostki usługi) i klucz, aby sprawdzić, czy są one prawidłowo ustawione.


## <a name="xml-format"></a>Format XML

### <a name="error-code-xmlsinknotsupported"></a>Kod błędu: XmlSinkNotSupported

- **Komunikat:**`Write data in XML format is not supported yet, choose a different format!`

- **Przyczyna:** Zestaw danych XML został użyty jako zestaw danych ujścia w działaniu kopiowania.

- **Zalecenie:** Użyj zestawu danych w innym formacie niż zestaw danych ujścia.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Kod błędu: XmlAttributeColumnNameConflict

- **Komunikat:**`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Przyczyna:** Został użyty prefiks atrybutu, co spowodowało konflikt.

- **Zalecenie:** ustaw inną wartość właściwości "attributePrefix".


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Kod błędu: XmlValueColumnNameConflict

- **Komunikat:**`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Przyczyna:** jedna z nazw elementów podrzędnych została użyta jako nazwa kolumny dla wartości elementu.

- **Zalecenie:** ustaw inną wartość właściwości "valueColumn".


### <a name="error-code-xmlinvalid"></a>Kod błędu: XmlInvalid

- **Komunikat:**`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Przyczyna:** Wejściowy plik XML nie jest dobrze uformowany.

- **Zalecenie:** Popraw plik XML, aby został poprawnie uformowany.


## <a name="general-copy-activity-error"></a>Błąd ogólnego działania kopiowania

### <a name="error-code-jrenotfound"></a>Kod błędu: JreNotFound

- **Komunikat:**`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Przyczyna:** Własne środowisko IR nie może odnaleźć środowiska uruchomieniowego Java. Środowisko uruchomieniowe Java jest wymagane do odczytywania określonych źródeł.

- **Zalecenie:** Sprawdź środowisko Integration Runtime. Zobacz [Use Self-hosted Integration Runtime](./format-parquet.md#using-self-hosted-integration-runtime)(Korzystanie z własnego środowiska Integration Runtime ).


### <a name="error-code-wildcardpathsinknotsupported"></a>Kod błędu: WildcardPathSinkNotSupported

- **Komunikat**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Przyczyna:** Zestaw danych ujścia nie obsługuje wartości z symbolami wieloznacznymi.

- **Zalecenie:** Sprawdź zestaw danych ujścia i ponownie zaewidencjonuj ścieżkę bez użycia wartości wieloznacznych.


### <a name="fips-issue"></a>Problem ze fipsem

- **Objawy:** działanie Kopiuj kończy się niepowodzeniem na własnym komputerze IR z włączoną obsługą programu FIPS z następującym komunikatem o błędzie: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Przyczyna:** Ten błąd może wystąpić podczas kopiowania danych za pomocą łączników, takich jak azure blob, SFTP i tak dalej. Federal Information Processing Standards (FIPS) definiuje określony zestaw algorytmów kryptograficznych, które mogą być używane. Po włączeniu trybu FIPS na maszynie niektóre klasy kryptograficzne, od których zależy działanie kopiowania, są blokowane w niektórych scenariuszach.

- **Rozwiązanie:** Dowiedz się, dlaczego nie zalecamy już ["trybu FIPS"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)i sprawdź, czy można wyłączyć tryb FIPS na własnym komputerze IR.

    Alternatywnie, jeśli chcesz tylko pozwolić na Azure Data Factory fips i upewnij się, że działania są uruchamiane pomyślnie, wykonaj następujące czynności:

    1. Otwórz folder, w którym zainstalowano własne ir ir. Ścieżka to zwykle *C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*.

    2. Otwórz *diawp.exe.config,* a następnie na końcu sekcji dodaj pozycję `<runtime>` `<enforceFIPSPolicy enabled="false"/>` , jak pokazano poniżej:

        ![Zrzut ekranu przedstawiający sekcję pliku diawp.exe.config z wyłączonym fipsem.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Zapisz plik, a następnie uruchom ponownie maszynę własnego ir ir.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej pomocy dotyczącej rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Data Factory bloga](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory dotyczące funkcji](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Strona microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)