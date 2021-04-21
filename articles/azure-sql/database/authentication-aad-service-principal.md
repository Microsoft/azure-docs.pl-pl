---
title: Azure Active Directory jednostki usługi za pomocą Azure SQL
description: Aplikacje usługi Azure AD (jednostki usługi) obsługują tworzenie użytkowników usługi Azure AD w Azure SQL Database, Azure SQL Managed Instance i Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 17e846c7435e2f1cc77c5915f7e0b308c3706f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775425"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory jednostki usługi za pomocą Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Obsługa tworzenia użytkowników usługi Azure Active Directory (Azure AD) w usługach Azure SQL Database (SQL DB) i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) w imieniu aplikacji usługi Azure AD (jednostki usługi) jest obecnie dostępna w publicznej wersji **zapoznawczej.**

> [!NOTE]
> Ta funkcja jest już obsługiwana w SQL Managed Instance.

## <a name="service-principal-azure-ad-applications-support"></a>Obsługa jednostki usługi (aplikacji usługi Azure AD)

Ten artykuł dotyczy aplikacji, które są zintegrowane z usługą Azure AD i są częścią rejestracji w usłudze Azure AD. Te aplikacje często potrzebują dostępu do uwierzytelniania i autoryzacji Azure SQL do wykonywania różnych zadań. Ta funkcja w **publicznej wersji zapoznawczej** umożliwia teraz jednostkom usługi tworzenie użytkowników usługi Azure AD w SQL Database i Azure Synapse. Istnieje ograniczenie uniemożliwiające utworzenie obiektu usługi Azure AD w imieniu usuniętych aplikacji usługi Azure AD.

Gdy aplikacja usługi Azure AD jest zarejestrowana przy użyciu Azure Portal lub polecenia programu PowerShell, w dzierżawie usługi Azure AD są tworzone dwa obiekty:

- Obiekt aplikacji
- Obiekt jednostki usługi

Aby uzyskać więcej informacji na temat aplikacji usługi Azure AD, zobacz Application and service principal objects in Azure Active Directory (Obiekty aplikacji i jednostki usługi w usłudze [Azure Active Directory)](../../active-directory/develop/app-objects-and-service-principals.md) i Create an Azure service principal with Azure PowerShell (Tworzenie jednostki usługi [platformy Azure za pomocą Azure PowerShell).](/powershell/azure/create-azure-service-principal-azureps)

SQL Database, Azure Synapse i SQL Managed Instance obsługują następujące obiekty usługi Azure AD:

- Użytkownicy usługi Azure AD (zarządzani, federowani i gość)
- Grupy usługi Azure AD (zarządzane i federowane)
- Aplikacje usługi Azure AD 

Polecenie języka T-SQL w imieniu aplikacji usługi Azure AD jest teraz obsługiwane na SQL Database `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` i Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funkcjonalność tworzenia użytkowników usługi Azure AD przy użyciu jednostki usługi

Obsługa tej funkcji jest przydatna w procesach automatyzacji aplikacji usługi Azure AD, w których obiekty usługi Azure AD są tworzone i utrzymywane w SQL Database i Azure Synapse bez interakcji z użytkownikami. Jednostkami usługi mogą być administratorzy usługi Azure AD dla serwera logicznego SQL w ramach grupy lub pojedynczego użytkownika. Aplikacja może zautomatyzować tworzenie obiektów usługi Azure AD w usługach SQL Database i Azure Synapse wykonywanych jako administrator systemu i nie wymaga żadnych dodatkowych uprawnień SQL. Umożliwia to pełną automatyzację tworzenia użytkownika bazy danych. Ta funkcja jest również obsługiwana w przypadku tożsamości zarządzanej przypisanej przez system i tożsamości zarządzanej przypisanej przez użytkownika. Aby uzyskać więcej informacji, [zobacz Co to są tożsamości zarządzane dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Włączanie tworzenia użytkowników usługi Azure AD przez jednostki usługi

Aby włączyć tworzenie obiektów usługi Azure AD w SQL Database i Azure Synapse w imieniu aplikacji usługi Azure AD, wymagane są następujące ustawienia:

1. Przypisz tożsamość serwera. Przypisana tożsamość serwera reprezentuje tożsamość usługi zarządzanej (MSI). Obecnie tożsamość serwera dla usługi Azure SQL nie obsługuje tożsamości zarządzanej przez użytkownika (UMI).
    - W przypadku nowego Azure SQL logicznego wykonaj następujące polecenie programu PowerShell:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Aby uzyskać więcej informacji, zobacz [polecenie New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver)

    - W przypadku Azure SQL serwerów logicznych wykonaj następujące polecenie:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Aby uzyskać więcej informacji, zobacz [polecenie Set-AzSqlServer.](/powershell/module/az.sql/set-azsqlserver)

    - Aby sprawdzić, czy tożsamość serwera jest przypisana do serwera, wykonaj Get-AzSqlServer polecenia.

    > [!NOTE]
    > Tożsamość serwera można również przypisać przy użyciu poleceń interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [az sql server create i](/cli/azure/sql/server#az_sql_server_create) az sql server [update](/cli/azure/sql/server#az_sql_server_update).

2. Przyznaj czytelnikom usługi Azure AD [**Directory**](../../active-directory/roles/permissions-reference.md#directory-readers) uprawnienie do tożsamości serwera utworzonej lub przypisanej do serwera.
    - Aby udzielić tego uprawnienia, postępuj zgodnie z opisem SQL Managed Instance dostępnym w następującym artykule: Aprowizowanie administratora [usługi Azure AD (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Użytkownik usługi Azure AD, który przyznaje to uprawnienie, musi być częścią roli **administratora** globalnego usługi Azure AD lub administratora **ról uprzywilejowanych.**

> [!IMPORTANT]
> Kroki 1 i 2 muszą być wykonywane w powyższej kolejności. Najpierw utwórz lub przypisz tożsamość serwera, a następnie przyznaj uprawnienie [**Czytelnicy**](../../active-directory/roles/permissions-reference.md#directory-readers) katalogów. Pominięcie jednego z tych kroków lub obu tych czynności spowoduje błąd wykonywania podczas tworzenia obiektu usługi Azure AD Azure SQL w imieniu aplikacji usługi Azure AD.
>
> Jeśli używasz jednostki usługi do ustawienia lub cofenia ustawienia administratora usługi Azure AD, aplikacja musi mieć również uprawnienie [Directory.Read.All](/graph/permissions-reference#application-permissions-18) interfejsu API aplikacji w usłudze Azure AD. Aby uzyskać więcej informacji na temat uprawnień wymaganych do ustawienia administratora usługi [Azure AD](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)i instrukcje krok po kroku dotyczące tworzenia użytkownika usługi Azure AD w imieniu aplikacji usługi Azure AD, zobacz [Samouczek:](authentication-aad-service-principal-tutorial.md)tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD.
>
> W **publicznej wersji zapoznawczej** możesz przypisać rolę **Czytelnicy katalogów** do grupy w usłudze Azure AD. Właściciele grupy mogą następnie dodać tożsamość zarządzaną jako członka tej grupy,  co  pozwala pominąć konieczność udzielania roli Czytelnik katalogu przez administratora globalnego lub administratora ról **uprzywilejowanych.** Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Rolę Czytelnicy](authentication-aad-directory-readers-role.md)katalogów w Azure Active Directory for Azure SQL .

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Rozwiązywanie problemów i ograniczenia dotyczące publicznej wersji zapoznawczej

- Podczas tworzenia obiektów usługi Azure AD w Azure SQL w imieniu aplikacji usługi  Azure AD bez włączania tożsamości serwera i udzielania uprawnień czytelnikom katalogów operacja nie powiedzie się z następującymi możliwymi błędami. Przykładowy błąd poniżej dotyczy wykonywania polecenia programu PowerShell w celu utworzenia użytkownika aplikacji SQL Database artykule Samouczek: tworzenie użytkowników usługi Azure AD przy użyciu aplikacji `myapp` [usługi Azure AD.](authentication-aad-service-principal-tutorial.md)
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - W przypadku powyższego błędu wykonaj kroki przypisywania tożsamości do serwera [logicznego usługi Azure SQL](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) i przypisywania czytelnikom katalogów uprawnienia do tożsamości serwera logicznego [SQL.](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity)
    > [!NOTE]
    > Wskazane powyżej komunikaty o błędach zostaną zmienione przed publicznie dostępną funkcją, aby wyraźnie zidentyfikować brakujące wymagania konfiguracji dotyczące obsługi aplikacji usługi Azure AD.
- Ustawienie aplikacji usługi Azure AD jako administratora usługi Azure AD dla usługi SQL Managed Instance jest obsługiwane tylko przy użyciu polecenia interfejsu wiersza polecenia, a polecenie programu PowerShell z programem [Az.Sql w wersji 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) lub wyższej. Aby uzyskać więcej informacji, zobacz [polecenia az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) i [Set-AzSqlInstanceActiveDirectoryAdministrator.](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) 
    - Jeśli chcesz użyć narzędzia Azure Portal for SQL Managed Instance do ustawienia administratora usługi Azure AD, możliwym obejściem jest utworzenie grupy usługi Azure AD. Następnie dodaj jednostkę usługi (aplikację usługi Azure AD) do tej grupy i ustaw tę grupę jako administratora usługi Azure AD dla SQL Managed Instance.
    - Ustawianie jednostki usługi (aplikacji usługi Azure AD) jako administratora usługi Azure AD dla usług SQL Database i Azure Synapse jest obsługiwane przy użyciu poleceń Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)i [cli.](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse)
- Używanie aplikacji usługi Azure AD z jednostką usługi z innej dzierżawy usługi Azure AD nie powiedzie się podczas uzyskiwania SQL Database lub SQL Managed Instance utworzonej w innej dzierżawie. Nazwa główna usługi przypisana do tej aplikacji musi pochodzić z tej samej dzierżawy co serwer logiczny SQL lub wystąpienie zarządzane.
- [Moduł Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) lub wyższy jest wymagany w przypadku używania programu PowerShell do skonfigurowania poszczególnych aplikacji usługi Azure AD jako administrator usługi Azure AD dla Azure SQL. Upewnij się, że uaktualniono do najnowszego modułu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](authentication-aad-service-principal-tutorial.md)
