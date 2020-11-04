---
title: 'Szybki Start: Nawiązywanie połączenia za pomocą języka Ruby-Azure Database for MySQL'
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu języka Ruby, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: f370794aafb4a5ac0948c219593e7028ff1b2e55
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337303"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki Start: używanie języka Ruby do łączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL

Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for MySQL przy użyciu aplikacji języka [Ruby](https://www.ruby-lang.org) i rozwiązania gem [mysql2](https://rubygems.org/gems/mysql2) na platformach Windows, Ubuntu Linux i Mac. Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych. W tym temacie założono, że wiesz już, jak opracowywać zawartość za pomocą języka Ruby, i dopiero zaczynasz pracę z usługą Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki start jako punktu wyjścia używa zasobów utworzonych w jednym z tych przewodników:
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Tworzenie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT]
> Upewnij się, że adres IP, z którego nawiązywane jest połączenie, został dodany przy użyciu [Azure Portal](./howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-ruby"></a>Instalowanie języka Ruby
Zainstaluj język Ruby, rozwiązanie Gem i bibliotekę MySQL2 na własnym komputerze.

### <a name="windows"></a>Windows
1. Pobierz i zainstaluj wersję 2.3 języka [Ruby](https://rubyinstaller.org/downloads/).
2. Uruchom nowy wiersz polecenia (cmd) z menu Start.
3. Zmień katalog na katalog języka Ruby w wersji 2.3. `cd c:\Ruby23-x64\bin`
4. Przetestuj instalację języka Ruby, uruchamiając polecenie `ruby -v`, aby sprawdzić zainstalowaną wersję.
5. Przetestuj instalację rozwiązania Gem, uruchamiając polecenie `gem -v`, aby sprawdzić zainstalowaną wersję.
6. Skompiluj moduł Mysql2 dla języka Ruby przy użyciu rozwiązania Gem, uruchamiając polecenie `gem install mysql2`.

### <a name="macos"></a>macOS
1. Zainstaluj język Ruby przy użyciu programu Homebrew, uruchamiając polecenie `brew install ruby`. Więcej opcji instalacji można znaleźć w [dokumentacji instalacji](https://www.ruby-lang.org/en/documentation/installation/#homebrew)języka Ruby.
2. Przetestuj instalację języka Ruby, uruchamiając polecenie `ruby -v`, aby sprawdzić zainstalowaną wersję.
3. Przetestuj instalację rozwiązania Gem, uruchamiając polecenie `gem -v`, aby sprawdzić zainstalowaną wersję.
4. Skompiluj moduł Mysql2 dla języka Ruby przy użyciu rozwiązania Gem, uruchamiając polecenie `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Zainstaluj język Ruby, uruchamiając polecenie `sudo apt-get install ruby-full`. Więcej opcji instalacji można znaleźć w [dokumentacji instalacji](https://www.ruby-lang.org/en/documentation/installation/)języka Ruby.
2. Przetestuj instalację języka Ruby, uruchamiając polecenie `ruby -v`, aby sprawdzić zainstalowaną wersję.
3. Zainstaluj najnowsze aktualizacje rozwiązania Gem, uruchamiając polecenie `sudo gem update --system`.
4. Przetestuj instalację rozwiązania Gem, uruchamiając polecenie `gem -v`, aby sprawdzić zainstalowaną wersję.
5. Zainstaluj narzędzia gcc, make i inne narzędzia do kompilacji, uruchamiając polecenie `sudo apt-get install build-essential`.
6. Zainstaluj biblioteki klienckie dewelopera programu MySQL, uruchamiając polecenie `sudo apt-get install libmysqlclient-dev`.
7. Skompiluj moduł mysql2 dla języka Ruby przy użyciu rozwiązania Gem, uruchamiając polecenie `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z usługą Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).
2. W menu po lewej stronie w witrynie Azure Portal kliknij pozycję **Wszystkie zasoby** i wyszukaj utworzony serwer, taki jak **mydemoserver**.
3. Kliknij nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 :::image type="content" source="./media/connect-ruby/1_server-overview-name-login.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::

## <a name="run-ruby-code"></a>Uruchamianie kodu w języku Ruby
1. Wklej kod języka Ruby z sekcji poniżej do plików tekstowych i zapisz pliki w folderze projektu z rozszerzeniem pliku rb, takim jak `C:\rubymysql\createtable.rb` lub `/home/username/rubymysql/createtable.rb`.
2. Aby uruchomić kod, uruchom wiersz polecenia lub powłokę Bash. Zmień katalog na folder projektu `cd rubymysql`
3. Następnie wpisz polecenie Ruby, a po nim nazwę pliku, taką jak `ruby createtable.rb`, aby uruchomić aplikację.
4. W systemie operacyjnym Windows, jeśli aplikacja języka Ruby nie znajduje się w zmiennej środowiskowej path, może być konieczne użycie pełnej ścieżki w celu uruchomienia aplikacji Node, takiej jak `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Łączenie i tworzenie tabeli
Użyj poniższego kodu w celu nawiązania połączenia i utworzenia tabeli za pomocą instrukcji **CREATE TABLE** języka SQL, a następnie instrukcji **INSERT INTO** języka SQL, aby dodać wiersze do tabeli.

Kod używa klasy [mysql2:: Client](https://www.rubydoc.info/gems/mysql2) w celu nawiązania połączenia z serwerem MySQL. Następnie wywołuje metodę, ```query()``` Aby uruchomić polecenia DROP, CREATE TABLE i INSERT INTO. Na koniec Wywołaj metodę, ```close()``` Aby zamknąć połączenie przed zakończeniem.

Zastąp ciągi `host`, `database`, `username` i `password` własnymi wartościami.
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Odczyt danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL.

Kod używa klasy [mysql2:: Client](https://www.rubydoc.info/gems/mysql2) do łączenia się z Azure Database for MySQL za pomocą ```new()``` metody. Następnie wywołuje metodę, ```query()``` Aby uruchomić polecenia SELECT. Następnie wywołuje metodę, ```close()``` Aby zamknąć połączenie przed zakończeniem.

Zastąp ciągi `host`, `database`, `username` i `password` własnymi wartościami.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Aktualizowanie danych
Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL.

Kod używa metody .new() klasy [mysql2::client](https://www.rubydoc.info/gems/mysql2) w celu nawiązania połączenia z usługą Azure Database for MySQL. Następnie wywołuje metodę, ```query()```  Aby uruchomić polecenia aktualizacji. Następnie wywołuje metodę, ```close()``` Aby zamknąć połączenie przed zakończeniem.

Zastąp ciągi `host`, `database`, `username` i `password` własnymi wartościami.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Usuwanie danych
Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **DELETE** języka SQL.

Kod używa klasy [mysql2:: Client](https://rubygems.org/gems/mysql2/) w celu nawiązania połączenia z serwerem MySQL, uruchom polecenie Delete, a następnie zamknij połączenie z serwerem.

Zastąp ciągi `host`, `database`, `username` i `password` własnymi wartościami.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
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
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./concepts-migrate-import-export.md) <br/>

> [!div class="nextstepaction"]
> [Dowiedz się więcej o kliencie MySQL2](https://www.rubydoc.info/gems/mysql2) <br/>

