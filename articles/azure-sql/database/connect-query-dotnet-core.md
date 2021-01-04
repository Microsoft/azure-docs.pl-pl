---
title: Korzystanie z platformy .NET Core do łączenia się z bazą danych i wykonywania w niej zapytań
description: W tym temacie pokazano, jak używać platformy .NET Core do utworzenia programu, który nawiązuje połączenie z bazą danych w Azure SQL Database lub wystąpieniu zarządzanym usługi Azure SQL, i wykonuje zapytania przy użyciu instrukcji języka Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705221"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Szybki Start: korzystanie z platformy .NET Core (C#) do wykonywania zapytań w bazie danych
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym przewodniku szybki start będziesz używać kodu [.NET Core](https://www.microsoft.com/net/) i C# do łączenia się z bazą danych. Następnie uruchomisz instrukcję języka Transact-SQL w celu wykonania zapytania na danych.

> [!TIP]
> Poniższy moduł Microsoft Learn umożliwia zapoznanie się z bezpłatnymi sposobami [tworzenia i konfigurowania aplikacji ASP.NET, która wysyła zapytanie do bazy danych w Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Zainstalowana platforma [.NET Core dla Twojego systemu operacyjnego](https://www.microsoft.com/net/core).
- Baza danych, w której można uruchomić zapytanie. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Tworzenie nowego projektu platformy .NET Core

1. Otwórz wiersz polecenia i utwórz folder o nazwie **sqltest**. Przejdź do tego folderu i uruchom to polecenie.

    ```cmd
    dotnet new console
    ```

    To polecenie tworzy nowe pliki projektu aplikacji, w tym początkowy plik kodu C# (**Program.cs**), plik konfiguracyjny XML (**sqltest.csproj**) i potrzebne pliki binarne.

2. W edytorze tekstów otwórz plik **sqltest.csproj** i wklej następujący kod XML między tagami `<Project>`. Ten kod XML dodaje przestrzeń nazw `System.Data.SqlClient` jako zależność.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Wstaw kod do wykonywania zapytań w bazie danych w Azure SQL Database

1. W edytorze tekstów otwórz plik **Program.cs**.

2. Zastąp jego zawartość następującym kodem i dodaj odpowiednie wartości dla serwera, bazy danych, nazwy użytkownika i hasła.

> [!NOTE]
> Aby użyć parametrów połączenia sterownika ADO.NET, zastąp cztery wiersze kodu, które ustawiają serwer, bazę danych, nazwę użytkownika i hasło, za pomocą poniższego wiersza. W parametrach ustaw swoją nazwę użytkownika i hasło.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Uruchamianie kodu

1. W wierszu polecenia uruchom następujące polecenia.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Sprawdź, czy wiersze są zwracane.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Wybierz klawisz **Enter**, aby zamknąć okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli).
- Dowiedz się [, jak nawiązać połączenie z wystąpieniem zarządzanym Azure SQL Database lub z usługą Azure SQL i uzyskać do niego zapytanie, używając .NET Framework i programu Visual Studio](connect-query-dotnet-visual-studio.md).  
- Dowiedz się, jak [zaprojektować pierwszą bazę danych za pomocą programu SSMS](design-first-database-tutorial.md) lub [zaprojektować bazę danych i połączyć się z programem C# i ADO.NET](design-first-database-csharp-tutorial.md).
- Aby uzyskać więcej informacji na temat platformy .NET, zobacz [.NET documentation](/dotnet/) (Dokumentacja platformy .NET).
