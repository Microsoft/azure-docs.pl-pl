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
ms.openlocfilehash: 8fe541432366d3c2ac1dc1470fea66d328f79780
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88213048"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Szybki Start: używanie platformy .NET i języka C# w programie Visual Studio do łączenia się z bazą danych i wykonywania w niej zapytań w Azure SQL Database lub wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym przewodniku szybki start pokazano, jak używać kodu [.NET Framework](https://www.microsoft.com/net/) i C# w programie Visual Studio do wykonywania zapytań w bazie danych w Azure SQL Database za pomocą instrukcji języka Transact-SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Baza danych w Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  | Akcja | Baza danych SQL | Wystąpienie zarządzane SQL | Program SQL Server na maszynie wirtualnej platformy Azure |
  |:--- |:--- |:---|:---|
  | Utwórz| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Interfejs wiersza polecenia](scripts/create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Program PowerShell](scripts/create-and-configure-database-powershell.md) | [Program PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [Program PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](firewall-create-server-level-portal-quickstart.md)| [Łączność z maszyny wirtualnej](../managed-instance/connect-vm-instance-configure.md)|
  |||[Łączność z lokalnego](../managed-instance/point-to-site-p2s-configure.md) | [Ustanawianie połączenia z programem SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Ładowanie danych|Ładowanie bazy danych Adventure Works na potrzeby samouczka Szybki start|[Przywracanie bazy danych Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Przywracanie bazy danych Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Przywróć lub zaimportuj Adventure Works z pliku [BACPAC](database-import.md) z usługi [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. W przypadku wystąpienia zarządzanego SQL należy zaimportować bazę danych firmy Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty w tym artykule, aby użyć bazy danych Wide World Imports.

- [Program Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional lub Enterprise Edition.

## <a name="get-server-connection-information"></a>Pobierz informacje o połączeniu z serwerem

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL**  lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** Przejrzyj w pełni kwalifikowaną nazwę serwera obok pozycji **Nazwa serwera** dla bazy danych w Azure SQL Database lub w pełni kwalifikowana nazwa serwera (lub adres IP) obok **hosta** dla wystąpienia zarządzanego usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu dla SQL Server na maszynie wirtualnej platformy Azure, zobacz [nawiązywanie połączenia z wystąpieniem SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Utwórz kod do wykonywania zapytań w bazie danych w Azure SQL Database

1. Utwórz nowy projekt w programie Visual Studio. 
   
1. W oknie dialogowym **Nowy projekt** wybierz **Visual C#**, **aplikację konsolową (.NET Framework)**.
   
1. Wprowadź *sqltest* jako nazwę projektu i wybierz **OK**. Zostanie utworzony nowy projekt. 
   
1. Wybierz pozycję **projekt**  >  **Zarządzaj pakietami NuGet**. 
   
1. W **Menedżerze pakietów NuGet**wybierz kartę **Przeglądaj** , a następnie wyszukaj i wybierz pozycję **Microsoft. Data. SqlClient**.
   
1. Na stronie **Microsoft. Data. SqlClient** wybierz pozycję **Zainstaluj**. 
   - W odpowiedzi na monit wybierz **OK**, aby kontynuować instalację. 
   - W przypadku wyświetlenia okna **Akceptacja licencji** wybierz przycisk **Akceptuję**.
   
1. Po zakończeniu instalacji można zamknąć **Menedżera pakietów NuGet**. 
   
1. W edytorze kodu zastąp zawartość **Program.cs** następującym kodem. Zastąp wartości dla `<server>` , `<username>` , `<password>` i `<database>` .
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 
   
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
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
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

1. Aby uruchomić aplikację, wybierz pozycję **Debuguj**  >  **Rozpocznij debugowanie**lub wybierz pozycję **Rozpocznij** na pasku narzędzi lub naciśnij klawisz **F5**.
1. Sprawdź, czy jest zwracanych 20 pierwszych wierszy kategorii/produktu z bazy danych, a następnie zamknij okno aplikacji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak nawiązać połączenie z bazą danych i zbadać ją w Azure SQL Database przy użyciu platformy .NET Core](connect-query-dotnet-core.md) w systemie Windows/Linux/macOS.  
- Dowiedz się więcej o [rozpoczynaniu pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli).
- Dowiedz się, jak [zaprojektować pierwszą bazę danych w Azure SQL Database za pomocą programu SSMS](design-first-database-tutorial.md) lub [zaprojektować pierwszą bazę danych w Azure SQL Database przy użyciu platformy .NET](design-first-database-csharp-tutorial.md).
- Aby uzyskać więcej informacji na temat platformy .NET, zobacz [.NET documentation](https://docs.microsoft.com/dotnet/) (Dokumentacja platformy .NET).
- Przykład logiki ponownych prób: [Połącz się odpornowo z usługą Azure SQL za pomocą ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

