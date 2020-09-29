---
title: Tworzenie użytkowników usługi Azure AD przy użyciu jednostek usługi
description: Ten samouczek przeprowadzi Cię przez proces tworzenia użytkownika usługi Azure AD za pomocą aplikacji usługi Azure AD (nazw podmiotów usługi) w Azure SQL Database i usługi Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: 453821e99f53a90a076ff13f010f2031a055cbf6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91444169"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>Samouczek: tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> Ten artykuł jest w **publicznej wersji zapoznawczej**. Aby uzyskać więcej informacji, zobacz [Azure Active Directory jednostki usługi przy użyciu usługi Azure SQL](authentication-aad-service-principal.md). Ten artykuł będzie używać Azure SQL Database do zademonstrowania niezbędnych kroków samouczka, ale można go również zastosować do [analizy usługi Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Ten artykuł przeprowadzi Cię przez proces tworzenia użytkowników usługi Azure AD w Azure SQL Database przy użyciu jednostek usługi platformy Azure (aplikacji usługi Azure AD). Ta funkcja już istnieje w wystąpieniu zarządzanym usługi Azure SQL, ale jest teraz wprowadzana w Azure SQL Database i w usłudze Azure Synapse Analytics. Aby móc obsługiwać ten scenariusz, należy wygenerować tożsamość usługi Azure AD i przypisać ją do serwera logicznego usługi Azure SQL.

Aby uzyskać więcej informacji na temat uwierzytelniania usługi Azure AD dla usługi Azure SQL, zobacz artykuł [używanie uwierzytelniania Azure Active Directory](authentication-aad-overview.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Przypisywanie tożsamości do serwera logicznego usługi Azure SQL
> - Przypisz uprawnienia czytelnikom katalogu do tożsamości serwera logicznego SQL
> - Tworzenie jednostki usługi (aplikacji usługi Azure AD) w usłudze Azure AD
> - Tworzenie użytkownika nazwy głównej usługi w Azure SQL Database
> - Tworzenie innego użytkownika usługi Azure AD w SQL Database przy użyciu użytkownika podstawowego usługi Azure AD

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące [Azure SQL Database](single-database-create-quickstart.md) lub wdrożenie [usługi Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) . Załóżmy, że masz działającą SQL Database na potrzeby tego samouczka.
- Dostęp do już istniejącego Azure Active Directory.
- W przypadku korzystania z programu PowerShell w celu skonfigurowania indywidualnej aplikacji usługi Azure AD jako administratora usługi Azure AD dla usługi Azure SQL jest wymagany moduł [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) module lub nowszy. Upewnij się, że uaktualniono do najnowszego modułu.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Przypisywanie tożsamości do serwera logicznego usługi Azure SQL

1. Połącz się z Azure Active Directory. Musisz znaleźć swój identyfikator dzierżawy. Można to znaleźć, przechodząc do [Azure Portal](https://portal.azure.com)i przechodząc do zasobu **Azure Active Directory** . W okienku **Przegląd** powinien zostać wyświetlony **Identyfikator dzierżawy**. Uruchom następujące polecenie programu PowerShell:

    - Zamień `<TenantId>` na **Identyfikator dzierżawy**.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    Rejestruje `TenantId` do użytku w przyszłości w tym samouczku.

1. Wygeneruj i przypisz tożsamość usługi Azure AD do serwera logicznego usługi Azure SQL. Wykonaj następujące polecenie programu PowerShell:

    - Zastąp `<resource group>` `<server name>` zasoby i zasobami. Jeśli nazwa serwera to `myserver.database.windows.net` , Zastąp `<server name>` ciąg `myserver` .

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    Aby uzyskać więcej informacji, zobacz polecenie [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) .

    > [!IMPORTANT]
    > Jeśli skonfigurowano tożsamość usługi Azure AD dla serwera logicznego usługi Azure SQL, należy przyznać uprawnienia do [**odczytu**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) tożsamości. Niniejszy krok zostanie przeprowadzony w następnej sekcji. **Nie** pomijaj tego kroku, ponieważ uwierzytelnianie usługi Azure AD przestanie działać.

    - W przypadku użycia polecenia [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) z parametrem `AssignIdentity` dla nowego tworzenia programu SQL Server w przeszłości należy wykonać polecenie [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) jako oddzielne polecenie, aby włączyć tę właściwość w sieci szkieletowej platformy Azure.

1. Sprawdź, czy tożsamość serwera została pomyślnie przypisana. Wykonaj następujące polecenie programu PowerShell:

    - Zastąp `<resource group>` `<server name>` zasoby i zasobami. Jeśli nazwa serwera to `myserver.database.windows.net` , Zastąp `<server name>` ciąg `myserver` .
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    Dane wyjściowe powinny zawierać użytkownika `PrincipalId` , `Type` i `TenantId` . Przypisana tożsamość to `PrincipalId` .

1. Tożsamość można także sprawdzić, przechodząc do [Azure Portal](https://portal.azure.com).

    - W obszarze zasób **Azure Active Directory** przejdź do pozycji **aplikacje dla przedsiębiorstw**. Wpisz nazwę serwera logicznego SQL. Zostanie wyświetlony komunikat o **identyfikatorze obiektu** dołączonym do zasobu.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="Identyfikator obiektu":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>Przypisz uprawnienia czytelnikom katalogu do tożsamości serwera logicznego SQL

Aby umożliwić prawidłowe działanie tożsamości przypisanej do usługi Azure AD dla usługi Azure SQL, `Directory Readers` należy przyznać uprawnienia usługi Azure AD do tożsamości serwera.

Aby udzielić tego wymaganego uprawnienia, uruchom następujący skrypt.

> [!NOTE] 
> Ten skrypt musi być wykonywany przez usługę Azure AD `Global Administrator` lub `Privileged Roles Administrator` .
>
> W **publicznej wersji zapoznawczej**można przypisać `Directory Readers` rolę do grupy w usłudze Azure AD. Właściciele grupy mogą następnie dodać tożsamość zarządzaną jako członek tej grupy, co spowodowałoby ominięcie potrzeby `Global Administrator` lub `Privileged Roles Administrator` udzielenie `Directory Readers` roli. Aby uzyskać więcej informacji na temat tej funkcji, zobacz " [czytelnicy Directory role in Azure Active Directory for Azure SQL](authentication-aad-directory-readers-role.md).

- Zamień `<TenantId>` na `TenantId` zebrane wcześniej.
- Zamień `<server name>` na nazwę serwera logicznego SQL. Jeśli nazwa serwera to `myserver.database.windows.net` , Zastąp `<server name>` ciąg `myserver` .

```powershell
# This script grants Azure "Directory Readers" permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or "Privileged Roles Administrator" type of user.
# To check if the "Directory Readers" permission was granted, execute this script again

Import-Module AzureAD
Connect-AzureAD -TenantId "<TenantId>"    #Enter your actual TenantId
$AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}
 
# Get service principal for managed instance
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$($AssignIdentityName)', make sure that AssignIdentityName parameter was entered correctly."
    exit
}

if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
 
if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($AssignIdentityName)' to 'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to 'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
} else {
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> Dane wyjściowe z tego skryptu będą wskazywać, czy uprawnienia do odczytu katalogów zostały przyznane tożsamości. Można uruchomić skrypt, jeśli nie masz pewności, czy uprawnienie zostało przyznane.

Aby poznać podobne podejście do sposobu ustawiania uprawnienia **czytelników katalogów** dla wystąpienia zarządzanego SQL, zobacz [Inicjowanie obsługi administracyjnej usługi Azure AD (wystąpienie zarządzane SQL)](authentication-aad-configure.md#powershell).

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Tworzenie jednostki usługi (aplikacji usługi Azure AD) w usłudze Azure AD

1. Postępuj zgodnie z przewodnikiem, aby [zarejestrować aplikację i ustawić uprawnienia](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions).

    Upewnij się, że dodano **uprawnienia aplikacji** oraz **uprawnienia delegowane**.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="Identyfikator obiektu":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="Identyfikator obiektu":::

2. Należy również utworzyć klucz tajny klienta do logowania. Postępuj zgodnie z przewodnikiem w tym miejscu, aby [przekazać certyfikat lub utworzyć wpis tajny do logowania](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options).

3. Zapisz poniższe elementy z rejestracji aplikacji. Powinien być dostępny w okienku **Przegląd** :
    - **Identyfikator aplikacji**
    - **Identyfikator dzierżawy** — powinien być taki sam jak poprzednio

W tym samouczku będziemy używać usługi *AppSP* jako naszej głównej nazwy głównej usługi i *MojaApl* jako drugi użytkownik głównej usługi, który zostanie utworzony w usłudze Azure SQL przez *AppSP*. Konieczne będzie utworzenie dwóch aplikacji, *AppSP* i *MojaApl*.

Aby uzyskać więcej informacji na temat tworzenia aplikacji usługi Azure AD, zobacz artykuł [jak: korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md).


## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Tworzenie użytkownika nazwy głównej usługi w Azure SQL Database

Po utworzeniu jednostki usługi w usłudze Azure AD Utwórz użytkownika w SQL Database. Musisz nawiązać połączenie z SQL Database przy użyciu prawidłowej nazwy logowania z uprawnieniami do tworzenia użytkowników w bazie danych.

1. Utwórz *AppSP* użytkownika w SQL Database przy użyciu następującego polecenia T-SQL:

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. Przyznaj `db_owner` uprawnienie *AppSP*, które umożliwia użytkownikowi tworzenie innych użytkowników usługi Azure AD w bazie danych.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    Aby uzyskać więcej informacji, zobacz [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)

    Alternatywnie `ALTER ANY USER` można udzielić uprawnienia zamiast udzielania `db_owner` roli. Umożliwi to jednostce usługi dodanie innych użytkowników usługi Azure AD.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > Powyższe ustawienie nie jest wymagane, gdy *AppSP* jest ustawiony jako administrator usługi Azure AD dla serwera. Aby ustawić nazwę główną usługi jako administratora usługi AD dla serwera logicznego SQL, można użyć poleceń Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz temat [Inicjowanie obsługi administracyjnej usługi Azure AD (SQL Database)](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse).

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Tworzenie użytkownika usługi Azure AD w SQL Database przy użyciu nazwy głównej usługi Azure AD

> [!IMPORTANT]
> Nazwa główna usługi użyta do zalogowania się do SQL Database musi mieć klucz tajny klienta. Jeśli go nie ma, wykonaj krok 2 tworzenia jednostki [usługi (aplikacji usługi Azure AD) w usłudze Azure AD](#create-a-service-principal-an-azure-ad-application-in-azure-ad). Ten klucz tajny klienta należy dodać jako parametr wejściowy w skrypcie poniżej.

1. Użyj poniższego skryptu, aby utworzyć główny użytkownik usługi Azure AD *MojaApl* przy użyciu nazwy głównej usługi *AppSP*.

    - Zamień `<TenantId>` na `TenantId` zebrane wcześniej.
    - Zamień `<ClientId>` na `ClientId` zebrane wcześniej.
    - Zamień `<ClientSecret>` na utworzony wcześniej wpis tajny klienta.
    - Zamień `<server name>` na nazwę serwera logicznego SQL. Jeśli nazwa serwera to `myserver.database.windows.net` , Zastąp `<server name>` ciąg `myserver` .
    - Zamień `<database name>` na nazwę SQL Database.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    Alternatywnie możesz użyć przykładu kodu w blogu, [uwierzytelniania podstawowego usługi Azure AD do bazy danych SQL](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467). Zmodyfikuj skrypt, aby wykonać instrukcję języka DDL `CREATE USER [myapp] FROM EXTERNAL PROVIDER` . Za pomocą tego samego skryptu można utworzyć grupę w SQL Database zwykłych użytkowników usługi Azure AD.

    > [!NOTE]
    > Jeśli musisz zainstalować moduł AzureRM. profile, musisz otworzyć program PowerShell jako administrator. Poniższe polecenia umożliwiają automatyczne zainstalowanie najnowszej wersji profilu AzureRM. profile i ustawienie `$adalpath` dla powyższego skryptu:
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. Sprawdź, czy użytkownik *MojaApl* znajduje się w bazie danych, wykonując następujące polecenie:

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    Powinny być widoczne podobne dane wyjściowe:

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>Następne kroki

- [Azure Active Directory jednostki usługi przy użyciu usługi Azure SQL](authentication-aad-service-principal.md)
- [Jakie są zarządzane tożsamości dla zasobów platformy Azure?](../../active-directory/managed-identities-azure-resources/overview.md)
- [Jak używać tożsamości zarządzanych do App Service i Azure Functions](../../app-service/overview-managed-identity.md)
- [Uwierzytelnianie główne usługi Azure AD w usłudze SQL DB — przykład kodu](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Obiekty aplikacji i jednostki usługi w usłudze Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)
- [Tworzenie jednostki usługi platformy Azure za pomocą programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
- [Rola czytelnicy Directory w Azure Active Directory dla usługi Azure SQL](authentication-aad-directory-readers-role.md)
