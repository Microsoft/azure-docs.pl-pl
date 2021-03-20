---
title: 'Szybki Start: Łączenie przy użyciu języka Python-Azure Database for MySQL'
description: Ten przewodnik Szybki start zawiera kilka przykładów kodu w języku Python, których można używać do nawiązywania połączeń z danymi usługi Azure Database for MySQL i wykonywania zapytań względem nich.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: 8aa0ea4b1e01cc7363f49d5897695c7c237b339b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535592"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Szybki Start: używanie języka Python do łączenia i wykonywania zapytań dotyczących danych w Azure Database for MySQL

W tym przewodniku szybki start nawiążesz połączenie z Azure Database for MySQL przy użyciu języka Python. Następnie można używać instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku Szybki Start są potrzebne następujące aplikacje:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free).
- Tworzenie Azure Database for MySQL pojedynczego serwera przy użyciu [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> lub [interfejs wiersza polecenia platformy Azure](./quickstart-create-mysql-server-database-using-azure-cli.md) , jeśli go nie masz.
- W zależności od tego, czy używasz dostępu publicznego, czy prywatnego, wykonaj **jedną** z poniższych czynności, aby włączyć łączność.

   |Akcja| Metoda łączności|Przewodnik z instrukcjami|
   |:--------- |:--------- |:--------- |
   | **Konfigurowanie reguł zapory** | Publiczne | [Portal](./howto-manage-firewall-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-firewall-using-cli.md)|
   | **Konfigurowanie punktu końcowego usługi** | Publiczne | [Portal](./howto-manage-vnet-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-vnet-using-cli.md)| 
   | **Konfigurowanie linku prywatnego** | Prywatny | [Portal](./howto-configure-privatelink-portal.md) <br/> [Interfejs wiersza polecenia](./howto-configure-privatelink-cli.md) | 

- [Tworzenie bazy danych i użytkownika niebędącego administratorem](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalowanie języka Python i łącznika programu MySQL

Zainstaluj środowisko Python i łącznik programu MySQL dla języka Python na komputerze, wykonując następujące czynności: 

> [!NOTE]
> Ten przewodnik Szybki Start korzysta z programu [MySQL Connector/Podręcznik programisty](https://dev.mysql.com/doc/connector-python/en/)dla języka Python.

1. Pobierz i zainstaluj środowisko [Python 3,7 lub nowsze](https://www.python.org/downloads/) dla systemu operacyjnego. Upewnij się, że dodano Język Python do programu `PATH` , ponieważ łącznik MySQL wymaga tego programu.
   
2. Otwórz wiersz polecenia lub `bash` powłokę i sprawdź wersję języka Python, uruchamiając polecenie `python -V` z przełącznikiem Wielka litera V.
   
3. `pip`Instalator pakietu jest uwzględniony w najnowszych wersjach języka Python. Zaktualizuj `pip` do najnowszej wersji, uruchamiając program `pip install -U pip` . 
   
   Jeśli `pip` program nie jest zainstalowany, można go pobrać i zainstalować za pomocą programu `get-pip.py` . Aby uzyskać więcej informacji, zobacz [Instalacja](https://pip.pypa.io/en/stable/installing/). 
   
4. Służy `pip` do instalowania łącznika programu MySQL dla języka Python i jego zależności:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Masz problemy? Daj nam znać](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z Azure Database for MySQL z Azure Portal. Wymagana jest nazwa serwera, nazwa bazy danych i poświadczenia logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
   
1. Na pasku wyszukiwania portalu Wyszukaj i wybierz utworzony przez siebie serwer Azure Database for MySQL, na przykład **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::
   
1. Na stronie **Przegląd** serwera Zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również zresetować hasło na tej stronie.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Nazwa serwera Azure Database for MySQL 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>Krok 1. Tworzenie tabeli i wstawianie danych

Użyj poniższego kodu, aby nawiązać połączenie z serwerem i bazą danych, utworzyć tabelę i załadować dane za pomocą instrukcji **INSERT** języka SQL. Kod importuje bibliotekę MySQL. Connector i używa metody:
- Funkcja [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) w celu nawiązania połączenia z Azure Database for MySQL przy użyciu [argumentów](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) w kolekcji config. 
- [cursor.exeMetoda urocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) wykonuje zapytanie SQL względem bazy danych MySQL. 
- [Cursor. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) po zakończeniu korzystania z kursora.
- Connection [. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) , aby zamknąć połączenie.

> [!IMPORTANT]
> - Protokół SSL jest domyślnie włączony. Może być konieczne pobranie [certyfikatu protokołu SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) w celu nawiązania połączenia ze środowiskiem lokalnym.
> - Zastąp `<mydemoserver>` `<myadmin>` `<mypassword>` `<mydatabase>` symbole zastępcze,, i wartościami dla serwera MySQL i bazy danych.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [ClientFlag.SSL],
  'ssl_cert': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
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

[Masz problemy? Daj nam znać](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Krok 2. odczyt danych

Użyj poniższego kodu, aby nawiązać połączenie i odczytać dane za pomocą instrukcji **SELECT** języka SQL. Kod importuje bibliotekę MySQL. Connector i używa [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) Metoda wykonuje zapytanie SQL względem bazy danych MySQL. 

Kod odczytuje wiersze danych przy użyciu metody [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , utrzymuje zestaw wyników w wierszu kolekcji i używa `for` iteratora do pętli w wierszach.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Krok 3. aktualizowanie danych

Użyj poniższego kodu, aby nawiązać połączenie i zaktualizować dane za pomocą instrukcji **UPDATE** języka SQL. Kod importuje bibliotekę MySQL. Connector i używa [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) Metoda wykonuje zapytanie SQL względem bazy danych MySQL. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Krok 4. Usuwanie danych

Użyj poniższego kodu, aby nawiązać połączenie i usunąć dane za pomocą instrukcji **DELETE** języka SQL. Kod importuje bibliotekę MySQL. Connector i używa [cursor.exeurocze ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) Metoda wykonuje zapytanie SQL względem bazy danych MySQL. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
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
> [Zarządzanie serwerem Azure Database for MySQL przy użyciu portalu](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Zarządzanie serwerem Azure Database for MySQL przy użyciu interfejsu wiersza polecenia](./how-to-manage-single-server-cli.md)

[Nie możesz znaleźć tego, czego szukasz? Daj nam znać.](https://aka.ms/mysql-doc-feedback)
