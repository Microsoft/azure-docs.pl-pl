---
title: Korzystanie z usługi Azure Table Storage lub interfejs API tabel Azure Cosmos DB w języku Java
description: Przechowuj dane strukturalne w chmurze przy użyciu usługi Azure Table Storage lub interfejs API tabel Azure Cosmos DB w języku Java.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: a5da5e1717f897d2236fd73f0fff525e157f7a0e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093693"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Jak korzystać z usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka Java

[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

W tym artykule przedstawiono sposób tworzenia tabel, przechowywania danych i wykonywania operacji CRUD na danych. Wybierz Azure Cosmos DB Table service platformy Azure lub interfejs API tabel. Przykłady są zapisywane w języku Java i używają [zestawu SDK usługi Azure Storage V8 for Java][Azure Storage SDK for Java]. Omówiono scenariusze **tworzenia**, **wyświetlania listy** i **usuwania** tabel, a także **wstawiania** jednostek w tabeli i **wykonywania na nich zapytań** oraz **modyfikowania** i **usuwania** jednostek w tabeli. Aby uzyskać więcej informacji na temat tabel, zobacz sekcję [Następne kroki](#next-steps).

> [!IMPORTANT]
> Ostatnia wersja zestawu SDK usługi Azure Storage, Table Storage jest [V8][Azure Storage SDK for Java]. Nowa wersja zestawu Table Storage SDK dla języka Java będzie dostępna wkrótce.

> [!NOTE]
> Jest dostępny zestaw SDK dla deweloperów korzystających z usługi Azure Storage na urządzeniach z systemem Android. Aby uzyskać więcej informacji, zobacz [zestaw SDK usługi Azure Storage dla systemu Android][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Tworzenie konta usługi Azure Storage**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Tworzenie konta usługi Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Tworzenie aplikacji Java

Podczas pracy z tym przewodnikiem będziesz używać funkcji magazynu, które można uruchamiać lokalnie w aplikacji Java lub w kodzie działającym w ramach roli internetowej lub roli procesu roboczego na platformie Azure.

Aby skorzystać z przykładów przedstawionych w tym artykule, zainstaluj zestaw Java Development Kit (JDK), a następnie utwórz konto usługi Azure Storage lub Azure Cosmos DB w ramach swojej subskrypcji platformy Azure. Po wykonaniu tych czynności sprawdź, czy Twój system programistyczny spełnia wymagania minimalne i zależności wymienione w repozytorium [Azure Storage SDK for Java][Azure Storage SDK for Java] w witrynie GitHub. Jeśli Twój system spełnia te wymagania, możesz pobrać biblioteki usługi Azure Storage dla języka Java z tego repozytorium i zainstalować je w systemie zgodnie z instrukcjami. Po wykonaniu tych zadań możesz utworzyć aplikację Java z użyciem przykładów zawartych w tym artykule.

## <a name="configure-your-application-to-access-table-storage"></a>Konfigurowanie aplikacji na potrzeby dostępu do usługi Table Storage

Na początku pliku Java, w którym chcesz używać interfejsów API usługi Azure Storage lub interfejsu API tabel usługi Azure Cosmos DB w celu uzyskania dostępu do tabel, dodaj następujące instrukcje import:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-your-connection-string"></a>Dodaj parametry połączenia

Możesz połączyć się z kontem usługi Azure Storage lub kontem Azure Cosmos DB interfejs API tabel. Pobierz parametry połączenia na podstawie typu konta, którego używasz.

### <a name="add-an-azure-storage-connection-string"></a>Dodawanie parametrów połączenia usługi Azure Storage

W kliencie usługi Azure Storage punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia magazynu. W aplikacji klienckiej należy podać parametry połączenia magazynu we wskazanym poniżej formacie, używając nazwy konta magazynu i podstawowego klucza dostępu do konta magazynu widocznego w witrynie [Azure Portal](https://portal.azure.com) jako wartości **AccountName** i **AccountKey**. 

W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Dodawanie parametrów połączenia interfejsu API tabel usługi Azure Cosmos DB

Na koncie usługi Azure Cosmos DB punkt końcowy tabeli i Twoje poświadczenia są przechowywane w parametrach połączenia. W aplikacji klienckiej należy podać parametry połączenia usługi Azure Cosmos DB we wskazanym poniżej formacie, używając nazwy konta usługi Azure Cosmos DB i podstawowego klucza dostępu do tego konta widocznego w witrynie [Azure Portal](https://portal.azure.com) jako wartości **AccountName** i **AccountKey**. 

W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia usługi Azure Cosmos DB:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

W aplikacji działającej w ramach roli na platformie Azure można przechowywać te parametry w pliku konfiguracji usługi *ServiceConfiguration.cscfg* i uzyskiwać do nich dostęp za pomocą wywołania metody **RoleEnvironment.getConfigurationSettings**. Oto przykład pobierania parametrów połączenia z **ustawienia** o nazwie *StorageConnectionString* w pliku konfiguracji usługi:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

Można również przechowywać parametry połączenia w pliku config.properties w projekcie:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych metod.

## <a name="create-a-table"></a>Tworzenie tabeli

`CloudTableClient`Obiekt umożliwia uzyskanie obiektów referencyjnych dla tabel i jednostek. Poniższy kod tworzy `CloudTableClient` obiekt i używa go do utworzenia nowego `CloudTable` obiektu, który reprezentuje tabelę o nazwie "osoby". 

> [!NOTE]
> Istnieją inne sposoby tworzenia `CloudStorageAccount` obiektów. Aby uzyskać więcej informacji, zobacz w temacie Informacje o `CloudStorageAccount` [zestawie SDK klienta usługi Azure Storage].
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>Wyświetlanie listy tabel

Aby uzyskać listę tabel, wywołaj metodę **CloudTableClient.listTables()**, aby pobrać iterowaną listę nazw tabel.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Jednostki mapują do obiektów Java przy użyciu klasy niestandardowej implementującej `TableEntity` . Dla wygody `TableServiceEntity` Klasa implementuje `TableEntity` i używa odbicia do właściwości mapy do metody pobierającej i ustawiającej o nazwie dla właściwości. Aby dodać jednostkę do tabeli, należy najpierw utworzyć klasę, która definiuje właściwości jednostki. Poniższy kod definiuje klasę jednostki, która używa imienia klienta jako klucza wiersza i nazwiska klienta jako klucza partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Zapytania mogą być wykonywane szybciej w przypadku jednostek mających taki sam klucz partycji niż w przypadku jednostek z różnymi kluczami partycji.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Operacje tabeli obejmujące jednostki wymagają `TableOperation` obiektu. Ten obiekt definiuje operację do wykonania na jednostce, która może być wykonywana z `CloudTable` obiektem. Poniższy kod tworzy nowe wystąpienie `CustomerEntity` klasy z danymi klienta, które mają być przechowywane. Kod Next wywołuje `TableOperation` . insertOrReplace * *, aby utworzyć `TableOperation` obiekt, aby wstawić jednostkę do tabeli, i kojarzy nową `CustomerEntity` z nią. Na koniec kod wywołuje `execute` metodę dla `CloudTable` obiektu, określając tabelę "osoby" i nową `TableOperation` , która następnie wysyła żądanie do usługi magazynu, aby wstawić nową jednostkę klienta do tabeli "osoby", lub zastąpić jednostkę, jeśli już istnieje.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek

Możesz wstawić partię jednostek do usługi tabel w ramach jednej operacji zapisu. Poniższy kod tworzy `TableBatchOperation` obiekt, a następnie dodaje do niego trzy operacje wstawiania. Każda operacja wstawiania jest dodawana przez utworzenie nowego obiektu jednostki, ustawienie jego wartości, a następnie wywołanie `insert` metody na obiekcie w `TableBatchOperation` celu skojarzenia jednostki z nową operacją wstawiania. Następnie kod wywołuje `execute` `CloudTable` obiekt, określając tabelę "osoby" i `TableBatchOperation` obiekt, który wysyła wsadowe operacje tabeli do usługi magazynu w ramach pojedynczego żądania.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

Kilka uwag dotyczących operacji zbiorczych:

* W ramach jednej partii można wykonać maksymalnie 100 operacji wstawiania, usuwania, łączenia, zastępowania, wstawiania lub łączenia i wstawiania lub zastępowania — w dowolnej kombinacji.
* Operacja zbiorcza może zawierać operację pobierania, o ile jest to jedyna operacja w partii.
* Wszystkie jednostki w jednej operacji zbiorczej muszą mieć ten sam klucz partycji.
* Maksymalny rozmiar ładunku danych operacji zbiorczej to 4 MB.

## <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji

Aby wykonać zapytanie dotyczące tabeli dla jednostek w partycji, można użyć `TableQuery` . Wywołanie metody `TableQuery.from` tworzenia zapytania na określonej tabeli zwracającej określony typ wyniku. Poniższy kod określa filtr jednostek, gdzie „Smith” jest kluczem partycji. `TableQuery.generateFilterCondition` to metoda pomocnicza służąca do tworzenia filtrów dla zapytań. Wywołaj `where` odwołanie zwrócone przez metodę, `TableQuery.from` Aby zastosować filtr do zapytania. Gdy zapytanie jest wykonywane z wywołaniem do `execute` `CloudTable` obiektu, zwraca obiekt `Iterator` z `CustomerEntity` określonym typem wyniku. Następnie można użyć `Iterator` zwracania w pętli "foreach", aby wykorzystać wyniki. Ten kod drukuje pola każdej jednostki w wynikach zapytania w konsoli.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Pobieranie zakresu jednostek w partycji

Jeśli nie chcesz wykonywać zapytania dla wszystkich jednostek w partycji, możesz określić zakres, korzystając z operatorów porównania w filtrze. Poniższy kod łączy dwa filtry w celu pobrania wszystkich jednostek w partycji „Smith”, w których klucz wiersza (imię) rozpoczyna się od litery alfabetu wcześniejszej niż „E”. Następnie drukuje wyniki zapytania. W przypadku użycia jednostek dodanych do tabeli w sekcji tego przewodnika dotyczącej wstawiania zbiorczego zostaną zwrócone tylko dwie jednostki, Ben i Denise Smith — nie zostanie zwrócona jednostka Jeff Smith.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki

Można napisać zapytanie do pobrania jednej, określonej jednostki. Poniższy kod wywołuje `TableOperation.retrieve` Parametry klucza partycji i klucza wiersza, aby określić klienta "Jan Kowalski" zamiast tworzenia `Table Query` i używania filtrów w celu wykonania tych samych czynności. Po wykonaniu tego kodu operacja pobierania zwróci tylko jedną jednostkę, a nie zbiór jednostek. `getResultAsType`Metoda rzutuje wynik na typ obiektu docelowego przypisania, `CustomerEntity` obiekt. Jeśli ten typ nie jest zgodny z typem określonym w zapytaniu, zostanie zwrócony wyjątek. Jeśli żadna jednostka nie ma dokładnie pasującego klucza partycji i wiersza, zostanie zwrócona wartość null. Określenie kluczy partycji i wiersza w pojedynczym zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z usługi tabel.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>Modyfikowanie jednostki

Aby zmodyfikować jednostkę, pobierz ją z usługi tabel, wprowadź zmiany w obiekcie jednostki, a następnie zapisz zmiany w usłudze tabel przy użyciu operacji zastępowania lub łączenia. Poniższy kod zmienia istniejący numer telefonu klienta. Zamiast wywołania metody **TableOperation.insert**, jak w przypadku wstawiania, jest w nim wywoływana metoda **TableOperation.replace**. Metoda **CloudTable.execute** wywołuje usługę tabel i zastępuje jednostkę, o ile jednostka nie została zmieniona przez inną aplikację po pobraniu jej przez tę aplikację. W takim przypadku zostanie zwrócony wyjątek i będzie konieczne ponowne pobranie, zmodyfikowanie i zapisanie jednostki. Taki wzorzec ponawiania z optymistyczną współbieżnością jest typowy w rozproszonych systemach przechowywania danych.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki

Za pomocą zapytania wykonywanego względem tabeli można pobrać tylko kilka właściwości z jednostki. Ta technika, zwana projekcją, redukuje przepustowość i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Zapytanie w poniższym kodzie używa `select` metody, aby zwrócić tylko adresy e-mail jednostek z tabeli. Wyniki są rzutowane do kolekcji `String` z pomocą `Entity Resolver` , która wykonuje konwersję typu jednostek zwracanych z serwera. Więcej informacji na temat projekcji można znaleźć w temacie [tabele platformy Azure: wprowadzenie upsert i projekcji zapytań] [tabele platformy Azure: wprowadzenie upsert i projekcji zapytań]. Projekcja nie jest obsługiwana w lokalnym emulatorze magazynu, więc ten kod jest uruchamiany tylko w przypadku korzystania z konta w usłudze Table Service.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define an Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>Wstawianie lub zastępowanie jednostki

Często zdarza się, że chcesz dodać jednostkę do tabeli, ale nie wiesz, czy taka jednostka już istnieje. Operacja wstawiania/zamieniania umożliwia wykonywanie pojedynczego żądania, które spowoduje wstawienie jednostki, jeśli nie istnieje, lub zastąpienie istniejącej, jeśli tak się robi. Poniższy kod, oparty na poprzednich przykładach, wstawia lub zastępuje jednostkę „Walter Harp”. Po utworzeniu nowej jednostki kod wywołuje metodę **TableOperation.insertOrReplace**. Ten kod następnie wywołuje **wykonywanie** w obiekcie **tabeli chmury** z tabelą oraz operacją Wstaw lub Zamień tabelę jako parametry. Aby zaktualizować tylko część jednostki, można użyć zamiast tego metody **TableOperation.insertOrMerge**. Wstawianie lub zamienianie nie jest obsługiwane w lokalnym emulatorze magazynu, więc ten kod jest uruchamiany tylko w przypadku korzystania z konta w usłudze Table Service. Więcej informacji na temat wstawiania lub zastępowania oraz wstawiania i scalania w tym [tabelach platformy Azure: wprowadzenie upsert i projekcji zapytań] [tabele platformy Azure: wprowadzenie upsert i projekcji zapytań].

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>Usuwanie jednostki

Można łatwo usunąć pobraną jednostkę. Po pobraniu jednostki Połącz `TableOperation.delete` się z jednostką do usunięcia. Następnie Wywołaj `execute` `CloudTable` obiekt. Poniższy kod umożliwia pobranie i usunięcie jednostki klienta.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>Usuwanie tabeli

Poniższy kod usuwa tabelę z konta magazynu. Około 40 sekund po usunięciu tabeli nie można utworzyć jej ponownie. 

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Table Service w języku Java](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Zestaw SDK usługi Azure Storage dla języka Java][Azure Storage SDK for Java]
* [Dokumentacja zestawu SDK klienta usługi Azure Storage][Azure Storage Client SDK Reference]
* [Usługa Azure Storage — interfejs API REST][Azure Storage REST API]
* [Blog zespołu usługi Azure Storage][Azure Storage Team Blog]

Aby uzyskać więcej informacji, odwiedź stronę [Azure dla deweloperów języka Java](/java/azure).

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/Azure/azure-storage-java/tree/v8.6.5
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Dokumentacja zestawu SDK klienta usługi Azure Storage]: https://azure.github.io/azure-storage-java/
[Azure Storage REST API]: /rest/api/storageservices/
[Azure Storage Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/