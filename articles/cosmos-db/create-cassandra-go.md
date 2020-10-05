---
title: Tworzenie aplikacji języka go za pomocą Azure Cosmos DB interfejs API Cassandra przy użyciu klienta gocql
description: Ten przewodnik Szybki Start przedstawia sposób korzystania z klienta programu go w celu współdziałania z Azure Cosmos DB interfejs API Cassandra
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: ba53fb786b1d1f61535168cda2152049a12dfb99
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "86535789"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Szybki Start: Tworzenie aplikacji języka go z `gocql` klientem w celu zarządzania danymi interfejs API Cassandra Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie. W tym przewodniku szybki start rozpocznie się tworzenie konta interfejs API Cassandra Azure Cosmos DB. Następnie uruchomisz aplikację go, aby utworzyć Cassandra przestrzeń kluczy, tabelę i wykonać kilka operacji. Ta aplikacja go używa [gocql](https://github.com/gocql/gocql), który jest klientem Cassandra dla języka go. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) bez subskrypcji platformy Azure.
- Zainstalowano na komputerze i działająca wiedza na temat [języka go.](https://golang.org/)
- Usługi [git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Aby można było utworzyć bazę danych, należy utworzyć konto Cassandra z Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Zacznij od klonowania aplikacji z usługi GitHub.

1. Otwórz wiersz polecenia i Utwórz nowy folder o nazwie `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminalu usługi Git, na przykład git bash. Użyj `cd` polecenia, aby przejść do nowego folderu i zainstalować przykładową aplikację.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz dowiedzieć się, jak kod tworzy zasoby bazy danych, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz przejść do [uruchamiania aplikacji](#run-the-application)

`GetSession`Funkcja (część `utils\utils.go` ) zwraca wartość [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) , która jest używana do wykonywania operacji klastra, takich jak INSERT, Find itp.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

Azure Cosmos DB Host Cassandra jest przesyłany do [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) funkcji, aby uzyskać [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) strukturę, która następnie jest skonfigurowana do korzystania z nazwy użytkownika, hasła, portu i odpowiedniej wersji protokołu TLS ([wymaganie zabezpieczeń protokołu HTTPS/SSL/TLS](https://docs.microsoft.com/azure/cosmos-db/database-security?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database))

`GetSession`Funkcja jest następnie wywoływana z `main` funkcji ( `main.go` ).

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

Informacje o łączności i poświadczenia są akceptowane w postaci zmiennych środowiskowych (rozwiązanych w `init` metodzie).

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Jest on następnie używany do wykonywania różnych operacji (część `operations\setup.go` ) na Azure Cosmos DB od i do `keyspace` `table` tworzenia.

Jak sugeruje nazwa, `DropKeySpaceIfExists` Funkcja opuszcza `keyspace` tylko, jeśli istnieje.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` funkcja służy do tworzenia `keyspace` ( `user_profile` )

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Po wykonaniu tej operacji następuje utworzenie tabeli ( `user` ), która jest traktowana jako `CreateUserTable` Funkcja

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

Po utworzeniu przestrzeni kluczy i tabeli wywoływane są operacje CRUD (część z `operations\crud.go` ). 

`InsertUser` służy do tworzenia `User` . Ustawia informacje o użytkowniku (identyfikator, nazwę i miasto) jako argumenty zapytania przy użyciu [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` służy do wyszukiwania użytkownika ( `model\user.go` ) przy użyciu określonego identyfikatora użytkownika podczas [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) wiązania atrybutów użytkownika (zwracanych przez Cassandra) do poszczególnych zmiennych ( `userid` , `name` , `city` ) — jest to tylko jeden z metod, w których można użyć wynik uzyskany jako wynik zapytania wyszukiwania

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` służy do pobierania wszystkich użytkowników. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) służy jako skrót do uzyskania wszystkich informacji o użytkowniku w postaci wycinka `map` s. Należy traktować każdą `map` rolę jako pary klucz-wartość, gdzie nazwa kolumny (na przykład `user_id` ) jest kluczem wraz z odpowiednią wartością.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Każda `map` informacja o użytkowniku jest konwertowana na `User` `mapToUser` funkcję using, która po prostu wyodrębnia wartość z odpowiedniej kolumny i używa jej do utworzenia wystąpienia `User` struktury

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Jak wspomniano wcześniej, aplikacja akceptuje łączność i poświadczenia w postaci zmiennych środowiskowych. 

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Wyświetl i skopiuj szczegóły ze strony parametrów połączenia w Azure Portal":::

Skopiuj wartości dla następujących atrybutów ( `CONTACT POINT` , `PORT` `USERNAME` i `PRIMARY PASSWORD` ) i ustaw je na odpowiednie zmienne środowiskowe

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

W oknie terminalu przejdź do odpowiedniego folderu. Na przykład:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. W terminalu uruchom następujące polecenie, aby uruchomić aplikację.

```shell
go run main.go
```

3. W oknie terminalu są wyświetlane powiadomienia dotyczące różnych operacji, w tym ustawień przestrzeni kluczy i tabeli, tworzenia użytkowników itp.

4. W witrynie Azure Portal otwórz **Eksploratora danych**, aby wykonywać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Wyświetl i skopiuj szczegóły ze strony parametrów połączenia w Azure Portal":::

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB przy użyciu interfejs API Cassandra i uruchamiania aplikacji języka go, która tworzy bazę danych i kontener Cassandra. Teraz możesz zaimportować dodatkowe dane do konta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)
