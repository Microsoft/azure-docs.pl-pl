---
title: Używanie programu Visual Studio z platformą .NET i C# do wykonywania zapytań
description: Użyj programu Visual Studio, aby utworzyć aplikację w języku C#, która łączy się z bazą danych w Azure SQL Database lub wystąpieniu zarządzanym usługi Azure SQL i uruchamia zapytania.
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
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705209"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Szybki Start: używanie platformy .NET i języka C# w programie Visual Studio do łączenia się z bazą danych i wykonywania w niej zapytań
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym przewodniku szybki start pokazano, jak używać kodu [.NET Framework](https://www.microsoft.com/net/) i C# w programie Visual Studio do wykonywania zapytań do bazy danych w usłudze Azure SQL lub Synapse SQL przy użyciu instrukcji języka Transact-SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Program Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional lub Enterprise Edition.
- Baza danych, w której można uruchomić zapytanie.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Utwórz kod do wykonywania zapytań w bazie danych w Azure SQL Database

1. Utwórz nowy projekt w programie Visual Studio. 
   
1. W oknie dialogowym **Nowy projekt** wybierz **Visual C#**, **aplikację konsolową (.NET Framework)**.
   
1. Wprowadź *sqltest* jako nazwę projektu i wybierz **OK**. Zostanie utworzony nowy projekt. 
   
1. Wybierz pozycję **projekt**  >  **Zarządzaj pakietami NuGet**. 
   
1. W **Menedżerze pakietów NuGet** wybierz kartę **Przeglądaj** , a następnie wyszukaj i wybierz pozycję **Microsoft. Data. SqlClient**.
   
1. Na stronie **Microsoft. Data. SqlClient** wybierz pozycję **Zainstaluj**. 
   - W odpowiedzi na monit wybierz **OK**, aby kontynuować instalację. 
   - W przypadku wyświetlenia okna **Akceptacja licencji** wybierz przycisk **Akceptuję**.
   
1. Po zakończeniu instalacji można zamknąć **Menedżera pakietów NuGet**. 
   
1. W edytorze kodu zastąp zawartość **Program.cs** następującym kodem. Zastąp wartości dla `<your_server>` , `<your_username>` , `<your_password>` i `<your_database>` .
   
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

1. Aby uruchomić aplikację, wybierz pozycję **Debuguj**  >  **Rozpocznij debugowanie** lub wybierz pozycję **Rozpocznij** na pasku narzędzi lub naciśnij klawisz **F5**.
1. Sprawdź, czy zostały zwrócone nazwy i sortowania bazy danych, a następnie zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak nawiązać połączenie z bazą danych i zbadać ją w Azure SQL Database przy użyciu platformy .NET Core](connect-query-dotnet-core.md) w systemie Windows/Linux/macOS.  
- Dowiedz się więcej o [rozpoczynaniu pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli).
- Dowiedz się, jak [zaprojektować pierwszą bazę danych w Azure SQL Database za pomocą programu SSMS](design-first-database-tutorial.md) lub [zaprojektować pierwszą bazę danych w Azure SQL Database przy użyciu platformy .NET](design-first-database-csharp-tutorial.md).
- Aby uzyskać więcej informacji na temat platformy .NET, zobacz [.NET documentation](/dotnet/) (Dokumentacja platformy .NET).
- Przykład logiki ponownych prób: [Połącz się odpornowo z usługą Azure SQL za pomocą ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
