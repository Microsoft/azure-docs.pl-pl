---
title: 'Szybki Start: łączenie z Rust-Azure Database for PostgreSQL — pojedynczy serwer'
description: Ten przewodnik Szybki Start udostępnia przykłady kodu Rust, których można używać do nawiązywania połączeń i wysyłania zapytań dotyczących danych z jednego serwera Azure Database for PostgreSQL.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505854"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Szybki Start: używanie Rust do nawiązywania połączeń i wykonywania zapytań dotyczących danych na pojedynczym serwerze Azure Database for PostgreSQL

W tym artykule dowiesz się, jak używać [sterownika PostgreSQL dla Rust](https://github.com/sfackler/rust-postgres) do korzystania z Azure Database for PostgreSQL przez Eksplorowanie operacji CRUD (tworzenie, odczytywanie, aktualizowanie, usuwanie) wdrożonych w przykładowym kodzie. Na koniec możesz uruchomić aplikację lokalnie, aby zobaczyć ją w działaniu.

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku Szybki Start są potrzebne następujące aplikacje:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free).
- Zainstalowano najnowszą wersję programu [Rust](https://www.rust-lang.org/tools/install) .
- Azure Database for PostgreSQL pojedynczy serwer — Utwórz jeden przy użyciu [Azure Portal](./quickstart-create-server-database-portal.md) <br/> lub [interfejs wiersza polecenia platformy Azure](./quickstart-create-server-database-azure-cli.md).
- W zależności od tego, czy używasz dostępu publicznego, czy prywatnego, wykonaj **jedną** z poniższych czynności, aby włączyć łączność.

  |Akcja| Metoda łączności|Przewodnik z instrukcjami|
  |:--------- |:--------- |:--------- |
  | **Konfigurowanie reguł zapory** | Publiczne | [Portal](./howto-manage-firewall-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-firewall-using-cli.md)|
  | **Konfigurowanie punktu końcowego usługi** | Publiczne | [Portal](./howto-manage-vnet-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-vnet-using-cli.md)|
  | **Konfigurowanie linku prywatnego** | Prywatny | [Portal](./howto-configure-privatelink-portal.md) <br/> [Interfejs wiersza polecenia](./howto-configure-privatelink-cli.md) |

- Zainstalowano usługę [git](https://git-scm.com/downloads) .

## <a name="get-database-connection-information"></a>Pobierz informacje o połączeniu z bazą danych
Połączenie z bazą danych Azure Database for PostgreSQL wymaga w pełni kwalifikowanej nazwy serwera i poświadczeń logowania. Te informacje można uzyskać z Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz nazwę serwera Azure Database for PostgreSQL.
1. Na stronie **Przegląd** serwera Skopiuj w pełni kwalifikowaną **nazwę serwera** i nazwa **użytkownika administratora**. W pełni kwalifikowana **Nazwa serwera** ma zawsze postać *\<my-server-name> . Postgres.Database.Azure.com*, a nazwa **użytkownika administratora** jest zawsze w postaci *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Przejrzyj kod (opcjonalnie)

Jeśli chcesz się dowiedzieć, jak działa kod, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz dowolnie przeskoczyć do [uruchomienia aplikacji](#run-the-application).

### <a name="connect"></a>Nawiązywanie połączenia

`main`Funkcja zaczyna się od nawiązywania połączenia z Azure Database for PostgreSQL i zależy od następujących zmiennych środowiskowych informacji o `POSTGRES_HOST` łączności `POSTGRES_USER` , `POSTGRES_PASSWORD` i `POSTGRES_DBNAME` . Domyślnie usługa bazy danych PostgreSQL jest skonfigurowana tak, aby wymagane było `TLS` połączenie. Można wyłączyć wymaganie, `TLS` Jeśli aplikacja kliencka nie obsługuje `TLS` łączności. Aby uzyskać szczegółowe informacje, zapoznaj się z tematem [Konfigurowanie łączności TLS w Azure Database for PostgreSQL-pojedynczym serwerze](./concepts-ssl-connection-security.md).

Przykładowa aplikacja w tym artykule używa protokołu TLS z [Postgres-OpenSSL](https://crates.io/crates/postgres-openssl/). [Postgres:: Client:: Connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) służy do inicjowania połączenia, a program kończy działanie w przypadku niepowodzenia.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Upuszczanie i tworzenie tabeli

Aplikacja Przykładowa używa prostej `inventory` tabeli do zademonstrowania operacji CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie).

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

`drop_create_table`Funkcja najpierw próbuje wykonać `DROP` `inventory` tabelę przed utworzeniem nowej. Ułatwia to uczenie się i eksperymentowanie, ponieważ zawsze zaczyna się od znanego (czystego) stanu. Metoda [Execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) służy do tworzenia i upuszczania operacji. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Wstawianie danych

`insert_data` dodaje wpisy do `inventory` tabeli. Tworzy [przygotowaną instrukcję](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) z funkcją [Prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) . 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Należy również zwrócić uwagę na użycie metody [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) , która umożliwia jawne określenie typów parametrów zapytania.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Na koniec `for` Pętla służy do dodawania `item-3` , `item-4` i, `item-5` z losowo wygenerowaną ilością dla każdej z nich.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Zapytania o dane

`query_data` Funkcja pokazuje, jak pobrać dane z `inventory` tabeli. Metoda [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) służy do pobierania elementu przez jego `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Wszystkie wiersze w tabeli spisu są pobierane przy użyciu `select * from` zapytania z metodą [zapytania](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) . Zwracane wiersze są powtarzane w celu wyodrębnienia wartości dla każdej kolumny przy użyciu polecenia [Get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Zwróć uwagę `get` , jak można określić kolumnę według jej indeksu liczbowego w wierszu lub według nazwy kolumny.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Aktualizowanie danych

`update_date`Funkcja losowo aktualizuje ilość dla wszystkich elementów. Ponieważ `insert_data` Funkcja dodała `5` wiersze, taka sama jest uwzględniana w `for` pętli- `for n in 1..=5`

> [!TIP]
> Należy pamiętać, że zamiast tego używamy, `query` `execute` Aby wrócić do `id` i nowo wygenerowana `quantity` (przy użyciu [klauzuli zwracającej](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Usuwanie danych

Na koniec `delete` Funkcja pokazuje, jak usunąć element z `inventory` tabeli `id` . `id`Opcja jest wybierana losowo — jest to Losowa liczba całkowita między `1` do `5` ( `5` włącznie), ponieważ `insert_data` Funkcja dodała `5` wiersze, aby rozpocząć od. 

> [!TIP]
> Należy pamiętać, że zamiast tego używamy, `query` `execute` Aby uzyskać informacje na temat usuniętego elementu (za pomocą [klauzuli zwracającej](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Aby rozpocząć pracę z programem, uruchom następujące polecenie w celu sklonowania przykładowego repozytorium:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Ustaw wymagane zmienne środowiskowe na wartości skopiowane z Azure Portal:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Aby uruchomić aplikację, przejdź do katalogu, w którym został sklonowany, i wykonaj `cargo run` następujące działania:

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    Powinny pojawić się dane wyjściowe podobne do tego:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. Aby potwierdzić, możesz również nawiązać połączenie z usługą Azure Database for PostgreSQL [przy użyciu PSQL](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) i uruchamiać zapytania względem bazy danych, na przykład:

    ```sql
    select * from inventory;
    ```

[Masz problemy? Daj nam znać](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki Start, Usuń grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Zarządzanie serwerem Azure Database for PostgreSQL przy użyciu portalu](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Zarządzanie serwerem Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia](./how-to-manage-server-cli.md)<br/>

[Nie możesz znaleźć tego, czego szukasz? Daj nam znać.](https://aka.ms/postgres-doc-feedback)
