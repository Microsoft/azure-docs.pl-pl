---
title: Używanie Visual Studio z programami .NET i C# do wykonywania zapytań
description: Użyj Visual Studio, aby utworzyć aplikację w języku C#, która nawiązuje połączenie z bazą danych w Azure SQL Database lub Azure SQL Managed Instance i uruchamia zapytania.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1fe39e0ff9acc0c092199ab2dd199cf396f67d01
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874769"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Szybki start: używanie platform .NET i C# w Visual Studio nawiązywania połączenia z bazą danych i wykonywania w jej ramach zapytań
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym przewodniku Szybki start pokazano, jak używać kodu [.NET Framework](https://dotnet.microsoft.com) i C# w języku Visual Studio do wykonywania zapytań w bazie danych w języku Azure SQL lub Synapse SQL za pomocą instrukcji języka Transact-SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019 r.](https://www.visualstudio.com/downloads/) Community, Professional lub Enterprise.
- Baza danych, w której można uruchomić zapytanie.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Tworzenie kodu do wykonywania zapytań w bazie danych w Azure SQL Database

1. Utwórz nowy projekt w programie Visual Studio. 
   
1. W **oknie dialogowym Nowy** projekt wybierz pozycję **Visual C#**, **aplikacja konsolowa (.NET Framework).**
   
1. Wprowadź *sqltest* jako nazwę projektu i wybierz **OK**. Zostanie utworzony nowy projekt. 
   
1. Wybierz **pozycję Project** Manage  >  **NuGet Packages (Zarządzaj pakietami NuGet projektu).** 
   
1. W **witrynie Menedżer pakietów NuGet** wybierz **kartę Przeglądaj,** a następnie wyszukaj i wybierz **pozycję Microsoft.Data.SqlClient.**
   
1. Na stronie **Microsoft.Data.SqlClient** wybierz pozycję **Zainstaluj**. 
   - W odpowiedzi na monit wybierz **OK**, aby kontynuować instalację. 
   - W przypadku wyświetlenia okna **Akceptacja licencji** wybierz przycisk **Akceptuję**.
   
1. Po zakończeniu instalacji można zamknąć **Menedżera pakietów NuGet**. 
   
1. W edytorze kodu zastąp zawartość **Program.cs** następującym kodem. Zastąp wartości `<your_server>` , `<your_username>` `<your_password>` , i `<your_database>` .
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Uruchamianie kodu

1. Aby uruchomić aplikację, wybierz pozycję  >  **Debuguj rozpocznij debugowanie** lub wybierz pozycję **Uruchom** na pasku narzędzi lub naciśnij **klawisz F5.**
1. Sprawdź, czy są zwracane nazwy i sortowanie bazy danych, a następnie zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [nawiązać połączenie z](connect-query-dotnet-core.md) bazą danych i Azure SQL Database za pomocą programu .NET Core w systemie Windows/Linux/macOS.  
- Dowiedz się więcej o [rozpoczynaniu pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli).
- Dowiedz się, jak zaprojektować pierwszą bazę danych w programie Azure SQL Database przy użyciu [programu SSMS](design-first-database-tutorial.md) lub zaprojektować pierwszą bazę danych w [programie Azure SQL Database przy użyciu programu .NET.](design-first-database-csharp-tutorial.md)
- Aby uzyskać więcej informacji na temat platformy .NET, zobacz [.NET documentation](/dotnet/) (Dokumentacja platformy .NET).
- Przykład logiki ponawiania prób: nawiązywanie połączeń [odpornych na błędy Azure SQL z ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
