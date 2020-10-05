---
title: 'Szybki Start: Łączenie przy użyciu języka Python-Azure Database for MySQL'
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu w języku Python, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: aad160f8a4ec96a1e0e627684c598062079b57e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90896311"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki Start: używanie języka Python do łączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL

W tym przewodniku szybki start nawiążesz połączenie z Azure Database for MySQL przy użyciu języka Python. Następnie można używać instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

W tym temacie założono, że wiesz już, jak opracowywać przy użyciu języka Python, ale dopiero zaczynasz pracę z Azure Database for MySQL.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Serwer Azure Database for MySQL. [Utwórz serwer Azure Database for MySQL przy użyciu Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) lub [Utwórz serwer Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Upewnij się, że adres IP, z którego nawiązywane jest połączenie, został dodany przy użyciu [Azure Portal](./howto-manage-firewall-using-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalowanie języka Python i łącznika programu MySQL

Zainstaluj środowisko Python i łącznik programu MySQL dla języka Python na komputerze, wykonując następujące czynności: 

> [!NOTE]
> W tym przewodniku szybki start do łączenia się z bazą danych MySQL jest stosowane nieprzetworzone podejście zapytania SQL. Jeśli używasz platformy sieci Web, użyj zalecanego łącznika [dla platformy](https://pypi.org/project/mysqlclient/) , na przykład Django.

1. Pobierz i zainstaluj środowisko [Python 3,7 lub nowsze](https://www.python.org/downloads/) dla systemu operacyjnego. Upewnij się, że dodano Język Python do programu `PATH` , ponieważ łącznik MySQL wymaga tego programu.
   
1. Otwórz wiersz polecenia lub `bash` powłokę i sprawdź wersję języka Python, uruchamiając polecenie `python -V` z przełącznikiem Wielka litera V.
   
1. `pip`Instalator pakietu jest uwzględniony w najnowszych wersjach języka Python. Zaktualizuj `pip` do najnowszej wersji, uruchamiając program `pip install -U pip` . 
   
   Jeśli `pip` program nie jest zainstalowany, można go pobrać i zainstalować za pomocą programu `get-pip.py` . Aby uzyskać więcej informacji, zobacz [Instalacja](https://pip.pypa.io/en/stable/installing/). 
   
1. Służy `pip` do instalowania łącznika programu MySQL dla języka Python i jego zależności:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Możesz również zainstalować łącznik języka Python dla programu MySQL z [MySQL.com](https://dev.mysql.com/downloads/connector/python/). Aby uzyskać więcej informacji na temat łącznika MySQL dla języka Python, zobacz Przewodnik po programie [MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z Azure Database for MySQL z Azure Portal. Wymagana jest nazwa serwera, nazwa bazy danych i poświadczenia logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
   
1. Na pasku wyszukiwania portalu Wyszukaj i wybierz utworzony przez siebie serwer Azure Database for MySQL, na przykład **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::
   
1. Na stronie **Przegląd** serwera Zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również zresetować hasło na tej stronie.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::

## <a name="run-the-python-examples"></a>Uruchamianie przykładów języka Python

Dla każdego przykładu kodu w tym artykule:

1. Utwórz nowy plik w edytorze tekstu.
1. Dodaj przykładowy kod do pliku. W kodzie Zastąp `<mydemoserver>` `<myadmin>` `<mypassword>` symbole zastępcze,, i `<mydatabase>` z wartościami dla serwera MySQL i bazy danych.
1. Zapisz plik w folderze projektu z rozszerzeniem *. PR* , takim jak *C:\pythonmysql\createtable.py* lub */home/username/pythonmysql/CreateTable.py*.
1. Aby uruchomić kod, Otwórz wiersz polecenia lub `bash` powłokę i zmień katalog na folder projektu, na przykład `cd pythonmysql` . Wpisz polecenie, a `python` po nim nazwę pliku, `python createtable.py` a następnie naciśnij klawisz ENTER. 
   
   > [!NOTE]
   > Jeśli nie można odnaleźć *python.exe* w systemie Windows, może być konieczne dodanie ścieżki języka Python do zmiennej środowiskowej PATH lub podawanie pełnej ścieżki *python.exe*, na przykład `C:\python27\python.exe createtable.py` .

## <a name="create-a-table-and-insert-data"></a>Tworzenie tabeli i wstawianie danych

Użyj poniższego kodu, aby nawiązać połączenie z serwerem i bazą danych, utworzyć tabelę i załadować dane za pomocą instrukcji **INSERT** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

Kod odczytuje wiersze danych przy użyciu metody [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , utrzymuje zestaw wyników w wierszu kolekcji i używa `for` iteratora do pętli w wierszach.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Aktualizowanie danych

Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Usuwanie danych

Użyj poniższego kodu, aby nawiązać połączenie i usunąć dane za pomocą instrukcji **DELETE** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./concepts-migrate-import-export.md)
