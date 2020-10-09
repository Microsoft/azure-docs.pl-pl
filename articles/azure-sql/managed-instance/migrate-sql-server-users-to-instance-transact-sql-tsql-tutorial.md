---
title: Migrowanie SQL Server użytkowników i grup systemu Windows do wystąpienia zarządzanego SQL przy użyciu języka T-SQL
description: Informacje na temat migracji użytkowników i grup systemu Windows w wystąpieniu SQL Server do wystąpienia zarządzanego usługi Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84708683"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Samouczek: Migrowanie użytkowników i grup systemu Windows w wystąpieniu SQL Server do wystąpienia zarządzanego usługi Azure SQL przy użyciu składni języka T-SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> Składnia służąca do migrowania użytkowników i grup do wystąpienia zarządzanego SQL w tym artykule jest w **publicznej wersji zapoznawczej**.

Ten artykuł przeprowadzi Cię przez proces migrowania lokalnych użytkowników i grup systemu Windows w SQL Server do wystąpienia zarządzanego usługi Azure SQL przy użyciu składni języka T-SQL.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> - Utwórz identyfikatory logowania dla SQL Server
> - Tworzenie testowej bazy danych na potrzeby migracji
> - Tworzenie nazw logowania, użytkowników i ról
> - Tworzenie kopii zapasowej i przywracanie bazy danych do wystąpienia zarządzanego SQL (MI)
> - Ręczne Migrowanie użytkowników do mnie przy użyciu instrukcji ALTER USER Syntax
> - Testowanie uwierzytelniania przy użyciu nowych zamapowanych użytkowników

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, obowiązują następujące wymagania wstępne:

- Domena systemu Windows jest federacyjny z Azure Active Directory (Azure AD).
- Dostęp do Active Directory w celu tworzenia użytkowników/grup.
- Istniejące SQL Server w środowisku lokalnym.
- Istniejące wystąpienie zarządzane SQL. Zobacz [Szybki Start: Tworzenie wystąpienia zarządzanego SQL](instance-create-quickstart.md).
  - `sysadmin`W wystąpieniu zarządzanym SQL należy użyć programu w celu utworzenia nazw logowania usługi Azure AD.
- [Utwórz administratora usługi Azure AD dla wystąpienia zarządzanego SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- Możesz połączyć się z wystąpieniem zarządzanym SQL w sieci. Dodatkowe informacje można znaleźć w następujących artykułach:
  - [Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL](connect-application-instance.md)
  - [Szybki Start: Konfigurowanie połączenia punkt-lokacja z wystąpieniem zarządzanym usługi Azure SQL z lokalnego](point-to-site-p2s-configure.md)
  - [Skonfiguruj publiczny punkt końcowy w wystąpieniu zarządzanym Azure SQL](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Składnia DDL języka T-SQL

Poniżej przedstawiono składnię DDL języka T-SQL służącą do obsługi migracji użytkowników i grup systemu Windows z wystąpienia SQL Server do wystąpienia zarządzanego SQL z uwierzytelnianiem za pomocą usługi Azure AD.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Argumenty

_domainName_</br>
Określa nazwę domeny użytkownika.

_Uż_</br>
Określa nazwę użytkownika zidentyfikowaną w bazie danych.

_= loginName \@ domainName.com_</br>
Ponownie mapuje użytkownika na nazwę logowania usługi Azure AD

_groupName_</br>
Określa nazwę grupy identyfikowanej w bazie danych.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>Część 1. Tworzenie nazw logowania w SQL Server dla użytkowników i grup systemu Windows

> [!IMPORTANT]
> Następująca składnia tworzy użytkownika i logowanie do grupy w SQL Server. Przed wykonaniem poniższej składni należy upewnić się, że użytkownik i Grupa znajdują się wewnątrz Active Directory (AD). </br> </br>
> Użytkownicy: testUser1, testGroupUser </br>
> Grupa: migracja-testGroupUser musi należeć do grupy migracji w usłudze AD

Poniższy przykład tworzy logowanie w SQL Server dla konta o nazwie _testUser1_ w domenie _aadsqlmi_.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

Utwórz bazę danych dla tego testu.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Część 2. tworzenie użytkowników i grup systemu Windows, a następnie Dodawanie ról i uprawnień

Aby utworzyć użytkownika testowego, należy użyć następującej składni.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

Sprawdź uprawnienia użytkownika:

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

Utwórz rolę i przypisz do niej użytkownika testowego:

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

Użyj następującego zapytania, aby wyświetlić nazwy użytkowników przypisane do określonej roli:

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

Aby utworzyć grupę, należy użyć następującej składni. Następnie Dodaj grupę do roli `db_owner` .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

Utwórz tabelę testową i Dodaj dane przy użyciu następującej składni:

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>Część 3: wykonywanie kopii zapasowej i przywracanie bazy danych poszczególnych użytkowników do wystąpienia zarządzanego SQL

Utwórz kopię zapasową bazy danych migracji przy użyciu artykułu [Kopiowanie baz danych z kopii zapasowej i przywracania](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)albo użyj następującej składni:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

Skorzystaj z naszego [przewodnika Szybki Start: Przywracanie bazy danych do wystąpienia zarządzanego SQL](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>Część 4: Migrowanie użytkowników do wystąpienia zarządzanego SQL

Wykonaj polecenie ALTER USER, aby zakończyć proces migracji w wystąpieniu zarządzanym SQL.

1. Zaloguj się do wystąpienia zarządzanego SQL przy użyciu konta administratora usługi Azure AD dla wystąpienia zarządzanego SQL. Następnie utwórz dane logowania usługi Azure AD w wystąpieniu zarządzanym SQL przy użyciu następującej składni. Aby uzyskać więcej informacji, zobacz [Samouczek: zabezpieczenia wystąpienia zarządzanego SQL w Azure SQL Database przy użyciu głównych nazw serwera usługi Azure AD](aad-security-configure-tutorial.md).

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. Sprawdź, czy migracja dotyczy poprawnej bazy danych, tabeli i podmiotów zabezpieczeń.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. Użyj składni ALTER USER, aby zamapować użytkownika lokalnego na nazwę logowania usługi Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. Za pomocą polecenia ALTER USER Syntax Mapuj grupę lokalną na nazwę logowania usługi Azure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Część 5: testowanie uwierzytelniania użytkowników lub grup usługi Azure AD

Przetestuj uwierzytelnianie w wystąpieniu zarządzanym SQL, używając wcześniej zamapowanego użytkownika na identyfikator logowania usługi Azure AD za pomocą polecenia ALTER USER Syntax.

1. Zaloguj się do federacyjnej maszyny wirtualnej przy użyciu subskrypcji wystąpienia zarządzanego usługi Azure SQL jako `aadsqlmi\testUser1`
1. Korzystając z SQL Server Management Studio (SSMS), zaloguj się do wystąpienia zarządzanego SQL przy użyciu uwierzytelniania **zintegrowanego Active Directory** , łącząc się z bazą danych `migration` .
    1. Możesz również zalogować się przy użyciu testUser1@aadsqlmi.net poświadczeń z opcją SSMS **Active Directory — uniwersalna z obsługą usługi MFA**. Jednak w tym przypadku nie można używać mechanizmu logowania jednokrotnego i należy wpisać hasło. Nie trzeba używać federacyjnej maszyny wirtualnej do logowania się do wystąpienia zarządzanego SQL.
1. W ramach **wyboru**elementu członkowskiego roli można wybrać jedną z tabeli. `test`

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Testowanie uwierzytelniania w wystąpieniu zarządzanym SQL przy użyciu elementu członkowskiego grupy systemu Windows `migration` . Użytkownik `aadsqlmi\testGroupUser` powinien zostać dodany do grupy `migration` przed migracją.

1. Zaloguj się do federacyjnej maszyny wirtualnej przy użyciu subskrypcji wystąpienia zarządzanego usługi Azure SQL jako `aadsqlmi\testGroupUser`
1. Za pomocą programu SSMS z Active Directory uwierzytelnianiem **zintegrowanym** Nawiąż połączenie z serwerem wystąpienia zarządzanego Azure SQL i bazą danych `migration`
    1. Możesz również zalogować się przy użyciu testGroupUser@aadsqlmi.net poświadczeń z opcją SSMS **Active Directory — uniwersalna z obsługą usługi MFA**. Jednak w tym przypadku nie można używać mechanizmu logowania jednokrotnego i należy wpisać hasło. Do zalogowania się do wystąpienia zarządzanego SQL nie trzeba używać federacyjnej maszyny wirtualnej.
1. W ramach `db_owner` roli można utworzyć nową tabelę.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> Ze względu na znany problem z projektem dla Azure SQL Database, instrukcja CREATE a Table wykonywana jako członek grupy zakończy się niepowodzeniem z powodu następującego błędu: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> Bieżące obejście polega na utworzeniu tabeli z istniejącym schematem w przypadku powyżej <dbo. New>

## <a name="next-steps"></a>Następne kroki

[Samouczek: Migrowanie SQL Server do wystąpienia zarządzanego usługi Azure SQL w trybie offline za pomocą usługi DMS](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
