---
title: Zabezpieczenia wystąpienia zarządzanego SQL z podmiotami zabezpieczeń serwera usługi Azure AD (logowania)
description: Dowiedz się więcej o technikach i funkcjach zabezpieczania wystąpienia zarządzanego usługi Azure SQL i używania podmiotów zabezpieczeń serwera Azure AD (logowania)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 9161bf4f99ddfed479451d2091458ab309aa2c17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788625"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Samouczek: zabezpieczenia w wystąpieniu zarządzanym usługi Azure SQL przy użyciu podmiotów zabezpieczeń serwera usługi Azure AD (nazwy logowania)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Wystąpienie zarządzane usługi Azure SQL zapewnia niemal wszystkie funkcje zabezpieczeń, które Najnowsza SQL Server (Enterprise Edition) aparat bazy danych:

- Ograniczanie dostępu w środowisku izolowanym
- Korzystanie z mechanizmów uwierzytelniania, które wymagają tożsamości: Azure Active Directory (Azure AD) i uwierzytelniania SQL
- Stosowanie autoryzacji za pośrednictwem członkostwa i uprawnień opartych na rolach.
- Włączanie funkcji zabezpieczeń.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Tworzenie podmiotu zabezpieczeń serwera usługi Azure AD (login) dla wystąpienia zarządzanego
> - Udzielanie uprawnień do jednostek usługi (identyfikatorów logowania) serwera Azure AD w wystąpieniach zarządzanych
> - Tworzenie użytkowników usługi Azure AD na podstawie jednostek usługi (identyfikatorów logowania) serwera Azure AD
> - Przypisywanie uprawnień do użytkowników usługi Azure AD i zarządzanie zabezpieczeniami bazy danych
> - Korzystanie z personifikacji użytkowników usługi Azure AD
> - Korzystanie z zapytań w wielu bazach danych dotyczących użytkowników usługi Azure AD
> - Informacje na temat funkcji zabezpieczeń takich jak ochrona przed zagrożeniami, inspekcja, maskowanie danych i szyfrowanie

Aby dowiedzieć się więcej, zobacz [Omówienie wystąpienia zarządzanego usługi Azure SQL](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że dysponujesz następującymi elementami:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Wystąpienie zarządzane
  - Wykonaj czynności opisane w tym artykule: [Szybki Start: Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md)
- Możliwość dostępu do wystąpienia zarządzanego oraz [aprowizowanego administratora usługi Azure AD dla wystąpienia zarządzanego](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Aby dowiedzieć się więcej, zobacz:
  - [Connect your application to a managed instance](connect-application-instance.md) (Łączenie aplikacji z wystąpieniem zarządzanym)
  - [Architektura łączności wystąpienia zarządzanego SQL](connectivity-architecture-overview.md)
  - [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Ogranicz dostęp 

Zarządzane wystąpienia są dostępne za pomocą prywatnego adresu IP. Podobnie jak izolowane środowisko SQL Server, aplikacje lub użytkownicy potrzebują dostępu do sieci wystąpienia zarządzanego SQL (VNet), zanim będzie można nawiązać połączenie. Aby uzyskać więcej informacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym bazy danych SQL](connect-application-instance.md).

Istnieje również możliwość skonfigurowania punktu końcowego usługi w wystąpieniu zarządzanym, które umożliwia połączenia publiczne w taki sam sposób jak w przypadku Azure SQL Database.
Aby uzyskać więcej informacji, zobacz [Konfigurowanie publicznego punktu końcowego w wystąpieniu zarządzanym usługi Azure SQL](public-endpoint-configure.md).

> [!NOTE]
> Nawet po włączeniu punktów końcowych usługi nie mają zastosowania [reguły zapory Azure SQL Database](../database/firewall-configure.md) . Wystąpienie zarządzane Azure SQL ma własną [wbudowaną zaporę](management-endpoint-verify-built-in-firewall.md) do zarządzania łącznością.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Tworzenie podmiotu zabezpieczeń serwera usługi Azure AD (logowanie) przy użyciu programu SSMS

Pierwszy podmiot zabezpieczeń serwera usługi Azure AD (login) można utworzyć przy użyciu standardowego konta administratora SQL (innego niż Azure AD), które jest `sysadmin` lub administratora usługi Azure AD dla wystąpienia zarządzanego utworzonego podczas procesu aprowizacji. Aby uzyskać więcej informacji, zobacz temat [Inicjowanie obsługi administracyjnej usługi SQL Azure Active Directory](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Zobacz następujące artykuły, aby zapoznać się z przykładami łączenia z wystąpieniem zarządzanym SQL:

- [Szybki Start: Konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym SQL](connect-vm-instance-configure.md)
- [Szybki Start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym SQL z lokalnego](point-to-site-p2s-configure.md)

1. Zaloguj się do swojego wystąpienia zarządzanego przy użyciu standardowego konta logowania SQL (innego niż Azure AD), które jest `sysadmin` lub administratorem usługi Azure AD dla wystąpienia zarządzanego SQL, przy użyciu [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

2. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

3. W oknie zapytania użyj następującej składni, aby utworzyć identyfikator logowania dla lokalnego konta usługi Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    W tym przykładzie utworzono identyfikator logowania dla konta nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na pasku narzędzi wybierz polecenie **Wykonaj**, aby utworzyć identyfikator logowania.

5. Sprawdź nowo dodany identyfikator logowania, wykonując następujące polecenie w języku T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Zrzut ekranu przedstawiający kartę wyniki w s s M s Eksplorator obiektów pokazywanie nazwy, principal_id, identyfikatora SID, typu i type_desc nowo dodanej nazwy logowania.](./media/aad-security-configure-tutorial/native-login.png)

Aby uzyskać więcej informacji, zobacz temat [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="grant-permissions-to-create-logins"></a>Przyznawanie uprawnień do tworzenia logowań

Aby utworzyć inne jednostki usługi (identyfikatory logowania) serwera Azure AD, należy udzielić uprawnień lub ról programu SQL Server jednostce usługi (SQL lub Azure AD).

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

- Jeśli nazwa logowania jest podmiotem SQL, tylko nazwy logowania należące do `sysadmin` roli mogą używać polecenia CREATE do tworzenia logowań dla konta usługi Azure AD.

### <a name="azure-ad-authentication"></a>Uwierzytelnianie w usłudze Azure AD

- Aby umożliwić nowo utworzonym jednostkom usługi (identyfikatorom logowania) serwera Azure AD tworzenie identyfikatorów logowania dla innych użytkowników, grup lub aplikacji usługi Azure AD, musisz udzielić identyfikatorowi logowania roli serwera `sysadmin` lub `securityadmin`.
- Jednostce usługi (identyfikatorowi logowania) serwera Azure AD należy udzielić przynajmniej uprawnienia **ALTER ANY LOGIN**, aby umożliwić jej tworzenie innych jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Domyślnie standardowe uprawnienia przyznawane nowo utworzonym podmiotom zabezpieczeń serwera usługi Azure AD (Logins) w bazie danych Master to: **Connect SQL** i **View any Database**.
- Roli serwera `sysadmin` można udzielić wielu jednostkom usługi (identyfikatorom logowania) serwera Azure AD w ramach wystąpienia zarządzanego.

Aby dodać identyfikator logowania do roli serwera `sysadmin`:

1. Zaloguj się ponownie do wystąpienia zarządzanego lub Użyj istniejącego połączenia z administratorem usługi Azure AD lub podmiotem zabezpieczeń SQL, który jest `sysadmin` .

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

1. Udziel jednostce usługi (identyfikatorowi logowania) serwera Azure AD roli serwera `sysadmin`, korzystając z następującej składni T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    W poniższym przykładzie przydzielono rolę serwera `sysadmin` identyfikatorowi logowania nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Tworzenie dodatkowych jednostek usługi (identyfikatorów logowania) serwera Azure AD przy użyciu programu SSMS

Po utworzeniu jednostki usługi (identyfikatora logowania) serwera Azure AD oraz dodaniu do niej uprawnień `sysadmin` można za jej pomocą tworzyć dodatkowe identyfikatory logowania, używając klauzuli **FROM EXTERNAL PROVIDER** w instrukcji **CREATE LOGIN**.

1. Połącz się z wystąpieniem zarządzanym za pomocą jednostki usługi (identyfikatora logowania) serwera Azure AD, korzystając z programu SQL Server Management Studio. Wprowadź nazwę hosta wystąpienia zarządzanego SQL. Są trzy opcje uwierzytelniania w programie SMSS podczas logowania za pomocą konta usługi Azure AD:

   - Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego
   - Active Directory — hasło
   - Active Directory — zintegrowane </br>

     ![Zrzut ekranu okna dialogowego Łączenie z serwerem w S S M S z obsługą usługi MFA Active Directory-uniwersalną z opcją wybraną na liście rozwijanej uwierzytelniania.](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Aby uzyskać więcej informacji, zobacz [uniwersalne uwierzytelnianie (Obsługa programu SSMS dla Multi-Factor Authentication)](../database/authentication-mfa-ssms-overview.md).

1. Wybierz opcję **Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego**. Spowoduje to wyświetlenie okna logowania Multi-Factor Authentication. Zaloguj się przy użyciu hasła usługi Azure AD.

    ![Zrzut ekranu okna logowania Multi-Factor Authentication z kursorem w polu Wprowadź hasło.](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. W **Eksploratorze obiektów** programu SSMS kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
1. W oknie zapytania użyj następującej składni, aby utworzyć identyfikator logowania dla kolejnego konta usługi Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Ten przykład tworzy identyfikator logowania dla użytkownika usługi Azure AD bob@aadsqlmi.net , którego aadsqlmi.net domeny jest federacyjny z domeną usługi Azure ad aadsqlmi.onmicrosoft.com.

    Wykonaj następujące polecenie w języku T-SQL. Federacyjne konta usługi Azure AD to zamienniki wystąpienia zarządzanego SQL dla lokalnych nazw logowania i użytkowników systemu Windows.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Utwórz bazę danych w zarządzanym wystąpieniu przy użyciu składni [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) . Ta baza danych będzie używana do przetestowania identyfikatorów logowania użytkownika w kolejnej sekcji.
    1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
    1. W oknie zapytania użyj następującej składni, aby utworzyć bazę danych o nazwie **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Utwórz identyfikator logowania wystąpienia zarządzanego SQL dla grupy w usłudze Azure AD. Aby można było dodać logowanie do wystąpienia zarządzanego SQL, musi istnieć Grupa w usłudze Azure AD. Zobacz [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Utwórz grupę _mygroup_ i dodaj członków do tej grupy.

1. Otwórz nowe okno zapytania w programie SQL Server Management Studio.

    W tym przykładzie przyjęto założenie, że istnieje grupa o nazwie Moja _Grupa_ w usłudze Azure AD. Uruchom następujące polecenie:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. W ramach testu zaloguj się do wystąpienia zarządzanego za pomocą nowo utworzonego identyfikatora logowania lub grupy. Otwórz nowe połączenie z wystąpieniem zarządzanym i użyj nowego identyfikatora logowania podczas uwierzytelniania.
1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie** dla nowego połączenia.
1. Sprawdź uprawnienia serwera dla nowo utworzonej jednostki usługi (identyfikatora logowania) serwera Azure AD, wykonując następujące polecenie:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Użytkownicy Gości usługi Azure AD są obsługiwani przez identyfikatory logowania wystąpienia zarządzanego SQL tylko w przypadku dodania ich jako części grupy usługi Azure AD. Użytkownik Gość usługi Azure AD to konto, które jest zapraszane do wystąpienia usługi Azure AD, do którego należy wystąpienie zarządzane, z innego wystąpienia usługi Azure AD. Na przykład joe@contoso.com (konto usługi Azure AD) lub steve@outlook.com (konto Microsoft) można dodać do grupy w wystąpieniu usługi Azure AD aadsqlmi. Po dodaniu użytkowników do grupy można utworzyć nazwę logowania w bazie danych **Master** wystąpienia zarządzanego SQL dla grupy przy użyciu SKŁADNI **CREATE LOGIN** . Użytkownicy-Goście, którzy są członkami tej grupy, mogą łączyć się z wystąpieniem zarządzanym przy użyciu bieżących logowań (na przykład joe@contoso.com lub steve@outlook.com ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Tworzenie użytkownika usługi Azure AD przy użyciu podmiotu zabezpieczeń serwera usługi Azure AD (logowanie)

Autoryzacja poszczególnych baz danych działa tak samo jak w wystąpieniu zarządzanym SQL, tak jak w przypadku baz danych w SQL Server. Można utworzyć użytkownika na podstawie istniejącego identyfikatora logowania w bazie danych i nadać mu uprawienia w tej bazie danych lub dodać go do roli bazy danych.

Po utworzeniu bazy danych o nazwie **MyMITestDB** oraz utworzeniu identyfikatora logowania, który ma tylko uprawnienia domyślne, utworzymy teraz użytkownika na podstawie tego identyfikatora. Obecnie identyfikator logowania może nawiązać połączenie z wystąpieniem zarządzanym i wyświetlać wszystkie bazy danych, ale nie może korzystać z tych baz danych. Jeśli zalogujesz się za pomocą konta usługi Azure AD, które ma uprawnienia domyślne, i spróbujesz rozwinąć nowo utworzoną bazę danych, zostanie wyświetlony następujący komunikat o błędzie:

![Zrzut ekranu komunikatu o błędzie z Eksplorator obiektów S s, który odczytuje "MyMITestDB bazy danych jest niedostępny. (ObjectExplorer)".](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Aby uzyskać więcej informacji na temat udzielania uprawnień bazie danych, zobacz [Getting Started with Database Engine Permissions (Rozpoczynanie pracy z uprawnieniami aparatu bazy danych)](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Tworzenie użytkownika usługi Azure AD oraz tworzenie przykładowej tabeli

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.
1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
1. W oknie zapytania użyj następującej składni, aby utworzyć użytkownika usługi Azure AD na podstawie jednostki usługi (identyfikatora logowania) serwera Azure AD:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    W poniższym przykładzie utworzono użytkownika bob@aadsqlmi.net na podstawie identyfikatora logowania bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Możliwe jest również utworzenie użytkownika usługi Azure AD na podstawie jednostki usługi (identyfikatora logowania) serwera Azure AD, która jest grupą.

    Poniższy przykład tworzy nazwę logowania dla grupy usługi Azure AD, _która istnieje_ w wystąpieniu usługi Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Wszyscy użytkownicy, którzy należą do grupy *mygroup*, mogą uzyskać dostęp do bazy danych **MyMITestDB**.

    > [!IMPORTANT]
    > Podczas tworzenia elementu **USER** na podstawie jednostki usługi (identyfikatora logowania) serwera Azure AD parametr user_name powinien być taki sam jak parametr login_name elementu **LOGIN**.

    Aby uzyskać więcej informacji, zobacz temat [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. W nowym oknie zapytania utwórz tabelę testów, korzystając z następującego polecenia w języku T-SQL:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Utwórz połączenie w programie SSMS z utworzonym użytkownikiem. Zauważysz, że nie można wyświetlić tabeli **TestTable**, która została wcześniej utworzona przez konto `sysadmin`. Musimy nadać temu użytkownikowi uprawnienia odczytu danych z bazy danych.

1. Możesz sprawdzić bieżące uprawnienia użytkownika, wykonując następujące polecenie:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Dodawanie użytkowników do ról na poziomie bazy danych

Wyświetlanie danych przez użytkownika jest możliwe dopiero po przyznaniu mu odpowiednich [ról na poziomie bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles).

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

1. Nadaj użytkownikowi usługi Azure AD rolę bazy danych `db_datareader`, korzystając z następującej składni w języku T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    W poniższym przykładzie przyznano użytkownikowi bob@aadsqlmi.net oraz grupie _mygroup_ uprawnienia `db_datareader` w bazie danych **MyMITestDB**:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Sprawdź, czy użytkownik usługi Azure AD, który został utworzony w bazie danych, już istnieje, wykonując następujące polecenie:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Utwórz nowe połączenie z wystąpieniem zarządzanym z użytkownikiem, który został dodany do roli `db_datareader`.
1. Rozwiń bazę danych w **Eksploratorze obiektów**, aby wyświetlić tabelę.

    ![Zrzut ekranu z Eksplorator obiektów w S S M S pokazujący strukturę folderów dla tabel w MyMITestDB. Obiekt dbo. Folder.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Otwórz nowe okno zapytania i wykonaj następującą instrukcję SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Czy jesteś w stanie wyświetlić dane w tabeli? Kolumny powinny zostać zwrócone.

    ![Zrzut ekranu przedstawiający kartę wyniki w s s M s Eksplorator obiektów pokazując nagłówki kolumny tabeli AccountNum, miasto, nazwa i stan.](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Personifikowanie podmiotów zabezpieczeń na poziomie serwera usługi Azure AD (nazwy logowania)

Wystąpienie zarządzane SQL obsługuje personifikację podmiotów zabezpieczeń na poziomie serwera usługi Azure AD.

### <a name="test-impersonation"></a>Testowanie personifikacji

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.

1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.

1. W oknie zapytania użyj następującego polecenia, aby utworzyć nową procedurę składowaną:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Użyj poniższego polecenia, aby zobaczyć, że użytkownik jest personifikowany podczas wykonywania procedury składowanej, **robert \@ aadsqlmi.NET**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Przetestuj personifikację, korzystając z instrukcji EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Tylko podmioty zabezpieczeń na poziomie programu SQL Server (identyfikatory logowania), które są częścią roli `sysadmin`, mogą wykonać następujące operacje, których celem są podmioty zabezpieczeń usługi Azure AD:
>
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="use-cross-database-queries"></a>Korzystanie z zapytań między bazami danych

Funkcja wykonywania zapytań w wielu bazach danych jest obsługiwana dla kont usługi Azure AD z jednostkami usługi (identyfikatorami logowania) serwera Azure AD. Aby przetestować zapytania w wielu bazach danych w grupie usługi Azure AD, musimy utworzyć kolejną bazę danych i tabelę. Możesz pominąć tworzenie innej bazy danych i tabeli, jeśli taka już istnieje.

1. Zaloguj się do wystąpienia zarządzanego za pomocą konta `sysadmin`, korzystając z programu SQL Server Management Studio.
1. W **Eksploratorze obiektów** kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
1. W oknie zapytania użyj następującego polecenia, aby utworzyć bazę danych o nazwie **MyMITestDB2** oraz tabelę o nazwie **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. W nowym oknie zapytania wykonaj następujące polecenie, aby utworzyć użytkownika _mygroup_ w nowej bazie danych **MyMITestDB2** i udziel uprawnień SELECT w tej bazie danych użytkownikowi _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Zaloguj się do wystąpienia zarządzanego przy użyciu SQL Server Management Studio jako elementu członkowskiego grupy usługi Azure _AD._ Otwórz nowe okno zapytania i wykonaj instrukcję SELECT w wielu bazach danych:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Powinny zostać wyświetlone wyniki z tabeli **TestTable2**.

## <a name="additional-supported-scenarios"></a>Dodatkowe obsługiwane scenariusze

- Dla jednostek usługi (identyfikatorów logowania) serwera Azure AD jest obsługiwane zarządzanie agentem SQL oraz wykonywanie zadań.
- Operacje tworzenia kopii zapasowej oraz przywracania bazy danych mogą być wykonywane przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- [Inspekcja](auditing-configure.md) wszystkich instrukcji związanych z jednostkami usługi (identyfikatorami logowania) serwera Azure AD oraz zdarzeniami uwierzytelniania.
- Dedykowane połączenie administratora dla jednostek usługi (identyfikatorów logowania) serwera Azure AD, które są członkami roli serwera `sysadmin`.
- Nazwy główne (Logins) serwera usługi Azure AD są obsługiwane za pomocą narzędzi [sqlcmd](/sql/tools/sqlcmd-utility) i narzędzia [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) .
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD można skonfigurować usługi Service Broker i DBMail.

## <a name="next-steps"></a>Następne kroki

### <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Zapoznaj się z artykułem dotyczącym [funkcji zabezpieczeń wystąpienia zarządzanego SQL](sql-managed-instance-paas-overview.md#security-features) , aby uzyskać kompleksową listę sposobów zabezpieczania bazy danych. Omówione są następujące funkcje zabezpieczeń:

- [Inspekcja wystąpienia zarządzanego SQL](auditing-configure.md)
- [Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Wykrywanie zagrożeń](threat-detection-configure.md)
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security)
- [Przezroczyste szyfrowanie danych (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Możliwości wystąpienia zarządzanego SQL

Aby zapoznać się z pełnym omówieniem możliwości wystąpienia zarządzanego SQL, zobacz:

> [!div class="nextstepaction"]
> [Możliwości wystąpienia zarządzanego SQL](sql-managed-instance-paas-overview.md)