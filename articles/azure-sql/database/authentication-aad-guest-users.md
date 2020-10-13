---
title: Tworzenie użytkowników Gości usługi Azure AD
description: Jak utworzyć użytkowników Gości usługi Azure AD i ustawić ich jako administratora usługi Azure AD bez używania grup usługi Azure AD w Azure SQL Database, wystąpieniu zarządzanym usługi Azure SQL i analizie Synapse Azure
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: aa74489a962708a1d3d5e6835f684e5cb8fc548b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444343"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Tworzenie użytkowników-gości usługi Azure AD i ustawianie jako administratora usługi Azure AD

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Ten artykuł jest w **publicznej wersji zapoznawczej**.

Użytkownicy-Goście w Azure Active Directory (Azure AD) są użytkownikami zaimportowanymi do bieżącej usługi Azure AD z innych aktywnych katalogów platformy Azure lub poza nią. Na przykład użytkownicy-Goście mogą dołączać użytkowników z innych katalogów usługi Azure Active Directory lub z kont takich jak * \@ Outlook.com*, * \@ hotmail.com*, * \@ Live.com*lub * \@ gmail.com*. W tym artykule pokazano, jak utworzyć użytkownika-gościa usługi Azure AD i ustawić tego użytkownika jako administratora usługi Azure AD dla serwera logicznego usługi Azure SQL, bez konieczności posiadania tego użytkownika gościa do grupy w usłudze Azure AD.

## <a name="feature-description"></a>Opis funkcji

Ta funkcja wypróbuje bieżące ograniczenie, które zezwala użytkownikom-Gościom na łączenie się z Azure SQL Database, wystąpieniem zarządzanym SQL lub usługą Azure Synapse Analytics, gdy są one członkami grupy utworzonej w usłudze Azure AD. Grupa wymagana do ręcznego mapowania na użytkownika przy użyciu instrukcji [Create User (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) w danej bazie danych. Po utworzeniu użytkownika bazy danych dla grupy usługi Azure AD zawierającego użytkownika-gościa użytkownik może zalogować się do bazy danych przy użyciu Azure Active Directory z uwierzytelnianiem MFA. W ramach tej **publicznej wersji zapoznawczej**można tworzyć użytkowników-Gości i łączyć się bezpośrednio z SQL Database, wystąpienia zarządzanego SQL lub Azure Synapse bez konieczności dodawania ich do grupy usługi Azure AD, a następnie tworzenia użytkownika bazy danych dla tej grupy usługi Azure AD.

W ramach tej funkcji można także ustawić użytkownika Gość usługi Azure AD bezpośrednio jako administratora usługi AD dla serwera logicznego usługi Azure SQL. Istniejące funkcje, w których użytkownik Gość może należeć do grupy usługi Azure AD, a następnie można ustawić tę grupę, ponieważ nie ma to wpływu na administratora usługi Azure AD dla serwera logicznego usługi Azure SQL. Ta zmiana nie ma wpływu na użytkowników-Gości w bazie danych będącej częścią grupy usługi Azure AD.

Aby uzyskać więcej informacji na temat istniejącej obsługi dla użytkowników-Gości korzystających z grup usługi Azure AD, zobacz [używanie uwierzytelniania wieloskładnikowego Azure Active Directory](authentication-mfa-ssms-overview.md).

## <a name="prerequisite"></a>Wymaganie wstępne

- W przypadku korzystania z programu PowerShell w celu ustawienia użytkownika-gościa jako administratora usługi Azure AD na serwerze logicznym Azure SQL Server jest wymagany moduł [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) lub nowszy.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Utwórz użytkownika bazy danych dla użytkownika-gościa usługi Azure AD 

Wykonaj następujące kroki, aby utworzyć użytkownika bazy danych przy użyciu użytkownika-gościa usługi Azure AD.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Tworzenie użytkownika-gościa w SQL Database i Azure Synapse

1. Upewnij się, że użytkownik-Gość (na przykład `user1@gmail.com` ) został już dodany do usługi Azure AD, a administrator usługi Azure AD został ustawiony dla serwera bazy danych. Aby uwierzytelnianie Azure Active Directory było wymagane, należy mieć administratora usługi Azure AD.

1. Nawiąż połączenie z bazą danych SQL jako administratorem usługi Azure AD lub użytkownikiem usługi Azure AD z odpowiednimi uprawnieniami SQL, aby utworzyć użytkowników, i uruchom poniższe polecenie w bazie danych, w której należy dodać użytkownika-gościa:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Powinien być teraz utworzony użytkownik bazy danych dla użytkownika-gościa `user1@gmail.com` .

1. Uruchom poniższe polecenie, aby sprawdzić, czy użytkownik bazy danych został pomyślnie utworzony:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Odłączanie i logowanie się do bazy danych jako użytkownik `user1@gmail.com` -gość przy użyciu [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) przy użyciu metody uwierzytelniania **Azure Active Directory — uniwersalna za**pomocą usługi MFA. Aby uzyskać więcej informacji, zobacz [Korzystanie z uwierzytelniania wieloskładnikowego Azure Active Directory](authentication-mfa-ssms-overview.md).

### <a name="create-guest-user-in-sql-managed-instance"></a>Utwórz użytkownika-gościa w wystąpieniu zarządzanym SQL

> [!NOTE]
> Wystąpienie zarządzane SQL obsługuje logowania użytkowników usługi Azure AD, a także użytkowników zawartej bazy danych usługi Azure AD. Poniższe kroki pokazują, jak utworzyć identyfikator logowania i użytkownika dla użytkownika-gościa usługi Azure AD w wystąpieniu zarządzanym SQL. Możesz również utworzyć [użytkownika zawartej bazy danych](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) w wystąpieniu zarządzanym SQL przy użyciu metody z sekcji [Tworzenie użytkownika-gościa w SQL Database i Azure Synapse](#create-guest-user-in-sql-database-and-azure-synapse) .

1. Upewnij się, że użytkownik-Gość (na przykład `user1@gmail.com` ) został już dodany do usługi Azure AD, a dla serwera wystąpienia zarządzanego SQL został ustawiony administrator usługi Azure AD. Aby uwierzytelnianie Azure Active Directory było wymagane, należy mieć administratora usługi Azure AD.

1. Połącz się z serwerem wystąpienia zarządzanego SQL jako administrator usługi Azure AD lub użytkownikiem usługi Azure AD z odpowiednimi uprawnieniami SQL do tworzenia użytkowników, a następnie uruchom następujące polecenie w `master` bazie danych, aby utworzyć identyfikator logowania dla użytkownika-gościa:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Powinna być teraz utworzona nazwa logowania dla użytkownika-gościa `user1@gmail.com` w `master` bazie danych.

1. Uruchom poniższe polecenie, aby sprawdzić, czy logowanie zostało pomyślnie utworzone:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Uruchom poniższe polecenie w bazie danych, w której należy dodać użytkownika-gościa: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Powinien być teraz utworzony użytkownik bazy danych dla użytkownika-gościa `user1@gmail.com` .

1. Odłączanie i logowanie się do bazy danych jako użytkownik `user1@gmail.com` -gość przy użyciu [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) przy użyciu metody uwierzytelniania **Azure Active Directory — uniwersalna za**pomocą usługi MFA. Aby uzyskać więcej informacji, zobacz [Korzystanie z uwierzytelniania wieloskładnikowego Azure Active Directory](authentication-mfa-ssms-overview.md).

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Ustawianie użytkownika gościa jako administratora usługi Azure AD

Wykonaj następujące kroki, aby ustawić użytkownika-gościa usługi Azure AD jako administratora usługi Azure AD dla serwera logicznego SQL.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Ustawianie administratora usługi Azure AD dla SQL Database i usługi Azure Synapse

1. Upewnij się, że użytkownik-Gość (na przykład `user1@gmail.com` ) został już dodany do usługi Azure AD.

1. Uruchom następujące polecenie programu PowerShell, aby dodać użytkownika-gościa jako administratora usługi Azure AD dla serwera logicznego usługi Azure SQL:

    - Zamień `<ResourceGroupName>` na nazwę grupy zasobów platformy Azure, która zawiera serwer logiczny usługi Azure SQL.
    - Zamień `<ServerName>` na nazwę serwera logicznego usługi Azure SQL. Jeśli nazwa serwera to `myserver.database.windows.net` , Zastąp `<Server Name>` ciąg `myserver` .
    - Zamień `<DisplayNameOfGuestUser>` na nazwę użytkownika gościa.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    Możesz również użyć polecenia, aby skonfigurować użytkownika [-](https://docs.microsoft.com/cli/azure/sql/server/ad-admin) gościa jako administratora usługi Azure AD dla serwera logicznego usługi Azure SQL Server jako administrator usługi Active Directory Azure.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Ustaw administratora usługi Azure AD dla wystąpienia zarządzanego SQL

1. Upewnij się, że użytkownik-Gość (na przykład `user1@gmail.com` ) został już dodany do usługi Azure AD.

1. Przejdź do [Azure Portal](https://portal.azure.com)i przejdź do zasobu **Azure Active Directory** . W obszarze **Zarządzaj**przejdź do okienka **Użytkownicy** . Wybierz użytkownika-gościa i Zapisz `Object ID` . 

1. Uruchom następujące polecenie programu PowerShell, aby dodać użytkownika-gościa jako administratora usługi Azure AD dla wystąpienia zarządzanego SQL:

    - Zamień `<ResourceGroupName>` na nazwę grupy zasobów platformy Azure zawierającą wystąpienie zarządzane SQL.
    - Zamień `<ManagedInstanceName>` na nazwę wystąpienia zarządzanego SQL.
    - Zamień `<DisplayNameOfGuestUser>` na nazwę użytkownika gościa.
    - Zamień na `<AADObjectIDOfGuestUser>` `Object ID` zebrane wcześniej.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    Można również użyć polecenia "CLI" platformy Azure [AZ SQL mi AD-administrator](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin) , aby ustawić użytkownika-gościa jako administratora usługi Azure AD dla wystąpienia zarządzanego SQL.

## <a name="limitations"></a>Ograniczenia

Istnieje ograniczenie dotyczące Azure Portal, które uniemożliwiają wybranie użytkownika Gość usługi Azure AD jako administratora usługi Azure AD dla wystąpienia zarządzanego SQL. W przypadku kont Gości spoza usługi Azure AD, takich jak * \@ Outlook.com*, * \@ hotmail.com*, * \@ Live.com*lub * \@ gmail.com*, selektor administratora usługi AD zawiera te konta, ale są wyszarzone i nie można ich wybrać. Użyj powyższej listy [poleceń programu PowerShell lub interfejsu wiersza polecenia](#setting-a-guest-user-as-an-azure-ad-admin) , aby ustawić administratora usługi Azure AD. Alternatywnie można ustawić grupę usługi Azure AD zawierającą użytkownika Gość jako administratora usługi Azure AD dla wystąpienia zarządzanego SQL.

Ta funkcja zostanie włączona dla wystąpienia zarządzanego SQL przed ogólnym udostępnieniem tej funkcji.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie uwierzytelniania usługi Azure AD i zarządzanie nim za pomocą usługi Azure SQL](authentication-aad-configure.md)
- [Korzystanie z uwierzytelniania wieloskładnikowego Azure Active Directory](authentication-mfa-ssms-overview.md)
- [CREATE USER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
