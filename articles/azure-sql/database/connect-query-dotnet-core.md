---
title: Nawiązywanie połączeń z bazą danych i wykonywanie zapytań o nie za pomocą programu .NET Core
description: W tym temacie pokazano, jak za pomocą programu .NET Core utworzyć program, który nawiązuje połączenie z bazą danych w programie Azure SQL Database lub Azure SQL Managed Instance i wykonuje zapytania za pomocą instrukcji języka Transact-SQL.
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
ms.openlocfilehash: 221b69d428556b031efd3bd91e16d12cfeb71393
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874805"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Szybki start: używanie programu .NET Core (C#) do wykonywania zapytań w bazie danych
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym przewodniku Szybki start użyjesz programu [.NET Core](https://dotnet.microsoft.com) i kodu C# do nawiązania połączenia z bazą danych. Następnie uruchomisz instrukcję języka Transact-SQL w celu wykonania zapytania na danych.

> [!TIP]
> Poniższy moduł Microsoft Learn za darmo dowiesz się, jak opracowywać i konfigurować aplikację [ASP.NET,](/learn/modules/develop-app-that-queries-azure-sql/) która wysyła zapytania do bazy danych w Azure SQL Database

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [zestaw .NET Core SDK dla zainstalowanego systemu](https://dotnet.microsoft.com/download) operacyjnego.
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

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Wstaw kod, aby odpytować bazę danych w Azure SQL Database

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
- Dowiedz się, jak [nawiązywać połączenia](connect-query-dotnet-visual-studio.md)i Azure SQL Database lub Azure SQL Managed Instance przy użyciu .NET Framework i Visual Studio .  
- Dowiedz się, jak [zaprojektować pierwszą bazę danych za pomocą programu SSMS](design-first-database-tutorial.md) lub zaprojektować bazę danych i nawiązać połączenie przy użyciu [języka C# i ADO.NET.](design-first-database-csharp-tutorial.md)
- Aby uzyskać więcej informacji na temat platformy .NET, zobacz [.NET documentation](/dotnet/) (Dokumentacja platformy .NET).
