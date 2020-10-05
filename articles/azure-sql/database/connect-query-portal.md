---
title: Wykonywanie zapytania dotyczącego SQL Database przy użyciu edytora zapytań w Azure Portal (wersja zapoznawcza)
description: Dowiedz się, jak używać edytora zapytań do uruchamiania zapytań Transact-SQL (T-SQL) względem bazy danych w Azure SQL Database.
titleSuffix: Azure SQL Database
keywords: Nawiązywanie połączenia z usługą SQL Database, tworzenie zapytań do bazy danych SQL, witryna Azure Portal, portal, Edytor zapytań
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: Ninarn
ms.author: ninarn
ms.reviewer: sstein
ms.date: 05/29/2020
ms.openlocfilehash: 4eb02c65a8ce486ea152863d8b6c6d0600280893
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91409594"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Szybki Start: korzystanie z edytora zapytań Azure Portal (wersja zapoznawcza) w celu zbadania Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Edytor zapytań jest narzędziem w Azure Portal do uruchamiania zapytań SQL dotyczących bazy danych w Azure SQL Database lub hurtowni danych w usłudze Azure Synapse Analytics. 

W tym przewodniku szybki start będziesz używać edytora zapytań do uruchamiania zapytań Transact-SQL (T-SQL) względem bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończenie tego przewodnika Szybki Start wymaga przykładowej bazy danych AdventureWorksLT. Jeśli nie masz działającej kopii przykładowej bazy danych AdventureWorksLT w SQL Database, następnym przewodnikiem Szybki Start utworzysz:

- [Szybki Start: Tworzenie bazy danych w Azure SQL Database przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](single-database-create-quickstart.md) 

### <a name="configure-network-settings"></a>Konfigurowanie ustawień sieciowych

Jeśli w edytorze zapytań zostanie wyświetlony jeden z następujących błędów: *Ustawienia sieci lokalnej mogą uniemożliwiać wygenerowanie zapytań przez Edytor zapytań. Kliknij tutaj, aby uzyskać instrukcje dotyczące sposobu konfigurowania ustawień sieciowych*lub nie można *nawiązać połączenia z serwerem. Może to wskazywać na problem z konfiguracją lokalnej zapory lub ustawieniami serwera proxy sieci*, dlatego następujące ważne informacje powinny pomóc w rozwiązaniu:

> [!IMPORTANT]
> Edytor zapytań używa portów 443 i 1443 do komunikacji. Upewnij się, że włączono ruch wychodzący HTTPS na tych portach. Należy również [dodać wychodzący adres IP do reguł zapory dozwolonych na serwerze](firewall-create-server-level-portal-quickstart.md) , aby uzyskać dostęp do baz danych i magazynów.


## <a name="open-the-sql-database-query-editor"></a>Otwórz Edytor zapytań SQL Database

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i wybierz bazę danych, którą chcesz zbadać.

2. W menu **bazy danych SQL** wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**.

    ![znajdowanie edytora zapytań](./media/connect-query-portal/find-query-editor.PNG)


## <a name="establish-a-connection-to-the-database"></a>Nawiąż połączenie z bazą danych

Mimo że logujesz się do portalu, nadal musisz podać poświadczenia, aby uzyskać dostęp do bazy danych. Możesz połączyć się przy użyciu uwierzytelniania SQL lub Azure Active Directory, aby nawiązać połączenie z bazą danych.

### <a name="connect-using-sql-authentication"></a>Nawiązywanie połączenia przy użyciu uwierzytelniania SQL

1. Na stronie **Logowanie** w obszarze **uwierzytelnianie programu SQL Server**wprowadź **Identyfikator logowania** i **hasło** użytkownika, który ma dostęp do bazy danych. Jeśli nie masz pewności, użyj nazwy logowania i hasła administratora serwera bazy danych.

    ![logowanie](./media/connect-query-portal/login-menu.png)

2. Wybierz przycisk **OK**.


### <a name="connect-using-azure-active-directory"></a>Nawiązywanie połączenia przy użyciu usługi Azure Active Directory

Skonfigurowanie administratora usługi Azure Active Directory (Azure AD) umożliwia użycie pojedynczej tożsamości do logowania się do Azure Portal i bazy danych. Aby nawiązać połączenie z bazą danych przy użyciu usługi Azure AD, wykonaj poniższe kroki, aby skonfigurować administratora usługi Azure AD dla wystąpienia SQL Server.

> [!NOTE]
> * Konta e-mail (na przykład outlook.com, gmail.com, yahoo.com itd.) nie są jeszcze obsługiwane jako Administratorzy usługi Azure AD. Upewnij się, że wybrano użytkownika w trybie macierzystym w usłudze Azure AD lub w ramach Federacji do usługi Azure AD.
> * Logowanie administratora usługi Azure AD nie działa w przypadku kont, dla których włączono uwierzytelnianie 2-etapowe.

#### <a name="set-an-active-directory-admin-for-the-server"></a>Ustaw administratora Active Directory dla serwera

1. W Azure Portal wybierz wystąpienie SQL Server.

2. W menu **programu SQL Server** wybierz **Active Directory administrator**.

3. Na pasku narzędzi SQL Server **Active Directory administrator** wybierz pozycję **Ustaw administratora** , a następnie wybierz użytkownika lub grupę jako administratora usługi Azure AD.

    ![wybierz usługę active directory](./media/connect-query-portal/select-active-directory.png)

4. Na stronie **Dodawanie administratora** w polu wyszukiwania wprowadź użytkownika lub grupę do znalezienia, wybierz ją jako administratora, a następnie wybierz przycisk **Wybierz** .

5. Na pasku narzędzi SQL Server **Active Directory administrator** wybierz pozycję **Zapisz**.

### <a name="connect-to-the-database"></a>Łączenie z bazą danych

6. W menu **programu SQL Server** wybierz opcję **bazy danych SQL**, a następnie wybierz bazę danych.

7. W menu **bazy danych SQL** wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**. Na stronie **logowania** na etykiecie **uwierzytelniania Active Directory** zostanie wyświetlony komunikat informujący, że użytkownik został zalogowany, jeśli jesteś administratorem usługi Azure AD. Następnie wybierz przycisk **Kontynuuj jako** *\<your user or group ID>* . Jeśli strona wskazuje, że użytkownik nie zalogował się pomyślnie, może być konieczne odświeżenie strony.

## <a name="query-a-database-in-sql-database"></a>Tworzenie zapytań względem bazy danych w SQL Database

Następujące przykładowe zapytania powinny zostać pomyślnie wykonane względem przykładowej bazy danych AdventureWorksLT.

### <a name="run-a-select-query"></a>Uruchamianie zapytania SELECT

1. Wklej następujące zapytanie do edytora zapytań:

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. Wybierz pozycję **Uruchom** , a następnie przejrzyj dane wyjściowe w okienku **wyników** .

   ![wyniki edytora zapytań](./media/connect-query-portal/query-editor-results.png)

3. Opcjonalnie można zapisać zapytanie jako plik. SQL lub wyeksportować zwrócone dane jako plik JSON, CSV lub XML.

### <a name="run-an-insert-query"></a>Uruchom zapytanie INSERT

Uruchom następującą instrukcję [INSERT](/sql/t-sql/statements/insert-transact-sql/) -SQL, aby dodać nowy produkt do `SalesLT.Product` tabeli.

1. Wpisz to zapytanie w miejsce poprzedniego.

    ```sql
    INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
    VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. Wybierz polecenie **Uruchom**, aby wstawić nowy wiersz do tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.


### <a name="run-an-update-query"></a>Uruchamianie zapytania UPDATE

Aby zmodyfikować nowy produkt, uruchom następującą instrukcję [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.

### <a name="run-a-delete-query"></a>Uruchamianie kwerendy usuwania

Uruchom następującą instrukcję [delete](/sql/t-sql/statements/delete-transact-sql/) T-SQL, aby usunąć nowy produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby usunąć wiersz z tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.


## <a name="query-editor-considerations"></a>Zagadnienia dotyczące edytora zapytań

Jest kilka rzeczy, o których trzeba wiedzieć podczas pracy z edytorem zapytań.

* Edytor zapytań używa portów 443 i 1443 do komunikacji. Upewnij się, że włączono ruch wychodzący HTTPS na tych portach. Należy również dodać wychodzący adres IP do reguł zapory dozwolonych na serwerze, aby uzyskać dostęp do baz danych i magazynów.

* Jeśli masz połączenie z linkiem prywatnym, Edytor zapytań działa bez konieczności dodawania adresu IP klienta do zapory SQL Databaseowej.

* Naciśnięcie klawisza **F5** spowoduje odświeżenie strony edytora zapytań, a wszystkie wykonywane zapytania są tracone.

* Edytor zapytań nie obsługuje nawiązywania połączeń z bazą danych `master`.

* Istnieje limit czasu na wykonanie zapytania wynoszący 5 minut.

* Edytor zapytań obsługuje tylko cylindryczne projekcje dla typów danych geograficznych.

* W przypadku tabel i widoków bazy danych nie jest obsługiwana funkcja IntelliSense, ale Edytor obsługuje Autouzupełnianie dla nazw, które zostały już wpisane.




## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat języka Transact-SQL (T-SQL) obsługiwanego w Azure SQL Database, zobacz [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database](transact-sql-tsql-differences-sql-server.md).
