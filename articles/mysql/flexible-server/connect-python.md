---
title: 'Szybki Start: Łączenie przy użyciu języka Python-Azure Database for MySQL-elastyczny serwer'
description: Ten przewodnik Szybki Start zawiera kilka przykładów kodu języka Python, których można użyć do nawiązywania połączeń i wysyłania zapytań dotyczących danych z serwera Azure Database for MySQL-elastycznym.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: ff14bb1daeef6fc54ee5d11632ad98a29db2a172
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90947378"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Szybki Start: używanie języka Python do nawiązywania połączeń i wykonywania zapytań dotyczących danych na serwerze elastycznym Azure Database for MySQL

> [!IMPORTANT] 
> Serwer elastyczny Azure Database for MySQL jest obecnie w publicznej wersji zapoznawczej.

W tym przewodniku szybki start nawiążesz połączenie z serwerem elastycznym Azure Database for MySQL przy użyciu języka Python. Następnie można używać instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

W tym artykule założono, że wiesz już, jak programować za pomocą języka Python, ale dopiero zaczynasz pracę z Azure Database for MySQL elastycznym serwerem.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Azure Database for MySQL elastyczny serwer. Aby utworzyć elastyczny serwer, zapoznaj się z tematem [tworzenie Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal](./quickstart-create-server-portal.md) lub [Utwórz Azure Database for MySQL elastyczny serwer przy użyciu interfejsu wiersza polecenia platformy Azure](./quickstart-create-server-cli.md).

## <a name="preparing-your-client-workstation"></a>Przygotowywanie stacji roboczej klienta
- Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera. Zapoznaj się z tematem [Tworzenie i zarządzanie Azure Database for MySQL elastyczną siecią wirtualną serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).
- Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)*, możesz dodać lokalny adres IP do listy reguł zapory na serwerze. Zapoznaj się z tematem [Tworzenie i zarządzanie Azure Database for MySQL elastycznych reguł zapory serwera przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-firewall-cli.md).

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

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z serwerem elastycznym Azure Database for MySQL z Azure Portal. Wymagana jest nazwa serwera, nazwa bazy danych i poświadczenia logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
   
1. Na pasku wyszukiwania portalu Wyszukaj i wybierz utworzony przez siebie Azure Database for MySQL elastyczny serwer, taki jak **mydemoserver**.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
1. Na stronie **Przegląd** serwera Zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również zresetować hasło na tej stronie.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Przykłady kodu

### <a name="run-below-mentioned-python-code-samples"></a>Uruchom poniżej wymienione przykłady kodu języka Python
Dla każdego przykładu kodu w tym artykule:

1. Utwórz nowy plik w edytorze tekstu.
1. Dodaj przykładowy kod do pliku. W kodzie Zastąp `<mydemoserver>` `<myadmin>` `<mypassword>` symbole zastępcze,, i `<mydatabase>` z wartościami dla serwera MySQL i bazy danych.
1. Zapisz plik w folderze projektu z rozszerzeniem *. PR* , takim jak *C:\pythonmysql\createtable.py* lub */home/username/pythonmysql/CreateTable.py*.
1. Aby uruchomić kod, Otwórz wiersz polecenia lub `bash` powłokę i zmień katalog na folder projektu, na przykład `cd pythonmysql` . Wpisz polecenie, a `python` po nim nazwę pliku, `python createtable.py` a następnie naciśnij klawisz ENTER. 
   
   > [!NOTE]
   > Jeśli nie można odnaleźć *python.exe* w systemie Windows, może być konieczne dodanie ścieżki języka Python do zmiennej środowiskowej PATH lub podawanie pełnej ścieżki *python.exe*, na przykład `C:\python27\python.exe createtable.py` .

### <a name="create-a-table-and-insert-data"></a>Tworzenie tabeli i wstawianie danych

Użyj poniższego kodu, aby nawiązać połączenie z serwerem i bazą danych, utworzyć tabelę i załadować dane za pomocą instrukcji **INSERT** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z elastycznym serwerem przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="read-data"></a>Odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z elastycznym serwerem przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

Kod odczytuje wiersze danych przy użyciu metody [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , utrzymuje zestaw wyników w wierszu kolekcji i używa `for` iteratora do pętli w wierszach.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="update-data"></a>Aktualizowanie danych

Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z elastycznym serwerem przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="delete-data"></a>Usuwanie danych

Użyj poniższego kodu, aby nawiązać połączenie i usunąć dane za pomocą instrukcji **DELETE** języka SQL. 

Kod importuje bibliotekę MySQL. Connector i używa funkcji [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) , aby nawiązać połączenie z elastycznym serwerem przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. Kod używa kursora w połączeniu, a Metoda [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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
- [Połączenie szyfrowane przy użyciu Transport Layer Security (TLS 1,2) w Azure Database for MySQL-elastycznym serwerze](./how-to-connect-tls-ssl.md).
- Dowiedz się więcej o [sieci w Azure Database for MySQL elastycznym serwerze](./concepts-networking.md).
- [Utwórz i zarządzaj Azure Database for MySQL elastycznymi regułami zapory serwera przy użyciu Azure Portal](./how-to-manage-firewall-portal.md).
- [Utwórz Azure Database for MySQL elastyczną sieć wirtualną serwera i zarządzaj nią przy użyciu Azure Portal](./how-to-manage-virtual-network-portal.md).
