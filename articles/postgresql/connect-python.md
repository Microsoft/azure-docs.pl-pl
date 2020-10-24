---
title: 'Szybki Start: Łączenie przy użyciu języka Python-Azure Database for PostgreSQL-pojedynczego serwera'
description: Ten przewodnik Szybki Start zawiera przykłady kodu w języku Python, których można używać do nawiązywania połączeń i wysyłania zapytań dotyczących danych z jednego serwera Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 2ecf5c540c3fce7a60ebf256d871993400a731ed
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481197"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Szybki Start: używanie języka Python do nawiązywania połączeń i wykonywania zapytań dotyczących danych na pojedynczym serwerze Azure Database for PostgreSQL

W tym przewodniku szybki start pracujesz z Azure Database for PostgreSQL przy użyciu języka Python w systemie macOS, Ubuntu Linux lub Windows. Przewodnik Szybki Start przedstawia sposób nawiązywania połączeń z bazą danych i używania instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych. W artykule założono, że znasz język Python, ale nowy do pracy z Azure Database for PostgreSQL.

> [!TIP]
> Jeśli chcesz skompilować aplikację Django z PostgreSQL, Wyewidencjonuj samouczek, [Wdróż aplikację sieci Web Django za pomocą](../app-service/tutorial-python-postgresql-app.md) samouczka PostgreSQL.


## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Kończenie [przewodnika Szybki Start: Tworzenie serwera Azure Database for PostgreSQL w Azure Portal](quickstart-create-server-database-portal.md) lub [szybki start: Tworzenie Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9 + lub 3.4 +.
  
- Najnowsza wersja Instalatora pakietu [PIP](https://pip.pypa.io/en/stable/installing/) .

## <a name="install-the-python-libraries-for-postgresql"></a>Instalowanie bibliotek języka Python dla PostgreSQL
Moduł [psycopg2](https://pypi.python.org/pypi/psycopg2/) umożliwia łączenie się z bazą danych PostgreSQL i wykonywanie na nich zapytań oraz jest dostępny [jako pakiet dla](https://pythonwheels.com/) systemu Linux, macOS lub Windows. Zainstaluj wersję binarną modułu, w tym wszystkie zależności. Aby uzyskać więcej informacji na temat `psycopg2` instalacji i wymagań, zobacz [Instalacja](http://initd.org/psycopg/docs/install.html). 

Aby zainstalować `psycopg2` program, Otwórz terminal lub wiersz polecenia i uruchom polecenie `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Pobierz informacje o połączeniu z bazą danych
Połączenie z bazą danych Azure Database for PostgreSQL wymaga w pełni kwalifikowanej nazwy serwera i poświadczeń logowania. Te informacje można uzyskać z Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz nazwę serwera Azure Database for PostgreSQL. 
1. Na stronie **Przegląd** serwera Skopiuj w pełni kwalifikowaną **nazwę serwera** i nazwa **użytkownika administratora**. W pełni kwalifikowana **Nazwa serwera** ma zawsze postać * \<my-server-name> . Postgres.Database.Azure.com*, a nazwa **użytkownika administratora** jest zawsze w postaci *\<my-admin-username>@\<my-server-name>* . 
   
   Potrzebujesz także hasła administratora. Jeśli zapomnisz, możesz zresetować ją na tej stronie. 
   
   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Nazwa serwera usługi Azure Database for PostgreSQL":::

## <a name="how-to-run-the-python-examples"></a>Jak uruchomić przykłady języka Python

Dla każdego przykładu kodu w tym artykule:

1. Utwórz nowy plik w edytorze tekstu. 
   
1. Dodaj przykładowy kod do pliku. W kodzie Zastąp:
   - `<server-name>` i `<admin-username>` z wartościami skopiowanymi z Azure Portal.
   - `<admin-password>` przy użyciu hasła serwera.
   - `<database-name>` Nazwa bazy danych Azure Database for PostgreSQL. Domyślna baza danych o nazwie *Postgres* została utworzona automatycznie podczas tworzenia serwera. Można zmienić nazwę tej bazy danych lub utworzyć nową bazę danych za pomocą poleceń SQL. 
   
1. Zapisz plik w folderze projektu z rozszerzeniem *. PR* , takim jak *Postgres-INSERT.py*. W przypadku systemu Windows upewnij się, że podczas zapisywania pliku wybrano kodowanie UTF-8. 
   
1. Aby uruchomić plik, przejdź do folderu projektu w interfejsie wiersza polecenia, a `python` następnie wpisz nazwę pliku, na przykład `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Tworzenie tabeli i wstawianie danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych Azure Database for PostgreSQL przy użyciu funkcji [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) i ładuje dane za pomocą instrukcji **INSERT** języka SQL. Funkcja [ uroczecursor.exe](http://initd.org/psycopg/docs/cursor.html#execute) wykonuje zapytanie SQL względem bazy danych. 

```Python
import psycopg2

# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Po pomyślnym uruchomieniu kodu generowane są następujące dane wyjściowe:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Nazwa serwera usługi Azure Database for PostgreSQL":::

## <a name="read-data"></a>Odczyt danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych Azure Database for PostgreSQL i używa [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją **SELECT** języka SQL w celu odczytu danych. Ta funkcja akceptuje zapytanie i zwraca zestaw wyników, aby wykonać iterację przy użyciu funkcji [Cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Aktualizowanie danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych Azure Database for PostgreSQL i używa [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją SQL **Update** , aby zaktualizować dane. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Usuwanie danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych Azure Database for PostgreSQL i używa [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją **delete** SQL, aby usunąć element spisu, który został wcześniej wstawiony. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)