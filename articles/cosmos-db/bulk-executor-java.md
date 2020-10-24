---
title: Użycie zbiorczej wykonawcze biblioteki środowiska Java w Azure Cosmos DB do wykonywania operacji importu zbiorczego i aktualizacji
description: Zbiorcze importowanie i aktualizowanie Azure Cosmos DB dokumentów przy użyciu biblioteki środowiska wykonawczego w języku Java
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 08/26/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: 03ef816632b73f46acaf1efdf2ddec28459fc9b8
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489340"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Wykonywanie operacji zbiorczych w usłudze Azure Cosmos DB przy użyciu biblioteki funkcji wykonawczej Java operacji zbiorczych

Ten samouczek zawiera instrukcje dotyczące korzystania z biblioteki języka Java modułu wykonawczego Azure Cosmos DB do importowania i aktualizowania Azure Cosmos DB dokumentów. Aby dowiedzieć się więcej o bibliotece narzędzia do wykonywania zbiorczego i jak można wykorzystać ogromną przepływność i magazyn, zobacz artykuł [Omówienie biblioteki wykonawców zbiorczych](bulk-executor-overview.md) . W tym samouczku utworzysz aplikację Java, która generuje losowe dokumenty i są one zbiorczo importowane do kontenera usługi Azure Cosmos. Po zaimportowaniu zbiorczo należy zaktualizować niektóre właściwości dokumentu. 

Obecnie Biblioteka wykonawców zbiorczych jest obsługiwana tylko przez Azure Cosmos DB z INTERFEJSem API SQL i kontami interfejsu API Gremlin. W tym artykule opisano sposób użycia zbiorczej procedury tworzenia biblioteki Java z kontami interfejsu API SQL. Aby dowiedzieć się więcej o używaniu zbiorczej biblioteki .NET Library z interfejsem API Gremlin, zobacz [wykonywanie operacji zbiorczych w interfejsie API usługi Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md). Opisana biblioteka wykonawczy Bulk jest dostępna tylko dla [Azure Cosmos DB Java Sync SDK V2](sql-api-sdk-java.md) i jest obecnie zalecanym rozwiązaniem dla obsługi zbiorczej Java. Nie jest ona obecnie dostępna dla 3. x, 4. x lub innych wersji zestawu SDK.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).  

* Możesz bezpłatnie [wypróbować Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można też użyć [emulatora Azure Cosmos DB](./local-emulator.md) z `https://localhost:8081` punktem końcowym. Klucz podstawowy został podany w sekcji [Uwierzytelnianie żądań](local-emulator.md#authenticate-requests).  

* [Java Development Kit (JDK) 1.7 +](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)  
  - W systemie Ubuntu uruchom polecenie `apt-get install default-jdk`, aby zainstalować zestaw JDK.  

  - Upewnij się, że zmienna środowiskowa JAVA_HOME wskazuje folder, w którym zainstalowano zestaw JDK.

* [Pobieranie](https://maven.apache.org/download.cgi) i [Instalowanie](https://maven.apache.org/install.html) archiwum binarnego [Maven](https://maven.apache.org/)  
  
  - W systemie Ubuntu możesz uruchomić polecenie `apt-get install maven`, aby zainstalować narzędzie Maven.

* Utwórz konto Azure Cosmos DB interfejsu API SQL, korzystając z procedury opisanej w sekcji [Tworzenie konta bazy danych](create-sql-api-java.md#create-a-database-account) artykułu przewodnika Szybki Start w języku Java.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem, pobierając przykładową aplikację w języku Java z usługi GitHub. Ta aplikacja wykonuje operacje zbiorcze na danych Azure Cosmos DB. Aby sklonować aplikację, Otwórz wiersz polecenia, przejdź do katalogu, w którym chcesz skopiować aplikację, a następnie uruchom następujące polecenie:

```bash
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

Sklonowane repozytorium zawiera dwa przykłady "BulkImport" i "bulkupdate" względem folderu "\azure-cosmosdb-bulkexecutor-Java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor". Aplikacja "BulkImport" generuje losowe dokumenty i importuje je do Azure Cosmos DB. Aplikacja "bulkupdate" aktualizuje niektóre dokumenty w Azure Cosmos DB. W następnych sekcjach będziemy przeglądać kod w każdej z tych przykładowych aplikacji. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Importuj zbiorczo dane do Azure Cosmos DB

1. Parametry połączenia Azure Cosmos DB są odczytywane jako argumenty i przypisywane do zmiennych zdefiniowanych w pliku CmdLineConfiguration. Java.  

2. Następnie należy zainicjować obiekt DocumentClient przy użyciu następujących instrukcji:  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. Obiekt DocumentBulkExecutor jest inicjowany z wysoką wartością ponowień dla żądań czasu oczekiwania i ograniczania. A następnie są ustawiane na 0, aby przekazać kontrolę przeciążenia do DocumentBulkExecutor przez jego okres istnienia.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Wywołaj interfejs API, który generuje losowe dokumenty do zbiorczego importowania do kontenera usługi Azure Cosmos. Konfiguracje wiersza polecenia można skonfigurować w pliku CmdLineConfiguration. Java.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   Interfejs API importowania zbiorczego akceptuje kolekcję dokumentów serializowanych w formacie JSON i ma następującą składnię, aby uzyskać więcej szczegółowych informacji, zobacz [dokumentację interfejsu API](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   Metoda nieportal akceptuje następujące parametry:
 
   |**Parametr**  |**Opis**  |
   |---------|---------|
   |isUpsert    |   Flaga umożliwiająca upsert dokumentów. Jeśli dokument o podanym IDENTYFIKATORze już istnieje, zostanie zaktualizowany.  |
   |disableAutomaticIdGeneration     |   Flaga wyłączenia automatycznej generacji identyfikatora. Domyślnie jest ustawiona wartość true.   |
   |maxConcurrencyPerPartitionRange    |  Maksymalny stopień współbieżności na zakres kluczy partycji. Wartość domyślna to 20.  |

   **Definicja obiektu odpowiedzi importu zbiorczego** Wynik wywołania interfejsu API importowania zbiorczego zawiera następujące metody Get:

   |**Parametr**  |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsImported ()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaimportowane z dokumentów dostarczonych do wywołania interfejsu API importu zbiorczego.      |
   |Podwójna getTotalRequestUnitsConsumed ()   |  Łączna liczba jednostek żądań (RU) zużytych przez wywołanie interfejsu API importu zbiorczego.       |
   |Czas trwania getTotalTimeTaken ()   |    Łączny czas trwania wywołania interfejsu API importu zbiorczego w celu ukończenia wykonywania.     |
   |Lista \<Exception> GetErrors () |  Pobiera listę błędów, jeśli niektóre dokumenty z partii dostarczone do wywołania interfejsu API importu zbiorczego nie powiodło się.       |
   |Lista \<Object> getBadInputDocuments ()  |    Lista dokumentów z nieprawidłowym formatem, które nie zostały pomyślnie zaimportowane do wywołania interfejsu API importu zbiorczego. Użytkownik powinien naprawić zwrócone dokumenty i ponowić próbę importu. Dokumenty z błędami sformatowanymi zawierają dokumenty, których identyfikator nie jest ciągiem (wartość null lub dowolny inny typ danych jest uznawany za nieprawidłowy).     |

5. Po przygotowaniu aplikacji do importowania zbiorczego Utwórz narzędzie wiersza polecenia ze źródła przy użyciu polecenia "MVN Clean Package". To polecenie generuje plik JAR w folderze docelowym:  

   ```bash
   mvn clean package
   ```

6. Po wygenerowaniu zależności docelowych można wywołać aplikację importera zbiorczego za pomocą następującego polecenia:  

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   Importer zbiorczy tworzy nową bazę danych i kolekcję z nazwą bazy danych, nazwą kolekcji i wartościami przepływności określonymi w pliku App.config. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Zbiorcze aktualizowanie danych w Azure Cosmos DB

Istniejące dokumenty można aktualizować za pomocą interfejsu API BulkUpdateAsync. W tym przykładzie ustawisz pole Nazwa na nową wartość i usuniesz pole Description z istniejących dokumentów. Pełny zestaw obsługiwanych operacji aktualizacji pól można znaleźć w [dokumentacji interfejsu API](/java/api/com.microsoft.azure.documentdb.bulkexecutor). 

1. Definiuje elementy aktualizacji wraz z odpowiednimi operacjami aktualizacji pola. W tym przykładzie użyjesz SetUpdateOperation, aby zaktualizować pole Name i UnsetUpdateOperation, aby usunąć pole Description ze wszystkich dokumentów. Można również wykonywać inne operacje, takie jak zwiększanie pola dokumentu przez określoną wartość, wypychanie określonych wartości do pola tablicy lub usuwanie określonej wartości z pola tablicy. Aby dowiedzieć się więcej na temat różnych metod udostępnianych przez interfejs API aktualizacji zbiorczej, zapoznaj się z [dokumentacją interfejsu API](/java/api/com.microsoft.azure.documentdb.bulkexecutor).  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                        
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Wywołaj interfejs API updateAll, który generuje losowe dokumenty, które mają zostać zbiorczo zaimportowane do kontenera usługi Azure Cosmos. Konfiguracje wiersza polecenia można skonfigurować do przekazywania w pliku CmdLineConfiguration. Java.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   Interfejs API aktualizacji zbiorczych akceptuje kolekcję elementów do zaktualizowania. Każdy element aktualizacji określa listę operacji aktualizacji pól, które mają być wykonywane na dokumencie identyfikowanym przez identyfikator i wartość klucza partycji. Aby uzyskać więcej informacji, zobacz [dokumentację interfejsu API](/java/api/com.microsoft.azure.documentdb.bulkexecutor):

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   Metoda updateAll akceptuje następujące parametry:

   |**Parametr** |**Opis** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  Maksymalny stopień współbieżności na zakres kluczy partycji. Wartość domyślna to 20.  |
 
   **Definicja obiektu odpowiedzi importu zbiorczego** Wynik wywołania interfejsu API importowania zbiorczego zawiera następujące metody Get:

   |**Parametr** |**Opis**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated ()  |   Całkowita liczba dokumentów, które zostały pomyślnie zaktualizowane z dokumentów dostarczonych do wywołania interfejsu API aktualizacji zbiorczej.      |
   |Podwójna getTotalRequestUnitsConsumed () |  Łączna liczba jednostek żądań (RU) zużytych przez wywołanie interfejsu API aktualizacji zbiorczej.       |
   |Czas trwania getTotalTimeTaken ()  |   Łączny czas trwania wywołania interfejsu API aktualizacji zbiorczej w celu ukończenia wykonywania.      |
   |Lista \<Exception> GetErrors ()   |       Pobiera listę problemów operacyjnych lub sieciowych związanych z operacją aktualizacji.      |
   |Lista \<BulkUpdateFailure> getFailedUpdates ()   |       Pobiera listę aktualizacji, których nie można było ukończyć wraz z określonymi wyjątkami prowadzącymi do błędów.|

3. Po przygotowaniu zbiorczej aktualizacji aplikacji Utwórz narzędzie wiersza polecenia ze źródła przy użyciu polecenia "MVN Clean Package". To polecenie generuje plik JAR w folderze docelowym:  

   ```bash
   mvn clean package
   ```

4. Po wygenerowaniu zależności docelowych można wywołać aplikację aktualizacji zbiorczej za pomocą następującego polecenia:

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>Porady dotyczące wydajności 

Należy wziąć pod uwagę następujące kwestie w celu uzyskania lepszej wydajności podczas korzystania z biblioteki wykonawców zbiorczych:

* Aby uzyskać najlepszą wydajność, uruchom aplikację z maszyny wirtualnej platformy Azure w tym samym regionie co region zapisu konta Cosmos DB.  
* W celu uzyskania większej przepływności:  

   * Ustaw rozmiar sterty JVM na wystarczająco dużą liczbę, aby uniknąć problemów z pamięcią w obsłudze dużej liczby dokumentów. Sugerowany rozmiar sterty: max (WŁĄCZONĄ, 3 * sizeof (wszystkie dokumenty przesłane do zbiorczego importu interfejsu API w jednej partii)).  
   * Istnieje czas przetwarzania wstępnego, z powodu którego uzyskano większą przepływność podczas wykonywania operacji zbiorczych przy użyciu dużej liczby dokumentów. Tak więc, jeśli chcesz zaimportować dokumenty 10 000 000, okresowe Importowanie zbiorcze 10 razy w 10 zbiorczych dokumentach, każdy z wielkości 1 000 000 jest preferowany niż uruchamianie zbiorczego importu 100 razy w witrynie 100 masowe dokumenty o rozmiarze 100 000.  

* Zaleca się utworzenie wystąpienia pojedynczego obiektu DocumentBulkExecutor dla całej aplikacji w ramach jednej maszyny wirtualnej odpowiadającej określonemu kontenerowi usługi Azure Cosmos.  

* Ponieważ wykonywanie pojedynczej operacji zbiorczej interfejsu API zużywa duży fragment procesora CPU i sieci na komputerze klienckim. Zdarza się to przez duplikowanie wielu zadań wewnętrznie, unikając duplikowania wielu współbieżnych zadań w ramach procesu aplikacji każdego wykonywania wywołań interfejsu API operacji zbiorczych. Jeśli jedno wywołanie interfejsu API operacji zbiorczej dla jednej maszyny wirtualnej nie może wykorzystać przepływności całego kontenera (jeśli przepływność kontenera > 1 000 000 RU/s), preferowane jest utworzenie oddzielnych maszyn wirtualnych w celu współbieżnego wykonywania wywołań interfejsu API operacji zbiorczej.

    
## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat szczegółów pakietu Maven i informacji o wersji biblioteki środowiska wykonawczego programu do obsługi zbiorczej, zobacz[Szczegóły zestawu SDK programu zbiorczego](sql-api-sdk-bulk-executor-java.md).