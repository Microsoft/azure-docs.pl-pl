---
title: Używanie zapytania przejdź do
description: Użyj funkcji Go, aby utworzyć program, który nawiązuje połączenie z bazą danych Azure SQL Database lub Azure SQL Managed Instance i uruchamia zapytania.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 04/14/2021
ms.openlocfilehash: 1a479572ba8dbd68ccc072fce32446abcc9b873c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517784"
---
# <a name="quickstart-use-golang-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Szybki start: używanie języka Golang do wykonywania zapytań w bazie danych w Azure SQL Database lub Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym przewodniku Szybki start użyjesz języka programowania [Golang](https://godoc.org/github.com/denisenkom/go-mssqldb) do nawiązania połączenia z bazą danych w Azure SQL Database lub Azure SQL Managed Instance. Następnie uruchomisz instrukcje języka Transact-SQL (T-SQL), aby wykonać zapytanie i zmodyfikować dane. [Golang](https://golang.org/) jest językiem programowania typu „open source”, który umożliwia łatwe tworzenie prostego, niezawodnego i wydajnego oprogramowania.  

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Baza danych w Azure SQL Database lub Azure SQL Managed Instance. Aby utworzyć bazę danych, możesz użyć jednego z tych przewodników Szybki start:

  || Baza danych SQL | Wystąpienie zarządzane SQL | Program SQL Server na maszynie wirtualnej platformy Azure |
  |:--- |:--- |:---|:---|
  | **Utwórz**| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  | **Utwórz** | [Interfejs wiersza polecenia](scripts/create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  | **Utwórz** | [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | **Ustaw opcję** | [Reguła zapory bazująca na adresach IP na poziomie serwera](firewall-create-server-level-portal-quickstart.md)| [Łączność z maszyny wirtualnej](../managed-instance/connect-vm-instance-configure.md)|
  | **Ustaw opcję** ||[Łączność ze środowisk lokalnych](../managed-instance/point-to-site-p2s-configure.md) | [Nawiązywanie połączenia z SQL Server wystąpieniem](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |**Ładowanie danych**|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Przywracanie bazy danych Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  | **Ładowanie danych** ||Przywracanie lub importowanie firmy Adventure Works z [pliku BACPAC](database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Przywracanie lub importowanie firmy Adventure Works z [pliku BACPAC](database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. W przypadku SQL Managed Instance należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty w tym artykule, aby używać bazy danych Wide World Importers.

- Zainstalowany język Golang i związane z nim oprogramowanie dla systemu operacyjnego:

  - **macOS:** zainstaluj oprogramowanie Homebrew i Golang. Zobacz [Krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
  - **Ubuntu:** zainstaluj oprogramowanie Golang. Zobacz [Krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
  - **Windows:** zainstaluj oprogramowanie Golang. Zobacz [Krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).

## <a name="get-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem

Uzyskaj informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **Bazy danych SQL** lub Wystąpienia zarządzane **SQL.**

3. Na  stronie Przegląd przejrzyj w pełni kwalifikowaną nazwę serwera obok pola Nazwa serwera dla bazy danych w usłudze Azure SQL Database lub w pełni kwalifikowaną nazwę serwera (lub adres IP) obok pola Host dla maszyny wirtualnej platformy Azure SQL Managed Instance lub SQL Server na maszynie wirtualnej platformy Azure.   Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu SQL Server na maszynie wirtualnej platformy Azure, zobacz Connect to a SQL Server instance (Nawiązywanie [połączenia SQL Server wirtualnej).](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-golang-project-and-dependencies"></a>Tworzenie projektu języka Golang i zależności

1. Z poziomu terminalu utwórz nowy folder projektu o nazwie **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Przejdź do folderu **SqlServerSample** i zainstaluj sterownik programu SQL Server dla języka Go.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
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

2. Użyj `sqlcmd` polecenia , aby nawiązać połączenie z bazą danych i uruchomić nowo Azure SQL skryptu. Zastąp odpowiednie wartości dla swojego serwera, bazy danych, nazwy użytkownika i hasła.

   ```bash
   sqlcmd -S <your_server>.database.windows.net -U <your_username> -P <your_password> -d <your_database> -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-the-database"></a>Wstawianie kodu w celu wykonywania zapytań w bazie danych

1. Utwórz plik o nazwie **sample.go** w folderze **SqlServerSample**.

2. Wklej ten kod w pliku. Dodaj wartości dla swojego serwera, bazy danych, nazwy użytkownika i hasła. W tym przykładzie użyto metod [kontekstu języka](https://golang.org/pkg/context/) Golang, aby upewnić się, że istnieje aktywne połączenie.

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

       tsql := `
         INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location);
         select isNull(SCOPE_IDENTITY(), -1);
       `

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

- [Projektowanie pierwszej bazy danych w Azure SQL Database](design-first-database-tutorial.md)
- [Sterownik Języka Golang dla SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Zgłaszanie problemów/zadawanie pytań](https://github.com/denisenkom/go-mssqldb/issues)

