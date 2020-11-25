---
title: 'Szybki Start: Łączenie przy użyciu języka Python-Azure Database for PostgreSQL-pojedynczego serwera'
description: Ten przewodnik Szybki Start zawiera przykłady kodu w języku Python, których można używać do nawiązywania połączeń i wysyłania zapytań dotyczących danych z jednego serwera Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998944"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Szybki Start: używanie języka Python do nawiązywania połączeń i wykonywania zapytań dotyczących danych na pojedynczym serwerze Azure Database for PostgreSQL

W tym przewodniku szybki start dowiesz się, jak nawiązać połączenie z bazą danych na Azure Database for PostgreSQL pojedynczym serwerze i uruchamiać instrukcje SQL w celu wykonywania zapytań przy użyciu języka Python w systemie macOS, Ubuntu Linux lub Windows.

> [!TIP]
> Jeśli chcesz skompilować aplikację Django z PostgreSQL, Wyewidencjonuj samouczek, [Wdróż aplikację sieci Web Django za pomocą](../app-service/tutorial-python-postgresql-app.md) samouczka PostgreSQL.


## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku Szybki Start są potrzebne następujące aplikacje:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free).
- Tworzenie Azure Database for PostgreSQL pojedynczego serwera przy użyciu [Azure Portal](./quickstart-create-server-database-portal.md) <br/> lub [interfejs wiersza polecenia platformy Azure](./quickstart-create-server-database-azure-cli.md) , jeśli go nie masz.
- W zależności od tego, czy używasz dostępu publicznego, czy prywatnego, wykonaj **jedną** z poniższych czynności, aby włączyć łączność.

  |Akcja| Metoda łączności|Przewodnik z instrukcjami|
  |:--------- |:--------- |:--------- |
  | **Konfigurowanie reguł zapory** | Publiczne | [Portal](./howto-manage-firewall-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-firewall-using-cli.md)|
  | **Konfigurowanie punktu końcowego usługi** | Publiczne | [Portal](./howto-manage-vnet-using-portal.md) <br/> [Interfejs wiersza polecenia](./howto-manage-vnet-using-cli.md)|
  | **Konfigurowanie linku prywatnego** | Prywatne | [Portal](./howto-configure-privatelink-portal.md) <br/> [Interfejs wiersza polecenia](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9 + lub 3.4 +.

- Najnowsza wersja Instalatora pakietu [PIP](https://pip.pypa.io/en/stable/installing/) .
- Zainstaluj program [psycopg2](https://pypi.python.org/pypi/psycopg2/) za pomocą `pip install psycopg2` programu w terminalu lub w oknie wiersza polecenia. Aby uzyskać więcej informacji, zobacz [jak zainstalować `psycopg2` ](http://initd.org/psycopg/docs/install.html)program.

## <a name="get-database-connection-information"></a>Pobierz informacje o połączeniu z bazą danych
Połączenie z bazą danych Azure Database for PostgreSQL wymaga w pełni kwalifikowanej nazwy serwera i poświadczeń logowania. Te informacje można uzyskać z Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)Wyszukaj i wybierz nazwę serwera Azure Database for PostgreSQL.
1. Na stronie **Przegląd** serwera Skopiuj w pełni kwalifikowaną **nazwę serwera** i nazwa **użytkownika administratora**. W pełni kwalifikowana **Nazwa serwera** ma zawsze postać *\<my-server-name> . Postgres.Database.Azure.com*, a nazwa **użytkownika administratora** jest zawsze w postaci *\<my-admin-username>@\<my-server-name>* .

   Potrzebujesz także hasła administratora. Jeśli zapomnisz, możesz zresetować ją na tej stronie.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Nazwa serwera usługi Azure Database for PostgreSQL":::

> [!IMPORTANT]
>  Zastąp następujące wartości:
>   - `<server-name>` i `<admin-username>` z wartościami skopiowanymi z Azure Portal.
>   - `<admin-password>` przy użyciu hasła serwera.
>   - `<database-name>` Domyślna baza danych o nazwie *Postgres* została utworzona automatycznie podczas tworzenia serwera. Można zmienić nazwę tej bazy danych lub [utworzyć nową bazę danych](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) za pomocą poleceń SQL.

## <a name="step-1-connect-and-insert-data"></a>Krok 1. Łączenie i wstawianie danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych Azure Database for PostgreSQL przy użyciu polecenia
-  [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) funkcja i ładuje dane za pomocą instrukcji SQL **INSERT** .
- [cursor.exefunkcja urocze](http://initd.org/psycopg/docs/cursor.html#execute) wykonuje zapytanie SQL względem bazy danych.

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

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Dane wyjściowe wiersza polecenia":::


[Masz problemy? Daj nam znać](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Krok 2. odczyt danych
Poniższy przykład kodu nawiązuje połączenie z bazą danych Azure Database for PostgreSQL i używa
- [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją **SELECT** języka SQL, aby odczytywać dane.
- [Cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) akceptuje zapytanie i zwraca zestaw wyników, aby wykonać iterację przy użyciu

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Masz problemy? Daj nam znać](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Krok 3. aktualizowanie danych
Poniższy przykład kodu używa [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją SQL **Update** , aby zaktualizować dane.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Masz problemy? Daj nam znać](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Krok 5. Usuwanie danych
Poniższy przykład kodu jest uruchamiany [cursor.exeurocze](http://initd.org/psycopg/docs/cursor.html#execute) z instrukcją **delete** języka SQL, aby usunąć element spisu, który został wcześniej wstawiony.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Masz problemy? Daj nam znać](https://aka.ms/postgres-doc-feedback)

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
> [Zarządzanie serwerem Azure Database for MySQL przy użyciu interfejsu wiersza polecenia](./how-to-manage-server-cli.md)<br/>

[Nie możesz znaleźć tego, czego szukasz? Daj nam znać.](https://aka.ms/postgres-doc-feedback)
