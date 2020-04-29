---
title: Zabezpieczenia wystąpienia zarządzanego z podmiotami zabezpieczeń serwera usługi Azure AD (logowania)
description: Informacje na temat technik i funkcji umożliwiających zabezpieczenie wystąpienia zarządzanego w usłudze Azure SQL Database oraz korzystania z jednostek usługi (identyfikatorów logowania) serwera Azure AD
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: bd65a21c2aa21643c76966410931949db7d17ad6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73822790"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Samouczek: zabezpieczenia wystąpienia zarządzanego w Azure SQL Database przy użyciu podmiotów zabezpieczeń serwera usługi Azure AD (nazwy logowania)

Wystąpienie zarządzane zapewnia niemal wszystkie funkcje zabezpieczeń, które oferuje najnowszy lokalny aparat bazy danych programu SQL Server (Enterprise Edition):

- Ograniczanie dostępu w izolowanym środowisku
- Korzystanie z mechanizmów uwierzytelniania, które wymagają potwierdzenia tożsamości (uwierzytelnianie w usłudze Azure AD i uwierzytelnianie SQL)
- Stosowanie autoryzacji za pośrednictwem członkostwa i uprawnień opartych na rolach.
- Włączanie funkcji zabezpieczeń.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Tworzenie jednostki usługi (identyfikatora logowania) serwera Azure Active Directory dla wystąpień zarządzanych
> - Udzielanie uprawnień do jednostek usługi (identyfikatorów logowania) serwera Azure AD w wystąpieniach zarządzanych
> - Tworzenie użytkowników usługi Azure AD na podstawie jednostek usługi (identyfikatorów logowania) serwera Azure AD
> - Przypisywanie uprawnień do użytkowników usługi Azure AD i zarządzanie zabezpieczeniami bazy danych
> - Korzystanie z personifikacji użytkowników usługi Azure AD
> - Korzystanie z zapytań w wielu bazach danych dotyczących użytkowników usługi Azure AD
> - Informacje na temat funkcji zabezpieczeń takich jak ochrona przed zagrożeniami, inspekcja, maskowanie danych i szyfrowanie

Aby dowiedzieć się więcej, zobacz artykuł [Azure SQL Database Omówienie wystąpienia zarządzanego](sql-database-managed-instance-index.yml) i [możliwości](sql-database-managed-instance.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że dysponujesz następującymi elementami:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Wystąpienie zarządzane usługi Azure SQL Database
  - Wykonaj czynności opisane w tym artykule: [Szybki Start: tworzenie Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-get-started.md)
- Możliwość dostępu do wystąpienia zarządzanego oraz [aprowizowanego administratora usługi Azure AD dla wystąpienia zarządzanego](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Aby dowiedzieć się więcej, zobacz:
    - [Connect your application to a managed instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym) 
    - [Managed instance connectivity architecture](sql-database-managed-instance-connectivity-architecture.md) (Architektura łączności wystąpienia zarządzanego)
    - [Configure and manage Azure Active Directory authentication with SQL (Konfigurowanie uwierzytelniania w usłudze Azure Active Directory i zarządzanie nim przy użyciu języka SQL)](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Ograniczanie dostępu do wystąpienia zarządzanego

Zarządzane wystąpienia są dostępne za pomocą prywatnego adresu IP. Podobnie jak izolowane SQL Server środowiska lokalnego, aplikacje lub użytkownicy potrzebują dostępu do sieci wystąpienia zarządzanego (VNet), zanim będzie można nawiązać połączenie. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Connect your application to a managed instance](sql-database-managed-instance-connect-app.md) (Łączenie aplikacji z wystąpieniem zarządzanym).

Istnieje również możliwość skonfigurowania punktu końcowego usługi w zarządzanym wystąpieniu, która umożliwia nawiązywanie połączeń publicznych w taki sam sposób jak Azure SQL Database. Aby uzyskać więcej informacji, zobacz następujący artykuł, [Konfigurowanie publicznego punktu końcowego w Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-public-endpoint-configure.md).

> [!NOTE] 
> Nawet po włączeniu punktów końcowych usługi nie mają zastosowania [reguły zapory SQL Database](sql-database-firewall-configure.md) . Zarządzane wystąpienie ma własną [wbudowaną zaporę](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) do zarządzania łącznością.

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Tworzenie jednostki usługi (identyfikatora logowania) serwera Azure AD dla wystąpienia zarządzanego przy użyciu programu SSMS

Pierwszy podmiot zabezpieczeń serwera usługi Azure AD (login) można utworzyć przy użyciu standardowego konta SQL Server (poza usługą Azure AD) `sysadmin`, czyli administratora usługi Azure AD dla wystąpienia zarządzanego utworzonego podczas procesu aprowizacji. Aby uzyskać więcej informacji, zobacz temat [udostępnianie administratorowi Azure Active Directory dla wystąpienia zarządzanego](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Ta funkcjonalność została zmieniona od czasu, w którym znajdują się [nazwy główne serwera usługi Azure AD](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Zobacz następujące artykuły, aby zapoznać się z przykładami nawiązywania połączenia z wystąpieniem zarządzanym:

- [Szybki Start: Konfigurowanie maszyny wirtualnej platformy Azure do nawiązywania połączenia z wystąpieniem zarządzanym](sql-database-managed-instance-configure-vm.md)
- [Szybki Start: Konfigurowanie połączenia typu punkt-lokacja z wystąpieniem zarządzanym w środowisku lokalnym](sql-database-managed-instance-configure-p2s.md)

1. Zaloguj się do wystąpienia zarządzanego przy użyciu standardowego konta SQL Server (poza usługą Azure AD), które jest `sysadmin` lub administratorem usługi Azure AD dla mnie, przy użyciu [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

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

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Aby uzyskać więcej informacji, zobacz temat [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Udzielanie uprawnień w celu umożliwienia tworzenia identyfikatorów logowania wystąpienia zarządzanego

Aby utworzyć inne jednostki usługi (identyfikatory logowania) serwera Azure AD, należy udzielić uprawnień lub ról programu SQL Server jednostce usługi (SQL lub Azure AD).

### <a name="sql-authentication"></a>Uwierzytelnianie SQL

- Jeśli identyfikator logowania to podmiot zabezpieczeń SQL, tylko identyfikatory logowania, które są częścią roli `sysadmin` mogą użyć polecenia create do utworzenia identyfikatorów logowania dla konta usługi Azure AD.

### <a name="azure-ad-authentication"></a>Uwierzytelnianie w usłudze Azure AD

- Aby umożliwić nowo utworzonym jednostkom usługi (identyfikatorom logowania) serwera Azure AD tworzenie identyfikatorów logowania dla innych użytkowników, grup lub aplikacji usługi Azure AD, musisz udzielić identyfikatorowi logowania roli serwera `sysadmin` lub `securityadmin`. 
- Jednostce usługi (identyfikatorowi logowania) serwera Azure AD należy udzielić przynajmniej uprawnienia **ALTER ANY LOGIN**, aby umożliwić jej tworzenie innych jednostek usługi (identyfikatorów logowania) serwera Azure AD. 
- Domyślnie standardowe uprawnienia przyznawane nowo utworzonym podmiotom zabezpieczeń serwera usługi Azure AD (Logins) w bazie danych Master to: **Connect SQL** i **View any Database**.
- Roli serwera `sysadmin` można udzielić wielu jednostkom usługi (identyfikatorom logowania) serwera Azure AD w ramach wystąpienia zarządzanego.

Aby dodać identyfikator logowania do roli serwera `sysadmin`:

1. Zaloguj się ponownie do wystąpienia zarządzanego lub Użyj istniejącego połączenia z administratorem usługi Azure AD lub podmiotem zabezpieczeń SQL, który jest `sysadmin`.

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

1. Połącz się z wystąpieniem zarządzanym za pomocą jednostki usługi (identyfikatora logowania) serwera Azure AD, korzystając z programu SQL Server Management Studio. Wprowadź nazwę hosta wystąpienia zarządzanego. Są trzy opcje uwierzytelniania w programie SMSS podczas logowania za pomocą konta usługi Azure AD:

   - Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego
   - Active Directory — hasło
   - Active Directory — zintegrowane </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Aby uzyskać więcej informacji, zobacz następujący artykuł: [uwierzytelnianie uniwersalne z SQL Database i SQL Data Warehouse (Obsługa programu SSMS dla usługi MFA)](sql-database-ssms-mfa-authentication.md)

1. Wybierz opcję **Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego**. To spowoduje wywołanie okna logowania uwierzytelniania wieloskładnikowego. Zaloguj się przy użyciu hasła usługi Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. W **Eksploratorze obiektów** programu SSMS kliknij prawym przyciskiem myszy serwer, a następnie wybierz pozycję **Nowe zapytanie**.
1. W oknie zapytania użyj następującej składni, aby utworzyć identyfikator logowania dla kolejnego konta usługi Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    W tym przykładzie utworzono identyfikator logowania dla użytkownika usługi Azure AD bob@aadsqlmi.net, którego domena aadsqlmi.net jest sfederowana z domeną usługi Azure AD aadsqlmi.onmicrosoft.com.

    Wykonaj następujące polecenie w języku T-SQL. Federacyjne konta usługi Azure AD to zamienniki wystąpień zarządzanych dla lokalnych nazw logowania i użytkowników systemu Windows.

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

1. Utwórz identyfikator logowania wystąpienia zarządzanego dla grupy w usłudze Azure AD. Grupa musi istnieć w usłudze Azure AD przed dodaniem identyfikatora logowania do wystąpienia zarządzanego. Zobacz [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Utwórz grupę _mygroup_ i dodaj członków do tej grupy.

1. Otwórz nowe okno zapytania w programie SQL Server Management Studio.

    W tym przykładzie przyjęto założenie, że istnieje grupa o nazwie _mygroup_ w usłudze Azure AD. Wykonaj następujące polecenie:

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
> Użytkownicy-goście usługi Azure AD mają możliwość obsługi identyfikatorów logowania wystąpienia zarządzanego tylko, jeśli są dodani jako członkowie grupy usługi Azure AD. Użytkownik-gość usługi Azure AD to konto zaproszone do usługi Azure AD, do której należy wystąpienie zarządzane, z innej usługi Azure AD. Na przykład joe@contoso.com (konto usługi Azure AD) lub steve@outlook.com (konto MSA) można dodać do grupy aadsqlmi usługi Azure AD. Po dodaniu użytkowników do grupy można utworzyć nazwę logowania w bazie danych **Master** wystąpienia zarządzanego dla grupy przy użyciu SKŁADNI **CREATE LOGIN** . Użytkownicy-goście, którzy są członkami tej grupy, mogą nawiązać połączenie z wystąpieniem zarządzanym, korzystając z aktualnego identyfikatora logowania (na przykład joe@contoso.com lub steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Tworzenie użytkownika usługi Azure AD na podstawie jednostki usługi (identyfikatora logowania) serwera Azure AD oraz nadawanie uprawnień

Autoryzacja poszczególnych baz danych działa tak samo w taki sam sposób w przypadku wystąpienia zarządzanego, jak w przypadku SQL Server lokalnego. Można utworzyć użytkownika na podstawie istniejącego identyfikatora logowania w bazie danych i nadać mu uprawienia w tej bazie danych lub dodać go do roli bazy danych.

Po utworzeniu bazy danych o nazwie **MyMITestDB** oraz utworzeniu identyfikatora logowania, który ma tylko uprawnienia domyślne, utworzymy teraz użytkownika na podstawie tego identyfikatora. Obecnie identyfikator logowania może nawiązać połączenie z wystąpieniem zarządzanym i wyświetlać wszystkie bazy danych, ale nie może korzystać z tych baz danych. Jeśli zalogujesz się za pomocą konta usługi Azure AD, które ma uprawnienia domyślne, i spróbujesz rozwinąć nowo utworzoną bazę danych, zostanie wyświetlony następujący komunikat o błędzie:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

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

    W następującym przykładzie utworzono identyfikator logowania dla grupy usługi Azure AD _mygroup_, która istnieje w usłudze Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Wszyscy użytkownicy, którzy należą do grupy **mygroup**, mogą uzyskać dostęp do bazy danych **MyMITestDB**.

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

1. Sprawdź, czy użytkownik usługi Azure AD utworzony w bazie danych istnieje, wykonując następujące polecenie:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Utwórz nowe połączenie z wystąpieniem zarządzanym z użytkownikiem, który został dodany do roli `db_datareader`.
1. Rozwiń bazę danych w **Eksploratorze obiektów**, aby wyświetlić tabelę.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Otwórz nowe okno zapytania i wykonaj następującą instrukcję SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Czy jesteś w stanie wyświetlić dane w tabeli? Kolumny powinny zostać zwrócone.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Personifikacja podmiotów zabezpieczeń na poziomie serwera usługi Azure AD (identyfikatorów logowania)

Wystąpienie zarządzane obsługuje personifikację podmiotów zabezpieczeń na poziomie serwera usługi Azure AD (identyfikatorów logowania).

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

1. Użyj poniższego polecenia, aby zobaczyć, że użytkownik jest personifikowany podczas wykonywania procedury składowanej, **robert\@aadsqlmi.NET**.

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
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>Korzystanie z zapytań w wielu bazach danych w wystąpieniach zarządzanych

Funkcja wykonywania zapytań w wielu bazach danych jest obsługiwana dla kont usługi Azure AD z jednostkami usługi (identyfikatorami logowania) serwera Azure AD. Aby przetestować zapytania w wielu bazach danych w grupie usługi Azure AD, musimy utworzyć kolejną bazę danych i tabelę. Możesz pominąć krok tworzenia kolejnej bazy danych i tabeli, jeśli już istnieją.

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

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins"></a>Dodatkowe scenariusze obsługiwane przez podmioty zabezpieczeń serwera usługi Azure AD (nazwy logowania)

- Dla jednostek usługi (identyfikatorów logowania) serwera Azure AD jest obsługiwane zarządzanie agentem SQL oraz wykonywanie zadań.
- Operacje tworzenia kopii zapasowej oraz przywracania bazy danych mogą być wykonywane przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- [Inspekcja](sql-database-managed-instance-auditing.md) wszystkich instrukcji związanych z jednostkami usługi (identyfikatorami logowania) serwera Azure AD oraz zdarzeniami uwierzytelniania.
- Dedykowane połączenie administratora dla jednostek usługi (identyfikatorów logowania) serwera Azure AD, które są członkami roli serwera `sysadmin`.
- Jednostki usługi (identyfikatory logowania) serwera Azure AD są obsługiwane przy użyciu narzędzi [sqlcmd](/sql/tools/sqlcmd-utility) oraz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Wyzwalacze logowania są obsługiwane dla zdarzeń logowania przychodzących z jednostek usługi (identyfikatorów logowania) serwera Azure AD.
- Przy użyciu jednostek usługi (identyfikatorów logowania) serwera Azure AD można skonfigurować usługi Service Broker i DBMail.


## <a name="next-steps"></a>Następne kroki

### <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Szczegółowe listę sposobów zabezpieczania bazy danych można znaleźć w artykule o następujących [funkcjach dotyczących możliwości wystąpienia zarządzanego](sql-database-managed-instance.md#azure-sql-database-security-features) . Omówione są następujące funkcje zabezpieczeń:

- [Inspekcja wystąpienia zarządzanego](sql-database-managed-instance-auditing.md) 
- [Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) 
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) 
- [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funkcje wystąpienia zarządzanego

Aby zapoznać się z pełnym omówieniem funkcji wystąpienia zarządzanego, zobacz:

> [!div class="nextstepaction"]
> [Funkcje wystąpienia zarządzanego](sql-database-managed-instance.md)
