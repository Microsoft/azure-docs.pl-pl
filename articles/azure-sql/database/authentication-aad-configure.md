---
title: Konfigurowanie uwierzytelniania usługi Azure Active Directory
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Dowiedz się, jak nawiązać SQL Database, SQL Managed Instance i Azure Synapse Analytics przy użyciu uwierzytelniania Azure Active Directory po skonfigurowaniu usługi Azure AD.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: c75364f2565611b6738996c082610229db0cb2a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762231"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Konfigurowanie uwierzytelniania usługi Azure AD i zarządzanie nim za pomocą Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

W tym artykule pokazano, jak utworzyć i wypełnić wystąpienie usługi Azure Active Directory (Azure AD), a następnie użyć usługi Azure AD z usługami [Azure SQL Database,](sql-database-paas-overview.md) [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)i [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Aby uzyskać omówienie, zobacz [Azure Active Directory uwierzytelniania.](authentication-aad-overview.md)

## <a name="azure-ad-authentication-methods"></a>Metody uwierzytelniania usługi Azure AD

Uwierzytelnianie usługi Azure AD obsługuje następujące metody uwierzytelniania:

- Tożsamości tylko w chmurze usługi Azure AD
- Tożsamości hybrydowe usługi Azure AD, które obsługują:
  - Uwierzytelnianie w chmurze z dwiema opcjami w połączeniu z bezproblemowym logowaniem jednokrotnym
    - Uwierzytelnianie przy użyciu skrótów haseł w usłudze Azure AD
    - Uwierzytelnianie pass-through usługi Azure AD
  - Uwierzytelnianie federacyjne

Aby uzyskać więcej informacji na temat metod uwierzytelniania usługi Azure AD i tego, którą z nich wybrać, zobacz Choose the right authentication method for your Azure Active Directory hybrid identity solution (Wybieranie właściwej metody uwierzytelniania dla rozwiązania do Azure Active Directory [tożsamości hybrydowej).](../../active-directory/hybrid/choose-ad-authn.md)

Aby uzyskać więcej informacji na temat tożsamości hybrydowych, konfiguracji i synchronizacji usługi Azure AD, zobacz:

- Uwierzytelnianie przy użyciu skrótów haseł — [implementowanie synchronizacji skrótów haseł Azure AD Connect synchronizacji](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Uwierzytelnianie pass-through [— Azure Active Directory uwierzytelnianie pass-through](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Uwierzytelnianie federowane — [wdrażanie Active Directory Federation Services platformie Azure oraz](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) Azure AD Connect i [federacji](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Tworzenie i wypełnianie wystąpienia usługi Azure AD

Utwórz wystąpienie usługi Azure AD i wypełnij je użytkownikami i grupami. Usługa Azure AD może być początkową domeną zarządzaną usługi Azure AD. Usługa Azure AD może być również lokalna usługa Active Directory Domain Services, która jest federowana z usługą Azure AD.

Aby uzyskać więcej informacji, zobacz tematy [Integrating your on-premises identities with Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) (Integrowanie tożsamości lokalnych z usługą Azure Active Directory), [Dodawanie niestandardowej nazwy domeny do usługi Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Platforma Microsoft Azure obsługuje teraz federację z usługą Windows Servder Active Directory), [Administering your Azure AD directory](../../active-directory/fundamentals/active-directory-whatis.md) (Administrowanie katalogiem usługi Azure AD), [Manage Azure AD using Windows PowerShell](/powershell/azure/) (Zarządzanie usługą Azure AD przy użyciu programu Windows PowerShell) i [Hybrid Identity Required Ports and Protocols](../../active-directory/hybrid/reference-connect-ports.md) (Wymagane porty i protokoły tożsamości hybrydowych).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory

1. Skojarz subskrypcję platformy Azure Azure Active Directory, aby katalog był zaufanym katalogiem dla subskrypcji platformy Azure hostowania bazy danych. Aby uzyskać szczegółowe informacje, [zobacz Kojarzenie lub dodawanie subskrypcji platformy Azure do Azure Active Directory dzierżawy.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

2. Użyj przełącznika katalogów w Azure Portal, aby przełączyć się na subskrypcję skojarzoną z domeną.

   > [!IMPORTANT]
   > Każda subskrypcja platformy Azure jest połączona relacją zaufania z wystąpieniem usługi Azure AD. Oznacza to, że subskrypcja ufa katalogowi na potrzeby uwierzytelniania użytkowników, usług i urządzeń. Wiele subskrypcji może ufać temu samemu katalogowi, ale dana subskrypcja może ufać tylko jednemu katalogowi. Relacja zaufania między subskrypcją a katalogiem różni się od relacji subskrypcji z wszystkimi innymi zasobami na platformie Azure (witrynami sieci Web, bazami danych itd.), które przypominają bardziej podrzędne zasoby subskrypcji. Jeśli subskrypcja wygaśnie, dostęp do innych zasobów skojarzonych z subskrypcją również nie będzie możliwy. Lecz katalog pozostanie na platformie Azure i będzie można skojarzyć z nim inną subskrypcję oraz kontynuować zarządzanie użytkownikami w katalogu. Aby uzyskać więcej informacji na temat zasobów, zobacz [Understanding resource access in Azure (Opis dostępu do zasobów na platformie Azure).](../../active-directory/external-identities/add-users-administrator.md) Aby dowiedzieć się więcej na temat tej zaufanej relacji, zobacz Jak skojarzyć lub [dodać subskrypcję platformy Azure do Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Administrator usługi Azure AD z serwerem w usłudze SQL Database

Każdy [serwer na](logical-servers.md) platformie Azure (który hostuje SQL Database lub Azure Synapse) rozpoczyna się od jednego konta administratora serwera, które jest administratorem całego serwera. Utwórz drugie konto administratora jako konto usługi Azure AD. Ten podmiot zabezpieczeń jest tworzony jako użytkownik zawartej bazy danych w bazie danych master serwera. Konta administratorów są członkami **roli db_owner** w każdej bazie danych użytkowników i wprowadź każdą bazę danych użytkowników jako **użytkownika dbo.** Aby uzyskać więcej informacji na temat kont administratorów, zobacz [Managing Databases and Logins (Zarządzanie bazami danych i nazwami logowania).](logins-create-manage.md)

W przypadku Azure Active Directory z replikacją geograficzną administrator Azure Active Directory musi być skonfigurowany zarówno dla serwera podstawowego, jak i pomocniczego. Jeśli serwer nie ma konta administratora Azure Active Directory, Azure Active Directory logowania i użytkownicy otrzymają komunikat o `Cannot connect` błędzie do serwera.

> [!NOTE]
> Użytkownicy, którzy nie są oparte na koncie usługi Azure AD (w tym na koncie administratora serwera), nie mogą tworzyć użytkowników opartych na usłudze Azure AD, ponieważ nie mają uprawnień do weryfikowania proponowanych użytkowników bazy danych w usłudze Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Aprowizuj administratora usługi Azure AD (SQL Managed Instance)

> [!IMPORTANT]
> Wykonaj te kroki tylko w przypadku aprowizowania Azure SQL Managed Instance. Tę operację może wykonać tylko administrator globalny lub administrator ról uprzywilejowanych w usłudze Azure AD.
>
> W **publicznej wersji zapoznawczej** można przypisać rolę **Czytelnicy katalogów** do grupy w usłudze Azure AD. Właściciele grupy mogą następnie dodać tożsamość wystąpienia zarządzanego jako członka tej grupy, co umożliwi aprowizowanie administratora usługi Azure AD dla SQL Managed Instance. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [rolę Czytniki](authentication-aad-directory-readers-role.md)katalogów w Azure Active Directory for Azure SQL .

Twoja SQL Managed Instance musi mieć uprawnienia do odczytu usługi Azure AD, aby pomyślnie wykonywać zadania, takie jak uwierzytelnianie użytkowników za pośrednictwem członkostwa w grupie zabezpieczeń lub tworzenia nowych użytkowników. Aby to działało, musisz przyznać uprawnienia SQL Managed Instance do odczytu usługi Azure AD. Można to zrobić przy użyciu Azure Portal programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

Aby udzielić SQL Managed Instance odczytu usługi Azure AD przy użyciu usługi Azure Portal, zaloguj się jako administrator globalny w usłudze Azure AD i wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)w prawym górnym rogu wybierz połączenie z listy rozwijanej możliwych katalogów Aktywnych.

2. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD.

   Ten krok łączy subskrypcję skojarzoną z usługą Active Directory z usługą SQL Managed Instance, upewnia się, że ta sama subskrypcja jest używana zarówno dla wystąpienia usługi Azure AD, jak i SQL Managed Instance.

3. Przejdź do SQL Managed Instance, którego chcesz użyć do integracji z usługą Azure AD.

   ![Zrzut ekranu przedstawiający Azure Portal z otwartą stroną administratora usługi Active Directory dla wybranego wystąpienia zarządzanego SQL.](./media/authentication-aad-configure/aad.png)

4. Wybierz transparent w górnej części strony administratora usługi Active Directory i przyznaj uprawnienia bieżącego użytkownikowi.

    ![Zrzut ekranu przedstawiający okno dialogowe udzielania uprawnień do wystąpienia zarządzanego SQL w celu uzyskania dostępu do usługi Active Directory. Zostanie wybrany przycisk Ułań uprawnienia.](./media/authentication-aad-configure/grant-permissions.png)

5. Po zakończeniu operacji w prawym górnym rogu zostanie wyświetlane następujące powiadomienie:

    ![Zrzut ekranu przedstawiający powiadomienie z potwierdzeniem, że uprawnienia do odczytu usługi Active Directory zostały pomyślnie zaktualizowane dla wystąpienia zarządzanego.](./media/authentication-aad-configure/success.png)

6. Teraz możesz wybrać administratora usługi Azure AD dla swojego SQL Managed Instance. W tym celu na stronie administratora usługi Active Directory wybierz pozycję **Ustaw polecenie** administratora.

    ![Zrzut ekranu przedstawiający polecenie Ustaw administratora wyróżnione na stronie administratora usługi Active Directory dla wybranego wystąpienia zarządzanego SQL.](./media/authentication-aad-configure/set-admin.png)

7. Na stronie administratora usługi Azure AD wyszukaj użytkownika, wybierz użytkownika lub grupę, która ma być administratorem, a następnie wybierz **pozycję Wybierz**.

   Strona administratora usługi Active Directory zawiera wszystkich członków i grupy usługi Active Directory. Nie można wybrać wyszarowanych użytkowników lub grup, ponieważ nie są oni obsługiwani jako administratorzy usługi Azure AD. Zobacz listę obsługiwanych administratorów w tesłudze [Azure AD Features and Limitations (Funkcje i ograniczenia usługi Azure AD).](authentication-aad-overview.md#azure-ad-features-and-limitations) Kontrola dostępu na podstawie ról (RBAC) platformy Azure ma zastosowanie tylko do usługi Azure Portal i nie jest propagowana do SQL Database, SQL Managed Instance ani Azure Synapse.

    ![Dodawanie Azure Active Directory administratora](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. W górnej części strony administratora usługi Active Directory wybierz pozycję **Zapisz.**

    ![Zrzut ekranu przedstawiający stronę administratora usługi Active Directory z przyciskiem Zapisz w górnym wierszu obok przycisków Ustaw administratora i Usuń administratora.](./media/authentication-aad-configure/save.png)

    Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu Administrator usługi Active Directory.

Po aprowizowaniu administratora usługi Azure AD dla SQL Managed Instance możesz rozpocząć tworzenie podmiotów zabezpieczeń (identyfikatorów logowania) serwera Azure AD przy użyciu składni [CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) Aby uzyskać więcej informacji, [zobacz SQL Managed Instance omówienie systemu](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration).

> [!TIP]
> Aby później usunąć administratora, w górnej części strony administratora usługi Active Directory wybierz pozycję Usuń **administratora,** a następnie wybierz pozycję **Zapisz.**

### <a name="powershell"></a>PowerShell

Aby udzielić uprawnienia SQL Managed Instance do odczytu usługi Azure AD przy użyciu programu PowerShell, uruchom ten skrypt:

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

### <a name="powershell-for-sql-managed-instance"></a>Program PowerShell dla SQL Managed Instance

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, musisz mieć Azure PowerShell i uruchomione. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/).

> [!IMPORTANT]
> Moduł PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Managed Instance, ale cały przyszły rozwój jest dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r.  Argumenty poleceń w module Az i modułach AzureRm są w znacznym stopniu identyczne. Aby uzyskać więcej informacji na temat ich zgodności, [zobacz Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az)(Wprowadzenie do nowego modułu Az Azure PowerShell Az).

Aby aprowizować administratora usługi Azure AD, wykonaj następujące Azure PowerShell polecenia:

- Connect-AzAccount
- Select-AzSubscription

Polecenia cmdlet używane do aprowizować administratora usługi Azure AD SQL Managed Instance są wymienione w poniższej tabeli:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Astrowiuje administratora usługi Azure AD dla SQL Managed Instance w bieżącej subskrypcji. (Musi pochodzić z bieżącej subskrypcji)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Usuwa administratora usługi Azure AD dla SQL Managed Instance w bieżącej subskrypcji. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Zwraca informacje o administratorze usługi Azure AD dla SQL Managed Instance w bieżącej subskrypcji.|

Następujące polecenie pobiera informacje o administratorze usługi Azure AD dla SQL Managed Instance o nazwie ManagedInstance01, która jest skojarzona z grupą zasobów o nazwie ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Następujące polecenie astrowiuje grupę administratorów usługi Azure AD o nazwie DBAs dla SQL Managed Instance o nazwie ManagedInstance01. Ten serwer jest skojarzony z grupą zasobów ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Następujące polecenie usuwa administratora usługi Azure AD dla grupy SQL Managed Instance o nazwie ManagedInstanceName01 skojarzonej z grupą zasobów ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Możesz również aprowizowania administratora usługi Azure AD dla SQL Managed Instance, wywołując następujące polecenia interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) | A Azure Active Directory administratora dla SQL Managed Instance (musi pochodzić z bieżącej subskrypcji). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_delete) | Usuwa Azure Active Directory administratora aplikacji SQL Managed Instance. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_list) | Zwraca informacje o Azure Active Directory danych aktualnie skonfigurowanych dla SQL Managed Instance. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_update) | Aktualizuje administratora usługi Active Directory dla SQL Managed Instance. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [az sql mi](/cli/azure/sql/mi).

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Aprowizuj administratora usługi Azure AD (SQL Database)

> [!IMPORTANT]
> Te kroki należy wykonać tylko w przypadku aprowizowania serwera [dla](logical-servers.md) SQL Database lub Azure Synapse.

Poniższe dwie procedury pokazują, jak aprowizować administratora Azure Active Directory dla serwera w Azure Portal i przy użyciu programu PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. W witrynie [Azure Portal](https://portal.azure.com/) w prawym górnym rogu wybierz swoje połączenia, aby wyświetlić listę rozwijaną możliwych usług Active Directory. Wybierz odpowiednią usługę Active Directory jako domyślną usługę Azure AD. Ten krok łączy usługę Active Directory skojarzoną z subskrypcją z serwerem, upewniamy się, że ta sama subskrypcja jest używana zarówno dla usługi Azure AD, jak i serwera.

2. Wyszukaj i wybierz **pozycję SQL Server**.

    ![Wyszukiwanie i wybieranie serwerów SQL](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Na tej stronie, przed wybraniem serwerów  **SQL,** możesz wybrać  gwiazdkę obok nazwy, aby dodać do ulubionych kategorię, i dodać serwery **SQL** na lewym pasku nawigacyjnym.

3. Na stronie **SQL Server** wybierz pozycję **Administrator usługi Active Directory.**

4. Na stronie **administratora usługi Active Directory** wybierz pozycję Ustaw **administratora.**

    ![Serwery SQL ustawiają administratora usługi Active Directory](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Na stronie **Dodawanie administratora** wyszukaj użytkownika, wybierz użytkownika lub grupę do pełnienia funkcji administratora, a następnie kliknij opcję **Wybierz**. (Na stronie administratora usługi Active Directory wyświetlono wszystkich członków i grupy danej usługi Active Directory). Nie można wybrać wyszarzonych użytkowników lub grup, ponieważ nie są oni obsługiwani jako administratorzy usługi Azure AD. (Zobacz listę obsługiwanych administratorów w sekcji Funkcje i ograniczenia usługi **Azure AD** artykułu Używanie uwierzytelniania usługi Azure Active Directory do uwierzytelniania za pomocą usługi SQL Database [lub Azure Synapse).](authentication-aad-overview.md) Kontrola dostępu oparta na rolach (RBAC) platformy Azure ma zastosowanie tylko do portalu i nie jest propagowana do SQL Server.

    ![Wybierz Azure Active Directory administratora](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. W górnej części strony **administratora usługi Active Directory** wybierz pozycję **Zapisz.**

    ![zapisywanie administratora](./media/authentication-aad-configure/save-admin.png)

Proces zmiany administratora może potrwać kilka minut. Nowy administrator pojawi się w polu **Administrator usługi Active Directory**.

   > [!NOTE]
   > Podczas konfigurowania administratora usługi Azure AD nowa nazwa administratora (użytkownik lub grupa) nie może już być obecna w wirtualnej bazie danych master jako użytkownik uwierzytelniania serwera. Jeśli już istnieje, konfiguracja administratora usługi Azure AD nie powiedzie się. Tworzenie administratora zostanie cofnięte i wyświetli się komunikat, że administrator z taką nazwą już istnieje. Ponieważ taki użytkownik uwierzytelniania serwera nie jest częścią usługi Azure AD, wszelkie wysiłki podejmowane w celu nawiązania połączenia z serwerem przy użyciu uwierzytelniania usługi Azure AD nie powiedą się.

Aby później usunąć administratora, w górnej części strony administratora usługi **Active Directory** wybierz pozycję **Usuń administratora,** a następnie wybierz pozycję **Zapisz.**

### <a name="powershell-for-sql-database-and-azure-synapse"></a>Program PowerShell dla SQL Database i Azure Synapse

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić polecenia cmdlet programu PowerShell, musisz mieć Azure PowerShell i uruchomione. Aby uzyskać szczegółowe informacje, zobacz temat [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/). Aby aprowizować administratora usługi Azure AD, wykonaj następujące Azure PowerShell polecenia:

- Connect-AzAccount
- Select-AzSubscription

Polecenia cmdlet używane do aprowizować administratora usługi Azure AD i zarządzać nimi SQL Database i Azure Synapse:

| Nazwa polecenia cmdlet | Opis |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |A Azure Active Directory administrator serwera hostującym SQL Database lub Azure Synapse. (Musi pochodzić z bieżącej subskrypcji) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Usuwa administratora Azure Active Directory dla serwera hostowania SQL Database lub Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Zwraca informacje o serwerze Azure Active Directory aktualnie skonfigurowanym dla serwera hostującym SQL Database lub Azure Synapse. |

Użyj polecenia programu PowerShell get-help, aby wyświetlić więcej informacji na temat każdego z tych poleceń. Na przykład `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Poniższy skrypt astrowiuje grupę administratorów usługi Azure AD **DBA_Group** (identyfikator obiektu ) dla serwera demo_server w grupie zasobów o nazwie `40b79501-b343-44ed-9ce7-da4c8cc7353f`  **Grupa-23:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

Parametr **wejściowy DisplayName** akceptuje nazwę wyświetlaną usługi Azure AD lub główną nazwę użytkownika. Na przykład ``DisplayName="John Smith"`` i ``DisplayName="johns@contoso.com"``. W przypadku grup usługi Azure AD obsługiwana jest tylko nazwa wyświetlana usługi Azure AD.

> [!NOTE]
> Polecenie Azure PowerShell nie uniemożliwia aprowizowania administratorów ```Set-AzSqlServerActiveDirectoryAdministrator``` usługi Azure AD dla nieobsługiwanych użytkowników. Nieobsługiwanego użytkownika można aprowizować, ale nie można nawiązać połączenia z bazą danych.

W poniższym przykładzie użyto opcjonalnego **parametru ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Identyfikator **ObjectID usługi** Azure AD jest wymagany, gdy **nazwa wyświetlana** nie jest unikatowa. Aby pobrać wartości **ObjectID** i **DisplayName,** użyj sekcji Active Directory klasycznego portalu Azure i wyświetl właściwości użytkownika lub grupy.

Poniższy przykład zwraca informacje o bieżącym administratorze usługi Azure AD dla serwera:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Poniższy przykład usuwa administratora usługi Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Administratora usługi Azure AD można aprowizowania, wywołując następujące polecenia interfejsu wiersza polecenia:

| Polecenie | Opis |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) | A Azure Active Directory administrator serwera hostującym SQL Database lub Azure Synapse. (Musi pochodzić z bieżącej subskrypcji) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) | Usuwa administratora Azure Active Directory dla serwera hostowania SQL Database lub Azure Synapse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) | Zwraca informacje o serwerze Azure Active Directory aktualnie skonfigurowanym dla serwera hostującym serwer SQL Database lub Azure Synapse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) | Aktualizuje administratora usługi Active Directory dla serwera host SQL Database lub Azure Synapse. |

Aby uzyskać więcej informacji na temat poleceń interfejsu wiersza polecenia, zobacz [az sql server](/cli/azure/sql/server).

* * *

> [!NOTE]
> Administrator aplikacji może również aprowiz Azure Active Directory przy użyciu interfejsów API REST. Aby uzyskać więcej informacji, zobacz [Service Management REST API Reference and Operations for Azure SQL Database Operations for Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Konfigurowanie komputerów klienckich

Na wszystkich maszynach klienckich, z których aplikacje lub użytkownicy łączą się z usługą SQL Database lub Azure Synapse przy użyciu tożsamości usługi Azure AD, należy zainstalować następujące oprogramowanie:

- .NET Framework 4.6 lub nowszy z [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory Authentication Library for SQL Server (*ADAL.DLL*). Poniżej znajdują się linki do pobierania służące do instalowania najnowszego sterownika SSMS, ODBC i OLE DB, który zawiera *ADAL.DLL* biblioteki.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [Sterownik ODBC 17 dla SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB Driver 18 for SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

Te wymagania można spełnić przez:

- Zainstalowanie najnowszej wersji narzędzi [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) [lub SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) spełnia .NET Framework 4.6.
  - Program SSMS instaluje wersję x86 programu *ADAL.DLL*.
  - Program SSDT instaluje wersję amd64 programu *ADAL.DLL*.
  - Najnowsza wersja Visual Studio z programu [Visual Studio Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs) spełnia wymaganie wersji .NET Framework 4.6, ale nie instaluje wymaganej wersji amd64 *ADAL.DLL.*

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Tworzenie zawartych użytkowników mapowanych na tożsamości usługi Azure AD

Ponieważ SQL Managed Instance obsługuje jednostki usługi (identyfikatory logowania) serwera Azure AD, używanie użytkowników zawartej bazy danych nie jest wymagane. Podmioty zabezpieczeń serwera usługi Azure AD (identyfikatory logowania) umożliwiają tworzenie danych logowania z użytkowników, grup lub aplikacji usługi Azure AD. Oznacza to, że możesz uwierzytelnić się za pomocą SQL Managed Instance przy użyciu identyfikatora logowania serwera usługi Azure AD, a nie użytkownika zawartej bazy danych. Aby uzyskać więcej informacji, [zobacz SQL Managed Instance omówienie systemu](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration). Aby uzyskać informacje o składni dotyczącej tworzenia podmiotów zabezpieczeń (identyfikatorów logowania) serwera Azure AD, [zobacz CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

Jednak używanie uwierzytelniania Azure Active Directory z SQL Database i Azure Synapse wymaga użycia użytkowników zawartej bazy danych na podstawie tożsamości usługi Azure AD. Użytkownik zawartej bazy danych nie ma identyfikatora logowania w bazie danych master i jest mapowany na tożsamość w usłudze Azure AD skojarzoną z bazą danych. Tożsamość w usłudze Azure AD może być indywidualnym kontem użytkownika lub grupą. Aby uzyskać więcej informacji na temat użytkowników zawartej bazy danych, patrz [Contained Database Users - Making Your Database Portable](/sql/relational-databases/security/contained-database-users-making-your-database-portable) (Użytkownicy zawartych baz danych — tworzenie przenośnej bazy danych).

> [!NOTE]
> Użytkowników bazy danych (z wyjątkiem administratorów) nie można tworzyć za pośrednictwem witryny Azure Portal. Role platformy Azure nie są propagowane do bazy danych w SQL Database, SQL Managed Instance lub Azure Synapse. Role platformy Azure są używane do zarządzania zasobami platformy Azure i nie mają zastosowania do uprawnień bazy danych. Na przykład rola **współautora SQL Server** nie udziela dostępu do nawiązywania połączenia z bazą danych w programie SQL Database, SQL Managed Instance lub Azure Synapse. Uprawnienie dostępu należy nadać bezpośrednio w bazie danych za pomocą instrukcji języka Transact-SQL.

> [!WARNING]
> Znaki specjalne, takie jak dwukropek lub handlowe "i", gdy są dołączone jako nazwy użytkowników w języku `:` `&` T-SQL, i `CREATE LOGIN` `CREATE USER` instrukcje nie są obsługiwane.

Aby utworzyć użytkownika zawartej bazy danych opartego na usłudze Azure AD (innego niż administrator serwera, który jest właścicielem bazy danych), połącz się z bazą danych przy użyciu tożsamości usługi Azure AD jako użytkownik z co najmniej uprawnieniem **ALTER ANY USER.** Następnie użyj następującej składni języka Transact-SQL:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* może być główną nazwą użytkownika usługi Azure AD lub nazwą wyświetlaną grupy usługi Azure AD.

**Przykłady:** Aby utworzyć użytkownika zawartej bazy danych reprezentującego użytkownika domeny federowej lub zarządzanej usługi Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych reprezentującego grupę domeny usługi Azure AD lub federacyjną, podaj nazwę wyświetlaną grupy zabezpieczeń:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Aby utworzyć użytkownika zawartej bazy danych reprezentującego aplikację, która nawiązuje połączenie przy użyciu tokenu usługi Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> To polecenie wymaga, aby usługa SQL uzyskuje dostęp do usługi Azure AD ("dostawca zewnętrzny") w imieniu zalogowanego użytkownika. Czasami wystąpią sytuacje, w których usługa Azure AD zwróci wyjątek z powrotem do bazy danych SQL. W takich przypadkach użytkownik zobaczy błąd SQL 33134, który powinien zawierać komunikat o błędzie specyficzny dla usługi Azure AD. W większości przypadków komunikat o błędzie będzie zawierać komunikat o odmowie dostępu lub o tym, że użytkownik musi zarejestrować się w u usługach MFA, aby uzyskać dostęp do zasobu, lub że dostęp między aplikacjami firmowych musi być obsługiwany za pośrednictwem wstępnej autoryzacji. W pierwszych dwóch przypadkach problem jest zwykle spowodowany przez zasady dostępu warunkowego ustawione w dzierżawie usługi Azure AD użytkownika: uniemożliwiają użytkownikowi dostęp do zewnętrznego dostawcy. Zaktualizowanie zasad dostępu warunkowego w celu umożliwienia dostępu do aplikacji "00000002-0000-0000-c000-000000000000" (identyfikator aplikacji usługi Azure AD interfejs Graph API) powinno rozwiązać ten problem. Jeśli błąd informuje o tym, że dostęp między aplikacjami firmowych musi być obsługiwany za pośrednictwem wstępnej autoryzacji, problem występuje, ponieważ użytkownik jest zalogowany jako nazwa główna usługi. Polecenie powinno zakończyć się powodzeniem, jeśli zamiast tego zostanie wykonane przez użytkownika.

> [!TIP]
> Nie można bezpośrednio utworzyć użytkownika na Azure Active Directory innej niż Azure Active Directory skojarzona z subskrypcją platformy Azure. Jednak członków innych katalogów active, które są zaimportowane użytkowników w skojarzonej usługi Active Directory (znane jako użytkowników zewnętrznych) można dodać do grupy usługi Active Directory w dzierżawie usługi Active Directory. Tworząc użytkownika zawartej bazy danych dla tej grupy usługi AD, użytkownicy z zewnętrznej usługi Active Directory mogą uzyskać dostęp do SQL Database.

Aby uzyskać więcej informacji na temat tworzenia użytkowników zawartej bazy danych na podstawie Azure Active Directory tożsamości, zobacz [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql).

> [!NOTE]
> Usunięcie Azure Active Directory administratora serwera uniemożliwia dowolnemu użytkownikowi uwierzytelniania usługi Azure AD nawiązanie połączenia z serwerem. W razie potrzeby użytkownicy bezużyteczności usługi Azure AD mogą zostać porzuceni ręcznie przez SQL Database administratora.

> [!NOTE]
> Jeśli zostanie wyświetlony limit **czasu połączenia wygasł,** może być konieczne ustawienie `TransparentNetworkIPResolution` parametru parametrów połączenia na wartość false. Aby uzyskać więcej informacji, zobacz Problem z limitem czasu połączenia [.NET Framework 4.6.1 — TransparentNetworkIPResolution.](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution)

Podczas tworzenia użytkownika bazy danych użytkownik ten otrzymuje uprawnienie **CONNECT** i może nawiązać połączenie z bazą danych jako członek **roli PUBLIC.** Początkowo jedynymi uprawnieniami dostępnymi dla użytkownika są wszelkie uprawnienia przyznane roli **PUBLIC** lub wszelkie uprawnienia przyznane dowolnym grupom usługi Azure AD, do których należy. Po aprowizowania użytkownika zawartej bazy danych opartej na usłudze Azure AD możesz udzielić użytkownikowi dodatkowych uprawnień, tak samo jak w przypadku udzielania uprawnień dowolnem innym typom użytkowników. Zazwyczaj przyznaj uprawnienia do ról bazy danych i dodaj użytkowników do ról. Aby uzyskać więcej informacji, zobacz [Database Engine Permission Basics (Podstawy uprawnień aparatu bazy danych).](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) Aby uzyskać więcej informacji na temat specjalnych ról SQL Database, zobacz Zarządzanie bazami danych i [nazwami logowania w Azure SQL Database.](logins-create-manage.md)
Konto użytkownika domeny federacji, które jest importowane do domeny zarządzanej jako użytkownik zewnętrzny, musi używać tożsamości domeny zarządzanej.

> [!NOTE]
> Użytkownicy usługi Azure AD są oznaczeni w metadanych bazy danych typem E (EXTERNAL_USER), a grupy typem X (EXTERNAL_GROUPS). Aby uzyskać więcej informacji, zobacz [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Nawiązywanie połączenia z bazą danych przy użyciu programu SSMS lub SSDT  

Aby potwierdzić, że administrator usługi Azure AD jest prawidłowo skonfigurowany, połącz się z bazą danych **master** przy użyciu konta administratora usługi Azure AD.
Aby aprowizować użytkownika zawartej bazy danych opartego na usłudze Azure AD (innego niż administrator serwera, który jest właścicielem bazy danych), połącz się z bazą danych przy użyciu tożsamości usługi Azure AD, która ma dostęp do bazy danych.

> [!IMPORTANT]
> Obsługa uwierzytelniania Azure Active Directory jest dostępna w [programach SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) i [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) w wersji Visual Studio 2015. Wersja programu SSMS z sierpnia 2016 r. obejmuje również obsługę uniwersalnego uwierzytelniania usługi Active Directory, która umożliwia administratorom wymaganie uwierzytelniania wieloskładnikowego przy użyciu połączenia telefonicznego, wiadomości SMS, kart inteligentnych z numerem PIN lub powiadomienia aplikacji mobilnej.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Nawiązywanie połączenia przy użyciu programu SSMS lub SSDT przy użyciu tożsamości usługi Azure AD

Poniższe procedury pokazują, jak nawiązać połączenie z usługą SQL Database tożsamości usługi Azure AD przy użyciu SQL Server Management Studio lub SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Użyj tej metody, jeśli logujesz się do systemu Windows przy użyciu poświadczeń usługi Azure Active Directory z domeny federowej lub domeny zarządzanej skonfigurowanej do bezproblemowego logowania się za pomocą uwierzytelniania za pomocą haseł i haseł. Aby uzyskać więcej informacji, [zobacz Azure Active Directory Seamless Single Sign-On (Bezproblemowe logowanie pojedynczej).](../../active-directory/hybrid/how-to-connect-sso.md)

1. Uruchom Management Studio lub Narzędzia danych, a następnie w oknie dialogowym Łączenie z  serwerem **(lub** Łączenie z aparatem bazy **danych)** w polu Uwierzytelnianie wybierz pozycję Azure Active Directory **— Zintegrowane.** Nie jest wymagane żadne hasło lub można je wprowadzić, ponieważ istniejące poświadczenia będą prezentowane dla połączenia.

   ![Wybieranie zintegrowanego uwierzytelniania usługi AD][11]

2. Wybierz przycisk **Opcje,** a  następnie na stronie  Właściwości połączenia w polu Połącz z bazą danych wpisz nazwę bazy danych użytkownika, z którą chcesz nawiązać połączenie. Aby uzyskać więcej informacji, zobacz artykuł [Multi-factor Azure AD auth](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) on the differences between the Connection Properties for SSMS 17.x and 18.x (Wieloskładnikowe uwierzytelnianie usługi Azure AD dotyczące różnic między właściwościami połączenia dla programu SSMS 17.x i 18.x).

   ![Wybierz nazwę bazy danych][13]

### <a name="active-directory-password-authentication"></a>Uwierzytelnianie hasłem w usłudze Active Directory

Użyj tej metody podczas nawiązywania połączenia z główną nazwą usługi Azure AD przy użyciu domeny zarządzanej usługi Azure AD. Można go również używać dla kont federowanych bez dostępu do domeny, na przykład podczas pracy zdalnej.

Użyj tej metody do uwierzytelniania w bazie danych w usłudze SQL Database lub SQL Managed Instance z użytkownikami tożsamości tylko w chmurze usługi Azure AD lub tymi, którzy korzystają z tożsamości hybrydowych usługi Azure AD. Ta metoda obsługuje użytkowników, którzy chcą używać poświadczeń systemu Windows, ale ich komputer lokalny nie jest przyłączony do domeny (na przykład przy użyciu dostępu zdalnego). W takim przypadku użytkownik systemu Windows może wskazać swoje konto domeny i hasło oraz uwierzytelnić się w bazie danych w SQL Database, SQL Managed Instance lub Azure Synapse.

1. Uruchom Management Studio lub Narzędzia danych, a następnie w oknie dialogowym Łączenie z  serwerem **(lub** Łączenie z aparatem bazy **danych)** w polu Uwierzytelnianie wybierz pozycję Azure Active Directory **— Hasło.**

2. W **polu Nazwa użytkownika** wpisz Azure Active Directory użytkownika w formacie nazwa **użytkownika \@ domain.com**. Nazwy użytkowników muszą być kontem z Azure Active Directory lub kontem z domeny zarządzanej lub federowej z Azure Active Directory.

3. W polu **Hasło** wpisz hasło użytkownika dla konta Azure Active Directory lub konta domeny zarządzanej/federowej.

    ![Wybieranie uwierzytelniania za pomocą hasła usługi AD][12]

4. Wybierz przycisk **Opcje,** a  następnie na stronie  Właściwości połączenia w polu Połącz z bazą danych wpisz nazwę bazy danych użytkownika, z którą chcesz nawiązać połączenie. (Zobacz grafikę w poprzedniej opcji).

### <a name="active-directory-interactive-authentication"></a>Uwierzytelnianie interakcyjne usługi Active Directory

Użyj tej metody do uwierzytelniania interakcyjnego z uwierzytelnianiem wieloskładnikowym (MFA) lub bez niego, z interakcyjnie żądanym hasłem. Ta metoda może służyć do uwierzytelniania w bazie danych w usługach SQL Database, SQL Managed Instance i Azure Synapse dla użytkowników tożsamości tylko w chmurze usługi Azure AD lub użytkowników korzystających z tożsamości hybrydowych usługi Azure AD.

Aby uzyskać więcej informacji, zobacz [Using multi-factor Azure AD authentication with SQL Database and Azure Synapse (SSMS support for MFA) (Używanie wieloskładnikowego](authentication-mfa-ssms-overview.md)uwierzytelniania usługi Azure AD z usługami SQL Database i Azure Synapse (obsługa programu SSMS dla usługi MFA).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Nawiązywanie połączenia z aplikacji klienckiej przy użyciu tożsamości usługi Azure AD

Poniższe procedury pokazują, jak nawiązać połączenie z SQL Database tożsamością usługi Azure AD z aplikacji klienckiej.

### <a name="active-directory-integrated-authentication"></a>Zintegrowane uwierzytelnianie usługi Active Directory

Aby można było korzystać ze zintegrowanego uwierzytelniania systemu Windows, usługa Active Directory domeny musi być federowana z usługą Azure Active Directory lub powinna być domeną zarządzaną skonfigurowaną do bezproblemowego logowania się za pomocą logowania pojedynczego na użytek uwierzytelniania za pośrednictwem hasła lub uwierzytelniania przy użyciu skrótu hasła. Aby uzyskać więcej informacji, [zobacz Azure Active Directory Seamless Single Sign-On (Bezproblemowe logowanie pojedynczej).](../../active-directory/hybrid/how-to-connect-sso.md)

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) do zintegrowanego uwierzytelniania systemu Windows nie jest obsługiwane w przypadku bezproblemowego logowania się za pomocą logowania jednorazowego w celu uwierzytelniania za pomocą skrótów haseł i haseł.

Aplikacja klienta (lub usługa) łącząca się z bazą danych musi być uruchomiona na komputerze przyłączonym do domeny w ramach poświadczeń domeny użytkownika.

Aby nawiązać połączenie z bazą danych przy użyciu zintegrowanego uwierzytelniania i tożsamości usługi Azure AD, słowo kluczowe Authentication w ciągu połączenia bazy danych musi mieć wartość `Active Directory Integrated` . Poniższy przykład kodu w języku C# używa ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Słowo kluczowe parametrów połączenia `Integrated Security=True` nie jest obsługiwane w przypadku nawiązywania połączenia z Azure SQL Database. Podczas tworzenia połączenia ODBC należy usunąć spacje i ustawić uwierzytelnianie na wartość "ActiveDirectoryIntegrated".

### <a name="active-directory-password-authentication"></a>Uwierzytelnianie hasłem w usłudze Active Directory

Aby nawiązać połączenie z bazą danych przy użyciu kont użytkowników tożsamości tylko w chmurze usługi Azure AD lub tych, którzy używają tożsamości hybrydowych usługi Azure AD, słowo kluczowe Uwierzytelnianie musi mieć wartość `Active Directory Password` . Ciągi połączenia muszą zawierać wartości i słowa kluczowe user ID/UID oraz password/PWD. Poniższy przykład kodu w języku C# używa ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Dowiedz się więcej o metodach uwierzytelniania usługi Azure AD przy użyciu przykładów kodu demonstracyjnego dostępnych w pokazie usługi GitHub uwierzytelniania [usługi Azure AD.](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)

## <a name="azure-ad-token"></a>Token usługi Azure AD

Ta metoda uwierzytelniania umożliwia usługom warstwy środkowej uzyskanie tokenów sieci [Web JSON (JWT)](../../active-directory/develop/id-tokens.md) w celu nawiązania połączenia z bazą danych w usłudze SQL Database, SQL Managed Instance lub Azure Synapse przez uzyskanie tokenu z usługi Azure AD. Ta metoda umożliwia korzystanie z różnych scenariuszy aplikacji, takich jak tożsamości usług, jednostki usługi i aplikacje korzystające z uwierzytelniania opartego na certyfikatach. Aby korzystać z uwierzytelniania tokenu usługi Azure AD, należy wykonać cztery podstawowe kroki:

1. Zarejestruj aplikację w Azure Active Directory i uzyskaj identyfikator klienta dla swojego kodu.
2. Utwórz użytkownika bazy danych reprezentującego aplikację. (Ukończono wcześniej w kroku 6).
3. Utwórz certyfikat na komputerze klienckim, na których jest uruchamiana aplikacja.
4. Dodaj certyfikat jako klucz dla aplikacji.

Przykładowe ciągi połączenia:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Aby uzyskać więcej informacji, [zobacz SQL Server Security Blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Aby uzyskać informacje na temat dodawania certyfikatu, zobacz Wprowadzenie do uwierzytelniania [opartego](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)na certyfikatach w Azure Active Directory .

### <a name="sqlcmd"></a>sqlcmd

Poniższe instrukcje łączą się przy użyciu wersji 13.1 narzędzia sqlcmd, która jest dostępna w [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` Polecenie nie `-G` działa z tożsamościami systemu i wymaga głównego identyfikatora logowania użytkownika.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Rozwiązywanie problemów z uwierzytelnianiem usługi Azure AD

Wskazówki dotyczące rozwiązywania problemów z uwierzytelnianiem usługi Azure AD można znaleźć w następującym blogu: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Następne kroki

- Omówienie identyfikatorów logowania, użytkowników, ról bazy danych i uprawnień w programie SQL Database zawiera temat [Logins, users, database roles, and user accounts](logins-create-manage.md)(Identyfikatory logowania, użytkownicy, role bazy danych i konta użytkowników).
- Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń bazy danych, zobacz [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine) (Podmioty zabezpieczeń).
- Aby uzyskać więcej informacji na temat ról bazy danych, zobacz [Database roles](/sql/relational-databases/security/authentication-access/database-level-roles) (Role bazy danych).
- Aby uzyskać więcej informacji na temat reguł zapory w usłudze SQL Database, zobacz [Omówienie reguł zapory usługi SQL Database](firewall-configure.md).
- Aby uzyskać informacje na temat sposobu ustawienia użytkownika-gościa usługi Azure AD jako administratora usługi Azure AD, zobacz Create Azure AD guest users and set as an Azure AD admin (Tworzenie użytkowników-gości usługi Azure AD i ustawianie go [jako administratora usługi Azure AD).](authentication-aad-guest-users.md)
- Aby uzyskać informacje na temat sposobu obsługi jednostki usługi za pomocą Azure SQL, zobacz [Tworzenie użytkowników usługi Azure AD przy użyciu aplikacji usługi Azure AD](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
