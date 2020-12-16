---
title: 'Szybki Start: Łączenie przy użyciu Node.js-Azure Database for MySQL'
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu Node.js, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 12/11/2020
ms.openlocfilehash: 6a9134e13e3145daea1eed81c4aa8795a0a49950
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588237"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki Start: używanie Node.js do nawiązywania połączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL

W tym przewodniku szybki start nawiążesz połączenie z Azure Database for MySQL przy użyciu Node.js. Następnie można używać instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

W tym temacie założono, że wiesz już, jak opracowywać przy użyciu Node.js, ale dopiero zaczynasz pracę z Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Serwer Azure Database for MySQL. [Utwórz serwer Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) lub [Utwórz serwer Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Upewnij się, że adres IP, z którego nawiązywane jest połączenie, został dodany przy użyciu [Azure Portal](./howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalowanie środowiska Node.js i łącznika programu MySQL

W zależności od używanej platformy postępuj zgodnie z instrukcjami w odpowiedniej sekcji, aby zainstalować [Node.js](https://nodejs.org). Użyj npm, aby zainstalować pakiet [MySQL](https://www.npmjs.com/package/mysql) wraz z jego zależnościami w folderze projektu.

### <a name="windows"></a>Windows

1. Odwiedź [stronę pobierania programu Node.js](https://nodejs.org/en/download/) i wybierz odpowiednią opcję Instalatora Windows.
2. Utwórz lokalny folder projektu, taki jak `nodejsmysql`. 
3. Otwórz wiersz polecenia, a następnie zmień katalog na folder projektu, taki jak `cd c:\nodejsmysql\`
4. Uruchom narzędzie NPM, aby zainstalować bibliotekę mysql w folderze projektu.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. Sprawdź instalację, sprawdzając tekst danych wyjściowych polecenia `npm list`. Numer wersji może się różnić po wydaniu nowych poprawek.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Uruchom następujące polecenia, aby zainstalować środowisko **Node.js** i program **npm**, czyli menedżera pakietów dla środowiska Node.js.

   ```bash
    # Using Ubuntu
    curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
    sudo apt-get install -y nodejs

    # Using Debian, as root
    curl -sL https://deb.nodesource.com/setup_14.x | bash -
    apt-get install -y nodejs
   ```

2. Uruchom następujące polecenia, aby utworzyć folder projektu `mysqlnodejs` i zainstalować pakiet mysql w tym folderze.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. Sprawdź instalację, sprawdzając tekst danych wyjściowych listy npm. Numer wersji może się różnić po wydaniu nowych poprawek.

### <a name="macos"></a>macOS

1. Node.js odwiedź [ stronę pliki do pobrania](https://nodejs.org/en/download/), a następnie wybierz macOS Instalatora.

2. Uruchom następujące polecenia, aby utworzyć folder projektu `mysqlnodejs` i zainstalować pakiet mysql w tym folderze.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. Sprawdź instalację, sprawdzając tekst danych wyjściowych polecenia `npm list`. Numer wersji może się różnić po wydaniu nowych poprawek.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z usługą Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Z menu po lewej stronie w obszarze Azure Portal wybierz pozycję **wszystkie zasoby**, a następnie wyszukaj utworzony serwer (na przykład **mydemoserver**).
3. Wybierz nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 :::image type="content" source="./media/connect-nodejs/server-name-azure-database-mysql.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::

## <a name="running-the-javascript-code-in-nodejs"></a>Uruchamianie kodu JavaScript w środowisku Node.js

1. Wklej kod JavaScript do plików tekstowych i zapisz w folderze projektu z rozszerzeniem js (na przykład C:\nodejsmysql\createtable.js lub /home/username/nodejsmysql/createtable.js).
2. Otwórz wiersz polecenia lub powłokę bash, a następnie zmień katalog na folder projektu `cd nodejsmysql` .
3. Aby uruchomić aplikację, wprowadź polecenie Node, a po nim nazwę pliku, na przykład `node createtable.js` .
4. W systemie Windows, jeśli aplikacja Node nie znajduje się w ścieżce zmiennej środowiskowej, może być konieczne użycie pełnej ścieżki do uruchomienia aplikacji Node, takiej jak `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>Nawiązywanie połączenia, tworzenie tabeli i wstawianie danych

Użyj poniższego kodu, aby nawiązać połączenie i załadować dane przy użyciu **CREATE TABLE** i  **Wstaw do** instrukcji SQL.

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Funkcja [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Funkcja [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. 

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }
});

function queryDatabase(){
    conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
        if (err) throw err; 
        console.log('Dropped inventory table if existed.');
    })
        conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Created inventory table.');
    })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
        else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
    function (err, results, fields) {
                if (err) throw err;
        console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
    conn.end(function (err) { 
    if (err) throw err;
    else  console.log('Done.') 
    });
};
```

## <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. Tablica wyników jest używana do przechowywania wyników zapytania.

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }
    });

function readData(){
    conn.query('SELECT * FROM inventory', 
        function (err, results, fields) {
            if (err) throw err;
            else console.log('Selected ' + results.length + ' row(s).');
            for (i = 0; i < results.length; i++) {
                console.log('Row: ' + JSON.stringify(results[i]));
            }
            console.log('Done.');
        })
    conn.end(
        function (err) { 
            if (err) throw err;
            else  console.log('Closing connection.') 
    });
};
```

## <a name="update-data"></a>Aktualizowanie danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **UPDATE** języka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. 

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Usuwanie danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **DELETE** języka SQL. 

Metoda [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) służy do połączenia interfejsem z serwerem programu MySQL. Metoda [connect()](https://github.com/mysqljs/mysql#establishing-connections) służy do nawiązywania połączenia z serwerem. Metoda [query()](https://github.com/mysqljs/mysql#performing-queries) służy do wykonywania zapytania SQL względem bazy danych MySQL. 

Zastąp parametry `host`, `user`, `password` i `database` wartościami określonymi podczas tworzenia serwera i bazy danych.

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
           })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki Start, Usuń grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./concepts-migrate-import-export.md)
