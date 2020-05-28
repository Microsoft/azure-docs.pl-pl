---
title: Używanie programu Visual Studio z platformą .NET i C# do wykonywania zapytań
description: Użyj programu Visual Studio, aby utworzyć aplikację w języku C#, która nawiązuje połączenie z bazą danych SQL Microsoft Azure i wysyła do niej zapytania przy użyciu instrukcji języka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/31/2019
ms.openlocfilehash: ea274805e936c327bf9db1c5eedc7e55a32d6c5f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054263"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-azure-sql-database"></a>Szybki Start: używanie platformy .NET i języka C# w programie Visual Studio do łączenia się z bazą danych Azure SQL i wykonywania w niej zapytań
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym przewodniku szybki start pokazano, jak używać kodu [.NET Framework](https://www.microsoft.com/net/) i C# w programie Visual Studio do wykonywania zapytań w bazie danych Azure SQL przy użyciu instrukcji języka Transact-SQL.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz spełnić następujące warunki:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Baza danych Azure SQL Database. Możesz użyć jednego z tych przewodników Szybki Start, aby utworzyć i skonfigurować bazę danych w usłudze Azure SQL:

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

  > [!IMPORTANT]
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. W przypadku wystąpienia zarządzanego SQL należy zaimportować bazę danych firmy Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty w tym artykule, aby użyć bazy danych Wide World Imports.

- [Program Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional lub Enterprise Edition.

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL** lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** Przejrzyj w pełni kwalifikowaną nazwę serwera obok pozycji **nazwa serwera** dla Azure SQL Database lub w pełni kwalifikowana nazwa serwera (lub adres IP) obok pozycji **host** dla wystąpienia zarządzanego Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu dla SQL Server na maszynie wirtualnej platformy Azure, zobacz [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-code-to-query-the-sql-database"></a>Tworzenie kodu zapytania do bazy danych SQL

1. W programie Visual Studio wybierz pozycję **plik**  >  **Nowy**  >  **projekt**. 
   
1. W oknie dialogowym **Nowy projekt** wybierz pozycję **Visual C#** i wybierz opcję **Aplikacja konsolowa (.NET Framework)**.
   
1. Wprowadź *sqltest* jako nazwę projektu i wybierz **OK**. Zostanie utworzony nowy projekt. 
   
1. Wybierz pozycję **projekt**  >  **Zarządzaj pakietami NuGet**. 
   
1. W **Menedżerze pakietów NuGet** wybierz kartę **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **System.Data.SqlClient**.
   
1. Na stronie **System.Data.SqlClient** wybierz przycisk **Instaluj**. 
   - W odpowiedzi na monit wybierz **OK**, aby kontynuować instalację. 
   - W przypadku wyświetlenia okna **Akceptacja licencji** wybierz przycisk **Akceptuję**.
   
1. Po zakończeniu instalacji można zamknąć **Menedżera pakietów NuGet**. 
   
1. W edytorze kodu zastąp zawartość **Program.cs** następującym kodem. Zastąp wartości dla `<server>` , `<username>` , `<password>` i `<database>` .
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 
   
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

- Dowiedz się, jak [uzyskać połączenie i wykonywać zapytania do bazy danych Azure SQL Database przy użyciu platformy .NET Core](connect-query-dotnet-core.md) w systemach operacyjnych Windows/Linux/macOS.  
- Dowiedz się więcej o [rozpoczynaniu pracy z platformą .NET Core w systemie Windows/Linux/macOS przy użyciu wiersza polecenia](/dotnet/core/tutorials/using-with-xplat-cli).
- Dowiedz się, jak [zaprojektować pierwszą bazę danych Azure SQL Database przy użyciu narzędzia SSMS](design-first-database-tutorial.md) lub [zaprojektować pierwszą bazę danych Azure SQL Database przy użyciu platformy .NET](design-first-database-csharp-tutorial.md).
- Aby uzyskać więcej informacji na temat platformy .NET, zobacz [.NET documentation](https://docs.microsoft.com/dotnet/) (Dokumentacja platformy .NET).
- Przykład logiki ponownych prób: [Połącz się z usługą SQL za pomocą ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

