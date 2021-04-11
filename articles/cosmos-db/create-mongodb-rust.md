---
title: Połącz aplikację Rust z interfejsem API Azure Cosmos DB dla MongoDB
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji Rust, która jest obsługiwana przez interfejs API Azure Cosmos DB dla MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 91e7bafe98b1aceaf8fe27b07029291a48a31351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555656"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Szybki Start: łączenie aplikacji Rust z interfejsem API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie. Przykład przedstawiony w tym artykule jest prostą aplikacją opartą na wierszu polecenia, która używa [sterownika Rust dla MongoDB](https://github.com/mongodb/mongo-rust-driver). Ponieważ interfejs API Azure Cosmos DB dla MongoDB jest [zgodny z protokołem telekomunikacyjnym MongoDB](./mongodb-introduction.md#wire-protocol-compatibility), możliwe jest, aby każdy sterownik klienta MongoDB mógł się z nim połączyć.

Dowiesz się, jak używać sterownika MongoDB Rust w celu współdziałania z interfejsem API Azure Cosmos DB dla MongoDB przez Eksplorowanie operacji, które są implementowane w przykładowym kodzie (tworzenie, odczytywanie, aktualizowanie, usuwanie). Na koniec możesz uruchomić aplikację lokalnie, aby zobaczyć ją w działaniu.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z parametrami połączenia `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Rust](https://www.rust-lang.org/tools/install) (wersja 1,39 lub nowsza)
- [Usługa Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Skonfiguruj Azure Cosmos DB

Aby skonfigurować konto Azure Cosmos DB, postępuj zgodnie z [instrukcjami](create-mongodb-dotnet.md)znajdującymi się tutaj. Aplikacja będzie potrzebować parametrów połączenia MongoDB, które można pobrać przy użyciu Azure Portal. Aby uzyskać szczegółowe informacje, zobacz [pobieranie parametrów połączenia MongoDB do dostosowania](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Uruchamianie aplikacji

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium.

1. Otwórz wiersz polecenia, Utwórz nowy folder o nazwie `git-samples` , a następnie Zamknij wiersz polecenia.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Kompilowanie aplikacji

Aby skompilować plik binarny:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Konfigurowanie aplikacji 

Wyeksportuj parametry połączenia, bazę danych MongoDB i nazwy kolekcji jako zmienne środowiskowe. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Ta `ssl=true` opcja jest ważna ze względu na wymagania Cosmos DB. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące parametrów połączenia](connect-mongodb-account.md#connection-string-requirements).
>

W polu `MONGODB_URL` zmienna środowiskowa Zastąp symbole zastępcze dla `<COSMOSDB_ACCOUNT_NAME>` i `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: Nazwa utworzonego konta Azure Cosmos DB
- `<COSMOSDB_PASSWORD>`: Klucz bazy danych wyodrębniony w poprzednim kroku

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Możesz wybrać preferowane wartości dla `MONGODB_DATABASE` i `MONGODB_COLLECTION` pozostawić je.

Aby uruchomić aplikację, przejdź do poprawnego folderu (w którym znajduje się plik binarny aplikacji):

```bash
cd target/release
```

Aby utworzyć `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Jeśli to się powiedzie, powinny pojawić się dane wyjściowe z MongoDB `_id` nowo utworzonego dokumentu:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Utwórz inny `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Wyświetl wszystkie `todo` elementy s

```bash
./todo list all
```

Powinny zostać wyświetlone te, które właśnie dodałeś:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Aby zaktualizować stan elementu `todo` (na przykład zmienić jego `completed` stan), użyj `todo` identyfikatora w taki sposób:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Wyświetl `todo` tylko zakończone

```bash
./todo list completed
```

Powinien zostać wyświetlony właśnie zaktualizowany

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Usuń `todo` Identyfikator using

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Wyświetl listę `todo` s do potwierdzenia

```bash
./todo list all
```

`todo`Właśnie usunięty nie powinien być obecny.

### <a name="view-data-in-data-explorer"></a>Wyświetlanie danych w Eksploratorze danych

Dane przechowywane w Azure Cosmos DB są dostępne do wyświetlania i wykonywania zapytań w Azure Portal.

Aby wyświetlać dane użytkownika utworzone w poprzednim kroku, a także pracować z nimi i wykonywać na nich zapytania, zaloguj się do witryny [Azure Portal](https://portal.azure.com) w przeglądarce sieci Web.

W górnym polu wyszukiwania wprowadź **Azure Cosmos DB**. Po otwarciu bloku konta usługi Cosmos wybierz swoje konto usługi Cosmos. Na lewym pasku nawigacyjnym wybierz pozycję **Eksplorator danych**. Rozwiń kolekcję w okienku Kolekcje. Następnie możesz wyświetlić dokumenty w kolekcji, wysłać zapytanie dotyczące danych, a nawet tworzyć i uruchamiać procedury składowane, wyzwalacze i funkcje definiowane przez użytkownika (UDF).

## <a name="review-the-code-optional"></a>Przejrzyj kod (opcjonalnie)

Jeśli chcesz się dowiedzieć, jak działa aplikacja, możesz przejrzeć fragmenty kodu w tej sekcji. Poniższe fragmenty kodu są pobierane z `src/main.rs` pliku.

`main`Funkcja jest punktem wejścia dla `todo` aplikacji. Oczekiwany adres URL połączenia dla interfejsu API Azure Cosmos DB MongoDB będzie dostarczany przez `MONGODB_URL` zmienną środowiskową. Tworzone jest nowe wystąpienie `TodoManager` , a następnie [ `match` wyrażenie](https://doc.rust-lang.org/book/ch06-02-match.html) delegowane do odpowiedniej `TodoManager` metody na podstawie operacji wybranej przez użytkownika `create` ,, `update` , `list` lub `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` jest to obiekt `struct` , który hermetyzuje [MongoDB:: Sync:: Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html). Podczas próby utworzenia wystąpienia `TodoManager` przy użyciu `new` funkcji program inicjuje połączenie z interfejsem API Azure Cosmos DB MongoDB.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

Co najważniejsze, `TodoManager` ma metody ułatwiające zarządzanie programem `todo` . Przejdźmy do nich po jednej.

`add_todo`Metoda przyjmuje `todo` Opis dostarczony przez użytkownika i tworzy wystąpienie `Todo` struktury, które wygląda jak poniżej. Struktura [elementu SERDE](https://github.com/serde-rs/serde) jest używana do mapowania (serializacji/deserializacji) BSON danych do wystąpień `Todo` struktur. Zwróć uwagę `serde` , jak atrybuty pola są używane do dostosowywania procesu serializacji/deserialzation. Na przykład `todo_id` pole w miejscu do zrobienia `struct` jest `ObjectId` i jest przechowywane w MongoDB jako `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) akceptuje [dokument](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) reprezentujący `todo` szczegóły do dodania. Należy zauważyć, że konwersja z `Todo` do a `Document` jest procesem dwuetapowym, osiąganym przy użyciu kombinacji [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) i [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Kolekcja. Find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) służy do pobierania *wszystkich* `todo` s lub filtruje je na podstawie stanu podanego przez użytkownika ( `pending` lub `completed` ). Zwróć uwagę, jak w `while` pętli każdy `Document` uzyskany w wyniku wyszukiwania jest konwertowany na `Todo` strukturę przy użyciu [BSON:: from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). Jest to przeciwieństwo tego, co zostało zrobione w `add_todo` metodzie.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

`todo`Stan można zaktualizować (od `pending` do `completed` lub odwrotnie). `todo`Jest konwertowany na [BSON:: OID:: objectid](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) , który następnie jest używany przez metodę[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) do lokalizowania dokumentu, który należy zaktualizować.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

Usuwanie elementu `todo` jest proste przy użyciu metody [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) .


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta interfejsu API usługi Azure Cosmos DB MongoDB przy użyciu Azure Cloud Shell i tworzenia i uruchamiania aplikacji wiersza polecenia Rust w celu zarządzania usługą `todo` . Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
