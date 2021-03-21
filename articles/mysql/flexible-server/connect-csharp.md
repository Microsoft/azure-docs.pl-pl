---
title: 'Szybki Start: Nawiązywanie połączenia przy użyciu języka C#-Azure Database for MySQL elastyczny serwer'
description: Ten przewodnik Szybki Start zawiera przykładowy kod języka C# (.NET), którego można używać do nawiązywania połączeń i wysyłania zapytań dotyczących danych z Azure Database for MySQL elastyczny serwer.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 01/16/2021
ms.openlocfilehash: c13b8392a766dc29d8c83f9986d0b43271caabbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98606147"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Szybki Start: używanie platformy .NET (C#) do nawiązywania połączeń i wykonywania zapytań dotyczących danych na serwerze elastycznym Azure Database for MySQL

Ten przewodnik Szybki start przedstawia sposób nawiązywania połączeń z usługą Azure Database for MySQL przy użyciu aplikacji języka C#. Pokazano w nim, jak używać instrukcji języka SQL w celu wysyłania zapytań o dane oraz wstawiania, aktualizowania i usuwania danych w bazie danych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku Szybki Start są potrzebne następujące aplikacje:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free).
- Utwórz Azure Database for MySQL elastyczny serwer przy użyciu [Azure Portal](./quickstart-create-server-portal.md) <br/> lub [interfejs wiersza polecenia platformy Azure](./quickstart-create-server-cli.md) , jeśli go nie masz.
- W zależności od tego, czy używasz dostępu publicznego, czy prywatnego, wykonaj **jedną** z poniższych czynności, aby włączyć łączność.
- [Tworzenie bazy danych i użytkownika niebędącego administratorem](../howto-create-users.md)

[Masz problemy? Daj nam znać](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="create-a-c-project"></a>Tworzenie projektu języka C#
W wierszu polecenia uruchom następujące polecenie:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Pobieranie informacji o połączeniu
Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z usługą Azure Database for MySQL. Potrzebna jest w pełni kwalifikowana nazwa serwera i poświadczenia logowania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Z menu po lewej stronie w obszarze Azure Portal wybierz pozycję **wszystkie zasoby**, a następnie wyszukaj utworzony serwer (na przykład **mydemoserver**).
3. Wybierz nazwę serwera.
4. Po przejściu do panelu **Przegląd** serwera zanotuj **nazwę serwera** i **nazwę logowania administratora serwera**. Jeśli zapomnisz hasła, możesz również je zresetować z poziomu tego panelu.
 :::image type="content" source="./media/connect-csharp/server-overview-name-login.png" alt-text="Nazwa serwera usługi Azure Database for MySQL":::

## <a name="step-1-connect-and-insert-data"></a>Krok 1. Łączenie i wstawianie danych
Użyj następującego kodu, aby nawiązać połączenie i załadować dane za pomocą instrukcji języka SQL `CREATE TABLE` i `INSERT INTO`. Kod używa metod `MySqlConnection` klasy:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) w celu nawiązania połączenia z bazą danych MySQL.
- [GetCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand), ustawia Właściwość CommandText
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) , aby uruchomić polecenia bazy danych.

Zastąp parametry `Server`, `Database`, `UserID` i `Password` wartościami określonymi podczas tworzenia serwera i bazy danych.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Masz jakieś problemy? Daj nam znać.](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-2-read-data"></a>Krok 2. odczyt danych

Przy użyciu następującego kodu nawiąż połączenie i odczytaj dane za pomocą instrukcji `SELECT` języka SQL. Kod używa `MySqlConnection` klasy z metodami:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) w celu nawiązania połączenia z bazą danych MySQL.
- [GetCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) , aby ustawić Właściwość CommandText.
- [ExecuteReaderAsync ()](/dotnet/api/system.data.common.dbcommand.executereaderasync) , aby uruchomić polecenia bazy danych.
- [ReadAsync ()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) , aby przejść do rekordów w wynikach. Następnie kod używa metod GetInt32() i GetString() w celu przeanalizowania wartości w rekordzie.


Zastąp parametry `Server`, `Database`, `UserID` i `Password` wartościami określonymi podczas tworzenia serwera i bazy danych.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Masz problemy? Daj nam znać](https://github.com/MicrosoftDocs/azure-docs/issues)

## <a name="step-3-update-data"></a>Krok 3. aktualizowanie danych
Przy użyciu następującego kodu nawiąż połączenie i odczytaj dane za pomocą instrukcji `UPDATE` języka SQL. Kod używa `MySqlConnection` klasy z metodą:
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) w celu nawiązania połączenia z bazą danych MySQL.
- [GetCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) , aby ustawić Właściwość CommandText
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) , aby uruchomić polecenia bazy danych.


Zastąp parametry `Server`, `Database`, `UserID` i `Password` wartościami określonymi podczas tworzenia serwera i bazy danych.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```


## <a name="step-4-delete-data"></a>Krok 4. Usuwanie danych
Przy użyciu następującego kodu nawiąż połączenie i usuń dane za pomocą instrukcji `DELETE` języka SQL.

Kod używa `MySqlConnection` klasy z metodą
- [OpenAsync ()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) w celu nawiązania połączenia z bazą danych MySQL.
- [GetCommand ()](/dotnet/api/system.data.common.dbconnection.createcommand) , aby ustawić Właściwość CommandText.
- [ExecuteNonQueryAsync ()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) , aby uruchomić polecenia bazy danych.


Zastąp parametry `Server`, `Database`, `UserID` i `Password` wartościami określonymi podczas tworzenia serwera i bazy danych.

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
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
> [Zarządzanie serwerem Azure Database for MySQL przy użyciu portalu](./how-to-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Zarządzanie serwerem Azure Database for MySQL przy użyciu interfejsu wiersza polecenia](./how-to-manage-server-cli.md)


