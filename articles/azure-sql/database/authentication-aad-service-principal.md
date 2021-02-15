---
title: Azure Active Directory jednostki usługi przy użyciu usługi Azure SQL
description: Aplikacje usługi Azure AD (jednostki usługi) obsługują tworzenie użytkowników w usłudze Azure AD w Azure SQL Database, wystąpieniu zarządzanym SQL Azure i analizą usługi Azure Synapse
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 4012cd83cf2e6fe438792a503731729b57a1425c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380597"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory jednostki usługi przy użyciu usługi Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Obsługa tworzenia użytkowników w usłudze Azure Active Directory (Azure AD) w Azure SQL Database (SQL DB) i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) w imieniu aplikacji usługi Azure AD (jednostki usługi) jest obecnie dostępna w **publicznej wersji zapoznawczej**.

> [!NOTE]
> Ta funkcja jest już obsługiwana w przypadku wystąpienia zarządzanego SQL.

## <a name="service-principal-azure-ad-applications-support"></a>Obsługa nazwy głównej usługi (aplikacje usługi Azure AD)

Ten artykuł ma zastosowanie do aplikacji zintegrowanych z usługą Azure AD, które są częścią rejestracji w usłudze Azure AD. Te aplikacje często wymagają uwierzytelniania i dostępu autoryzacji do usługi Azure SQL w celu wykonywania różnych zadań. Ta funkcja w **publicznej wersji zapoznawczej** umożliwia teraz podmiotom usługi tworzenie użytkowników usługi Azure AD w SQL Database i Azure Synapse. Przekroczono ograniczenie uniemożliwiające Tworzenie obiektów usługi Azure AD w imieniu aplikacji usługi Azure AD, które zostały usunięte.

Gdy aplikacja usługi Azure AD jest zarejestrowana przy użyciu Azure Portal lub polecenia programu PowerShell, w dzierżawie usługi Azure AD tworzone są dwa obiekty:

- Obiekt aplikacji
- Obiekt jednostki usługi

Aby uzyskać więcej informacji na temat aplikacji usługi Azure AD, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) i [Utwórz jednostkę usługi platformy Azure z Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

SQL Database, Azure Synapse i wystąpienie zarządzane SQL obsługują następujące obiekty usługi Azure AD:

- Użytkownicy usługi Azure AD (zarządzani, federacyjny i gość)
- Grupy usługi Azure AD (zarządzane i federacyjne)
- Aplikacje usługi Azure AD 

Polecenie T-SQL `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` w imieniu aplikacji usługi Azure AD jest teraz obsługiwane dla SQL Database i usługi Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funkcja tworzenia użytkowników usługi Azure AD przy użyciu jednostek usługi

Obsługa tej funkcji jest przydatna w procesach automatyzacji aplikacji usługi Azure AD, w których obiekty usługi Azure AD są tworzone i utrzymywane w SQL Database i na platformie Azure Synapse bez interakcji z człowiekiem. Jednostki usługi mogą być administratorem usługi Azure AD dla serwera logicznego SQL jako częścią grupy lub pojedynczego użytkownika. Aplikacja umożliwia automatyzację tworzenia obiektów usługi Azure AD w SQL Database i Azure Synapse podczas wykonywania jako administrator systemu i nie wymaga żadnych dodatkowych uprawnień SQL. Pozwala to na pełną automatyzację tworzenia użytkowników bazy danych. Ta funkcja jest również obsługiwana w przypadku tożsamości zarządzanej przypisanej do systemu i tożsamości zarządzanej przypisanej przez użytkownika. Aby uzyskać więcej informacji, zobacz [co to są zarządzane tożsamości dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Włącz jednostki usługi do tworzenia użytkowników usługi Azure AD

Aby umożliwić tworzenie obiektów usługi Azure AD w SQL Database i Azure Synapse w imieniu aplikacji usługi Azure AD, wymagane są następujące ustawienia:

1. Przypisz tożsamość serwera. Przypisana tożsamość serwera reprezentuje tożsamość systemu zarządzanego (MSI). Obecnie tożsamość serwera usługi Azure SQL nie obsługuje tożsamości zarządzanej przez użytkownika (UMI).
    - Dla nowego serwera logicznego SQL platformy Azure wykonaj następujące polecenie programu PowerShell:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Aby uzyskać więcej informacji, zobacz polecenie [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) .

    - W przypadku istniejących serwerów logicznych usługi Azure SQL wykonaj następujące polecenie:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Aby uzyskać więcej informacji, zobacz polecenie [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) .

    - Aby sprawdzić, czy tożsamość serwera jest przypisana do serwera, uruchom polecenie Get-AzSqlServer.

    > [!NOTE]
    > Tożsamość serwera można przypisać również przy użyciu poleceń interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [AZ SQL Server Create](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create&preserve-view=true) i [AZ SQL Server Update](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update&preserve-view=true).

2. Przyznaj [**czytelnikom katalogu**](../../active-directory/roles/permissions-reference.md#directory-readers) usługi Azure AD uprawnienia do tożsamości serwera utworzonej lub przypisanej do serwera.
    - Aby udzielić tego uprawnienia, postępuj zgodnie z opisem używanym dla wystąpienia zarządzanego SQL, które jest dostępne w następującym artykule: [Inicjowanie obsługi administracyjnej usługi Azure AD (wystąpienie zarządzane SQL)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - Użytkownik usługi Azure AD, który udziela tego uprawnienia, musi należeć do roli administratora **globalnego** lub **ról uprzywilejowanych** usługi Azure AD.

> [!IMPORTANT]
> Kroki 1 i 2 muszą zostać wykonane w powyższej kolejności. Najpierw utwórz lub Przypisz tożsamość serwera, a następnie przyznaj uprawnienia do [**odczytu katalogów**](../../active-directory/roles/permissions-reference.md#directory-readers) . Pominięcie jednego z tych kroków lub powoduje błąd wykonania podczas tworzenia obiektu usługi Azure AD w usłudze Azure SQL w imieniu aplikacji usługi Azure AD.
>
> Jeśli używasz nazwy głównej usługi do ustawiania lub nieustawienia administratora usługi Azure AD, aplikacja musi mieć również uprawnienia [katalog. odczyt. wszystkie](/graph/permissions-reference#application-permissions-18) aplikacje w usłudze Azure AD. Aby uzyskać więcej informacji na temat [uprawnień wymaganych do ustawienia administratora usługi Azure AD](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin), a następnie instrukcje krok po kroku dotyczące tworzenia użytkownika usługi Azure AD w imieniu aplikacji usługi Azure AD, zobacz [Samouczek: tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](authentication-aad-service-principal-tutorial.md).
>
> W **publicznej wersji zapoznawczej** można przypisać rolę **czytelnicy Directory** do grupy w usłudze Azure AD. Właściciele grupy mogą następnie dodać tożsamość zarządzaną jako członek tej grupy, co spowodowałoby ominięcie potrzeby uprawnienia administratora **globalnego** lub **administratora ról uprzywilejowanych** w celu przyznania roli **czytelnicy katalogów** . Aby uzyskać więcej informacji na temat tej funkcji, zobacz " [czytelnicy Directory role in Azure Active Directory for Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Rozwiązywanie problemów i ograniczenia dotyczące publicznej wersji zapoznawczej

- W przypadku tworzenia obiektów usługi Azure AD w usłudze Azure SQL w imieniu aplikacji usługi Azure AD bez włączania tożsamości serwera i udzielania uprawnień do obiektów do **odczytu** operacja zakończy się niepowodzeniem z następującymi możliwymi błędami. Poniższy przykładowy błąd dotyczy wykonania polecenia programu PowerShell w celu utworzenia SQL Database użytkownika `myapp` w [samouczku: tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - W przypadku powyższego błędu postępuj zgodnie z instrukcjami, aby [przypisać tożsamość do serwera logicznego usługi Azure SQL Server](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) i [przypisać uprawnienia do czytnika katalogów do tożsamości serwera logicznego SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > Komunikaty o błędach wskazane powyżej zostaną zmienione przed funkcją GA, aby jasno określić brakujący wymóg instalacji dla obsługi aplikacji usługi Azure AD.
- Ustawianie aplikacji usługi Azure AD jako administratora usługi Azure AD dla wystąpienia zarządzanego SQL jest obsługiwane tylko za pomocą polecenia interfejsu wiersza poleceń i polecenia programu PowerShell z poleceniem [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) lub nowszym. Aby uzyskać więcej informacji, zobacz [AZ SQL mi AD-admin Create](/cli/azure/sql/mi/ad-admin?view=azure-cli-latest&preserve-view=true#az-sql-mi-ad-admin-create) i [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) . 
    - Jeśli chcesz użyć Azure Portal dla wystąpienia zarządzanego SQL do ustawienia administratora usługi Azure AD, możliwe jest obejście tego problemu, aby utworzyć grupę usługi Azure AD. Następnie Dodaj nazwę główną usługi (aplikację usługi Azure AD) do tej grupy, a następnie ustaw tę grupę jako administratora usługi Azure AD dla wystąpienia zarządzanego SQL.
    - Ustawienie nazwy głównej usługi (aplikacji usługi Azure AD) jako administratora usługi Azure AD dla SQL Database i usługi Azure Synapse jest obsługiwane za pomocą poleceń Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)i [interfejsu wiersza](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) polecenia.
- Korzystanie z aplikacji usługi Azure AD z inną dzierżawą usługi Azure AD kończy się niepowodzeniem podczas uzyskiwania dostępu do SQL Database lub wystąpienia zarządzanego SQL utworzonego w innej dzierżawie. Nazwa główna usługi przypisana do tej aplikacji musi należeć do tej samej dzierżawy co serwer logiczny SQL lub wystąpienie zarządzane.
- W przypadku korzystania z programu PowerShell w celu skonfigurowania indywidualnej aplikacji usługi Azure AD jako administratora usługi Azure AD dla usługi Azure SQL jest wymagany moduł [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) module lub nowszy. Upewnij się, że uaktualniono do najnowszego modułu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](authentication-aad-service-principal-tutorial.md)