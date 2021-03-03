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
ms.date: 03/01/2021
ms.openlocfilehash: b2b614002a380ecd9c7eb6ec40e9dbd734b99628
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692848"
---
# <a name="quickstart-use-the-azure-portals-query-editor-preview-to-query-an-azure-sql-database"></a>Szybki Start: korzystanie z edytora zapytań Azure Portal (wersja zapoznawcza) w celu zbadania Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Edytor zapytań jest narzędziem w Azure Portal do uruchamiania zapytań SQL dotyczących bazy danych w Azure SQL Database lub hurtowni danych w usłudze Azure Synapse Analytics.

W tym przewodniku szybki start będziesz używać edytora zapytań do uruchamiania zapytań Transact-SQL (T-SQL) względem bazy danych.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="create-a-database-with-sample-data"></a>Tworzenie bazy danych z przykładowymi danymi

Ukończenie tego przewodnika Szybki Start wymaga przykładowej bazy danych AdventureWorksLT. Jeśli nie masz działającej kopii przykładowej bazy danych AdventureWorksLT w SQL Database, Poniższy przewodnik Szybki Start pomaga szybko utworzyć:

[Szybki Start: Tworzenie bazy danych w Azure SQL Database przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](single-database-create-quickstart.md)

### <a name="set-an-azure-active-directory-admin-for-the-server-optional"></a>Ustaw administratora Azure Active Directory dla serwera (opcjonalnie)

Skonfigurowanie administratora usługi Azure Active Directory (Azure AD) umożliwia użycie pojedynczej tożsamości do logowania się do Azure Portal i bazy danych. Jeśli chcesz używać usługi Azure AD do łączenia się z edytorem zapytań, wykonaj poniższe czynności.

Ten proces jest opcjonalny, zamiast tego można użyć uwierzytelniania SQL w celu nawiązania połączenia z edytorem zapytań.

> [!NOTE]
> * Konta e-mail (na przykład outlook.com, gmail.com, yahoo.com itd.) nie są jeszcze obsługiwane jako Administratorzy usługi Azure AD. Upewnij się, że wybrano użytkownika w trybie macierzystym w usłudze Azure AD lub w ramach Federacji do usługi Azure AD.
> * Logowanie administratora usługi Azure AD działa z kontami z włączonym uwierzytelnianiem dwuskładnikowym, ale Edytor zapytań nie obsługuje uwierzytelniania dwuskładnikowego.

1. W Azure Portal przejdź do serwera bazy danych SQL.

2. W menu **programu SQL Server** wybierz **Active Directory administrator**.

3. Na pasku narzędzi Strona **administracyjna SQL Server Active Directory** wybierz pozycję **Ustaw administratora**.

    ![wybierz usługę active directory](./media/connect-query-portal/select-active-directory.png)

4. Na stronie **Dodawanie administratora** w polu wyszukiwania wprowadź użytkownika lub grupę do znalezienia, wybierz ją jako administratora, a następnie wybierz przycisk **Wybierz** .

5. Na pasku narzędzi SQL Server **Active Directory administrator** wybierz pozycję **Zapisz**.

## <a name="using-sql-query-editor"></a>Korzystanie z edytora zapytań SQL

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i wybierz bazę danych, którą chcesz zbadać.

2. W menu **bazy danych SQL** wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**.

    ![znajdowanie edytora zapytań](./media/connect-query-portal/find-query-editor.PNG)

### <a name="establish-a-connection-to-the-database"></a>Nawiąż połączenie z bazą danych

Mimo że logujesz się do portalu, nadal musisz podać poświadczenia, aby uzyskać dostęp do bazy danych. Możesz połączyć się przy użyciu uwierzytelniania SQL lub Azure Active Directory, aby nawiązać połączenie z bazą danych.

#### <a name="connect-using-sql-authentication"></a>Nawiązywanie połączenia przy użyciu uwierzytelniania SQL

1. Na stronie **Logowanie** w obszarze **uwierzytelnianie programu SQL Server** wprowadź **Identyfikator logowania** i **hasło** użytkownika, który ma dostęp do bazy danych. Jeśli nie masz pewności, użyj nazwy logowania i hasła administratora serwera bazy danych.

    ![logowanie](./media/connect-query-portal/login-menu.png)

2. Wybierz przycisk **OK**.

#### <a name="connect-using-azure-active-directory"></a>Nawiązywanie połączenia przy użyciu usługi Azure Active Directory

W **Edytorze zapytań (wersja zapoznawcza)** zapoznaj się ze stroną **logowania** w sekcji **Uwierzytelnianie Active Directory** . Uwierzytelnianie zostanie wykonane automatycznie, więc jeśli jesteś administratorem usługi Azure AD z bazą danych, zobaczysz komunikat informujący, że użytkownik zalogował się. Następnie wybierz przycisk **Kontynuuj jako** *\<your user or group ID>* . Jeśli strona wskazuje, że użytkownik nie zalogował się pomyślnie, może być konieczne odświeżenie strony.

### <a name="query-a-database-in-sql-database"></a>Tworzenie zapytań względem bazy danych w SQL Database

Następujące przykładowe zapytania powinny zostać pomyślnie wykonane względem przykładowej bazy danych AdventureWorksLT.

#### <a name="run-a-select-query"></a>Uruchamianie zapytania SELECT

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

#### <a name="run-an-insert-query"></a>Uruchom zapytanie INSERT

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


#### <a name="run-an-update-query"></a>Uruchamianie zapytania UPDATE

Aby zmodyfikować nowy produkt, uruchom następującą instrukcję [Update](/sql/t-sql/queries/update-transact-sql/) T-SQL.

1. Wpisz to zapytanie w miejsce poprzedniego.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby zaktualizować określony wiersz w tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.

#### <a name="run-a-delete-query"></a>Uruchamianie kwerendy usuwania

Uruchom następującą instrukcję [delete](/sql/t-sql/statements/delete-transact-sql/) T-SQL, aby usunąć nowy produkt.

1. Zastąp poprzednie zapytanie następującym:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Wybierz polecenie **Uruchom**, aby usunąć wiersz z tabeli `Product`. W okienku **komunikaty** zostanie wyświetlone **zapytanie zakończone powodzeniem: uwzględnione wiersze: 1**.


## <a name="troubleshooting-and-considerations"></a>Rozwiązywanie problemów i istotne zagadnienia

Jest kilka rzeczy, o których trzeba wiedzieć podczas pracy z edytorem zapytań.

### <a name="configure-local-network-settings"></a>Konfigurowanie ustawień sieci lokalnej

Jeśli w edytorze zapytań zostanie wyświetlony jeden z następujących błędów:
 - *Ustawienia sieci lokalnej mogą uniemożliwiać edytorowi zapytań wystawianie zapytań. Kliknij tutaj, aby uzyskać instrukcje dotyczące konfigurowania ustawień sieci*
 - *Nie można nawiązać połączenia z serwerem. Może to wskazywać na problem z konfiguracją lokalnej zapory lub ustawieniami serwera proxy sieci*

Wynika to z faktu, że Edytor zapytań używa portów 443 i 1443 do komunikacji. Należy upewnić się, że włączono ruch wychodzący HTTPS na tych portach. Poniższe instrukcje zamieszczono w tym przewodniku, w zależności od używanego systemu operacyjnego. Może być konieczne skontaktowanie się z działem IT w celu udzielenia zgody na otwarcie tego połączenia w sieci lokalnej.

#### <a name="steps-for-windows"></a>Kroki dla systemu Windows

1. Otwórz **zaporę Windows Defender**
2. W menu po lewej stronie wybierz pozycję **Ustawienia zaawansowane** .
3. W oknie **Zapora Windows Defender z zabezpieczeniami zaawansowanymi** wybierz pozycję **reguły ruchu wychodzącego** w menu po lewej stronie.
4. Wybierz **nową regułę...** w menu po prawej stronie

W **Kreatorze nowej reguły ruchu wychodzącego** wykonaj następujące czynności:

1. Wybierz pozycję **port** jako typ reguły, którą chcesz utworzyć. Wybierz pozycję **Dalej**
2. Wybieranie **protokołu TCP**
3. Wybierz **określone porty zdalne** i wprowadź wartość "443, 1443". Następnie wybierz pozycję **dalej** .
4. Wybierz pozycję "Zezwalaj na połączenie, jeśli jest bezpieczne"
5. Wybierz pozycję **dalej** , a następnie ponownie wybierz pozycję **dalej** .
5. Zachowaj wszystkie wybrane "Domain", "Private" i "Public"
6. Nadaj regule nazwę, na przykład "dostęp do edytora zapytań usługi Azure SQL" i opcjonalnie opis. Następnie wybierz pozycję **Zakończ** .

#### <a name="steps-for-mac"></a>Kroki dla komputerów Mac
1. Otwórz aplet **Preferencje systemu** (menu Apple > Preferencje systemowe).
2. Kliknij pozycję **zabezpieczenia & prywatność**.
3. Kliknij pozycję **Zapora**.
4. Jeśli Zapora jest wyłączona, wybierz **pozycję Kliknij blokadę, aby wprowadzić zmiany** na dole, a następnie wybierz pozycję **Włącz zaporę** .
4. Kliknij pozycję **Opcje zapory**.
5. W oknie **zabezpieczenia & prywatności** wybierz tę opcję: "automatycznie Zezwalaj na odbieranie połączeń przychodzących przez podpisane oprogramowanie".

#### <a name="steps-for-linux"></a>Kroki dla systemu Linux
Uruchom te polecenia, aby zaktualizować dołączenie iptables
  ```
  sudo iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
  sudo iptables -A OUTPUT -p tcp --dport 1443 -j ACCEPT
  ```

### <a name="connection-considerations"></a>Zagadnienia dotyczące połączenia

* W przypadku połączeń publicznych z edytorem zapytań należy [dodać wychodzący adres IP do reguł zapory dozwolonych na serwerze](firewall-create-server-level-portal-quickstart.md) , aby uzyskać dostęp do baz danych i magazynów.

* Jeśli skonfigurowano połączenie z linkiem prywatnym na serwerze i łączysz się z edytorem zapytań z adresu IP w Virtual Network prywatnym, Edytor zapytań działa bez konieczności dodawania adresu IP klienta do reguł zapory serwera SQL Database.

* Najważniejszymi uprawnieniami RBAC wymaganymi do korzystania z edytora zapytań jest dostęp do odczytu do serwera i bazy danych. Każda osoba mająca ten poziom dostępu może uzyskać dostęp do funkcji edytora zapytań. Aby ograniczyć dostęp do określonych użytkowników, należy uniemożliwić im zalogowanie się do edytora zapytań przy użyciu poświadczeń uwierzytelniania Azure Active Directory lub SQL. Jeśli nie można przypisać siebie jako administratora usługi AAD dla serwera lub dostęp/Dodawanie konta administratora SQL, nie powinno być możliwe korzystanie z edytora zapytań.

* Edytor zapytań nie obsługuje nawiązywania połączeń z bazą danych `master`.

* Edytor zapytań nie może nawiązać połączenia z repliką bazy danych za pomocą `ApplicationIntent=ReadOnly`

* Jeśli zostanie wyświetlony komunikat o błędzie "nie można sprawdzić poprawności nagłówka X-CSRF-Signature", wykonaj następujące czynności w celu rozwiązania problemu:

    * Upewnij się, że zegar komputera jest ustawiony na właściwą strefę czasową i czasową. Możesz również spróbować dopasować strefę czasową komputera do platformy Azure, wyszukując strefę czasową dla lokalizacji wystąpienia, na przykład Wschodnie stany USA, Pacyfik i tak dalej.
    * Jeśli używasz sieci proxy, upewnij się, że nagłówek żądania "X-CSRF-Signature" nie jest modyfikowany ani porzucany.

### <a name="other-considerations"></a>Inne zagadnienia

* Naciśnięcie klawisza **F5** spowoduje odświeżenie strony edytora zapytań, a wszystkie wykonywane zapytania są tracone.

* Istnieje limit czasu na wykonanie zapytania wynoszący 5 minut.

* Edytor zapytań obsługuje tylko cylindryczne projekcje dla typów danych geograficznych.

* W przypadku tabel i widoków bazy danych nie jest obsługiwana funkcja IntelliSense, ale Edytor obsługuje Autouzupełnianie dla nazw, które zostały już wpisane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat języka Transact-SQL (T-SQL) obsługiwanego w Azure SQL Database, zobacz [Rozwiązywanie różnic w języku Transact-SQL podczas migracji do SQL Database](transact-sql-tsql-differences-sql-server.md).
