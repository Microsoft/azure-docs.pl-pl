---
title: Używanie środowiska Node. js do wykonywania zapytań w bazie danych
description: Jak użyć środowiska Node. js do utworzenia programu, który nawiązuje połączenie z bazą danych w Azure SQL Database i wykonuje zapytania przy użyciu instrukcji języka T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2 
ms.openlocfilehash: 5f53d6b3e8b477d7b93eb1063679126a9533ef03
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054479"
---
# <a name="quickstart-use-nodejs-to-query-a-microsoft-azure-sql-database"></a>Szybki Start: Używanie środowiska Node. js do wykonywania zapytań w bazie danych SQL Microsoft Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym przewodniku szybki start użyjesz środowiska Node. js do nawiązywania połączenia z usługą Azure SQL Database i używasz instrukcji języka T-SQL do wykonywania zapytań dotyczących danych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || SQL Database | Wystąpienie zarządzane SQL | SQL Server na maszynie wirtualnej platformy Azure |
  |:--- |:--- |:---|:---|
  | Utwórz| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Interfejs wiersza polecenia](scripts/create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Program PowerShell](scripts/create-and-configure-database-powershell.md) | [Program PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [Program PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](firewall-create-server-level-portal-quickstart.md)| [Łączność z maszyny wirtualnej](../managed-instance/connect-vm-instance-configure.md)|
  |||[Łączność ze środowiska lokalnego](../managed-instance/point-to-site-p2s-configure.md) | [Ustanawianie połączenia z programem SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Przywracanie bazy danych Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||


- Oprogramowanie związane z [Node. js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Zainstaluj oprogramowania Homebrew i Node. js, a następnie zainstaluj sterownik ODBC i polecenie SQLCMD, wykonując kroki **1,2** i **1,3** w temacie [Tworzenie aplikacji Node. js przy użyciu SQL Server na macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Zainstaluj program Node. js, a następnie zainstaluj sterownik ODBC i Narzędzie SQLCMD, wykonując kroki **1,2** i **1,3** w temacie [Tworzenie aplikacji Node. js przy użyciu SQL Server w Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Zainstaluj czekoladę i Node. js, a następnie zainstaluj sterownik ODBC i polecenie SQLCMD, wykonując kroki **1,2** i **1,3** w temacie [Tworzenie aplikacji Node. js przy użyciu SQL Server w systemie Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Skrypty w tym artykule są przeznaczone do korzystania z bazy danych firmy **Adventure Works** .

> [!NOTE]
> Opcjonalnie możesz wybrać użycie wystąpienia zarządzanego Azure SQL.
>
> Aby utworzyć i skonfigurować, użyj [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)lub [interfejsu wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), a następnie skonfiguruj łączność między [lokacjami](../managed-instance/point-to-site-p2s-configure.md) i [maszynami](../managed-instance/connect-vm-instance-configure.md) wirtualnymi.
>
> Aby załadować dane, zobacz [Restore with BACPAC](database-import.md) with the [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) File lub zobacz [przywracanie bazy danych Wide World](../managed-instance/restore-sample-database-quickstart.md)Imports.

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL** lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** Przejrzyj w pełni kwalifikowaną nazwę serwera obok pozycji **nazwa serwera** dla Azure SQL Database lub w pełni kwalifikowana nazwa serwera (lub adres IP) obok pozycji **host** dla wystąpienia zarządzanego Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu dla SQL Server na maszynie wirtualnej platformy Azure, zobacz [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-the-project"></a>Tworzenie projektu

Otwórz wiersz polecenia i utwórz folder o nazwie *sqltest*. Otwórz utworzony folder i uruchom następujące polecenie:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Dodawanie kodu umożliwiającego wykonywanie zapytań w bazie danych

1. W swoim ulubionym edytorze tekstów utwórz nowy plik o nazwie *sqltest.js*.

1. Zastąp jego zawartość poniższym kodem. Następnie dodaj odpowiednie wartości dla swojego serwera, bazy danych, użytkownika i hasła.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> W przykładzie kodu użyto przykładowej bazy danych **AdventureWorksLT** dla usługi Azure SQL.

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom program.

    ```bash
    node sqltest.js
    ```

1. Sprawdź, czy zostało zwróconych 20 pierwszych wierszy, i zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Sterownik firmy Microsoft środowiska Node.js dla programu SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Nawiązywanie połączeń i wykonywanie zapytań w systemie Windows/Linux/macOS za pomocą platformy [.NET Core](connect-query-dotnet-core.md), programu [Visual Studio Code](connect-query-vscode.md) lub programu [SSMS](connect-query-ssms.md) (tylko system Windows)

- [Rozpoczynanie pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli)

- Projektowanie pierwszego Azure SQL Database przy użyciu [platformy .NET lub programu](design-first-database-csharp-tutorial.md) [SSMS](design-first-database-tutorial.md)
