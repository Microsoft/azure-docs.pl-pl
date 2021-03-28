---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Dowiedz się, jak nawiązać połączenie z usługą SQL Database, wystąpieniem zarządzanym SQL i usługą Azure Synapse Analytics przy użyciu uwierzytelniania Azure Active Directory po skonfigurowaniu usługi Azure AD.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: 9e7b337d4358f9685d683c308d6df9110607207a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643426"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Konfigurowanie uwierzytelniania usługi Azure AD i zarządzanie nim za pomocą usługi Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym artykule opisano sposób tworzenia i wypełniania wystąpienia Azure Active Directory (Azure AD), a następnie używania usługi Azure AD z usługą [Azure SQL Database](sql-database-paas-overview.md), [wystąpieniem zarządzanym usługi Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md)i [analizą usługi Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Aby zapoznać się z omówieniem, zobacz [Azure Active Directory Authentication](authentication-aad-overview.md).

## <a name="azure-ad-authentication-methods"></a>Metody uwierzytelniania usługi Azure AD

Uwierzytelnianie usługi Azure AD obsługuje następujące metody uwierzytelniania:

- Tożsamości tylko w chmurze usługi Azure AD
- Tożsamości hybrydowe usługi Azure AD, które obsługują:
  - Uwierzytelnianie w chmurze z dwoma opcjami połączonymi z bezproblemowym logowaniem jednokrotnym (SSO)
    - Uwierzytelnianie skrótu hasła usługi Azure AD
    - Uwierzytelnianie przekazywane przez usługę Azure AD
  - Uwierzytelnianie federacyjne

Aby uzyskać więcej informacji na temat metod uwierzytelniania usługi Azure AD, które można wybrać, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../../active-directory/hybrid/choose-ad-authn.md).

Aby uzyskać więcej informacji na temat tożsamości i konfiguracji hybrydowej usługi Azure AD, zobacz:

- Uwierzytelnianie skrótu hasła — [implementowanie synchronizacji skrótów haseł z synchronizacją Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Uwierzytelnianie przekazywane — [Azure Active Directory uwierzytelniania przekazywanego](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Uwierzytelnianie federacyjne — [wdrażanie Active Directory Federation Services na platformie Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) i [Azure AD Connect i w Federacji](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Tworzenie i wypełnianie wystąpienia usługi Azure AD

Utwórz wystąpienie usługi Azure AD i wypełnij je użytkownikami i grupami. Usługa Azure AD może być początkową domeną zarządzaną usługi Azure AD. Usługa Azure AD może być również lokalnym Active Directory Domain Servicesm federacyjnym z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz tematy [Integrating your on-premises identities with Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) (Integrowanie tożsamości lokalnych z usługą Azure Active Directory), [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Platforma Microsoft Azure obsługuje teraz federację z usługą Windows Servder Active Directory), [Administering your Azure AD directory](../../active-directory/fundamentals/active-directory-whatis.md) (Administrowanie katalogiem usługi Azure AD), [Manage Azure AD using Windows PowerShell](/powershell/azure/) (Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell) i [Hybrid Identity Required Ports and Protocols](../../active-directory/hybrid/reference-connect-ports.md) (Wymagane porty i protokoły tożsamości hybrydowych).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory

1. Skojarz swoją subskrypcję platformy Azure z Azure Active Directory przez utworzenie katalogu jako zaufanego katalogu subskrypcji platformy Azure, w którym znajduje się baza danych. Aby uzyskać szczegółowe informacje, zobacz [kojarzenie lub Dodawanie subskrypcji platformy Azure do dzierżawy Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. Użyj przełącznika katalogu w Azure Portal, aby przełączyć się na subskrypcję skojarzoną z domeną.

   > [!IMPORTANT]
   > Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Relacja zaufania między subskrypcją a katalogiem różni się od relacji subskrypcji z wszystkimi innymi zasobami na platformie Azure (witrynami sieci Web, bazami danych itd.), które przypominają bardziej podrzędne zasoby subskrypcji. Jeśli subskrypcja wygaśnie, dostęp do innych zasobów skojarzonych z subskrypcją również nie będzie możliwy. Lecz katalog pozostanie na platformie Azure i będzie można skojarzyć z nim inną subskrypcję oraz kontynuować zarządzanie użytkownikami w katalogu. Aby uzyskać więcej informacji o zasobach, zobacz [Opis dostępu do zasobów na platformie Azure](../../active-directory/external-identities/add-users-administrator.md). Aby dowiedzieć się więcej na temat relacji zaufania [, zobacz Jak skojarzyć lub dodać subskrypcję platformy Azure do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Administrator usługi Azure AD z serwerem w SQL Database

Każdy [serwer](logical-servers.md) na platformie Azure (który zawiera SQL Database lub Azure Synapse) jest uruchamiany przy użyciu jednego konta administratora serwera, które jest administratorem całego serwera. Utwórz drugie konto administratora jako konto usługi Azure AD. Ten podmiot zabezpieczeń jest tworzony jako użytkownik zawartej bazy danych w bazie danych Master serwera. Konta administratorów są członkami roli **db_owner** w każdej bazie danych użytkownika, a następnie wprowadź każdą bazę danych użytkownika jako użytkownika **dbo** . Aby uzyskać więcej informacji o kontach administratorów, zobacz [Zarządzanie bazami danych i](logins-create-manage.md)nazwami logowania.

W przypadku korzystania z Azure Active Directory z replikacją geograficzną administrator Azure Active Directory musi być skonfigurowany zarówno dla serwera podstawowego, jak i pomocniczego. Jeśli serwer nie ma Azure Active Directory administrator, wówczas Azure Active Directory logowania i użytkownicy otrzymają `Cannot connect` błąd na serwerze.

> [!NOTE]
> Użytkownicy niekorzystający z konta usługi Azure AD (w tym konta administratora serwera) nie mogą tworzyć użytkowników opartych na usłudze Azure AD, ponieważ nie mają uprawnień do weryfikowania użytkowników proponowanych baz danych w usłudze Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Inicjowanie obsługi administracyjnej usługi Azure AD (wystąpienie zarządzane SQL)

> [!IMPORTANT]
> Wykonaj te czynności tylko w przypadku aprowizacji wystąpienia zarządzanego Azure SQL. Tę operację może wykonać tylko administrator globalny lub administrator ról uprzywilejowanych w usłudze Azure AD.
>
> W **publicznej wersji zapoznawczej** można przypisać rolę **czytelnicy Directory** do grupy w usłudze Azure AD. Właściciele grupy mogą następnie dodać tożsamość wystąpienia zarządzanego jako element członkowski tej grupy, co umożliwi udostępnienie administratora usługi Azure AD dla wystąpienia zarządzanego SQL. Aby uzyskać więcej informacji na temat tej funkcji, zobacz " [czytelnicy Directory role in Azure Active Directory for Azure SQL](authentication-aad-directory-readers-role.md).

Wystąpienie zarządzane SQL wymaga uprawnień do odczytu usługi Azure AD w celu pomyślnego wykonania zadań, takich jak uwierzytelnianie użytkowników za pomocą przynależności do grupy zabezpieczeń lub tworzenie nowych użytkowników. Aby to umożliwić, należy przyznać uprawnienia wystąpienia zarządzanego SQL do odczytu usługi Azure AD. Można to zrobić przy użyciu Azure Portal lub programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

Aby udzielić uprawnienia do odczytu wystąpienia zarządzanego usługi SQL AD przy użyciu Azure Portal, zaloguj się jako Administrator globalny w usłudze Azure AD i wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)w prawym górnym rogu wybierz połączenie z listy rozwijanej możliwych aktywnych katalogów.

2. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD.

   Ten krok łączy subskrypcję skojarzoną z Active Directory z wystąpieniem zarządzanym SQL, upewniając się, że ta sama subskrypcja jest używana zarówno w przypadku wystąpienia usługi Azure AD, jak i wystąpienia zarządzanego SQL.

3. Przejdź do wystąpienia zarządzanego SQL, którego chcesz użyć do integracji z usługą Azure AD.

   ![Zrzut ekranu przedstawiający Azure Portal wyświetlenia strony administratora Active Directory otwartej dla wybranego wystąpienia zarządzanego SQL.](./media/authentication-aad-configure/aad.png)

4. Wybierz transparent w górnej części strony administratora Active Directory i Udziel uprawnienia bieżącemu użytkownikowi.

    ![Zrzut ekranu okna dialogowego, w którym można udzielić uprawnień do wystąpienia zarządzanego SQL na potrzeby uzyskiwania dostępu Active Directory. Wybrano przycisk Udziel uprawnień.](./media/authentication-aad-configure/grant-permissions.png)

5. Po pomyślnym zakończeniu operacji następujące powiadomienie zostanie wyświetlone w prawym górnym rogu:

    ![Zrzut ekranu powiadomienia z potwierdzeniem, że uprawnienia do odczytu usługi Active Directory zostały pomyślnie zaktualizowane dla wystąpienia zarządzanego.](./media/authentication-aad-configure/success.png)

6. Teraz możesz wybrać administratora usługi Azure AD dla wystąpienia zarządzanego SQL. W tym celu na stronie Administrator Active Directory wybierz pozycję **Ustaw polecenie administracyjne** .

    ![Zrzut ekranu przedstawiający polecenie Ustaw administrator wyróżnione na stronie Administrator Active Directory dla wybranego wystąpienia zarządzanego SQL.](./media/authentication-aad-configure/set-admin.png)

7. Na stronie administrator usługi Azure AD Wyszukaj użytkownika, wybierz użytkownika lub grupę, który ma być administratorem, a następnie wybierz pozycję **Wybierz**.

   Na stronie Administrator Active Directory są wyświetlane wszystkie elementy członkowskie i grupy Active Directory. Nie można wybrać użytkowników lub grup, które są wyszarzone, ponieważ nie są one obsługiwane jako Administratorzy usługi Azure AD. Zapoznaj się z listą obsługiwanych administratorów w [funkcjach i ograniczeniach usługi Azure AD](authentication-aad-overview.md#azure-ad-features-and-limitations). Kontrola dostępu oparta na rolach (Azure RBAC) ma zastosowanie tylko do Azure Portal i nie jest propagowana do SQL Database, wystąpienia zarządzanego SQL ani usługi Azure Synapse.

    ![Dodaj administratora Azure Active Directory](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. W górnej części strony Administrator Active Directory wybierz pozycję **Zapisz**.

    ![Zrzut ekranu strony administratora Active Directory z przyciskiem Zapisz w górnym wierszu obok przycisków ustaw administratora i Usuń administratora.](./media/authentication-aad-configure/save.png)

    Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu Administrator usługi Active Directory.

Po zainicjowaniu obsługi administracyjnej administratora usługi Azure AD dla wystąpienia zarządzanego SQL można rozpocząć tworzenie podmiotów zabezpieczeń serwera usługi Azure AD przy użyciu składni [tworzenia nazwy logowania](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) . Aby uzyskać więcej informacji, zobacz [Omówienie wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Aby później usunąć administratora, w górnej części strony Administrator Active Directory wybierz pozycję **Usuń administratora**, a następnie wybierz pozycję **Zapisz**.

### <a name="powershell"></a>PowerShell

Aby udzielić uprawnienia do odczytu wystąpienia zarządzanego usługi SQL AD przy użyciu programu PowerShell, uruchom następujący skrypt:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

$aadTenant = "<YourTenantId>" # Enter your tenant ID
$managedInstanceName = "MyManagedInstance"

# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>PowerShell dla wystąpienia zarządzanego SQL

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, należy zainstalować i uruchomić Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) nadal jest obsługiwany przez wystąpienie zarządzane usługi Azure SQL, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

Aby zainicjować obsługę administracyjną administratora usługi Azure AD, wykonaj następujące polecenia Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Polecenia cmdlet służące do aprowizacji i zarządzania administratorem usługi Azure AD dla wystąpienia zarządzanego SQL są wymienione w poniższej tabeli:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Inicjuje administratora usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji. (Musi być z bieżącej subskrypcji)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Usuwa administratora usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Zwraca informacje o administratorze usługi Azure AD dla wystąpienia zarządzanego SQL w bieżącej subskrypcji.|

Następujące polecenie pobiera informacje o administratorze usługi Azure AD dla wystąpienia zarządzanego SQL o nazwie ManagedInstance01, które jest skojarzone z grupą zasobów o nazwie ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Następujące polecenie inicjuje grupę administratorów usługi Azure AD o nazwie przetwarzający dla wystąpienia zarządzanego SQL o nazwie ManagedInstance01. Ten serwer jest skojarzony z grupą zasobów ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Następujące polecenie usuwa administratora usługi Azure AD dla wystąpienia zarządzanego SQL o nazwie ManagedInstanceName01 skojarzone z grupą zasobów ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Można również zainicjować obsługę administracyjną usługi Azure AD dla wystąpienia zarządzanego SQL przez wywołanie następujących poleceń interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[AZ SQL mi AD-administrator Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Inicjuje obsługę administracyjną Azure Active Directory administratora wystąpienia zarządzanego SQL (musi być z bieżącej subskrypcji). |
|[AZ SQL mi AD-administrator Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Usuwa Azure Active Directory administratora dla wystąpienia zarządzanego SQL. |
|[AZ SQL mi AD — Lista administratorów](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Zwraca informacje o Azure Active Directory administrator, który jest obecnie skonfigurowany dla wystąpienia zarządzanego SQL. |
|[AZ SQL mi AD-administrator Update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Aktualizuje Active Directory administratora dla wystąpienia zarządzanego SQL. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [AZ SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Inicjowanie obsługi administracyjnej usługi Azure AD administrator (SQL Database)

> [!IMPORTANT]
> Wykonaj te czynności tylko w przypadku aprowizacji [serwera](logical-servers.md) dla SQL Database lub Azure Synapse.

W poniższych dwóch procedurach pokazano, jak udostępnić administratorowi Azure Active Directory serwera w Azure Portal i przy użyciu programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. W witrynie [Azure Portal](https://portal.azure.com/) w prawym górnym rogu wybierz swoje połączenia, aby wyświetlić listę rozwijaną możliwych usług Active Directory. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD. Ten krok łączy Active Directory skojarzone z subskrypcją z serwerem, upewniając się, że ta sama subskrypcja jest używana zarówno w przypadku usługi Azure AD, jak i serwera.

2. Wyszukaj i wybierz pozycję **SQL Server**.

    ![Wyszukaj i wybierz pozycję Serwery SQL](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Na tej stronie przed wybraniem opcji **serwery SQL** możesz wybrać **gwiazdkę** obok nazwy, aby dodać do *ulubionych* kategorię, i Dodaj **serwery SQL** na lewym pasku nawigacyjnym.

3. Na stronie **SQL Server** wybierz pozycję **administrator Active Directory**.

4. Na stronie **administrator Active Directory** wybierz pozycję **Ustaw administratora**.

    ![Serwery SQL ustawione jako administrator Active Directory](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Na stronie **Dodawanie administratora** wyszukaj użytkownika, wybierz użytkownika lub grupę do pełnienia funkcji administratora, a następnie kliknij opcję **Wybierz**. (Na stronie administratora usługi Active Directory wyświetlono wszystkich członków i grupy danej usługi Active Directory). Nie można wybrać wyszarzonych użytkowników lub grup, ponieważ nie są oni obsługiwani jako administratorzy usługi Azure AD. (Zobacz listę obsługiwanych administratorów w sekcji funkcje usługi **Azure AD i ograniczenia** dotyczące [uwierzytelniania przy użyciu usługi Azure Active Directory Authentication SQL Database lub Azure Synapse](authentication-aad-overview.md)). Kontrola dostępu oparta na rolach (Azure RBAC) ma zastosowanie tylko do portalu i nie jest propagowana do SQL Server.

    ![Wybierz administratora Azure Active Directory](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. W górnej części strony **administrator Active Directory** wybierz pozycję **Zapisz**.

    ![zapisywanie administratora](./media/authentication-aad-configure/save-admin.png)

Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu **Administrator usługi Active Directory**.

   > [!NOTE]
   > Podczas konfigurowania administratora usługi Azure AD Nowa nazwa administratora (użytkownik lub Grupa) nie może już znajdować się w wirtualnej głównej bazie danych jako użytkownik uwierzytelniania serwera. Jeśli już istnieje, konfiguracja administratora usługi Azure AD nie powiedzie się. Tworzenie administratora zostanie cofnięte i wyświetli się komunikat, że administrator z taką nazwą już istnieje. Ponieważ taki użytkownik uwierzytelniania serwera nie jest częścią usługi Azure AD, nie można nawiązać połączenia z serwerem przy użyciu uwierzytelniania usługi Azure AD.

Aby później usunąć administratora, w górnej części strony **administrator Active Directory** wybierz pozycję **Usuń administratora**, a następnie wybierz pozycję **Zapisz**.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>Program PowerShell dla SQL Database i Azure Synapse

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, należy zainstalować i uruchomić Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/). Aby zainicjować obsługę administracyjną administratora usługi Azure AD, wykonaj następujące polecenia Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Polecenia cmdlet służące do aprowizacji i zarządzania administratorem usługi Azure AD dla SQL Database i usługi Azure Synapse:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Inicjuje Azure Active Directory administratora dla serwera hostingowego SQL Database lub Azure Synapse. (Musi być z bieżącej subskrypcji) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Usuwa Azure Active Directory administratora dla serwera hostingowego SQL Database lub Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Zwraca informacje o Azure Active Directory administrator, który jest obecnie skonfigurowany dla serwera hostingowego SQL Database lub Azure Synapse. |

Aby uzyskać więcej informacji na temat każdego z tych poleceń, użyj polecenia programu PowerShell get-help. Na przykład `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Poniższy skrypt inicjuje grupę administratorów usługi Azure AD o nazwie **DBA_Group** (identyfikator obiektu `40b79501-b343-44ed-9ce7-da4c8cc7353f` ) dla serwera **demo_server** w grupie zasobów o nazwie **Grupa-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Parametr wejściowy **DisplayName** akceptuje nazwę wyświetlaną usługi Azure AD lub główną nazwę użytkownika. Na przykład ``DisplayName="John Smith"`` i ``DisplayName="johns@contoso.com"``. W przypadku grup usługi Azure AD jest obsługiwana tylko nazwa wyświetlana usługi Azure AD.

> [!NOTE]
> Polecenie Azure PowerShell nie ```Set-AzSqlServerActiveDirectoryAdministrator``` uniemożliwia aprowizacji administratorów usługi Azure AD dla nieobsługiwanych użytkowników. Nieobsługiwany użytkownik może zostać zainicjowany, ale nie może nawiązać połączenia z bazą danych.

Poniższy przykład używa opcjonalnego **identyfikatora objectid**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Identyfikator **objectid** usługi Azure AD jest wymagany, gdy **Właściwość DisplayName** nie jest unikatowa. Aby pobrać wartości **objectid** i **DisplayName** , użyj Active Directory sekcji klasyczny portal Azure i Wyświetl właściwości użytkownika lub grupy.

Poniższy przykład zwraca informacje dotyczące bieżącego administratora usługi Azure AD dla serwera:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Poniższy przykład usuwa administratora usługi Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Administrator usługi Azure AD można zainicjować za pomocą następujących poleceń interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[AZ SQL Server AD-administrator Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Inicjuje Azure Active Directory administratora dla serwera hostingowego SQL Database lub Azure Synapse. (Musi być z bieżącej subskrypcji) |
|[AZ SQL Server AD-administrator Delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Usuwa Azure Active Directory administratora dla serwera hostingowego SQL Database lub Azure Synapse. |
|[AZ SQL Server AD-administrator list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Zwraca informacje o Azure Active Directory administrator, który jest obecnie skonfigurowany dla serwera hostingowego SQL Database lub Azure Synapse. |
|[AZ SQL Server AD-administrator Update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Aktualizuje Active Directory administratora dla serwera hostingowego SQL Database lub Azure Synapse. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [AZ SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Możesz również udostępnić administratorowi Azure Active Directory za pomocą interfejsów API REST. Aby uzyskać więcej informacji, zobacz informacje o [interfejsie API REST usługi Service Management oraz operacje Azure SQL Database operacji dla Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurowanie komputerów klienckich

Na wszystkich komputerach klienckich, z których aplikacje lub użytkownicy łączą się z SQL Database lub Azure Synapse przy użyciu tożsamości usługi Azure AD, należy zainstalować następujące oprogramowanie:

- .NET Framework 4,6 lub nowszy z [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory bibliotekę uwierzytelniania dla SQL Server (*ADAL.DLL*). Poniżej znajdują się linki pobierania umożliwiające zainstalowanie najnowszego sterownika programu SSMS, ODBC i OLE DB zawierającego bibliotekę *ADAL.DLL* .
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [Sterownik ODBC 17 dla SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB Driver 18 dla SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Wymagania te można spełnić w następujący sposób:

- Zainstalowanie najnowszej wersji [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) lub [narzędzi danych SQL Server](/sql/ssdt/download-sql-server-data-tools-ssdt) spełnia wymagania .NET Framework 4,6.
  - Program SSMS instaluje wersję x86 *ADAL.DLL*.
  - SSDT instaluje wersję amd64 *ADAL.DLL*.
  - Najnowsza wersja programu Visual Studio z programu [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) jest zgodna z wymaganiami dotyczącymi .NET Framework 4,6, ale nie instaluje wymaganej wersji amd64 *ADAL.DLL*.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Tworzenie zawartych użytkowników mapowanych na tożsamości usługi Azure AD

Ponieważ wystąpienie zarządzane SQL obsługuje nazwy główne (logowania) serwera usługi Azure AD, korzystanie z użytkowników zawartej bazy danych nie jest wymagane. Podmioty zabezpieczeń serwera usługi Azure AD (identyfikatory logowania) umożliwiają tworzenie danych logowania z użytkowników, grup lub aplikacji usługi Azure AD. Oznacza to, że można uwierzytelnić się za pomocą wystąpienia zarządzanego SQL, używając identyfikatora logowania serwera usługi Azure AD, a nie użytkownika zawartego w bazie danych. Aby uzyskać więcej informacji, zobacz [Omówienie wystąpienia zarządzanego SQL](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Aby zapoznać się ze składnią tworzenia podmiotów zabezpieczeń serwera usługi Azure AD (logowania), zobacz [Tworzenie nazwy logowania](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

Jednak używanie uwierzytelniania Azure Active Directory z usługami SQL Database i Azure Synapse wymaga korzystania z użytkowników zawartej bazy danych w oparciu o tożsamość usługi Azure AD. Użytkownik zawartej bazy danych nie ma nazwy logowania w bazie danych Master i mapuje na tożsamość w usłudze Azure AD, która jest skojarzona z bazą danych. Tożsamość w usłudze Azure AD może być indywidualnym kontem użytkownika lub grupą. Aby uzyskać więcej informacji na temat użytkowników zawartej bazy danych, patrz [Contained Database Users - Making Your Database Portable](/sql/relational-databases/security/contained-database-users-making-your-database-portable) (Użytkownicy zawartych baz danych — tworzenie przenośnej bazy danych).

> [!NOTE]
> Użytkowników bazy danych (z wyjątkiem administratorów) nie można tworzyć za pośrednictwem witryny Azure Portal. Role platformy Azure nie są propagowane do bazy danych w SQL Database, wystąpienia zarządzanego SQL lub Azure Synapse. Role platformy Azure służą do zarządzania zasobami platformy Azure i nie mają zastosowania do uprawnień bazy danych. Na przykład rola **współautor SQL Server** nie udziela dostępu do łączenia się z bazą danych w SQL Database, wystąpieniu zarządzanym SQL lub Azure Synapse. Uprawnienie dostępu należy nadać bezpośrednio w bazie danych za pomocą instrukcji języka Transact-SQL.

> [!WARNING]
> Znaki specjalne, takie jak dwukropek `:` lub znak ampersand, `&` gdy są zawarte jako nazwy użytkowników w instrukcjach T-SQL `CREATE LOGIN` i `CREATE USER` nie są obsługiwane.

Aby utworzyć użytkownika zawartej bazy danych opartej na usłudze Azure AD (innym niż administrator serwera, który jest właścicielem bazy danych), nawiąż połączenie z bazą danych za pomocą tożsamości usługi Azure AD jako użytkownik z co najmniej uprawnieniem **ALTER ANY User** . Następnie użyj następującej składni języka Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* może być główną nazwą użytkownika usługi Azure AD lub nazwą wyświetlaną grupy usługi Azure AD.

**Przykłady:** Aby utworzyć użytkownika zawartej bazy danych reprezentującego użytkownika domeny federacyjnej lub zarządzanej usługi Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych reprezentującego usługę Azure AD lub grupę domen federacyjnych, podaj nazwę wyświetlaną grupy zabezpieczeń:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych reprezentującego aplikację, która nawiązuje połączenie przy użyciu tokenu usługi Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> To polecenie wymaga, aby program SQL Server w imieniu zalogowanego użytkownika miał dostęp do usługi Azure AD ("dostawca zewnętrzny"). Czasami powstają sytuacje, w których usługa Azure AD zwróci wyjątek z powrotem do bazy danych SQL. W takich przypadkach użytkownik zobaczy błąd SQL 33134, który powinien zawierać komunikat o błędzie dotyczący usługi Azure AD. W większości przypadków błąd oznacza odmowa dostępu lub że użytkownik musi zarejestrować się w usłudze MFA w celu uzyskania dostępu do zasobu lub uzyskać dostęp między aplikacjami pierwszej firmy, muszą być obsługiwane za pośrednictwem autoryzacji wstępnej. W pierwszych dwóch przypadkach problem jest zwykle spowodowany przez zasady dostępu warunkowego, które są ustawione w dzierżawie usługi Azure AD użytkownika: uniemożliwia użytkownikowi dostęp do zewnętrznego dostawcy. Aktualizacja zasad dostępu warunkowego w celu zezwolenia na dostęp do aplikacji "00000002-0000-0000-C000-000000000000" (Identyfikator aplikacji usługi Azure AD interfejs API programu Graph) powinna rozwiązać ten problem. W przypadku, gdy błąd mówi dostępu między aplikacjami pierwszej firmy, muszą być obsługiwane za pośrednictwem autoryzacji wstępnej. przyczyną tego problemu jest to, że użytkownik jest zalogowany jako nazwa główna usługi. Polecenie powinno zakończyć się pomyślnie, jeśli zostanie wykonane przez użytkownika.

> [!TIP]
> Nie można bezpośrednio utworzyć użytkownika na podstawie Azure Active Directory innego niż Azure Active Directory skojarzony z subskrypcją platformy Azure. Jednak elementy członkowskie innych aktywnych katalogów, które są zaimportowani użytkownicy w skojarzonych Active Directory (nazywanych użytkownikami zewnętrznymi) można dodać do grupy Active Directory w Active Directory dzierżawcy. Utworzenie użytkownika zawartej bazy danych dla tej grupy usługi AD pozwala użytkownikom z Active Directory zewnętrznych uzyskać dostęp do SQL Database.

Aby uzyskać więcej informacji na temat tworzenia użytkowników zawartej bazy danych na podstawie tożsamości Azure Active Directory, zobacz [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> Usunięcie administratora Azure Active Directory serwera uniemożliwi użytkownikowi uwierzytelniania usługi Azure AD nawiązywanie połączenia z serwerem. W razie potrzeby użytkownicy usługi Azure AD, którzy nie będą mogli korzystać, mogą być porzucani ręcznie przez administratora SQL Database.

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat **upłynął limit czasu połączenia**, może być konieczne ustawienie `TransparentNetworkIPResolution` parametru parametrów połączenia na wartość false. Aby uzyskać więcej informacji, zobacz [problem z limitem czasu połączenia w .NET Framework 4.6.1-TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

Podczas tworzenia użytkownika bazy danych użytkownik otrzymuje uprawnienie **Połącz** i może połączyć się z tą bazą danych jako członkiem roli **publicznej** . Początkowo jedynymi uprawnieniami dostępnymi dla użytkownika są uprawnienia przyznane do roli **publicznej** lub wszelkie uprawnienia przyznane do grup usługi Azure AD, których są członkami. Po aprowizacji użytkownika zawartej bazy danych opartej na usłudze Azure AD można udzielić użytkownikom dodatkowych uprawnień w taki sam sposób jak w przypadku każdego innego typu użytkownika. Zazwyczaj przyznaj uprawnienia do ról bazy danych i Dodaj użytkowników do ról. Aby uzyskać więcej informacji, zobacz [podstawowe informacje o uprawnieniach aparatu bazy danych](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Aby uzyskać więcej informacji na temat specjalnych ról SQL Database, zobacz [Zarządzanie bazami danych i nazwami logowania w programie Azure SQL Database](logins-create-manage.md).
Konto użytkownika domeny federacyjnej zaimportowane do domeny zarządzanej jako użytkownik zewnętrzny musi używać tożsamości domeny zarządzanej.

> [!NOTE]
> Użytkownicy usługi Azure AD są oznaczeni w metadanych bazy danych typem E (EXTERNAL_USER), a grupy typem X (EXTERNAL_GROUPS). Aby uzyskać więcej informacji, zobacz [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Łączenie się z bazą danych przy użyciu programu SSMS lub SSDT  

Aby upewnić się, że administrator usługi Azure AD został prawidłowo skonfigurowany, nawiąż połączenie z bazą danych **Master** przy użyciu konta administratora usługi Azure AD.
Aby udostępnić użytkownikom zawartej bazy danych opartych na usłudze Azure AD (innym niż administrator serwera, który jest właścicielem bazy danych), Połącz się z bazą danych za pomocą tożsamości usługi Azure AD, która ma dostęp do bazy danych.

> [!IMPORTANT]
> Obsługa Azure Active Directorygo uwierzytelniania jest dostępna w [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server narzędziach](/sql/ssdt/download-sql-server-data-tools-ssdt) do obsługi danych w programie Visual Studio 2015. Wydanie programu SSMS 2016 sierpnia w sierpniu obejmuje również obsługę uwierzytelniania uniwersalnego Active Directory, co umożliwia administratorom wymaganie Multi-Factor Authentication przy użyciu połączenia telefonicznego, wiadomości tekstowej, kart inteligentnych z numerem PIN lub powiadomień aplikacji mobilnej.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Używanie tożsamości usługi Azure AD do nawiązywania połączenia przy użyciu programu SSMS lub SSDT

Poniższe procedury pokazują, jak nawiązać połączenie z usługą SQL Database za pomocą tożsamości usługi Azure AD przy użyciu narzędzi bazy danych SQL Server Management Studio lub SQL Server.

### <a name="active-directory-integrated-authentication"></a>Active Directory uwierzytelnianie zintegrowane

Użyj tej metody, jeśli logujesz się do systemu Windows przy użyciu poświadczeń Azure Active Directory z domeny federacyjnej lub domeny zarządzanej, która jest skonfigurowana do bezproblemowego logowania jednokrotnego na potrzeby uwierzytelniania za pośrednictwem funkcji przekazywania i hasła. Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../../active-directory/hybrid/how-to-connect-sso.md).

1. Uruchom Management Studio lub narzędzia danych, a następnie w oknie dialogowym **łączenie z serwerem** (lub **łączenie z aparatem bazy danych**) w polu **uwierzytelnianie** wybierz pozycję **Azure Active Directory — zintegrowane**. Nie jest potrzebne żadne hasło ani nie można go wprowadzić, ponieważ Twoje istniejące poświadczenia zostaną wyświetlone dla tego połączenia.

   ![Wybieranie uwierzytelniania zintegrowanego usługi AD][11]

2. Wybierz przycisk **Opcje** , a następnie na stronie **Właściwości połączenia** w polu **Połącz z bazą danych** wpisz nazwę bazy danych użytkownika, z którą chcesz nawiązać połączenie. Aby uzyskać więcej informacji, zobacz artykuł [wieloskładnikowej usługi Azure AD](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) dotyczący różnic między właściwościami połączenia dla programu SSMS 17. x i 18. x.

   ![Wybierz nazwę bazy danych][13]

### <a name="active-directory-password-authentication"></a>Active Directory uwierzytelnianie hasła

Użyj tej metody podczas nawiązywania połączenia z nazwą główną usługi Azure AD przy użyciu domeny zarządzanej usługi Azure AD. Można go również użyć dla kont federacyjnych bez dostępu do domeny, na przykład podczas pracy zdalnej.

Użyj tej metody, aby uwierzytelnić się w bazie danych w SQL Database lub wystąpieniu zarządzanym SQL z użytkownikami tożsamości tylko w chmurze usługi Azure AD lub tymi, które korzystają z tożsamości hybrydowych usługi Azure AD. Ta metoda obsługuje użytkowników, którzy chcą korzystać z poświadczeń systemu Windows, ale ich komputer lokalny nie jest przyłączony do domeny (na przykład przy użyciu dostępu zdalnego). W takim przypadku użytkownik systemu Windows może wskazać swoje konto domeny i hasło, a także uwierzytelnić się w bazie danych w SQL Database, wystąpieniu zarządzanym SQL lub Azure Synapse.

1. Uruchom Management Studio lub narzędzia danych, a następnie w oknie dialogowym **łączenie z serwerem** (lub **łączenie z aparatem bazy danych**) w polu **uwierzytelnianie** wybierz pozycję **Azure Active Directory-Password**.

2. W polu **Nazwa użytkownika** wpisz nazwę użytkownika Azure Active Directory w formacie **username \@ Domain.com**. Nazwy użytkowników muszą być kontami z Azure Active Directory lub konta z domeny zarządzanej lub federacyjnej z Azure Active Directory.

3. W polu **hasło** wpisz hasło użytkownika dla konta Azure Active Directory lub konta domeny zarządzane/federacyjne.

    ![Wybieranie opcji uwierzytelnianie hasła usługi AD][12]

4. Wybierz przycisk **Opcje** , a następnie na stronie **Właściwości połączenia** w polu **Połącz z bazą danych** wpisz nazwę bazy danych użytkownika, z którą chcesz nawiązać połączenie. (Zobacz grafika w poprzedniej opcji).

### <a name="active-directory-interactive-authentication"></a>Active Directory uwierzytelnianie interaktywne

Ta metoda służy do interaktywnego uwierzytelniania z Multi-Factor Authentication (MFA) z hasłem wymaganym interaktywnie. Za pomocą tej metody można uwierzytelniać się w bazie danych w SQL Database, wystąpieniu zarządzanym SQL i Azure Synapse dla użytkowników tożsamości tylko w chmurze usługi Azure AD lub tych, którzy korzystają z tożsamości hybrydowych usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [używanie uwierzytelniania wieloskładnikowego usługi Azure AD z usługami SQL Database i Azure Synapse (Obsługa programu SSMS dla usługi MFA)](authentication-mfa-ssms-overview.md).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Używanie tożsamości usługi Azure AD do nawiązywania połączenia z aplikacji klienckiej

Poniższe procedury pokazują, jak nawiązać połączenie z SQL Databaseą z tożsamością usługi Azure AD z aplikacji klienckiej.

### <a name="active-directory-integrated-authentication"></a>Active Directory uwierzytelnianie zintegrowane

Aby można było korzystać ze zintegrowanego uwierzytelniania systemu Windows, Active Directory domeny musi być federacyjny z Azure Active Directory lub być domeną zarządzaną, która jest skonfigurowana do bezproblemowego logowania jednokrotnego do uwierzytelniania za pomocą przekazywania lub skrótu hasła. Aby uzyskać więcej informacji, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne](../../active-directory/hybrid/how-to-connect-sso.md).

> [!NOTE]
> [MSAL.NET (Microsoft. Identity. Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) na potrzeby zintegrowanego uwierzytelniania systemu Windows nie jest obsługiwane w celu zapewnienia bezproblemowego logowania jednokrotnego na potrzeby uwierzytelniania w postaci przekazywania i skrótu hasła.

Aplikacja kliencka (lub usługa) łącząca się z bazą danych musi być uruchomiona na komputerze przyłączonym do domeny w ramach poświadczeń domeny użytkownika.

Aby nawiązać połączenie z bazą danych przy użyciu uwierzytelniania zintegrowanego i tożsamości usługi Azure AD, słowo kluczowe uwierzytelniania w parametrach połączenia bazy danych musi być ustawione na wartość `Active Directory Integrated` . Poniższy przykładowy kod w języku C# używa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Słowo kluczowe parametrów połączenia `Integrated Security=True` nie jest obsługiwane w przypadku nawiązywania połączenia z Azure SQL Database. Podczas tworzenia połączenia ODBC należy usunąć spacje i ustawić uwierzytelnianie na "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Active Directory uwierzytelnianie hasła

Aby nawiązać połączenie z bazą danych przy użyciu kont użytkowników tożsamości tylko w chmurze usługi Azure AD lub tych, którzy korzystają z tożsamości hybrydowych usługi Azure AD, słowo kluczowe uwierzytelniania musi być ustawione na wartość `Active Directory Password` . Parametry połączenia muszą zawierać słowa kluczowe ID/UID i Password/PWD oraz wartości. Poniższy przykładowy kod w języku C# używa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Dowiedz się więcej na temat metod uwierzytelniania usługi Azure AD przy użyciu przykładów kodu demonstracyjnego dostępnych w [demonstracji usługi GitHub w usłudze Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token usługi Azure AD

Ta metoda uwierzytelniania umożliwia usługom warstwy środkowej uzyskanie [tokenów sieci Web JSON (JWT)](../../active-directory/develop/id-tokens.md) w celu nawiązania połączenia z bazą danych w SQL Database, wystąpienia zarządzanego SQL lub Azure Synapse przez uzyskanie tokenu z usługi Azure AD. Ta metoda zapewnia różne scenariusze aplikacji, w tym tożsamości usług, nazwy główne usług i aplikacje korzystające z uwierzytelniania opartego na certyfikatach. Należy wykonać cztery podstawowe kroki, aby użyć uwierzytelniania przy użyciu tokenu usługi Azure AD:

1. Zarejestruj swoją aplikację przy użyciu Azure Active Directory i uzyskaj identyfikator klienta dla kodu.
2. Utwórz użytkownika bazy danych reprezentujący aplikację. (Ukończono wcześniej w kroku 6).
3. Utworzenie certyfikatu na komputerze klienckim powoduje uruchomienie aplikacji.
4. Dodaj certyfikat jako klucz dla aplikacji.

Przykładowe parametry połączenia:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Aby uzyskać więcej informacji, zobacz Blog dotyczący [zabezpieczeń SQL Server](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Informacje o dodawaniu certyfikatu znajdują się [w temacie Wprowadzenie do uwierzytelniania opartego na certyfikatach w Azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Poniższe instrukcje łączą się przy użyciu wersji 13,1 narzędzia sqlcmd, która jest dostępna w [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` polecenie nie `-G` działa z tożsamościami systemu i wymaga logowania do podmiotu zabezpieczeń użytkownika.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Rozwiązywanie problemów z uwierzytelnianiem w usłudze Azure AD

Wskazówki dotyczące rozwiązywania problemów z uwierzytelnianiem za pomocą usługi Azure AD można znaleźć w następującym blogu: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem nazw logowania, użytkowników, ról bazy danych i uprawnień w SQL Database, zobacz [logowania, użytkowników, role bazy danych i konta użytkowników](logins-create-manage.md).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](/sql/relational-databases/security/authentication-access/database-level-roles) (Role bazy danych).
- Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](firewall-configure.md).
- Aby uzyskać informacje na temat sposobu ustawiania użytkownika gościa usługi Azure AD jako administratora usługi Azure AD, zobacz [Tworzenie użytkowników Gości usługi Azure AD i Ustawianie ich jako administratora usługi Azure AD](authentication-aad-guest-users.md).
- Aby uzyskać informacje na temat sposobu obsługi jednostek głównych przy użyciu usługi Azure SQL, zobacz [Tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
