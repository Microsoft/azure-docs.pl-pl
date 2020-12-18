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
ms.openlocfilehash: 37cd2051670221b8b78c075f249f633f9f447099
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674309"
---
# <a name="quickstart-use-net-core-c-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Szybki Start: używanie platformy .NET Core (C#) do wykonywania zapytań w bazie danych w Azure SQL Database lub wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym przewodniku szybki start będziesz używać kodu [.NET Core](https://www.microsoft.com/net/) i C# do łączenia się z bazą danych. Następnie uruchomisz instrukcję języka Transact-SQL w celu wykonania zapytania na danych.

> [!TIP]
> Poniższy moduł Microsoft Learn umożliwia zapoznanie się z bezpłatnymi sposobami [tworzenia i konfigurowania aplikacji ASP.NET, która wysyła zapytanie do bazy danych w Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności opisanych w tym przewodniku Szybki start potrzebne są następujące elementy:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Baza danych programu. Możesz użyć jednego z tych przewodników Szybki Start, aby utworzyć i skonfigurować bazę danych:

  | Akcja | Baza danych SQL | Wystąpienie zarządzane SQL | Program SQL Server na maszynie wirtualnej platformy Azure | Azure Synapse Analytics |
  |:--- |:--- |:---|:---|:---|
  | Przycisk Utwórz| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) | [Portal](/azure/synapse-analytics/quickstart-create-workspace.md) |
  || [Interfejs wiersza polecenia](scripts/create-and-configure-database-cli.md) | [Interfejs wiersza polecenia](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) | | [Interfejs wiersza polecenia](/azure/synapse-analytics/quickstart-create-workspace-cli.md) |
  || [Program PowerShell](scripts/create-and-configure-database-powershell.md) | [Program PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [Program PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md) | [Program PowerShell](/azure/synapse-analytics/quickstart-create-workspace-powershell.md) |
  || | | [Szablon wdrożenia](/azure/azure-sql/virtual-machines/windows/create-sql-vm-resource-manager-template.md) | [Szablon wdrożenia](/azure/synapse-analytics/quickstart-deployment-template-workspaces.md) | 
  | Konfigurowanie | [Reguła zapory bazująca na adresach IP na poziomie serwera](firewall-create-server-level-portal-quickstart.md)| [Łączność z maszyny wirtualnej](../managed-instance/connect-vm-instance-configure.md)| |
  |||[Łączność z lokalnego](../managed-instance/point-to-site-p2s-configure.md) | [Nawiązywanie połączenia z wystąpieniem SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md) |
  ||||

- Zainstalowana platforma [.NET Core dla Twojego systemu operacyjnego](https://www.microsoft.com/net/core).

## <a name="get-server-connection-information"></a>Pobierz informacje o połączeniu z serwerem

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych w Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL**  lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** Przejrzyj w pełni kwalifikowaną nazwę serwera obok pozycji **Nazwa serwera** dla bazy danych w Azure SQL Database lub w pełni kwalifikowana nazwa serwera (lub adres IP) obok **hosta** dla wystąpienia zarządzanego usługi Azure SQL lub SQL Server na maszynie wirtualnej platformy Azure. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu dla SQL Server na maszynie wirtualnej platformy Azure, zobacz [nawiązywanie połączenia z wystąpieniem SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>Pobierz informacje o połączeniu ADO.NET (tylko opcjonalne-SQL Database)

1. Przejdź do bloku baza danych w Azure Portal i w obszarze **Ustawienia** wybierz pozycję **Parametry połączenia**.

2. Sprawdź pełne parametry połączenia sterownika **ADO.NET**.

    ![Parametry połączenia sterownika ADO.NET](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Skopiuj parametry połączenia sterownika **ADO.NET**, jeśli zamierzasz go używać.
  
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
