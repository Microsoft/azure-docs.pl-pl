---
title: Użyj przejdź do zapytania
description: Użyj języka Go do utworzenia programu, który nawiązuje połączenie z bazą danych Azure SQL Database i za pomocą instrukcji języka Transact-SQL wykonuje zapytania oraz modyfikuje dane.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: craigg
ms.reviewer: MightyPen
ms.date: 02/12/2019
ms.openlocfilehash: 9b85b1bfb8935b5e311bb7d9503c17261a210127
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73827077"
---
# <a name="quickstart-use-golang-to-query-an-azure-sql-database"></a>Szybki Start: używanie golang do wykonywania zapytań w bazie danych Azure SQL

W tym przewodniku Szybki start użyjesz języka programowania [Golang](https://godoc.org/github.com/denisenkom/go-mssqldb) do nawiązania połączenia z bazą danych Azure SQL Database. Następnie uruchomisz instrukcje języka Transact-SQL (T-SQL), aby wykonać zapytanie i zmodyfikować dane. [Golang](https://golang.org/) jest językiem programowania typu „open source”, który umożliwia łatwe tworzenie prostego, niezawodnego i wydajnego oprogramowania.  

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Baza danych Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  || Pojedyncza baza danych | Wystąpienie zarządzane |
  |:--- |:--- |:---|
  | Utwórz| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [Interfejs wiersza polecenia](scripts/sql-database-create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](sql-database-server-level-firewall-rule.md)| [Łączność z maszyny wirtualnej](sql-database-managed-instance-configure-vm.md)|
  |||[Łączność ze środowiska lokalnego](sql-database-managed-instance-configure-p2s.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](sql-database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. Za pomocą wystąpienia zarządzanego należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty znajdujące się w tym artykule, aby korzystały z bazy danych Wide World Importers.

- Zainstalowany język Golang i związane z nim oprogramowanie dla systemu operacyjnego:

  - **MacOS**: Zainstaluj oprogramowania Homebrew i golang. Zobacz [Krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
  - **Ubuntu**: Zainstaluj golang. Zobacz [Krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
  - **Windows**: Zainstaluj golang. Zobacz [Krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

## <a name="create-golang-project-and-dependencies"></a>Tworzenie projektu języka Golang i zależności

1. Z poziomu terminalu utwórz nowy folder projektu o nazwie **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Przejdź do folderu **SqlServerSample** i zainstaluj sterownik programu SQL Server dla języka Go.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Tworzenie danych przykładowych

1. W edytorze tekstów utwórz plik o nazwie **CreateTestData.sql** w folderze **SqlServerSample**. W pliku wklej poniższy kod T-SQL, który tworzy schemat oraz tabelę i wstawia kilka wierszy.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. Użyj polecenia `sqlcmd`, aby nawiązać połączenie z bazą danych i uruchomić nowo utworzony skrypt SQL. Zastąp odpowiednie wartości dla swojego serwera, bazy danych, nazwy użytkownika i hasła.

   ```bash
   sqlcmd -S <your_server>.database.windows.net -U <your_username> -P <your_password> -d <your_database> -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Wstawianie kodu zapytania bazy danych SQL

1. Utwórz plik o nazwie **sample.go** w folderze **SqlServerSample**.

2. Wklej ten kod w pliku. Dodaj wartości dla swojego serwera, bazy danych, nazwy użytkownika i hasła. W tym przykładzie są używane [metody kontekstowe](https://golang.org/pkg/context/) języka Golang w celu upewnienia się, że istnieje aktywne połączenie z serwerem bazy danych.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "<your_server.database.windows.net>"
   var port = 1433
   var user = "<your_username>"
   var password = "<your_password>"
   var database = "<your_database>"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := "INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location); select convert(bigint, SCOPE_IDENTITY());"

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenie.

   ```bash
   go run sample.go
   ```

2. Sprawdź dane wyjściowe.

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>Następne kroki

- [Projektowanie pierwszej bazy danych Azure SQL Database](sql-database-design-first-database.md)
- [Sterownik języka Golang dla programu Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Zgłaszanie problemów/zadawanie pytań](https://github.com/denisenkom/go-mssqldb/issues)

