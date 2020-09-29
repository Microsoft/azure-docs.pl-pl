---
title: 'SSMS: łączenie i wykonywanie zapytań dotyczących danych'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Dowiedz się, jak nawiązać połączenie z usługą Azure SQL Database lub wystąpieniem zarządzanym SQL przy użyciu SQL Server Management Studio (SSMS). Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: łączenie z bazą danych SQL, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/28/2020
ms.openlocfilehash: 35a637df85984bcfd20836bcd87aa5ecf1583170
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461142"
---
# <a name="quickstart-use-ssms-to-connect-to-and-query-azure-sql-database-or-azure-sql-managed-instance"></a>Szybki Start: korzystanie z programu SSMS do nawiązywania połączenia z usługą i zapytań Azure SQL Database lub wystąpienia zarządzanego Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W tym przewodniku szybki start dowiesz się, jak używać programu SQL Server Management Studio (SSMS) do nawiązywania połączenia z programem Azure SQL Database lub wystąpieniem zarządzanym usługi Azure SQL i uruchamiać niektóre zapytania.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończenie tego przewodnika Szybki Start wymaga następujących elementów:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).

- Baza danych w Azure SQL Database. Aby utworzyć, a następnie skonfigurować bazę danych w usłudze Azure SQL Database, można użyć instrukcji z jednego z tych przewodników Szybki start:

  | Akcja | Baza danych SQL | Wystąpienie zarządzane SQL | Program SQL Server na maszynie wirtualnej platformy Azure |
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
  > Skrypty zamieszczone w tym artykule korzystają z bazy danych Adventure Works. Za pomocą wystąpienia zarządzanego należy zaimportować bazę danych Adventure Works do bazy danych wystąpienia lub zmodyfikować skrypty znajdujące się w tym artykule, aby korzystały z bazy danych Wide World Importers.

Jeśli po prostu chcesz uruchamiać niektóre zapytania ad hoc bez instalowania programu SSMS, zobacz [Szybki Start: Użyj edytora zapytań Azure Portal, aby wysyłać zapytania do bazy danych w programie Azure SQL Database](connect-query-portal.md).

## <a name="get-server-connection-information"></a>Pobierz informacje o połączeniu z serwerem

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych. Aby ukończyć ten przewodnik Szybki Start, potrzebna jest w pełni kwalifikowana nazwa [serwera](logical-servers.md) lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do **bazy danych** lub **wystąpienia zarządzanego** , dla którego chcesz wykonać zapytanie.

3. Na stronie **Przegląd** Sprawdź w pełni kwalifikowaną nazwę serwera obok **nazwy serwera** dla bazy danych w SQL Database lub w pełni kwalifikowana nazwa serwera (lub adres IP) obok pozycji **host** dla wystąpienia zarządzanego w wystąpieniu zarządzanym SQL lub w wystąpieniu SQL Server na maszynie wirtualnej. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

> [!NOTE]
> Aby uzyskać informacje o połączeniu dla SQL Server na maszynie wirtualnej platformy Azure, zobacz [Connect to SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

W programie SSMS Połącz się z serwerem.

> [!IMPORTANT]
> Serwer nasłuchuje na porcie 1433. Aby nawiązać połączenie z serwerem za zaporą firmową, zapora musi mieć otwarty ten port.

1. Otwórz program SSMS.

2. Zostanie wyświetlone okno dialogowe **Nawiązywanie połączenia z serwerem**. Wprowadź następujące informacje:

   | Ustawienie      | Sugerowana wartość    | Opis |
   | ------------ | ------------------ | ----------- |
   | **Typ serwera** | Aparat bazy danych | Wartość wymagana. |
   | **Nazwa serwera** | W pełni kwalifikowana nazwa serwera | Coś takiego jak: **servername.Database.Windows.NET**. |
   | **Authentication** | Uwierzytelnianie programu SQL Server | W tym samouczku używane jest uwierzytelnianie SQL. |
   | **Zaloguj się** | Identyfikator użytkownika konta administratora serwera | Identyfikator użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Hasło** | Hasło do konta administratora serwera | Hasło użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   ||||

   ![łączenie z serwerem](./media/connect-query-ssms/connect.png)  

> [!NOTE]
> W tym samouczku użyto uwierzytelniania SQL Server.  Jednak w celu nawiązania połączenia za pośrednictwem Azure Active Directory z usługą MFA upewnij się, że korzystasz z programu [SSMS 18,6 lub nowszego](https://aka.ms/ssms). 

3. Wybierz pozycję **Opcje** w oknie dialogowym **Połącz z serwerem**. Z menu rozwijanego **Połącz z bazą danych** wybierz pozycję **mojaPrzykladowaBazaDanych**. Ukończenie szybkiego startu w [sekcji wymagania wstępne](#prerequisites) powoduje utworzenie bazy danych AdventureWorksLT o nazwie mySampleDatabase. Jeśli robocza kopia bazy danych AdventureWorks ma inną nazwę niż mySampleDatabase, zaznacz ją.

   ![nawiązywanie połączenia z bazą danych na serwerze](./media/connect-query-ssms/options-connect-to-db.png)  

4. Wybierz pozycję **Połącz**. Zostanie otwarte okno Eksplorator obiektów.

5. Aby wyświetlić obiekty bazy danych, rozwiń węzeł **Bazy danych**, a następnie rozwiń węzeł odpowiedniej bazy danych.

   ![mySampleDatabase — obiekty](./media/connect-query-ssms/connected.png)  

## <a name="query-data"></a>Zapytania o dane

Uruchom następujący kod z instrukcją [SELECT](/sql/t-sql/queries/select-transact-sql/) języka Transact-SQL, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii.

1. W Eksploratorze obiektów kliknij prawym przyciskiem myszy pozycję **mojaPrzykladowaBazaDanych** i wybierz opcję **Nowe zapytanie**. Zostanie otwarte nowe okno zapytania połączone z Twoją bazą danych.

2. W oknie zapytania Wklej następujące zapytanie SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na pasku narzędzi wybierz opcję **Execute (wykonaj** ), aby uruchomić zapytanie i pobrać dane z `Product` `ProductCategory` tabel i.

    ![zapytanie w celu pobrania danych z tabeli produktów i ProductCategory](./media/connect-query-ssms/query2.png)

### <a name="insert-data"></a>Wstawianie danych

Uruchom następujący kod z instrukcją [INSERT](/sql/t-sql/statements/insert-transact-sql/) języka Transact-SQL, aby utworzyć nowy produkt w tabeli `SalesLT.Product`.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Wybierz polecenie **Wykonaj**, aby wstawić nowy wiersz do tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

#### <a name="view-the-result"></a>Wyświetlanie wyniku

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Wybierz pozycję **Wykonaj**. Zostanie wyświetlony następujący wynik.

   ![wynik zapytania tabeli produktu](./media/connect-query-ssms/result.png)

### <a name="update-data"></a>Aktualizowanie danych

Uruchom ten kod [aktualizacji](/sql/t-sql/queries/update-transact-sql) Transact-SQL, aby zmodyfikować nowy produkt.

1. Zastąp poprzednie zapytanie tym, które zwraca nowy utworzony wcześniej rekord:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

### <a name="delete-data"></a>Usuwanie danych

Uruchom następujący kod z instrukcją [DELETE](/sql/t-sql/statements/delete-transact-sql/) języka Transact-SQL, aby usunąć nowy produkt.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby usunąć określony wiersz z tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o programie SSMS, zobacz [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms/) (Program SQL Server Management Studio).
- Aby połączyć się i wykonać zapytanie za pomocą witryny Azure Portal, zobacz [Connect and query with the Azure portal SQL Query editor (Nawiązywanie połączeń i wykonywanie zapytań za pomocą edytora zapytań SQL w witrynie Azure Portal)](connect-query-portal.md).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą programu Visual Studio Code, zobacz [Connect and query with Visual Studio Code](connect-query-vscode.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą programu Visual Studio Code).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą platformy .NET, zobacz [Connect and query with .NET](connect-query-dotnet-visual-studio.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą platformy .NET).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka PHP, zobacz [Connect and query with PHP](connect-query-php.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka PHP).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą oprogramowania Node.js, zobacz [Connect and query with Node.js](connect-query-nodejs.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą oprogramowania Node.js).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Java, zobacz [Connect and query with Java](connect-query-java.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Java).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Python, zobacz [Connect and query with Python](connect-query-python.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Python).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Ruby, zobacz [Connect and query with Ruby](connect-query-ruby.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Ruby).
