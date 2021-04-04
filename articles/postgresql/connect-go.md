---
title: 'Szybki Start: łączenie z usługą go-Azure Database for PostgreSQL — pojedynczy serwer'
description: Ten przewodnik Szybki Start zawiera przykładowy język programowania języka go, którego można używać do nawiązywania połączeń i wykonywania zapytań dotyczących danych z Azure Database for PostgreSQL-jednego serwera.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: eb844790ac0ae97c281f6d2d7022bee559f545cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93331934"
---
# <a name="quickstart-use-go-language-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Szybki Start: używanie języka go do nawiązywania połączenia i wykonywania zapytań dotyczących danych na pojedynczym serwerze Azure Database for PostgreSQL

Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for PostgreSQL przy użyciu kodu napisanego w języku [Go](https://golang.org/) (golang). Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych. W tym artykule założono, że wiesz już, jak programować za pomocą języka Go, i dopiero zaczynasz pracę z usługą Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z tych przewodników:
- [Tworzenie bazy danych — portal](quickstart-create-server-database-portal.md)
- [Tworzenie bazy danych — interfejs wiersza polecenia platformy Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Instalowanie łączników języków Go i pq
Zainstaluj język [Go](https://golang.org/doc/install) i [sterownik Pure Go Postgres (pq)](https://github.com/lib/pq) na własnej maszynie. W zależności od używanej platformy wykonaj odpowiednie czynności:

### <a name="windows"></a>Windows
1. [Pobierz](https://golang.org/dl/) i zainstaluj środowisko języka Go dla systemu Microsoft Windows zgodnie z [instrukcjami dotyczącymi instalacji](https://golang.org/doc/install).
2. Uruchom wiersz polecenia z menu Start.
3. Utwórz folder dla projektu, np. `mkdir  %USERPROFILE%\go\src\postgresqlgo`.
4. Przejdź do folderu projektu, np. `cd %USERPROFILE%\go\src\postgresqlgo`.
5. Ustaw zmienną środowiskową GOPATH, aby wskazywała katalog kodu źródłowego. `set GOPATH=%USERPROFILE%\go`.
6. Zainstaluj [sterownik Pure Go Postgres (pq)](https://github.com/lib/pq), uruchamiając polecenie `go get github.com/lib/pq`.

   Podsumowując: zainstaluj środowisko języka Go, a następnie uruchom następujące polecenia w wierszu polecenia:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Uruchom powłokę Bash. 
2. Zainstaluj środowisko języka Go, uruchamiając polecenie `sudo apt-get install golang-go`.
3. Utwórz folder dla projektu w katalogu macierzystym, np. `mkdir -p ~/go/src/postgresqlgo/`.
4. Przejdź do tego folderu, np. `cd ~/go/src/postgresqlgo/`.
5. Ustaw zmienną środowiskową GOPATH tak, aby wskazywała prawidłowy katalog źródłowy, np. folder go w bieżącym katalogu macierzystym. W powłoce Bash uruchom polecenie `export GOPATH=~/go`, aby dodać katalog go jako zmienną GOPATH dla bieżącej sesji powłoki.
6. Zainstaluj [sterownik Pure Go Postgres (pq)](https://github.com/lib/pq), uruchamiając polecenie `go get github.com/lib/pq`.

   Podsumowując: uruchom te polecenia programu Bash:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Pobierz i zainstaluj język Go zgodnie z [instrukcjami dotyczącymi instalacji](https://golang.org/doc/install) odpowiedniej platformy. 
2. Uruchom powłokę Bash. 
3. Utwórz folder dla projektu w katalogu macierzystym, np. `mkdir -p ~/go/src/postgresqlgo/`.
4. Przejdź do tego folderu, np. `cd ~/go/src/postgresqlgo/`.
5. Ustaw zmienną środowiskową GOPATH tak, aby wskazywała prawidłowy katalog źródłowy, np. folder go w bieżącym katalogu macierzystym. W powłoce Bash uruchom polecenie `export GOPATH=~/go`, aby dodać katalog go jako zmienną GOPATH dla bieżącej sesji powłoki.
6. Zainstaluj [sterownik Pure Go Postgres (pq)](https://github.com/lib/pq), uruchamiając polecenie `go get github.com/lib/pq`.

   Podsumowując: zainstaluj środowisko języka Go, następnie uruchom te polecenia programu Bash:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z usługą Azure Database for PostgreSQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Wszystkie zasoby** i wyszukaj utworzony serwer, taki jak **mydemoserver**.
3. Kliknij nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 :::image type="content" source="./media/connect-go/1-connection-string.png" alt-text="Nazwa serwera usługi Azure Database for PostgreSQL":::

## <a name="build-and-run-go-code"></a>Kompilowanie i uruchamianie kodu języka Go 
1. Do pisania kodu w języku Golang można użyć edytora zwykłego tekstu, takiego jak Notatnik w systemie Microsoft Windows, [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) lub [Nano](https://www.nano-editor.org/) w systemie Ubuntu lub TextEdit w systemie macOS. Jeśli wolisz używać bogatszego interaktywnego środowiska programistycznego (IDE), wypróbuj [GoLand](https://www.jetbrains.com/go/) przez Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) przez firmę Microsoft lub [Atom](https://atom.io/).
2. Wklej kod języka Golang z poniższych sekcji do plików tekstowych i zapisz pliki w folderze projektu z rozszerzeniem pliku \*.go. Na przykład ścieżka w systemie Windows: `%USERPROFILE%\go\src\postgresqlgo\createtable.go`, ścieżka w systemie Linux: `~/go/src/postgresqlgo/createtable.go`.
3. Zlokalizuj zmienne `HOST`, `DATABASE`, `USER` i `PASSWORD` w kodzie i zastąp przykładowe wartości wybranymi samodzielnie wartościami.  
4. Uruchom wiersz polecenia lub powłokę bash. Przejdź do folderu projektu. Na przykład w systemie Windows uruchom polecenie `cd %USERPROFILE%\go\src\postgresqlgo\`. W systemie Linux: `cd ~/go/src/postgresqlgo/`. Niektóre z wymienionych środowisk IDE oferują możliwości debugowania i uruchamiania bez konieczności używania poleceń powłoki.
5. Uruchom kod, wpisując polecenie `go run createtable.go`, aby skompilować aplikację i uruchomić ją. 
6. Alternatywnie, aby skompilować kod w aplikację natywną, uruchom polecenie `go build createtable.go`, a następnie uruchom plik `createtable.exe` w celu uruchomienia aplikacji.

## <a name="connect-and-create-a-table"></a>Łączenie i tworzenie tabeli
Użyj poniższego kodu w celu nawiązania połączenia i utworzenia tabeli za pomocą instrukcji **CREATE TABLE** języka SQL, a następnie instrukcji **INSERT INTO** języka SQL, aby dodać wiersze do tabeli.

Kod importuje trzy pakiety: [pakiet sql](https://golang.org/pkg/database/sql/), [pakiet pq](https://godoc.org/github.com/lib/pq) jako sterownik do komunikacji z serwerem PostgreSQL i [pakiet fmt](https://golang.org/pkg/fmt/) dla drukowanych danych wejściowych i wyjściowych w wierszu polecenia.

Kod wywołuje metodę [sql.Open()](https://godoc.org/github.com/lib/pq#Open), aby nawiązać połączenie z bazą danych usługi Azure Database for PostgreSQL, i sprawdza połączenie przy użyciu metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Używane jest [dojście do bazy danych](https://golang.org/pkg/database/sql/#DB), które utrzymuje pulę połączeń dla serwera bazy danych. Kod wywołuje metodę [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) kilka razy, aby uruchomić kilka poleceń SQL. Za każdym razem jest wywoływana niestandardowa metoda checkError() w celu sprawdzenia, czy wystąpił błąd, i awaryjnego zakończenia pracy w przypadku wystąpienia błędu.

Zastąp parametry `HOST`, `DATABASE`, `USER` i `PASSWORD` własnymi wartościami. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Odczyt danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. 

Kod importuje trzy pakiety: [pakiet sql](https://golang.org/pkg/database/sql/), [pakiet pq](https://godoc.org/github.com/lib/pq) jako sterownik do komunikacji z serwerem PostgreSQL i [pakiet fmt](https://golang.org/pkg/fmt/) dla drukowanych danych wejściowych i wyjściowych w wierszu polecenia.

Kod wywołuje metodę [sql.Open()](https://godoc.org/github.com/lib/pq#Open), aby nawiązać połączenie z bazą danych usługi Azure Database for PostgreSQL, i sprawdza połączenie przy użyciu metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Używane jest [dojście do bazy danych](https://golang.org/pkg/database/sql/#DB), które utrzymuje pulę połączeń dla serwera bazy danych. Zapytanie dotyczące instrukcji select jest uruchamiane przez wywołanie metody [db.Query()](https://golang.org/pkg/database/sql/#DB.Query), a wiersze wynikowe są przechowywane w zmiennej typu [rows](https://golang.org/pkg/database/sql/#Rows) (wiersze). Kod odczytuje wartości danych kolumny danych w bieżącym wierszu przy użyciu metody [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) i wykonuje operacje dla wierszy w pętli, używając iteratora [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) do momentu, gdy nie ma już kolejnych wierszy. Wartości kolumn każdego wiersza są drukowane w konsoli. Za każdym razem, gdy niestandardowa metoda niestandardowa checkError () jest używana do sprawdzenia, czy wystąpił błąd, i awaryjnego do zakończenia, jeśli wystąpi błąd.

Zastąp parametry `HOST`, `DATABASE`, `USER` i `PASSWORD` własnymi wartościami. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)
    defer rows.Close()

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Aktualizowanie danych
Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL.

Kod importuje trzy pakiety: [pakiet sql](https://golang.org/pkg/database/sql/), [pakiet pq](https://godoc.org/github.com/lib/pq) jako sterownik do komunikacji z serwerem Postgres i [pakiet fmt](https://golang.org/pkg/fmt/) dla drukowanych danych wejściowych i wyjściowych w wierszu polecenia.

Kod wywołuje metodę [sql.Open()](https://godoc.org/github.com/lib/pq#Open), aby nawiązać połączenie z bazą danych usługi Azure Database for PostgreSQL, i sprawdza połączenie przy użyciu metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Używane jest [dojście do bazy danych](https://golang.org/pkg/database/sql/#DB), które utrzymuje pulę połączeń dla serwera bazy danych. Kod wywołuje metodę [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) w celu uruchomienia instrukcji SQL służącej do aktualizowania tabeli. Używana jest niestandardowa metoda checkError() w celu sprawdzenia, czy wystąpił błąd, i awaryjnego zakończenia pracy w przypadku wystąpienia błędu.

Zastąp parametry `HOST`, `DATABASE`, `USER` i `PASSWORD` własnymi wartościami. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Usuwanie danych
Użyj poniższego kodu, aby nawiązać połączenie i usunąć dane za pomocą instrukcji **DELETE** języka SQL. 

Kod importuje trzy pakiety: [pakiet sql](https://golang.org/pkg/database/sql/), [pakiet pq](https://godoc.org/github.com/lib/pq) jako sterownik do komunikacji z serwerem Postgres i [pakiet fmt](https://golang.org/pkg/fmt/) dla drukowanych danych wejściowych i wyjściowych w wierszu polecenia.

Kod wywołuje metodę [sql.Open()](https://godoc.org/github.com/lib/pq#Open), aby nawiązać połączenie z bazą danych usługi Azure Database for PostgreSQL, i sprawdza połączenie przy użyciu metody [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Używane jest [dojście do bazy danych](https://golang.org/pkg/database/sql/#DB), które utrzymuje pulę połączeń dla serwera bazy danych. Kod wywołuje metodę [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) w celu uruchomienia instrukcji SQL służącej do usuwania wiersza z tabeli. Używana jest niestandardowa metoda checkError() w celu sprawdzenia, czy wystąpił błąd, i awaryjnego zakończenia pracy w przypadku wystąpienia błędu.

Zastąp parametry `HOST`, `DATABASE`, `USER` i `PASSWORD` własnymi wartościami. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki Start, Usuń grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
