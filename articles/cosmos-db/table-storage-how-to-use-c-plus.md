---
title: Korzystanie z usługi Azure Table Storage i Azure Cosmos DB interfejs API tabel z C++
description: Przechowuj dane strukturalne w chmurze przy użyciu usługi Azure Table Storage lub interfejs API tabel Azure Cosmos DB przy użyciu języka C++.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: ed3ea64bf76eafd965e13f4dab1911840ed8139a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282854"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Jak korzystać z usługi Azure Table Storage i interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka C++

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

W tym przewodniku przedstawiono typowe scenariusze przy użyciu usługi Azure Table Storage lub interfejs API tabel Azure Cosmos DB. Przykłady są napisane w języku C++ i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). W tym artykule opisano następujące scenariusze:

* Tworzenie i usuwanie tabeli
* Współpraca z jednostkami tabel

> [!NOTE]
> Ten przewodnik jest przeznaczony do użycia z biblioteką klienta usługi Azure Storage dla języka C++ w wersji 1.0.0 lub wyższej. Zalecana wersja to 2.2.0 Biblioteka klienta magazynu, która jest dostępna za pomocą programu [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Utwórz konta

### <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++

W tym przewodniku są używane funkcje magazynu z aplikacji C++. Aby to zrobić, zainstaluj bibliotekę klienta usługi Azure Storage dla języka C++.

Aby zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, użyj następujących metod:

* System **Linux:** Postępuj zgodnie z instrukcjami podanymi w [bibliotece klienta usługi Azure Storage dla pliku Readme języka C++: wprowadzenie na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stronie systemu Linux.
* **System Windows:** W systemie Windows Użyj [vcpkg](https://github.com/microsoft/vcpkg) jako Menedżera zależności. Postępuj zgodnie z [przewodnikiem Szybki Start](https://github.com/microsoft/vcpkg#quick-start) , aby zainicjować vcpkg. Następnie użyj następującego polecenia, aby zainstalować bibliotekę:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Przewodnik dotyczący sposobu tworzenia kodu źródłowego i eksportowania go do programu NuGet można znaleźć w pliku [README](https://github.com/Azure/azure-storage-cpp#download--install) .

### <a name="configure-access-to-the-table-client-library"></a>Konfigurowanie dostępu do biblioteki klienta usługi Table Storage

Aby używać interfejsów API usługi Azure Storage do uzyskiwania dostępu do tabel, Dodaj następujące `include` instrukcje na początku pliku C++:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

W kliencie usługi Azure Storage lub Cosmos DB punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia. Po uruchomieniu aplikacji klienckiej należy podać parametry połączenia z magazynem lub Azure Cosmos DB parametry połączenia w odpowiednim formacie.

### <a name="set-up-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage

Ten przykład pokazuje, jak zadeklarować pole statyczne do przechowywania parametrów połączenia usługi Azure Storage:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Użyj nazwy konta magazynu dla usługi `<your_storage_account>` . W przypadku <your_storage_account_key> Użyj klucza dostępu dla konta magazynu wymienionego w [Azure Portal](https://portal.azure.com). Aby uzyskać informacje na temat kont magazynu i kluczy dostępu, zobacz [Tworzenie konta magazynu](../storage/common/storage-create-storage-account.md).

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Cosmos DB

Ten przykład pokazuje, jak zadeklarować pole statyczne do przechowywania parametrów połączenia Azure Cosmos DB:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Użyj nazwy konta Azure Cosmos DB `<your_cosmos_db_account>` . Wprowadź klucz podstawowy dla programu `<your_cosmos_db_account_key>` . Wprowadź punkt końcowy wymieniony w [Azure Portal](https://portal.azure.com) dla `<your_cosmos_db_endpoint>` .

Aby przetestować aplikację na lokalnym komputerze z systemem Windows, można użyć emulatora usługi Azure Storage, który jest instalowany z [zestawem Azure SDK](https://azure.microsoft.com/downloads/). Emulator magazynu to narzędzie, które symuluje usługi obiektów blob, kolejek i tabel platformy Azure dostępne na lokalnym komputerze deweloperskim. Poniższy przykład pokazuje, jak zadeklarować pole statyczne do przechowywania parametrów połączenia do lokalnego emulatora magazynu:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Aby uruchomić emulator usługi Azure Storage, z poziomu pulpitu systemu Windows wybierz przycisk **Start** lub klawisz systemu Windows. Wprowadź i uruchom *emulator magazynu Microsoft Azure*. Aby uzyskać więcej informacji, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>Pobieranie parametrów połączenia

Możesz użyć `cloud_storage_account` klasy do reprezentowania informacji o koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia magazynu, użyj `parse` metody.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie Pobierz odwołanie do `cloud_table_client` klasy. Ta klasa umożliwia pobieranie obiektów referencyjnych dla tabel i jednostek przechowywanych w usłudze Table Storage. Poniższy kod tworzy `cloud_table_client` Obiekt przy użyciu pobranego wcześniej obiektu konta magazynu:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Tworzenie i Dodawanie jednostek do tabeli

### <a name="create-a-table"></a>Tworzenie tabeli

`cloud_table_client`Obiekt umożliwia uzyskanie obiektów referencyjnych dla tabel i jednostek. Poniższy kod tworzy `cloud_table_client` obiekt i używa go do utworzenia nowej tabeli.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać jednostkę do tabeli, Utwórz nowy `table_entity` obiekt i przekaż go do `table_operation::insert_entity` . W poniższym kodzie imię klienta jest używane jako klucz wiersza, a nazwisko jako klucz partycji. Razem klucz partycji i klucz wiersza jednostki jednoznacznie identyfikują jednostkę w tabeli. Jednostki z tym samym kluczem partycji mogą być przeszukiwane szybciej niż jednostki z różnymi kluczami partycji. Użycie różnych kluczy partycji pozwala zwiększyć skalowalność operacji równoległych. Aby uzyskać więcej informacji, zobacz temat [Microsoft Azure Storage Performance and Scalability Checklist (Lista kontrolna dotycząca wydajności i skalowalności usługi Microsoft Azure Storage)](../storage/common/storage-performance-checklist.md).

Poniższy kod tworzy nowe wystąpienie `table_entity` z danymi klienta, które mają być przechowywane. Kod Next wywołuje `table_operation::insert_entity` , aby utworzyć `table_operation` obiekt, aby wstawić jednostkę do tabeli, i kojarzy z nią nową jednostkę tabeli. Na koniec kod wywołuje `execute` metodę dla `cloud_table` obiektu. Nowy `table_operation` wysyła żądanie do Table Service, aby wstawić nową jednostkę klienta do `people` tabeli.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Zbiorcze wstawianie jednostek

Możesz wstawić partię jednostek do usługi tabel w ramach jednej operacji zapisu. Poniższy kod tworzy `table_batch_operation` obiekt, a następnie dodaje do niego trzy operacje wstawiania. Każda operacja wstawiania jest dodawana przez utworzenie nowego obiektu jednostki, ustawienie jego wartości, a następnie wywołanie `insert` metody na obiekcie w `table_batch_operation` celu skojarzenia jednostki z nową operacją wstawiania. Następnie kod wywołuje, `cloud_table.execute` Aby uruchomić operację.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Kilka uwag dotyczących operacji zbiorczych:

* Można wykonać do 100 `insert` ,,, `delete` , `merge` `replace` `insert-or-merge` i `insert-or-replace` operacje w dowolnej kombinacji pojedynczej partii.  
* Operacja wsadowa może mieć operację pobierania, jeśli jest jedyną operacją w partii.  
* Wszystkie jednostki w jednej operacji zbiorczej muszą mieć ten sam klucz partycji.  
* Maksymalny rozmiar ładunku danych operacji zbiorczej to 4 MB.  

## <a name="query-and-modify-entities"></a>Zapytanie i modyfikowanie jednostek

### <a name="retrieve-all-entities-in-a-partition"></a>Pobieranie wszystkich jednostek w partycji

Aby wykonać zapytanie dotyczące tabeli dla wszystkich jednostek w partycji, użyj `table_query` obiektu. Poniższy przykład kodu określa filtr dla jednostek `Smith` , gdzie jest kluczem partycji. W tym przykładzie drukowane są pola każdej jednostki w wynikach zapytania w konsoli.  

> [!NOTE]
> Te metody języka C++ nie są obecnie obsługiwane w przypadku usługi Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

Zapytanie w tym przykładzie zwraca wszystkie jednostki, które pasują do kryteriów filtrowania. Jeśli masz duże tabele i często pobierasz jednostki z tabel, zalecamy zamiast tego przechowywanie danych w obiektach blob w usłudze Azure Storage.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Pobieranie zakresu jednostek w partycji

Jeśli nie chcesz wykonywać zapytania dla wszystkich jednostek w partycji, możesz określić zakres. Połącz filtr klucza partycji z filtrem klucza wiersza. Poniższy przykład kodu używa dwóch filtrów do pobrania wszystkich jednostek w partycji `Smith` , w których klucz wiersza (imię) rozpoczyna się od litery wcześniejszej niż `E` w alfabecie, a następnie drukuje wyniki zapytania.  

> [!NOTE]
> Te metody języka C++ nie są obecnie obsługiwane w przypadku usługi Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Pobieranie pojedynczej jednostki

Można napisać zapytanie do pobrania jednej, określonej jednostki. Poniższy kod używa `table_operation::retrieve_entity` do określenia klienta `Jeff Smith` . Ta metoda zwraca tylko jedną jednostkę, a nie kolekcję, a zwrócona wartość jest w `table_result` . Określenie kluczy partycji i wiersza w pojedynczym zapytaniu jest najszybszym sposobem na pobranie jednej jednostki z usługi tabel.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Zastępowanie jednostki

Aby zastąpić jednostkę, pobierz ją z usługi Table Storage, zmodyfikuj obiekt jednostki, a następnie zapisz zmiany w usłudze Table Storage. Poniższy kod zmienia numer telefonu i adres e-mail istniejącego klienta. Zamiast wywoływania `table_operation::insert_entity` , ten kod używa `table_operation::replace_entity` . Takie podejście powoduje, że jednostka zostanie całkowicie zastąpiona na serwerze, chyba że jednostka na serwerze zmieniła się od czasu jej pobrania. Jeśli została zmieniona, operacja zakończy się niepowodzeniem. Ten błąd uniemożliwia aplikacji zastąpienie zmiany wprowadzonej między pobieraniem i aktualizacją przez inny składnik. Właściwa obsługa tego błędu polega na ponownym pobraniu jednostki, wprowadzeniu zmian, jeśli jest nadal ważna, a następnie wykonaniu innej `table_operation::replace_entity` operacji.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Wstawianie lub zastępowanie jednostki

`table_operation::replace_entity` operacje kończą się niepowodzeniem, jeśli jednostka została zmieniona od czasu pobrania z serwera. Ponadto należy najpierw pobrać jednostkę z serwera w celu zapewnienia `table_operation::replace_entity` pomyślnego zakończenia. Czasami nie wiadomo, czy jednostka istnieje na serwerze. Bieżące przechowywane w nim wartości są nieistotne, ponieważ aktualizacja powinna zastąpić wszystkie. Aby zrealizować ten wynik, użyj `table_operation::insert_or_replace_entity` operacji. Ta operacja wstawia jednostkę, jeśli nie istnieje. Operacja zastępuje jednostkę, jeśli istnieje. W poniższym przykładzie kod jednostka klienta dla `Jeff Smith` jest wciąż pobierana, ale jest następnie zapisywana z powrotem na serwer przy użyciu `table_operation::insert_or_replace_entity` . Wszelkie aktualizacje wprowadzone w jednostce między operacjami pobrania i aktualizacji zostaną zastąpione.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki

Za pomocą zapytania wykonywanego względem tabeli można pobrać tylko kilka właściwości z jednostki. Zapytanie w poniższym kodzie używa `table_query::set_select_columns` metody, aby zwrócić tylko adresy e-mail jednostek z tabeli.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Pobieranie wybranych właściwości z jednostki za pomocą zapytania będzie bardziej wydajne niż pobieranie wszystkich właściwości.
>

## <a name="delete-content"></a>Usuń zawartość

### <a name="delete-an-entity"></a>Usuwanie jednostki

Można usunąć jednostkę po jej pobraniu. Po pobraniu jednostki Połącz `table_operation::delete_entity` się z jednostką do usunięcia. Następnie Wywołaj `cloud_table.execute` metodę. Poniższy kod pobiera i usuwa jednostkę z kluczem partycji `Smith` i kluczem wiersza `Jeff` .

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Usuwanie tabeli

Poniższy przykład kodu usuwa tabelę z konta magazynu. Usunięta tabela nie jest dostępna do ponownego utworzenia przez jakiś czas po usunięciu.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W przypadku programu Visual Studio Community Edition, jeśli projekt pobiera błędy kompilacji z powodu plików dołączanych *storage_account. h* i *Table. h*, Usuń przełącznik kompilatora **/permissive-** :

1. Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Właściwości**.
1. W oknie dialogowym **Strony właściwości** rozwiń węzeł **Właściwości konfiguracji**, rozwiń węzeł **C/C++**, a następnie wybierz pozycję **Język**.
1. Zmień ustawienie **Tryb zgodności** na **Nie**.

## <a name="next-steps"></a>Następne kroki

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.

Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat usługi Azure Storage i interfejsu API tabel usługi Azure Cosmos DB:

* [Wprowadzenie do interfejsu API tabel](table-introduction.md)
* [Generowanie listy zasobów usługi Azure Storage w języku C++](../storage/common/storage-c-plus-plus-enumeration.md)
* [Dokumentacja biblioteki klienta usługi Storage dla języka C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
