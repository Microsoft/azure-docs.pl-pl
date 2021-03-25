---
title: 'Szybki Start: Łączenie przy użyciu języka Python-Azure Database for PostgreSQL-elastyczny serwer'
description: Ten przewodnik Szybki Start zawiera kilka przykładów kodu języka Python, których można użyć do nawiązywania połączeń i wysyłania zapytań dotyczących danych z serwera Azure Database for PostgreSQL-elastycznym.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 2bcf467eea9186cc57fb4c6c3d8964632b8fec03
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047715"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Szybki Start: używanie języka Python do nawiązywania połączeń i wykonywania zapytań dotyczących danych na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

W tym przewodniku szybki start nawiążesz połączenie z serwerem elastycznym Azure Database for PostgreSQL przy użyciu języka Python. Następnie można używać instrukcji SQL do wykonywania zapytań, wstawiania, aktualizowania i usuwania danych w bazie danych z platform Mac, Ubuntu Linux i Windows. 

W tym artykule założono, że wiesz już, jak programować za pomocą języka Python, ale dopiero zaczynasz pracę z serwerem elastycznym Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Serwer elastyczny Azure Database for PostgreSQL. Aby utworzyć elastyczny serwer, zapoznaj się z tematem [tworzenie Azure Database for PostgreSQL-elastyczny serwer przy użyciu Azure Portal](./quickstart-create-server-portal.md).
* [Python](https://www.python.org/downloads/) 2,7 lub 3.6 +.
* Najnowsza wersja Instalatora pakietu [PIP](https://pip.pypa.io/en/stable/installing/) .

## <a name="preparing-your-client-workstation"></a>Przygotowywanie stacji roboczej klienta
- Jeśli serwer elastyczny został utworzony z *dostępem prywatnym (Integracja z siecią wirtualną)*, należy połączyć się z serwerem z zasobu w ramach tej samej sieci wirtualnej co serwer. Można utworzyć maszynę wirtualną i dodać ją do sieci wirtualnej utworzonej przy użyciu elastycznego serwera. Zapoznaj się z tematem [Tworzenie sieci wirtualnej serwera Azure Database for PostgreSQL elastyczną i zarządzanie nią przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-virtual-network-cli.md).
- Jeśli został utworzony serwer elastyczny z *dostępem publicznym (dozwolone adresy IP)*, możesz dodać lokalny adres IP do listy reguł zapory na serwerze. Zapoznaj się z tematem [Tworzenie reguł zapory serwera Azure Database for PostgreSQL i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure](./how-to-manage-firewall-cli.md).

## <a name="install-the-python-libraries-for-postgresql"></a>Instalowanie bibliotek języka Python dla PostgreSQL
Moduł [psycopg2](https://pypi.python.org/pypi/psycopg2/) umożliwia łączenie się z bazą danych PostgreSQL i wykonywanie na nich zapytań oraz jest dostępny [jako pakiet dla](https://pythonwheels.com/) systemu Linux, macOS lub Windows. Zainstaluj wersję binarną modułu, w tym wszystkie zależności. Aby uzyskać więcej informacji na temat `psycopg2` instalacji i wymagań, zobacz [Instalacja](http://initd.org/psycopg/docs/install.html). 

Aby zainstalować `psycopg2` program, Otwórz terminal lub wiersz polecenia i uruchom polecenie `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Pobierz informacje o połączeniu z bazą danych
Połączenie z serwerem elastycznym Azure Database for PostgreSQL wymaga w pełni kwalifikowanej nazwy serwera i poświadczeń logowania. Te informacje można uzyskać z Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz elastyczną nazwę serwera. 
2. Na stronie **Przegląd** serwera Skopiuj w pełni kwalifikowaną **nazwę serwera** i nazwa **użytkownika administratora**. W pełni kwalifikowana **Nazwa serwera** ma zawsze postać *\<my-server-name> . Postgres.Database.Azure.com*.

   Potrzebujesz także hasła administratora. Jeśli zapomnisz, możesz zresetować ją ze strony przegląd. 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>Jak uruchomić przykłady języka Python

Dla każdego przykładu kodu w tym artykule:

1. Utwórz nowy plik w edytorze tekstu. 

1. Dodaj przykładowy kod do pliku. W kodzie Zastąp:
   - `<server-name>` i `<admin-username>` z wartościami skopiowanymi z Azure Portal.
   - `<admin-password>` przy użyciu hasła serwera.
   - `<database-name>` Nazwa bazy danych serwera, która jest elastyczna Azure Database for PostgreSQL. Domyślna baza danych o nazwie *Postgres* została utworzona automatycznie podczas tworzenia serwera. Można zmienić nazwę tej bazy danych lub utworzyć nową bazę danych za pomocą poleceń SQL. 

1. Zapisz plik w folderze projektu z rozszerzeniem *. PR* , takim jak *Postgres-INSERT.py*. W przypadku systemu Windows upewnij się, że podczas zapisywania pliku wybrano kodowanie UTF-8. 

1. Aby uruchomić plik, przejdź do folderu projektu w interfejsie wiersza polecenia, a `python` następnie wpisz nazwę pliku, na przykład `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Tworzenie tabeli i wstawianie danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych serwera Azure Database for PostgreSQL elastyczny przy użyciu funkcji [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) i ładuje dane za pomocą instrukcji **INSERT** języka SQL. Funkcja [ uroczecursor.exe](http://initd.org/psycopg/docs/cursor.html#execute) wykonuje zapytanie SQL względem bazy danych. 

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

![Dane wyjściowe wiersza polecenia](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Odczyt danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych serwera Azure Database for PostgreSQL-elastyczną i używa [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją **SELECT** języka SQL w celu odczytu danych. Ta funkcja akceptuje zapytanie i zwraca zestaw wyników, aby wykonać iterację przy użyciu funkcji [Cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
Poniższy przykład kodu nawiązuje połączenie z bazą danych serwera Azure Database for PostgreSQL-elastyczną i używa [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją SQL **Update** , aby zaktualizować dane. 

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
Poniższy przykład kodu nawiązuje połączenie z bazą danych serwera Azure Database for PostgreSQL-elastyczną i używa [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją **delete** języka SQL, aby usunąć element spisu, który został wcześniej wstawiony. 

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
> [Migrowanie bazy danych przy użyciu zrzutów i przywracania](../howto-migrate-using-dump-and-restore.md)
