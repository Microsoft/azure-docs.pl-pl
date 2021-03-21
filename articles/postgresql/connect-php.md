---
title: 'Szybki Start: Łączenie przy użyciu języka PHP-Azure Database for PostgreSQL-pojedynczego serwera'
description: Ten przewodnik Szybki Start zawiera przykład kodu w języku PHP, którego można użyć do nawiązywania połączeń i wysyłania zapytań dotyczących danych z jednego serwera Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: php
ms.topic: quickstart
ms.date: 2/28/2018
ms.openlocfilehash: 200fdd126e2ed95804f81c1dd36804ecc6c61d85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019690"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Szybki Start: używanie języka PHP do nawiązywania połączeń i wykonywania zapytań dotyczących danych na pojedynczym serwerze Azure Database for PostgreSQL

Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for PostgreSQL przy użyciu aplikacji języka [PHP](https://secure.php.net/manual/intro-whatis.php). Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych. W krokach w tym artykule założono, że wiesz już, jak programować za pomocą języka PHP, i dopiero zaczynasz pracę z usługą Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne
Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z tych przewodników:
- [Tworzenie bazy danych — portal](quickstart-create-server-database-portal.md)
- [Tworzenie bazy danych — interfejs wiersza polecenia platformy Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-php"></a>Instalowanie języka PHP
Zainstaluj język PHP na własnym serwerze lub utwórz [aplikację internetową](../app-service/overview.md), która zawiera język PHP.

### <a name="windows"></a>Windows
- Pobierz [bezpieczny, niestanowiący zagrożenia język PHP w wersji 7.1.4 (x64)](https://windows.php.net/download#php-7.1).
- Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.windows.php) w celu przeprowadzenia dalszej konfiguracji.
- Kod używa klasy **pgsql** (ext/php_pgsql.dll) uwzględnionej w instalacji języka PHP. 
- Włącz rozszerzenie **pgsql**, edytując plik konfiguracji php.ini, który zazwyczaj znajduje się w folderze `C:\Program Files\PHP\v7.1\php.ini`. Plik konfiguracji powinien zawierać wiersz z tekstem `extension=php_pgsql.so`. Jeśli nie jest wyświetlany, dodaj tekst i zapisz plik. Jeśli tekst jest wyświetlany, ale zawiera komentarz z prefiksem ze średnikiem, usuń komentarz z tekstu, usuwając średnik.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Pobierz [bezpieczny, niestanowiący zagrożenia język PHP w wersji 7.1.4 (x64)](https://secure.php.net/downloads.php). 
- Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.unix.php) w celu przeprowadzenia dalszej konfiguracji.
- Kod używa klasy **pgsql** (php_pgsql.so). Zainstaluj ją, uruchamiając element `sudo apt-get install php-pgsql`.
- Włącz rozszerzenie **pgsql**, edytując plik konfiguracji `/etc/php/7.0/mods-available/pgsql.ini`. Plik konfiguracji powinien zawierać wiersz z tekstem `extension=php_pgsql.so`. Jeśli nie jest wyświetlany, dodaj tekst i zapisz plik. Jeśli tekst jest wyświetlany, ale zawiera komentarz z prefiksem ze średnikiem, usuń komentarz z tekstu, usuwając średnik.

### <a name="macos"></a>MacOS
- Pobierz [język PHP w wersji 7.1.4](https://secure.php.net/downloads.php).
- Zainstaluj język PHP i zapoznaj się z [podręcznikiem języka PHP](https://secure.php.net/manual/install.macosx.php) w celu przeprowadzenia dalszej konfiguracji.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z usługą Azure Database for PostgreSQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Wszystkie zasoby** i wyszukaj utworzony serwer, taki jak **mydemoserver**.
3. Kliknij nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 :::image type="content" source="./media/connect-php/1-connection-string.png" alt-text="Nazwa serwera usługi Azure Database for PostgreSQL":::

## <a name="connect-and-create-a-table"></a>Łączenie i tworzenie tabeli
Użyj poniższego kodu w celu nawiązania połączenia i utworzenia tabeli za pomocą instrukcji **CREATE TABLE** języka SQL, a następnie instrukcji **INSERT INTO** języka SQL, aby dodać wiersze do tabeli.

Metoda wywołująca kod [pg_connect ()](https://secure.php.net/manual/en/function.pg-connect.php) w celu nawiązania połączenia z Azure Database for PostgreSQL. Następnie kod wywołuje metodę [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) kilka razy w celu uruchomienia wielu poleceń i metodę [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) w celu sprawdzenia szczegółów, jeśli za każdym razem występuje błąd. W kolejnym kroku kod wywołuje metodę [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) w celu zamknięcia połączenia.

Zastąp parametry `$host`, `$database`, `$user` i `$password` własnymi wartościami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password") 
        or die("Failed to create connection to database: ". pg_last_error(). "<br/>");
    print "Successfully created connection to database.<br/>";

    // Drop previous table of same name if one exists.
    $query = "DROP TABLE IF EXISTS inventory;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished dropping table (if existed).<br/>";

    // Create table.
    $query = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    print "Finished creating table.<br/>";

    // Insert some data into table.
    $name = '\'banana\'';
    $quantity = 150;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'orange\'';
    $quantity = 154;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");

    $name = '\'apple\'';
    $quantity = 100;
    $query = "INSERT INTO inventory (name, quantity) VALUES ($name, $quantity);";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error()). "<br/>";

    print "Inserted 3 rows of data.<br/>";

    // Closing connection
    pg_close($connection);
?>
```

## <a name="read-data"></a>Odczyt danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. 

 Metoda wywołująca kod [pg_connect ()](https://secure.php.net/manual/en/function.pg-connect.php) w celu nawiązania połączenia z Azure Database for PostgreSQL. Następnie kod wywołuje metodę [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) w celu uruchomienia polecenia SELECT, zachowując wyniki w zestawie wyników, i metodę [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) w celu sprawdzenia błędów, jeśli wystąpił błąd.  Aby odczytać zestaw wyników, metoda [pg_fetch_row()](https://secure.php.net/manual/en/function.pg-fetch-row.php) jest wywoływana w postaci pętli, jeden raz dla każdego wiersza, a dane są pobierane w tablicy `$row`, z jedną wartością danych na kolumnę w każdej pozycji tablicy.  Aby zwolnić zestaw wyników, należy wywołać metodę [pg_free_result()](https://secure.php.net/manual/en/function.pg-free-result.php). W kolejnym kroku kod wywołuje metodę [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) w celu zamknięcia połączenia.

Zastąp parametry `$host`, `$database`, `$user` i `$password` własnymi wartościami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";
    
    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). "<br/>");

    print "Successfully created connection to database. <br/>";

    // Perform some SQL queries over the connection.
    $query = "SELECT * from inventory";
    $result_set = pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). "<br/>");
    while ($row = pg_fetch_row($result_set))
    {
        print "Data row = ($row[0], $row[1], $row[2]). <br/>";
    }

    // Free result_set
    pg_free_result($result_set);

    // Closing connection
    pg_close($connection);
?>
```

## <a name="update-data"></a>Aktualizowanie danych
Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL.

Metoda wywołująca kod [pg_connect ()](https://secure.php.net/manual/en/function.pg-connect.php) w celu nawiązania połączenia z Azure Database for PostgreSQL. Następnie kod wywołuje metodę [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) w celu uruchomienia polecenia i metodę [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) w celu sprawdzenia szczegółów, jeśli wystąpił błąd. W kolejnym kroku kod wywołuje metodę [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) w celu zamknięcia połączenia.

Zastąp parametry `$host`, `$database`, `$user` i `$password` własnymi wartościami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
                or die("Failed to create connection to database: ". pg_last_error(). ".<br/>");

    print "Successfully created connection to database. <br/>";

    // Modify some data in table.
    $new_quantity = 200;
    $name = '\'banana\'';
    $query = "UPDATE inventory SET quantity = $new_quantity WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ".<br/>");
    print "Updated 1 row of data. </br>";

    // Closing connection
    pg_close($connection);
?>
```


## <a name="delete-data"></a>Usuwanie danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **DELETE** języka SQL. 

 Kod wywołuje metodę [pg_connect()](https://secure.php.net/manual/en/function.pg-connect.php), aby nawiązać połączenie z usługą Azure for PostgreSQL. Następnie kod wywołuje metodę [pg_query()](https://secure.php.net/manual/en/function.pg-query.php) w celu uruchomienia polecenia i metodę [pg_last_error()](https://secure.php.net/manual/en/function.pg-last-error.php) w celu sprawdzenia szczegółów, jeśli wystąpił błąd. W kolejnym kroku kod wywołuje metodę [pg_close()](https://secure.php.net/manual/en/function.pg-close.php) w celu zamknięcia połączenia.

Zastąp parametry `$host`, `$database`, `$user` i `$password` własnymi wartościami. 

```php
<?php
    // Initialize connection variables.
    $host = "mydemoserver.postgres.database.azure.com";
    $database = "mypgsqldb";
    $user = "mylogin@mydemoserver";
    $password = "<server_admin_password>";

    // Initialize connection object.
    $connection = pg_connect("host=$host dbname=$database user=$user password=$password")
            or die("Failed to create connection to database: ". pg_last_error(). ". </br>");

    print "Successfully created connection to database. <br/>";

    // Delete some data from table.
    $name = '\'orange\'';
    $query = "DELETE FROM inventory WHERE name = $name;";
    pg_query($connection, $query) 
        or die("Encountered an error when executing given sql statement: ". pg_last_error(). ". <br/>");
    print "Deleted 1 row of data. <br/>";

    // Closing connection
    pg_close($connection);
?>
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
