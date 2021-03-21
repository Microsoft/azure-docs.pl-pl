---
title: Korzystanie z usługi Azure cache for Redis z usługą Rust
description: W tym przewodniku szybki start dowiesz się, jak korzystać z usługi Azure cache for Redis przy użyciu Rust.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 17f38d79b75179d7a54ca5ed1d20dff18d0a0363
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121103"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Szybki Start: korzystanie z usługi Azure cache for Redis z usługą Rust

W tym artykule dowiesz się, jak używać [języka programowania Rust](https://www.rust-lang.org/) na potrzeby współdziałania z [usługą Azure cache dla Redis](./cache-overview.md). Przedstawiono przykłady często używanych Redis struktur danych, takich jak [String](https://redis.io/topics/data-types-intro#redis-strings), [hash](https://redis.io/topics/data-types-intro#redis-hashes), [list](https://redis.io/topics/data-types-intro#redis-lists) itp. Korzystanie z biblioteki [Redis-RS](https://github.com/mitsuhiko/redis-rs) dla Redis. Ten klient udostępnia interfejsy API wysokiego i niskiego poziomu, a oba te style są wyświetlane w akcji z pomocą przykładowego kodu przedstawionego w tym artykule.

## <a name="skip-to-the-code-on-github"></a>Przejdź do kodu w usłudze GitHub

Jeśli chcesz pominąć prosty kod, zobacz [Przewodnik Szybki Start Rust](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (wersja 1,39 lub nowsza)
- [Usługa Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Tworzenie wystąpienia pamięci podręcznej Azure Cache for Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Przejrzyj kod (opcjonalnie)

Jeśli chcesz się dowiedzieć, jak działa kod, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz dowolnie przeskoczyć do [uruchomienia aplikacji](#run-the-application).

`connect`Funkcja służy do nawiązywania połączenia z usługą Azure cache for Redis. Oczekuje, że nazwa hosta i hasło (klucz dostępu) mają być przekazane za pośrednictwem zmiennych środowiskowych `REDIS_HOSTNAME` i `REDIS_PASSWORD` odpowiednio. Format adresu URL połączenia to `rediss://<username>:<password>@<hostname>` pamięć podręczna platformy Azure dla Redis. akceptowane są tylko bezpieczne połączenia z protokołem [TLS 1,2 jako minimalną wymaganą wersję](cache-remove-tls-10-11.md).

Wywołanie [Redis:: Client:: Open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) wykonuje podstawowe sprawdzanie poprawności, podczas gdy [get_connection ()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) faktycznie inicjuje połączenie — program zatrzymuje się w przypadku niepowodzenia łączności z powodu nieprawidłowego hasła.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

`basics`Funkcja obejmuje polecenia [Set](https://redis.io/commands/set), [Get](https://redis.io/commands/get)i [incr](https://redis.io/commands/incr) . Interfejs API niskiego poziomu jest używany dla `SET` i `GET` , który ustawia i pobiera wartość klucza o nazwie `foo` . `INCRBY`Polecenie jest wykonywane przy użyciu interfejsu API wysokiego poziomu, tj. [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) zwiększa wartość klucza (o nazwie `counter` ), `2` a następnie wywołanie do [pobrania](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) .

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

Poniższy fragment kodu przedstawia funkcjonalność `HASH` struktury danych Redis. [HSET](https://redis.io/commands/hset) jest wywoływany przy użyciu interfejsu API niskiego poziomu do przechowywania informacji ( `name` , `version` , `repo` ) dotyczących sterowników Redis (klientów). Na przykład szczegóły dotyczące sterownika Rust (jeden używany w tym przykładowym kodzie!) są przechwytywane w formie [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) , a następnie przesyłane do interfejsu API niskiego poziomu. Następnie jest pobierany przy użyciu [HGETALL](https://redis.io/commands/hgetall).

`HSET` można również wykonać przy użyciu interfejsu API wysokiego poziomu, korzystając z [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) , który akceptuje tablicę krotek. następnie [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) jest wykonywane w celu pobrania wartości dla pojedynczego atrybutu ( `repo` w tym przypadku).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

W funkcji poniżej można zobaczyć, jak używać `LIST` struktury danych. [LPUSH](https://redis.io/commands/lpush) jest wykonywany (z interfejsem API niskiego poziomu), aby dodać wpis do listy, a Metoda [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) wysokiego poziomu jest używana do pobierania tego elementu z listy. Następnie Metoda [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) jest używana do dodawania do listy kilku wpisów, które następnie są pobierane przy użyciu metody [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) niskiego poziomu.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

W tym miejscu można zobaczyć niektóre `SET` operacje. Metoda [Sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (High-Level API) służy do dodawania kilku wpisów do `SET` nazwy `users` . [SISMEMBER](https://redis.io/commands/hset) jest następnie wykonywany (interfejs API niskiego poziomu), aby sprawdzić, czy `user1` istnieje. Na koniec [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) jest używany do pobierania i iterowania wszystkich wpisów ustawionych w postaci wektora ([VEC <String> ](https://doc.rust-lang.org/std/vec/struct.Vec.html)).

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

`sorted_set` Funkcja poniżej demonstruje posortowaną strukturę danych zestawu. [ZADD](https://redis.io/commands/zadd) jest wywoływany (z interfejsem API niskiego poziomu) w celu dodania losowego wyniku liczby całkowitej dla odtwarzacza ( `player-1` ). Następnie Metoda [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (interfejs API wysokiego poziomu) służy do dodawania większej liczby graczy ( `player-2` do `player-5` ) i ich odpowiednich (losowo generowanych) ocen. Liczba wpisów w posortowanym zestawie jest określana przy użyciu [ZCARD](https://redis.io/commands/zcard) i jest używana jako limit polecenia [ZRANGE](https://redis.io/commands/zrange) (wywoływane z interfejsem API niskiego poziomu), aby wystawić graczom wyniki w kolejności rosnącej.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Zacznij od klonowania aplikacji z usługi GitHub.

1. Otwórz wiersz polecenia i Utwórz nowy folder o nazwie `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

1. Otwórz okno terminalu usługi Git, na przykład git bash. Użyj `cd` polecenia, aby przejść do nowego folderu, w którym zostanie sklonowana Przykładowa aplikacja.

    ```bash
    cd "C:\git-samples"
    ```

1. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aplikacja akceptuje połączenia i poświadczenia w postaci zmiennych środowiskowych. 

1. Pobierz **nazwę hosta** i **klucze dostępu** (dostępne za pośrednictwem kluczy dostępu) dla wystąpienia usługi Azure Cache for Redis w [Azure Portal](https://portal.azure.com/).

1. Ustaw je na odpowiednie zmienne środowiskowe:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. W oknie terminalu przejdź do odpowiedniego folderu. Na przykład:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. W terminalu uruchom następujące polecenie, aby uruchomić aplikację.

    ```shell
    cargo run
    ```
    
    Zostaną wyświetlone dane wyjściowe w taki sposób:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Jeśli chcesz uruchomić konkretną funkcję, Skomentuj inne funkcje w `main` funkcji:
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zakończysz pracę z grupą zasobów i zasobami platformy Azure utworzoną w tym przewodniku Szybki Start, możesz je usunąć, aby uniknąć naliczania opłat.

> [!IMPORTANT]
> Usuwanie grupy zasobów jest nieodwracalne, a grupa zasobów i wszystkie znajdujące się w niej zasoby są trwale usuwane. Jeśli utworzono wystąpienie usługi Azure cache for Redis w istniejącej grupie zasobów, która ma zostać zachowana, można usunąć tylko pamięć podręczną, wybierając pozycję **Usuń** na stronie **Przegląd** pamięci podręcznej. 

Aby usunąć grupę zasobów i jej Redis Cache dla wystąpienia platformy Azure:

1. W [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **grupy zasobów**.
1. W polu tekstowym **Filtruj według nazwy** wprowadź nazwę grupy zasobów zawierającej wystąpienie pamięci podręcznej, a następnie wybierz ją z wyników wyszukiwania. 
1. Na stronie grupy zasobów wybierz pozycję **Usuń grupę zasobów**.
1. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.
   
   ![Usuwanie grupy zasobów dla usługi Azure cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób użycia sterownika Rust dla Redis do łączenia i wykonywania operacji w usłudze Azure cache dla Redis.

> [!div class="nextstepaction"]
> [Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.](./cache-web-app-howto.md)
