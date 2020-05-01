---
title: 'SSMS: łączenie i wykonywanie zapytań dotyczących danych'
description: Dowiedz się, jak łączyć się z bazą danych SQL Database na platformie Azure przy użyciu programu SQL Server Management Studio (SSMS). Następnie uruchom instrukcje Transact-SQL (T-SQL), aby wykonać zapytanie i edytować dane.
keywords: łączenie z bazą danych SQL, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79299298"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Szybki Start: używanie SQL Server Management Studio do nawiązywania połączenia i wysyłania zapytań do bazy danych Azure SQL Database

W tym przewodniku szybki start dowiesz się, jak używać narzędzia SQL Server Management Studio (SSMS) do łączenia się z bazą danych Azure SQL i uruchamiania niektórych zapytań.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończenie tego przewodnika Szybki Start wymaga następujących elementów:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Przykładowa baza danych AdventureWorksLT. Jeśli potrzebujesz działającej kopii bazy danych AdventureWorksLT, utwórz ją, wykonując procedurę szybkiego startu [tworzenia bazy danych Azure SQL Database](sql-database-single-database-get-started.md) .
    - Skrypty w tym artykule są zapisywane w celu korzystania z bazy danych AdventureWorksLT. Jeśli używasz wystąpienia zarządzanego, musisz zaimportować bazę danych AdventureWorks do bazy danych wystąpienia lub zmodyfikować skrypty w tym artykule, aby użyć bazy danych Wide World Imports.

Jeśli po prostu chcesz uruchamiać niektóre zapytania ad hoc bez instalowania programu SSMS, zobacz [Szybki Start: Użyj edytora zapytań Azure Portal, aby wykonać zapytanie do bazy danych SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych. Aby ukończyć ten przewodnik Szybki Start, potrzebna jest w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do **bazy danych SQL** lub **wystąpienia zarządzanego SQL** , które chcesz zbadać.

3. Na stronie **Przegląd** Skopiuj w pełni kwalifikowaną nazwę serwera. Jest to **Nazwa serwera** dla pojedynczej bazy danych lub w pełni kwalifikowana nazwa serwera obok **hosta** dla wystąpienia zarządzanego. W pełni kwalifikowana nazwa wygląda następująco: *servername.Database.Windows.NET*, z tą różnicą, że ma rzeczywistą nazwę serwera.

## <a name="connect-to-your-database"></a>Nawiązywanie połączenia z bazą danych

W programie SSMS Połącz się z serwerem Azure SQL Database.

> [!IMPORTANT]
> Serwer usługi Azure SQL Database nasłuchuje na porcie 1433. Aby można było nawiązać połączenie z serwerem usługi SQL Database spoza firmowej zapory, ten port w zaporze musi być otwarty.

1. Otwórz program SSMS.

2. Zostanie wyświetlone okno dialogowe **Nawiązywanie połączenia z serwerem**. Wprowadź następujące informacje.

   | Ustawienie      | Sugerowana wartość    | Opis |
   | ------------ | ------------------ | ----------- |
   | **Typ serwera** | Aparat bazy danych | Wartość wymagana. |
   | **Nazwa serwera** | W pełni kwalifikowana nazwa serwera | Coś takiego jak: **servername.Database.Windows.NET**. |
   | **Authentication** | Uwierzytelnianie programu SQL Server | W tym samouczku używane jest uwierzytelnianie SQL. |
   | **Logowanie** | Identyfikator użytkownika konta administratora serwera | Identyfikator użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   | **Hasło** | Hasło konta administratora serwera | Hasło użytkownika konta administratora serwera, którego użyto do utworzenia serwera. |
   ||||

   ![łączenie z serwerem](./media/sql-database-connect-query-ssms/connect.png)  

3. Wybierz pozycję **Opcje** w oknie dialogowym **Połącz z serwerem**. Z menu rozwijanego **Połącz z bazą danych** wybierz pozycję **mojaPrzykladowaBazaDanych**. Ukończenie szybkiego startu w [sekcji wymagania wstępne](#prerequisites) powoduje utworzenie bazy danych AdventureWorksLT o nazwie mySampleDatabase. Jeśli robocza kopia bazy danych AdventureWorks ma inną nazwę niż mySampleDatabase, zaznacz ją.

   ![nawiązywanie połączenia z bazą danych na serwerze](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Wybierz przycisk **Połącz**. Zostanie otwarte okno Eksplorator obiektów.

5. Aby wyświetlić obiekty bazy danych, rozwiń węzeł **bazy** danych, a następnie rozwiń węzeł bazy danych.

   ![mySampleDatabase — obiekty](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Zapytania o dane

Uruchom następujący kod z instrukcją [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) języka Transact-SQL, aby wykonać zapytanie o 20 najpopularniejszych produktów według kategorii.

1. W Eksploratorze obiektów kliknij prawym przyciskiem myszy pozycję **mojaPrzykladowaBazaDanych** i wybierz opcję **Nowe zapytanie**. Zostanie otwarte nowe okno zapytania połączone z Twoją bazą danych.

2. W oknie zapytania Wklej następujące zapytanie SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na pasku narzędzi wybierz opcję **Execute (wykonaj** ), aby uruchomić zapytanie i pobrać `Product` dane `ProductCategory` z tabel i.

    ![zapytanie w celu pobrania danych z tabeli produktów i ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Wstawianie danych

Uruchom następujący kod z instrukcją [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) języka Transact-SQL, aby utworzyć nowy produkt w tabeli `SalesLT.Product`.

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

   ![wynik zapytania tabeli produktu](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Aktualizowanie danych

Uruchom ten kod [aktualizacji](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL, aby zmodyfikować nowy produkt.

1. Zastąp poprzednie zapytanie tym, które zwraca nowy utworzony wcześniej rekord:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

### <a name="delete-data"></a>Usuwanie danych

Uruchom następujący kod z instrukcją [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) języka Transact-SQL, aby usunąć nowy produkt.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Wykonaj**, aby usunąć określony wiersz z tabeli `Product`. W okienku **Komunikaty** zostanie wyświetlony komunikat **(dotyczy 1 wiersza)**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji o programie SSMS, zobacz [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (Program SQL Server Management Studio).
- Aby połączyć się i wykonać zapytanie za pomocą witryny Azure Portal, zobacz [Connect and query with the Azure portal SQL Query editor (Nawiązywanie połączeń i wykonywanie zapytań za pomocą edytora zapytań SQL w witrynie Azure Portal)](sql-database-connect-query-portal.md).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą programu Visual Studio Code, zobacz [Connect and query with Visual Studio Code](sql-database-connect-query-vscode.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą programu Visual Studio Code).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą platformy .NET, zobacz [Connect and query with .NET](sql-database-connect-query-dotnet.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą platformy .NET).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka PHP, zobacz [Connect and query with PHP](sql-database-connect-query-php.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka PHP).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą oprogramowania Node.js, zobacz [Connect and query with Node.js](sql-database-connect-query-nodejs.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą oprogramowania Node.js).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Java, zobacz [Connect and query with Java](sql-database-connect-query-java.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Java).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Python, zobacz [Connect and query with Python](sql-database-connect-query-python.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Python).
- Aby nawiązywać połączenia i wykonywać zapytania za pomocą języka Ruby, zobacz [Connect and query with Ruby](sql-database-connect-query-ruby.md) (Nawiązywanie połączeń i wykonywanie zapytań za pomocą języka Ruby).
